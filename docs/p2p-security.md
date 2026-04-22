---
title: "P2P Security Issues"
layout: default
nav_order: 10
permalink: "/docs/p2p-security/"
---

# 9. P2P Security Issues

Peer-to-peer systems distribute storage and lookup across many nodes instead of relying on central servers.

## 9.1 Why P2P Matters in Security

P2P systems raise questions about:

- trust,
- routing integrity,
- anonymity,
- resilience,
- censorship resistance,
- and abuse.

## 9.2 Structured P2P Networks

Structured P2P systems use disciplined overlay routing, often based on distributed hash tables (DHTs).

Examples:
- Chord
- Pastry
- Kademlia

### Security issues
- malicious nodes in routing tables,
- incorrect lookups,
- poisoning,
- Sybil-like positioning problems,
- anonymity leakage,
- and content integrity concerns.

## 9.3 BitTorrent and Abuse Questions

BitTorrent is a prominent P2P protocol for file sharing.

Security discussions include:
- tracker trust,
- metadata leakage,
- peer behavior,
- abuse of distributed sharing,
- and “will I be caught?” style operational visibility questions.

## 9.4 Small-World / Unstructured P2P

Unstructured or small-world overlays rely more on ad hoc or human-established relationships.

### Freenet
Freenet is an important example focused on anonymous information distribution.

### Security themes
- routing manipulation,
- caching behavior,
- anonymity limits,
- trust-path assumptions,
- and exploitation of overlay logic.
---

## Page Navigation

[← Web Security Attacks]({{ '/docs/web-attacks/' | relative_url }}) | [Bots, Botnets, and DDoS →]({{ '/docs/botnets-ddos/' | relative_url }})

