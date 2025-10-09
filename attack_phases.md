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

### Privilege Escalation

- CVE-2023-366874 (7.8 score): A reporting service error occurs when it comes to privilege elevation, allowing malicious actors to increase their privilege in a system with no indication being made to other users, including admins of the server.

### Credential Harvesting 

- LSASS Dumping: By using the task manager, credentials were extracted from the system memory.
- Mimikatz: Created a log to collect passwords stored in memory in plaintext.
- Registry: Extracted stored passwords from the registry through the Command Prompt.
- Hash Extraction: Displayed the password hashes in the security account manager database.


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
