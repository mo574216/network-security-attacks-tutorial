    ---
    title: TCP/IP Vulnerabilities and DoS Attacks
    layout: default
    ---

[Home](index.md) | [Glossary](glossary.md)

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


    # 2. TCP/IP Vulnerabilities and DoS Attacks

This module begins with TCP/IP because many attacks become intuitive only after we understand how communication is supposed to work.

## 2.1 IP and TCP: Division of Responsibilities

### IP
IP provides **addressing and routing**.  
Its job is to move packets from a source host toward a destination host.

### TCP
TCP provides **reliable transport**.  
Its job is to make sure that data arrives in order, is acknowledged, and can adapt to congestion.

The distinction matters:

- IP is connectionless and best-effort.
- TCP is stateful and reliability-oriented.

Attackers exploit both sides:
- IP can be spoofed.
- TCP can be overloaded or manipulated.

## 2.2 The TCP/IP Protocol Stack

The network stack is commonly described with layers such as:

- Application
- Presentation
- Session
- Transport
- Network
- Data Link
- Physical

Security failures can appear in any layer, but multi-layer reasoning is often needed to understand real attacks.

Examples:

- A malformed application request may trigger a buffer overflow in server code.
- A flood of SYN packets abuses transport-layer connection setup.
- A spoofed source IP abuses network-layer trust.
- Browser-side script execution exploits application behavior plus client-side execution rules.

## 2.3 The IPv4 Header and Security-Relevant Fields

Important IPv4 fields include:

- **Source IP Address**
- **Destination IP Address**
- **Protocol**
- **TTL**
- **Header checksum**
- **Fragmentation-related fields**

### TTL (Time To Live)
TTL limits how long a packet can circulate.  
It is reduced at each router hop.

Security relevance:
- traceroute relies on TTL expiration,
- TTL-based behavior can reveal path information,
- packet manipulation and filtering decisions may be related to TTL patterns.

### Protocol field
This identifies the higher-level payload, such as TCP, UDP, or ICMP.

Security relevance:
- filters and IDS rules often inspect or classify traffic partly based on this field.

## 2.4 TCP Connection Establishment: The 3-Way Handshake

TCP uses a three-step handshake:

1. Client sends **SYN**
2. Server replies **SYN+ACK**
3. Client replies **ACK**

This handshake is fundamental for understanding SYN flooding.

### Why state matters
When the server receives a SYN, it must allocate partial connection state before the connection is fully established.

That design enables normal communication, but it also creates an attack surface:  
an adversary can generate many incomplete handshakes and consume server resources.

## 2.5 TCP Congestion Control and the Shrew Attack

TCP uses congestion control to prevent network collapse.  
It adjusts its sending rate in response to signs of congestion, such as:

- timeouts,
- duplicate acknowledgments,
- packet loss signals.

### The Shrew DoS attack
A **Shrew attack** is a low-rate but carefully timed denial-of-service attack.  
Instead of sending overwhelming traffic continuously, the attacker sends bursts that align with TCP retransmission timers and congestion-control behavior.

#### Why this is dangerous
A naive view of DoS is that the attacker must send “more traffic than the victim can handle.”  
The Shrew attack shows that this is false.

A well-timed, periodic burst can:

- repeatedly trigger TCP backoff,
- keep legitimate flows in reduced-throughput mode,
- and cause severe performance degradation with much lower average attack bandwidth.

#### Educational lesson
Security is not only about volume.  
It is often about **protocol timing**, **state machines**, and **control-loop manipulation**.

## 2.6 SYN Flooding

A SYN flood targets the server’s willingness to reserve state for half-open connections.

### Mechanism
The attacker sends many SYN packets to the victim server.  
The server responds with SYN+ACK and waits for the final ACK that never arrives.

As a result:

- the backlog queue fills,
- state accumulates,
- legitimate clients cannot establish new connections.

### Why spoofing helps
If the attacker spoofs source IP addresses, the replies from the server go elsewhere.  
That makes the flood harder to trace and ensures the server waits on many nonexistent clients.

### Symptoms
- connection failures for legitimate users,
- many half-open TCP connections,
- backlog exhaustion,
- resource spikes,
- apparent service outage even though the server process is still running.

## 2.7 IP Source Address Spoofing

In source address spoofing, the sender puts a false IP address in the source field of outgoing packets.

### Why spoofing is useful to attackers
- hides the real sender,
- makes reflection and amplification easier,
- supports SYN flooding,
- complicates attribution,
- can exploit trust relationships if networks rely too much on source address assumptions.

### Limitations
Spoofing is easier when the attacker does not need to receive return traffic.  
That is why it is especially powerful in one-way attacks such as some forms of DoS.

## 2.8 BCP 38 and Ingress Filtering

A major defense against IP spoofing is **BCP 38**, which recommends filtering packets at network edges so that outgoing packets with illegitimate source addresses are blocked.

### Basic idea
If an ISP or enterprise knows which source address ranges are valid for a customer network, it should reject outbound packets that claim impossible source addresses.

### Security significance
Widespread deployment of ingress/egress filtering would drastically reduce spoofing-based attacks.

## 2.9 Netstat and Operational Troubleshooting

Even defensive work requires visibility.

Useful operational questions include:

- Which sockets are open?
- Which ports are listening?
- Which connections are established, half-open, or closing?
- Is abnormal TCP state accumulation occurring?

Utilities such as **netstat** help defenders observe local network behavior and diagnose attacks or misconfigurations.

---

    ## Page Navigation

    [← Foundations](foundations.md) | [DNS and Cache Poisoning →](dns-attacks.md)
