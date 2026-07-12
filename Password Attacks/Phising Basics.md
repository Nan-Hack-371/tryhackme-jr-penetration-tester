# Password Attacks – Phishing Basics

> **Platform:** TryHackMe
>
> **Room:** Password Attacks – Phishing Basics
>
> **Category:** Social Engineering
>
> **Difficulty:** Easy
>
> **Focus:** Understanding phishing attacks, psychology, email manipulation, phishing campaign lifecycle, and Social Engineering Toolkit (SET).

---

# Introduction

Phishing is one of the most effective methods for obtaining initial access during a penetration test because it targets the weakest part of any security system—the human.

Instead of exploiting software vulnerabilities, phishing exploits trust, urgency, fear, curiosity, and authority to convince users to reveal credentials or perform actions that benefit an attacker.

---

# Learning Objectives

- Understand phishing fundamentals
- Differentiate phishing, spear phishing, and whaling
- Learn psychological principles behind phishing
- Understand common phishing techniques
- Learn the anatomy of a phishing campaign
- Understand credential harvesting
- Learn how the Social Engineering Toolkit (SET) works
- Understand phishing reporting metrics

---

# What is Phishing?

Phishing is a **social engineering attack** where an attacker impersonates a trusted entity to trick victims into:

- Revealing credentials
- Opening malicious attachments
- Clicking malicious links
- Installing malware
- Sending sensitive information

Unlike traditional attacks, phishing targets **people rather than software**.

---

# Types of Phishing

## 1. Phishing

Mass attack targeting many users.

Example:

- Fake Microsoft email
- Fake bank notification
- Fake package delivery

Goal:

- Maximum victims
- Low customization

---

## 2. Spear Phishing

Highly targeted phishing.

Targets:

- Specific employee
- Finance team
- HR
- IT administrators

Uses OSINT like:

- LinkedIn
- Company websites
- News
- Social media

Goal:

- Gain initial access

---

## 3. Whaling

Targets executives.

Examples:

- CEO
- CFO
- Directors
- Managers

Goal:

- Financial fraud
- Business Email Compromise (BEC)
- Sensitive company information

---

# Why Phishing Works

People trust:

- Brands
- Managers
- IT department
- HR department

Attackers exploit emotions rather than technical weaknesses.

---

# Social Engineering Principles

## Scarcity

Creates fear of missing out.

Example:

> Only 3 licenses remaining.

---

## Urgency

Forces immediate action.

Example:

> Your account will expire in 12 hours.

---

## Authority

Pretends to be someone important.

Examples:

- CEO
- HR
- IT Support
- Finance

---

## Fear

Creates panic.

Example:

> Unauthorized login detected.

---

## Curiosity

Makes victims want to know more.

Example:

> Confidential Salary Revision

---

## Trust

Uses familiar branding.

Examples:

- Microsoft
- Google
- Company HR
- Known coworker

---

# Cognitive Biases

## Overconfidence Bias

"I'll never fall for phishing."

This often reduces caution.

---

## Confirmation Bias

Victim expects an email and trusts a fake version.

Example:

Waiting for bank email.

Fake bank email arrives.

Victim trusts it.

---

## Authority Bias

People naturally obey authority figures.

Example:

CEO requests urgent payment.

Employee complies.

---

# Phishing Techniques

## URL Masking

Displayed link:

```
https://tryhackme.com
```

Actual destination:

```
http://evil.attacker.com
```

---

## Typosquatting

Register similar domains.

Examples:

```
google.com

gooogle.com

go0gle.com

tryhacme.com
```

---

## Homograph Attack

Uses visually identical Unicode characters.

Example:

```
gοogle.com
```

(The "o" is actually a Greek character.)

---

## URL Shorteners

Hide destination.

Examples:

```
bit.ly
tinyurl
```

---

# Email Spoofing

Attackers manipulate email headers to impersonate trusted senders.

Examples:

```
From:
support@company.com
```

Actual sender:

```
attacker@gmail.com
```

Common methods:

- Display Name Spoofing
- Reply-To manipulation
- Return-Path manipulation

---

# Email Authentication

Modern email systems use:

## SPF

Specifies which mail servers may send emails.

---

## DKIM

Digitally signs emails.

Ensures integrity.

---

## DMARC

Combines SPF and DKIM.

Defines what happens when authentication fails.

---

# Credential Harvesting

Instead of stealing passwords directly, attackers clone login pages.

Workflow:

Victim →

Fake Login Page →

Credentials sent to attacker →

Victim redirected to legitimate site

Victim often assumes they simply mistyped their password.

---

# Payload Delivery

Common methods include:

- Microsoft Word macros
- PDF attachments
- ZIP files
- HTML attachments
- Fake login portals

---

# Macro-Based Attack

Typical workflow:

1. Victim opens document
2. Microsoft Word requests "Enable Content"
3. Victim enables macros
4. VBA executes
5. Malware or beacon runs

During authorized phishing simulations, benign beacons are used instead of malware.

---

# Common Phishing Tools

## GoPhish

Features:

- Email campaigns
- Landing pages
- Analytics
- Tracking
- SMTP integration

---

## Evilginx

Reverse proxy phishing framework.

Can capture:

- Credentials
- Session cookies
- MFA session tokens

---

## Social Engineering Toolkit (SET)

Provides:

- Credential Harvester
- Website Cloner
- Spear Phishing
- Payload generation
- Fake login pages

---

# Anatomy of a Phishing Campaign

## 1. Planning & Scoping

Define:

- Objectives
- Targets
- Timeline
- Rules of engagement
- Kill switch

---

## 2. Reconnaissance

Collect OSINT.

Sources:

- LinkedIn
- Company website
- Press releases
- Public documents
- Social media

---

## 3. Scenario Development

Create believable lures.

Examples:

- HR Update
- Invoice
- Password Expiration
- Security Alert

---

## 4. Exploitation

Launch campaign.

Monitor:

- Email opens
- Link clicks
- Simulated credential submissions
- Reports

---

## 5. Reporting

Analyze:

- Open rate
- Click rate
- Credential submissions
- Reporting rate

Recommend:

- Security awareness training
- MFA
- SPF
- DKIM
- DMARC

---

# Important Metrics

## Open Rate

Measures:

Users who opened the email.

Typical:

50–65%

---

## Click Rate

Measures:

Users clicking phishing links.

Typical:

8–14%

---

## Credential Entry Rate

Measures:

Users entering credentials.

Risk:

- <2% Low
- 2–5% Moderate
- >5% High

---

## Attachment Detonation Rate

Measures:

Users opening malicious attachments.

Higher than 5–7% indicates increased risk.

---

## Reporting Rate

Measures:

Users reporting phishing.

Good:

Above 40%

---

# Social Engineering Toolkit (SET) Walkthrough

## Goal

Create a credential harvesting phishing page using SET.

---

## Login

SSH into attacker VM.

```
ssh attacker@MACHINE_IP
```

---

## Start SET

```
SET
```

---

## Menu Path

```
1. Social Engineering Attacks

↓

2. Website Attack Vectors

↓

3. Credential Harvester Attack Method

↓

3. Custom Import
```

---

## Configure

POST Back IP:

```
MACHINE_IP
```

Website path:

```
/home/attacker/setoolkit/
```

Copy:

```
index.html
```

Original URL:

```
http://tryacounting.thm
```

Credential Harvester starts on:

```
Port 80
```

---

# Credential Harvesting Flow

Victim

↓

Receives phishing email

↓

Clicks fake login page

↓

Enters credentials

↓

SET captures POST request

↓

Credentials displayed in terminal

Example output:

```
WE GOT A HIT!

USERNAME:
bob.wilkinson

PASSWORD:
************
```

---

# Rainloop Email

Login:

```
attacker@phisher.thm
```

Password:

```
attacker1234
```

Use sender alias:

```
support@tryaccounting.thm
```

Subject:

```
Action Required:
Password Expiration Notice
```

Email contains:

- Password expiration pretext
- Typosquatted domain
- Credential harvesting page

---

# Real-World Attack Chain

Recon →

OSINT →

Typosquatted Domain →

Fake Login Page →

Credential Harvesting →

Initial Access →

Internal Network

---

# Red Flags Users Should Notice

- Misspelled domains
- Urgent language
- Password reset requests
- Unexpected attachments
- Shortened URLs
- Fake sender names
- Grammar mistakes
- Requests for credentials

---

# Pentester Checklist

✔ Define campaign scope

✔ Gather OSINT

✔ Choose believable scenario

✔ Register phishing domain (authorized engagements only)

✔ Configure landing page

✔ Configure credential harvester

✔ Send campaign

✔ Track metrics

✔ Analyze results

✔ Write report

---

# Key Takeaways

- Phishing exploits human psychology rather than software flaws.
- Spear phishing and whaling are targeted forms of phishing.
- Psychological triggers like urgency, authority, fear, curiosity, and trust significantly increase success rates.
- URL manipulation, email spoofing, and credential harvesting are common phishing techniques.
- SET simplifies the creation of phishing simulations for authorized security assessments.
- Professional phishing engagements emphasize planning, measurement, reporting, and user awareness—not credential collection for malicious purposes.
