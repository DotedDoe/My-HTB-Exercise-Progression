### Linux File Transfer Exercise
 
IP: 10.129.234.168
 
---
 
### Question 1:
Download the file flag.txt from the web root using Python from the Pwnbox. Submit the contents of the file as your answer.
 
I start off by doing as the question asked and download the content via Python, using a command mentioned in the module, then `cat` it.
 
```diff
+ $ python3 -c 'import urllib.request;urllib.request.urlretrieve("http://10.129.234.168/flag.txt", "flag.txt")'
+ $ cat flag.txt
```
 
	5d21cf3da9c0ccb94f709e2559f3ea50
 
&#x1F6A9; found **5d21cf3da9c0ccb94f709e2559f3ea50**.
 
---
 
### Question 2:
Upload the attached file named upload_nix.zip to the target using the method of your choice. Once uploaded, SSH to the box, extract the file, and run "hasher <extracted file>" from the command line. Submit the generated hash as your answer.
 
Next I download the file meant to be uploaded by using wget and then unzip it.
 
```diff
+ $ wget https://cdn.services-k8s.prod.aws.htb.systems/content/questions/file/82b4928b-6bfa-4da5-9377-5263107d6866.zip
+ $ unzip 82b4928b-6bfa-4da5-9377-5263107d6866.zip
```
 
Once that's done, I host a Python web server inside of a temporary directory, which I move the file into.
 
```diff
+ $ mkdir tempdir
+ $ mv upload_nix.txt tempdir/
+ $ cd tempdir/
+ $ python3 -m http.server 8000
```
 
After that, I SSH into the user with the given credentials.
 
```diff
+ $ ssh -v htb-student@10.129.234.168
```
 
Once on the machine, I `curl` my Python web server and run hasher against the downloaded file.
 
```diff
+ htb-student@nix04:~$ curl http://10.10.14.216:8000/upload_nix.txt > upload_nix.txt
+ htb-student@nix04:~$ hasher upload_nix.txt
```
 
	159cfe5c65054bbadb2761cfa359c8b0
 
&#x1F6A9; found **159cfe5c65054bbadb2761cfa359c8b0**.
