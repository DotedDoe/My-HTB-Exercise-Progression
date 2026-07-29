### MySQL Exercise
 
IP: 10.129.42.195
 
---
 
### Question 1:
Enumerate the MySQL server and determine the version in use. (Format: MySQL X.X.XX)
 
I start off with an nmap scan against the target, wanting to be aggressive and get a relatively fast response while looking for versions and using basic scripts.
 
```diff
+ $ nmap -A -sV -sC -Pn -n 10.129.42.195
```
 
	3306/tcp open  mysql    MySQL 8.0.27-0ubuntu0.20.04.1
	| mysql-info:
	|   Protocol: 10
	|   Version: 8.0.27-0ubuntu0.20.04.1
	|   Thread ID: 11
	|   Capabilities flags: 65535
	|   Some Capabilities: ConnectWithDatabase, SupportsTransactions, IgnoreSpaceBeforeParenthesis, DontAllowDatabaseTableColumn, SwitchToSSLAfterHandshake, Speaks41ProtocolNew, IgnoreSigpipes, Support41Auth, SupportsCompression, SupportsLoadDataLocal, LongColumnFlag, FoundRows, InteractiveClient, ODBCClient, Speaks41ProtocolOld, LongPassword, SupportsMultipleStatments, SupportsAuthPlugins, SupportsMultipleResults
	|   Status: Autocommit
	|   Salt: \x18Pqu,kV\x12<o\x1Dk
	| X\x0E-YH\x1A\x0E
	|_  Auth Plugin Name: caching_sha2_password
 
We can see from this the version of the MySQL server.
 
&#x1F6A9; found **MySQL 8.0.27**.
 
---
 
### Question 2:
During our penetration test, we found weak credentials "robin:robin". We should try these against the MySQL server. What is the email address of the customer "Otto Lang"?
 
Moving on to connect, the question states the credentials, so I log in with those.
 
```diff
+ $ mysql -u robin -probin -h 10.129.42.195
```
 
Upon login, I can see multiple databases, but what stands out is "customers". I'm supposed to be looking for a user's email, so I go with that one. There's only one table, named myTable.
 
Selecting the table and showing all content in the table, I can see a great many users and their information. I decide to use the command to match a string in a column in a table.
 
```diff
+ MySQL [customers]> select * from myTable where name = "Otto Lang";
```
 
	+----+-----------+---------------------+---------+-----------+---------+-----------------+------------------+------+
	| id | name      | email               | country | postalZip | city    | address         | pan              | cvv  |
	+----+-----------+---------------------+---------+-----------+---------+-----------------+------------------+------+
	| 88 | Otto Lang | ultrices@google.htb | France  | 76733-267 | Belfast | 4708 Auctor Rd. | 5322224628183391 | 595  |
	+----+-----------+---------------------+---------+-----------+---------+-----------------+------------------+------+
	1 row in set (0.007 sec)
 
From this, I can see that Otto Lang's email is ultrices@google.htb.
 
&#x1F6A9; found **ultrices@google.htb**.
 
