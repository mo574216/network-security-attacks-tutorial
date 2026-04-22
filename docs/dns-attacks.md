    ---
    title: DNS and DNS Cache Poisoning
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

    # 3. DNS and DNS Cache Poisoning

DNS is one of the most important systems in all of networking because users work with names, while protocols need IP addresses.

## 3.1 What DNS Does

DNS translates:

- hostnames to IP addresses,
- IP addresses to hostnames,
- and domain names to other essential records such as MX records for mail delivery.

DNS is simultaneously:

- a naming system,
- a distributed database,
- a protocol,
- and an operational infrastructure.

## 3.2 Why DNS is a High-Value Target

If an attacker can influence DNS, the attacker can redirect users without changing the users’ behavior.

This makes DNS attractive because:

- users still type the correct domain name,
- browsers still appear to function normally,
- and the compromise can occur “underneath” the user’s actions.

## 3.3 The Distributed Nature of DNS

DNS is hierarchical.

At a high level:
- root servers know top-level domains,
- TLD servers know authoritative servers for subdomains,
- authoritative servers know the actual records for domains.

Resolvers query the hierarchy and cache results.

This design gives DNS excellent scalability, but introduces opportunities for poisoning and hijacking.

## 3.4 DNS Cache and TTL

### Cache
Resolvers store answers locally to speed up future lookups.

### TTL
Every DNS record typically carries a **Time To Live (TTL)** value that tells the resolver how long a cached answer may be used.

### Security implication
Caching improves efficiency, but if a malicious response gets cached, many subsequent users may be redirected until the poisoned record expires.

## 3.5 Phishing versus Pharming

### Phishing
The user is tricked into visiting a malicious site, often via email or social engineering.

### Pharming
The user is redirected to a malicious site even when the user tries to visit the correct site.

DNS manipulation is a classic pharming mechanism.

## 3.6 DNS Cache Poisoning

### Core idea
In a DNS cache poisoning attack, the attacker injects a forged DNS response into a resolver’s cache.

If successful:

- future lookups return a malicious IP address,
- users are silently redirected,
- and services such as email or web browsing can be subverted.

### Why the attack works
Traditional DNS was not designed with strong built-in origin authentication for all records.  
Resolvers often accept the first response that appears to match an outstanding query.

An attacker may guess or predict:

- transaction IDs,
- source ports,
- question names,
- and timing windows.

If the forged reply arrives convincingly enough, it may be cached.

## 3.7 Kaminsky-Style Cache Poisoning

Dan Kaminsky’s attack made cache poisoning much more virulent by dramatically increasing the attacker’s number of attempts to guess the right response parameters.

### Main insight
If the attacker can trigger many resolver queries for random subdomains of a target domain, the attacker gets many chances to race legitimate responses.

This changed the risk profile of DNS poisoning and led to widespread defensive improvements.

## 3.8 DNS Hijacking More Broadly

Beyond resolver poisoning, DNS-related compromise can happen through:

- malware changing local DNS settings,
- rogue DNS servers,
- malicious rewriting of configuration files,
- ISP interference on nonexistent domains,
- or administrative compromise of DNS infrastructure.

## 3.9 Defenses

### Randomization
Defenses include:
- stronger transaction ID randomization,
- randomized source ports,
- case randomization in queries in some implementations.

### DNSSec
DNS Security Extensions provide cryptographic authentication of DNS data.

Important idea:
- DNSSec does not encrypt DNS,
- but it helps validate that data is authentic and untampered.

### Operational hygiene
- protect resolver configuration,
- restrict recursive service exposure,
- monitor unexpected changes,
- validate zones,
- minimize overly long TTLs for sensitive situations.

---

    ## Page Navigation

    [← TCP/IP, Spoofing, and DoS](tcp-ip-dos.md) | [Buffer Overflow →](buffer-overflow.md)
