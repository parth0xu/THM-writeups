# TryHackMe - OhSINT Writeup

## Room Overview

The challenge provides only a single image file:

```text
WindowsXP_1551719014755.jpg
```

Goal:

Use Open Source Intelligence (OSINT) techniques to uncover information about the person connected to this image.

---

# Step 1: Initial Inspection

Open the image.

It appears to be the famous Windows XP wallpaper called **Bliss**.

Nothing visually suspicious appears in the image itself.

Classic OSINT instinct activates:

> Check the metadata.

---

# Step 2: Extract Metadata Using ExifTool

Run:

```bash
exiftool WindowsXP_1551719014755.jpg
```

Important output:

```text
Copyright : OWoodflint
```

We now have a username:

```text
OWoodflint
```

This is our first pivot point.

---

# Step 3: Search the Username

Search the username on Google:

```text
OWoodflint
```

Several profiles appear.

Important platforms discovered:

- Twitter/X
- GitHub

---

# Step 4: Investigate Twitter

The Twitter account provides multiple clues.

## Clue 1: Avatar

The profile picture is a:

```text
cat
```

### Answer

## What is this user's avatar of?

```text
cat
```

---

## Clue 2: Location

The profile/location information indicates:

```text
London
```

### Answer

## What city is this person in?

```text
London
```

---

# Step 5: Find the SSID

Continue examining tweets/posts carefully.

One of the clues references a WiFi network.

The SSID found is:

```text
UnileverWiFi
```

### Answer

## What is the SSID of the WAP he connected to?

```text
UnileverWiFi
```

---

# Step 6: Investigate GitHub

Search:

```text
OWoodflint GitHub
```

Open the GitHub profile.

Check:

- repositories
- commits
- README files
- profile details

Eventually the email address appears.

Email found:

```text
OWoodflint@gmail.com
```

### Answer

## What is his personal email address?

```text
OWoodflint@gmail.com
```

---

# Step 7: Identify the Website

Question asks:

> What site did you find his email address on?

The email was discovered on:

```text
GitHub
```

### Answer

## What site did you find his email address on?

```text
GitHub
```

---

# Step 8: Discover Holiday Destination

Continue investigating public posts and linked information.

A holiday destination is revealed:

```text
New York
```

### Answer

## Where has he gone on holiday?

```text
New York
```

---

# Step 9: Find the Password

Further OSINT investigation reveals the password.

Password found:

```text
pennYDr0pper.!
```

### Answer

## What is the person's password?

```text
pennYDr0pper.!
```

---

# Final Answers

| Question | Answer |
|---|---|
| Avatar | cat |
| City | London |
| SSID | UnileverWiFi |
| Email Address | OWoodflint@gmail.com |
| Website | GitHub |
| Holiday Destination | New York |
| Password | pennYDr0pper.! |

---

# Full Methodology Summary

## Workflow Used

1. Analyze image metadata
2. Extract username
3. Search username online
4. Pivot between social media platforms
5. Correlate public information
6. Recover sensitive details

---

# Useful Commands

## Metadata Analysis

```bash
exiftool image.jpg
```

```bash
strings image.jpg
```

```bash
binwalk image.jpg
```

---

# OSINT Lessons Learned

This room demonstrates how dangerous public digital breadcrumbs can be.

A single image exposed:

- Social accounts
- Location
- Email
- WiFi SSID
- Travel history
- Password

Tiny clue → massive identity map.

Modern OSINT feels less like detective work and more like assembling shattered mirrors into a full reflection.

---

# Tools Used

| Tool | Purpose |
|---|---|
| exiftool | Metadata extraction |
| Google Search | Username pivoting |
| Twitter/X | Social intelligence |
| GitHub | Email discovery |

---

# Conclusion

OhSINT is one of the best beginner OSINT rooms because it teaches the most important principle:

> Small public clues combine into major intelligence leaks.

Always sanitize metadata before uploading files publicly.
