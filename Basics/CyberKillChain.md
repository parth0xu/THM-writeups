# Cyber Kill Chain Write-up

## Introduction
The Cyber Kill Chain is a cyber security framework introduced by Lockheed Martin in 2011. It helps organisations understand how cyber attacks are performed and how defenders can stop attackers at different stages.

The Cyber Kill Chain consists of **7 phases**:

1. Reconnaissance  
2. Weaponisation  
3. Delivery  
4. Exploitation  
5. Installation  
6. Command & Control (C2)  
7. Actions on Objectives  

---

# 1. Reconnaissance

## Overview
Reconnaissance is the information-gathering stage. Attackers collect details about the target’s systems, employees, domains, and vulnerabilities.

It can be:
- **Passive Reconnaissance** → No direct interaction with the target
- **Active Reconnaissance** → Direct interaction such as scanning or social engineering

## Examples
- WHOIS lookup
- DNS enumeration
- Social media analysis
- Google Dorking
- Port scanning
- Vulnerability scanning

## Defence Measures
- Reduce public information exposure
- Use WHOIS privacy
- Monitor logs and traffic
- Detect scanning attempts

## Questions & Answers
### Q1. What is the term for using search engines to reveal sensitive information and confidential files?
**Answer:** Google Dorking

### Q2. What type of reconnaissance is it where the attacker checks social media pages?
**Answer:** Passive reconnaissance

---

# 2. Weaponisation

## Overview
In this phase, the attacker prepares the malicious payload based on the vulnerabilities discovered during reconnaissance.

The payload may be:
- Malware
- Exploit code
- Malicious Office document
- Trojanized software

Attackers often use:
- Obfuscation
- Encryption
- Exploit kits

## Examples
- Malicious Word documents
- Macro-based attacks
- Exploit kits
- Weaponized PDFs

## Defence Measures
- User awareness training
- Disable unnecessary software
- Disable Office macros
- Apply group policies

## Questions & Answers
### Q1. What technique is mentioned to evade detection by making malicious code difficult to analyse?
**Answer:** Obfuscation

### Q2. What built-in feature makes creating a malicious MS Office document possible?
**Answer:** Macros

---

# 3. Delivery

## Overview
The attacker sends the prepared payload to the victim using different delivery methods.

## Examples
- Phishing emails
- Spear phishing
- Malicious web links
- Smishing
- Malvertising
- USB attacks
- Social engineering

## Defence Measures
- Email filtering
- Web filtering
- User training
- WAF deployment
- Network monitoring

## Questions & Answers
### Q1. What method involves showing advertisements on legitimate websites to redirect users to malicious pages?
**Answer:** Malvertising

### Q2. What phishing attack sends text messages with malicious links?
**Answer:** Smishing

---

# 4. Exploitation

## Overview
The attacker exploits vulnerabilities or weak authentication mechanisms to gain access.

## Examples
- Weak passwords
- SQL injection
- Buffer overflow
- Zero-day exploits
- Vulnerable services

## Defence Measures
- Patch management
- MFA implementation
- Vulnerability scanning
- IPS and WAF deployment

## Questions & Answers
### Q1. What type of exploit is used before the vendor becomes aware of a vulnerability?
**Answer:** Zero-day exploit

### Q2. What technology prevents attackers from gaining access even with valid credentials?
**Answer:** Multi-factor authentication (MFA)

---

# 5. Installation

## Overview
The attacker installs malware or persistence mechanisms to maintain long-term access.

## Examples
- Malware installation
- Rootkits
- Backdoors
- Web shells
- Scheduled tasks
- Startup services

## Defence Measures
- EDR solutions
- Process monitoring
- Secure baselines
- Application allowlisting

## Questions & Answers
### Q1. What tactic allows attackers to execute operating system commands through a browser interface?
**Answer:** Web shell

### Q2. What technique prevents unauthorised software from running?
**Answer:** Application allowlisting

---

# 6. Command & Control (C2)

## Overview
Attackers establish communication channels with compromised systems to control them remotely.

## Examples
- HTTPS communication
- DNS tunnelling
- SMTP communication
- Social media messaging
- Cloud services
- Fast Flux
- DGA (Domain Generation Algorithm)

## Defence Measures
- IDS/IPS
- DNS monitoring
- Traffic analysis
- Encryption inspection
- Honeypots

## Questions & Answers
### Q1. What is the tactic where data is hidden inside DNS requests?
**Answer:** DNS tunnelling

### Q2. What protocol is used to disguise data as encrypted web traffic?
**Answer:** HTTPS

---

# 7. Actions on Objectives

## Overview
This is the final phase where attackers achieve their goals.

## Examples
- Data theft
- Ransomware
- Financial fraud
- Industrial espionage
- Lateral movement
- ICS manipulation

## Defence Measures
- DLP solutions
- Network segmentation
- EDR
- User activity monitoring
- Backup and recovery plans

## Questions & Answers
### Q1. What is the term for stealing sensitive files from a target network?
**Answer:** Data exfiltration

### Q2. What principle limits access to sensitive systems and data?
**Answer:** Principle of least privilege

### Q3. What attack encrypts files and demands payment for decryption?
**Answer:** Ransomware attack

---

# Final Challenge Mapping

| Cyber Kill Chain Stage | Correct Match |
|---|---|
| Reconnaissance | WHOIS query |
| Weaponisation | Create a malicious document |
| Delivery | Send spear phishing email |
| Exploitation | Log in with default credentials |
| Installation | Install malware |
| Command & Control | Use SMTP for communication |
| Actions on Objectives | Data exfiltration |

---

# Final Flag

`THM{CKC_NJHERDX327}`

---

# Conclusion
The Cyber Kill Chain framework provides a structured way to understand cyber attacks from start to finish. By understanding each phase, defenders can detect, prevent, and disrupt attacks before attackers achieve their objectives. It remains one of the most widely used concepts in modern cyber defence and threat analysis.
