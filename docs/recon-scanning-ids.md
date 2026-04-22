---
title: "Reconnaissance, Scanning, Sniffing, IDS, and Penetration Testing"
layout: default
nav_order: 7
permalink: "/docs/recon-scanning-ids/"
---

# 6. Reconnaissance, Scanning, Sniffing, IDS, and Penetration Testing

Before exploitation, attackers often need visibility.

## 6.1 Port Scanning

Port scanning tries to determine which ports on a target are:

- open,
- closed,
- or filtered.

### Why attackers scan
Open ports reveal:
- reachable services,
- probable operating systems,
- application versions,
- and possible vulnerability classes.

### Why defenders scan
Defenders scan to discover:
- unintended exposure,
- forgotten services,
- risky configurations,
- or drift from security baselines.

## 6.2 connect() Scans

A simple scan can use normal TCP connection attempts.

If the connection succeeds:
- the port is open.

If it fails:
- the port may be closed or blocked.

This method is straightforward but noisy because it completes full connections.

## 6.3 SYN Scans

A SYN scan sends SYN packets and infers port state based on responses.

Typical outcomes:
- **SYN+ACK** → port appears open
- **RST** → port appears closed
- **no response** → filtered or blocked

SYN scanning is efficient and widely used.

## 6.4 Nmap

**nmap** is a foundational scanning tool.

It supports:
- host discovery,
- SYN scans,
- connect() scans,
- UDP scans,
- version detection,
- OS fingerprinting,
- script-based probing.

### Teaching objective
Students should understand not only how to run nmap, but what nmap is inferring from protocol behavior.

## 6.5 Vulnerability Scanning

A vulnerability scanner goes beyond port state and tries to determine:

- which services are running,
- what versions they are,
- what known weaknesses may apply.

### Nessus
Nessus is a widely used vulnerability scanner.

It illustrates a broader lesson:
- enumeration and vulnerability knowledge are tightly linked.

## 6.6 Packet Sniffing

Packet sniffing captures traffic for analysis.

### Uses
- troubleshooting,
- security monitoring,
- learning protocol behavior,
- incident response,
- credential and session analysis in insecure setups.

### Tools
- **tcpdump**
- **Wireshark**

### Security significance
If data is sent in plaintext, sniffing can expose:
- credentials,
- cookies,
- command strings,
- email contents,
- and operational details.

## 6.7 Intrusion Detection

An IDS monitors activity for signs of malicious behavior.

### Snort
Snort is a classic IDS/IPS tool.

It teaches:
- signature-based detection,
- traffic inspection,
- alerting rules,
- and the limits of pattern-based detection.

## 6.8 Penetration Testing and Metasploit

Penetration testing evaluates security by ethically simulating attacks in authorized environments.

### Metasploit
Metasploit supports:
- exploit testing,
- payload selection,
- proof-of-concept validation,
- and security research.

### Educational message
Students must understand both:
- how attacks are chained together,
- and why pentesting must be bounded by scope, authorization, and ethics.

## 6.9 Netcat

Netcat is a versatile utility for:

- opening raw connections,
- testing ports,
- transferring data,
- scripting simple interactions,
- and understanding client-server behavior.

It is useful both offensively and defensively.
---

## Page Navigation

[← Malware: Viruses and Worms]({{ '/docs/malware/' | relative_url }}) | [Password Attacks: Dictionary and Rainbow-Table Attacks →]({{ '/docs/password-attacks/' | relative_url }})

