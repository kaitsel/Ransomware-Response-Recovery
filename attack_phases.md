# Ransomware Attack Phases Analysis

## Overview

This document outlines the six phases mapped out in the MITRE ATT&CK framework, explaining the tactics, techniques, and procedures employed to create a successful attack. 

## Phase 1: Initial Access and Reconnaissance

### Attack Description

The attackers initiated an attack by sending a phishing email containing a Word document with hidden VBA macros within. Once entry is made, malicious actors could spend days or even weeks on the network before revealing themselves to the company. This was done in the Co-op and M&S attacks this year.

### MITRE ATT&CK Mapping

- T1566.001 - Spearphishing Attachment
   - The malware is sent through an attachment (Microsoft Office docs / executable PDFs / archived files), relying on user execution.
- T1589.002 - Email Addresses
   - Adversaries will use public-facing emails that are readily available.
- T1564.007 - VBA Stomping
   - Replaces VBA source code with benign data.

### Social Engineering Techniques

- Fear (threatens to shut down the employee's access to the network).
- Urgency (creates a fake deadline to rush the user into a split decision).
- Helpfulness (mimics an official business, NHS in this instance, gaining trust).

### Implementation

- Malicious Document: Microsoft Word file containing VBA macros sent to unsuspecting user
- Payload Delivery: Compromises the system unknowingly to the user
- Evasion: Obfuscation of macros to avoid detection and automatically runs upon opening the document

## Phase 2: Execution and Persistence 

### Attack Description 

After the attacker gains initial access, they must then use techniques to maintain it, as systems are unpredictable due to inevitable restarts, shutdowns, or credential changes. One way to ensure persistent access is by modifying the Registry keys that store the operating system's configuration settings. 

### MITRE ATT&CK Mapping

- T1547.014 - Active Setup
  - Adds Registry key to Active Setup of the local machine, executed when the user logs in.
- T1037.002 - Logout Hook
  - Utilises a plist file as a pointer to a script and executes upon user logging out.
- T1546.005 - Trap
  - Executes malicious content when triggered by an interrupt signal.
 
### Implementation

- PowerShell Script: Creates new registry keys and points to malware.exe
- Registry Editor: Contains all keys, including the added malicious ones
- Living off the Land tactic: Uses legitimate Windows tools for easier execution
- Masquerading: Disguised as authentic Windows system files, prolongs persistence

## Phase 3: Privilege Escalation and Credential Access

### Attack Description

The attacker will escalate their privileges to gain greater access to the network. This will allow the adversary to gain access to employee and customer credentials that may be stored on the system. In the case of Co-Op and M&S, millions of people had active accounts with them, which included information such as their name, date of birth, home address, and online order history. Credit card details that were saved were also at risk of having been leaked. In this case scenario, patient and employee records will be the primary focus for access.

### MITRE ATT&CK Mapping

- T1068 - Exploitation for Privilege Escalation
   - Software vulnerabilities are taken advantage of to achieve higher levels of access.
- T1003.001 - LSASS Memory
   - The Local Security Authority Subsystem Service is accessed, where data from a user login is stored.
- T1555 - Credentials from Password Stores
   - Mimikatz is manipulated to dump credentials for greater access to systems.
- T1552.002 - Unsecured Credentials
   - Insecurely stored passwords are searched through queries.

### Privilege Escalation Vulnerability 

- CVE-2023-366874 (7.8 score): In 2023, an exploitation was discovered in the privilege escalation reporting service. This allowed malicious actors to increase their access level in the system without raising any flags to other users, including the server administrators. This became a zero-day vulnerability and has been patched. However, it can still be used by those who have local access to the system, raising concerns for insider threats.

### Implementation 

- LSASS Dumping: The Local Security Authority Subsystem Service (LSASS) is responsible for authenticating users and managing credentials, allowing for smoother sign-in times for legitimate users. By using the task manager, credentials can be extracted from the system memory.
- Mimikatz: This open-source application can be used to collect passwords in plaintext from memory and is commonly used by black-hat hackers and security professionals.
- Registry: Extracted stored passwords from the registry through the Command Prompt.
- Hash Extraction: Displayed the password hashes in the security account manager database.

## Phase 4: Exploration and Lateral Movement

### Attack Description

By using the elevated credentials gained by the previous step, attackers are able to map a network and identify weak areas to gain access to assets. This allows for a broader surface to cause damage to, expanding from one M&S store, for example, to the whole national branch. This step proves to be more difficult for detection as there is a large quantity of traffic on any given network, and with the escalated credentials, there would be no bells of alarm ringing from any disguised malicious packet transports.
This step has been enhanced by the use of AI, which has removed the need for communication back and forth with a user, removing any return address and creating a more airtight cover for the red-hat hacker. To train the AI, it can be provided a script outlining the targets it should prioritise to take over (see 'Target Prioritisation' below for more information).


### MITRE ATT&CK Mapping

- insert here

### Target Prioritisation 

1. Domain Controllers - These devices house the Active Directory Database, which contains sensitive information about all user accounts connected to the network. By compromising this, red-hat hackers would be able to obtain hashed passwords to further their credential access, increasing the total area of damage.
2. File Servers - The servers tend to contain valuable data worth stealing and usually have underlying weaknesses due to unpatched software, allowing for easy backdoor access. It can then serve as a leaping point to move around the network and start the encryption process when the ransomware is deployed.
3. Database Servers - Accessed through the neglected security of the servers to gain sensitive data. This is also a target for Denial of Service attacks, as the cost for the unexpected downtime is astronomically high in some cases. For example, in the M&S and Co-Op situation, the downtime of the database led to empty shelves in stores as stocks could not be updated and renewed from the inaccessible database.
4. Backup Systems - To ensure the attack meets the desired destruction of the adversary, these systems must be compromised to prevent speedy recovery and ensure the company will pay out in hopes of the key to unencrypting their data. 

### Network Discovery Methods

- Active Directory Enumeration: Queried Active Directory for user accounts, groups, and systems using PowerShell with elevated access achieved through Phase 3.
- Network Scanning: Identified accessible systems and services using the open source tool: 'Nmap'.

### Lateral Movement Methods

- Pass-the-Hash: By using an extracted NTLM hash provided by Mimikatz from the previous step, the adversary can send the hash to another system remotely to gain authorisation without the need for plaintext passwords.

### Current AI Frameworks that can Assist

- BloodHound: Uses machine learning to analyse and prioritise attack paths based on the level of impact the attacker is seeking. Can be found for free on GitHub for anyone's use, whether an authorised penetration tester or malicious user: https://github.com/MorDavid/BloodHound-MCP-AI.git. 
- DeepExploit: Adapts its vulnerability scanning and exploitation to what is most likely to succeed. Once again, this is a free and accessible project on GitHub https://github.com/TheDreamPort/deep_exploit.git.
- MITRE CALDERA: Uses plugins to autonomously explore networks and escalate privileges. It can be found on GitHub for free https://github.com/mitre/caldera.git. 

(to lock the files for the later phase)
import os

// Directory to 'lock' files in (choose a harmless test folder!)
target_dir = "C:/Users/YourUser/Desktop/testfolder"

for filename in os.listdir(target_dir):
    file_path = os.path.join(target_dir, filename)
    if os.path.isfile(file_path):
        # Simulate locking by renaming the file
        os.rename(file_path, file_path + ".locked")

// Create a ransom note
with open(os.path.join(target_dir, "README_RESTORE_FILES.txt"), "w") as f:
    f.write("Your files have been 'locked'. This is a simulation for educational purposes.\nNo data has been harmed.\n")
