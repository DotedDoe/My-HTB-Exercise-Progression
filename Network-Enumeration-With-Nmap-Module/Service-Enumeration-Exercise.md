### Service Enumeration Exercise
 
IP: 10.129.72.138
 
---
 
### Question 1:
Enumerate all ports and their services. One of the services contains the flag you have to submit as the answer.
 
Ran an nmap scan that followed as:
 
```diff
+ $ nmap -Pn -sV -sC --source-port 53 10.129.72.138
```
 
	PORT      STATE SERVICE     VERSION
	22/tcp    open  ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
	| ssh-hostkey:
	|   2048 71:c1:89:90:7f:fd:4f:60:e0:54:f3:85:e6:35:6c:2b (RSA)
	|   256 e1:8e:53:18:42:af:2a:de:c0:12:1e:2e:54:06:4f:70 (ECDSA)
	|_  256 1a:cc:ac:d4:94:5c:d6:1d:71:e7:39:de:14:27:3c:3c (ED25519)
	80/tcp    open  http        Apache httpd 2.4.29 ((Ubuntu))
	|_http-title: Apache2 Ubuntu Default Page: It works
	|_http-server-header: Apache/2.4.29 (Ubuntu)
	110/tcp   open  pop3        Dovecot pop3d
	|_pop3-capabilities: SASL AUTH-RESP-CODE PIPELINING CAPA RESP-CODES TOP UIDL
	139/tcp   open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
	143/tcp   open  imap        Dovecot imapd (Ubuntu)
	|_imap-capabilities: SASL-IR LITERAL+ LOGINDISABLEDA0001 IMAP4rev1 ID post-login ENABLE OK listed IDLE more have LOGIN-REFERRALS capabilities Pre-login
	445/tcp   open  netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
	31337/tcp open  Elite?
	| fingerprint-strings:
	|   GetRequest:
	|_    220 HTB{pr0F7pDv3r510nb4nn3r}
	1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
	SF-Port31337-TCP:V=7.95%I=7%D=7/25%Time=6A6459B8%P=x86_64-pc-linux-gnu%r(G
	SF:etRequest,1F,"220\x20HTB{pr0F7pDv3r510nb4nn3r}\r\n");
	Service Info: Host: NIX-NMAP-DEFAULT; OS: Linux; CPE: cpe:/o:linux:linux_kernel
 
Found the flag on port 31337's banner.
 
&#x1F6A9; found **HTB{pr0F7pDv3r510nb4nn3r}**.
