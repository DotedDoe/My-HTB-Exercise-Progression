### SMTP Exercise
 
IP: 10.129.74.119
 
---
 
### Question 1:
Enumerate the SMTP service and submit the banner, including its version as the answer.
 
I start with an nmap scan looking at the ports, running default script and version checker against them to see if I can get a banner or version on the SMTP service.
 
```diff
+ $ nmap -Pn -n -sV -sC -A 10.129.74.119
```
 
	<snip>
	25/tcp   open  smtp
	| fingerprint-strings:
	|   Hello:
	|     220 InFreight ESMTP v2.11
	|_    Syntax: EHLO hostname
	|_smtp-commands: mail1, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8, CHUNKING
	<snip>
 
From this, I can see the banner from the server, which includes its version.
 
&#x1F6A9; found **220 InFreight ESMTP v2.11**.
 
---
 
### Question 2:
Enumerate the SMTP service even further and find the username that exists on the system. Submit it as the answer.
 
Since I need to now find a username that exists, I think to use smtp-user-enum, which can be found at https://github.com/cytopia/smtp-user-enum#tada-installation. I also use a wordlist meant for footprinting users, found at https://github.com/PetrGallus/Hacking_Tools/blob/main/footprinting-wordlist.txt.
 
```diff
+ $ smtp-user-enum -U ~/Downloads/footprinting-wordlist.txt -t 10.129.74.119 -v -w20
```
 
	Starting smtp-user-enum v1.2 ( http://pentestmonkey.net/tools/smtp-user-enum )
 
	 ----------------------------------------------------------
	|                   Scan Information                       |
	 ----------------------------------------------------------
 
	Mode ..................... VRFY
	Worker Processes ......... 5
	Usernames file ........... /home/htb-ac-2643052/Downloads/footprinting-wordlist.txt
	Target count ............. 1
	Username count ........... 101
	Target TCP port .......... 25
	Query timeout ............ 20 secs
	Target domain ............
 
	######## Scan started at Mon Jul 27 00:45:23 2026 #########
	<snip>
	10.129.74.119: brenda <no such user>
	10.129.74.119: pamela <no such user>
	10.129.74.119: kelly <no such user>
	10.129.74.119: rachel <no such user>
	10.129.74.119: robin exists
	10.129.74.119: heather <no such user>
	10.129.74.119: adam <no such user>
	10.129.74.119: christine <no such user>
	<snip>
 
From this, I can see that the user robin exists.
 
&#x1F6A9; found **robin**.
 
