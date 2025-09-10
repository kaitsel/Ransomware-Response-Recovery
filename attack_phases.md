# Ransomware Attack Phases Analysis

## Overview
This document provides detailed analysis of each phase of the ransomware attack, explaining the tactics, techniques, and procedures (TTPs) used by the attackers, mapped to the MITRE ATT&CK framework.

---

## Phase 1: Initial Access and Reconnaissance

### MITRE ATT&CK Mapping
- **T1566.001** - Spearphishing Attachment
- **T1598** - Phishing for Information
- **T1082** - System Information Discovery

### Attack Description
The attackers initiated their campaign with a carefully crafted spear-phishing email targeting employees in HR and Finance departments. This approach demonstrates:

#### Social Engineering Tactics
- **Domain Spoofing:** Used a typosquatted domain (techc0rp-industries[.]com vs techcorp-industries.com)
- **Contextual Relevance:** Email claimed to contain quarterly benefits information, relevant to HR staff
- **Timing:** Sent during business hours to increase likelihood of interaction

#### Technical Implementation
- **Malicious Document:** Microsoft Word document containing VBA macros
- **Payload Delivery:** Macro downloads second-stage payload from compromised infrastructure
- **Evasion Techniques:** 
  - Document appears legitimate with corporate branding
  - Macros use obfuscation to avoid AV detection
  - Delayed execution after document opening

### Forensic Evidence Patterns
- Email metadata analysis reveals suspicious headers
- Document analysis shows embedded macros and external connections
- Network traffic shows initial beacon to C2 infrastructure

---

## Phase 2: Execution and Persistence

### MITRE ATT&CK Mapping
- **T1059.001** - PowerShell
- **T1055** - Process Injection
- **T1053.005** - Scheduled Task/Job
- **T1547.001** - Registry Run Keys

### Attack Description
After successful initial compromise, attackers established persistent access through multiple mechanisms:

#### Execution Chain
1. **Macro Execution:** VBA macro executes PowerShell command
2. **PowerShell Script:** Downloads and executes additional payload
3. **Process Injection:** Injects malicious code into legitimate Windows processes
4. **Persistence:** Creates scheduled tasks and registry entries

#### Persistence Mechanisms
- **Scheduled Tasks:** Creates "WindowsSecurityUpdateCheck" task for periodic execution
- **Registry Persistence:** Adds entries to autorun locations
- **Service Installation:** Installs malicious service disguised as Windows component
- **File System:** Places executables in Windows system directories with legitimate names

### Defense Evasion
- **Living off the Land:** Uses legitimate Windows tools (PowerShell, WMI)
- **Masquerading:** Disguises malicious files as Windows system files
- **Process Injection:** Hides in legitimate processes to avoid detection

---

## Phase 3: Privilege Escalation and Credential Access

### MITRE ATT&CK Mapping
- **T1068** - Exploitation for Privilege Escalation
- **T1003.001** - LSASS Memory
- **T1558.001** - Golden Ticket

### Attack Description
Attackers escalated privileges and harvested credentials to gain broader network access:

#### Privilege Escalation
- **CVE-2023-36874:** Exploited Windows Error Reporting vulnerability
- **SYSTEM Access:** Gained highest level of privileges on compromised systems
- **Token Manipulation:** Modified security tokens to maintain elevated access

#### Credential Harvesting
- **LSASS Dumping:** Extracted credentials from system memory
- **Mimikatz Techniques:** Used similar methods to harvest plaintext passwords
- **Cached Credentials:** Accessed cached domain credentials
- **Registry Secrets:** Extracted stored passwords from registry

#### Advanced Techniques
- **Golden Ticket Creation:** Generated forged Kerberos tickets for domain persistence
- **NTDS Database Access:** Compromised Active Directory database
- **Hash Extraction:** Obtained password hashes for offline cracking

---

## Phase 4: Discovery and Lateral Movement

### MITRE ATT&CK Mapping
- **T1018** - Remote System Discovery
- **T1083** - File and Directory Discovery
- **T1021.001** - Remote Desktop Protocol
- **T1558** - Steal or Forge Kerberos Tickets

### Attack Description
With elevated credentials, attackers mapped the network and moved laterally:

#### Network Discovery
- **Active Directory Enumeration:** Queried AD for user accounts, groups, and systems
- **Network Scanning:** Identified accessible systems and services
- **Share Discovery:** Located network file shares and databases
- **Administrative Tools:** Used legitimate AD tools for reconnaissance

#### Lateral Movement Methods
- **RDP Connections:** Used Remote Desktop with compromised credentials
- **SMB Lateral Movement:** Leveraged file sharing protocols
- **WMI Execution:** Executed commands remotely via Windows Management Instrumentation
- **Pass-the-Hash:** Used harvested NTLM hashes for authentication

#### Target Prioritization
- **Domain Controllers:** Highest priority for complete network control
- **File Servers:** Targeted for data access and ransomware deployment
- **Database Servers:** Accessed for sensitive data extraction
- **Backup Systems:** Compromised to prevent recovery

---

## Phase 5: Collection and Exfiltration

### MITRE ATT&CK Mapping
- **T1560** - Archive Collected Data
- **T1041** - Exfiltration Over C2 Channel
- **T1048.003** - Exfiltration Over Unencrypted Non-C2 Protocol

### Attack Description
Before deploying ransomware, attackers collected and exfiltrated sensitive data:

#### Data Discovery
- **Sensitive File Identification:** Located financial records, customer data, IP
- **Database Queries:** Accessed structured data through legitimate database connections
- **Share Enumeration:** Catalogued network file shares for valuable information
- **Email Access:** Compromised email servers for communication data

#### Collection and Staging
- **Data Aggregation:** Consolidated valuable files into staging directories
- **Compression:** Created encrypted archives to reduce transfer size
- **Staging Servers:** Used compromised internal systems as collection points
- **Access Logging:** Maintained detailed logs of accessed data for leverage

#### Exfiltration Methods
- **HTTPS Upload:** Encrypted transfers to attacker-controlled servers
- **Cloud Storage:** Utilized legitimate cloud services to avoid detection
- **DNS Tunneling:** Small data exfiltration through DNS queries
- **Steganography:** Hid data in image files for covert transfer

---

## Phase 6: Impact and Ransomware Deployment

### MITRE ATT&CK Mapping
- **T1486** - Data Encrypted for Impact
- **T1490** - Inhibit System Recovery
- **T1489** - Service Stop
- **T1491** - Defacement

### Attack Description
The final phase involved coordinated ransomware deployment across the network:

#### Pre-Deployment Preparation
- **Backup Destruction:** Deleted or encrypted backup systems
- **Security Disabling:** Stopped antivirus and security services
- **Shadow Copy Deletion:** Removed Windows restore points
- **Service Termination:** Stopped database and application services

#### Ransomware Execution
- **Coordinated Launch:** Simultaneous execution across compromised systems
- **File Encryption:** Targeted documents, databases, and media files
- **Key Management:** Unique encryption keys per system/file type
- **Progress Monitoring:** Tracked encryption progress across network

#### Psychological Impact
- **Ransom Notes:** Displayed threatening messages on all screens
- **Data Proof:** Provided samples of exfiltrated data as leverage
- **Communication Channels:** Established secure channels for negotiations
- **Deadline Pressure:** Imposed strict payment deadlines with escalating threats

---

## Detection Opportunities

### Early Stage Detection
- **Email Security:** Advanced threat detection could identify spear-phishing
- **Endpoint Detection:** Behavioral analysis could detect macro execution
- **Network Monitoring:** C2 communication patterns could trigger alerts

### Mid-Stage Detection
- **Privilege Escalation:** Unusual process privileges could be detected
- **Lateral Movement:** Abnormal authentication patterns could raise flags
- **Data Access:** Unusual database or file access patterns

### Late Stage Detection
- **Data Staging:** Large file movements could be detected
- **Exfiltration:** Unusual network traffic patterns
- **Service Disruption:** System performance degradation

---

## Recommendations for Prevention

### Technical Controls
1. **Email Security:** Implement advanced threat protection for email
2. **Endpoint Protection:** Deploy EDR solutions with behavioral analysis
3. **Network Segmentation:** Limit lateral movement opportunities
4. **Privileged Access Management:** Control and monitor administrative access
5. **Backup Security:** Protect backup systems from compromise

### Procedural Controls
1. **Security Awareness Training:** Regular phishing simulation and training
2. **Incident Response Planning:** Develop and test response procedures
3. **Vulnerability Management:** Regular patching and security updates
4. **Access Reviews:** Regular review of user privileges and access

### Detective Controls
1. **Security Monitoring:** 24/7 SOC with advanced analytics
2. **Log Analysis:** Centralized logging with behavioral analysis
3. **Threat Hunting:** Proactive threat hunting activities
4. **Penetration Testing:** Regular security assessments

---

*This analysis is created for educational purposes as part of BFOR 419 coursework. All attack scenarios are fictional and based on real-world threat intelligence.*