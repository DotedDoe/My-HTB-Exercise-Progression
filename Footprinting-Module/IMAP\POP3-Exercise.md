### IMAP/POP3 Enumeration Exercise
 
IP: 10.129.42.195
 
---
 
### Question 1:
Figure out the exact organization name from the IMAP/POP3 service and submit it as the answer.
 
### Question 2:
What is the FQDN that the IMAP and POP3 servers are assigned to?
 
I start off with an nmap scan on the target, including version and script scanning, with hopes to get the organization, version, and/or FQDN.
 
```diff
+ $ nmap -sV -sC -A -Pn -n 10.129.42.195
```
 
	110/tcp  open  pop3     Dovecot pop3d
	|_pop3-capabilities: CAPA PIPELINING RESP-CODES UIDL TOP STLS AUTH-RESP-CODE SASL
	|_ssl-date: TLS randomness does not represent time
	| ssl-cert: Subject: commonName=dev.inlanefreight.htb/organizationName=InlaneFreight Ltd/stateOrProvinceName=London/countryName=UK
	| Not valid before: 2021-11-08T23:10:05
	|_Not valid after:  2295-08-23T23:10:05
	143/tcp  open  imap     Dovecot imapd
	| ssl-cert: Subject: commonName=dev.inlanefreight.htb/organizationName=InlaneFreight Ltd/stateOrProvinceName=London/countryName=UK
	| Not valid before: 2021-11-08T23:10:05
	|_Not valid after:  2295-08-23T23:10:05
	|_ssl-date: TLS randomness does not represent time
	|_imap-capabilities: capabilities more listed LOGINDISABLEDA0001 ENABLE have post-login STARTTLS LOGIN-REFERRALS IMAP4rev1 OK SASL-IR LITERAL+ Pre-login IDLE ID
	993/tcp  open  ssl/imap Dovecot imapd
	|_imap-capabilities: capabilities more listed ENABLE have post-login AUTH=PLAINA0001 LOGIN-REFERRALS IMAP4rev1 OK SASL-IR LITERAL+ Pre-login IDLE ID
	| ssl-cert: Subject: commonName=dev.inlanefreight.htb/organizationName=InlaneFreight Ltd/stateOrProvinceName=London/countryName=UK
	| Not valid before: 2021-11-08T23:10:05
	|_Not valid after:  2295-08-23T23:10:05
	|_ssl-date: TLS randomness does not represent time
	995/tcp  open  ssl/pop3 Dovecot pop3d
	| ssl-cert: Subject: commonName=dev.inlanefreight.htb/organizationName=InlaneFreight Ltd/stateOrProvinceName=London/countryName=UK
	| Not valid before: 2021-11-08T23:10:05
	|_Not valid after:  2295-08-23T23:10:05
	|_ssl-date: TLS randomness does not represent time
	|_pop3-capabilities: CAPA PIPELINING RESP-CODES UIDL TOP USER AUTH-RESP-CODE SASL(PLAIN)
 
From this output, I can see the organization name to be InlaneFreight Ltd, and the FQDN to be dev.inlanefreight.htb.
 
&#x1F6A9; found **InlaneFreight Ltd** (Question 1).
&#x1F6A9; found **dev.inlanefreight.htb** (Question 2).
 
---
 
### Question 3:
Enumerate the IMAP service and submit the flag as the answer. (Format: HTB{...})
 
Now I aim to connect to the IMAP server. To do this I use openssl.
 
```diff
+ $ openssl s_client -connect 10.129.42.195:imaps
```
 
	Connecting to 10.129.42.195
	CONNECTED(00000003)
	<snip>
	---
	read R BLOCK
	* OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE LITERAL+ AUTH=PLAIN] HTB{roncfbw7iszerd7shni7jr2343zhrj}
 
Upon connecting, one of the flags is revealed to me just from enumerating the service.
 
&#x1F6A9; found **HTB{roncfbw7iszerd7shni7jr2343zhrj}**.
 
---
 
### Question 4:
What is the customized version of the POP3 server?
 
### Question 5:
What is the admin email address?
 
I login with the credentials given and look around. There are two folders, a DEV.DEPARTMENT.INT and an INBOX. I select the DEV.DEPARTMENT.INT and it states that there's an email available. I then fetch the information for that message, specifying I want the header information, and afterwards, the text.
 
```diff
+ 1 FETCH 1 BODY[HEADER]
```
 
	* 1 FETCH (BODY[HEADER] {133}
	Subject: Flag
	To: Robin <robin@inlanefreight.htb>
	From: CTO <devadmin@inlanefreight.htb>
	Date: Wed, 03 Nov 2021 16:13:27 +0200
	)
	1 OK Fetch completed (0.001 + 0.000 secs).
 
From this, I can see the admin's email.
 
&#x1F6A9; found **devadmin@inlanefreight.htb** (Question 5).
 
---
 
### Question 6:
Try to access the emails on the IMAP server and submit the flag as the answer. (Format: HTB{...})
 
```diff
+ 1 FETCH 1 BODY[TEXT]
```
 
	* 1 FETCH (BODY[TEXT] {34}
	HTB{983uzn8jmfgpd8jmof8c34n7zio}
	)
	1 OK Fetch completed (0.001 + 0.000 secs).
 
&#x1F6A9; found **HTB{983uzn8jmfgpd8jmof8c34n7zio}**.
