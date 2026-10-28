# Microsoft 365, Entra ID & Intune Administration Lab

A hands-on Microsoft 365 Business Premium trial lab for cloud identity administration, Exchange shared mailbox access, Windows endpoint enrollment, and BitLocker compliance.

> This is a personal learning lab using test accounts and a Windows 11 Enterprise Evaluation VM. It is not a production deployment. The tenant is on a Microsoft 365 Business Premium trial, not a Microsoft 365 E5 Developer tenant.

## Project status

**Approximately 60% complete** against the planned lab scope. Core identity, mailbox, device enrollment, and one endpoint compliance scenario are complete. App deployment and Conditional Access remain.

| Workstream | Status |
| --- | --- |
| Entra ID test users, departments, security groups, and delegated Helpdesk Administrator role | Complete |
| Microsoft 365 Business Premium trial license assignment | Complete for the tested account |
| Sales shared mailbox, member access, and Send as verification | Complete |
| Windows 11 VM joined to Entra ID and enrolled in Intune | Complete |
| BitLocker policy, compliance evaluation, and compliant device result | Complete |
| Intune silent application deployment | Planned |
| Conditional Access MFA and compliant-device access test | Planned |
| Hybrid connection to the existing on-premises AD lab | Optional future extension |

## Evidence and implementation notes

Detailed steps, configuration choices, verification results, and screenshots are in [admin.md](admin.md). Screenshots are embedded directly below the related tasks and stored in [resources/](resources/).

The project uses fictional employee accounts. Passwords, temporary credentials, and BitLocker recovery keys are not published.
