# TryHackMe - Introduction to Wordlists

## Room Overview

This room introduces the importance of **wordlists** in penetration testing and ethical hacking. It covers how to create targeted custom wordlists using OSINT, clean and optimize them, and use them with tools such as **ffuf** and **Hydra** for directory enumeration and authentication testing.

---

# Learning Objectives

- Understand what wordlists are
- Learn where wordlists are used
- Gather OSINT for custom wordlists
- Build target-specific username and password lists
- Clean and normalize collected data
- Perform directory enumeration using ffuf
- Perform authentication testing using Hydra

---

# What is a Wordlist?

A wordlist is simply a text file where every line contains a possible value.

Examples include:

- Passwords
- Usernames
- Directories
- Files
- API endpoints
- Subdomains
- Parameters

Instead of manually guessing, security tools automatically iterate through every entry.

Example:

```
admin
administrator
login
backup
config
```

---

# Where Wordlists are Used

## Password Cracking

Tools

- John the Ripper
- Hashcat

Purpose

- Crack password hashes using dictionary attacks.

---

## Network Login Brute Force

Tools

- Hydra
- Medusa

Purpose

- SSH
- FTP
- SMB
- HTTP Login
- RDP

---

## Directory Enumeration

Tools

- ffuf
- Gobuster
- DirBuster

Purpose

Discover hidden directories such as

```
/admin
/uploads
/dev
/backup
```

---

## Subdomain Enumeration

Tools

- ffuf
- DNSRecon
- Sublist3r

Example

```
admin.example.com
dev.example.com
vpn.example.com
```

---

## Web Fuzzing

Tools

- Burp Suite
- OWASP ZAP

Used for

- Parameters
- Headers
- Cookies
- Hidden endpoints

---

# Common Wordlist Sources

## RockYou

Location

```
/usr/share/wordlists/rockyou.txt
```

Contains

- 14+ million leaked passwords

Used for

- Password cracking
- Hydra
- John
- Hashcat

---

## SecLists

Large collection of

- Passwords
- Usernames
- Directories
- Subdomains
- Payloads
- API endpoints

Common locations

```
/usr/share/seclists/
```

---

## Custom Wordlists

Created specifically for the target.

Examples

- Company names
- Employee names
- Products
- Technologies
- Locations
- Internal projects

Custom lists are usually far more effective than generic ones.

---

## Crunch

Generates passwords based on patterns.

Example

```
crunch 6 6 abc123
```

Example pattern

```
crunch 11 11 -t Helios20%%!
```

Useful when password format is known.

---

# Information Gathering for Custom Wordlists

Good custom wordlists contain three categories.

## Company Specific

Examples

- Product names
- Internal projects
- Office names
- Departments
- Company slogans

---

## Technology Specific

Examples

- wordpress
- laravel
- react
- api
- graphql
- nginx

---

## Generic Terms

Examples

```
admin
login
portal
dashboard
settings
api
assets
uploads
backup
```

---

# OSINT Sources

## LinkedIn

Useful for

- Employee names
- Job titles
- Technologies
- Naming conventions

Tools

- linkedin2username
- CrossLinked

---

## Company Website

Collect

- Products
- Services
- Team names
- Blog content
- Internal terminology

---

## Social Media

Useful for

- Project names
- Events
- Hashtags
- Technologies

---

## Job Advertisements

Can reveal

- AWS
- Azure
- Kubernetes
- React
- Laravel
- Jenkins

Technology names often become

- Directories
- API paths
- Subdomains

---

## WHOIS

Provides

- Registrar
- DNS servers
- Contact information
- Domain details

Command

```
whois example.com
```

---

## Certificate Transparency

Useful for discovering

- Historical subdomains

Examples

```
crt.sh
```

---

## Technology Fingerprinting

Tools

- Wappalyzer
- BuiltWith

Useful for selecting technology-specific wordlists.

---

# CeWL

CeWL crawls websites and extracts useful words.

Example

```bash
cewl -d 2 \
-m 3 \
--lowercase \
--with-numbers \
-e \
--email_file emails.txt \
-w cewl_words.txt \
http://target
```

## Useful Options

| Option | Purpose |
|----------|----------|
| -d | Crawl depth |
| -m | Minimum word length |
| --lowercase | Convert words to lowercase |
| --with-numbers | Include numeric words |
| -e | Extract email addresses |
| --email_file | Save emails |
| -w | Save words |

Outputs

```
cewl_words.txt
emails.txt
```

---

# Downloading Documents

Example

```bash
wget -r -A pdf http://target/docs/
```

Useful for

- Internal terminology
- Company names
- Credentials
- Employee names

---

# Extracting Strings

Example

```bash
strings document.pdf
```

Useful for finding

- Hidden words
- URLs
- Paths
- API names

---

# Extracting Emails

Example

```bash
grep -RhiaoP 'regex' docs/
```

Generate usernames from

```
john.smith@company.com

↓

john.smith
```

---

# Harvesting Employee Names

Example

```bash
curl http://social.target
```

Extract

```
Alex Johnson
Jane Smith
```

Generate username formats

```
alex.johnson

ajohnson

alexj
```

---

# Cleaning Wordlists

Raw data contains

- Duplicate entries
- Uppercase words
- Invalid symbols
- Short words

Cleaning improves speed and accuracy.

---

## Merge Lists

```bash
cat cewl_words.txt raw_words.txt | sort -u > words_raw.txt
```

---

## Normalize

```bash
tr '[:upper:]' '[:lower:]'
```

---

## Remove Windows Characters

```bash
tr -d '\r'
```

---

## Filter

Example

```bash
grep -P '^[a-z0-9][a-z0-9._-]{4,}$'
```

Removes

- Short words
- Invalid strings
- Noise

---

## Preview

Count

```bash
wc -l words_clean.txt
```

View

```bash
head words_clean.txt
```

---

# Username List

Merge username formats

```bash
cat users_first.last.txt \
users_flast.txt \
users_firstl.txt \
users_from_emails.txt \
| sort -u > users.txt
```

---

# Password Pattern Generation

If password format is known

Example

```
Helios20NN!
```

Generate

```bash
crunch 11 11 -t Helios20%%!
```

Produces

```
Helios2000!
Helios2001!
...
Helios2099!
```

---

# Directory Enumeration with ffuf

Purpose

Discover

- Hidden directories
- Files
- Applications

Example

```bash
ffuf \
-w words_clean.txt \
-u http://target/FUZZ \
-e .php,.html,/ \
-mc 200,301,302
```

Important Flags

| Flag | Purpose |
|---------|----------|
| -w | Wordlist |
| -u | Target URL |
| FUZZ | Position for payload |
| -e | Extensions |
| -mc | Match status codes |

Useful filters

```
-fs
```

Filter by response size.

```
-fl
```

Filter by response lines.

---

# Login Brute Force with Hydra

Example

```bash
hydra \
-L users.txt \
-P pass_helios.txt \
-f \
-V \
-t 4 \
target \
http-post-form '/login.php:username=^USER^&password=^PASS^:S=Success'
```

## Flags

| Flag | Purpose |
|----------|----------|
| -L | Username file |
| -P | Password file |
| -f | Stop after first success |
| -V | Verbose |
| -t | Threads |

Hydra automatically replaces

```
^USER^

^PASS^
```

during every login attempt.

---

# Typical Workflow

```
OSINT

↓

Collect Company Words

↓

Collect Employee Names

↓

Generate Usernames

↓

Generate Password Patterns

↓

Clean Wordlists

↓

Directory Enumeration (ffuf)

↓

Authentication Testing (Hydra)

↓

Discover Valid Credentials
```

---

# Best Practices

- Prefer custom wordlists over generic ones.
- Keep wordlists clean and deduplicated.
- Use company-specific terminology.
- Fingerprint technologies before fuzzing.
- Generate usernames from public OSINT.
- Use Crunch when password patterns are known.
- Filter ffuf results to remove false positives.
- Limit Hydra threads to avoid overwhelming services.
- Always stay within the scope of authorized penetration tests.

---

# Key Takeaways

- Wordlists are fundamental to offensive security.
- Custom OSINT-based wordlists outperform generic lists.
- CeWL helps collect target-specific words.
- PDFs often reveal useful internal terminology.
- Username generation improves authentication testing.
- Cleaning wordlists increases efficiency.
- ffuf discovers hidden directories and files.
- Hydra performs automated authentication testing using custom username and password lists.
