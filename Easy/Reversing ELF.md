# Reverse Engineering CrackMe Writeup Collection 🧩

This document contains a complete step-by-step walkthrough for solving all the CrackMe binaries from `crackme1` to `crackme8`.

The techniques used include:

* Running binaries safely
* Using `strings`
* Static analysis
* Base64 decoding
* Ghidra/IDA analysis
* Password extraction
* Understanding comparisons and hidden logic

---

# CrackMe 1

## Challenge

> Let's start with a basic warmup, can you run the binary?

## Objective

Find the flag.

---

## Step 1: Check File Type

```bash
file crackme1
```

Output:

```bash
ELF 64-bit LSB executable
```

This confirms it is a Linux executable.

---

## Step 2: Give Execute Permission

```bash
chmod +x crackme1
```

---

## Step 3: Run the Binary

```bash
./crackme1
```

The binary immediately prints the flag.

Output:

```bash
flag{not_that_kind_of_elf}
```

---

# Final Flag

```text
flag{not_that_kind_of_elf}
```

================================================================================

# CrackMe 2

## Challenge

> Find the super-secret password! and use it to obtain the flag

---

## Objective

1. Find the hidden password
2. Use it to get the flag

---

## Step 1: Extract Strings

```bash
strings crackme2
```

Important output:

```text
super_secret_password
```

This immediately reveals the password.

---

## Step 2: Run the Binary

```bash
./crackme2
```

The program asks for a password.

Enter:

```text
super_secret_password
```

---

## Step 3: Obtain the Flag

After entering the password correctly:

```text
flag{if_i_submit_this_flag_then_i_will_get_points}
```

---

# Super Secret Password

```text
super_secret_password
```

# Final Flag

```text
flag{if_i_submit_this_flag_then_i_will_get_points}
```

================================================================================

# CrackMe 3

## Challenge

> Use basic reverse engineering skills to obtain the flag

---

## Objective

Find the hidden flag.

---

## Step 1: Use Strings

```bash
strings crackme3
```

You will notice suspicious Base64-looking text.

Example:

```text
ZmxhZ3tmMHJfeTB1cl81ZWMwbmRfbGU1NW9uX3VuYmFzZTY0XzRsbF83aDNfN2gxbmc1fQ==
```

---

## Step 2: Decode Base64

```bash
echo 'ZmxhZ3tmMHJfeTB1cl81ZWMwbmRfbGU1NW9uX3VuYmFzZTY0XzRsbF83aDNfN2gxbmc1fQ==' | base64 -d
```

Decoded result:

```text
flag{f0r_y0ur_5ec0nd_le55on_unbase64_4ll_7h3_7h1ng5}
```

---

# Final Flag

```text
flag{f0r_y0ur_5ec0nd_le55on_unbase64_4ll_7h3_7h1ng5}
```

================================================================================

# CrackMe 4

## Challenge

> Analyze and find the password for the binary

---

## Objective

Find the correct password.

---

## Step 1: Open in Ghidra or IDA

Load the binary into:

* Ghidra
* IDA Free
* Cutter
* Radare2

---

## Step 2: Locate Main Function

Inside the `main()` function you can see a password comparison.

Pseudo-code looked similar to:

```c
if(strcmp(input,"my_m0r3_secur3_pwd") == 0)
```

This directly reveals the password.

---

## Step 3: Run Binary

```bash
./crackme4
```

Enter:

```text
my_m0r3_secur3_pwd
```

Access granted.

---

# Password

```text
my_m0r3_secur3_pwd
```

================================================================================

# CrackMe 5

## Challenge

> What will be the input of the file to get output Good game?

---

## Objective

Find the exact input that produces:

```text
Good game
```

---

## Step 1: Analyze Binary Logic

Using Ghidra/IDA, inspect the comparison logic.

The binary validates a specific encoded input string.

The comparison eventually resolves to:

```text
OfdlDSA|3tXb32~X3tX@sX`4tXtz
```

---

## Step 2: Run Binary

```bash
./crackme5
```

Enter:

```text
OfdlDSA|3tXb32~X3tX@sX`4tXtz
```

Output:

```text
Good game
```

---

# Correct Input

```text
OfdlDSA|3tXb32~X3tX@sX`4tXtz
```

================================================================================

# CrackMe 6

## Challenge

> Analyze the binary for the easy password

---

## Objective

Find the password.

---

## Step 1: Run Strings

```bash
strings crackme6
```

Interesting output:

```text
1337_pwd
```

---

## Step 2: Verify in Binary

Run:

```bash
./crackme6
```

Enter:

```text
1337_pwd
```

Correct password accepted.

---

# Password

```text
1337_pwd
```

================================================================================

# CrackMe 7

## Challenge

> Analyze the binary to get the flag

---

## Objective

Extract the hidden flag.

---

## Step 1: Open Binary in Ghidra

After decompiling `main()`, the flag appears hardcoded inside the binary.

The decompiled code reveals:

```text
flag{much_reversing_very_ida_wow}
```

The challenge name itself hints toward using IDA.

---

## Step 2: Alternative Method

You can also use:

```bash
strings crackme7
```

Which may expose the flag directly.

---

# Final Flag

```text
flag{much_reversing_very_ida_wow}
```

================================================================================

# CrackMe 8

## Challenge

> Analyze the binary and obtain the flag

---

## Objective

Recover the hidden flag.

---

## Step 1: Static Analysis

Load the binary into:

* Ghidra
* IDA Free
* Cutter

Analyze the `main()` function.

---

## Step 2: Inspect Comparison Logic

The binary contains a hidden string comparison.

The recovered flag is:

```text
flag{at_least_this_cafe_wont_leak_your_credit_card_numbers}
```

---

## Step 3: Validate Format

Challenge format:

```text
****{**_*****_****_****_****_****_****_******_****_*******}
```

Recovered flag matches perfectly.

---

# Final Flag

```text
flag{at_least_this_cafe_wont_leak_your_credit_card_numbers}
```

================================================================================

# Common Reverse Engineering Techniques Used ⚙️

## 1. strings

Extracts readable text from binaries.

```bash
strings binary_name
```

Useful for:

* Passwords
* Flags
* URLs
* Error messages

---

## 2. file

Identifies binary type.

```bash
file binary_name
```

---

## 3. chmod

Makes binary executable.

```bash
chmod +x binary_name
```

---

## 4. Base64 Decoding

```bash
echo 'encoded' | base64 -d
```

---

## 5. Ghidra / IDA

Used for:

* Decompiled code
* Password checks
* Logic tracing
* Hidden strings

---

# Recommended Beginner RE Toolkit 🧠

## Linux Tools

```bash
strings
file
ltrace
strace
objdump
readelf
```

---

## GUI Reverse Engineering Tools

* Ghidra
* IDA Free
* Cutter
* Binary Ninja

---

# Final Thoughts 🚀

These CrackMe challenges are designed to teach the foundations of reverse engineering:

* Inspecting binaries
* Finding hidden strings
* Understanding program flow
* Recognizing encodings
* Reading decompiled code

The progression is like a dungeon crawler for binaries:

`strings → decoding → comparisons → decompilation → logic tracing`

Tiny ELF creatures hiding secrets in machine-code caves 🏰⚔️
