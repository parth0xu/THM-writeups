# Anthem TryHackMe Walkthrough

## Room Overview

This room focuses on:

* Basic enumeration
* Web reconnaissance
* Metadata analysis
* Credential discovery
* Windows file permission abuse
* RDP access

The room is beginner-friendly and does not require brute force.

---

# 1. Nmap Enumeration

Start with a full service scan:

```bash
nmap -sC -sV -A -Pn 10.49.184.58
```

## Results

```text
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft HTTPAPI httpd 2.0
3389/tcp open  ms-wbt-server Microsoft Terminal Services
```

## Answers

| Question            | Answer |
| ------------------- | ------ |
| Web server port     | 80     |
| Remote desktop port | 3389   |

---

# 2. Web Enumeration

Open the website:

```text
http://10.49.184.58
```

The site is a blog named:

```text
Anthem.com
```

## Answer

| Question | Answer     |
| -------- | ---------- |
| Domain   | anthem.com |

---

# 3. robots.txt Enumeration

Check robots.txt:

```text
http://10.49.184.58/robots.txt
```

## Contents

```text
UmbracoIsTheBest!

# Use for all search robots
User-agent: *

Disallow: /bin/
Disallow: /config/
Disallow: /umbraco/
Disallow: /umbraco_client/
```

## Findings

### Possible Password

```text
UmbracoIsTheBest!
```

### CMS Identification

The directories `/umbraco/` and `/umbraco_client/` indicate the CMS is:

```text
Umbraco
```

## Answers

| Question          | Answer            |
| ----------------- | ----------------- |
| Possible password | UmbracoIsTheBest! |
| CMS               | Umbraco           |

---

# 4. View Source Enumeration

Inspect the source code of the homepage:

```text
view-source:http://10.49.184.58/
```

Inside the search placeholder:

```html
placeholder="Search... THM{G!T_G00D}"
```

## Flag 2

```text
THM{G!T_G00D}
```

---

# 5. Blog Post Enumeration

## Post 1

Open:

```text
http://10.49.184.58/archive/a-cheers-to-our-it-department/
```

Inspect source:

```text
view-source:http://10.49.184.58/archive/a-cheers-to-our-it-department/
```

### Flag 1

Found in meta description:

```html
<meta content="THM{AN0TH3R_M3TA}" property="og:description" />
```

### Administrator Name

Found in author section:

```text
James Orchard Halliwell
```

## Answers

| Question           | Answer                  |
| ------------------ | ----------------------- |
| Flag 1             | THM{AN0TH3R_M3TA}       |
| Administrator name | James Orchard Halliwell |

---

# 6. Hiring Post Enumeration

Open:

```text
http://10.49.184.58/archive/we-are-hiring/
```

Inspect source:

```text
view-source:http://10.49.184.58/archive/we-are-hiring/
```

### Flag 3

```html
<meta content="THM{L0L_WH0_US3S_M3T4}" property="og:description" />
```

### Email Address

```text
JD@anthem.com
```

### Additional Administrator Name

```text
Jane Doe
```

## Answers

| Question            | Answer                                |
| ------------------- | ------------------------------------- |
| Flag 3              | THM{L0L_WH0_US3S_M3T4}                |
| Administrator email | [JD@anthem.com](mailto:JD@anthem.com) |
| Administrator name  | Jane Doe                              |

---

# 7. Author Page Enumeration

Open:

```text
http://10.49.184.58/authors/jane-doe/
```

## Flag 4

Inside the page:

```html
Website: <a href="THM{L0L_WH0_D15}">THM{L0L_WH0_D15}</a>
```

## Answer

| Question | Answer           |
| -------- | ---------------- |
| Flag 4   | THM{L0L_WH0_D15} |

---

# 8. Initial Access via RDP

The Umbraco login page is mostly a distraction.

Use RDP instead.

## Working Credentials

```text
Username: SG
Password: UmbracoIsTheBest!
```

## Linux Command

```bash
xfreerdp /u:SG /p:'UmbracoIsTheBest!' /v:10.49.184.58
```

---

# 9. User Flag

After logging in:

Navigate to:

```text
C:\Users\SG\Desktop
```

Read:

```text
user.txt
```

## Contents

```text
THM{N00T_NO0T}
```

## Answer

| Question | Answer         |
| -------- | -------------- |
| user.txt | THM{N00T_NO0T} |

---

# 10. Privilege Escalation

## Hidden Folder Discovery

Enable hidden items in Windows Explorer.

Navigate to:

```text
C:\
```

A hidden folder named:

```text
backup
```

is discovered.

Inside:

```text
restore.txt
```

Attempting to open the file results in Access Denied.

---

# 11. File Permission Abuse

## Check File Ownership

Right click:

```text
restore.txt → Properties → Security → Advanced
```

The owner is:

```text
SG
```

Since SG owns the file, permissions can be modified.

---

# 12. Grant Read Permissions

Add read permission for user:

```text
SG
```

Then open:

```text
restore.txt
```

## Administrator Password

```text
ChangeMeBaby1MoreTime
```

## Answer

| Question               | Answer                |
| ---------------------- | --------------------- |
| Administrator password | ChangeMeBaby1MoreTime |

---

# 13. Administrator Access

Login using RDP again:

```bash
xfreerdp /v:10.49.184.58 /u:administrator /p:'ChangeMeBaby1MoreTime'
```

---

# 14. Root Flag

Navigate to:

```text
C:\Users\Administrator\Desktop
```

Read:

```text
root.txt
```

## Contents

```text
THM{Y0U_4R3_1337}
```

## Answer

| Question | Answer            |
| -------- | ----------------- |
| root.txt | THM{Y0U_4R3_1337} |

---

# Final Answers Summary

| Question               | Answer                                |
| ---------------------- | ------------------------------------- |
| Web Server Port        | 80                                    |
| RDP Port               | 3389                                  |
| Possible Password      | UmbracoIsTheBest!                     |
| CMS                    | Umbraco                               |
| Domain                 | anthem.com                            |
| Administrator Name     | Jane Doe                              |
| Administrator Email    | [JD@anthem.com](mailto:JD@anthem.com) |
| Flag 1                 | THM{AN0TH3R_M3TA}                     |
| Flag 2                 | THM{G!T_G00D}                         |
| Flag 3                 | THM{L0L_WH0_US3S_M3T4}                |
| Flag 4                 | THM{L0L_WH0_D15}                      |
| user.txt               | THM{N00T_NO0T}                        |
| Administrator Password | ChangeMeBaby1MoreTime                 |
| root.txt               | THM{Y0U_4R3_1337}                     |

---

# Key Takeaways

* Always inspect `robots.txt`
* View-source often reveals hidden metadata
* CMS fingerprints can expose attack paths
* Hidden files and weak permissions are common Windows misconfigurations
* File ownership can allow privilege escalation even without explicit permissions
* RDP credential reuse is dangerous

---

# Room Completed

The room demonstrates how small pieces of information:

* metadata
* comments
* hidden files
* ownership permissions

can chain together into full system compromise.
