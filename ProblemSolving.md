# ProblemSolving: Microsoft 365, Entra ID & Intune Lab

This log records issues seen during the lab and the approach used to investigate or resolve them.

## 1. VM Had No Internet Connection

**Issue:** Windows showed Ethernet connected but reported no internet. The VM was using VMware networking while the existing AD lab also used VMnet8.

**Approach:** Checked the VM network adapter and VMware Virtual Network Editor. Kept the existing AD network unchanged, switched the Windows lab VM to Bridged networking, then confirmed Windows had internet access.

**Result:** The Windows VM could reach Microsoft 365 and complete Entra ID join and Intune enrollment.

## 2. Intune Device Did Not Appear Immediately

**Issue:** The Intune Windows devices list initially showed zero devices after connecting the work account.

**Approach:** Confirmed Windows displayed both the Entra ID connection and the MDM connection. Triggered **Sync** from Windows and Intune, refreshed the device list, and checked the device's last check-in.

**Result:** `DESKTOP-3SO75PG` appeared in Intune with Alex Kim as primary user. The first sync pane showed policies and applications as not started; later check-in and device status provided the completion evidence.

## 3. BitLocker Did Not Start

**Issue:** The compliance policy reported BitLocker as not compliant, and the first attempt to start BitLocker reported bootable media (CD/DVD) was attached.

**Approach:** Disconnected the Windows installation ISO from the VM's virtual CD/DVD device, restarted the VM, then retried BitLocker. Checked the policy and encryption status in Windows and Intune.

**Result:** BitLocker encryption started and completed on the C: drive. After another device check-in, Intune reported the device as compliant. The recovery key was checked in Intune and was not copied into the project.

## 4. Intune App Install Report Was Empty or Delayed

**Issue:** The Company Portal deployment appeared in Intune, but the device and user install-status reports initially showed no items.

**Approach:** Confirmed the app assignment was Required for `SG-Sales-Users`, synced the enrolled VM, and waited for the endpoint to process the deployment. Used the Windows notification as the endpoint-side result and checked the Intune report again later.

**Result:** Windows displayed “Company Portal deployment test was installed successfully.” The delayed/empty report was not treated as proof of failure.

## 5. Exchange Transport Rule Rejected a Condition

**Issue:** Exchange rejected the first transport rule because a word in the exception contained only blank spaces.

**Approach:** Replaced the blank value with the actual tag `[LAB] -`, saved the rule, and sent a test message to the Sales shared mailbox.

**Result:** The received subject began with `[LAB] -`, verifying that the rule applied.

## 6. Defender Custom Policy Creation Returned a Client Error

**Issue:** Creating custom inbound anti-spam and anti-malware policies returned `Microsoft.Exchange.Management.PSDirectInvoke.DirectInvokeCmdletExecutionException`. The portal showed a generic client error; diagnostic details included a WUS production environment and a timestamp.

**Approach:** Reviewed the existing policy list and confirmed the default inbound anti-spam policy was Always on. Retried the custom policy workflow and observed the same exception. Did not record either custom policy as created.

**Result:** Custom policy creation remains unresolved. The project records the default protection review and does not claim successful custom anti-spam or anti-malware deployment.

## 7. Conditional Access Report-Only Result Initially Failed

**Issue:** An initial sign-in evaluation showed Report-only failure, and the sign-in details showed the device as unmanaged and noncompliant.

**Approach:** Checked the sign-in's Device info and Conditional Access/Report-only details. Repeated the test in a regular Edge profile signed in with the lab account rather than relying on the earlier private-browser session.

**Result:** The later normal Edge sign-in from the enrolled, compliant Windows VM showed **Report-only: Success**. The policy was left in Report-only mode; it was not enabled or used to block access.

## 8. Entra Cloud Sync Pilot Timed Out

**Issue:** The on-premises pilot user was not confirmed in Entra ID. **Provision on demand** repeatedly timed out, and the Entra provisioning logs did not show a corresponding completed operation. The Cloud Sync configuration showed **Healthy** and the agent showed **Active**, but the user/group sync cycle remained **Not started / Never**. Password Hash Sync reported `HybridIdentityServiceAgentTimeout` and `ConnectorTimeout` / `GatewayTimeout`.

**Approach:**

1. Verified the pilot account in AD:
   - Account: `hybridpilot`
   - UPN: `hybridpilot@junuihonglab.onmicrosoft.com`
   - OU: `OU=CloudSync-Pilot,DC=jh,DC=local`
   - The account's distinguished name and object GUID were returned by `Get-ADUser`.
2. Confirmed the provisioning agent service was running and the Entra portal listed the server agent as active.
3. Restarted the Windows Server VM and the Cloud Sync provisioning configuration, then retried on-demand provisioning.
4. Checked outbound connectivity from the server. TCP connections to `crl.microsoft.com:80` and `his-sb-hisgeneral-nam-eus1.servicebus.windows.net:443` succeeded.
5. Sent a WebSocket upgrade probe to the Service Bus endpoint. It returned `HTTP/1.1 101 Switching Protocols`; the client then timed out while the upgraded connection remained open. This confirmed the initial upgrade response, but did not prove that the agent's sustained signaling session or provisioning request worked.
6. Inspected the provisioning-agent trace. It recorded `Unable to establish a web socket connection`, a remote-host connection reset, and listeners going offline. The agent log also showed the gMSA identity resolved, a credential-store SPN registered, AD credentials written to the Windows credential store, and directory changes applied successfully.
7. Checked Windows Firewall for enabled outbound block rules; no matching rules were found. The server's firewall profiles were enabled with outbound action `NotConfigured`.

**Result:** The cause was not conclusively identified. The evidence points to an unresolved agent-to-Entra signaling/connector communication timeout; the basic TCP tests alone did not establish that the full sync channel was healthy. The Cloud Sync setup did not complete a verified user synchronization, so this is recorded as an attempted but unsuccessful hybrid integration.

**Next attempt:** Build a fresh AD environment this weekend, then repeat the pilot with a clean domain, new OU/user, and new Cloud Sync configuration. Re-check the provisioning cycle and Entra user object before treating the integration as successful.

## Notes

- A Report-only result evaluates a policy without enforcing its access controls.
- Custom Defender policies are not marked complete unless the portal confirms creation.
- No passwords, MFA setup codes, or BitLocker recovery keys belong in screenshots or repository files.
- A Cloud Sync agent marked Active or a configuration marked Healthy is not, by itself, proof that a user object synchronized.
