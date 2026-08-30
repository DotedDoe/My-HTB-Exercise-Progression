### PHP Web Shell Exercise
 
IP: 10.129.201.101
 
---
 
### Question 1:
In the example shown, what must the Content-Type be changed to in order to successfully upload the web shell? (Format: .../... )
 
I start off by running an nmap scan against the target.
 
```diff
+ $ nmap -A -sV -sC 10.129.201.101
```
 
	PORT     STATE SERVICE  VERSION
	21/tcp   open  ftp      vsftpd 2.0.8 or later
	22/tcp   open  ssh      OpenSSH 7.4 (protocol 2.0)
	| ssh-hostkey:
	|   2048 2d:b2:23:75:87:57:b9:d2:dc:88:b9:f4:c1:9e:36:2a (RSA)
	|   256 c4:88:20:b0:22:2b:66:d0:8e:9d:2f:e5:dd:32:71:b1 (ECDSA)
	|_  256 e3:2a:ec:f0:e4:12:fc:da:cf:76:d5:43:17:30:23:27 (ED25519)
	80/tcp   open  http     Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips PHP/7.2.34)
	|_http-title: Did not follow redirect to https://10.129.201.101/
	|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/7.2.34
	111/tcp  open  rpcbind  2-4 (RPC #100000)
	| rpcinfo:
	|   program version    port/proto  service
	|   100000  2,3,4        111/tcp   rpcbind
	|   100000  2,3,4        111/udp   rpcbind
	|   100000  3,4          111/tcp6  rpcbind
	|_  100000  3,4          111/udp6  rpcbind
	443/tcp  open  ssl/http Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips PHP/7.2.34)
	|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/7.2.34
	|_ssl-date: TLS randomness does not represent time
	|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
	| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
	| Not valid before: 2021-09-24T19:29:26
	|_Not valid after:  2022-09-24T19:29:26
	3306/tcp open  mysql    MySQL (unauthorized)
 
Since this section is about abusing rConfig's vendor image upload and circumventing its restricted file type upload, I login with the base credentials admin:admin and go over to Devices > Vendor > Add Vendor.
 
From here I use the web shell mentioned in the section, wwwolf-php-webshell, and upload it, making sure to toggle on Burp Suite to intercept the outgoing POST request. Inside the POST, I edit the Content-Type to read image/gif, instead of application/x-php.
 
&#x1F6A9; found **image/gif**.
 
---
 
### Question 2:
Use what you learned from the module to gain a web shell. What is the file name of the gif in the /images/vendor directory on the target? (Format: xxxx.gif)
 
This works! And the vendor gets made. From here I navigate to images/vendor/filename.php and find my web shell, which, typing in an `ls` command, reveals the name of the gif in the images/vendor directory.
 
```diff
+ $ ls
```
 
	ajax-loader.gif
	cisco.jpg
	juniper.jpg
	webshell.php
 
&#x1F6A9; found **ajax-loader.gif**.
