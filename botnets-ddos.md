    ---
    title: Bots, Botnets, and DDoS
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

    # 10. Bots, Botnets, and DDoS

## 10.1 What is a Bot?

A bot is malware that can receive or retrieve commands from a human operator or command structure.

Unlike a simple virus or worm, a bot often has a richer set of behaviors and is part of a coordinated system.

## 10.2 Botnets

A **botnet** is a collection of bots controlled in a coordinated way.

### Why botnets are powerful
They allow attackers to distribute:
- spam,
- scanning,
- credential abuse,
- malware delivery,
- and DDoS attacks.

## 10.3 Command and Control (C&C)

Bots need a way to receive instructions.

Two classic models:

### Push model
Commands are pushed from a C&C server to bots.
- IRC has historically been important here.

### Pull model
Bots periodically ask for new commands.
- HTTP/HTTPS infrastructure often supports this.

### Why standard protocols help attackers
Using normal-looking traffic makes malicious control traffic harder to distinguish.

## 10.4 DDoS

A Distributed Denial of Service attack uses many distributed sources to overwhelm or degrade a target.

### Targets
- bandwidth,
- connection tables,
- CPU,
- application threads,
- caches,
- or upstream infrastructure.

### Why distributed attacks are hard
Defenders must distinguish malicious from legitimate traffic when both may be geographically widespread.

## 10.5 Amplification and Reflection

Some DDoS methods multiply attacker effort through protocol properties or third-party systems.

The fundamental lesson is that asymmetric cost is central to many network attacks.

## 10.6 Mitigation Strategies

### Architectural defenses
- multi-layer switching,
- content delivery networks,
- load distribution,
- geographic dispersion.

### Routing-based responses
- manual BGP reconfiguration,
- scrubbing centers,
- upstream filtering.

### Host and service defenses
- rate limiting,
- connection handling improvements,
- anomaly detection,
- service degradation modes,
- and rapid isolation of abusive patterns.

## 10.7 Mirai and IoT Exploitation

The Mirai botnet highlighted how poorly secured IoT devices such as webcams can be turned into large-scale DDoS infrastructure.

### Key lesson
Weak embedded security can create massive systemic risk far beyond the value of each individual device.

---

    ## Page Navigation

    [← P2P Security Issues](p2p-security.md) | [Social Engineering and Trojans →](social-engineering.md)
