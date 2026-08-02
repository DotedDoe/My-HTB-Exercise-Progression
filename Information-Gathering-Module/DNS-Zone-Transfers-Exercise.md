### DNS Zone Transfer Exercise
 
Domain: inlanefreight.htb
 
IP: 10.129.81.22
 
---
 
### Question 1:
After performing a zone transfer for the domain inlanefreight.htb on the target system, how many DNS records are retrieved from the target system's name server? Provide your answer as an integer, e.g., 123.
 
I start off with adding the domain to the IP address in the /etc/hosts file, so that I can refer to it in dig commands.
 
```diff
+ $ sudo nano /etc/hosts
```
 
	127.0.0.1	localhost
	127.0.1.1	pwnbox7.1
	10.129.81.22  inlanefreight.htb
 
I then do a DNS zone transfer against inlanefreight.htb, which gives me this output.
 
```diff
+ $ dig axfr inlanefreight.htb @inlanefreight.htb
```
 
	inlanefreight.htb.	604800	IN	SOA	inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
	inlanefreight.htb.	604800	IN	NS	ns.inlanefreight.htb.
	admin.inlanefreight.htb. 604800	IN	A	10.10.34.2
	ftp.admin.inlanefreight.htb. 604800 IN	A	10.10.34.2
	careers.inlanefreight.htb. 604800 IN	A	10.10.34.50
	dc1.inlanefreight.htb.	604800	IN	A	10.10.34.16
	dc2.inlanefreight.htb.	604800	IN	A	10.10.34.11
	internal.inlanefreight.htb. 604800 IN	A	127.0.0.1
	admin.internal.inlanefreight.htb. 604800 IN A	10.10.1.11
	wsus.internal.inlanefreight.htb. 604800	IN A	10.10.1.240
	ir.inlanefreight.htb.	604800	IN	A	10.10.45.5
	dev.ir.inlanefreight.htb. 604800 IN	A	10.10.45.6
	ns.inlanefreight.htb.	604800	IN	A	127.0.0.1
	resources.inlanefreight.htb. 604800 IN	A	10.10.34.100
	securemessaging.inlanefreight.htb. 604800 IN A	10.10.34.52
	test1.inlanefreight.htb. 604800	IN	A	10.10.34.101
	us.inlanefreight.htb.	604800	IN	A	10.10.200.5
	cluster14.us.inlanefreight.htb.	604800 IN A	10.10.200.14
	messagecenter.us.inlanefreight.htb. 604800 IN A	10.10.200.10
	ww02.inlanefreight.htb.	604800	IN	A	10.10.34.112
	www1.inlanefreight.htb.	604800	IN	A	10.10.34.111
	inlanefreight.htb.	604800	IN	SOA	inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
 
From this I can see that there are 22 records given.
 
&#x1F6A9; found **22**.
 
---
 
### Question 2:
Within the zone record transferred above, find the IP address for ftp.admin.inlanefreight.htb. Respond only with the IP address, e.g. 127.0.0.1.
 
Looking through the same zone transfer output, the IP for ftp.admin.inlanefreight.htb is listed directly.
 
&#x1F6A9; found **10.10.34.2**.
 
---
 
### Question 3:
Within the same zone record, identify the largest IP address allocated within the 10.10.200 IP range. Respond with the full IP address, e.g. 10.10.200.1.
 
Scanning the same output for the 10.10.200 range, there are three entries: us.inlanefreight.htb (10.10.200.5), messagecenter.us.inlanefreight.htb (10.10.200.10), and cluster14.us.inlanefreight.htb (10.10.200.14). The largest is cluster14.us.inlanefreight.htb.
 
&#x1F6A9; found **10.10.200.14**.
