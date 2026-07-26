### FTP Exercise
 
IP: 10.129.73.113
 
---
 
### Question 1:
Which version of the FTP server is running on the target system? Submit the entire banner as the answer.
 
I begin with an Nmap scan to see if I can find the FTP server version that way, I structure it like so.
 
```diff
+ $ nmap -Pn -n -A -sV -sC 10.129.73.113
```
 
	21/tcp   open  ftp
	| ftp-anon: Anonymous FTP login allowed (FTP code 230)
	|_-rw-r--r--   1 ftpuser  ftpuser        39 Nov  8  2021 flag.txt
	| fingerprint-strings:
	|   GenericLines:
	|     220 InFreight FTP v1.1
	|     Invalid command: try being more creative
	|     Invalid command: try being more creative
	|   NULL:
	|_    220 InFreight FTP v1.1
 
From this output, I can see that the server version is InFreight FTP v1.1, and one of the common scripts run with -sC points out a flag.txt in the directory you land in.
 
&#x1F6A9; found **220 InFreight FTP v1.1**.
 
---
 
### Question 2:
Enumerate the FTP server and find the flag.txt file. Submit the contents of it as the answer.
 
The scripts also show that anonymous login to the FTP server is allowed. Seeing this, I try a passive connect like so.
 
```diff
+ $ ftp -p 10.129.73.113
+ Name (10.129.73.113:root): anonymous
+ Password:
+ ftp> ls
```
 
	Connected to 10.129.73.113.
	220 InFreight FTP v1.1
	331 Anonymous login ok, send your complete email address as your password
	230 Anonymous access granted, restrictions apply
	Remote system type is UNIX.
	Using binary mode to transfer files.
	229 Entering Extended Passive Mode (|||10522|)
	150 Opening ASCII mode data connection for file list
	-rw-r--r--   1 ftpuser  ftpuser        39 Nov  8  2021 flag.txt
	226 Transfer complete
 
I grab the file and disconnect.
 
```diff
+ ftp> get flag.txt
+ ftp> exit
```
 
	221 Goodbye.
 
And a simple `cat` of the flag answers question 2.
 
```diff
+ $ cat flag.txt
```
 
	HTB{b7skjr4c76zhsds7fzhd4k3ujg7nhdjre}
 
&#x1F6A9; found **HTB{b7skjr4c76zhsds7fzhd4k3ujg7nhdjre}**.
