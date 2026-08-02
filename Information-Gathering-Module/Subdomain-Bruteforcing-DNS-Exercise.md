### Subdomain Brute Forcing DNS Exercise
 
Domain: inlanefreight.com
 
---
 
### Question 1:
Using the known subdomains for inlanefreight.com (www, ns1, ns2, ns3, blog, support, customer), find any missing subdomains by brute-forcing possible domain names. Provide your answer with the complete subdomain, e.g., www.inlanefreight.com.
 
I use the dnsenum command as shown in the section to enumerate any subdomains not found.
 
```diff
+ $ dnsenum --enum inlanefreight.com -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt
```
 
	www.inlanefreight.com.                   300      IN    A        134.209.24.248
	ns1.inlanefreight.com.                   300      IN    A        178.128.39.165
	ns2.inlanefreight.com.                   300      IN    A        206.189.119.186
	blog.inlanefreight.com.                  300      IN    A        134.209.24.248
	ns3.inlanefreight.com.                   300      IN    A        134.209.24.248
	support.inlanefreight.com.               300      IN    A        134.209.24.248
	my.inlanefreight.com.                    300      IN    A        134.209.24.248
	customer.inlanefreight.com.              300      IN    A        134.209.24.248
 
From this output, I can see a non-listed subdomain, my.inlanefreight.com.
 
&#x1F6A9; found **my.inlanefreight.com**.
