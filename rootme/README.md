# TryHackMe RootMe Challenge

A beginner-friendly walkthrough documenting the exploitation and privilege escalation steps for the RootMe room on TryHackMe.

---

## Enumeration

Nmap:

```bash
nmap -sV <ip>
 ```
![Nmap Scan](images/nmap.png)
As we can see from the Nmap scan, we have ports open for SSH and HTTP. The first step I took was to visit the webserver and then run a gobuster scan to discover hidden directories that may lead to a vulnerability.
 <img width="750" height="319" alt="image" src="https://github.com/user-attachments/assets/d458d8ff-35b2-4e3c-916c-c4059863fcab" />

The index.html webpage didn’t offer anything useful, so let’s begin with directory enumeration.




GoBuster:
CMD: gobuster dir -u http://<ip> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
 

Quickly the scan revealed a /uploads directory, which suggested the target might be vulnerable to a malicious PHP upload. It also identified a /panel directory which may be useful as /uploads could be used to execute an uploaded script.
 







Exploitation
Here we are, at the upload panel. Let’s try uploading a PHP reverse shell and see if we can make progress.
 

The application blocked .php extensions. To bypass this, I renamed the reverse shell file to .php5, which was accepted.
 
 
Navigating to the /uploads directory confirmed our shell had been uploaded.
 






Before executing the script, we need to set up a Netcat listener on our attacking machine:
Executing the shell gave us a session as 'www-data'.
 

After stabilizing the shell, I used the find command to search for the user flag.
 












Privilege Escalation
After some enumeration I decided to look for files that have SUID permission with the command:
find / -user root -perm /4000 2> /dev/null
 


One binary that stood out as unusual was /usr/bin/python. Searching GTFOBins confirmed that Python with SUID can be exploited for privilege escalation.
 

After executing the provided GTFOBins payload, I successfully escalated privileges to root.
 

All that was left to do was capture the root flag, located in /root/root.txt:
 
