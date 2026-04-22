    ---
    title: Suggested Lab Roadmap
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

    # 14. Suggested Lab Roadmap

Below is a safe, educational progression for an undergraduate course.

## Lab 1: Observe TCP state and handshake behavior
- Capture packets for a normal TCP handshake.
- Identify SYN, SYN+ACK, ACK.
- Compare successful and failed connections.

## Lab 2: Basic network scanning
- Use nmap on an isolated VM network.
- Compare SYN scan and connect() scan results.
- Explain what “open”, “closed”, and “filtered” mean.

## Lab 3: Packet sniffing and protocol visibility
- Capture DNS, HTTP, HTTPS, and ICMP traffic.
- Compare plaintext visibility with encrypted traffic.

## Lab 4: DNS behavior and caching
- Use dig, host, and nslookup.
- Measure TTL effects.
- Explain how cache poisoning would conceptually work.

## Lab 5: Authentication logging
- Examine SSH authentication logs.
- Identify repeated failed login patterns.
- Design simple alert rules.

## Lab 6: Web input validation
- Build a toy unsafe query.
- Refactor it into a parameterized version.
- Demonstrate why SQL injection works conceptually.

## Lab 7: XSS fundamentals
- Use a deliberately vulnerable sandbox app.
- Observe reflected output.
- Fix the issue using output encoding and input constraints.

## Lab 8: IDS rule thinking
- Inspect a Snort rule set.
- Map rules to observable traffic behaviors.
- Discuss false positives and false negatives.

## Lab 9: Botnet traffic reasoning
- Compare centralized client-server traffic with periodic beaconing behavior.
- Identify what might make HTTP-based C&C hard to detect.

## Lab 10: Mobile threat models
- Create attacker models for:
  - a normal smartphone user,
  - a corporate smartphone,
  - a smartcard,
  - and a USB-based removable device.

---

    ## Page Navigation

    [← Defensive Principles](defensive-principles.md) | [Review Questions →](review-questions.md)
