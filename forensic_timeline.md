# Ransomware Attack Forensic Timeline

## Case Overview
- **Attack Type:** Ransomware
- **Target:** NHS Patient Data
- **Discovery Date:** (Enter later)
- **Timeline Period:** September 24, 2025, to (enter later)

## Executive Summary

This forensic timeline outlines the progression of the ransomware attack, from initial reconnaissance to full system encryption. 

## Timeline of Events

### Phase 1: Reconnaissance and Initial Access

September 24, 2025, 19:04 GMT - Email Reconnaissance
- Activity: Phishing email campaign initiated
- Target: NHS staff members
- Evidence:
   - Attachment: "IMPORTANT OPEN.docm"
- Forensic Artefacts:
   - Malicious document VBA code
 
September 25, 2025, 11:37 GMT - Initial Compromise
- Activity: Employee andrewbertrude@outlook.com opens a malicious document
- System: Windows 11 (10.0.2.15)
- Evidence:
   - Macro execution in Microsoft Word

### Phase 3: Privilege Escalation and Credential Access

October 1, 2025, 17:09 - 17:13 GMT - Event Viewer Logs
- Activity: Event ID 10016 was logged several times during this date. This event warns that an application is trying to run without the approved permissions. This was most likely caused by the adversary trying to gain credential access and running outside software such as Mimikatz.
- Target: Windows System Logs
- Evidence:
<img width="3840" height="2160" alt="event viewer log - 10016" src="https://github.com/user-attachments/assets/e6577519-20df-4c68-83d4-2d1d049dcd1d" />

October 1, 2025, 17:10 GMT - SAM Creation
- Activity: The directory was checked for the SAM file. There was a positive hit in the config.
- Code:
  ```
  dir /s *sam* #searched all contents of the directory and subdirectories
  ```
- Evidence:
<img width="3840" height="2160" alt="search dir for sam" src="https://github.com/user-attachments/assets/9472dbc4-a0cf-4e83-b55d-3c96bb7073f9" />

October 8, 2025, 18:24 & 18:28 - Dump Files Created
- Activity: Directories were searched for files ending with .dmp. Two hits were found in the LiveKernelDumps directory.
- Code:
  ```
  dir C:\*.dmp /s /a #/a searches all directories, including hidden and system areas
  ```
- Evidence:
<img width="3840" height="2160" alt="dump files found" src="https://github.com/user-attachments/assets/efa3ca9e-23fb-4c96-bc84-dcff5394bb25" />

October 1, 2025 and October 8, 2025 - PowerShell Log Warning Flags Generated
- Activity: Event Viewer was used to check the PowerShell logs for any suspicious activity. By filtering the results, several warning flags were found with Event IDs 4104 and 4100. This was caused by an unauthorised operation trying to be performed. This is most likely due to the actor attempting to gain credential access through testing for vulnerable areas in the domain.
- Evidence:
<img width="3840" height="2160" alt="powershell logs" src="https://github.com/user-attachments/assets/b34e23bc-0c63-4b96-b54b-7f700f3fb9ee" />

### Phase 4: Exploration and Lateral Movement

October 17, 2025, 13:27 GMT - Event ID 4624 Created
- Activity: Several Event ID 4624 were created in the Event Viewer under the Security Log around this time. This signals that a user/process has successfully logged on to the system. However, under the events, a warning has been flagged by the system, warning that impersonation is likely due to unusual patterns or token manipulation being detected.
- Evidence:
  <img width="3840" height="2160" alt="event 4624 was found" src="https://github.com/user-attachments/assets/c56a03c0-985a-4b85-938b-4943ea40e3cd" />

October 17, 2025, 13:23 GMT - TCP SYN Packet
- Activity: By analysing the network transportation around the time of the attack through Wireshark, several TCP SYN packets were captured. This is the first packet sent in a TCP three-way handshake, and due to the amount present, it is evident that port scanning was used within the attack. 
- Code: ``` tcp.flags.syn == 1 && tcp.flags.ack == 0 ```
- Evidence:
  <img width="3840" height="2160" alt="syn scan" src="https://github.com/user-attachments/assets/8d507171-56ec-44e6-a9c2-59e36ad19879" />

October 17, 2025, 13:26 GMT - XMAS Scan
- Activity: Using Wireshark, network traffic was filtered for TCP packets that contain both FIN and URG flags. This is uncommon traffic that is found when using a specialised application and attempting to evade detection by using unusual flag combinations. There was a packet found that fit the filter, flagging the potential for a malicious event to have occurred around this time. 
- Code: ``` tcp.flags.fin == 1 && tcp.flags.urg == 1 ```
- Evidence:
  <img width="3840" height="2160" alt="xmas scan" src="https://github.com/user-attachments/assets/2330b6ae-088c-43da-ba4b-11f789a3144a" />

