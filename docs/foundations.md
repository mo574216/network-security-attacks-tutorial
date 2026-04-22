---
title: "Foundations: Why Network Attacks Work"
layout: default
nav_order: 2
permalink: "/docs/foundations/"
---

# 1. Foundations: Why Network Attacks Work

This course begins from a simple but deep observation:

> **Every cyber attack is an abuse of a legitimate mechanism, a broken assumption, or a misplaced trust decision.**

Networks are built to enable communication. Security exists because communication itself creates exposure. A host must accept packets before it can decide whether those packets are useful, malformed, malicious, or deceptive. A service must parse input before it can reject it. A browser must render content before a user can benefit from it. A server must allocate resources before a session becomes productive. DNS must answer queries before applications can find each other. In all of these cases, the defender is forced to remain open enough for normal use, while the attacker studies exactly how that openness can be turned into leverage. :contentReference[oaicite:1]{index=1}

So the foundational question of network security is not merely:

**How do networks work?**

It is:

**How can the normal behavior of networks be bent, overloaded, forged, chained, or misinterpreted by an adversary?**

---

## 1.1 Security Must Be Studied Adversarially

A system is not secure just because it behaves correctly when users act honestly and traffic is well formed. A secure system must continue to behave acceptably when:

- packets are spoofed,
- requests are repeated,
- inputs are malformed,
- services are probed at scale,
- names are manipulated,
- memory is corrupted,
- credentials are guessed,
- sessions are hijacked,
- browsers execute hostile content,
- or users are deceived into helping the attacker. :contentReference[oaicite:2]{index=2}

This is why network security is inherently **adversarial engineering**. You must understand both:

- the **intended design path** of a protocol or application, and
- the **abuse path** that becomes available when an attacker controls timing, scale, syntax, routing, or user perception.

A course on attacks therefore is not a detour from understanding systems. It is one of the best ways to understand systems deeply.

---

## 1.2 The First Principle: Connectivity Creates Attack Surface

Any system that can receive traffic exposes some **attack surface**. In practice, that surface includes:

- listening services and open ports,
- network stacks,
- name-resolution behavior,
- authentication endpoints,
- APIs,
- browser-exposed logic,
- administrative interfaces,
- software update paths,
- mobile apps,
- removable media,
- and the humans who operate the system. :contentReference[oaicite:3]{index=3}

Students often think of attack surface too narrowly, as if it means only “open ports.” That is incomplete. A port may expose a daemon, but an application may also expose unsafe parsing logic, a database query path, a password-reset workflow, a browser script context, or a trust relationship with another machine. The attacker does not care whether the weakness lives in TCP, DNS, PHP, SQL, JavaScript, or a human inbox. The attacker cares only that it can be reached and exploited.

So when you study any system, ask:

1. **What can an outsider reach?**
2. **What must the target accept before it can decide?**
3. **What assumptions does the target make about that input?**
4. **What resources does it allocate before full trust is established?**
5. **What happens if the input is syntactically valid but semantically hostile?**

That way of thinking will unify the entire course.

---

## 1.3 The Second Principle: Protocols Are Built on Assumptions

Protocols are full of assumptions because protocols must simplify reality in order to work at internet scale.

### Example 1: IP
IP is designed to deliver packets across networks. It is fundamentally about addressing and routing, not about proving that a source address is truthful. That is why source-IP spoofing is possible in many contexts unless networks apply filtering at their edges. BCP 38 exists precisely because forged source addresses are operationally dangerous and widely useful to attackers. :contentReference[oaicite:4]{index=4}

### Example 2: TCP
TCP provides reliable, stateful communication and establishes sessions through a three-way handshake. That handshake is excellent for normal communication, but it also means the server may create partial state before the connection is fully established. SYN flooding abuses this design fact. The attack does not “break TCP” in the sense of violating the protocol. It weaponizes TCP’s normal state allocation behavior. RFC 9293 explicitly defines the three-way handshake and the stateful nature of TCP connection establishment. :contentReference[oaicite:5]{index=5}

### Example 3: DNS
DNS is a hierarchical, distributed naming system. That architecture gives it enormous scalability, but also introduces caching, delegation, trust chains, and query/response timing windows. Those same features create opportunities for cache poisoning, hijacking, and redirection. RFC 1034 defines DNS as a distributed hierarchical namespace and distributed database, which is exactly why it is both powerful and attackable. :contentReference[oaicite:6]{index=6}

The lesson is foundational:

> **Attacks often succeed not because protocols are useless, but because protocols optimize for function first and rely on surrounding controls to contain abuse.**

---

## 1.4 The Third Principle: State Is Valuable, and Therefore Attackable

A major dividing line in attacks is whether the target is:

- **stateless**, or mostly stateless,
- **lightly stateful**, or
- **heavily stateful**.

The more state a system must remember per request, client, session, lookup, or transaction, the more it becomes vulnerable to **state exhaustion**, **state confusion**, or **state desynchronization**.

### Why this matters

- A TCP server maintains connection state. That is why half-open connection abuse matters. :contentReference[oaicite:7]{index=7}
- A DNS resolver caches answers and outstanding query context. That is why poisoning and timing races matter. :contentReference[oaicite:8]{index=8}
- A web application maintains sessions, cookies, CSRF assumptions, authorization state, and backend query context. That is why XSS, SQL injection, and session theft matter. :contentReference[oaicite:9]{index=9}
- An authentication service tracks attempts, identities, and policy state. That is why dictionary attacks, lockout evasion, and privilege escalation matter. :contentReference[oaicite:10]{index=10}

A useful mental model is this:

> **The attacker is always looking for expensive state on the defender’s side that can be triggered cheaply from the attacker’s side.**

That one sentence explains a remarkable portion of denial-of-service, authentication abuse, and application-layer exploitation.

---

## 1.5 The Fourth Principle: Parsing Is a Security Boundary

Nearly every attack begins at a parser.

A parser may process:

- packet headers,
- protocol options,
- filenames,
- URLs,
- HTTP parameters,
- cookies,
- JSON bodies,
- SQL parameters,
- shell arguments,
- uploaded documents,
- or script-bearing browser content.

Security failures appear when the defender assumes that input is only data, while the downstream component interprets part of that input as **structure**, **control**, or **code**.

That is the heart of **injection**. OWASP defines injection as a class of application flaws in which untrusted input is sent to an interpreter and causes the interpreter to execute part of that input as commands. That description captures SQL injection, command injection, and several related problems. :contentReference[oaicite:11]{index=11}

The same underlying logic appears in:

- SQL injection: input is treated as query logic,
- XSS: input is emitted into a browser execution context,
- command injection: input is interpreted by a shell or system command processor,
- buffer overflow: input crosses an allocation boundary and corrupts nearby memory or control data. :contentReference[oaicite:12]{index=12}

So one of the strongest foundations for students is this:

> **Security problems often arise not when data is received, but when data crosses into a new interpreter, parser, privilege domain, or trust context.**

---

## 1.6 The Fifth Principle: Trust Boundaries Are Where Attacks Become Interesting

A **trust boundary** is any place where one component accepts something from another component and assumes some degree of correctness, legitimacy, or safety.

Common trust boundaries include:

- network to host,
- client to server,
- browser to web app,
- web app to database,
- user to email message,
- resolver to authoritative DNS response,
- app to OS,
- removable device to endpoint,
- employee to internal service,
- one host to another host inside the same organization. :contentReference[oaicite:13]{index=13}

Many attacks are easiest to understand as trust-boundary failures:

- **IP spoofing** exploits trust in packet source identity.
- **DNS cache poisoning** exploits trust in response legitimacy and cache insertion.
- **SQL injection** exploits trust in input handling between app and database.
- **XSS** exploits trust in content rendered by the browser.
- **Dictionary attacks** exploit exposed authentication boundaries.
- **Spear phishing** exploits trust between a human and a message that appears legitimate.
- **Botnets** exploit trust relationships among protocols and management infrastructure that can be hidden in normal traffic. :contentReference[oaicite:14]{index=14}

Whenever you analyze a system, identify its trust boundaries first. Many of the attack paths will become obvious.

---

## 1.7 The Sixth Principle: Identity on a Network Is Harder Than It Looks

Students often assume that if a packet arrives “from” somewhere, then it really came from there. Or they assume that if a hostname resolves, the path is trustworthy. Or if a login page looks familiar, the user is safe. These assumptions fail constantly.

Identity in networked systems is layered and fragile:

- IP identity is not the same as user identity.
- DNS names are not the same as cryptographic proof.
- An authenticated session is not the same as correct authorization.
- A trusted sender display name is not the same as a trustworthy email.
- A correct password is not the same as a safe workflow. :contentReference[oaicite:15]{index=15}

This is why attacks often chain together:

1. redirect the victim with DNS manipulation,
2. imitate the real site,
3. steal credentials through phishing,
4. authenticate as the user,
5. escalate privileges through application or administrative weakness.

If students fail to distinguish between naming, authentication, authorization, and trust, they will misunderstand modern attacks.

---

## 1.8 The Seventh Principle: The Human Is Part of the Protocol Stack

A strong foundational treatment of attacks must explicitly reject the idea that cyber attacks are purely technical.

Humans are routinely:

- targeted through phishing,
- socially engineered into clicking links,
- tricked into opening documents,
- persuaded to trust spoofed urgency,
- manipulated through interface design,
- or induced to weaken security controls for convenience. :contentReference[oaicite:16]{index=16}

In practice, many successful campaigns do not begin by defeating encryption or bypassing firewalls. They begin by persuading a user to do something the attacker could not force technically.

This does **not** mean “the user is the problem.”  
It means secure systems must be designed with realistic models of human behavior. Attackers understand that authentication and authorization are embedded in workflows, not just in math.

That is why phishing, spear phishing, trojans, fake password resets, and malicious attachments belong in a foundational chapter, not only in a later “soft topics” section.

---

## 1.9 The Eighth Principle: Scale Changes Everything

A flaw that looks small locally may become severe when automated.

One failed login attempt is noise.  
Ten thousand attempts across exposed SSH endpoints become a campaign.

One malformed DNS response is irrelevant.  
A resolver that can be forced into many query races becomes a strategic target.

One infected host is a cleanup problem.  
A worm with autonomous propagation becomes an outbreak.

One compromised webcam seems minor.  
Hundreds of thousands of weak IoT devices become DDoS infrastructure.

At scale, attackers gain three advantages:

- **speed**,
- **parallelism**,
- **asymmetry**.

Automation is the bridge from weakness to incident. This is why scanning, botnets, worms, and large-scale credential abuse are central to network security.

---

## 1.10 The Ninth Principle: Most Real Attacks Are Chains, Not Single Events

Undergraduate students often learn attacks as isolated named categories:

- SYN flood,
- DNS poisoning,
- SQL injection,
- XSS,
- malware,
- phishing.

That is pedagogically useful, but operationally incomplete. Real intrusions are often **chains** of actions.

A typical chain may look like this:

1. reconnaissance,
2. service identification,
3. credential guessing or phishing,
4. initial compromise,
5. persistence,
6. privilege escalation,
7. lateral movement,
8. exfiltration or disruption.

This is why the course should not be read as a list of disconnected tricks. It should be read as a study of **how attackers move through systems by exploiting one boundary after another**. NIST’s incident-handling guidance is built around the reality that cyber risk must be understood across preparation, detection, analysis, containment, eradication, and recovery, not as isolated technical events. :contentReference[oaicite:17]{index=17}

---

## 1.11 A Security-Centered Taxonomy of Attacks

For this tutorial, it is useful to organize attacks by the kind of leverage they gain.

### 1.11.1 Availability attacks
These attacks try to reduce or deny service by exhausting bandwidth, queues, CPU, memory, threads, or protocol state.

Examples include:

- SYN flooding,
- Shrew attacks,
- Slowloris,
- botnet-driven DDoS.

These attacks teach that the attacker does not always need to break confidentiality or gain code execution. Making the service unavailable may itself achieve the objective.

### 1.11.2 Integrity attacks
These attacks alter data, control flow, routing outcomes, or interpretation.

Examples include:

- DNS cache poisoning,
- SQL injection,
- command injection,
- buffer-overflow-based control corruption,
- malicious browser script injection.

These attacks teach that correctness of output depends on correctness of interpretation.

### 1.11.3 Confidentiality attacks
These attacks seek information: credentials, cookies, private content, topology, software versions, keys, or behavioral patterns.

Examples include:

- packet sniffing,
- XSS-based cookie theft,
- malware-based surveillance,
- phishing,
- side-channel leakage,
- reconnaissance and enumeration.

These attacks teach that exposure often begins with visibility.

### 1.11.4 Authentication and authorization attacks
These attacks target identity and permission.

Examples include:

- dictionary attacks,
- credential stuffing,
- spear phishing,
- session hijacking,
- post-compromise privilege escalation.

These attacks teach that “who are you?” and “what may you do?” are separate but tightly linked questions.

### 1.11.5 Trust and deception attacks
These attacks manipulate what the victim believes.

Examples include:

- phishing,
- pharming,
- spoofed admin pages,
- malicious updates,
- fake support messages,
- fraudulent urgency cues.

These attacks teach that perception itself is a security boundary.

---

## 1.12 The Lens for the Rest of the Course

As you move into TCP/IP attacks, DNS manipulation, buffer overflows, malware, scanning, password attacks, web exploits, botnets, and mobile security, evaluate each topic with the same nine questions:

1. **What legitimate function does the system provide?**
2. **What must it trust in order to provide that function?**
3. **What state does it create before trust is fully established?**
4. **What parser or interpreter processes attacker-controlled input?**
5. **What identity assumption can be forged or manipulated?**
6. **What happens when the system is stressed at scale?**
7. **What trust boundary is crossed?**
8. **Can the attack be chained with others?**
9. **What defender-side asymmetry makes the attack attractive?**

If you keep asking those questions, the individual attacks in the rest of the tutorial will feel like variations of a coherent security logic rather than a bag of unrelated case studies.

---

## 1.13 Key Takeaways

- Networks are vulnerable not because they are badly designed in every respect, but because useful communication necessarily creates reachable behavior.
- Attackers study assumptions: source identity, timing, parsing, trust, caching, browser execution, human judgment, and resource allocation.
- The most important mental shift in security is from **“How is this supposed to work?”** to **“How could this be abused while still looking plausible to the target?”**
- Cyber attacks are best understood as **abuses of openness, state, interpretation, and trust**.
- Once that lens is adopted, topics such as SYN floods, DNS poisoning, buffer overflows, malware, SQL injection, XSS, botnets, and phishing become part of one unified story.

---

## Suggested Reading Mindset

As you study the rest of the tutorial:

- treat every protocol as a set of assumptions,
- treat every parser as a boundary,
- treat every service as an exposed economic target,
- treat every identity claim as something that may need proof,
- and treat every convenience feature as something an attacker may eventually weaponize.

That is the mindset of a network security professional.

---

## Page Navigation

[← Home]({{ '/' | relative_url }}) | [TCP/IP Vulnerabilities and DoS Attacks →]({{ '/docs/tcp-ip-dos/' | relative_url }})
