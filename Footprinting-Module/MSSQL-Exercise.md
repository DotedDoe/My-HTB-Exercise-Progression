### MSSQL Exercise
 
IP: 10.129.230.249
 
---
 
### Question 1:
Enumerate the target using the concepts taught in this section. List the hostname of MSSQL server.
 
I start off using an nmap scan, equipped with the scripts given by the module as well as the credentials.
 
```diff
+ $ sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=backdoor,mssql.password=Password1,mssql.instance-name=MSSQLSERVER -sV -p 1433 10.129.230.249
```
 
	PORT     STATE SERVICE  VERSION
	1433/tcp open  ms-sql-s Microsoft SQL Server 2019 15.00.2000.00; RTM
	| ms-sql-ntlm-info:
	|   10.129.230.249\MSSQLSERVER:
	|     Target_Name: ILF-SQL-01
	|     NetBIOS_Domain_Name: ILF-SQL-01
	|     NetBIOS_Computer_Name: ILF-SQL-01
	|     DNS_Domain_Name: ILF-SQL-01
	|     DNS_Computer_Name: ILF-SQL-01
	|_    Product_Version: 10.0.17763
	| ms-sql-dump-hashes:
	|_  10.129.230.249\MSSQLSERVER: ERROR: Bad username or password
	| ms-sql-config:
	|   10.129.230.249\MSSQLSERVER:
	|_  ERROR: Bad username or password
	| ms-sql-xp-cmdshell:
	|_  (Use --script-args=ms-sql-xp-cmdshell.cmd='<CMD>' to change command.)
	| ms-sql-dac:
	|   10.129.230.249\MSSQLSERVER:
	|     port: 1434
	|     state: closed
	|_    error: ERROR
	|_ms-sql-tables: ERROR: Script execution failed (use -d to debug)
	| ms-sql-info:
	|   10.129.230.249\MSSQLSERVER:
	|     Instance name: MSSQLSERVER
	|     Version:
	|       name: Microsoft SQL Server 2019 RTM
	|       number: 15.00.2000.00
	|       Product: Microsoft SQL Server 2019
	|       Service pack level: RTM
	|       Post-SP patches applied: false
	|     TCP port: 1433
	|     Named pipe: \\10.129.230.249\pipe\sql\query
	|_    Clustered: false
	| ms-sql-empty-password:
	|_  10.129.230.249\MSSQLSERVER:
 
Some scripts fail, but this does reveal the hostname of the server.
 
&#x1F6A9; found **ILF-SQL-01**.
 
---
 
### Question 2:
Connect to the MSSQL instance running on the target using the account (backdoor:Password1), then list the non-default database present on the server.
 
Then I use mssqlclient.py from Impacket, mentioned in the module and already on the local machine at /usr/share/doc/python3-impacket/examples/mssqlclient.py.
 
```diff
+ $ python3 mssqlclient.py backdoor@10.129.230.249 -windows-auth
```
 
From here, I show names from sys.databases, which shows the following.
 
```diff
+ SQL (ILF-SQL-01\backdoor  dbo@master)> select name from sys.databases;
```
 
	name
	---------
	master
	tempdb
	model
	msdb
	Employees
 
Looking at the output, it's clear that Employees is the non-default database.
 
&#x1F6A9; found **Employees**.
