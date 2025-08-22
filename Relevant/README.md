- RECON using NMAP and gobuster
 `nmap 10.201.111.170 -T 4 -A -v 5 `
 `gobuster dir -u http://10.201.111.170/ -w /usr/share/wordlists/dirbuster/directory-list-1.0.txt`
- nmap report
![Nmap Report 1](images/Pasted%20image%2020250820004847.png)  
![Nmap Report 2](images/Pasted%20image%2020250820004920.png)
- the machine have a smb server let try to enumerate it using smbmap
![SMBMap Result](images/Pasted%20image%2020250820005040.png)
- now let try login to nt4wrksv
![Login to nt4wrksv](images/Pasted%20image%2020250820005133.png)
- this is the content of the password file
![Password File](images/Pasted%20image%2020250820005228.png)
- as the file write that the pass word is encoded, i try to decode it using base64 and got this
![Base64 Decode 1](images/Pasted%20image%2020250820005825.png)  
![Base64 Decode 2](images/Pasted%20image%2020250820005903.png)
- so now i got 2 credential that i can use: 
	- `Bill:Juw4nnaM4n420696969!$$$` 
	- `Bob:!P@$$W0rD!123`
  ![Credentials Found](images/Pasted%20image%2020250820011512.png)
- When i try to access the IPC folder using Bill and Bob account nothing came up when i listed the files
- Hmm i remember that that the nt4wrksv allow READ AND WRITE let try to put a shell in that
![Upload Shell 1](images/Pasted%20image%2020250821014501.png)  
![Upload Shell 2](images/Pasted%20image%2020250821014531.png)
- now i will call that payload using curl and i should get a meterpreter in metasploit
	`curl http://10.201.77.152:49663/nt4wrksv/shell.aspx`
![Meterpreter Session](images/Pasted%20image%2020250821014739.png)
- nice now let find the flag
![User Flag Found 1](images/Pasted%20image%2020250821014826.png)  
- that is the user flag
![User Flag Found 2](images/Pasted%20image%2020250821015010.png)
- i will use getsystem to do privilege escalation
![GetSystem Privesc](images/Pasted%20image%2020250821015652.png)
- now that is the root flag
