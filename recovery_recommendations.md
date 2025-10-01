# Recovery Recommendations

## Executive Summary

The following recommendations are a mixture of technical, administrative, and physical controls that a company should implement to prevent, detect, and correct a Ransomware attack. By using these guidelines, a business, such as the previously attacked Co-op or M&S, will be able to decrease the likelihood and/or impact of the risk.

## Administrative Controls

- **Auditing Logs**
   - During Phase 2 of the attack, the Registry Editor were manipulated, with the creation of two new keys that point to a malicious code (malware.exe). This was unable to be detected during the attack or after in the forensics timeline due to the current setting of the Local Security Policies. Windows does not automatically enable auditing logs for object access, such as the registry. Enabling auditing events for these areas will allow for an increase in **detection**.
   - See below for the newly updated Local Security Policy:

<img width="3840" height="2160" alt="updated policy" src="https://github.com/user-attachments/assets/6b744d40-fb96-43d0-a55d-b1716dc3d2cd" />


## Technical Controls


## Physical Controls
