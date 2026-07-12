# Cyber Kill Chain

> **Platform:** TryHackMe
> **Room:** Cyber Kill Chain
> **Category:** Penetration Testing Foundations

---

# Overview

The **Cyber Kill Chain** is a framework developed by Lockheed Martin that describes the stages an attacker follows during a cyber attack.

Understanding each phase helps defenders detect, disrupt, and prevent attacks before they reach their objective.

---

# Cyber Kill Chain

```
Reconnaissance
      ↓
Weaponisation
      ↓
Delivery
      ↓
Exploitation
      ↓
Installation
      ↓
Command & Control (C2)
      ↓
Actions on Objectives
```

---

# 1. Reconnaissance

## Definition

Reconnaissance is the information gathering phase where attackers collect intelligence about a target before launching an attack.

---

## Types

### Passive Reconnaissance

Performed without directly interacting with the target.

Examples

* WHOIS lookup
* DNS enumeration
* Google Dorking
* Social media research
* Website crawling
* Public document analysis
* OSINT

---

### Active Reconnaissance

Requires interaction with the target.

Examples

* Port scanning
* Service enumeration
* Vulnerability scanning
* Network mapping
* Social engineering
* Physical reconnaissance

---

## Common Tools

* Nmap
* WHOIS
* nslookup
* dig
* theHarvester
* Amass
* Shodan
* Maltego

---

## Goal

* Identify technologies
* Discover attack surface
* Find exposed services
* Gather employee information
* Locate public assets

---

## Defensive Measures

* Minimise public information
* WHOIS privacy protection
* Secure DNS records
* Network monitoring
* Log analysis
* IDS/IPS
* Detect scanning activity

---

# 2. Weaponisation

## Definition

The attacker creates or modifies a payload capable of exploiting the identified weakness.

---

## Examples

* Malware
* Malicious Office documents
* PDF exploits
* Exploit kits
* Ransomware
* Weaponised USB devices
* Web shells

---

## Common Techniques

* Payload obfuscation
* Encryption
* Macro-enabled documents
* Custom exploit development

---

## Goal

Prepare a payload that bypasses security controls and successfully exploits the target.

---

## Defensive Measures

* User awareness training
* Disable Office macros
* Remove unnecessary software
* Restrict browser plugins
* Application allowlisting

---

# 3. Delivery

## Definition

The payload is delivered to the victim.

---

## Common Delivery Methods

### Email

* Phishing
* Spear phishing
* Malicious attachments

---

### Web

* Malicious websites
* Drive-by downloads
* URL shortening
* Domain spoofing

---

### Social Engineering

* Fake technical support
* Fake software updates
* Impersonation attacks

---

### Physical

* USB drives
* DVDs
* External storage devices

---

### Mobile

* Smishing (SMS phishing)

---

### Advertising

* Malvertising

---

## Goal

Convince the victim to execute or access the payload.

---

## Defensive Measures

* Email filtering
* Web filtering
* User awareness
* WAF
* Patch management
* Network monitoring

---

# 4. Exploitation

## Definition

The attacker exploits a vulnerability to gain access to the target.

---

## Common Exploitation Methods

* Weak passwords
* Default credentials
* SQL Injection
* XSS
* CSRF
* Buffer Overflow
* Remote Code Execution
* Zero-Day exploits

---

## Goal

Execute malicious code or obtain unauthorized access.

---

## Defensive Measures

* Strong password policies
* Multi-Factor Authentication
* Regular patching
* Vulnerability scanning
* Intrusion Prevention Systems
* Web Application Firewall

---

# 5. Installation

## Definition

The attacker establishes persistence to maintain long-term access.

---

## Common Techniques

* Scheduled tasks
* Cron jobs
* Startup scripts
* Backdoors
* Rootkits
* Malware installation
* Web shells
* LOLBins (Living Off the Land Binaries)

---

## Goal

Maintain access even after system reboot.

---

## Defensive Measures

* Endpoint Detection and Response (EDR)
* Process monitoring
* File integrity monitoring
* Application allowlisting
* Regular security audits
* Baseline comparison

---

# 6. Command and Control (C2)

## Definition

The compromised system communicates with the attacker's infrastructure to receive commands and send data.

---

## Common Communication Channels

* HTTP
* HTTPS
* DNS
* SMTP

---

## C2 Techniques

### DNS Tunneling

Encodes attacker communication inside DNS requests.

---

### HTTPS Communication

Encrypts traffic to evade monitoring.

---

### Cloud Services

Uses trusted platforms such as cloud storage or document services.

---

### Social Media

Uses messaging platforms for command delivery.

---

### Domain Generation Algorithm (DGA)

Automatically generates thousands of domains, making blocking difficult.

---

### Fast Flux

Frequently changes IP addresses associated with a single domain to improve resilience.

---

## Goal

Maintain stealthy communication between attacker and victim.

---

## Defensive Measures

* Firewall monitoring
* IDS/IPS
* DNS monitoring
* HTTPS inspection
* Content filtering
* Threat intelligence
* Honeypots
* Block known malicious domains

---

# 7. Actions on Objectives

## Definition

The attacker performs the final objective after successfully compromising the environment.

---

## Examples

* Data theft
* Credential dumping
* Privilege escalation
* Lateral movement
* Ransomware deployment
* Financial fraud
* Data destruction
* Business disruption

---

## Goal

Achieve the intended mission.

---

## Defensive Measures

* Least privilege
* Network segmentation
* Data Loss Prevention (DLP)
* Security monitoring
* Incident response
* Backup strategy
* Continuous logging

---

# Complete Attack Flow

```
Reconnaissance
      ↓
Information Gathering

Weaponisation
      ↓
Payload Creation

Delivery
      ↓
Payload Transmission

Exploitation
      ↓
Gain Initial Access

Installation
      ↓
Persistence

Command & Control
      ↓
Remote Communication

Actions on Objectives
      ↓
Mission Complete
```

---

# Key Takeaways

* Every cyber attack follows a structured process.
* Breaking the attack chain at any stage can prevent a successful compromise.
* Reconnaissance is often the most critical stage because it determines the success of later phases.
* Defense in depth is essential since no single security control can stop every stage.
* Monitoring, patching, user awareness, and continuous detection significantly reduce attack success.

---

# Skills Learned

* Cyber Kill Chain methodology
* Reconnaissance techniques
* Payload weaponisation
* Delivery methods
* Exploitation concepts
* Persistence mechanisms
* Command & Control infrastructure
* Defensive countermeasures
* Attack lifecycle analysis

---

# References

* Lockheed Martin Cyber Kill Chain
* MITRE ATT&CK Framework
* TryHackMe – Cyber Kill Chain
