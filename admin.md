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

- Created Alex Kim as a cloud user with username **alexkim@junuihonglab.onmicrosoft.com** and job title Sales Associate.
- Initially observed Alex as unlicensed, then assigned Microsoft 365 Business Premium. The updated assignment was verified in the Active users list.
- Reset Alex's automatically generated temporary password when it was unavailable, then verified successful Microsoft 365 sign-in.
- Verified delivery of a message from Alex to Junui Hong in Outlook.
- Created nine additional test employees and populated department and job title information.

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

The screenshots show the initial user creation and license assignment. They do not establish the license status of every test employee. An IT job title does not itself grant an administrative role.

## 2. Department Security Groups

Created assigned-membership security groups for department-based access and future policy targeting:

| Group | Members |
| --- | --- |
| SG-Sales-Users | Alex Kim, Emma Lee, Daniel Park |
| SG-HR-Users | Olivia Chen, James Wilson |
| SG-Finance-Users | Sophia Patel, Ethan Brown |
| SG-IT-Users | Mia Garcia, Noah Smith |
| SG-Operations-Users | Ava Martin |

The Sales group membership is directly shown in the evidence. The full group list is also captured. Completion of other group memberships was reported by the lab operator. These are assigned groups: changing a user's Department attribute does not automatically update group membership.

## 3. Sales Shared Mailbox

- Created **sales@junuihonglab.onmicrosoft.com**.
- Added Alex Kim, Daniel Park, and Emma Lee as members.
- Verified that the mailbox details showed three Read and manage assignments, three Send as assignments, and zero Send on behalf assignments.
- Verified that Alex sent a message using the Sales identity and that it arrived in Junui's mailbox.

Read and manage permits mailbox access; Send as permits sending using the shared mailbox identity. The shared mailbox is accessed through delegated user accounts.

The screenshots document mailbox membership and permission counts, Alex's access, and the resulting Sales message. Reply delivery back to the Sales mailbox was not confirmed. Shared-mailbox sent-item copying was shown as disabled and was not changed.

## 4. Delegated Helpdesk Administration

- Assigned the Helpdesk Administrator role to Mia Garcia. The role assignment is shown in the evidence.
- A screenshot documents a successful password reset performed by Mia for a user account.
- A separate screenshot shows Emma Lee's successful password update/sign-in flow following the reset.
- A later attempt to reset Junui Hong's password failed. The displayed error was “We could not reset the password for this user” with detail **Junui Hong : null**.

The failed reset is recorded as an observed result. Its generic error does not establish that role restrictions caused it. The acting account and audit details were not captured with the error, so it is not claimed as a verified least-privilege denial.

## Validation Status

| Check | Status |
| --- | --- |
| Alex user creation and Business Premium assignment | Visually verified |
| Alex Microsoft 365 sign-in | Visually verified |
| Alex-to-Junui email delivery | Visually verified |
| Ten-employee roster and department group completion | Operator reported; evidence includes initial user creation and group list |
| Alex membership in SG-Sales-Users | Visually verified |
| Sales mailbox creation and permission counts | Visually verified |
| Sales sender message delivery to Junui | Visually verified |
| Mia Helpdesk Administrator assignment | Visually verified |
| Mia password reset success | Screenshot available |
| Emma password update/sign-in result | Screenshot available |
| Junui password reset attempt | Failure visually verified; cause unconfirmed |
| Reply receipt in Alex/Sales mailbox | Not confirmed |

## Evidence

All screenshots are in [resources/](resources/). Select a filename to view its image.

| File | Evidence |
| --- | --- |
| [00-create-new-user.png](resources/00-create-new-user.png) | New user creation details |
| [01-create-new-user-licence.png](resources/01-create-new-user-licence.png) | User and license assignment |
| [02-mail-send-receive.png](resources/02-mail-send-receive.png) | Alex-to-Junui email conversation |
| [03-sales-security-group.png](resources/03-sales-security-group.png) | Alex as a member of SG-Sales-Users |
| [04-SG.png](resources/04-SG.png) | Department security group list |
| [05-Sales-shared-memory.png](resources/05-Sales-shared-memory.png) | Sales shared mailbox configuration and members |
| [06-sales-shared-from-alex.png](resources/06-sales-shared-from-alex.png) | Alex's Sales shared mailbox access |
| [07-sales-shared.png](resources/07-sales-shared.png) | Message sent using the Sales mailbox |
| [08-IT-helpdesk-role-given.png](resources/08-IT-helpdesk-role-given.png) | Mia's Helpdesk Administrator role assignment |
| [09-Mia-password-reset-success.png](resources/09-Mia-password-reset-success.png) | Successful password reset result |
| [10-Emma-password-success.png](resources/10-Emma-password-success.png) | Emma's password update/sign-in result |
| [11-delpdesk-admin-reset-failed.png](resources/11-delpdesk-admin-reset-failed.png) | Failed reset attempt for Junui; cause unconfirmed |

Do not publish passwords, temporary passwords, MFA QR codes, recovery codes, or payment information.

## Next Phase: Windows Endpoint Management

Create a separate Windows 11 VM in VMware, join it to Microsoft Entra ID, and enroll it in Intune. This provides a managed endpoint for testing app installation, configuration delivery, device compliance, and access outcomes. The existing AD lab remains available for a later hybrid identity extension.

Planned VM baseline: 8 GB RAM, 2 CPU cores, 80 GB virtual disk, NAT networking, and Windows 11 Enterprise evaluation media. Confirm the VMware version and Windows 11 installation requirements before creation.

No Intune enrollment, app deployment, Conditional Access policy, BitLocker enforcement, or device compliance result is claimed as completed in this phase.
