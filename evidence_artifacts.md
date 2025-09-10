# Forensic Evidence Artifacts

## Overview
This document catalogs the forensic artifacts that would be collected and analyzed during the investigation of the ransomware attack. These artifacts are organized by category and include their forensic value and analysis techniques.

---

## Digital Evidence Categories

### 1. Email Evidence

#### Artifacts Collected
- **Original Email Messages** (.eml, .msg files)
- **Email Headers** (SMTP routing information)
- **Attachment Files** (malicious documents)
- **Email Server Logs** (Exchange, Postfix logs)

#### Analysis Value
- **Attack Vector Identification:** Confirms spear-phishing as initial compromise
- **Attribution:** Email metadata may reveal attacker infrastructure
- **Timeline Establishment:** Message timestamps provide attack timeline
- **IoC Development:** Email addresses and domains for blocking

#### Sample Evidence
```
Received: from mail.techc0rp-industries.com (185.243.112.45)
    by mx1.techcorp.com with ESMTP id ABC123
    Fri, 01 Mar 2024 14:23:15 +0000
From: hr-admin@techc0rp-industries.com
To: all-staff@techcorp.com
Subject: Quarterly Benefits Update - Action Required
Message-ID: <20240301142315.ABC123@techc0rp-industries.com>
```

### 2. Malware Samples

#### Artifacts Collected
- **Initial Dropper** (Quarterly_Benefits_Update.docx)
- **Second Stage Payload** (svchost_update.exe)
- **Ransomware Binary** (update_security.exe)
- **Memory Dumps** (containing decrypted payloads)

#### Analysis Value
- **Malware Family Identification:** Confirms Conti ransomware variant
- **Capabilities Assessment:** Understanding of malware functionality
- **IoC Development:** File hashes, mutexes, registry keys
- **Attribution:** Code similarities to known threat groups

#### Sample Hash Values
```
SHA256 Hashes:
- Quarterly_Benefits_Update.docx: a1b2c3d4e5f6789012345678901234567890abcdef
- svchost_update.exe: b2c3d4e5f6789012345678901234567890abcdef12
- update_security.exe: c3d4e5f6789012345678901234567890abcdef1234
```

### 3. System Artifacts

#### Windows Event Logs
- **Security.evtx** (logon events, privilege escalation)
- **System.evtx** (service installations, system changes)
- **Application.evtx** (application errors and warnings)
- **PowerShell/Operational.evtx** (script execution)

#### Registry Evidence
- **Persistence Keys:**
  - `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`
  - `HKLM\Software\Microsoft\Windows\CurrentVersion\Run`
- **Scheduled Tasks:**
  - `HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Schedule\TaskCache`
- **Service Installations:**
  - `HKLM\SYSTEM\CurrentControlSet\Services`

#### File System Artifacts
- **Prefetch Files** (.pf files showing program execution)
- **Jump Lists** (recent file access patterns)
- **LNK Files** (shortcut files with timestamps)
- **NTFS Journal** (file system change log)
- **USN Journal** (Update Sequence Number records)

#### Sample Registry Evidence
```
Registry Key: HKCU\Software\Microsoft\Windows\CurrentVersion\Run
Value: WindowsSecurityUpdater
Data: C:\Users\jane.smith\AppData\Roaming\Microsoft\Windows\svchost_update.exe
Timestamp: 2024-03-04 02:15:41 UTC
```

### 4. Network Evidence

#### Network Flow Data
- **NetFlow/sFlow Records** (network communication patterns)
- **DNS Logs** (domain resolution attempts)
- **Proxy Logs** (web traffic analysis)
- **Firewall Logs** (blocked and allowed connections)

#### Packet Captures
- **Initial C2 Communication** (first beacon to 185.243.112.45)
- **Data Exfiltration** (HTTPS uploads to 5.149.248.73)
- **Lateral Movement** (SMB and RDP sessions)
- **Ransomware Deployment** (network propagation)

#### Sample Network Evidence
```
NetFlow Record:
Start Time: 2024-03-01 16:47:45 UTC
Source IP: 192.168.1.105 (WORKSTATION-HR-05)
Destination IP: 185.243.112.45
Protocol: TCP
Port: 443
Bytes: 1,247,892
Packets: 1,205
Duration: 00:15:23
```

### 5. Database Evidence

#### Database Logs
- **SQL Server Transaction Logs** (data access patterns)
- **MySQL Binary Logs** (database modifications)
- **Oracle Audit Trails** (privileged access events)
- **Application Database Logs** (custom application data access)

#### Sample Database Evidence
```
SQL Server Audit Log:
Timestamp: 2024-03-12 20:45:18 UTC
Login: TECHCORP\compromised_admin
Database: CustomerDB
Action: SELECT
Object: Customers, Orders, PaymentInfo
Statement: SELECT * FROM Customers INNER JOIN Orders...
Result: SUCCESS
Rows Affected: 245,892
```

### 6. Memory Forensics

#### Memory Dumps
- **Full System Memory Dumps** (complete RAM image)
- **Process Memory Dumps** (specific process analysis)
- **Hibernation Files** (hiberfil.sys analysis)
- **Page Files** (pagefile.sys forensics)

#### Analysis Results
- **Running Processes** (malicious process identification)
- **Network Connections** (active C2 communications)
- **Loaded Modules** (injected DLLs)
- **Encryption Keys** (potential key recovery)

#### Sample Memory Analysis
```
Volatility Analysis Output:
Process: svchost.exe (PID: 1284)
PPID: 624 (services.exe)
Threads: 8
Handles: 156
Session: 0
Created: 2024-03-01 16:47:50 UTC
Status: RUNNING
Command Line: C:\Windows\System32\svchost.exe
Note: Unusual for svchost to have network connections to external IPs
```

### 7. Active Directory Evidence

#### AD Database (NTDS.dit)
- **User Account Information** (compromised accounts)
- **Group Memberships** (privilege escalation paths)
- **Password Hashes** (credential extraction evidence)
- **Replication Metadata** (unauthorized changes)

#### Kerberos Logs
- **Authentication Events** (TGT/TGS requests)
- **Golden Ticket Activity** (forged ticket usage)
- **Service Principal Names** (targeted services)
- **Account Lockout Events** (brute force attempts)

#### Sample AD Evidence
```
Security Event 4769 - Kerberos Service Ticket Request:
Account Name: compromised_admin@TECHCORP.COM
Service Name: cifs/FILESERVER-01.techcorp.com
Client Address: 192.168.1.105
Ticket Encryption Type: 0x17 (AES256-CTS-HMAC-SHA1-96)
Ticket Options: 0x60810010
Result Code: 0x0 (Success)
Note: Unusual service ticket requests from HR workstation
```

### 8. Backup System Evidence

#### Backup Logs
- **Backup Job Logs** (successful/failed backups)
- **Retention Policies** (data availability)
- **Restore Point Information** (available recovery options)
- **Backup Verification Logs** (integrity checks)

#### Impact Assessment
- **Compromised Backups** (encrypted backup files)
- **Available Clean Backups** (pre-infection backups)
- **Backup System Logs** (attacker access evidence)
- **Recovery Time Estimates** (business impact)

---

## Indicators of Compromise (IoCs)

### File Hashes
```
MD5 Hashes:
- Quarterly_Benefits_Update.docx: 5d41402abc4b2a76b9719d911017c592
- svchost_update.exe: 7d865e959b2466918c9863afca942d0f
- update_security.exe: 098f6bcd4621d373cade4e832627b4f6

SHA1 Hashes:
- Quarterly_Benefits_Update.docx: aaf4c61ddcc5e8a2dabede0f3b482cd9aea9434d
- svchost_update.exe: 356a192b7913b04c54574d18c28d46e6395428ab
- update_security.exe: da4b9237bacccdf19c0760cab7aec4a8359010b0
```

### Network Indicators
```
C2 Servers:
- 185.243.112.45 (primary C2)
- 5.149.248.73 (exfiltration server)
- cdn-updates[.]net (payload hosting)
- backup-sync[.]org (backup destruction tool)

Domains:
- techc0rp-industries[.]com (typosquatted domain)
- windows-security-update[.]net (fake update server)
- cloud-backup-service[.]org (fake backup service)
```

### Registry Artifacts
```
Persistence Locations:
- HKCU\Software\Microsoft\Windows\CurrentVersion\Run\WindowsSecurityUpdater
- HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Schedule\TaskCache\Tree\WindowsSecurityUpdateCheck

Service Installations:
- HKLM\SYSTEM\CurrentControlSet\Services\WinSecuritySvc
```

### File System Indicators
```
Dropped Files:
- %TEMP%\svchost_update.exe
- %APPDATA%\Microsoft\Windows\security_update.exe
- %SYSTEMROOT%\System32\drivers\winsec.sys

Ransom Notes:
- HOW_TO_RESTORE_FILES.txt
- README_IMPORTANT.txt
- DECRYPT_INSTRUCTIONS.html
```

---

## Chain of Custody Documentation

### Evidence Collection Log
| Item | Description | Location | Collector | Date/Time | Hash |
|------|-------------|----------|-----------|-----------|------|
| E001 | WORKSTATION-HR-05 Hard Drive | Building A, Cube 15 | J. Analyst | 2024-03-15 10:30 | abc123... |
| E002 | Email Server Memory Dump | Server Room Rack 3 | J. Analyst | 2024-03-15 11:45 | def456... |
| E003 | Network Packet Capture | Security Operations Center | S. Engineer | 2024-03-15 09:15 | ghi789... |

### Evidence Analysis Results

#### Timeline Reconstruction
- **Attack Duration:** 14 days (2024-03-01 to 2024-03-15)
- **Systems Compromised:** 150+ workstations, 5 servers
- **Data Exfiltrated:** ~2.3 TB
- **Ransom Demand:** $2.5M in Bitcoin

#### Attribution Indicators
- **TTP Alignment:** Consistent with Conti ransomware group
- **Infrastructure Overlap:** Shared C2 servers with previous attacks
- **Code Similarities:** 89% code overlap with known Conti samples

---

## Legal Considerations

### Evidence Preservation
- **Write Protection:** All storage devices write-protected during collection
- **Hashing:** SHA-256 hashes calculated for all evidence
- **Documentation:** Detailed collection procedures documented
- **Chain of Custody:** Continuous custody maintained

### Regulatory Requirements
- **Data Breach Notifications:** Required within 72 hours (GDPR)
- **Law Enforcement:** FBI and local authorities notified
- **Industry Reporting:** Sharing with industry threat intelligence
- **Insurance Claims:** Evidence supporting insurance claims

---

*This evidence catalog is created for educational purposes as part of BFOR 419 coursework. All artifacts and indicators are fictional examples based on real-world forensic investigations.*