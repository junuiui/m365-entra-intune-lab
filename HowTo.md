# HowTo: Microsoft 365, Entra ID & Intune Lab

This guide records the steps used to configure the lab. It focuses on how to complete each task.

## Lab Environment

- Microsoft 365 Business Premium trial tenant: `junuihonglab.onmicrosoft.com`
- Windows 11 Enterprise Evaluation VM in VMware Workstation
- Test user: Alex Kim (`alexkim@junuihonglab.onmicrosoft.com`)
- Existing on-premises AD lab remains separate

## 1. Create Users, Groups, and a Delegated Admin

1. In the Microsoft 365 admin center, open **Users > Active users > Add a user**.
2. Create a cloud user and enter the display name, username, password settings, department, and job title.
3. Assign a Microsoft 365 Business Premium license to the test user.
4. Repeat for the remaining lab users.
5. Open **Teams & groups > Active teams & groups > Security groups** and create assigned-membership groups:
   - `SG-Sales-Users`
   - `SG-HR-Users`
   - `SG-Finance-Users`
   - `SG-IT-Users`
   - `SG-Operations-Users`
6. Add each test user to the matching department group.
7. In the Entra admin center, open **Roles & administrators**, select **Helpdesk Administrator**, and assign the role to the IT helpdesk test user.

![Create a cloud user](resources/00-create-new-user.png)

![Assign a Business Premium license](resources/01-create-new-user-licence.png)

![Department group membership](resources/03-sales-security-group.png)

![Department security groups](resources/04-SG.png)

![Helpdesk Administrator role assignment](resources/08-IT-helpdesk-role-given.png)

## 2. Create and Test the Sales Shared Mailbox

1. In the Exchange admin center, open **Recipients > Mailboxes > Add a shared mailbox**.
2. Create `sales@junuihonglab.onmicrosoft.com`.
3. Add Alex Kim, Daniel Park, and Emma Lee as mailbox members.
4. Grant the test users **Read and manage** and **Send as** access.
5. Sign in as Alex and open the Sales shared mailbox in Outlook.
6. Send a test message using the Sales mailbox as the sender.
7. Open the recipient's mailbox and verify the sender and message.

![Sales shared mailbox and members](resources/05-Sales-shared-memory.png)

![Sales mailbox opened by Alex](resources/06-sales-shared-from-alex.png)

![Message received from the Sales mailbox](resources/07-sales-shared.png)

## 3. Join and Enroll the Windows VM

1. Create a Windows 11 Enterprise Evaluation x64 VM in VMware Workstation. The lab VM uses 8 GB RAM, 2 vCPUs, and an 80 GB virtual disk.
2. Connect the VM to a network with internet access. The lab used Bridged networking to keep the existing AD lab's VMnet8 network unchanged.
3. Complete Windows setup with a local account.
4. Open **Settings > Accounts > Access work or school > Connect**.
5. Sign in with the Entra test user and complete the join.
6. From **Access work or school**, start device management enrollment when prompted. Confirm both the Entra ID connection and MDM connection are listed.
7. Sign in to Windows as Alex Kim.
8. In Windows, open **Settings > Accounts > Access work or school**, select the connected account, and choose **Info > Sync**.
9. In the Intune admin center, open **Devices > Windows > Windows devices** and refresh until the device appears. Open the device to confirm the primary user and check-in.

![Windows 11 Enterprise Evaluation VM](resources/15-alex-window11-first.png)

![Entra ID join prompt](resources/12-entraid-join-message.png)

![Windows connected to Entra ID](resources/13-entraid-join-success.png)

![Alex's first Windows sign-in](resources/14-alex-login-success.png)

![Windows work account and MDM connection](resources/16-alex-intune-setting.png)

![Sync initiated from Windows](resources/17-alex-intune-sync-vmside.png)

![Device visible in Intune](resources/18-alex-intune-sync-adminside.png)

![Intune check-in completed](resources/19-alex-intune-sync-complete-adminside.png)

## 4. Configure BitLocker Compliance

1. In Intune, open **Devices > Compliance > Policies > Create policy**.
2. Select **Windows 10 and later** and the Windows 10/11 compliance policy type.
3. Name the policy `WIN-COMP-Sales-BitLocker`.
4. In **Compliance settings**, require BitLocker.
5. Assign the policy to `SG-Sales-Users` and create it.
6. Open **Endpoint security > Disk encryption > Create policy**.
7. Select Windows, name the policy `WIN-ENC-Sales-BitLocker`, and enable device encryption.
8. Under **Operating System Drives**, enable drive encryption, select **Used Space Only encryption**, and enable BitLocker recovery options. Keep recovery key material private.
9. Assign the policy to `SG-Sales-Users` and create it.
10. In VMware settings, disconnect the Windows installation ISO from the virtual CD/DVD drive.
11. In the Windows VM, open **Control Panel > System and Security > BitLocker Drive Encryption** and start BitLocker if it has not started from policy.
12. Wait for encryption to finish, sync the device, and refresh its Intune compliance status.
13. Confirm the device is marked **Compliant** and that the recovery key is available in Intune. Do not copy the recovery key into this repository.

![BitLocker compliance policy report](resources/20-bitlocker-report-success.png)

![BitLocker encryption in progress](resources/21-bitlocker-encypting.png)

![BitLocker on for the C drive](resources/22-bitlocker-on.png)

![Intune device reports Compliant](resources/23-desktop-compliance.png)

## 5. Deploy an Intune App

1. In Intune, open **Apps > Windows > Create**.
2. Add **Company Portal** as a Microsoft Store app (new) and name the deployment `Company Portal deployment test`.
3. Assign it as **Required** to `SG-Sales-Users`.
4. Sync the Windows VM from **Access work or school**.
5. Wait for the Windows notification confirming installation, then review the app's install status in Intune.

![Company Portal installation succeeded on the VM](resources/24-VM-intune-software-success.png)

## 6. Create and Test an Exchange Mail Flow Rule

1. In the Exchange admin center, open **Mail flow > Rules > Add a rule**.
2. Name the rule `LAB - Tag Sales mailbox messages`.
3. Set the recipient condition to `sales@junuihonglab.onmicrosoft.com`.
4. Set the action to prepend `[LAB] - ` to the subject.
5. Add an exception for messages whose subject or body includes `[LAB] -`.
6. Save the rule.
7. Send a new test email to the Sales mailbox with a subject that does not already contain the tag.
8. Verify the received message subject begins with `[LAB] -`.

![Initial transport rule configuration](resources/25-exchange-rule.png)

![Corrected transport rule configuration](resources/26-exchange-rule-fixed.png)

![Transport rule test message](resources/28-email-hearder-test.png)

![Received email with the subject prefix](resources/29-email-header-success.png)

## 7. Create a Conditional Access Policy in Report-Only Mode

1. In the Entra admin center, open **Protection > Conditional Access > Policies > New policy**.
2. Name it `CA-Sales-Require-MFA-Compliant-Device`.
3. Include the `SG-Sales-Users` group.
4. Select **Office 365** as the target resource.
5. Under grant controls, require multifactor authentication and a compliant device.
6. Set the policy state to **Report-only** and create it.
7. Sign in to an Office 365 app from the enrolled, compliant Windows VM using a regular Edge browser profile.
8. Open **Sign-in logs**, select the sign-in, and check the Conditional Access or Report-only tab for the policy result.

![Conditional Access Report-only evaluation succeeded](resources/27-compliant-success.png)

## 8. Review Default Email Protection

1. In the Microsoft Defender portal, open **Email & collaboration > Policies & rules > Threat policies > Anti-spam policies**.
2. Review the default inbound anti-spam policy and its status.
3. Custom policy creation attempts and their portal errors are recorded in [ProblemSolving.md](ProblemSolving.md).
