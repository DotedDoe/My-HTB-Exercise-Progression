### Living of the Land Exercise
 
IP: 10.129.100.223
 
---
 
### Optional Exercise 1:
Connect to the target machine via RDP (Username: htb-student | Password: HTB_@cademy_stdnt!) and use Living Off The Land techniques presented in this section or any other found on the LOLBAS and GTFOBins websites to transfer files between the Pwnbox and the Windows target. Type "DONE" when finished.
 
I start off by logging into the Windows environment using the credentials and xfreerdp.
 
```diff
+ $ xfreerdp /u:htb-student /p:HTB_@cademy_stdnt! /v:10.129.100.223
```
 
I then set up a Python HTTP web server in a temporary folder on my local machine, to download on the remote Windows machine.
 
```diff
+ $ mkdir temp/
+ $ cd temp
+ $ echo "hello" > test.txt
+ $ python3 -m http.server
```
 
Then inside my Windows environment, I run bitsadmin to pull the file down.
 
```diff
+ PS C:\Users\htb-student> bitsadmin /transfer wcb /priority foreground http://10.10.15.66:8000/test.txt C:\Users\htb-student\Desktop\test.txt
+ PS C:\Users\htb-student> ls Desktop/
```
 
	    Directory: C:\Users\htb-student\Desktop
	Mode                 LastWriteTime         Length Name
	----                 -------------         ------ ----
	-a----          8/13/2026   7:03 PM              6 test.txt
	-a----           9/9/2020   1:56 PM              0 upload_win.txt
 
I confirm the transfer worked by reading the file's contents.
 
```diff
+ PS C:\Users\htb-student> Get-Content Desktop/test.txt
```
 
	hello
 
DONE
