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

- Created Alex Kim as a cloud user with username `alexkim@junuihonglab.onmicrosoft.com` and job title Sales Associate.
- Initially observed Alex as Unlicensed, then assigned Microsoft 365 Business Premium. The updated license assignment was verified in the Active users list.
- Reset the automatically generated temporary password when it was unavailable, then verified Alex's successful Microsoft 365 sign-in.
- Verified delivery of a message from Alex to Junui Hong in Outlook.
- Created the remaining test employees and populated their department and job title information, as reported by the lab operator.

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

License assignment was visually verified for Alex and Junui. This record does not establish the license status of all other users. An IT job title does not itself grant an administrative role.

## 2. Department Security Groups

Created assigned-membership security groups for department-based access and future policy targeting:

| Group | Members |
| --- | --- |
| SG-Sales-Users | Alex Kim, Emma Lee, Daniel Park |
| SG-HR-Users | Olivia Chen, James Wilson |
| SG-Finance-Users | Sophia Patel, Ethan Brown |
| SG-IT-Users | Mia Garcia, Noah Smith |
| SG-Operations-Users | Ava Martin |

Alex's membership in SG-Sales-Users was visually verified. Completion of the remaining memberships was reported by the lab operator. These are assigned groups: setting a user's Department attribute does not automatically update membership. Security groups were not used as mail distribution lists.

## 3. Sales Shared Mailbox

- Created `sales@junuihonglab.onmicrosoft.com`.
- Added Alex Kim, Emma Lee, and Daniel Park as members.
- Verified that the mailbox details displayed three Read and manage assignments and three Send as assignments, with zero Send on behalf assignments.
- Following the Alex access test, verified receipt in Junui's mailbox of a message displaying Sales as the sender, with subject `Lab Test - Sales shared mailbox`.

Read and manage permits mailbox access; Send as permits sending using the shared mailbox identity. The shared mailbox is accessed through delegated user accounts.

Reply delivery back to the Sales mailbox was requested but not explicitly confirmed in the supplied evidence. Shared-mailbox sent-item copying was shown as disabled and was not changed in this phase.

## 4. Delegated Helpdesk Administration

The lab procedure assigned the Helpdesk Administrator role to Mia Garcia, then tested a password reset for Emma Lee. The operator proceeded through the procedure and supplied Emma's forced password-change screen. That screen establishes that Emma authenticated with a temporary password and was prompted to change it; it does not independently establish the identity of the administrator who performed the reset.

A subsequent attempt to reset Junui Hong's password returned:

> We could not reset the password for this user.

The detail displayed `Junui Hong : null`. The failure is recorded as observed, but the generic error does not prove that role restrictions caused it. Confirmation of the acting account, target account roles, and relevant audit details remains necessary before documenting this as a verified authorization denial.

## Validation Status

| Check | Status |
| --- | --- |
| Alex user creation and Business Premium assignment | Visually verified |
| Alex Microsoft 365 sign-in | Visually verified |
| Alex-to-Junui email delivery | Visually verified |
| Ten-employee roster and department group completion | Operator reported; partial visual evidence |
| Sales mailbox creation and permission counts | Visually verified |
| Sales sender message delivery to Junui | Visually verified |
| Reply receipt in Alex/Sales mailbox | Not explicitly confirmed |
| Emma temporary-password authentication and change prompt | Visually verified |
| Emma completed password change and subsequent sign-in | Not explicitly confirmed |
| Junui password reset attempt | Failure visually verified; cause unconfirmed |
| Mia role assignment and audit trail | Further evidence needed |

## Evidence Inventory

Suggested filenames for the supplied screenshots are listed below. Images are not embedded in this document; their permanent storage and sanitization should be verified before publication.

- `01-user-license-assignment.png`: Alex and Junui license assignments.
- `02-alex-mail-delivery.png`: Alex's message received by Junui.
- `03-sales-security-group.png`: Alex in the Sales security group.
- `04-sales-shared-mailbox-permissions.png`: Sales members and delegation counts.
- `05-shared-mailbox-send-as.png`: Sales message received by Junui.
- `06-helpdesk-role-assignment.png`: Capture still needed to establish Mia's assigned role.
- `07-helpdesk-admin-reset-failed.png`: Failed reset attempt for Junui; cause unconfirmed.

Do not publish passwords, temporary passwords, MFA QR codes, recovery codes, or payment information. Password-entry screenshots are unnecessary for this evidence set.

## Next Phase: Windows Endpoint Management

Create a separate Windows 11 VM in VMware, join it to Microsoft Entra ID, and enroll it in Intune. This provides an endpoint on which to test app installation, configuration delivery, device compliance, and access outcomes. The existing AD lab remains available for a later hybrid identity extension.

Planned VM baseline: 8 GB RAM, 2 CPU cores, 80 GB virtual disk, NAT networking, and Windows 11 Enterprise evaluation media. Confirm the VMware version and Windows 11 installation requirements before creation.

No Intune enrollment, app deployment, Conditional Access policy, BitLocker enforcement, or device compliance result is claimed as completed in this document.
