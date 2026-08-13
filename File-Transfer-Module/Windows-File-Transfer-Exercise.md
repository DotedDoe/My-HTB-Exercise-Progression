### Windows File Transfer Exercise
 
IP: 10.129.99.36
 
---
 
### Question 1:
Download the file flag.txt from the web root using wget from the Pwnbox. Submit the contents of the file as your answer.
 
I start off by doing a wget against the given IP, targeting its web root and the flag.txt as mentioned in the question, then `cat`'ing it.
 
```diff
+ $ wget http://10.129.99.36/flag.txt
+ $ cat flag.txt
```
 
	b1a4ca918282fcd96004565521944a3b
 
&#x1F6A9; found **b1a4ca918282fcd96004565521944a3b**.
 
---
 
### Question 2:
Upload the attached file named upload_win.zip to the target using the method of your choice. Once uploaded, unzip the archive, and run "hasher upload_win.txt" from the command line. Submit the generated hash as your answer.
 
I then grab the file from the website using wget.
 
```diff
+ $ wget https://cdn.services-k8s.prod.aws.htb.systems/content/questions/file/52d91df5-24dd-4aa3-b156-8d777b89481e.zip
+ $ unzip 52d91df5-24dd-4aa3-b156-8d777b89481e.zip
```
 
Afterwards I log into the Windows environment using xfreerdp and passing the given credentials.
 
```diff
+ $ xfreerdp /u:htb-student /p:HTB_@cademy_stdnt! /v:10.129.99.36
```
 
Once it's loaded, I go into PowerShell, and on my local machine, turn the downloaded file into a base64 string.
 
```diff
+ $ cat id_rsa | base64 -w 0; echo
```
 
Then on the Windows machine, I run the following, passing the string into the string section.
 
```diff
+ PS C:\Users\Public> [IO.File]::WriteAllBytes("C:\Users\Public\upload_win.txt", [Convert]::FromBase64String("<string>"))
```
 
Once that's complete, I run hasher on the now-created file and submit the result as the answer.
 
```diff
+ PS C:\Users\Public> hasher ./upload_win.txt
```
 
	f458303ea783c224c6b4e7ef7f17eb9d
 
&#x1F6A9; found **f458303ea783c224c6b4e7ef7f17eb9d**.
