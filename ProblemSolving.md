# ProblemSolving: Microsoft 365, Entra ID & Intune Lab

This document records the issue, the checks used, why those checks were chosen, the action taken, and the verified result. An attempted fix is not treated as successful until the result is confirmed.

## 1. Windows VM Had No Internet Connection

**Issue:** The Windows 11 client VM showed an Ethernet adapter but no internet access.

**Approach:** I checked the VMware adapter mode, the guest IP configuration, and the Virtual Network Editor. These checks separated a guest OS issue from a virtual network or DHCP issue. I also compared the existing VMnet networks because the earlier AD lab already used VMnet8.

**Action:** I used the existing internet-enabled NAT path for internet access and created a separate host-only VMnet2 for private lab traffic. VMware would not allow VMnet2 to become a second NAT network, so I kept the network roles separate and avoided enabling competing DHCP services on the same isolated segment.

**Result:** The VM obtained internet access. The existing AD environment used VMnet8; keeping the new private VMnet distinct avoided changing that network's subnet and DHCP configuration.

## 2. Intune Device Did Not Appear Immediately

**Issue:** The Entra-joined Windows VM was not listed in Intune right after connecting the work account.

**Approach:** I checked **Access work or school** in Windows, verified the connection said it was joined to the lab's Entra ID, and compared the Windows and Intune device records. I then triggered a sync and checked the last check-in time. This confirmed enrollment state before diagnosing a policy issue.

**Action:** I completed the Entra ID join, used the device sync action, and waited for Intune inventory to update.

**Result:** The device appeared as Intune-managed. Its compliance state changed after the assigned policy processed and a later check-in completed.

## 3. BitLocker Did Not Start

**Issue:** Turning on BitLocker returned a message that bootable media (CD or DVD) was detected.

**Approach:** I checked the VM's virtual CD/DVD device because the Windows installation ISO was still attached. The BitLocker message directly identified bootable media as the startup blocker.

**Action:** I disconnected the ISO from the VM's CD/DVD device and restarted Windows. I then started BitLocker again and waited for encryption.

**Result:** BitLocker encryption started and Control Panel showed the C: drive as BitLocker on. Intune later reported the device as compliant.

## 4. Company Portal Install Status Was Delayed

**Issue:** The Intune app install report showed no device rows shortly after assigning Company Portal as Required.

**Approach:** I compared the Intune assignment with the VM's sync/check-in state and checked the Windows notification. The report itself warned that its information could be delayed, so the endpoint was the direct place to verify whether installation occurred.

**Action:** I left the VM online and allowed policy processing to continue. I verified the installation using the Windows notification and app presence rather than treating the empty report as a failure.

**Result:** Windows displayed “Company Portal deployment test was installed successfully.” The portal report can lag behind the endpoint.

## 5. Exchange Transport Rule Rejected a Condition

**Issue:** Exchange Online failed to create the mail flow rule with an error that a specified word must contain a non-blank character and cannot begin or end with a blank character.

**Approach:** I reviewed the condition and exception fields in the rule builder. The validation text pointed to a word value containing a blank or an exception condition that duplicated the prefix check.

**Action:** I removed the invalid blank/prefix exception and kept the rule simple: recipient is the Sales mailbox; prepend `[LAB] - ` to the subject.

**Result:** The rule saved. A test email arrived with the `[LAB] -` subject prefix.

## 6. Custom Defender Email Policies Returned a Client Error

**Issue:** Creating custom anti-spam and anti-malware policies in the Microsoft Defender portal returned `Microsoft.Exchange.Management.PSDirectInvoke.DirectInvokeCmdletExecutionException`. The policy did not appear in the policy list.

**Approach:** I checked whether the custom policy existed after the error and compared the policy list with the built-in policies. This distinguishes a failed creation attempt from a policy that saved despite a UI error.

**Action:** I recorded the portal error and reviewed the built-in inbound anti-spam policy, which was shown as Always on. I did not retry with scripts or claim the custom policies were created.

**Result:** The built-in policy was reviewed. Custom anti-spam and anti-malware policies remain uncreated and unverified.

## 7. Conditional Access Report-Only Evaluation Initially Failed

**Issue:** The first sign-in checked against the new policy showed **Report-only: Failure**.

**Approach:** I opened the sign-in details and reviewed the **Device info**, **Conditional Access**, and **Report-only** tabs. This checked whether the result came from policy scope, grant controls, or device state instead of guessing from the summary row.

**Action:** I kept the policy in Report-only mode and tested again from the Intune-enrolled, compliant Windows VM. I did not switch the policy to On.

**Result:** A later sign-in showed **Report-only: Success**. This confirms the policy evaluation result for that sign-in; it does not mean the policy enforced MFA or blocked a device.

## 8. First Entra Cloud Sync Pilot Timed Out

**Issue:** The first Cloud Sync setup for `jh.local` showed the provisioning agent as active, but Provision on demand timed out. Entra reported that the on-premises environment did not return a timely response.

**Approach:** I verified that the pilot user existed in AD, was under the scoped OU, and had the intended cloud UPN. I checked the provisioning agent service and registration, the Cloud Sync configuration, its scope, provisioning logs, and agent traces. These checks tested the input object, service readiness, and connection path separately.

**Action:** I retried after agent and server restarts, checked .NET Framework and TLS settings, tested DNS and outbound TCP access to Microsoft sign-in, Service Bus, and application proxy endpoints, and reviewed outbound firewall rules. I also tried the Cloud Sync troubleshooting log collection tool.

**Result:** The agent was registered and showed active, but no successful user sync was verified. The service continued to report a timeout.

## 9. Rebuilt the AD Pilot and Retried Cloud Sync

**Issue:** Recreating the AD pilot as a fresh `jh.test` domain did not resolve the provisioning timeout.

**Approach:** I rebuilt the domain and pilot OU/user to rule out stale objects or a bad source DN from the first `jh.local` setup. I checked the new user's actual DistinguishedName with `Get-ADUser`, compared the OU scope, confirmed the agent wizard completed, and reviewed provisioning status and logs.

**Action:** I used the DN returned by Active Directory for the pilot user, confirmed that the new Cloud Sync configuration selected the pilot OU, restarted the server and provisioning, then repeated Provision on demand. I examined the latest agent trace for the matching request.

**Result:** Provision on demand still timed out. The trace contained WebSocket connection failures and “Sending Response to Proxy - timeout elapsed.” A basic WebSocket handshake test to the Service Bus endpoint returned HTTP 101, and endpoint TCP checks succeeded for the tested hosts. Those checks confirm that selected endpoints were reachable; they do not establish that the agent's full provisioning exchange worked.

The agent trace also showed an Event Viewer initialization warning for `pap_connector_lib.dll`. The available evidence did not show that this warning caused the provisioning timeout.

## Current Status

| Area | Verified status |
|---|---|
| Entra ID users, groups, and delegated role | Created and visible |
| Sales shared mailbox and mail flow rule | Test message confirmed |
| Windows 11 Entra join and Intune enrollment | Device listed in Intune |
| Company Portal deployment | Installation confirmed in Windows |
| BitLocker and device compliance | BitLocker on; Intune later showed compliant |
| Conditional Access | Report-only evaluation succeeded; enforcement not tested |
| Built-in anti-spam policy | Reviewed |
| Custom anti-spam and anti-malware policies | Creation failed in the portal; not verified |
| AD to Entra Cloud Sync | Agent registered; pilot provisioning unresolved |

## Notes

- No root cause was confirmed for the Cloud Sync timeout. The evidence supports a repeatable timeout in this lab, not a confirmed Microsoft service outage or a confirmed VM limitation.
- Network tests were point-in-time checks from the server VM. A successful TCP or WebSocket handshake does not validate the full agent-to-cloud provisioning request.
- The packet capture was collected for the on-demand attempt. It is a diagnostic artifact, not proof of a specific root cause.
- Keep tenant credentials, recovery keys, full user identifiers, and sensitive packet contents out of public screenshots and repository files.
