# Password Cracking

> Comprehensive notes on password hashing, hash identification, attack strategies, Hashcat, and John the Ripper from the TryHackMe Password Cracking room.

---

# Table of Contents

- Introduction
- Password Storage
- Cryptographic Hash Functions
- Common Hash Algorithms
- Salting
- Hash Identification
- Hashid
- Hashcat --identify
- Online Lookup Services
- Hashcat Modes & John Formats
- Attack Strategies
- Dictionary Attack
- Brute Force Attack
- Rule-Based Attack
- Mask Attack
- Choosing the Right Attack
- John the Ripper
- Hashcat
- Performance Tips
- John vs Hashcat
- Quick Reference
- Key Takeaways

---

# Introduction

Password cracking is an **offline attack** performed against password hashes.

Instead of attacking a login page directly, the attacker obtains password hashes from:

- Database leaks
- Windows SAM files
- Active Directory
- Linux shadow files
- Captured authentication handshakes

The objective is to recover the original plaintext password.

Learning goals:

- Understand password hashing
- Identify hash algorithms
- Crack hashes using John and Hashcat
- Perform dictionary, rule-based, brute-force, and mask attacks
- Choose the correct cracking technique

---

# Password Storage

Passwords should **never** be stored as plaintext.

❌ Bad

```
username : password123
```

✔ Good

```
username : hash(password123)
```

Authentication workflow:

```
User Password
      │
      ▼
 Hash Function
      │
      ▼
Generated Hash
      │
Compare with Stored Hash
      │
 ┌───────────────┐
 │ Match?        │
 └──────┬────────┘
        │
   Yes → Login
    No → Reject
```

---

# Cryptographic Hash Functions

A secure password hash has these properties:

## One-way

Cannot reverse the hash.

```
password
    ↓
Hash

Impossible to reverse
```

---

## Deterministic

Same input always produces the same output.

```
hash(password)

↓

5f4dcc3...
```

Every time.

---

## Fixed Length

Regardless of input size:

```
a

↓

32-character MD5

--------------------------------

verylongpassword...

↓

32-character MD5
```

---

## Collision Resistant

Very difficult to find two different inputs producing the same output.

Broken algorithms:

- MD5
- SHA-1

---

# Common Hash Algorithms

| Algorithm | Length | Recommended | Notes |
|-----------|----------|-------------|------|
| MD5 | 32 hex | ❌ | Very fast |
| SHA1 | 40 hex | ❌ | Broken |
| SHA256 | 64 hex | ⚠ Sometimes | Fast |
| NTLM | 32 hex | Legacy | Windows |
| bcrypt | ~60 chars | ✔ | Slow |
| Argon2 | Variable | ✔✔ | Modern standard |

---

# Why Slow Hashes Matter

Fast algorithms:

```
MD5
SHA1
SHA256
```

Modern GPUs calculate **billions per second.**

Slow algorithms:

```
bcrypt
Argon2
```

Designed to slow attackers.

---

# Salting

Without salt:

```
Password

↓

Hash

↓

Same password
=
Same hash
```

Example

```
Alice

password123

↓

HASH A

Bob

password123

↓

HASH A
```

---

With Salt

```
password123 + randomSalt

↓

Different hash
```

Example

```
Alice

password123 + X7$P

↓

HASH A

Bob

password123 + M29#

↓

HASH B
```

Benefits

- Prevents rainbow tables
- Prevents identical hashes
- Makes precomputed attacks useless

---

# Hash Identification

Always inspect:

- Length
- Prefix
- Context

| Type | Length | Prefix |
|-------|----------|--------|
| MD5 | 32 | None |
| SHA1 | 40 | None |
| SHA256 | 64 | None |
| bcrypt | ~60 | $2a$ $2b$ $2y$ |

---

Example

MD5

```
5f4dcc3b5aa765d61d8327deb882cf99
```

bcrypt

```
$2y$10$...
```

---

# Hashid

Install

```bash
sudo apt install hashid
```

Usage

```bash
hashid HASH
```

Example

```bash
hashid 5f4dcc3b5aa765d61d8327deb882cf99
```

Possible output

```
MD5
NTLM
MD4
...
```

Use context to determine the correct algorithm.

---

# Hashcat --identify

Hashcat can identify hash modes directly.

```bash
hashcat --identify HASH
```

Output

```
Mode 0 → MD5

Mode 1000 → NTLM

Mode 1400 → SHA256
```

Very useful before cracking.

---

# Online Lookup Services

Useful in labs and CTFs:

- CrackStation
- Hashes.com

These compare hashes against huge databases.

⚠ Never upload client hashes during real penetration tests.

---

# Hashcat Modes

| Algorithm | Hashcat Mode |
|------------|-------------|
| MD5 | 0 |
| SHA1 | 100 |
| SHA256 | 1400 |
| SHA512 | 1700 |
| NTLM | 1000 |
| bcrypt | 3200 |

---

# John Formats

| Algorithm | Format |
|-----------|---------|
| MD5 | raw-md5 |
| SHA1 | raw-sha1 |
| SHA256 | raw-sha256 |
| SHA512 | raw-sha512 |
| NTLM | nt |
| bcrypt | bcrypt |

---

# Attack Strategies

## Dictionary Attack

Uses an existing password list.

Example:

```
password

123456

admin123

summer2025
```

Advantages

- Fast
- Most successful first attack

Wordlists

```
rockyou.txt

SecLists
```

---

## Brute Force Attack

Tries every possible combination.

Example

```
aaaa

aaab

aaac

...

zzzz
```

Guaranteed success **if enough time is available**.

Disadvantages

- Extremely slow
- Exponential growth

---

## Rule-Based Attack

Mutates existing passwords.

Example

```
password

↓

Password

↓

Password1

↓

Password!

↓

P@ssword1
```

Useful Hashcat rules:

```
best64.rule

rockyou-30000.rule

d3ad0ne.rule

dive.rule
```

---

## Mask Attack

Use when password structure is known.

Hashcat placeholders

| Placeholder | Meaning |
|-------------|---------|
| ?l | lowercase |
| ?u | uppercase |
| ?d | digit |
| ?s | special |
| ?a | all printable |

Example

```
Summer2026!

↓

?u?l?l?l?l?l?d?d?d?d?s
```

Much faster than full brute force.

---

# Choosing the Right Attack

| Scenario | Best Attack |
|-----------|-------------|
| Unknown password | Dictionary |
| Common mutation | Rules |
| Known format | Mask |
| Small keyspace | Brute Force |
| Company-specific passwords | Custom wordlist + Rules |

---

# John the Ripper

Dictionary attack

```bash
john \
--format=raw-md5 \
--wordlist=/usr/share/wordlists/rockyou.txt \
demo.txt
```

---

Rule attack

```bash
john \
--format=raw-md5 \
--wordlist=/usr/share/wordlists/rockyou.txt \
--rules=wordlist \
demo2.txt
```

---

Show cracked passwords

```bash
john --show --format=raw-md5 demo.txt
```

---

# Hashcat

Dictionary attack

```bash
hashcat \
-m 0 \
-a 0 \
demo.txt \
/usr/share/wordlists/rockyou.txt
```

---

Rule attack

```bash
hashcat \
-m 0 \
-a 0 \
demo2.txt \
/usr/share/wordlists/rockyou.txt \
-r /usr/share/hashcat/rules/best64.rule
```

---

Mask attack

```bash
hashcat \
-m 0 \
-a 3 \
demo3.txt \
'?l?l?l?l?l?l?l?l'
```

---

Save output

```bash
hashcat \
-m 0 \
-a 0 \
demo.txt \
rockyou.txt \
-o cracked.txt
```

---

Show cracked passwords

```bash
hashcat -m 0 demo.txt --show
```

---

Resume interrupted session

Start

```bash
hashcat \
--session=bcrypt_crack
```

Resume

```bash
hashcat \
--session=bcrypt_crack \
--restore
```

---

# Performance Tips

Hashcat

✔ GPU accelerated

✔ Extremely fast

✔ Best for large cracking jobs

John

✔ CPU optimized

✔ Better automatic detection

✔ Excellent for Linux password files

---

# John vs Hashcat

| Feature | John | Hashcat |
|-----------|---------|----------|
| Primary Hardware | CPU | GPU |
| Speed | Fast | Very Fast |
| Auto Detection | Excellent | Manual mode |
| Rule Support | Good | Excellent |
| Shadow Files | Excellent | Good |
| Large Scale Cracking | Good | Excellent |

---

# Common Wordlists

RockYou

```
/usr/share/wordlists/rockyou.txt
```

SecLists

```
/usr/share/wordlists/SecLists/
```

Hashcat Rules

```
/usr/share/hashcat/rules/
```

John Rules

```
/usr/local/john/run/rules/
```

---

# Workflow

```
Obtain Hash
      │
      ▼
Identify Hash
(hashid / hashcat --identify)
      │
      ▼
Dictionary Attack
      │
      ▼
Rule Attack
      │
      ▼
Mask Attack
      │
      ▼
Brute Force
      │
      ▼
Recovered Password
```

---

# Key Takeaways

- Never store plaintext passwords.
- Modern applications should use bcrypt or Argon2.
- Salt prevents rainbow table attacks.
- Always identify the hash before cracking.
- Dictionary attacks should be the first choice.
- Rules significantly improve success with little extra cost.
- Mask attacks are ideal when password patterns are known.
- Hashcat excels at GPU-accelerated cracking.
- John the Ripper is excellent for auto-detection and Unix password formats.
- Custom wordlists dramatically increase success during real-world engagements.

---

# References

- TryHackMe — Password Cracking
- Hashcat Documentation
- John the Ripper Documentation
- SecLists
- RockYou Wordlist
- Hashid
