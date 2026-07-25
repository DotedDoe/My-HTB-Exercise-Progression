### Firewall and IDS/IPS Evasion Easy Lab Exercise
 
IP: 10.129.2.80
 
---
 
### Question 1:
Our client wants to know if we can identify which operating system their provided machine is running on. Submit the OS name as the answer.
 
I think to be sneaky and go with this nmap scan.
 
```diff
+ $ sudo nmap -sV -sS --source-port 53 -Pn 10.129.2.80
```
 
	PORT      STATE SERVICE     VERSION
	22/tcp    open  ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
	80/tcp    open  http        Apache httpd 2.4.29 ((Ubuntu))
	10001/tcp open  scp-config?
 
From this output, I can tell that the services are distributed for an Ubuntu OS, meaning that the machine's OS is Ubuntu.
 
&#x1F6A9; found **Ubuntu**.
 
