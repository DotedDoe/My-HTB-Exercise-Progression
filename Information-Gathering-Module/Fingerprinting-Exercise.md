### Fingerprinting Exercise
 
Domains: app.inlanefreight.local, dev.inlanefreight.local
 
---
 
### Question 1:
Determine the Apache version running on app.inlanefreight.local on the target system. (Format: 0.0.0)
 
I start off using a curl header command against app.inlanefreight.local to find what Apache server it's running.
 
```diff
+ $ curl -I http://app.inlanefreight.local
```
 
	HTTP/1.1 200 OK
	Date: Tue, 04 Aug 2026 19:25:52 GMT
	Server: Apache/2.4.41 (Ubuntu)
	Set-Cookie: 72af8f2b24261272e581a49f5c56de40=h894579r00p30vq2hv3ct4982k; path=/; HttpOnly
	Permissions-Policy: interest-cohort=()
	Expires: Wed, 17 Aug 2005 00:00:00 GMT
	Last-Modified: Tue, 04 Aug 2026 19:25:52 GMT
	Cache-Control: no-store, no-cache, must-revalidate, post-check=0, pre-check=0
	Pragma: no-cache
	Content-Type: text/html; charset=utf-8
 
I can see that the version is 2.4.41.
 
&#x1F6A9; found **2.4.41**.
 
---
 
### Question 2:
Which CMS is used on app.inlanefreight.local on the target system? Respond with the name only, e.g., WordPress.
 
I then use whatweb against app.inlanefreight.local, which reveals the CMS being used.
 
```diff
+ $ whatweb http://app.inlanefreight.local
```
 
	http://app.inlanefreight.local [200 OK] Apache[2.4.41], Bootstrap, Cookies[72af8f2b24261272e581a49f5c56de40], Country[RESERVED][ZZ], HTML5, HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], HttpOnly[72af8f2b24261272e581a49f5c56de40], IP[10.129.85.48], JQuery, MetaGenerator[Joomla! - Open Source Content Management], OpenSearch[http://app.inlanefreight.local/index.php/component/search/?layout=blog&id=9&Itemid=101&format=opensearch], Script, Title[Home], UncommonHeaders[permissions-policy]
 
From this I can see it's using Joomla.
 
&#x1F6A9; found **Joomla**.
 
---
 
### Question 3:
On which operating system is the dev.inlanefreight.local webserver running in the target system? Respond with the name only, e.g., Debian.
 
Finally, I do a curl header against dev.inlanefreight.local and can see that its Apache server is Ubuntu based, signatured by its package manager.
 
```diff
+ $ curl -I http://dev.inlanefreight.local
```
 
	HTTP/1.1 200 OK
	Date: Tue, 04 Aug 2026 19:26:32 GMT
	Server: Apache/2.4.41 (Ubuntu)
	Set-Cookie: 02a93f6429c54209e06c64b77be2180d=qjuun0lrh28ihh6m7crdso6okp; path=/; HttpOnly
	Expires: Wed, 17 Aug 2005 00:00:00 GMT
	Last-Modified: Tue, 04 Aug 2026 19:26:38 GMT
	Cache-Control: no-store, no-cache, must-revalidate, post-check=0, pre-check=0
	Pragma: no-cache
	Content-Type: text/html; charset=utf-8
 
&#x1F6A9; found **Ubuntu**.
 


