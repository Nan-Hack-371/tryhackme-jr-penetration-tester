# Active Reconnaissance

> **Room:** Network Security – Active Reconnaissance  
> **Platform:** TryHackMe

---

# Introduction

Active reconnaissance is the process of **directly interacting** with a target system or network to gather information.

Unlike passive reconnaissance, active reconnaissance sends packets, establishes connections, and probes services, making it **detectable** by the target.

---

# Passive vs Active Reconnaissance

## Passive Reconnaissance

- Collects information from public sources.
- Does **not** send traffic to the target.
- Very difficult for the target to detect.
- Used during the initial intelligence-gathering phase.

### Examples

- WHOIS
- DNS Records
- Certificate Transparency Logs
- Shodan
- Search Engines
- Public GitHub repositories

---

## Active Reconnaissance

- Directly communicates with the target.
- Sends packets and network requests.
- Can generate logs, IDS alerts, WAF events, and firewall logs.
- Requires authorization before performing.

### Examples

- Visiting websites
- Ping
- Traceroute
- Port scanning
- Banner grabbing
- Directory brute forcing
- Service enumeration

---

# Key Differences

| Passive | Active |
|----------|---------|
| No direct interaction | Direct interaction |
| Stealthy | Detectable |
| No packets sent | Packets are transmitted |
| Public information only | Live system information |
| Low legal risk | Requires authorization |

---

# Why Active Recon Matters

Active reconnaissance reveals information that passive techniques cannot discover.

It helps identify:

- Live hosts
- Running services
- Open ports
- Network path
- Operating system hints
- Software banners
- Hidden web functionality

---

# Risks

Active reconnaissance leaves evidence such as:

- Firewall logs
- IDS alerts
- SIEM events
- WAF detections
- Honeypot triggers

---

# Modern Security Challenges

Modern organizations commonly use:

- Cloudflare
- Akamai
- Zero Trust Networks
- WAFs
- SIEM
- EDR
- CDN protection

These technologies detect reconnaissance attempts much faster than before.

---

# Legal Warning

Never perform active reconnaissance without:

- Written authorization
- Penetration testing contract
- Bug bounty scope

Unauthorized probing may be illegal.

---

# Learning Objectives

After completing this room you should be able to:

- Use browser Developer Tools for reconnaissance.
- Use Ping to test host availability.
- Use Traceroute to map network paths.
- Perform banner grabbing using Telnet.
- Use Netcat for banner grabbing and communication.

---

# Web Browser Reconnaissance

A web browser is one of the safest active reconnaissance tools because browsing appears like normal user activity.

---

## Default Ports

| Protocol | Port |
|-----------|------|
| HTTP | 80 |
| HTTPS | 443 |
| HTTP/3 (QUIC) | UDP 443 |

---

## Accessing Non-Standard Ports

Example:

```text
https://target.com:8443

http://192.168.1.100:8080
```

---

# Developer Tools

Shortcut:

**Windows/Linux**

```text
Ctrl + Shift + I
```

**macOS**

```text
Option + Command + I
```

---

## Network Tab

Useful for viewing:

- Request headers
- Response headers
- Cookies
- Status codes
- Timing
- CSP
- Server headers
- X-Powered-By

---

## Console Tab

Used to:

- Execute JavaScript
- Inspect DOM
- Debug scripts

---

## Sources Tab

One of the most useful tabs.

Look for:

- Hidden API endpoints
- Internal URLs
- JavaScript files
- Comments
- Secret paths
- Hardcoded keys
- Backend routes

---

## Application Tab

Inspect:

- Cookies
- Local Storage
- Session Storage

Possible findings:

- Session tokens
- API keys
- Authentication data
- Tracking IDs

---

## Security Tab

Provides certificate information:

- Certificate issuer
- Expiration date
- SAN (Subject Alternative Names)

SANs often reveal additional subdomains.

---

# Useful Browser Extensions

## FoxyProxy

Purpose:

- Switch proxies
- Burp Suite
- OWASP ZAP
- SOCKS5

---

## User-Agent Switcher

Allows changing:

- Browser
- OS
- Device type

Useful for discovering:

- Mobile endpoints
- Browser-specific behavior

---

## Wappalyzer

Identifies:

- CMS
- Frameworks
- JavaScript libraries
- CDN
- Analytics
- Web server
- Database

---

## Other Useful Extensions

- BuiltWith
- WhatRuns
- Library Detector

---

# Ping

Ping checks whether a host is reachable.

Uses:

**ICMP Echo Request**

↓

**ICMP Echo Reply**

---

# Basic Syntax

Linux/macOS

```bash
ping -c 5 IP
```

Windows

```cmd
ping -n 5 IP
```

IPv4 only

```bash
ping -4 IP
```

IPv6

```bash
ping -6 IP
```

---

# Successful Ping

Shows:

- Target is alive
- RTT (Round Trip Time)
- TTL
- Packet loss

Example:

```text
5 transmitted
5 received
0% packet loss
```

---

# TTL (Time To Live)

TTL decreases by **1** at every router.

Common initial values:

| OS | Initial TTL |
|----|-------------|
| Linux | 64 |
| Windows | 128 |

Example:

TTL 58

↓

Linux host approximately **6 hops away**

---

# Common Ping Results

## Success

- Host online
- ICMP allowed

---

## Destination Host Unreachable

Possible reasons:

- Host down
- Wrong route
- Network issue

---

## 100% Packet Loss

Possible reasons:

- Firewall blocks ICMP
- Cloud filtering
- WAF
- Host unreachable

---

## High Latency

Possible causes:

- Congestion
- Long-distance routing
- Packet filtering

---

# Traceroute

Traceroute discovers the path packets take to reach a destination.

Shows:

- Routers (Hops)
- Network path
- Delay
- Packet loss

---

# Commands

Linux

```bash
traceroute IP
```

Windows

```cmd
tracert IP
```

IPv6

```bash
traceroute -6 IP
```

---

# How Traceroute Works

Uses TTL.

TTL starts at:

```
1
```

Router 1 decrements TTL to 0.

↓

Returns:

```
ICMP Time Exceeded
```

Then TTL becomes:

```
2
```

Router 2 replies.

This repeats until reaching the destination.

---

# Output

Each numbered line represents:

- One network hop

Each hop usually sends:

- Three probes

Example:

```
1
2
3
...
14
```

Hop 14 = Destination

---

# Why Routes Change

Routes may differ because of:

- Dynamic routing
- BGP
- OSPF
- Load balancing
- Anycast
- CDN optimization

---

# Useful Traceroute Modes

TCP mode

```bash
traceroute -T IP
```

ICMP mode

```bash
traceroute -I IP
```

Continuous monitoring

```bash
mtr IP
```

---

# Telnet

Telnet is an old protocol used for remote command-line access.

Default Port:

```
23
```

---

# Why Telnet is Insecure

- No encryption
- Passwords sent in plaintext
- Easily intercepted

Modern replacement:

- SSH

---

# Banner Grabbing using Telnet

Connect:

```bash
telnet IP 80
```

Send:

```http
GET / HTTP/1.1
Host: example
```

Example response:

```http
Server: nginx/1.6.2
```

Useful for identifying:

- Web server
- Software version

---

# Banner Grabbing on Other Services

FTP

```text
Port 21
```

SMTP

```text
Port 25
```

POP3

```text
Port 110
```

Many services immediately display a banner after connection.

---

# HTTPS Alternative

Telnet cannot communicate over TLS.

Instead use:

```bash
curl --head https://IP
```

or

```bash
openssl s_client -connect IP:443
```

---

# Netcat (nc)

Netcat is a powerful networking utility.

Supports:

- TCP
- UDP
- IPv6
- SSL (ncat)

Uses:

- Banner grabbing
- Port probing
- File transfer
- Client-server communication

---

# Banner Grabbing using Netcat

Connect:

```bash
nc IP 80
```

Send:

```http
GET / HTTP/1.1
Host: netcat
```

Example:

```http
Server: nginx/1.6.2
```

---

# Listening with Netcat

Server:

```bash
nc -vnlp 1234
```

Client:

```bash
nc IP 1234
```

Both systems can exchange messages.

---

# Netcat Options

| Option | Purpose |
|---------|----------|
| -l | Listen mode |
| -p | Specify port |
| -n | No DNS lookup |
| -v | Verbose |
| -vv | Very verbose |
| -k | Keep listening |
| -6 | IPv6 |

---

# Netcat SSL

Encrypted communication:

```bash
ncat --ssl
```

---

# Quick Command Reference

## Ping

```bash
ping -c 5 IP
```

---

## IPv4 Ping

```bash
ping -4 IP
```

---

## IPv6 Ping

```bash
ping -6 IP
```

---

## Traceroute

```bash
traceroute IP
```

---

## TCP Traceroute

```bash
traceroute -T IP
```

---

## ICMP Traceroute

```bash
traceroute -I IP
```

---

## MTR

```bash
mtr IP
```

---

## Telnet

```bash
telnet IP 80
```

---

## Netcat Banner Grabbing

```bash
nc IP 80
```

---

## Netcat Listener

```bash
nc -vnlp 1234
```

---

## Netcat Client

```bash
nc IP 1234
```

---

## HTTPS Banner

```bash
curl --head https://IP
```

---

## SSL Connection

```bash
openssl s_client -connect IP:443
```

---

# Key Takeaways

- Active reconnaissance directly interacts with the target.

- It reveals live hosts, services, software versions, and network paths.

- Browsers are useful for collecting headers, JavaScript files, cookies, storage data, and certificates.

- Ping checks host availability and provides TTL information.

- Traceroute maps the path packets travel through the network.

- Telnet demonstrates banner grabbing on unencrypted TCP services.

- Netcat is a modern, flexible alternative for banner grabbing, communication, and port interaction.

- Active reconnaissance is detectable and should only be performed with proper authorization.
