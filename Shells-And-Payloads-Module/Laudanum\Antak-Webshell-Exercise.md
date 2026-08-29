### Laudanum/Antak Webshell Exercise.md

IP: 10.129.42.197
 
---
 
### Question 1:
Establish a web shell session with the target using the concepts covered in this section. Submit the full path of the directory you land in. (Format: c:\path\you\land\in)
 
I start off by running nmap against the target.
 
```diff
+ $ nmap -A -sV 10.129.42.197
```
 
	PORT     STATE SERVICE       VERSION
	80/tcp   open  http          Microsoft IIS httpd 10.0
	135/tcp  open  msrpc         Microsoft Windows RPC
	139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
	445/tcp  open  microsoft-ds  Windows Server 2019 Standard 17763 microsoft-ds
	515/tcp  open  printer
	1801/tcp open  msmq?
	2103/tcp open  msrpc         Microsoft Windows RPC
	2105/tcp open  msrpc         Microsoft Windows RPC
	2107/tcp open  msrpc         Microsoft Windows RPC
	3389/tcp open  ms-wbt-server Microsoft Terminal Services
	5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
	8080/tcp open  http          Apache Tomcat (language: en)
 
Many services open, and considering the section we're on, I inspect the web server, making sure to add the suggested domain to my /etc/hosts, status.inlanefreight.local.
 
```diff
+ $ sudo nano /etc/hosts
```
 
Navigating to the web server, I can see that we have file upload availability, so I try to upload the suggested aspx web shell from Laudanum after editing it and adding my IP to the list of allowed ones.
 
```diff
+ Uploaded Configuration File Name: C:\inetpub\wwwroot\status.inlanefreight.local\files\demo.aspx
```
 
Visiting here, I can see that it works and gives me limited command execution with stdout and stderr. Using the command DIR, I can see the directory I'm in.
 
```diff
+ C:\...> DIR
```
 
	c:\windows\system32\inetsrv
 
&#x1F6A9; found **c:\windows\system32\inetsrv**.
 
---
 
### Question 2:
Where is the Laudanum aspx web shell located on Pwnbox? Submit the full path. (Format: /path/to/laudanum/aspx)
 
The file itself is located in /usr/share/laudanum/aspx/shell.aspx.
 
&#x1F6A9; found **/usr/share/laudanum/aspx/shell.aspx**.
 
---
 
### Antak
 
### Question 1:
Where is the Antak webshell located on Pwnbox? Submit the full path. (Format: /path/to/antakwebshell)
 
Alternatively, you could use the Antak webshell, located in /usr/share/nishang/Antak-WebShell/antak.aspx, which can be edited for specific credentials which are required to access the webshell.
 
&#x1F6A9; found **/usr/share/nishang/Antak-WebShell/antak.aspx**.
 
---
 
### Question 2:
Establish a web shell with the target using the concepts covered in this section. Submit the name of the user on the target that the commands are being issued as. In order to get the correct answer you must navigate to the web shell you upload using the vHost name. (Format: ****\****, 1 space)
 
Accessing it and running whoami reveals the user.
 
```diff
+ PS> whoami
```
 
	iis apppool\status
 
&#x1F6A9; found **iis apppool\status**.
ebshell.
Accessing it and running whoami reveals 
PS> whoami
iis apppool\status
