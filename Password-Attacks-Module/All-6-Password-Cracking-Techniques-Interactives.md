### Password Cracking Exercises
 
---
 
### Introduction to Password Cracking
 
### Question 1:
What is the SHA1 hash for `Academy#2025`?
 
Very simple one-liner.
 
```diff
+ $ echo -n 'Academy#2025' | sha1sum
```
 
	750fe4b402dc9f91cedf09b652543cd85406be8c
 
&#x1F6A9; found **750fe4b402dc9f91cedf09b652543cd85406be8c**.
 
---
 
### Introduction to John the Ripper
 
### Question 1:
Use single-crack mode to crack r0lf's password.
 
Typical john usage: I just copied the r0lf file in the module to a file and then passed it to john using the single crack mode.
 
```diff
+ $ john --single hash.txt
```
 
	NAITSABES        (r0lf)
 
&#x1F6A9; found **NAITSABES**.
 
---
 
### Question 2:
Use wordlist-mode with rockyou.txt to crack the RIPEMD-128 password.
 
Then I use the wordlist mode, specifying the hash format and having the hash from the section in a file named hash.file.
 
```diff
+ $ john --wordlist=/usr/share/wordlists/rockyou.txt --format=ripemd-128 hash.file
```
 
	50cent           (?)
 
&#x1F6A9; found **50cent**.
 
---
 
### Introduction to Hashcat
 
### Question 1:
Use a dictionary attack to crack the first password hash. (Hash: e3e3ec5831ad5e7288241960e5d4fdb8)
 
Pretty basic use of hashcat, getting accustomed to it, using the rockyou.txt wordlist.
 
```diff
+ $ hashcat -a 0 -m 0 e3e3ec5831ad5e7288241960e5d4fdb8 /usr/share/wordlists/rockyou.txt
```
 
	e3e3ec5831ad5e7288241960e5d4fdb8:crazy!
 
&#x1F6A9; found **crazy!**.
 
---
 
### Question 2:
Use a dictionary attack with rules to crack the second password hash. (Hash: 1b0556a75770563578569ae21392630c)
 
```diff
+ $ hashcat -a 0 -m 0 1b0556a75770563578569ae21392630c /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule
```
 
	1b0556a75770563578569ae21392630c:c0wb0ys1
 
&#x1F6A9; found **c0wb0ys1**.
 
---
 
### Question 3:
Use a mask attack to crack the third password hash. (Hash: 1e293d6912d074c0fd15844d803400dd)
 
Using the mask shown in the section.
 
```diff
+ $ hashcat -a 3 -m 0 1e293d6912d074c0fd15844d803400dd '?u?l?l?l?l?d?s'
```
 
	1e293d6912d074c0fd15844d803400dd:Mouse5!
 
&#x1F6A9; found **Mouse5!**.
 
---
 
### Writing Custom Wordlists and Rules
 
### Question 1:
What is Mark's password?
 
I start off by creating a wordlist of 100 candidate strings from the words gathered via OSINT on Mark White. I then copy the ruleset used in the module and echo it to a file named custom.rules. After this, I pass both files to hashcat, using the wordlist and the custom rule.
 
```diff
+ $ hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list
```
 
After that's done, I then use the new wordlist with hashcat using dictionary mode.
 
```diff
+ $ hashcat -a 0 -m 0 97268a8ae45ac7d15c3cea4ce6ea550b mutated.txt -d 1
```
 
	/snip/
	97268a8ae45ac7d15c3cea4ce6ea550b:Baseball1998!
	/snip/
 
&#x1F6A9; found **Baseball1998!**.
 
---
 
### Cracking Protected Files
 
### Question 1:
Download the attached ZIP archive (cracking-protected-files.zip), and crack the file within. What is the password?
 
Pretty basic, I start by curling the link to the file.
 
```diff
+ $ curl https://cdn.services-k8s.prod.aws.htb.systems/content/questions/file/c2d77580-c371-45c4-9af4-e964d8bdeef4.zip > file.zip
```
 
Unzip the file, which gives me Confidential.xlsx.
 
```diff
+ $ unzip file.zip
```
 
And from here I get the hashes from the file using the office2john tool.
 
```diff
+ $ office2john Confidential.xlsx > hash.txt
```
 
Then I pass the file to john for cracking.
 
```diff
+ $ john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```
 
After some time it reveals the password.
 
&#x1F6A9; found **beethoven**.
 
---
 
### Cracking Protected Archives
 
IP: 154.57.164.82:30180
 
### Question 1:
Run the above target, then navigate to http://ip:port/download, then extract the downloaded file. Inside, you will find a password-protected VHD file. Crack the password for the VHD and submit the recovered password as your answer.
 
Start off by visiting the site at http://154.57.164.82:30180/download and downloading the file. From there I unzip the file and get the Private.vhd. With this, I use the bitlocker2john tool to get the hashes from the file.
 
```diff
+ $ bitlocker2john -i Private.vhd > hashes.txt
```
 
I then take the specific hash I want to crack, the password hash.
 
```diff
+ $ grep "bitlocker\$0" hashes.txt > thehash.txt
+ $ cat thehash.txt
```
 
	$bitlocker$0$16$b3c105c7ab7faaf544e84d712810da65$1048576$12$b020fe18bbb1db0103000000$60$e9c6b548788aeff190e517b0d85ada5daad7a0a3f40c4467307011ac17f79f8c99768419903025fd7072ee78b15a729afcf54b8c2e3af05bb18d4ba0
 
I then take this hash and pass it to hashcat, specifying the hash to be for bitlocker.
 
```diff
+ $ hashcat -a 0 -m 22100 '$bitlocker$0$16$b3c105c7ab7faaf544e84d712810da65$1048576$12$b020fe18bbb1db0103000000$60$e9c6b548788aeff190e517b0d85ada5daad7a0a3f40c4467307011ac17f79f8c99768419903025fd7072ee78b15a729afcf54b8c2e3af05bb18d4ba0' /usr/share/wordlists/rockyou.txt
```
 
Once it cracks the hash, the password is revealed.
 
&#x1F6A9; found **francisco**.
 
---
 
### Question 2:
Mount the BitLocker-encrypted VHD and enter the contents of flag.txt as your answer.
 
Now I start the mounting process using the dislocker tool.
 
```diff
+ $ sudo mkdir -p /media/bitlocker
+ $ sudo mkdir -p /media/bitlockermount
+ $ sudo losetup -f -P Private.vhd
+ $ sudo dislocker /dev/loop0p1 -u'francisco' -- /media/bitlocker
+ $ sudo mount -o loop /media/bitlocker/dislocker-file /media/bitlockermount
+ $ cd /media/bitlockermount/
+ $ cat flag.txt
```
 
