### Skills Assessment Exercise
 
Domain: inlanefreight.com / inlanefreight.htb
 
IP: 154.57.164.82
 
---
 
### Question 1:
What is the IANA ID of the registrar of the inlanefreight.com domain?
 
I begin by doing a whois lookup against inlanefreight.com and grepping for IANA.
 
```diff
+ $ whois inlanefreight.com | grep IANA
```
 
	Registrar IANA ID: 468
	Registrar IANA ID: 468
 
This shows an IANA ID of 468.
 
&#x1F6A9; found **468**.
 
---
 
### Question 2:
What http server software is powering the inlanefreight.htb site on the target system? Respond with the name of the software, not the version, e.g., Apache.
 
I then start off the enumeration process by adding the domain to the given IP in my /etc/hosts.
 
```diff
+ $ sudo nano /etc/hosts
```
 
	127.0.0.1	localhost
	127.0.1.1	pwnbox7.1
	154.57.164.82   inlanefreight.htb
 
I navigate over to the website, checking out the robots.txt and inspecting the page. There seems to be nothing of note, so I do a vhost brute force with gobuster. While that runs, I do a whatweb lookup against inlanefreight.htb to see the server software.
 
```diff
+ $ whatweb http://inlanefreight.htb:30093
```
 
	http://inlanefreight.htb:30093 [200 OK] Country[UNITED STATES][US], HTML5, HTTPServer[nginx/1.26.1], IP[154.57.164.82], Title[inlanefreight], nginx[1.26.1]
 
&#x1F6A9; found **nginx**.
 
---
 
### Question 3:
What is the API key in the hidden admin directory that you have discovered on the target system?
 
```diff
+ $ gobuster vhost -u http://inlanefreight.htb:30093/ -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain
```
 
	===============================================================
	Gobuster v3.6
	by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
	===============================================================
	[+] Url:             http://inlanefreight.htb:30093/
	[+] Method:          GET
	[+] Threads:         10
	[+] Wordlist:        /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt
	[+] User Agent:      gobuster/3.6
	[+] Timeout:         10s
	[+] Append Domain:   true
	===============================================================
	Starting gobuster in VHOST enumeration mode
	===============================================================
	Found: web1337.inlanefreight.htb:30093 Status: 200 [Size: 104]
	Progress: 114442 / 114443 (100.00%)
 
From this I've found the virtual host web1337, so I add it to my /etc/hosts and check it out. Looking at the website, there isn't anything much different visually, and inspecting the HTML reveals nothing useful, but looking at the robots.txt reveals indexed directories and an admin_h1dd3n/ directory. Navigating over to it, I find the API key.
 
	Welcome to web1337 admin site
	The admin panel is currently under maintenance, but the API is still accessible with the key e963d863ee0e82ba7080fbf558ca0d3f
 
&#x1F6A9; found **e963d863ee0e82ba7080fbf558ca0d3f**.
 
---
 
### Question 4:
After crawling the inlanefreight.htb domain on the target system, what is the email address you have found? Respond with the full email, e.g., mail@inlanefreight.htb.
 
### Question 5:
What is the API key the inlanefreight.htb developers will be changing too?
 
Now I do a virtual host brute force with gobuster against web1337.
 
```diff
+ $ gobuster vhost -u http://web1337.inlanefreight.htb:30093/ -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain
```
 
	===============================================================
	Gobuster v3.6
	by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
	===============================================================
	[+] Url:             http://web1337.inlanefreight.htb:30093/
	[+] Method:          GET
	[+] Threads:         10
	[+] Wordlist:        /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt
	[+] User Agent:      gobuster/3.6
	[+] Timeout:         10s
	[+] Append Domain:   true
	===============================================================
	Starting gobuster in VHOST enumeration mode
	===============================================================
	Found: dev.web1337.inlanefreight.htb:30093 Status: 200 [Size: 123]
	Progress: 114442 / 114443 (100.00%)
 
Adding this to my /etc/hosts and checking out the new virtual host, I can see that there are links that go on and on, so I decide to use the ReconSpider web crawler.
 
```diff
+ $ python3 ReconSpider.py http://dev.web1337.inlanefreight.htb:30093/
+ $ cat results.json
```
 
Reading the results.json, I can see that it found the email address and the API key.
 
	"emails": [
	        "1337testing@inlanefreight.htb"
	"comments": [
	        "<!-- Remember to change the API key to ba988b835be4aa97d068941dc852ff33 -->"
 
&#x1F6A9; found **1337testing@inlanefreight.htb** (Question 4).
&#x1F6A9; found **ba988b835be4aa97d068941dc852ff33** (Question 5).
