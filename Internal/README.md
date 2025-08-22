- RECON using nmap and gobuster  
![Nmap Recon 1](images/Pasted%20image%2020250821191644.png)  
![Nmap Recon 2](images/Pasted%20image%2020250821191709.png)

- ok let view all of this  
![Directory Enumeration](images/Pasted%20image%2020250821203144.png)

- after mess around i see that they have a wordpress login page  
- let use wpscan to enumerate it  
  `wpscan --url http://internal.thm/blog/ --api_token=[_____] --enumerate u,vp`  
![WPScan Users Plugins](images/Pasted%20image%2020250821203502.png)

- the result came back with the name admin found, now let try i brute force it  
  `wpscan --url http://internal.thm/blog/ --passwords /usr/share/wordlists/rockyou.txt --usernames admin`  
![WPScan Bruteforce](images/Pasted%20image%2020250821204544.png)  
- Found it ,let login   
![WordPress Login](images/Pasted%20image%2020250821204823.png)

- hmm maybe this is a ssh credential ?  
- i think we can put a shell in the server using the 404.php file  
![Edit 404 File 1](images/Pasted%20image%2020250821212708.png)  
![Edit 404 File 2](images/Pasted%20image%2020250821212723.png)

- i will set up a reverse and a listener  
- now let pass the the payload data in to the 404 file and trigger the payload  
![Insert Payload](images/Pasted%20image%2020250821212833.png)

- trigger it using this  
  `internal.thm/blog/wp-content/themes/twentyseventeen/404.php`  
![Trigger Payload](images/Pasted%20image%2020250821212947.png)

- great we have a shell now, let find the flag  
![User Flag](images/Pasted%20image%2020250821214513.png)

- i found this , another cre ? `aubreanna:bubb13guM!@#123`  
![Found Credentials](images/Pasted%20image%2020250821215638.png)

- after a while searching for the flag, i can't find it so i remember that i found someone credential so i try to login using that and boom i found the first flag  
- And i saw that a jenkins server is running on port 8080 so let try to access that  
![Jenkins Login Page](images/Pasted%20image%2020250821220942.png)

- i use port forward to access the server, now i will try to brute force it  
![Jenkins Bruteforce](images/Pasted%20image%2020250821221802.png)

- ok i found a valid credential  `admin:spongebob`  
![Jenkins Credentials](images/Pasted%20image%2020250821222326.png)

- i think i will try to get a reverse shell using this function  
![Jenkins Reverse Shell Config](images/Pasted%20image%2020250821224327.png)  
- here is my payload  
![Jenkins Payload](images/Pasted%20image%2020250821224348.png)

- we got connection, i will try to find something usefull  
![Shell Gained](images/Pasted%20image%2020250821224559.png)

- nice i got root access now `root:tr0ub13guM!@#123`  
![Root Flag](images/Pasted%20image%2020250821224732.png)

- done that is the root flag

