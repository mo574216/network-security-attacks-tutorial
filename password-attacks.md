    ---
    title: Password Attacks: Dictionary and Rainbow-Table Attacks
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

    # 7. Password Attacks: Dictionary and Rainbow-Table Attacks

Password-protected systems often fail not because cryptography is broken, but because passwords and authentication workflows are weak.

## 7.1 Dictionary Attacks

A dictionary attack tries likely usernames and likely passwords rather than exhaustively checking all possibilities.

### Why it works
Users choose predictable names and passwords.

Common targets:
- root
- admin
- guest
- test
- oracle
- postgres
- webmaster
- webadmin

### Operational evidence
Authentication logs often show repeated login attempts against SSH and other exposed services.

## 7.2 SSH as a High-Value Target

Port 22 is a common focus because SSH is widely used for:

- remote login,
- administration,
- secure transfer,
- and machine-to-machine access.

A successful SSH compromise often leads directly to system control.

## 7.3 Brute Force versus Dictionary

### Brute force
Tries all possibilities.

### Dictionary attack
Tries likely possibilities first.

Dictionary attacks are more practical over networks because the search space is smaller and more realistic.

## 7.4 Log-Based Detection and Throttling

Defenders can use:

- authentication logs,
- repeated failure counters,
- geo-pattern anomalies,
- and blocking tools such as Fail2Ban-style responses.

The broader lesson is that **telemetry is part of defense**.

## 7.5 Password Storage and Hashing

Passwords should never be stored in plaintext.

Instead, systems store password hashes.  
However, weak hashing schemes or unsalted hashes can still be attacked effectively.

## 7.6 Direct Table Lookup

If attackers possess precomputed mappings from passwords to hashes, they may recover passwords rapidly via lookup.

## 7.7 Hash Chains and Rainbow Tables

Rainbow tables are a storage-optimized technique for inverting hash functions across likely password spaces.

### Why rainbow tables matter pedagogically
They show that:
- even one-way hashing is not enough by itself,
- precomputation changes the attack economics,
- and salts are essential.

## 7.8 Proper Defenses

- unique salts per password,
- slow password hashing functions,
- MFA,
- login throttling,
- anomaly detection,
- strong password policies,
- and secure administrative defaults.

---

    ## Page Navigation

    [← Recon, Scanning, Sniffing, IDS](recon-scanning-ids.md) | [Web Security Attacks →](web-attacks.md)
