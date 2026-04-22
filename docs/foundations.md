---
    title: Foundations: Why Network Attacks Work
    layout: default
---

## Tutorial Navigation
- [Foundations](/docs/foundations.md)
- [TCP/IP, Spoofing, and DoS](/docs/tcp-ip-dos.md)
- [DNS and Cache Poisoning](/docs/dns-attacks.md)
- [Buffer Overflow](/docs/buffer-overflow.md)
- [Viruses and Worms](/docs/malware.md)
- [Recon, Scanning, Sniffing, IDS](/docs/recon-scanning-ids.md)
- [Password Attacks](/docs/password-attacks.md)
- [Web Security Attacks](/docs/web-attacks.md)
- [P2P Security Issues](/docs/p2p-security.md)
- [Bots, Botnets, and DDoS](/docs/botnets-ddos.md)
- [Social Engineering and Trojans](/docs/social-engineering.md)
- [Mobile Device Security](/docs/mobile-security.md)
- [Defensive Principles](/docs/defensive-principles.md)
- [Lab Roadmap](/docs/lab-roadmap.md)
- [Review Questions](/docs/review-questions.md)


# 1. Foundations: Why Network Attacks Work

Network attacks succeed because digital systems must remain **open enough to communicate** while also being **restrictive enough to resist abuse**. Security problems emerge wherever there is a mismatch between:

- what a protocol was designed to do,
- what a program assumes will happen,
- and what an attacker can actually force it to do.

## 1.1 Security as an Adversarial Study of Systems

A secure system is not simply a system that functions correctly in normal conditions.  
It is a system that continues to behave acceptably even when:

- inputs are malformed,
- peers lie,
- packets are replayed or forged,
- memory is overwritten,
- credentials are guessed,
- users are tricked,
- or software components are chained together in unexpected ways.

For this reason, network security must always be studied from two viewpoints:

- **the engineer’s viewpoint**: how the system is intended to work,
- **the attacker’s viewpoint**: how the same mechanisms can be abused.

## 1.2 Services, Ports, and Exposure

A networked host offers functionality through **services** bound to **ports**.

Examples include:

- HTTP on port 80
- HTTPS on port 443
- SSH on port 22
- Telnet on port 23
- SMTP on port 25
- DNS on port 53

A port is not “dangerous” by itself. The risk comes from the code listening behind it.  
If that code contains:

- unsafe parsing logic,
- poor authentication,
- weak defaults,
- memory corruption bugs,
- or unnecessary privileges,

then the corresponding port becomes an entry point for attack.

## 1.3 Why Attackers Love Layered Protocol Stacks

The TCP/IP stack is layered so that systems remain modular. That is good for engineering.  
However, attackers exploit the fact that assumptions made in one layer may not be fully enforced in another.

Examples:

- IP allows source addresses to be forged in some contexts.
- TCP must allocate state during connection establishment.
- DNS depends on trust in replies and caching behavior.
- Applications often assume that lower layers already performed sufficient validation.
- Web applications trust browser-side behavior too much.
- Mobile systems rely on app isolation, store vetting, and network-side controls that are not perfect.

## 1.4 A Practical Taxonomy of Network Security Attacks

For undergraduate study, it is useful to group attacks as follows:

### Availability attacks
Attacks that try to degrade or deny service.

Examples:
- SYN flooding
- Shrew attacks
- Slowloris
- DDoS

### Confidentiality attacks
Attacks that try to steal information.

Examples:
- packet sniffing,
- cookie theft,
- phishing,
- malware,
- side-channel attacks.

### Integrity attacks
Attacks that modify data, control flow, or name resolution.

Examples:
- DNS cache poisoning,
- SQL injection,
- malware infection,
- command injection,
- buffer-overflow-based code execution.

### Authentication and authorization attacks
Attacks that bypass identity checks or abuse credentials.

Examples:
- dictionary attacks,
- spear phishing,
- credential theft,
- privilege escalation after exploitation.

---

    ## Page Navigation

    [← Home](index.md) | [TCP/IP, Spoofing, and DoS →](tcp-ip-dos.md)
