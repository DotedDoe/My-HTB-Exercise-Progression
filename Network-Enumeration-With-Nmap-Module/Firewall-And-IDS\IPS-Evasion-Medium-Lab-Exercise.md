### Firewall And IDS\IPS Evasion Medium Lab Exercise
 
IP: 10.129.2.48
 
---
 
### Question 1:
After the configurations are transferred to the system, our client wants to know if it is possible to find out our target's DNS server version. Submit the DNS server version of the target as the answer.
 
I start with an nmap scan aimed at being stealthy. I go for using decoys and making the source port disguise as DNS while I scan for service versions and use basic scripts.
 
```diff
+ $ sudo nmap --source-port 53 -D RND:5 -sV -sC -sS 10.129.2.48
```
 
	21/tcp  open     ftp          ProFTPD 1.3.5e
	22/tcp  open     ssh          OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
	| ssh-hostkey:
	|   2048 71:c1:89:90:7f:fd:4f:60:e0:54:f3:85:e6:35:6c:2b (RSA)
	|   256 e1:8e:53:18:42:af:2a:de:c0:12:1e:2e:54:06:4f:70 (ECDSA)
	|_  256 1a:cc:ac:d4:94:5c:d6:1d:71:e7:39:de:14:27:3c:3c (ED25519)
	53/tcp  filtered domain
	80/tcp  open     http         Apache httpd 2.4.29 ((Ubuntu))
	|_http-server-header: Apache/2.4.29 (Ubuntu)
	|_http-title: Apache2 Ubuntu Default Page: It works
	110/tcp open     pop3         Dovecot pop3d
	|_pop3-capabilities: CAPA TOP PIPELINING AUTH-RESP-CODE UIDL RESP-CODES SASL
	139/tcp open     netbios-ssn  Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
	143/tcp open     imap         Dovecot imapd (Ubuntu)
	|_imap-capabilities: more post-login have LOGINDISABLEDA0001 IMAP4rev1 SASL-IR capabilities listed IDLE Pre-login ENABLE LITERAL+ LOGIN-REFERRALS OK ID
	445/tcp filtered microsoft-ds
	Service Info: Host: HTB984NIFN97CBO783QBNJCPAS984UIN; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
 
Seeing this, I notice port 53 being open (filtered), and it comes to mind that I can maybe banner grab it. I then build an nmap scan targeted at that specific port, using the banner NSE script.
 
```diff
+ $ sudo nmap --source-port 53 -D RND:5 -sV --script banner 10.129.2.48 -p 53
```
 
	PORT   STATE SERVICE VERSION
	53/tcp open  domain  (unknown banner: HTB{GoTtgUnyze9Psw4vGjcuMpHRp})
	| fingerprint-strings:
	|   DNSVersionBindReqTCP:
	|     version
	|     bind
	|_    HTB{GoTtgUnyze9Psw4vGjcuMpHRp}
 
This proves useful, as the banner was the flag.
 
&#x1F6A9; found **HTB{GoTtgUnyze9Psw4vGjcuMpHRp}**.
