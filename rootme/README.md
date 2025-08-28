# TryHackMe RootMe Challenge

A beginner-friendly walkthrough documenting the exploitation and privilege escalation steps for the RootMe room on TryHackMe.

---

## Enumeration

### Nmap:

```bash
nmap -sV <ip>
 ```
<img width="750" height="219" alt="image" src="https://github.com/user-attachments/assets/68602032-5ecd-45fe-b111-407cfa8bc46d" />

As we can see from the Nmap scan, we have ports open for SSH and HTTP. The first step I took was to visit the webserver and then run a gobuster scan to discover hidden directories that may lead to a vulnerability.
 <img width="750" height="319" alt="image" src="https://github.com/user-attachments/assets/d458d8ff-35b2-4e3c-916c-c4059863fcab" />

The index.html webpage didn’t offer anything useful, so let’s begin with directory enumeration.




### GoBuster:
```bash
gobuster dir -u http://<ip> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
 ```
<img width="750" height="310" alt="image" src="https://github.com/user-attachments/assets/ea4f95cb-d439-4fb0-8607-490a86027d5f" />

Quickly the scan revealed a /uploads directory, which suggested the target might be vulnerable to a malicious PHP upload. It also identified a /panel directory which may be useful as /uploads could be used to execute an uploaded script.
 
<img width="600" height="307" alt="image" src="https://github.com/user-attachments/assets/0a57ecf6-dfc7-45cc-a886-a5a5bd98cca9" />


## Exploitation
Here we are, at the upload panel. Let’s try uploading a PHP reverse shell and see if we can make progress.
 <img width="600" height="569" alt="image" src="https://github.com/user-attachments/assets/04bffa27-10c1-4320-b056-74d4cbffe712" />


The application blocked .php extensions. To bypass this, I renamed the reverse shell file to .php5, using the command:
 ```bash
mv shell.php shell.php5
```

 <img width="600" height="608" alt="image" src="https://github.com/user-attachments/assets/ca7b739e-5119-4ae0-8929-2c1c969a0646" />

Navigating to the /uploads directory confirmed our shell had been uploaded.
<img width="600" height="324" alt="image" src="https://github.com/user-attachments/assets/733140df-29a7-4a7f-a602-18e80e2ea93b" />







Before executing the script, we need to set up a Netcat listener on our attacking machine:
Executing the shell gave us a session as 'www-data'.
 <img width="750" height="275" alt="image" src="https://github.com/user-attachments/assets/feabdb87-3745-4dab-a481-51f03aa99fad" />


After stabilizing the shell, I used the find command to search for the user flag.
 


<img width="750" height="248" alt="image" src="https://github.com/user-attachments/assets/ed540e59-d3c8-4bae-a284-5413248a6b6e" />










## Privilege Escalation
After some enumeration I decided to look for files that have SUID permission with the command:
```bash
find / -user root -perm /4000 2> /dev/null
```
 <img width="750" height="544" alt="image" src="https://github.com/user-attachments/assets/4683a5d5-e4cf-43bd-aa1c-554d702df510" />



One binary that stood out as unusual was /usr/bin/python. Searching GTFOBins confirmed that Python with SUID can be exploited for privilege escalation.

<img width="750" height="296" alt="image" src="https://github.com/user-attachments/assets/ce77d985-6934-4304-a541-1de5f8b7b1d9" />


After executing the provided GTFOBins payload, I successfully escalated privileges to root.
 <img width="750" height="133" alt="image" src="https://github.com/user-attachments/assets/392e1800-ab40-4ead-bbbe-d5d9c50d30fb" />


All that was left to do was capture the root flag, located in /root/root.txt:
 
<img width="600" height="453" alt="image" src="https://github.com/user-attachments/assets/133ac335-f5c0-4df6-be35-56a1573a4b2a" />
