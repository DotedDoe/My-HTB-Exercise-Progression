### NSE Enumeration Exercise
 
IP: 10.129.2.49
 
---
 
### Question 1:
Use NSE and its scripts to find the flag that one of the services contains and submit it as the answer.
 
I start off by running an Nmap scan, and since it mentions using NSE scripts, I think first to try the banner NSE.
 
```diff
+ $ sudo nmap -Pn -sV -sC --script banner.nse --source-port 53 10.129.2.49
```
 
This returned nothing too useful outside of giving the previous exercise's flag.
 
I then think of other NSE scripts and notice in my notes I have mentioned http-enum, and there's an http service on port 80, so I give it a try.
 
```diff
+ $ sudo nmap -Pn --script http-enum -p 80 --source-port 53 10.129.2.49
```
 
	PORT    STATE  SERVICE
	80/tcp  open   http
	|  http-enum:
	|_   /robots.txt: Robots file
 
Seeing this, I navigate to my browser and check out the robots.txt at their IP, which reveals the flag.
 
	User-agent: *
	Allow: /
	HTB{873nniuc71bu6usbsli96as6dsv26}
 
&#x1F6A9; found **HTB{873nniuc71bu6usbsli96as6dsv26}**.
