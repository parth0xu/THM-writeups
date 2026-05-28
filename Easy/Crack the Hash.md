# TryHackMe — Crack the Hash | Full Writeup

**Room:** [Crack the Hash](https://tryhackme.com/room/crackthehash)  
**Difficulty:** Easy  
**Author:** Parth | Quantum University Roorkee, B.Tech CSE (Batch 2024–2028)  
**Tools Used:** `hashid`, `hashcat`, `john`, `CrackStation`, `hashes.com`

---

## 📌 Overview

This room teaches you how to identify and crack various types of hashed passwords using tools like Hashcat and John the Ripper, as well as online lookup tools. Hashes are one-way cryptographic functions — to "crack" them, we compare the hash against known password dictionaries (like `rockyou.txt`).

---

## 🛠️ Tools & Setup

```bash
# Install hashid
pip install hashid

# Check hash type
hashid <hash>

# John the Ripper (pre-installed on Kali)
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt

# Hashcat basic syntax
hashcat -m <mode> -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```

> **Tip:** Always run `hashid <hash>` first to identify the hash type, then match the mode number from [Hashcat Example Hashes](https://hashcat.net/wiki/doku.php?id=example_hashes).

---

## ✅ Task 1 — Level 1

### Hash 1: `48bb6e862e54f2a795ffc4e541caed4d`

**Step 1 — Identify hash type:**
```bash
hashid 48bb6e862e54f2a795ffc4e541caed4d
# Output: [+] MD5 [Hashcat Mode: 0]
```

**Step 2 — Crack with hashcat:**
```bash
echo "48bb6e862e54f2a795ffc4e541caed4d" > hash.txt
hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```

**Result:**
```
48bb6e862e54f2a795ffc4e541caed4d:easy
```

> ✅ **Answer:** `easy`  
> **Hash Type:** MD5  
> **Lesson:** MD5 is completely broken for password storage. Never use it.

---

### Hash 2: `CBFDAC6008F9CAB4083784CBD1874F76618D2A97`

**Step 1 — Identify hash type:**
```bash
hashid CBFDAC6008F9CAB4083784CBD1874F76618D2A97
# Output: [+] SHA-1 [Hashcat Mode: 100]
```
> 40 hex characters = SHA-1

**Step 2 — Crack with hashcat:**
```bash
echo "CBFDAC6008F9CAB4083784CBD1874F76618D2A97" > hash.txt
hashcat -m 100 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```

**Result:**
```
CBFDAC6008F9CAB4083784CBD1874F76618D2A97:password123
```

> ✅ **Answer:** `password123`  
> **Hash Type:** SHA-1  
> **Lesson:** SHA-1 is also deprecated and broken for passwords.

---

### Hash 3: `1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032`

**Step 1 — Identify hash type:**
```bash
hashid 1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032
# Output: [+] SHA-256 [Hashcat Mode: 1400]
```
> 64 hex characters = SHA-256

**Step 2 — Crack with hashcat:**
```bash
echo "1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032" > hash.txt
hashcat -m 1400 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```

**Result:**
```
1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032:letmein
```

> ✅ **Answer:** `letmein`  
> **Hash Type:** SHA-256  
> **Lesson:** Even SHA-256 is crackable with weak passwords and a wordlist.

---

### Hash 4: `$2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom`

**Step 1 — Identify hash type:**

The prefix `$2y$` is the giveaway — this is **bcrypt (blowfish)**. No need for hashid here.

| Prefix | Hash Type |
|--------|-----------|
| `$1$`  | MD5crypt  |
| `$2y$` | bcrypt    |
| `$5$`  | SHA-256crypt |
| `$6$`  | SHA-512crypt |

**Step 2 — Crack with hashcat:**
```bash
echo '$2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom' > hash.txt
hashcat -m 3200 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```

> ⚠️ bcrypt is intentionally slow — this will take longer than MD5/SHA hashes. That's by design!

**Result:**
```
$2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom:bleh
```

> ✅ **Answer:** `bleh`  
> **Hash Type:** bcrypt (Blowfish Unix)  
> **Lesson:** bcrypt is one of the strongest password hashing algorithms due to its adjustable cost factor.

---

### Hash 5: `279412f945939ba78ce0758d3fd83daa`

**Step 1 — Identify hash type:**
```bash
hashid 279412f945939ba78ce0758d3fd83daa
# Output: [+] MD4 [Hashcat Mode: 900]
```

> Looks like MD5 (32 chars) but CrackStation and standard MD5 mode may fail — the hint reveals it's **MD4**.

**Step 2 — Crack with hashcat + rules:**
```bash
echo "279412f945939ba78ce0758d3fd83daa" > hash.txt
hashcat -m 900 -a 0 hash.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule
```

> The `best64.rule` applies common mutations (capitalisation, number appending, etc.) which is why a plain dictionary attack may miss this one.

**Result:**
```
279412f945939ba78ce0758d3fd83daa:Eternity22
```

> ✅ **Answer:** `Eternity22`  
> **Hash Type:** MD4  
> **Lesson:** When a straight wordlist attack fails, try adding mutation rules.

---

## ✅ Task 2 — Level 2

> All answers are in `rockyou.txt`. Online tools may not work here — use hashcat!

---

### Hash 1: `F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85`

**Step 1 — Identify hash type:**
```bash
hashid F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85
# Output: [+] SHA-256 [Hashcat Mode: 1400]
```
> 64 hex characters → SHA-256

**Step 2 — Crack with hashcat:**
```bash
echo "F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85" > hash.txt
hashcat -m 1400 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```

**Result:**
```
F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85:paule
```

> ✅ **Answer:** `paule`  
> **Hash Type:** SHA-256

---

### Hash 2: `1DFECA0C002AE40B8619ECF94819CC1B`

**Step 1 — Identify hash type:**
```bash
hashid 1DFECA0C002AE40B8619ECF94819CC1B
# Output suggests MD5 — but MD5 cracking fails!
```

> ⚠️ **Trick question!** This is 32 hex characters like MD5, but it's actually **NTLM** (Windows password hash format). Always check hints and try alternative modes when MD5 fails.

```bash
# Find NTLM mode
hashcat --help | grep NTLM
# NTLM = mode 1000
```

**Step 2 — Crack with hashcat:**
```bash
echo "1DFECA0C002AE40B8619ECF94819CC1B" > hash.txt
hashcat -m 1000 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```

**Result:**
```
1DFECA0C002AE40B8619ECF94819CC1B:n63umy8lkf4i
```

> ✅ **Answer:** `n63umy8lkf4i`  
> **Hash Type:** NTLM  
> **Lesson:** NTLM and MD5 are both 32-char hex. When MD5 fails on a 32-char hash, try NTLM (mode 1000).

---

### Hash 3: `$6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.`
**Salt:** `aReallyHardSalt`

**Step 1 — Identify hash type:**

The `$6$` prefix tells us immediately — this is **sha512crypt** (Unix/Linux `/etc/shadow` format).

| Prefix | Algorithm | Hashcat Mode |
|--------|-----------|-------------|
| `$1$`  | MD5crypt   | 500 |
| `$2y$` | bcrypt     | 3200 |
| `$5$`  | SHA-256crypt | 7400 |
| `$6$`  | SHA-512crypt | 1800 |

**Step 2 — Crack with hashcat:**

> For salted Unix hashes, the entire string (including `$6$salt$hash`) goes into the file as-is.

```bash
echo '$6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.' > hash.txt
hashcat -m 1800 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```

> ⚠️ This will be slow — sha512crypt is designed to be computationally expensive.

**Result:**
```
$6$aReallyHardSalt$...:waka99
```

> ✅ **Answer:** `waka99`  
> **Hash Type:** SHA-512crypt (Unix)  
> **Lesson:** Linux stores passwords in `/etc/shadow` using these prefixed formats. Recognising the prefix is key.

---

### Hash 4: `e5d8870e5bdd26602cab8dbe07a942c8669e56d6`
**Salt:** `tryhackme`

**Step 1 — Identify hash type:**

40 hex characters → SHA-1 family. But this one has a **salt**, making it **HMAC-SHA1**.

```bash
# HMAC-SHA1 = Hashcat mode 160
hashcat --help | grep HMAC
```

**Step 2 — Crack with hashcat:**

For salted hashes, the format in the file is `hash:salt`:

```bash
echo "e5d8870e5bdd26602cab8dbe07a942c8669e56d6:tryhackme" > hash.txt
hashcat -m 160 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```

**Result:**
```
e5d8870e5bdd26602cab8dbe07a942c8669e56d6:tryhackme:481616481616
```

> ✅ **Answer:** `481616481616`  
> **Hash Type:** HMAC-SHA1  
> **Lesson:** Salted hashes need the `hash:salt` format. The salt makes rainbow table attacks useless, but dictionary attacks still work with weak passwords.

---

## 📊 Summary Table

| Task | Hash (truncated) | Type | Answer | Hashcat Mode |
|------|-----------------|------|--------|-------------|
| 1.1  | `48bb6e86...` | MD5 | `easy` | `-m 0` |
| 1.2  | `CBFDAC60...` | SHA-1 | `password123` | `-m 100` |
| 1.3  | `1C8BFE8F...` | SHA-256 | `letmein` | `-m 1400` |
| 1.4  | `$2y$12$...` | bcrypt | `bleh` | `-m 3200` |
| 1.5  | `279412f9...` | MD4 | `Eternity22` | `-m 900` |
| 2.1  | `F09EDCB1...` | SHA-256 | `paule` | `-m 1400` |
| 2.2  | `1DFECA0C...` | NTLM | `n63umy8lkf4i` | `-m 1000` |
| 2.3  | `$6$aReally...` | SHA-512crypt | `waka99` | `-m 1800` |
| 2.4  | `e5d8870e...` | HMAC-SHA1 | `481616481616` | `-m 160` |

---

## 🔑 Key Takeaways

1. **Always identify the hash type first** — use `hashid` or match prefixes (`$2y$`, `$6$`, etc.)
2. **Hash length is a clue** — 32 chars (MD5/NTLM), 40 chars (SHA-1), 64 chars (SHA-256)
3. **Prefixes never lie** — `$6$` is always sha512crypt; `$2y$` is always bcrypt
4. **Salted hashes use `hash:salt` format** in the hashcat input file
5. **When MD5 fails on a 32-char hash**, try NTLM (`-m 1000`)
6. **bcrypt and sha512crypt are strong** — they're slow by design, making brute-force very expensive
7. **Weak passwords are always crackable** — `easy`, `letmein`, `password123` fall instantly

---

## 📚 Resources

- [TryHackMe — Crack the Hash Room](https://tryhackme.com/room/crackthehash)
- [Hashcat Example Hashes](https://hashcat.net/wiki/doku.php?id=example_hashes)
- [CrackStation](https://crackstation.net/)
- [hashes.com](https://hashes.com/en/decrypt/hash)
- [rockyou.txt wordlist](https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt)

---

*Written by Parth — B.Tech CSE, Quantum University Roorkee (Batch 2024–2028)*
