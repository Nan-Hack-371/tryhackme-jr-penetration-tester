#  Passive Reconnaissance

## Overview

This room introduces **Passive Reconnaissance**, the first phase of a network security assessment. Passive reconnaissance focuses on gathering publicly available information about a target **without directly interacting with its infrastructure**.

Unlike active reconnaissance, passive techniques leave little to no trace on the target and are commonly used during penetration testing, bug bounty hunting, and threat intelligence.

---

# Learning Objectives

After completing this room, I learned how to:

* Understand the difference between passive and active reconnaissance.
* Collect publicly available information about a target.
* Query WHOIS and RDAP records.
* Perform DNS lookups using `dig` and `nslookup`.
* Enumerate subdomains using passive OSINT sources.
* Gather information about internet-facing assets using Shodan.
* Build a passive reconnaissance methodology.

---

# Passive vs Active Reconnaissance

## Passive Reconnaissance

### Description

Passive reconnaissance gathers information **without sending traffic directly to the target**.

### Characteristics

* Low risk
* Difficult to detect
* Uses public information
* No interaction with target systems

### Common Techniques

* WHOIS lookups
* RDAP queries
* DNS record enumeration
* Certificate Transparency logs
* DNSDumpster
* Shodan
* Censys
* GitHub OSINT
* Public search engines
* Job postings and public documentation

---

## Active Reconnaissance

### Description

Active reconnaissance involves directly interacting with the target infrastructure.

### Characteristics

* Detectable
* Higher legal risk
* Generates network traffic
* May trigger IDS/IPS alerts

### Common Techniques

* Nmap scanning
* Port scanning
* Service enumeration
* Directory brute forcing
* API testing
* Web fuzzing
* Social engineering
* Physical security testing

---

# WHOIS & RDAP

## Purpose

WHOIS provides domain registration information maintained by domain registrars.

Modern systems are transitioning to **RDAP (Registration Data Access Protocol)**, which returns structured JSON over HTTPS.

---

## Information Collected

* Domain registrar
* Registration date
* Last updated date
* Expiration date
* Domain status
* Name servers
* Abuse contact
* Registrant information (when available)

---

## Commands

### WHOIS

```bash
whois tryhackme.com
```

### RDAP

```bash
curl -s https://rdap.verisign.com/com/v1/domain/tryhackme.com | jq .
```

---

## Why It Matters

WHOIS and RDAP help identify:

* Domain ownership
* Infrastructure providers
* DNS servers
* Domain age
* Registration history
* Potential attack surface

---

# DNS Enumeration

## Purpose

DNS queries reveal how a domain is configured without interacting directly with the target application.

---

## Common DNS Record Types

| Record | Purpose                        |
| ------ | ------------------------------ |
| A      | IPv4 Address                   |
| AAAA   | IPv6 Address                   |
| MX     | Mail Servers                   |
| TXT    | SPF, DKIM, DMARC, Verification |
| CNAME  | Domain Alias                   |
| SOA    | Zone Information               |

---

## nslookup

### Query A Record

```bash
nslookup -type=A tryhackme.com
```

### Query MX Record

```bash
nslookup -type=MX tryhackme.com
```

### Query TXT Record

```bash
nslookup -type=TXT tryhackme.com
```

---

## dig

### Query A Record

```bash
dig tryhackme.com A
```

### Query MX Record

```bash
dig @1.1.1.1 tryhackme.com MX
```

### Query TXT Record

```bash
dig tryhackme.com TXT
```

---

## Why dig is Preferred

* Cleaner output
* Displays TTL values
* Better for scripting
* More reliable
* Supports advanced queries

---

# Passive Subdomain Enumeration

## Purpose

Subdomains often expose:

* Development servers
* Test environments
* APIs
* Admin panels
* Forgotten applications

Passive techniques discover these without contacting the target.

---

# DNSDumpster

## Description

DNSDumpster collects publicly available DNS information to identify subdomains and related infrastructure.

---

## Information Collected

* Subdomains
* Host IPs
* MX records
* TXT records
* CNAME records
* Network relationships
* Visual infrastructure maps

---

## Benefits

* Completely passive
* Easy visualization
* Useful for infrastructure mapping

---

# Certificate Transparency Logs (crt.sh)

## Description

Certificate Transparency logs record publicly issued SSL/TLS certificates.

Searching these logs often reveals additional subdomains.

---

## Search Pattern

```text
%.example.com
```

---

## Advantages

* Real-time information
* Large subdomain coverage
* Completely passive
* No rate limits for basic searches

---

## Other Passive Sources

* SecurityTrails
* Subfinder
* Censys
* DNSDumpster

---

# Shodan

## Purpose

Shodan is a search engine for internet-connected devices.

Instead of web pages, it indexes:

* Servers
* Routers
* Cameras
* Firewalls
* Industrial devices
* IoT systems

---

## Information Available

* Public IP address
* Hosting provider
* ASN
* Geographic location
* Open ports
* Running services
* Software versions
* HTTP banners
* SSL information

---

## Useful Search Filters

```text
hostname:example.com
```

```text
org:"Company Name"
```

```text
port:443
```

```text
country:US
```

---

# Tools Used

* WHOIS
* RDAP
* dig
* nslookup
* DNSDumpster
* crt.sh
* Shodan
* Censys

---

# Passive Recon Workflow

```text
Target Domain
      │
      ▼
WHOIS / RDAP
      │
      ▼
DNS Enumeration
      │
      ▼
Subdomain Discovery
      │
      ▼
Certificate Transparency
      │
      ▼
Shodan / Censys
      │
      ▼
Build Target Profile
```

---

# Quick Command Reference

## WHOIS

```bash
whois tryhackme.com
```

---

## RDAP

```bash
curl -s https://rdap.verisign.com/com/v1/domain/example.com | jq .
```

---

## DNS A Record

```bash
dig example.com A
```

---

## DNS MX Record

```bash
dig @1.1.1.1 example.com MX
```

---

## DNS TXT Record

```bash
dig example.com TXT
```

---

## Legacy DNS Lookup

```bash
nslookup -type=A example.com
```

---

# Key Takeaways

* Passive reconnaissance is the safest stage of information gathering.
* Public information can reveal valuable intelligence without interacting with the target.
* WHOIS and RDAP provide domain registration details.
* DNS records expose important infrastructure information.
* Certificate Transparency logs are one of the best passive sources for discovering subdomains.
* Shodan helps identify exposed internet-facing services.
* Passive reconnaissance forms the foundation for later active testing.

---

# Skills Practiced

* Open Source Intelligence (OSINT)
* Passive Reconnaissance
* Domain Enumeration
* DNS Analysis
* Infrastructure Mapping
* Subdomain Discovery
* Internet Asset Discovery
* Information Gathering
* Reconnaissance Methodology
