### SMB Exercise
 
IP: 10.129.202.5
 
---
 
### Question 1:
What version of the SMB server is running on the target system? Submit the entire banner as the answer.
 
I start off with an nmap scan. I want it to get available versions for services and use default scripts against them. I do it like so.
 
```diff
+ $ nmap -Pn -n -sC -sV -A 10.129.202.5
```
 
	PORT     STATE SERVICE     VERSION
	21/tcp   open  ftp
	22/tcp   open  ssh         OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
	111/tcp  open  rpcbind     2-4 (RPC #100000)
	139/tcp  open  netbios-ssn Samba smbd 4
	445/tcp  open  netbios-ssn Samba smbd 4
	2049/tcp open  nfs_acl     3 (RPC #100227)
 
We don't get much info on the SMB ports outside of their version.
 
&#x1F6A9; found **Samba smbd 4**.
 
---
 
### Question 2:
What is the name of the accessible share on the target?
 
I check to see if I'm able to login by attempting to connect via smbclient. I start off by listing available shares using the -L flag.
 
```diff
+ $ smbclient -L //10.129.202.5/
```
 
	Password for [WORKGROUP\htb-ac-2643052]:
	Sharename       Type      Comment
	---------       ----      -------
	print$          Disk      Printer Drivers
	sambashare      Disk      InFreight SMB v3.1
	IPC$            IPC       IPC Service (InlaneFreight SMB server (Samba, Ubuntu))
	SMB1 disabled -- no workgroup available
 
No password was required, and I can see the name of the accessible share on the target.
 
&#x1F6A9; found **sambashare**.
 
---
 
### Question 3:
Connect to the discovered share and find the flag.txt file. Submit the contents as the answer.
 
I then login regularly with smbclient, now with the knowledge credentials aren't needed. Upon entering and doing an `ls`, there isn't much aside from 3 files and a directory. I think to check out the directory, and inside I find the flag.txt.
 
```diff
+ $ smbclient //10.129.202.5/sambashare
+ smb: \> ls
```
 
	  .                                   D        0  Mon Nov  8 08:43:14 2021
	  ..                                  D        0  Mon Nov  8 10:53:19 2021
	  .profile                            H      807  Tue Feb 25 07:03:22 2020
	  contents                            D        0  Mon Nov  8 08:43:45 2021
	  .bash_logout                        H      220  Tue Feb 25 07:03:22 2020
	  .bashrc                             H     3771  Tue Feb 25 07:03:22 2020
			5090944 blocks of size 1024. 1765968 blocks available
 
```diff
+ smb: \> cd contents
+ smb: \contents\> ls
+ smb: \contents\> get flag.txt
+ smb: \contents\> !cat flag.txt
```
 
	  .                                   D        0  Mon Nov  8 08:43:45 2021
	  ..                                  D        0  Mon Nov  8 08:43:14 2021
	  flag.txt                            N       38  Mon Nov  8 08:43:45 2021
			5090944 blocks of size 1024. 1765968 blocks available
	getting file \contents\flag.txt of size 38 as flag.txt (1.2 KiloBytes/sec) (average 1.2 KiloBytes/sec)
	HTB{o873nz4xdo873n4zo873zn4fksuhldsf}
 
Seeing the flag, I use the `get` command and, utilizing local command execution via `!`, I am able to `cat` the file I retrieved.
 
&#x1F6A9; found **HTB{o873nz4xdo873n4zo873zn4fksuhldsf}**.
 
---
 
### Question 4:
Find out which domain the server belongs to.
 
### Question 5:
Find additional information about the specific share we found previously and submit the customized version of that specific share as the answer.
 
### Question 6:
What is the full system path of that specific share? (format: "/directory/names")
 
I then decide to check out the SMB server via rpcclient, to get more info on the server and domain. Connecting with an empty user via `-U ""` proved useful, and I ran the following.
 
```diff
+ $ rpcclient -U "" 10.129.202.5
+ rpcclient $> enumdomains
+ rpcclient $> querydominfo
+ rpcclient $> netsharegetinfo sambashare
```
 
	name:[DEVSMB] idx:[0x0]
	name:[Builtin] idx:[0x1]
 
	Domain:		DEVOPS
	Server:		DEVSMB
	Comment:	InlaneFreight SMB server (Samba, Ubuntu)
	Total Users:	0
	Total Groups:	0
	Total Aliases:	0
	Sequence No:	1785055683
	Force Logoff:	4294967295
	Domain Server State:	0x1
	Server Role:	ROLE_DOMAIN_PDC
	Unknown 3:	0x1
 
	netname: sambashare
		remark:	InFreight SMB v3.1
		path:	C:\home\sambauser\
		password:
		type:	0x0
		perms:	0
		max_uses:	-1
		num_uses:	1
 
The output from these two commands answers questions 4, 5, and 6.
 
&#x1F6A9; found **DEVOPS** (Question 4).
&#x1F6A9; found **InFreight SMB v3.1** (Question 5).
&#x1F6A9; found **/home/sambauser/** (Question 6).

