# Ransomware Attack Forensic Timeline

## Case Overview
**Attack Type:** Ransomware (Conti variant)  
**Target:** Corporate Network - TechCorp Industries  
**Discovery Date:** 2024-03-15 08:30:00 UTC  
**Timeline Period:** 2024-03-01 to 2024-03-15  

## Executive Summary
This forensic timeline documents a sophisticated ransomware attack that unfolded over 14 days, from initial reconnaissance to full system encryption. The attack demonstrates typical advanced persistent threat (APT) tactics, techniques, and procedures (TTPs) commonly associated with ransomware groups.

---

## Timeline of Events

### Phase 1: Reconnaissance and Initial Access (Days -14 to -10)

#### 2024-03-01 14:23:15 UTC - Email Reconnaissance
- **Activity:** Spear-phishing email campaign initiated
- **Target:** HR and Finance departments
- **Evidence:** 
  - Email headers show spoofed sender domain (techc0rp-industries[.]com)
  - Attachment: "Quarterly_Benefits_Update.docx" (SHA256: a1b2c3d4...)
- **Forensic Artifacts:**
  - Email server logs
  - SMTP header analysis
  - Malicious document samples

#### 2024-03-01 16:45:22 UTC - Initial Compromise
- **Activity:** Employee Jane.Smith@techcorp.com opens malicious document
- **System:** WORKSTATION-HR-05 (192.168.1.105)
- **Evidence:**
  - Macro execution in Microsoft Word
  - PowerShell process spawning from winword.exe
  - Network beacon to C2 server 185.243.112[.]45
- **Forensic Artifacts:**
  - Windows Event Logs (Process Creation - Event ID 4688)
  - PowerShell command history
  - Network connection logs
  - Browser artifacts

#### 2024-03-01 16:47:33 UTC - Malware Dropper Execution
- **Activity:** First-stage payload downloads additional components
- **File:** `%TEMP%\svchost_update.exe` (disguised system file)
- **Evidence:**
  - HTTP GET request to hxxp://cdn-updates[.]net/win/svchost.bin
  - Process injection into legitimate svchost.exe
- **Forensic Artifacts:**
  - Prefetch files
  - Registry modifications (HKCU\Software\Microsoft\Windows\CurrentVersion\Run)
  - File system timeline

### Phase 2: Persistence and Privilege Escalation (Days -10 to -7)

#### 2024-03-04 02:15:41 UTC - Persistence Mechanism Established
- **Activity:** Scheduled task creation for persistence
- **Task Name:** "WindowsSecurityUpdateCheck"
- **Evidence:**
  - Scheduled task runs every 4 hours
  - Points to malicious executable in %APPDATA%\Microsoft\Windows\
- **Forensic Artifacts:**
  - Task Scheduler logs
  - Registry analysis (HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Schedule\TaskCache)

#### 2024-03-05 09:32:18 UTC - Credential Harvesting
- **Activity:** Memory dumping and credential extraction
- **Tool:** Custom LSASS dumper
- **Evidence:**
  - Suspicious process access to lsass.exe (PID 624)
  - Mimikatz-style memory patterns
- **Forensic Artifacts:**
  - Windows Security Event Log (Event ID 4656 - Object Access)
  - Memory forensics artifacts
  - Process memory dumps

#### 2024-03-06 13:45:07 UTC - Privilege Escalation
- **Activity:** Local privilege escalation using CVE-2023-36874
- **Evidence:**
  - Windows Error Reporting service exploitation
  - SYSTEM level process spawning
- **Forensic Artifacts:**
  - System Event Logs
  - Error reporting dumps
  - Token privilege modifications

### Phase 3: Discovery and Lateral Movement (Days -7 to -3)

#### 2024-03-08 10:22:31 UTC - Network Discovery
- **Activity:** Active Directory enumeration
- **Tools:** Custom PowerShell scripts, AdFind.exe
- **Evidence:**
  - LDAP queries to domain controllers
  - Network scanning on ports 445, 3389, 135
- **Forensic Artifacts:**
  - PowerShell command history
  - Network flow data
  - Domain controller security logs

#### 2024-03-09 15:17:44 UTC - Lateral Movement Begins
- **Activity:** RDP connection to file server
- **Source:** WORKSTATION-HR-05
- **Target:** FILESERVER-01 (192.168.1.10)
- **Evidence:**
  - RDP login using compromised admin credentials
  - File access on network shares
- **Forensic Artifacts:**
  - RDP logs (Event ID 4624, 4625)
  - File access logs
  - Network authentication logs

#### 2024-03-10 08:30:12 UTC - Additional System Compromise
- **Activity:** Domain controller compromise
- **Target:** DC-PRIMARY (192.168.1.5)
- **Evidence:**
  - Golden ticket creation
  - NTDS.dit database access
- **Forensic Artifacts:**
  - Kerberos authentication logs
  - NTDS database forensics
  - Active Directory replication logs

### Phase 4: Data Exfiltration Preparation (Days -3 to -1)

#### 2024-03-12 20:45:18 UTC - Data Discovery and Staging
- **Activity:** Sensitive data identification and collection
- **Targets:** 
  - Financial databases
  - Customer records
  - Intellectual property
- **Evidence:**
  - Large file transfers to staging directory
  - Database query logs showing bulk data access
- **Forensic Artifacts:**
  - File system access logs
  - Database audit trails
  - Network storage logs

#### 2024-03-13 02:15:33 UTC - Data Exfiltration
- **Activity:** Sensitive data uploaded to external servers
- **Method:** HTTPS POST requests to cloud storage
- **Volume:** ~2.3 TB of data
- **Evidence:**
  - Encrypted data streams to 5.149.248[.]73
  - Suspicious network traffic patterns
- **Forensic Artifacts:**
  - Network packet captures
  - Proxy/firewall logs
  - Bandwidth usage anomalies

### Phase 5: Ransomware Deployment (Day 0)

#### 2024-03-15 05:30:00 UTC - Ransomware Staging
- **Activity:** Ransomware binary distributed across network
- **File:** `update_security.exe` (Conti ransomware variant)
- **Evidence:**
  - SMB file shares used for distribution
  - Group Policy used for automated execution
- **Forensic Artifacts:**
  - Group Policy logs
  - SMB session logs
  - File distribution timeline

#### 2024-03-15 06:00:00 UTC - Coordinated Execution
- **Activity:** Ransomware execution across all compromised systems
- **Evidence:**
  - Simultaneous process creation on 150+ workstations
  - File encryption begins (targeting documents, databases, backups)
  - Ransom notes dropped: `HOW_TO_RESTORE_FILES.txt`
- **Forensic Artifacts:**
  - Process creation logs (Event ID 4688)
  - File modification timestamps
  - Ransom note artifacts

#### 2024-03-15 06:15:00 UTC - System Impact
- **Activity:** Critical systems become unavailable
- **Evidence:**
  - File extensions changed to .conti
  - Database corruption
  - Backup systems compromised
- **Forensic Artifacts:**
  - System error logs
  - Application failure logs
  - Backup system logs

#### 2024-03-15 08:30:00 UTC - Discovery and Response
- **Activity:** Attack discovered by IT staff
- **Evidence:**
  - Multiple user reports of inaccessible files
  - Ransom note discovered on desktop screens
  - Network monitoring alerts triggered
- **Forensic Artifacts:**
  - Help desk tickets
  - Monitoring system alerts
  - Initial incident response logs

---

## Key Forensic Artifacts Summary

### System Artifacts
- Windows Event Logs (Security, System, Application)
- Registry modifications and timeline
- Prefetch files and application execution artifacts
- File system metadata and timeline analysis
- Memory dumps and volatile data

### Network Artifacts
- Network flow data and packet captures
- DNS queries and resolutions
- HTTP/HTTPS traffic analysis
- SMB and RDP session logs
- Firewall and proxy logs

### Application Artifacts
- Email headers and attachment analysis
- Browser history and downloads
- Database access and audit logs
- Scheduled task creation and execution
- PowerShell command history

### Malware Artifacts
- Static and dynamic malware analysis
- IOCs (Indicators of Compromise)
- Command and control communications
- Persistence mechanisms
- Encryption routines and file markers

---

## Lessons Learned

1. **Early Detection Opportunities:** Multiple detection points were missed during the 14-day timeline
2. **Lateral Movement:** Insufficient network segmentation allowed rapid spread
3. **Backup Failures:** Backup systems were not adequately protected
4. **User Training:** Social engineering awareness could have prevented initial compromise

## Next Steps
- Complete forensic analysis and evidence preservation
- Implement incident response procedures
- Begin recovery and restoration processes
- Conduct lessons learned session and improve security controls

---

*This timeline is created for educational purposes as part of BFOR 419 coursework. All timestamps, IP addresses, and system names are fictional.*