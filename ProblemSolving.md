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

## Notes

- A Report-only result evaluates a policy without enforcing its access controls.
- Custom Defender policies are not marked complete unless the portal confirms creation.
- No passwords, MFA setup codes, or BitLocker recovery keys belong in screenshots or repository files.
