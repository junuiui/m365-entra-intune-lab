# Microsoft 365, Entra ID & Intune Administration Lab

A hands-on Microsoft 365 Business Premium trial lab for cloud identity administration, Exchange mail flow, Windows endpoint enrollment, application deployment, BitLocker compliance, and Conditional Access evaluation.

> This is a personal learning lab using test accounts and a Windows 11 Enterprise Evaluation VM. It is not a production deployment. The tenant uses a Microsoft 365 Business Premium trial, not a Microsoft 365 E5 Developer tenant.

## Project status

**Approximately 90% complete** against the planned lab scope. The core identity, Exchange, Intune enrollment, app deployment, BitLocker compliance, and Conditional Access evaluation scenarios are complete. Conditional Access remains in Report-only mode by choice, and custom Defender portal policies could not be created because the portal returned a DirectInvoke client error.

| Workstream | Status |
| --- | --- |
| Entra ID test users, department groups, and delegated Helpdesk Administrator role | Complete |
| Microsoft 365 Business Premium trial license assignment | Complete for the tested account |
| Sales shared mailbox, member access, and Send as verification | Complete |
| Exchange mail flow rule and successful subject-tag test | Complete |
| Custom anti-spam and anti-malware policy creation | Attempted; portal returned a DirectInvoke client error. Default protections remain in place |
| Windows 11 VM joined to Entra ID and enrolled in Intune | Complete |
| BitLocker policy, encryption, compliance evaluation, and compliant device result | Complete |
| Intune Company Portal app deployment | Complete; installation success verified in the VM |
| Conditional Access MFA and compliant-device policy | Created and evaluated successfully in Report-only mode; not enforced |
| Hybrid connection to the existing on-premises AD lab | Optional future extension |

## Evidence and implementation notes

Detailed steps, configuration choices, verification results, and screenshots are in [admin.md](admin.md). Screenshots are embedded directly below the related tasks and stored in [resources/](resources/).

The project uses fictional employee accounts. Passwords, temporary credentials, MFA setup codes, and BitLocker recovery keys are not published.