---
title: "DNS Attacks"
layout: default
nav_order: 4
permalink: "/docs/dns-attacks/"
---

# 3. DNS Attacks: Cache Poisoning, Hijacking, and the Security of Name Resolution

DNS is one of the most deceptively important systems in all of networking.

Students often meet DNS as a convenience service: users type names, computers need IP addresses, and DNS provides the mapping. That is true, but it is far too shallow for security work. In practice, DNS is one of the most strategically important infrastructures on the Internet because it sits at the boundary between **human intention** and **machine destination**.

A user may intend to visit a legitimate site, connect to a mail server, retrieve a software update, or access a cloud service. But before any of that happens, the system must answer a deeper question:

> **Where should the traffic actually go?**

DNS influences that answer. That is why attackers care so much about it.

If an attacker can manipulate DNS, the attacker may not need to break TLS directly, compromise the browser, or crash the server. It may be enough to misdirect the victim toward the wrong destination, poison a resolver’s cache, alter local DNS settings, or exploit the trust that resolvers place in apparently valid responses.

So this chapter studies DNS not as a lookup table, but as an **attack surface made of delegation, caching, trust, timing, and operational dependency**.

---

## 3.1 Why DNS Is a Security-Critical System

RFC 1034 defines DNS as a distributed hierarchical database and naming system. That description is foundational because it reveals both why DNS scales so well and why it is vulnerable in so many interesting ways. DNS is not a single database, a single server, or a single trust point. It is a layered, distributed system composed of many cooperating authorities and many caches. :contentReference[oaicite:1]{index=1}

From a security viewpoint, DNS is critical because:

- users rely on names rather than IP addresses,
- applications often trust DNS answers automatically,
- caches extend the influence of one response to many later requests,
- DNS affects web access, email routing, service discovery, and increasingly other security controls,
- and a single successful manipulation can redirect large numbers of users without changing their behavior. :contentReference[oaicite:2]{index=2}

This gives DNS a special status in attack analysis:

> **If TCP/IP attacks target connectivity, DNS attacks target destination truth.**

That is a different kind of leverage.

---

## 3.2 DNS Through the Lens of Cyber Attacks

A normal networking explanation of DNS says:

- the client asks a resolver,
- the resolver follows delegations if needed,
- the answer is returned and often cached,
- future queries become faster.

A security explanation begins differently. It asks:

1. Who is trusted to answer?
2. How does the resolver decide that a response matches a query?
3. What information is cached, and for how long?
4. What happens if the first acceptable response is false?
5. What if the local device uses the wrong resolver?
6. What if the authoritative server is correct but an intermediary lies?
7. What if the attacker does not need to forge the final destination, only to alter the path to it?

That viewpoint transforms DNS from “name lookup” into a system of **resolver trust and answer validation**.

---

## 3.3 The Core Security Property DNS Must Provide

At the most basic level, a DNS user needs confidence that:

- the answer really corresponds to the requested name,
- the answer really came from the correct authority chain,
- the answer has not been maliciously altered,
- and the answer has not been replaced in cache by forged information.

Traditional DNS was not originally designed to provide cryptographic origin authentication for ordinary answers. That historical fact is central to understanding cache poisoning. DNSSEC was later introduced to add data origin authentication and data integrity to DNS data, but DNSSEC changes the trust model rather than the basic role of DNS. RFC 4033 explicitly states that DNSSEC adds data origin authentication and integrity, but does not by itself provide confidentiality. :contentReference[oaicite:3]{index=3}

So, from the attacker’s viewpoint, DNS presents two major opportunities:

1. **make the victim ask the wrong place**, or
2. **make the victim accept the wrong answer**.

Many DNS attacks are variations of these two ideas.

---

## 3.4 DNS Architecture and Why It Is Both Powerful and Dangerous

DNS is hierarchical.

At a simplified level:

- root servers know where top-level domains are delegated,
- top-level domain servers know where lower-level authorities are,
- authoritative servers know the actual records for their zones,
- recursive resolvers perform lookups on behalf of clients,
- and caches remember previous answers for efficiency. :contentReference[oaicite:4]{index=4}

This architecture is elegant and scalable, but each feature creates a corresponding security question.

### Delegation
Delegation allows the namespace to scale globally.  
Security question: how do we know that a delegation chain is authentic and not maliciously altered?

### Recursion
Recursion allows clients to offload complexity to recursive resolvers.  
Security question: how much trust should clients place in their recursive resolver?

### Caching
Caching reduces latency and traffic.  
Security question: what happens if a cache stores forged data?

### Distributed administration
Different entities control different zones.  
Security question: which administrative compromise is enough to redirect traffic meaningfully?

This is a classic security trade-off:

> **The very features that make DNS practical at internet scale also create rich opportunities for abuse.**

---

## 3.5 DNS Records and Their Attack Relevance

Students should not memorize all DNS record types at this stage, but they should understand why several of them matter in attacks.

### A and AAAA records
These map names to IPv4 and IPv6 addresses.

Security relevance:
- malicious answers here redirect users or applications to attacker-controlled destinations.

### CNAME records
These alias one name to another.

Security relevance:
- chaining or misconfiguration can produce surprising resolution behavior and broaden the impact of compromise.

### NS records
These define authoritative name servers for a zone.

Security relevance:
- manipulation here can redirect trust itself, not just one final address.

### MX records
These define mail exchangers.

Security relevance:
- if altered, email delivery may be redirected or intercepted.

### TXT records
These are widely used for metadata and policy, including email-related controls.

Security relevance:
- DNS increasingly carries security-relevant policy information, so DNS compromise may affect more than web browsing.

The deeper lesson is this:

> **DNS is not only about “where is the website?” It is also about who handles mail, who is authoritative, and which policy data other systems trust.**

---

## 3.6 Recursive Resolvers: Why They Are Such Important Targets

For many users and devices, the recursive resolver is the practical face of DNS.

A client usually does not walk the global hierarchy itself. Instead, it sends a query to a recursive resolver, which performs the work and returns an answer. That answer may come from cache or from a fresh iterative resolution process. RFC 1034 and RFC 1035 describe this general model, and modern operational security guidance still treats recursive resolvers as high-value infrastructure. :contentReference[oaicite:5]{index=5}

Why attackers care about recursive resolvers:

- one poisoned cache entry may affect many users,
- one compromised resolver changes trust for all clients using it,
- resolvers are central observation and control points,
- and resolvers often sit in privileged enterprise positions.

A user’s browser may be secure, the operating system may be patched, and the server certificate may be valid in principle. But if the wrong DNS answer is accepted early enough in the workflow, the user may still be routed into danger.

---

## 3.7 TTL and Caching: Performance Feature, Security Multiplier

TTL, or Time To Live, tells a resolver how long a cached answer may be reused.

From a performance perspective, TTL is wonderful:
- fewer repeated lookups,
- lower latency,
- reduced upstream traffic,
- better scalability.

From a security perspective, TTL changes the economics of a successful forgery.

If a false answer is accepted and cached:

- the attack may persist beyond the original forged exchange,
- many later users may inherit the bad mapping,
- and the harm continues until expiry or manual correction.

So TTL is not simply a technical parameter. It is a **risk-multiplication parameter**.

A forged answer that lasts for seconds is one thing. A forged answer that lives in cache for hours is another.

This is why caching must always be discussed with security in mind, not only efficiency.

---

## 3.8 DNS Threat Categories

A strong DNS chapter should distinguish several threat categories rather than collapsing all misuse into “poisoning.”

### 3.8.1 Cache poisoning
The attacker injects forged DNS data into a resolver cache so later clients receive false answers.

### 3.8.2 DNS hijacking
A broader term covering situations in which name resolution is diverted by:
- changed local resolver settings,
- malicious resolvers,
- ISP manipulation,
- compromised routers,
- malware rewriting configuration,
- or administrative takeover of DNS infrastructure.

### 3.8.3 Pharming
The user attempts to visit the correct name but is silently redirected to the wrong destination.

This is conceptually different from phishing, where the user is lured into taking the wrong action directly.

### 3.8.4 Authoritative compromise
The attacker compromises the actual authority for a zone or its management plane.

### 3.8.5 On-path or man-in-the-middle DNS modification
The attacker alters traffic between resolver and server or between client and resolver.

### 3.8.6 Abuse of open resolvers and DNS infrastructure
DNS systems may also be abused in reflection, amplification, tunneling, covert communication, and reconnaissance, although those topics are adjacent rather than central to this chapter.

This categorization matters because the **same visible symptom** — the user goes to the wrong IP — may arise from very different causes.

---

## 3.9 Phishing versus Pharming

Students often confuse these terms, so the distinction should be sharp.

### Phishing
The attacker tricks the user into going to a malicious site or giving away credentials, often through email, messaging, or fake urgency.

### Pharming
The user attempts to go to the correct site, but DNS or another resolution layer redirects the user elsewhere.

Why this matters:
- phishing is often centered on **human deception**,
- pharming is often centered on **resolution-path manipulation**.

In real campaigns, they may combine. For example:
- phishing gets the user to install malware,
- malware changes local DNS settings,
- later browsing attempts are pharmed even when the user types the right domain.

That is why students should not think of these categories as isolated silos.

---

## 3.10 DNS Cache Poisoning: The Core Attack Logic

Cache poisoning is one of the classic DNS attacks because it reveals the essential problem: if the resolver cannot strongly distinguish legitimate from forged answers, an attacker may succeed by racing, guessing, or injecting plausible response data.

At a high level, cache poisoning works like this:

1. the resolver issues a DNS query,
2. the attacker sends one or more forged responses that appear to match that query,
3. if one forged response is accepted,
4. the false data may be cached,
5. later clients receive the poisoned answer.

The elegance of the attack lies in its leverage:
- one successful forged response may affect many later users,
- the attacker need not compromise the final destination server,
- and the victim may appear to behave normally.

This is why NIST guidance repeatedly treats cache poisoning as a major DNS security concern. :contentReference[oaicite:6]{index=6}

---

## 3.11 Why Traditional DNS Was Vulnerable to Forged Answers

Traditional DNS matching logic was historically based on query-response consistency rather than modern cryptographic proof. Defenses improved over time, but the basic reason poisoning was possible is conceptually simple:

> **If a resolver accepts the first response that looks like the correct answer to an outstanding query, an attacker may win by making a forged answer arrive convincingly enough and early enough.**

Historically relevant matching elements include:
- transaction identifiers,
- source and destination information,
- the questioned name,
- expected record context,
- and timing.

RFC 5452 explicitly focuses on making DNS more resilient against forged answers, which highlights that forged-answer acceptance was a serious operational problem that required concrete mitigations. :contentReference[oaicite:7]{index=7}

The attacker does not need perfect omniscience. The attacker needs enough of:
- prediction,
- brute-force opportunity,
- query triggering,
- or on-path positioning.

That is why poisoning is partly a protocol issue and partly an operational issue.

---

## 3.12 Kaminsky-Style Cache Poisoning: Why It Was Such a Turning Point

Dan Kaminsky’s attack was so important because it changed the scale and practicality of poisoning.

The key insight was not simply “forge a DNS answer.” Attackers already understood that idea. The breakthrough was realizing that if the attacker can trigger many resolver queries for unpredictable subdomains of a target zone, the attacker gets **many fresh opportunities** to race legitimate responses and guess the matching parameters.

Instead of waiting for naturally occurring resolution events, the attacker induces repeated chances.

Why this was strategically important:
- it increased the number of trials available to the attacker,
- it made poisoning much more practical,
- and it pushed the community toward emergency hardening measures.

This is a classic case where the real danger was not merely a flaw in one field, but the attacker’s ability to **amplify the number of attack attempts per unit time**.

That is an important security lesson far beyond DNS.

---

## 3.13 DNS Hijacking Beyond Cache Poisoning

A mature DNS chapter must emphasize that “wrong DNS answers” do not always come from cache poisoning.

Other common hijacking paths include:

### Local configuration compromise
Malware may change the system’s configured resolvers.

### Home or enterprise router compromise
If the gateway distributes malicious DNS settings via DHCP or equivalent configuration, many clients may be silently affected.

### Rogue or deceptive public resolvers
Clients may rely on resolvers that are malicious, manipulated, or operationally unsafe.

### ISP-side interference
Some providers have historically altered error handling for nonexistent domains, injected redirects, or otherwise interfered with expected resolution semantics.

### Administrative DNS takeover
If the attacker gains control of registrar, zone-management, or authoritative infrastructure, the attack bypasses many lower-level assumptions.

The lesson here is powerful:

> **DNS abuse is not only about forged packets. It is also about who controls the naming path.**

---

## 3.14 DNSSEC: What It Solves and What It Does Not

DNSSEC was introduced to add data origin authentication and integrity to DNS data. RFC 4033 explains that DNSSEC does not provide confidentiality, but it does let resolvers validate that signed DNS data is authentic and untampered when the chain of trust is intact. :contentReference[oaicite:8]{index=8}

### What DNSSEC helps with
- forged-answer resistance,
- cache poisoning resistance,
- validation of signed data across the delegation chain,
- stronger assurance that the answer really came from the right authority.

### What DNSSEC does not solve by itself
- it does not encrypt DNS queries or responses,
- it does not stop all forms of local configuration hijacking,
- it does not protect users who rely on non-validating infrastructure,
- it does not replace general endpoint or operational security.

This distinction is essential. Students often hear “DNSSEC fixes DNS.” That is too broad.

A better statement is:

> **DNSSEC strengthens answer authenticity and integrity for validating resolvers, but secure name resolution still depends on the whole operational environment.**

---

## 3.15 Operational Defenses Against DNS Poisoning and Hijacking

A good defender does not rely on one countermeasure.

### 3.15.1 Query/response hardening
RFC 5452 emphasizes measures that make forged-answer guessing harder, including stronger matching practices and resolver-side hardening. :contentReference[oaicite:9]{index=9}

### 3.15.2 Source-port and transaction-ID randomness
Attackers succeed more easily when matching parameters are predictable.

### 3.15.3 DNSSEC validation
Resolvers that validate DNSSEC gain much stronger integrity assurances for signed zones. :contentReference[oaicite:10]{index=10}

### 3.15.4 Restrict and harden recursive service
Do not expose recursive capability unnecessarily. Keep resolvers patched and properly configured.

### 3.15.5 Protect configuration paths
Secure:
- local resolver settings,
- router management,
- DHCP distribution of resolver information,
- registrar accounts,
- zone-management interfaces.

### 3.15.6 Monitor for anomalies
Look for:
- unexpected resolver changes,
- sudden traffic to unfamiliar destinations,
- certificate mismatch waves following DNS changes,
- abnormal TTL patterns,
- suspicious query bursts for random subdomains,
- and discrepancies between independent resolvers.

### 3.15.7 Use secure operational architecture
NIST secure-DNS guidance emphasizes that DNS should be treated as foundational infrastructure and secured accordingly, not as a background utility that can be ignored until something breaks. :contentReference[oaicite:11]{index=11}

---

## 3.16 DNS as a Dependency for Other Security Mechanisms

One reason DNS deserves a major place in a network security course is that many other systems depend on it.

When DNS is wrong, the damage can spread outward into:

- web access,
- software updates,
- email routing,
- security policy retrieval,
- certificate-related workflows,
- logging destinations,
- cloud service discovery,
- remote management paths.

So DNS compromise is rarely “just a DNS problem.”

It is often a **multiplier problem**. It weakens the trust of other systems that assume name resolution is basically correct.

That is why attackers like DNS so much. It sits upstream of many user and system actions.

---

## 3.17 A Defender’s Mental Model for DNS Attacks

When you investigate a suspected DNS attack, think in layers:

### Layer 1: User behavior
Did the user type the right domain? Click a phishing link? Accept an unusual prompt?

### Layer 2: Local client configuration
Which resolver is the device using? Was that changed recently?

### Layer 3: Recursive resolver behavior
Did the resolver return the wrong data from cache? Is it validating? Is it compromised?

### Layer 4: Authoritative path
Was the authoritative data itself changed? Was a delegation altered?

### Layer 5: Network path
Could an on-path attacker have modified the exchange?

### Layer 6: Time and persistence
Was the problem momentary, cached, policy-driven, or administrative?

This layered analysis prevents a common student error: jumping straight to “cache poisoning” whenever a name resolves incorrectly.

---

## 3.18 Key Takeaways

- DNS is a high-value target because it translates human intent into machine destination.
- The DNS architecture is powerful because it is distributed, cached, and hierarchical, but those same features create attack opportunities.
- Cache poisoning is fundamentally about forged answers being accepted and then multiplied by caching.
- Pharming and hijacking are broader than phishing because they manipulate where resolution leads, even when the user intends the correct destination.
- Recursive resolvers are critical security infrastructure because one bad resolver decision can affect many clients.
- DNSSEC strengthens integrity and origin authentication for DNS data, but it does not solve every DNS-related problem.
- Secure DNS depends on protocol hardening, validation, configuration security, and operational monitoring together.

In short:

> **DNS attacks work when the attacker can alter trust in name resolution more cheaply than the defender can validate, detect, or contain that alteration.**

That is the central lens of this chapter.

---

## References

1. Mockapetris, P. *RFC 1034: Domain Names — Concepts and Facilities.* RFC Editor, 1987.  
2. Mockapetris, P. *RFC 1035: Domain Names — Implementation and Specification.* RFC Editor, 1987.  
3. Hubert, A., & van Mook, R. *RFC 5452: Measures for Making DNS More Resilient against Forged Answers.* RFC Editor, 2009.  
4. Arends, R., Austein, R., Larson, M., Massey, D., & Rose, S. *RFC 4033: DNS Security Introduction and Requirements.* RFC Editor, 2005.  
5. Chandramouli, R., et al. *NIST SP 800-81-2: Secure Domain Name System (DNS) Deployment Guide.* NIST.  
6. Rose, S., et al. *NIST SP 800-177 Rev. 1: Trustworthy Email.* NIST.  

---

## Page Navigation

[← TCP/IP Vulnerabilities and DoS Attacks]({{ '/docs/tcp-ip-dos/' | relative_url }}) | [Buffer Overflow Attacks →]({{ '/docs/buffer-overflow/' | relative_url }})
