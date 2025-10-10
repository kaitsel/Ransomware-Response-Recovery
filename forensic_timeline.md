# Ransomware Attack Forensic Timeline

## Case Overview
- **Attack Type:** Ransomware
- **Target:** NHS Patient Data
- **Discovery Date:** (Enter later)
- **Timeline Period:** September 24, 2025, to (enter later)

## Executive Summary

This forensic timeline outlines the progression of the ransomware attack, from initial reconnaissance to full system encryption. 

## Timeline of Events

### Phase 1: Reconnaissance and Initial Access (Days -? to -?)

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

(look at window event logs - event id 4688? event id 10?? -- on event viewer/sysmon)

(search file for lsass.dmp -- dir /s lsass.dmp || dir /s *sam*)

(check powershell logs)
