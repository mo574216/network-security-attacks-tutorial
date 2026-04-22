---
title: "Cross-Cutting Defensive Principles"
layout: default
nav_order: 14
permalink: "/docs/defensive-principles/"
---

# 13. Cross-Cutting Defensive Principles

This section ties the attack modules together.

## 13.1 Reduce Attack Surface

- disable unused services,
- close unnecessary ports,
- remove legacy software,
- avoid needless exposure to the public internet.

## 13.2 Patch Aggressively

Many famous attacks succeed because systems remain unpatched long after vulnerabilities are known.

## 13.3 Enforce Strong Authentication

- MFA where possible,
- hardened SSH policies,
- strong password hashing,
- account lockout and throttling,
- secure recovery workflows.

## 13.4 Design for Least Privilege

A compromise should not automatically become total system compromise.

## 13.5 Validate Inputs Everywhere

This applies to:
- packets,
- protocol fields,
- application parameters,
- database inputs,
- file uploads,
- and browser-generated data.

## 13.6 Log, Monitor, and Correlate

Logs are critical for:
- detecting dictionary attacks,
- recognizing scans,
- identifying unusual DNS behavior,
- spotting malware spread,
- and understanding post-incident timelines.

## 13.7 Use Defense in Depth

No single control is enough.

Examples:
- BCP 38 + SYN defenses + IDS + patching
- DNSSec + resolver hardening + endpoint hygiene
- secure coding + canaries + ASLR + sandboxing
- user awareness + MFA + email filtering + endpoint controls

## 13.8 Teach Ethical Boundaries Clearly

Students must understand:
- scanning and exploitation are legal only with authorization,
- proof-of-concept work belongs in isolated labs,
- and security education must strengthen defense rather than enable harm.
---

## Page Navigation

[← Mobile Device Security Vulnerabilities]({{ '/docs/mobile-security/' | relative_url }}) | [Suggested Lab Roadmap →]({{ '/docs/lab-roadmap/' | relative_url }})

