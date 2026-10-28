# Microsoft 365, Entra ID & Intune Administration Lab

## Phase 1: Cloud Identity and Mail Administration

This personal lab simulates employee onboarding, department-based group management, shared mailbox access, and delegated helpdesk administration. It uses a Microsoft 365 Business Premium trial in a separate test tenant. It is not a production deployment.

## Environment

| Component | Configuration |
| --- | --- |
| Tenant domain | junuihonglab.onmicrosoft.com |
| Subscription | Microsoft 365 Business Premium - Trial |
| Primary administrator | Junui Hong |
| Test workforce | 10 employees across Sales, HR, Finance, IT, and Operations |
| Identity model | Cloud-only; existing VMware Active Directory lab remains separate |
| Endpoint management | Planned; no Windows device enrollment completed in this phase |

## 1. User Provisioning and Licensing

Created Alex Kim as a cloud user with username **alexkim@junuihonglab.onmicrosoft.com** and job title Sales Associate. After initially observing the account as unlicensed, assigned Microsoft 365 Business Premium and verified the assignment in Active users. Reset Alex's temporary password and verified Microsoft 365 sign-in.

![Create a cloud user](resources/00-create-new-user.png)

![Assign Microsoft 365 Business Premium](resources/01-create-new-user-licence.png)

Created nine additional test employees and populated their department and job title information.

| Employee | Department | Job title |
| --- | --- | --- |
| Alex Kim | Sales | Sales Associate |
| Emma Lee | Sales | Sales Associate |
| Daniel Park | Sales | Sales Manager |
| Olivia Chen | HR | HR Coordinator |
| James Wilson | HR | HR Manager |
| Sophia Patel | Finance | Accountant |
| Ethan Brown | Finance | Finance Analyst |
| Mia Garcia | IT | IT Support Technician |
| Noah Smith | IT | IT Administrator |
| Ava Martin | Operations | Operations Coordinator |

The images show initial account creation and Alex's license assignment; they do not establish the license status of all test employees. An IT job title does not itself grant an administrative role.

## 2. Department Security Groups

Created assigned-membership security groups for department-based access and future policy targeting. The Sales group contains Alex Kim, Emma Lee, and Daniel Park. Department groups were created for HR, Finance, IT, and Operations as well.

![Alex Kim in the Sales security group](resources/03-sales-security-group.png)

![Department security groups](resources/04-SG.png)

Changing a user's Department attribute does not automatically update assigned group membership. These security groups are intended for access and policy targeting, not as mail distribution lists.

## 3. Sales Shared Mailbox

Created **sales@junuihonglab.onmicrosoft.com** and added Alex Kim, Daniel Park, and Emma Lee as members. The mailbox showed three Read and manage assignments, three Send as assignments, and zero Send on behalf assignments.

![Sales shared mailbox configuration and members](resources/05-Sales-shared-memory.png)

Alex opened the shared mailbox and sent a message using the Sales identity. The message arrived in Junui Hong's mailbox with Sales as the sender.

![Alex's access to the Sales shared mailbox](resources/06-sales-shared-from-alex.png)

![Email received from the Sales shared mailbox](resources/07-sales-shared.png)

Read and manage permits mailbox access; Send as permits sending using the shared mailbox identity. Reply delivery back to the Sales mailbox was not confirmed. Shared-mailbox sent-item copying was shown as disabled and was not changed.

## 4. Delegated Helpdesk Administration

Assigned the Helpdesk Administrator role to Mia Garcia.

![Mia Garcia assigned the Helpdesk Administrator role](resources/08-IT-helpdesk-role-given.png)

A successful password reset was recorded for a user, followed by Emma Lee's password update/sign-in result.

![Successful password reset by Mia](resources/09-Mia-password-reset-success.png)

![Emma Lee password update/sign-in result](resources/10-Emma-password-success.png)

A later attempt to reset Junui Hong's password failed with the message “We could not reset the password for this user” and detail **Junui Hong : null**.

![Failed password reset attempt for Junui Hong](resources/11-delpdesk-admin-reset-failed.png)

The failure is an observed result. Its generic error does not establish that role restrictions caused it. The acting account and audit details were not captured with the error, so this is not claimed as a verified least-privilege denial.

## Validation Status

| Check | Status |
| --- | --- |
| Alex user creation and Business Premium assignment | Visually verified |
| Alex Microsoft 365 sign-in | Visually verified |
| Alex-to-Junui email delivery | Visually verified |
| Ten-employee roster and department group completion | Operator reported; initial user and group list screenshots available |
| Alex membership in SG-Sales-Users | Visually verified |
| Sales mailbox creation and permission counts | Visually verified |
| Sales sender message delivery to Junui | Visually verified |
| Mia Helpdesk Administrator assignment | Visually verified |
| Mia password reset success | Screenshot available |
| Emma password update/sign-in result | Screenshot available |
| Junui password reset attempt | Failure visually verified; cause unconfirmed |
| Reply receipt in Alex/Sales mailbox | Not confirmed |

Screenshots are embedded under the corresponding lab activity and stored in [resources/](resources/). Do not publish passwords, temporary passwords, MFA QR codes, recovery codes, or payment information.

## Next Phase: Windows Endpoint Management

Create a separate Windows 11 VM in VMware, join it to Microsoft Entra ID, and enroll it in Intune. This provides a managed endpoint for testing app installation, configuration delivery, device compliance, and access outcomes. The existing AD lab remains available for a later hybrid identity extension.

Planned VM baseline: 8 GB RAM, 2 CPU cores, 80 GB virtual disk, NAT networking, and Windows 11 Enterprise evaluation media. Confirm the VMware version and Windows 11 installation requirements before creation.

No Intune enrollment, app deployment, Conditional Access policy, BitLocker enforcement, or device compliance result is claimed as completed in this phase.
