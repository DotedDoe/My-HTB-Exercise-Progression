### Creepy Crawlies Exercise
 
Domain: inlanefreight.com
 
---
 
### Question 1:
After spidering inlanefreight.com, identify the location where future reports will be stored. Respond with the full domain, e.g., files.inlanefreight.com.
 
I start off by downloading ReconSpider and run it against https://inlanefreight.com.
 
```diff
+ $ python3 ReconSpider.py https://inlanefreight.com
```
 
Afterwards I `cat` the results.json and look at what it found.
 
```diff
+ $ cat results.json
```
 
In the comments section, I see that the crawler found a comment mentioning the location of where the future reports will be stored.
 
	"comments": [
	        "<!--==================== TOP BAR ====================-->",
	        "<!-- Right nav -->",
	        "<!-- Navigation -->",
	        "<!-- change Jeremy's email to jeremy-ceo@inlanefreight.com -->",
	        "<!-- TO-DO: change the location of future reports to inlanefreight-comp133.s3.amazonaws.htb -->",
	        "<!-- navbar-toggle -->",
	        "<!--\nSkip to content<div class=\"wrapper\">\n<header class=\"transportex-trhead\">\n\t<!--==================== Header ====================-->",
	        "<!-- Logo -->",
	        "<!--/overlay-->",
	        "<!--Sidebar Area-->",
	        "<!--==================== feature-product ====================-->",
	        "<!-- #masthead -->",
	        "<!-- #secondary -->",
	        "<!-- /Right nav -->",
	        "<!--==================== transportex-FOOTER AREA ====================-->",
	        "<!-- Blog Area -->",
	        "<!-- /navbar-toggle -->",
	        "<!-- /Navigation -->"
	    ]
 
This shows that the location is inlanefreight-comp133.s3.amazonaws.htb.
 
&#x1F6A9; found **inlanefreight-comp133.s3.amazonaws.htb**.
