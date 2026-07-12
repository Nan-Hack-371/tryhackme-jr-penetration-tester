# Server Side Request Forgery (SSRF)

## Overview

SSRF allows an attacker to make the server send HTTP requests to arbitrary destinations.

---

## Types

- Regular SSRF
- Blind SSRF

---

## Common Locations

- URL Fetch
- PDF Generator
- Image Fetch
- Webhooks
- File Import
- Integrations

---

## Detection

Look for

- URL parameter
- Host parameter
- Hidden fields
- File download URL

---

## Exploitation Flow

Find URL Parameter

↓

localhost

↓

127.0.0.1

↓

Internal Services

↓

Cloud Metadata

↓

Credentials

---

## Blind SSRF Detection

- Burp Collaborator
- Interactsh
- RequestBin
- HTTP Listener
- DNS Callback

---

## Bypass Techniques

### Loopback Variations

127.1

2130706433

017700000001

[::1]

127.0.0.1.nip.io

---

### Allowlist Bypass

https://website.thm@attacker.com

https://website.thm.attacker.com

---

### Open Redirect

https://trusted.com/redirect?url=http://169.254.169.254

---

## Cloud Metadata

AWS

169.254.169.254/latest/meta-data/

---

## Prevention

- Strict Allowlist
- Disable Internal Requests
- URL Parsing
- Firewall Rules
- Network Segmentation
