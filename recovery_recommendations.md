# Recovery Recommendations

## Executive Summary

The following recommendations are a mixture of technical, administrative, and physical controls that a company should implement to prevent, detect, and correct a Ransomware attack. By using these guidelines, a business, such as the previously attacked Co-op or M&S, will be able to decrease the likelihood and/or impact of the risk.

## Administrative Controls

- **Auditing Logs**
   - During Phase 2 of the attack, the Registry Editor were manipulated, with the creation of two new keys that point to a malicious code (malware.exe). This was unable to be detected during the attack or after in the forensics timeline due to the current setting of the Local Security Policies. Windows does not automatically enable auditing logs for object access, such as the registry. Enabling auditing events for these areas will allow for an increase in **detection**.
   - See below for the newly updated Local Security Policy:
<img width="3840" height="2160" alt="updated policy" src="https://github.com/user-attachments/assets/6b744d40-fb96-43d0-a55d-b1716dc3d2cd" />

- **Windows Security Logs**
  - During Phase 3 of the attack, a new process, Mimikatz, was started in the command prompt. This could not be detected when reviewing the Windows Security logs, as Event ID 4688 is not automatically configured. This Event ID would notify the reviewer that a new process has been created and would enable them to track its lineage. It helps when it comes to determining threats, as it can spot patterns or anomalies within the system. By configuring the 'Audit Process Creation' local policy to 'Success', the company will be better suited to **detecting** any anomalies running on the system.
  - See below for the newly updated Local Security Policy:
<img width="3840" height="2160" alt="local policy audit update" src="https://github.com/user-attachments/assets/dd0387de-022e-4101-8a8f-00673af31ad5" />

- **Incident Response Plan**
   - After the ransomware took over the system, a cohesive response plan is needed in order to **correct** the data that has been lost or leaked, and bring all services back online to reduce the overall loss cost. This should outline the roles within the company in order to optimise the task force to contain the damage and eradicate it from spreading further within the network. For example, the information security groups around the nation will be led by their executive officer to provide a communication line and draw out the exact areas in which this attack took place. The head of the NHS would be responsible for post-incident activities when it comes to addressing public concerns. An overview of the attack should be provided, including recommendations for how those affected can strengthen their security due to the heightened risk from the data breach. This could be through the changing of the passwords, notifying insurance companies of this recent outbreak, so they can be better aware of suspicious activities, and being cautious of links sent through email. This is important to have in any company and to be reviewed at least every two years to make sure it is up-to-date and everyone is still aware of how to handle situations such as this to speed up recovery time.

- **Data Classification**
   - As was found when conducting this project, sensitive data was stored next to regular data, which was both unencrypted and easily accessible to the malicious actor. To better **prevent** easy access, an organised system should be set in place, where data is classified into separate categories of restricted, confidential, internal, and public. This way, the company is able to apply separate security controls to ensure they are continuing to comply with regulations such as HIPAA. It also provides the framework for creating a separation of duties.

## Technical Controls

- **Intrusion Detection System (IDS)**
   - This piece of software will be able to monitor for any malicious activities through either signature or anomaly-based **detection**. For this case study, several suspicious packets were transported over the network, as captured by Wireshark, and so a network-based IDS should be set in place to monitor packets. The other option would be a host-based, which in this study would have been suitable, as all malicious activity transpired throughout one workstation. However, a company such as the NHS is vast, and the cost-benefit of having a host-based IDS on each system would be far less effective than a few network ones. One potential danger with this system is that it can only send alerts upon detection and is unable to interfere. Some sophisticated malware will be able to evade or destroy this notification system without the system administrator's knowledge. This is less likely to be done by a common script kiddie; however, the NHS is a vital tool within the United Kingdom and is vulnerable to elite hackers who may work for organised criminal gangs or nation-states. Take the attacks on Co-Op and M&S, for example. These are popular stores within the country and were taken down by a sophisticated group of criminals. To counter this, obscurity can be used by not disclosing the IDS system to the public to hinder adversaries' planning.
 
- **Multi-Factor Authentication**
   - The adversary in this case study was able to commit the phases easily due to the credential access. It was obtained through an easy password-stealing manoeuvre which could have been **prevented** in two different ways. First is using a more complex password that includes upper and lowercase letters, numbers, and special characters. There are no set global policies within the system on how passwords should be created, which should be immediately rectified. The second important control to implement is multi-factor authentication, which would alert a user if someone were trying to access their workstation with their credentials. This will allow for the actor to be removed safely and the user to reconstruct their password to avoid a repeat attack.
     (insert new global password policy pic here)

- **Quarantine**
   - A useful tool in **correction** is through examining the virus at hand in a safe environment to understand how it works to increase the effectiveness of prevention for next time and to find a path towards recovery. The first step is to isolate the software into a secure area in either the network or the workstation, cutting it off from access to the rest of the untouched data. It should then be tested to ensure it is not a false positive. Treat it with caution and only restore if there is a one-hundred per cent assurance that it is not infected. If it is, then analyse and delete.

- **Patch Management**
  - Throughout the study, several software update notifications were not downloaded, and there are no current policies set into place for automatic patching. This is extremely important for every business and user to include in their defence system, as exploitation, such as the CVE-2023-36874, which was previously discussed during this case, has been **corrected** through a Windows patch. Frequent updating can ensure systems are clear of any unknown zero-day vulnerabilities and **prevent** adversaries from finding a backdoor.
    (insert pic)

## Physical Controls

- **Backups**
   - Create physical backups that are stored offline to ensure they cannot be wiped remotely on the network. This ensures **correction** can take place after the attack promptly, reducing the cost damage of services being offline.

- **Portable Device Protection**
   - All portable devices within the company must include encryption on sensitive data to **prevent** physical theft. It should also make use of privacy screens to avoid over-the-shoulder attacks. The NHS makes use of portable tablets to easily move from room to room and pull up patient details without having to sign in on separate workstations each time, which would decrease operational speed. Adversaries may collect secure information physically to open the door online, gaining access to the network.
 
- **Awareness Training**
   - A common phishing email was used to initiate the attack during this study, which could have been caught by a well-trained eye due to the number of red flags throughout, as found during the forensic review. By providing security training to all employees, **detection** will increase for potential attacks by being more aware of signs surrounding suspicious emails or unwarranted notifications of sign-ins. Not all employees are well-versed in the technology world, but the patients also pose a risk factor, especially the elderly. There should be frequent emails notifying what official emails look like and to never send personal details to anyone claiming to be the NHS, as they would never ask over the phone/email. This can help mitigate the spread of the virus in the system and better **prevent** attacks from occurring. 
