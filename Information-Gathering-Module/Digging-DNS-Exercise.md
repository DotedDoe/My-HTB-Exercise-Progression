### Digging DNS Exercise
 
Domains: inlanefreight.com, facebook.com
 
---
 
### Question 1:
Which IP address maps to inlanefreight.com?
 
I start off doing a dig against inlanefreight.com.
 
```diff
+ $ dig inlanefreight.com
```
 
	;; ANSWER SECTION:
	inlanefreight.com.	300	IN	A	134.209.24.248
 
This gives the IP to inlanefreight.com.
 
&#x1F6A9; found **134.209.24.248**.
 
---
 
### Question 2:
Which domain is returned when querying the PTR record for 134.209.24.248?
 
I then query the PTR record for the IP given.
 
```diff
+ $ dig -x 134.209.24.248
```
 
	;; ANSWER SECTION:
	248.24.209.134.in-addr.arpa. 1800 IN	PTR	inlanefreight.com.
 
This points back to inlanefreight.com.
 
&#x1F6A9; found **inlanefreight.com**.
 
---
 
### Question 3:
What is the full domain returned when you query the mail records for facebook.com?
 
Finally, I query the mail records for facebook.com.
 
```diff
+ $ dig facebook.com MX
```
 
	;; ANSWER SECTION:
	facebook.com.		3600	IN	MX	10 smtpin.vvv.facebook.com.
 
From this I can see that the mail server is smtpin.vvv.facebook.com.
 
&#x1F6A9; found **smtpin.vvv.facebook.com**.
