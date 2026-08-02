### WHOIS Exercise
 
Domains: paypal.com, tesla.com
 
---
 
### Question 1:
Perform a WHOIS lookup against the paypal.com domain. What is the registrar Internet Assigned Numbers Authority (IANA) ID number?
 
I start off with a simple whois command against PayPal's domain and pipe it into a grep searching for the IANA ID.
 
```diff
+ $ whois paypal.com | grep IANA
```
 
	Registrar IANA ID: 292
	Registrar IANA ID: 292
 
I can see that the IANA ID is 292.
 
&#x1F6A9; found **292**.
 
---
 
### Question 2:
What is the admin email contact for the tesla.com domain (also in-scope for the Tesla bug bounty program)?
 
I then do a whois lookup for Tesla's domain and grep for an @ symbol.
 
```diff
+ $ whois tesla.com | grep @
```
 
	Registrar Abuse Contact Email: abusecomplaints@markmonitor.com
	Registrant Email: admin@dnstinations.com
	Tech Email: admin@dnstinations.com
 
From this I can see that the admin's email is admin@dnstinations.com.
 
&#x1F6A9; found **admin@dnstinations.com**.
 
