### Nmap Enumeration Exercise
 
IP: 10.129.2.49
 
---
 
### Question 1:
Find all TCP ports on your target. Submit the total number of found TCP ports as the answer.
 
My first nmap scan is aimed at finding the active TCP ports, I structure it like so.
 
```diff
+ $ sudo nmap --source-port 53 -sS -Pn 10.129.2.49
```
 
	PORT      STATE  SERVICE
	22/tcp    open   ssh
	80/tcp    open   http
	110/tcp   open   pop3
	139/tcp   open   netbios-ssn
	143/tcp   open   imap
	445/tcp   open   microsoft-ds
	31337/tcp open   Elite
 
This shows off a total of 7 ports open on the target.
 
&#x1F6A9; found **7**.
 
---
 
### Question 2:
Enumerate the hostname of your target and submit it as the answer. (case-sensitive)
 
Then I construct another nmap scan.
 
```diff
+ $ sudo nmap --source-port 53 -sS -Pn -sV 10.129.2.49
```
 
	PORT      STATE  SERVICE      VERSION
	22/tcp    open   ssh          OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
	80/tcp    open   http         Apache httpd 2.4.29 ((Ubuntu))
	110/tcp   open   pop3         Dovecot pop3d
	139/tcp   open   netbios-ssn  Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
	143/tcp   open   imap         Dovecot imapd (Ubuntu)
	445/tcp   open   microsoft-ds Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
	31337/tcp open   Elite
	Service Info: Host: NIX-NMAP-DEFAULT; OS: Linux; CPE: cpe:/o:linux:linux_kernel
 
From this I see that the hostname of the target is NIX-NMAP-DEFAULT.
 
&#x1F6A9; found **NIX-NMAP-DEFAULT**.
