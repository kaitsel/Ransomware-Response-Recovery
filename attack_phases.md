# Ransomware Attack Phases Analysis

## Overview

This document outlines the six phases mapped out in the MITRE ATT&CK framework, explaining the tactics, techniques, and procedures employed to create a successful attack. 

## Phase 1: Initial Access and Reconnaissance

### Attack Description

The attackers initiated an attack by sending a phishing email containing a Word document with hidden VBA macros within. Once entry is made, malicious actors could spend days or even weeks on the network before revealing themselves to the company. This was done in the Co-op and M&S attacks this year.

### MITRE ATT&CK Mapping

- T1566.001 - Spearphishing Attachment
- T1598 - Phishing for Information

### Social Engineering Techniques

- Fear (threatens to shut down the employee's access to the network).
- Urgency (creates a fake deadline to rush the user into a split decision).
- Helpfulness (mimics an official business, NHS in this instance, gaining trust).

### Implementation

- Malicious Document: Microsoft Word file containing VBA macros sent to unsuspecting user
- Payload Delivery: Compromises the system unknowingly to the user
- Evasion: Obfuscation of macros to avoid detection and automatically runs upon opening the document

## Phase 2: Execution and Persistence 





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
