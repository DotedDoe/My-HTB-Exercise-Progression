Question 1 optional: Use xfreerdp or rdesktop to connect to the target machine via RDP (Username: htb-student | Password:HTB_@cademy_stdnt!) and mount a Linux directory to practice file transfer operations (upload and download) with your attack host. Type "DONE" when finished.

I use xfreerdp, passing the credentials to the command, what I want to name the shared folder as, and where on my local machine itll be.
$ xfreerdp /u:htb-student /p:HTB_@cademy_stdnt! /v:10.129.100.223 /drive:linux,/home/htb-ac-2643052/temp/
