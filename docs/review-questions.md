    ---
    title: Review Questions
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

    # 15. Review Questions

## Conceptual Questions

1. Why is TCP more vulnerable to state-exhaustion attacks than IP?
2. Why does DNS caching improve performance but also create security risk?
3. What is the difference between phishing and pharming?
4. Why can a low-rate Shrew attack still be highly damaging?
5. Why is source-IP spoofing especially useful in some DoS attacks?
6. What makes buffer overflow a control-flow problem and not just a crash problem?
7. Why are worms typically more dangerous at internet scale than viruses?
8. Why can SYN scanning reveal open ports without fully establishing connections?
9. Why are salted password hashes more resistant to rainbow-table attacks?
10. Why is Slowloris considered an application-layer attack?
11. What is the difference between SQL injection and XSS?
12. Why do standard protocols such as IRC or HTTP help botnets hide?
13. Why can spear phishing succeed even in well-engineered networks?
14. Why does sandboxing help mobile security?
15. Why are side-channel attacks different from ordinary software-input attacks?

## Applied Questions

1. A server shows many half-open connections. Which attack should you suspect first, and why?
2. A resolver suddenly sends users to the wrong IP address for a legitimate domain. What attack categories should you investigate?
3. Authentication logs show repeated attempts for usernames like `admin`, `root`, and `oracle`. What is the likely attack type?
4. A web server remains responsive to pings but new HTTP clients hang. Which attack classes could explain this?
5. A browser executes injected script after a victim clicks a crafted link. What type of vulnerability is this?
6. A company sees thousands of compromised webcams sending traffic to one target. What major security story does this resemble?
7. A smartcard reveals secret-dependent timing variation. What attack family does that indicate?

---

    ## Page Navigation

    [← Lab Roadmap](lab-roadmap.md) | [Glossary →](glossary.md)
