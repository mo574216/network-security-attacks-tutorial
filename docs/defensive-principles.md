    ---
    title: Cross-Cutting Defensive Principles
    layout: default
    ---

    [Home](index.md) | [Glossary](glossary.md)

    ## Tutorial Navigation

    - [Foundations](foundations.md)
- [TCP/IP, Spoofing, and DoS](tcp-ip-dos.md)
- [DNS and Cache Poisoning](dns-attacks.md)
- [Buffer Overflow](buffer-overflow.md)
- [Viruses and Worms](malware.md)
- [Recon, Scanning, Sniffing, IDS](recon-scanning-ids.md)
- [Password Attacks](password-attacks.md)
- [Web Security Attacks](web-attacks.md)
- [P2P Security Issues](p2p-security.md)
- [Bots, Botnets, and DDoS](botnets-ddos.md)
- [Social Engineering and Trojans](social-engineering.md)
- [Mobile Device Security](mobile-security.md)
- [Defensive Principles](defensive-principles.md)
- [Lab Roadmap](lab-roadmap.md)
- [Review Questions](review-questions.md)

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

    [← Mobile Device Security](mobile-security.md) | [Lab Roadmap →](lab-roadmap.md)
