### Sessions Exercise
 
IP: 154.57.164.64
 
---
 
### Question 1:
Try running some of the web enumeration techniques you learned in this section on the server above, and use the info you get to get the flag.
 
I start off with an Nmap scan against the host, targeting the port I'm meant to attack.
 
```diff
+ $ nmap -A -sV -sT -sC -Pn 154.57.164.64 -p 30348
```
 
	PORT      STATE SERVICE VERSION
	30348/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
	| http-robots.txt: 1 disallowed entry
	|_/admin-login-page.php
	|_http-title: HTB Academy
	|_http-server-header: Apache/2.4.41 (Ubuntu)
 
This shows that it's an Apache web server, so I go onto my browser and navigate to the IP, including its port, and check out the robots.txt.
 
I notice 1 disallowed entry being an admin-login-page.php, which is exactly as it's named. Inspecting the source code I see a comment mentioning test credentials admin:password123.
 
Inputting those into the login works and gives me the flag for the machine.
 
&#x1F6A9; found **HTB{w3b_3num3r4710n_r3v34l5_53cr375}**.
