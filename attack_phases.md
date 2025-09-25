# Ransomware Attack Phases Analysis

## Overview

This document outlines the six phases mapped out in the MITRE ATT&CK framework, explaining the tactics, techniques, and procedures employed to create a successful attack. 

## Phase 1: Initial Access and Reconnaissance






(to lock the files for later phase)
import os

# Directory to 'lock' files in (choose a harmless test folder!)
target_dir = "C:/Users/YourUser/Desktop/testfolder"

for filename in os.listdir(target_dir):
    file_path = os.path.join(target_dir, filename)
    if os.path.isfile(file_path):
        # Simulate locking by renaming the file
        os.rename(file_path, file_path + ".locked")

# Create a ransom note
with open(os.path.join(target_dir, "README_RESTORE_FILES.txt"), "w") as f:
    f.write("Your files have been 'locked'. This is a simulation for educational purposes.\nNo data has been harmed.\n")
