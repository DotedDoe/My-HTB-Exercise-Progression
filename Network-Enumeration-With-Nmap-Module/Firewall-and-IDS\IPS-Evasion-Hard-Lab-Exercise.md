### Firewall and IDS\IPS Evasion Hard Lab Exercise
 
IP: 10.129.2.47
 
---
 
### Question 1:
Now our client wants to know if it is possible to find out the version of the running services. Identify the version of service our client was talking about and submit the flag as the answer.
 
I start off with an nmap scan to get a view of the services on the network. Trying to avoid IDS/IPS detection, I make sure to disguise my source port as 53 and look for versions of the services.
 
```diff
+ $ sudo nmap -Pn -n -sS -sV -sC --source-port 53 10.129.2.47
```
 
	PORT      STATE SERVICE    VERSION
	22/tcp    open  ssh        OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
	| ssh-hostkey:
	|   2048 71:c1:89:90:7f:fd:4f:60:e0:54:f3:85:e6:35:6c:2b (RSA)
	|   256 e1:8e:53:18:42:af:2a:de:c0:12:1e:2e:54:06:4f:70 (ECDSA)
	|_  256 1a:cc:ac:d4:94:5c:d6:1d:71:e7:39:de:14:27:3c:3c (ED25519)
	80/tcp    open  http       Apache httpd 2.4.29 ((Ubuntu))
	|_http-server-header: Apache/2.4.29 (Ubuntu)
	|_http-title: Apache2 Ubuntu Default Page: It works
	50000/tcp open  tcpwrapped
	|_drda-info: TIMEOUT
	Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
 
Seeing this service on port 50000, and that trying to get info on it timed out, sparks my interest. I try investigating further by targeting that port specifically and adding a banner NSE script, to no avail.
 
I then think to try banner grabbing with ncat, disguising my source port as 53 as I do so.
 
```diff
+ $ sudo ncat --source-port 53 10.129.2.47 50000
```
 
	220 HTB{kjnsdf2n982n1827eh76238s98dilw6}
 
This does the trick and doesn't get timed out.
 
&#x1F6A9; found **HTB{kjnsdf2n982n1827eh76238s98dilw6}**.
