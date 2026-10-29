# Microsoft 365, Entra ID & Intune Administration Lab

This project demonstrates cloud identity management, Exchange administration, and Windows endpoint deployment in a simulated company. I used a Microsoft 365 Business Premium trial tenant and Windows 11 Enterprise Evaluation VMs in VMware Workstation.

I built the lab to practice common entry-level IT administration tasks: creating and supporting user accounts, managing shared mailbox access, enrolling a Windows device, deploying an app, applying endpoint security, and investigating a hybrid identity sync issue.

## What I Built

### Entra ID user and group management

I created a 10-person test workforce across Sales, HR, Finance, IT, and Operations. I created department security groups and assigned the Helpdesk Administrator role to an IT support test account.

![Department security groups](resources/04-SG.png)

### Exchange Online shared mailbox and mail flow

I created a Sales shared mailbox, added three members, and verified Send as access. I also created a mail flow rule that adds a `[LAB] -` tag to messages sent to Sales. A test email arrived with the expected subject prefix.

![Sales shared mailbox access](resources/06-sales-shared-from-alex.png)

![Test email with the mail flow tag](resources/29-email-header-success.png)

### Intune enrollment and endpoint compliance

I joined a Windows 11 VM to Entra ID and enrolled it in Intune. I deployed Company Portal to the Sales group and verified installation in Windows. I configured BitLocker encryption and confirmed that the device reported Compliant after it checked in.

![Company Portal installation succeeded](resources/24-VM-intune-software-success.png)

![Windows device reported compliant in Intune](resources/23-desktop-compliance.png)

### Conditional Access evaluation

I created a policy for the Sales group that requires MFA and a compliant device. I tested it in Report-only mode from the enrolled Windows VM and confirmed a successful policy evaluation.

![Conditional Access Report-only evaluation succeeded](resources/27-compliant-success.png)

The policy remains in Report-only mode, so this result confirms evaluation only. It does not mean the policy enforced MFA or blocked access.

### Hybrid identity sync: attempted, not completed

I prepared an on-premises AD pilot OU and a `hybridpilot` account, installed and registered the Microsoft Entra provisioning agent on the Windows Server VM, and configured a Cloud Sync setup for `jh.local`.

![Hybrid pilot account and OU](resources/hybrid/02-hybrid-pilot-creation.png)

![Provisioning agent registered for the lab domain](resources/hybrid/03-agent.png)

The agent appeared active and the Cloud Sync configuration later showed Healthy, but user/group sync never completed. On-demand provisioning timed out, and the provisioning cycle reported `HybridIdentityServiceAgentTimeout` / `ConnectorTimeout`. I have not confirmed that the pilot user synchronized into Entra ID. This is an unresolved troubleshooting result, not a completed hybrid deployment.

![Alternative UPN suffix configured for the pilot](resources/hybrid/01-alternative-UPN-suffix.png)

The next step is to build a fresh, isolated AD lab this weekend and repeat the hybrid setup from a clean baseline.

## Lab Notes

The default inbound anti-spam policy was shown as Always on. Custom anti-spam and anti-malware policy creation returned a Microsoft Exchange DirectInvoke client error, so I have not listed those custom policies as created.

## Environment

- Microsoft 365 Business Premium trial
- Entra ID tenant: `junuihonglab.onmicrosoft.com`
- VMware Workstation Windows 11 Enterprise Evaluation client VM
- Windows Server 2022 AD lab using `jh.local`
- Microsoft Entra Cloud Sync agent installed on the domain controller VM
- Hybrid sync validation remains incomplete

## Guides

- [HowTo.md](HowTo.md) — configuration steps used in the lab
- [ProblemSolving.md](ProblemSolving.md) — issues found, troubleshooting approach, and results
