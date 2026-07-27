### DNS Exercise
 
IP: 10.129.74.102
 
---
 
### Question 1:
Interact with the target DNS using its IP address and enumerate the FQDN of it for the "inlanefreight.htb" domain.
 
I begin by digging for a name server with the IP and domain that was given.
 
```diff
+ $ dig ns inlanefreight.htb @10.129.74.102
```
 
	;; ANSWER SECTION:
	inlanefreight.htb.	604800	IN	NS	ns.inlanefreight.htb.
 
	;; ADDITIONAL SECTION:
	ns.inlanefreight.htb.	604800	IN	A	127.0.0.1
 
From this, I can see that the FQDN is ns.inlanefreight.htb.
 
&#x1F6A9; found **ns.inlanefreight.htb**.
 
---
 
### Question 2:
Identify if it's possible to perform a zone transfer and submit the TXT record as the answer. (Format: HTB{...})
 
I then attempt a zone transfer on the target.
 
```diff
+ $ dig axfr inlanefreight.htb @10.129.74.102
```
 
	inlanefreight.htb.	604800	IN	SOA	inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
	inlanefreight.htb.	604800	IN	TXT	"MS=ms97310371"
	inlanefreight.htb.	604800	IN	TXT	"atlassian-domain-verification=t1rKCy68JFszSdCKVpw64A1QksWdXuYFUeSXKU"
	inlanefreight.htb.	604800	IN	TXT	"v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.124.8 ip4:10.129.127.2 ip4:10.129.42.106 ~all"
	inlanefreight.htb.	604800	IN	NS	ns.inlanefreight.htb.
	app.inlanefreight.htb.	604800	IN	A	10.129.18.15
	dev.inlanefreight.htb.	604800	IN	A	10.12.0.1
	internal.inlanefreight.htb. 604800 IN	A	10.129.1.6
	mail1.inlanefreight.htb. 604800	IN	A	10.129.18.201
	ns.inlanefreight.htb.	604800	IN	A	127.0.0.1
	inlanefreight.htb.	604800	IN	SOA	inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
 
This gives me a fair amount of available subdomains to keep in mind, but there is no TXT record that sticks out, so I try for an internal zone transfer.
 
```diff
+ $ dig axfr internal.inlanefreight.htb @10.129.74.102
```
 
	internal.inlanefreight.htb. 604800 IN	SOA	inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
	internal.inlanefreight.htb. 604800 IN	TXT	"MS=ms97310371"
	internal.inlanefreight.htb. 604800 IN	TXT	"HTB{DN5_z0N3_7r4N5F3r_iskdufhcnlu34}"
	internal.inlanefreight.htb. 604800 IN	TXT	"atlassian-domain-verification=t1rKCy68JFszSdCKVpw64A1QksWdXuYFUeSXKU"
	internal.inlanefreight.htb. 604800 IN	TXT	"v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.124.8 ip4:10.129.127.2 ip4:10.129.42.106 ~all"
	internal.inlanefreight.htb. 604800 IN	NS	ns.inlanefreight.htb.
	dc1.internal.inlanefreight.htb.	604800 IN A	10.129.34.16
	dc2.internal.inlanefreight.htb.	604800 IN A	10.129.34.11
	mail1.internal.inlanefreight.htb. 604800 IN A	10.129.18.200
	ns.internal.inlanefreight.htb. 604800 IN A	127.0.0.1
	vpn.internal.inlanefreight.htb.	604800 IN A	10.129.1.6
	ws1.internal.inlanefreight.htb.	604800 IN A	10.129.1.34
	ws2.internal.inlanefreight.htb.	604800 IN A	10.129.1.35
	wsus.internal.inlanefreight.htb. 604800	IN A	10.129.18.2
	internal.inlanefreight.htb. 604800 IN	SOA	inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
 
From this output, I can see an HTB flag as a TXT record.
 
&#x1F6A9; found **HTB{DN5_z0N3_7r4N5F3r_iskdufhcnlu34}**.
 
---
 
### Question 3:
What is the IPv4 address of the hostname DC1?
 
I can also see that amongst these A records is one for dc1, which I can find its IPv4 right next to it, in the same internal zone transfer output above.
 
&#x1F6A9; found **10.129.34.16**.
 
---
 
### Question 4:
What is the FQDN of the host where the last octet ends with "x.x.x.203"?
 
Looking at these outputs, no subdomain has an IP with the last octet containing a 203, so I use dnsenum to brute force any further subdomains, starting with the ones I gathered in the external zone transfer with inlanefreight.htb. Attempting app doesn't return anything, but going for dev does.
 
```diff
+ $ dnsenum --dnsserver 10.129.74.102 --enum -p 0 -s 0 -o subdomains.txt -f /opt/useful/seclists/Discovery/DNS/fierce-hostlist.txt dev.inlanefreight.htb
```
 
	dnsenum VERSION:1.3.1
 
	-----   dev.inlanefreight.htb   -----
 
	Host's addresses:
	__________________
 
	Name Servers:
	______________
 
	ns.inlanefreight.htb.                    604800   IN    A         127.0.0.1
 
	Mail (MX) Servers:
	___________________
 
	Trying Zone Transfers and getting Bind Versions:
	_________________________________________________
 
	unresolvable name: ns.inlanefreight.htb at /usr/bin/dnsenum line 892 thread 2.
 
	Trying Zone Transfer for dev.inlanefreight.htb on ns.inlanefreight.htb ...
	AXFR record query failed: no nameservers
 
	Brute forcing with /opt/useful/seclists/Discovery/DNS/fierce-hostlist.txt:
	___________________________________________________________________________
 
	dev1.dev.inlanefreight.htb.              604800   IN    A         10.12.3.6
	ns.dev.inlanefreight.htb.                604800   IN    A         127.0.0.1
	win2k.dev.inlanefreight.htb.             604800   IN    A        10.12.3.203
 
	Launching Whois Queries:
	_________________________
 
	dev.inlanefreight.htb_____________________
 
	Performing reverse lookup on 0 ip addresses:
	_____________________________________________
 
	0 results out of 0 IP addresses.
 
	dev.inlanefreight.htb ip blocks:
	_________________________________
 
	done.
 
Finally, I can see an IP with its last octet being 203, whose FQDN is win2k.dev.inlanefreight.htb.
 
&#x1F6A9; found **win2k.dev.inlanefreight.htb**.

