### Spraying, Stuffing, and Defaults Exercise

---
 
### Question 1:
Use the credentials provided to log into the target machine and retrieve the MySQL credentials. Submit them as the answer. (Format: <username>:<password>)
 
I start by installing the default creds tool mentioned in the section and run it for the vendor mysql.
 
```diff
+ $ creds search mysql
```
 
	+---------------------+-------------------+----------+
	| Product             |      username     | password |
	+---------------------+-------------------+----------+
	| mysql (ssh)         |        root       |   root   |
	| mysql               | admin@example.com |  admin   |
	| mysql               |        root       | <blank>  |
	| mysql               |      superdba     |  admin   |
	| scrutinizer (mysql) |    scrutremote    |  admin   |
	+---------------------+-------------------+----------+
 
I then ssh into the user given and test the credentials against the MySQL service, eventually allowing a login with superdba:admin.
 
```diff
+ sam@nix01:~$ mysql -u superdba -padmin
```
 
	mysql>
 
&#x1F6A9; found **superdba:admin**.
 
