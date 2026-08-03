### VHost Exercise
 
Domain: inlanefreight.htb (port 31627)
 
---
 
I do a vhost enumeration with gobuster, as gone over in the module, which will give all the answers in its output.
 
```diff
+ $ gobuster vhost -u http://inlanefreight.htb:31627 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt --append-domain
```
 
	Found: blog.inlanefreight.htb:31627 Status: 200 [Size: 98]
	Found: forum.inlanefreight.htb:31627 Status: 200 [Size: 100]
	Found: admin.inlanefreight.htb:31627 Status: 200 [Size: 100]
	Found: support.inlanefreight.htb:31627 Status: 200 [Size: 104]
	Found: vm5.inlanefreight.htb:31627 Status: 200 [Size: 96]
	Found: browse.inlanefreight.htb:31627 Status: 200 [Size: 102]
	Found: web17611.inlanefreight.htb:31627 Status: 200 [Size: 106]
 
I can see from the output that there exists web17611.inlanefreight.htb, vm5.inlanefreight.htb, browse.inlanefreight.htb, admin.inlanefreight.htb, and support.inlanefreight.htb, answering questions 1 through 5 respectively.
 
---
 
### Question 1:
Brute-force vhosts on the target system. What is the full subdomain that is prefixed with "web"? Answer using the full domain, e.g. "x.inlanefreight.htb"
 
&#x1F6A9; found **web17611.inlanefreight.htb**.
 
---
 
### Question 2:
Brute-force vhosts on the target system. What is the full subdomain that is prefixed with "vm"? Answer using the full domain, e.g. "x.inlanefreight.htb"
 
&#x1F6A9; found **vm5.inlanefreight.htb**.
 
---
 
### Question 3:
Brute-force vhosts on the target system. What is the full subdomain that is prefixed with "br"? Answer using the full domain, e.g. "x.inlanefreight.htb"
 
&#x1F6A9; found **browse.inlanefreight.htb**.
 
---
 
### Question 4:
Brute-force vhosts on the target system. What is the full subdomain that is prefixed with "a"? Answer using the full domain, e.g. "x.inlanefreight.htb"
 
&#x1F6A9; found **admin.inlanefreight.htb**.
 
---
 
### Question 5:
Brute-force vhosts on the target system. What is the full subdomain that is prefixed with "su"? Answer using the full domain, e.g. "x.inlanefreight.htb"
 
&#x1F6A9; found **support.inlanefreight.htb**.
