### NFS Exercise
 
IP: 10.129.202.5
 
---
 
### Question 1:
Enumerate the NFS service and submit the contents of the flag.txt in the "nfs" share as the answer.
 
### Question 2:
Enumerate the NFS service and submit the contents of the flag.txt in the "nfsshare" share as the answer.
 
I start off with a basic nmap scan, wanting to get thorough info on the target and open ports.
 
```diff
+ $ nmap -Pn -n -sC -sV -A 10.129.202.5
```
 
	21/tcp   open  ftp
	22/tcp   open  ssh         OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
	111/tcp  open  rpcbind     2-4 (RPC #100000)
	| rpcinfo:
	|   program version    port/proto  service
	|   100000  2,3,4        111/tcp   rpcbind
	|   100000  2,3,4        111/udp   rpcbind
	|   100000  3,4          111/tcp6  rpcbind
	|   100000  3,4          111/udp6  rpcbind
	|   100003  3           2049/udp   nfs
	|   100003  3           2049/udp6  nfs
	|   100003  3,4         2049/tcp   nfs
	|   100003  3,4         2049/tcp6  nfs
	|   100005  1,2,3      37634/udp6  mountd
	|   100005  1,2,3      37795/tcp   mountd
	|   100005  1,2,3      46981/tcp6  mountd
	|   100005  1,2,3      52050/udp   mountd
	|   100021  1,3,4      34869/tcp6  nlockmgr
	|   100021  1,3,4      43218/udp6  nlockmgr
	|   100021  1,3,4      46835/tcp   nlockmgr
	|   100021  1,3,4      56625/udp   nlockmgr
	|   100227  3           2049/tcp   nfs_acl
	|   100227  3           2049/tcp6  nfs_acl
	|   100227  3           2049/udp   nfs_acl
	|_  100227  3           2049/udp6  nfs_acl
	139/tcp  open  netbios-ssn Samba smbd 4
	445/tcp  open  netbios-ssn Samba smbd 4
	2049/tcp open  nfs         3-4 (RPC #100003)
 
Seeing these NFS ports, I decide to focus on them, and use NFS scripts focused on it.
 
```diff
+ $ nmap -sV -sC -A -Pn -n 10.129.202.5 -p 111,2049 --script nfs*
```
 
	PORT     STATE SERVICE VERSION
	111/tcp  open  rpcbind 2-4 (RPC #100000)
	| nfs-showmount:
	|   /var/nfs 10.0.0.0/8
	|_  /mnt/nfsshare 10.0.0.0/8
	| rpcinfo:
	|   program version    port/proto  service
	|   100000  2,3,4        111/tcp   rpcbind
	|   100000  2,3,4        111/udp   rpcbind
	|   100000  3,4          111/tcp6  rpcbind
	|   100000  3,4          111/udp6  rpcbind
	|   100003  3           2049/udp   nfs
	|   100003  3           2049/udp6  nfs
	|   100003  3,4         2049/tcp   nfs
	|   100003  3,4         2049/tcp6  nfs
	|   100005  1,2,3      37634/udp6  mountd
	|   100005  1,2,3      37795/tcp   mountd
	|   100005  1,2,3      46981/tcp6  mountd
	|   100005  1,2,3      52050/udp   mountd
	|   100021  1,3,4      34869/tcp6  nlockmgr
	|   100021  1,3,4      43218/udp6  nlockmgr
	|   100021  1,3,4      46835/tcp   nlockmgr
	|   100021  1,3,4      56625/udp   nlockmgr
	|   100227  3           2049/tcp   nfs_acl
	|   100227  3           2049/tcp6  nfs_acl
	|   100227  3           2049/udp   nfs_acl
	|_  100227  3           2049/udp6  nfs_acl
	2049/tcp open  nfs     3-4 (RPC #100003)
 
This additionally shows the available mount points, so I make a directory to mount the share to, and mount.
 
```diff
+ $ mkdir target-NFS
+ $ sudo mount -t nfs 10.129.202.5:/ ./target-NFS/ -o nolock
+ $ cd target-NFS
+ $ tree .
```
 
	.
	├── mnt
	│   └── nfsshare
	│       └── flag.txt
	└── var
	    └── nfs
	        └── flag.txt
 
From this, we can see both available flags, and traversing to their directories and cat'ing the flag gives us the answer to both questions.
 
```diff
+ ~/target-NFS/mnt/nfsshare$ cat flag.txt
```
 
	HTB{8o7435zhtuih7fztdrzuhdhkfjcn7ghi4357ndcthzuc7rtfghu34}
 
```diff
+ ~/target-NFS/var/nfs$ cat flag.txt
```
 
	HTB{hjglmvtkjhlkfuhgi734zthrie7rjmdze}
 
&#x1F6A9; found **HTB{hjglmvtkjhlkfuhgi734zthrie7rjmdze}** (Question 1, "nfs" share).
&#x1F6A9; found **HTB{8o7435zhtuih7fztdrzuhdhkfjcn7ghi4357ndcthzuc7rtfghu34}** (Question 2, "nfsshare" share).
