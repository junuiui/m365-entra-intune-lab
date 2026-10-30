# ProblemSolving: Microsoft 365, Entra ID & Intune Lab

This log records the issues I encountered, how I investigated them, and what I could confirm.

## 1. Windows VM Had No Internet Connection

**Issue:** The Windows VM showed Ethernet but could not reach the internet. The existing AD lab was already using VMware VMnet8.

**Approach:** Checked the VMware virtual network assignments and created a separate host-only VMnet2 network for the new server lab. VMware would not allow a second NAT network, so I used the existing VMnet8 NAT network for internet access and avoided running two VMs with overlapping network settings at the same time.

**Result:** The server VM gained internet access. I kept the existing AD VM configuration in mind while changing the lab network.

## 2. Intune Device Did Not Appear Immediately

**Issue:** The Intune Windows devices list initially showed zero devices after connecting the work account.

**Approach:** Confirmed Windows displayed both the Entra ID connection and the MDM connection. Triggered **Sync** from Windows and Intune, refreshed the device list, and checked the device's last check-in.

**Result:** `DESKTOP-3SO75PG` appeared in Intune with Alex Kim as primary user. The first sync pane showed policies and applications as not started; a later check-in showed the device compliant.

## 3. BitLocker Did Not Start

**Issue:** The compliance policy reported BitLocker as not compliant, and the first attempt to start BitLocker reported bootable media (CD/DVD) was attached.

**Approach:** Disconnected the Windows installation ISO from the VM's virtual CD/DVD device, restarted the VM, and retried BitLocker. Checked encryption in Windows and device compliance in Intune.

**Result:** BitLocker encryption started and completed on the C: drive. After another device check-in, Intune reported the device as compliant. The recovery key was checked in Intune and was not copied into the project.

## 4. Company Portal Install Status Was Delayed

**Issue:** The Company Portal deployment appeared in Intune, but device and user install-status reports initially showed no items.

**Approach:** Confirmed the app assignment was Required for `SG-Sales-Users`, synced the enrolled VM, and waited for the endpoint to process the deployment. Used the Windows notification as endpoint-side evidence and checked the Intune report again.

**Result:** Windows displayed “Company Portal deployment test was installed successfully.” The initial empty report was a reporting delay, not evidence that the install had failed.

## 5. Exchange Transport Rule Rejected a Condition

**Issue:** Exchange rejected the first transport rule because an exception value contained only blank spaces.

**Approach:** Replaced the blank value with the actual subject tag `[LAB] -`, saved the rule, and sent a test message to the Sales shared mailbox.

**Result:** The received subject began with `[LAB] -`, verifying that the rule applied.

## 6. Custom Defender Email Policies Returned a Client Error

**Issue:** Creating custom inbound anti-spam and anti-malware policies returned `Microsoft.Exchange.Management.PSDirectInvoke.DirectInvokeCmdletExecutionException`. The portal showed a generic client error, including when submitting the anti-malware policy.

**Approach:** Reviewed the policy list and confirmed the default inbound anti-spam policy was Always on. Retried the custom policy workflow and captured the error details.

**Result:** Custom policy creation remains unverified. The project records the default protection review and does not claim that custom policies were created.

## 7. Conditional Access Report-Only Evaluation Initially Failed

**Issue:** An initial sign-in evaluation showed Report-only failure. Sign-in details showed the device as unmanaged and noncompliant.

**Approach:** Reviewed the sign-in's Device info and Conditional Access/Report-only details. Repeated the test in a regular Edge profile signed in with the lab account, from the enrolled and compliant VM.

**Result:** A later sign-in showed **Report-only: Success** for the policy. The policy remains in Report-only mode; it was not enabled to enforce access controls.

## 8. First Entra Cloud Sync Pilot Timed Out

**Issue:** The first pilot used the `jh.local` AD domain. The pilot user was not confirmed in Entra ID. **Provision on demand** repeatedly timed out, and provisioning logs did not show a completed synchronization. The agent appeared **Active** and the configuration sometimes showed **Healthy**, while the user/group cycle remained **Not started / Never**. Password Hash Sync reported `HybridIdentityServiceAgentTimeout`; the portal also returned `ConnectorTimeout` / `GatewayTimeout`.

**Approach and evidence:**

1. Verified the AD account with `Get-ADUser`:
   - SamAccountName: `hybridpilot`
   - UPN: `hybridpilot@junuihonglab.onmicrosoft.com`
   - Distinguished name: `CN=Hybrid Pilot,OU=CloudSync-Pilot,DC=jh,DC=local`
   - Object GUID: `7105f6ba-5927-45e0-9ded-235f6d2f34ac`
2. Confirmed the provisioning agent service was running and the Entra portal listed the server agent as active.
3. Reviewed the setup trace. It recorded successful agent registration, gMSA identity resolution, SPN registration, writing AD credentials to the Windows credential store, applying directory changes, and restarting services.
4. Restarted the server and provisioning configuration and retried on-demand provisioning.
5. Checked outbound connectivity. TCP tests succeeded for the tested login, proxy, Service Bus, CRL, and Microsoft endpoints. `ocsp.msocsp.com` did not resolve in DNS; `oneocsp.microsoft.com` resolved and TCP port 80 succeeded.
6. Checked WinHTTP proxy settings; the server reported direct access with no proxy. Checked for enabled outbound Windows Firewall block rules; none matched.
7. Confirmed .NET Framework 4.8 was installed, meeting the documented 4.7.1 minimum. TLS 1.2 registry values were not explicitly configured; their absence alone did not prove TLS 1.2 was disabled.
8. Inspected agent traces. They included `Unable to establish a web socket connection`, a remote-host connection reset, listeners going offline, and `Sending backend response to Proxy - timeout elapsed`.
9. Sent a manual WebSocket upgrade request to the Service Bus endpoint. The endpoint returned `HTTP/1.1 101 Switching Protocols`; the test then timed out while the upgraded connection remained open. This verified the initial handshake response only, not the agent's full signaling or provisioning operation.
10. Captured a Packet Monitor trace during a retry. It showed TCP 443 connections and data exchange with Service Bus endpoints, but did not identify which component caused the provisioning timeout.

**Result:** The agent could register and communicate with parts of the Microsoft service path, but the provisioning request still did not complete. The available evidence did not identify whether the remaining failure was caused by the agent, the VM/network path, the Cloud Sync service, or an interaction between them. The first hybrid sync was not successful.

## 9. Rebuilt the AD Environment and Retried

**Issue:** Repeating the configuration in the original `jh.local` environment did not resolve the timeouts, so I rebuilt the AD lab to test with a clean domain.

**Approach and evidence:**

1. Created a new Windows Server 2022 VM, `WIN-9TRC5DR3JHV`, and configured a new AD DS/DNS forest using `jh.test`.
2. Confirmed the AD DS and DNS roles were installed and the domain was visible in the server tools.
3. Created a new pilot OU/user and configured the Entra provisioning agent for `jh.test`. The wizard reached its completion page and listed the domain service account.
4. Created a new Entra Cloud Sync configuration scoped to the pilot OU.
5. Retried **Provision on demand** and checked the configuration and provisioning logs.

**Result:** The clean-domain retry also timed out. The on-premises user was not verified in Entra ID. Rebuilding the AD environment ruled out a simple stale configuration in the original `jh.local` lab, but it did not establish the underlying cause.

## Current Status

- Completed and verified: cloud users and groups, delegated role assignment, Sales shared mailbox, mail flow rule test, Entra ID join, Intune enrollment, Company Portal deployment, BitLocker encryption/compliance, and Conditional Access evaluation in Report-only mode.
- Attempted but unresolved: custom Defender policy creation and AD-to-Entra Cloud Sync.
- Cloud Sync must remain labelled **attempted, not completed** unless a pilot user is visible in Entra ID and the provisioning result/log confirms success.

## Notes

- A Cloud Sync agent marked **Active** or configuration marked **Healthy** does not, by itself, prove a user synchronized.
- A successful TCP connection or WebSocket upgrade response does not prove the full provisioning channel is working.
- The packet capture is diagnostic evidence, not proof of a Microsoft service incident. Do not publish the raw capture; it may include network metadata.
- No passwords, MFA setup codes, or BitLocker recovery keys belong in screenshots or repository files.
