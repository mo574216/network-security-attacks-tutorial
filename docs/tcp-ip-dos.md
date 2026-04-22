---
title: "TCP/IP Vulnerabilities and DoS Attacks"
layout: default
nav_order: 3
permalink: "/docs/tcp-ip-dos/"
---

# 2. TCP/IP Vulnerabilities and DoS Attacks

This chapter studies the TCP/IP stack through the lens of cyber attack.

That wording matters. In a conventional networking course, TCP and IP are often taught as elegant engineering layers: IP moves packets, TCP provides reliable transport, and the rest of the stack builds useful applications on top. In a security course, that is only the starting point. The real question is this:

> **What must the network stack do for legitimate communication, and how can an attacker force those same mechanisms to work against the defender?**

TCP/IP is especially important in attack analysis because it exposes nearly all of the basic strategic themes of cybersecurity:

- trust without proof,
- state creation before full validation,
- asymmetry between attacker cost and defender cost,
- control-loop manipulation,
- source deception,
- queue exhaustion,
- and the difficulty of distinguishing legitimate from malicious traffic at line rate.

This chapter therefore does more than explain a few named attacks. It teaches how to reason about protocol abuse.

---

## 2.1 Why TCP/IP Is a Security Gold Mine for Attackers

The Internet protocol stack was built for interoperability, robustness, and scale. That design goal was historically about making heterogeneous systems communicate, not about making every field, packet, and timing decision cryptographically self-proving. As a result, the stack contains several properties that are enormously useful in ordinary operation but potentially exploitable under adversarial pressure:

- IP is connectionless and does not, by itself, prove source identity.
- TCP is stateful and must remember partial progress during communication setup.
- Routers and hosts must process packets quickly, often before deep verification is possible.
- Diagnostic and control behavior can reveal internal conditions.
- Congestion-control logic can be manipulated rather than merely overloaded.
- Security decisions frequently depend on context outside the packet itself. :contentReference[oaicite:1]{index=1}

This means TCP/IP attacks do not all look alike. Some attacks forge identity, some exhaust state, some exploit retransmission timing, some abuse congestion control, and some try to degrade service while staying statistically quiet.

So instead of memorizing attack names, students should keep asking:

1. What does the protocol trust?
2. What state does it create?
3. What does it do before full validation?
4. What is cheap for the attacker but expensive for the defender?
5. Which control loop can be manipulated?

Those questions will make the rest of the chapter much easier.

---

## 2.2 IP and TCP: Division of Responsibilities and Division of Weaknesses

At a high level:

### IP
IP provides addressing, forwarding, and best-effort packet delivery across interconnected networks.

### TCP
TCP provides reliable, ordered, full-duplex byte-stream transport between endpoints. It establishes connections, tracks sequence space, acknowledges data, retransmits lost segments, and reacts to congestion. :contentReference[oaicite:2]{index=2}

That clean division of responsibilities also creates a clean division of attack opportunities.

### Typical IP-layer attack opportunities
- source-address spoofing,
- fragmentation abuse,
- path and TTL inference,
- reflection-enabling deception,
- routing or reachability manipulation in broader contexts.

### Typical TCP-layer attack opportunities
- SYN-state exhaustion,
- blind spoofed segment injection,
- reset or sequence-number guessing attacks,
- congestion-control manipulation,
- application starvation through connection management abuse. :contentReference[oaicite:3]{index=3}

The key lesson is that **IP and TCP are not just functional layers; they are attack surfaces with different economics**.

---

## 2.3 The TCP/IP Stack as a Multi-Layer Attack Surface

The protocol stack is commonly described as layered:

- Application
- Presentation
- Session
- Transport
- Network
- Data Link
- Physical

From a security viewpoint, the value of this model is not just conceptual neatness. It shows where assumptions are made and where those assumptions can fail.

For example:

- A spoofed IP packet abuses the network layer.
- A SYN flood abuses transport-layer state creation.
- A malformed application request may trigger a buffer overflow in the server process above TCP.
- Browser-side script abuse may begin at the application layer but depends on lower-layer reachability.
- A DDoS attack may combine transport abuse, application behavior, and routing realities at the same time.

So when an attack is described as “TCP-layer” or “IP-layer,” that is usually shorthand, not a complete explanation. Serious attacks often cross layers.

---

## 2.4 IPv4 Header Fields That Matter in Security

RFC 791 defines the IPv4 header and its core fields. In a networking class, these fields may be introduced as packet-format details. In security, they are clues, levers, and sometimes weapons. :contentReference[oaicite:4]{index=4}

Important fields include:

- **Source Address**
- **Destination Address**
- **Protocol**
- **Time To Live (TTL)**
- **Identification**
- **Flags**
- **Fragment Offset**
- **Header Checksum**
- **Options** :contentReference[oaicite:5]{index=5}

### 2.4.1 Source Address

This field says where the packet claims to have come from.

Security significance:
- It is central to attribution attempts.
- It is central to reply routing.
- It is central to access-control decisions in badly designed systems.
- It is a primary enabler of spoofing-based attacks.

The problem is obvious: **the network cannot blindly trust a field simply because the sender wrote it down**.

### 2.4.2 Destination Address

This seems simple, but destination choice shapes attack intent:

- direct attack on a target,
- reflected traffic toward a victim,
- scanning across ranges,
- hotspot concentration on a service class.

In DDoS analysis, destination distribution patterns often matter as much as packet volume.

### 2.4.3 Protocol Field

This identifies the upper-layer payload, such as TCP, UDP, or ICMP.

Security significance:
- ACLs and firewalls often classify using this field.
- IDS/IPS systems use it as part of flow semantics.
- Attack campaigns may choose different protocols based on whether they want state exhaustion, bandwidth saturation, reflection, or diagnostic discovery.

### 2.4.4 TTL (Time To Live)

TTL decreases at each forwarding hop.

Normal purpose:
- prevent indefinite circulation of packets.

Security significance:
- traceroute depends on TTL expiration behavior,
- TTL profiles can help fingerprint paths,
- abnormal TTL patterns may support anomaly detection,
- TTL-limited probes can reveal topology and middlebox behavior.

This is a good example of a field that is not “a security feature” yet still has major security implications.

### 2.4.5 Fragmentation Fields

IPv4 fragmentation behavior is controlled by fields such as Identification, Flags, and Fragment Offset.

Security significance:
- fragmentation complicates filtering and reassembly,
- fragmented traffic can stress state and inspection systems,
- security devices may disagree with end hosts on fragment interpretation,
- historically, fragmentation has been used to bypass or confuse defenses. :contentReference[oaicite:6]{index=6}

Students should remember this general lesson:

> **Any time the network must split, reorder, reassemble, or infer context across packets, the chance of security trouble goes up.**

---

## 2.5 TCP Header Fields and Why They Matter to Attackers

TCP is not just “reliable transport.” It is a finely structured state machine built on fields that directly influence endpoint behavior.

Security-relevant TCP concepts include:

- source and destination ports,
- sequence numbers,
- acknowledgment numbers,
- flags such as SYN, ACK, RST, FIN,
- receive windows,
- retransmission behavior,
- and endpoint state transitions. :contentReference[oaicite:7]{index=7}

### Ports
Ports expose services. Attackers care about ports because they map network reachability to specific application logic.

### Sequence and acknowledgment numbers
These fields are central to ordered, reliable delivery. They are also central to off-path and blind injection attacks because guessing them correctly can let a spoofed segment appear plausible to a receiver. RFC 5961 was written specifically to improve TCP robustness against blind in-window attacks. :contentReference[oaicite:8]{index=8}

### Control bits
- **SYN** creates connection state.
- **ACK** advances connection progress.
- **RST** can tear a connection down.
- **FIN** participates in graceful closure.

When you understand what each bit causes the receiver to do, you begin to understand why TCP is so rich for both diagnostics and abuse.

---

## 2.6 TCP Connection Establishment: Why the Three-Way Handshake Is Both Elegant and Dangerous

TCP uses a three-way handshake:

1. Client sends **SYN**
2. Server replies **SYN+ACK**
3. Client replies **ACK**

This handshake is foundational because it lets both sides synchronize state and confirm reachability. RFC 9293 defines this stateful connection-establishment process in detail. :contentReference[oaicite:9]{index=9}

But from an attacker’s viewpoint, the handshake has a crucial property:

> **The server must do work before the client proves it will finish the exchange.**

That is the opening for SYN flooding.

### Why servers are vulnerable here

When the server receives a SYN on a listening port, it typically:

- allocates memory or queue state,
- records enough information to continue the connection,
- sends SYN+ACK,
- and waits for the final ACK.

If that final ACK never comes, the server still must remember the half-open connection for some period of time. RFC 4987 describes SYN flooding precisely as exploiting this state-retention behavior. :contentReference[oaicite:10]{index=10}

This is a perfect example of asymmetric security economics:

- the attacker sends one lightweight trigger packet,
- the defender allocates durable state.

Whenever students see this pattern, they should immediately suspect denial-of-service potential.

---

## 2.7 TCP State Machines and Why Security Students Must Care

Many students treat protocol state diagrams as dull formalism. In security, they are essential.

TCP endpoints transition through states such as:

- LISTEN
- SYN-SENT
- SYN-RECEIVED
- ESTABLISHED
- FIN-WAIT
- CLOSE-WAIT
- TIME-WAIT

Every state implies:

- expected incoming flags,
- valid transitions,
- acceptable timers,
- and how much memory or queue context the host must maintain.

From a defender’s viewpoint, state machines are normal protocol logic.

From an attacker’s viewpoint, state machines answer these questions:

- Which state is cheapest to force?
- Which state consumes the most server resources?
- Which state is easiest to prolong?
- Which transitions can be triggered with forged or mistimed traffic?

SYN flooding is really a state-machine attack disguised as a packet flood.

---

## 2.8 Denial of Service: A More Precise View

A common beginner mistake is to think DoS means “sending lots of traffic.”

That is incomplete.

A denial-of-service attack is better understood as:

> **an attempt to reduce or deny useful service by exploiting some asymmetry in resource consumption, protocol behavior, or operational dependency.**

That resource may be:

- link bandwidth,
- CPU,
- memory,
- connection-table capacity,
- retransmission budget,
- application threads,
- kernel queues,
- firewall state,
- resolver cache integrity,
- or even human attention.

This broader definition helps students classify attacks correctly.

### Examples

- A SYN flood attacks connection-establishment state.
- A Shrew attack attacks congestion-control dynamics.
- Slowloris attacks application-side request handling.
- Reflection/amplification attacks attack the victim’s inbound capacity while hiding the true origin.

So volume matters, but **what is being exhausted** matters even more.

---

## 2.9 TCP Congestion Control as an Attack Surface

TCP congestion control exists to keep the network usable under stress. It adjusts transmission behavior using signals such as:

- timeouts,
- duplicate acknowledgments,
- loss inference,
- and sometimes ECN-related behavior in modern environments. :contentReference[oaicite:11]{index=11}

This is a control system. That alone should interest a security student, because control systems can often be manipulated.

A defender may think:
- congestion control makes TCP adaptive.

An attacker may think:
- congestion control is a feedback mechanism I might be able to push into the wrong regime.

That is exactly the intuition behind the Shrew attack.

---

## 2.10 The Shrew Attack: Low Rate, High Insight

The classic Kuzmanovic and Knightly paper showed that a denial-of-service attack need not be high rate to be highly effective. The authors studied low-rate TCP-targeted DoS attacks, which they termed “shrew attacks,” that send brief bursts timed to interact destructively with TCP retransmission timeout behavior. :contentReference[oaicite:12]{index=12}

### Core idea

Instead of flooding continuously, the attacker sends periodic bursts that create packet loss events at moments likely to trigger TCP backoff.

### Why this works

TCP congestion control and retransmission logic operate on timing assumptions. If repeated bursts arrive at just the wrong rhythm, legitimate flows may repeatedly:

- infer congestion,
- reduce sending rates,
- back off exponentially,
- and spend large fractions of time in underutilization.

### Why this matters conceptually

The Shrew attack teaches one of the most important ideas in security engineering:

> **Attack power does not depend only on traffic volume. It can also depend on timing, synchronization, and feedback-loop manipulation.**

That lesson generalizes far beyond TCP. Anytime a defender relies on adaptive control, rate adjustment, timeout logic, or retry behavior, carefully timed interference may be disproportionately effective.

### Security takeaway

A low average-rate attacker may still cause serious harm if it understands the target’s control dynamics.

That is why traffic analysis cannot look only for “the biggest flows.” Sometimes the most damaging attack is the one that looks moderate but arrives at exactly the wrong moments.

---

## 2.11 SYN Flooding: The Canonical TCP DoS Attack

RFC 4987 describes SYN flooding as a denial-of-service method that exploits the state TCP retains after receiving SYN segments on listening ports. :contentReference[oaicite:13]{index=13}

### 2.11.1 Basic mechanism

The attacker sends many SYN packets to a server. The server replies with SYN+ACK and waits for the final ACK.

If the final ACK never arrives, half-open connections accumulate.

### 2.11.2 What gets exhausted

Depending on implementation, the attack may pressure:

- backlog queues,
- per-socket connection structures,
- kernel memory,
- timer management,
- CPU handling for repeated retries,
- and in some cases downstream service responsiveness.

### 2.11.3 Why spoofing helps

If the attacker spoofs source addresses:

- the SYN+ACK replies go elsewhere,
- the attacker does not need to receive return traffic,
- the server waits on clients that do not exist,
- attribution becomes harder,
- and reflective side effects may confuse analysis.

### 2.11.4 What legitimate users observe

Legitimate clients may see:

- timeouts,
- refused or stalled connections,
- intermittent service,
- or total inability to establish new TCP sessions.

Crucially, the server process may still be running. This is an important operational lesson: **availability failure does not always look like a crash**.

---

## 2.12 Common Mitigations Against SYN Flooding

RFC 4987 surveys common SYN flooding mitigations and their trade-offs. That trade-off idea is important: there is rarely a perfect defense with no operational cost. :contentReference[oaicite:14]{index=14}

### 2.12.1 Increase backlog resources
This can help absorb bursts, but it only raises the ceiling. It does not remove the asymmetry.

### 2.12.2 Reduce timeout exposure
If half-open state is discarded sooner, the window for abuse narrows. But short timeouts may hurt legitimate clients on lossy or high-latency paths.

### 2.12.3 SYN cookies
SYN cookies reduce server-side state retention during the handshake by encoding enough information in the server’s initial reply so that the server need not commit normal state immediately.

Security significance:
- they directly target the attacker’s state-exhaustion strategy.

Operational significance:
- they are powerful, but may have trade-offs depending on stack behavior and options.

### 2.12.4 Rate limiting and filtering
Edge filtering, upstream collaboration, and rate controls can reduce exposure, especially during obvious bursts.

### 2.12.5 Anycast, load distribution, and front-end protection
At scale, architectural defenses matter. DDoS resistance is often a systems problem, not just a host-stack problem.

---

## 2.13 IP Source Address Spoofing: Why It Is So Useful

In source address spoofing, the attacker forges the source IP field of outbound packets.

Students sometimes ask: why is spoofing so important if the attacker cannot see replies?

Because many attacks do not require reply visibility.

### Spoofing is useful when the attacker wants to:
- hide origin,
- make traceback harder,
- cause the target to waste effort on false clients,
- support reflection,
- exploit trust decisions based on address assumptions,
- or launch one-way denial-of-service activity.

This is why spoofing and DoS are historically intertwined.

### Limits of spoofing

Spoofing is less useful when the attacker needs:
- a full bidirectional application session,
- interactive control,
- or direct visibility into target responses.

So spoofing is not universally useful. It is strategically useful where one-way leverage is enough.

---

## 2.14 BCP 38 and Ingress Filtering: The Network Should Refuse Lies at the Edge

RFC 2827, also known as BCP 38, recommends network ingress filtering to block packets with forged source addresses from leaving customer or downstream networks. RFC 3704 extends the discussion for multihomed environments. :contentReference[oaicite:15]{index=15}

### Fundamental idea

If a provider knows what source prefixes are valid behind a given access point, it should reject outbound traffic claiming impossible addresses.

### Why this matters

Ingress filtering does not eliminate every DoS attack, but it sharply reduces the feasibility of many spoofing-based attacks, especially:

- SYN floods with fake origins,
- reflection and amplification attacks,
- and attacks that rely on making traffic non-traceable.

### Strategic lesson

BCP 38 is one of the clearest examples in security of a control that is most effective when deployed broadly rather than locally.

It illustrates a hard truth about internet defense:

> **Some of the best technical defenses only reach their full value when many independent networks cooperate operationally.**

That is why some attacks remain feasible even though the defense principle is well known.

---

## 2.15 Blind TCP Attacks Beyond DoS

A security-centered TCP chapter should not stop at SYN floods. TCP has also faced blind in-window injection issues, which is why RFC 5961 introduced changes to improve robustness against spoofed off-path segments that might otherwise terminate or corrupt active connections. :contentReference[oaicite:16]{index=16}

This matters for two reasons:

1. It reminds students that TCP vulnerabilities are not only about availability.
2. It reinforces the deeper point that sequence space, acknowledgment validation, and reset behavior are security-relevant.

Even when you are not studying a “DoS attack” narrowly, you are still studying how protocol trust is enforced.

---

## 2.16 Reflection and Amplification: Where Spoofing Meets Scale

Although the classic textbook examples often emphasize UDP reflection, the conceptual lesson belongs here because it follows directly from spoofing economics.

In reflection-style attacks:

- the attacker sends traffic with the victim’s source address,
- third-party systems send their replies to the victim,
- the attacker’s true location is hidden behind innocent intermediaries.

If the reply is larger than the trigger, the attack becomes amplification.

Why mention this in a TCP/IP chapter?
Because it reinforces the foundational logic:
- forged identity,
- asymmetric effort,
- and abuse of legitimate responders.

Once students understand spoofing at the IP layer, they are better prepared to understand reflection at internet scale.

---

## 2.17 ICMP, Diagnostics, and Security Visibility

Even though this chapter centers on TCP/IP DoS, students should remember that IP-layer diagnostics also matter.

ICMP supports control and error signaling such as:

- reachability failures,
- time exceeded messages,
- echo request/reply behavior used by ping,
- and path-discovery logic in tools such as traceroute.

Security significance:
- diagnostics aid defenders,
- but they also aid reconnaissance,
- reveal topology,
- and interact with filtering policies.

A strong defender uses visibility tools without assuming that diagnostic traffic is purely benign.

---

## 2.18 Observability: How Defenders See TCP/IP Abuse

Security is not only about preventing attacks. It is also about detecting and diagnosing them.

Operationally useful questions include:

- Which ports are listening?
- How many connections are in SYN-RECEIVED or other unusual states?
- Is there a surge in half-open connections?
- Are source distributions implausibly broad?
- Do retransmissions spike at periodic intervals?
- Are there abrupt changes in handshake completion ratios?
- Are flows failing at connection setup or later during application exchange?

Utilities such as `netstat`, `ss`, packet capture tools, and flow telemetry help answer these questions.

### Why this matters pedagogically

Students should not leave this chapter thinking only in terms of “how to attack.”

They should learn the paired skill:
- what artifacts the attack leaves,
- what state variables change,
- and what a defender would monitor.

A protocol attack is never just a packet trick. It is also a visibility problem.

---

## 2.19 A Security Summary of the Chapter

By now, the attack logic of TCP/IP should be clearer:

- IP is powerful but not inherently truthful about source identity.
- TCP is reliable but must create state before trust is fully earned.
- Congestion control is stabilizing under normal conditions but manipulable under adversarial timing.
- SYN flooding is not merely “a lot of packets”; it is exploitation of state retention.
- Spoofing is valuable because many attacks are one-way and asymmetric.
- Ingress filtering is powerful because it attacks the attacker’s deception mechanism at the network edge.
- Defensive understanding requires both protocol theory and operational observation.

In other words:

> **TCP/IP attacks work when the attacker can force the stack to spend memory, time, bandwidth, or trust faster than the defender can validate, absorb, or filter.**

That is the lens you should carry into the next chapters.

---

## References

1. Eddy, W. M. (Ed.). *RFC 9293: Transmission Control Protocol (TCP).* RFC Editor, 2022.  
2. Postel, J. *RFC 791: Internet Protocol.* RFC Editor, 1981.  
3. Eddy, W. *RFC 4987: TCP SYN Flooding Attacks and Common Mitigations.* RFC Editor, 2007.  
4. Ferguson, P., & Senie, D. *RFC 2827 / BCP 38: Network Ingress Filtering: Defeating Denial of Service Attacks which employ IP Source Address Spoofing.* RFC Editor, 2000.  
5. Baker, F., & Savola, P. *RFC 3704: Ingress Filtering for Multihomed Networks.* RFC Editor, 2004.  
6. Ramaiah, A., Stewart, R., & Dalal, M. *RFC 5961: Improving TCP's Robustness to Blind In-Window Attacks.* RFC Editor, 2010.  
7. Kuzmanovic, A., & Knightly, E. W. “Low-Rate TCP-Targeted Denial of Service Attacks (The Shrew vs. the Mice and Elephants).” *ACM SIGCOMM*, 2003.

---

## Page Navigation

[← Foundations: Why Network Attacks Work]({{ '/docs/foundations/' | relative_url }}) | [DNS and DNS Cache Poisoning →]({{ '/docs/dns-attacks/' | relative_url }})
