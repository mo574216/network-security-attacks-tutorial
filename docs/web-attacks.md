---
title: "Web Security Attacks"
layout: default
nav_order: 9
permalink: "/docs/web-attacks/"
---

# 8. Web Security Attacks

Web security deserves its own major section because modern attacks frequently target applications, browsers, cookies, sessions, and databases.

## 8.1 What Web Security Includes

Web security covers more than transport confidentiality.  
It includes:

- how servers generate content,
- how browsers execute it,
- how state is stored,
- how user input is handled,
- and how attackers abuse the interaction between browser, server, and database.

## 8.2 PHP and Server-Side Execution Risks

Server-side languages such as PHP are powerful because they can:

- generate dynamic content,
- access databases,
- invoke system functions,
- and combine user input with backend operations.

That same flexibility is dangerous when validation is poor.

## 8.3 Server-Side Injection and Command Abuse

If a web application passes user-controlled input into system commands or unsafe handlers, attackers may:

- execute arbitrary commands,
- send spam,
- alter files,
- or pivot deeper into the system.

## 8.4 SQL Injection

SQL injection occurs when user input is inserted into database queries without proper separation between code and data.

### Consequences
- unauthorized data retrieval,
- authentication bypass,
- data modification,
- database enumeration,
- or complete application compromise.

### Why it happens
Applications mistakenly trust input or build queries through string concatenation.

### Defenses
- parameterized queries,
- stored procedures used correctly,
- validation,
- least privilege for database accounts,
- and careful error handling.

## 8.5 Slowloris

Slowloris is an application-layer denial-of-service attack against web servers.

### Basic idea
The attacker opens many HTTP connections but sends requests very slowly, keeping them incomplete.

### Effect
The server keeps resources reserved for these partial requests, eventually starving legitimate clients.

### Why it matters
Slowloris shows that:
- DDoS is not always about bandwidth,
- application-layer behavior matters,
- and protocol compliance can still be malicious.

## 8.6 mod_security and Web Server Protection

Protective layers for web servers include:

- reverse proxies,
- rate limiting,
- request normalization,
- WAF rules,
- timeout tuning,
- and modules such as **mod_security**.

## 8.7 Cross-Site Scripting (XSS)

XSS allows attacker-controlled script to execute in a victim’s browser.

### Server-side XSS context
A server stores or reflects malicious content that later runs in users’ browsers.

### Client-side XSS / browser-side exploitation
A victim may be lured into clicking a crafted URL that causes injected JavaScript to execute.

### Why XSS matters
Once malicious script runs in the victim’s browser, it may:
- steal cookies,
- manipulate the DOM,
- trigger background requests,
- rewrite content,
- or load further malicious code.

## 8.8 Cookies and Session Theft

Cookies often carry session identifiers or other state.

If script can read them and exfiltrate them, attackers may hijack sessions.

### Defensive themes
- HttpOnly,
- Secure flag,
- SameSite,
- output encoding,
- content security policy,
- and strict input handling.

## 8.9 Heap Spray and Browser Exploitation

Heap spraying is a browser exploitation strategy in which memory is filled with repeated attacker-controlled patterns to increase the chance that redirected execution lands on useful payload bytes.

It is a bridge topic between web security and memory corruption.
---

## Page Navigation

[← Password Attacks: Dictionary and Rainbow-Table Attacks]({{ '/docs/password-attacks/' | relative_url }}) | [P2P Security Issues →]({{ '/docs/p2p-security/' | relative_url }})

