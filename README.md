<div align="center">
  <img src="craf_branding_animation.svg" alt="CRAF — two agents writing together" width="100%"/>
</div>

# Clustered Real-Time Agent Federation

**A cognitive fusion protocol for multi-agent systems**

[![Status](https://img.shields.io/badge/status-alpha-orange?style=flat-square)](https://github.com/Tryboy229/CRAF)
[![Version](https://img.shields.io/badge/version-0.2.0--alpha-blue?style=flat-square)](https://github.com/Tryboy229/CRAF/blob/main/SPEC.md)
[![License](https://img.shields.io/badge/license-MIT-green?style=flat-square)](LICENSE)
[![Spec](https://img.shields.io/badge/spec-SPEC.md-purple?style=flat-square)](SPEC.md)

</div>

---

## The Problem

Every multi-agent framework in 2026 communicates the same way: **sequentially**.

Agent A sends a task. Agent B responds. Agent A reacts. Repeat.

This is fine for pipelines. It breaks down the moment a problem requires **simultaneous reasoning** — the kind that happens when a team thinks together around a whiteboard, not by trading emails.

## What CRAF Does

CRAF introduces a **Cluster** — a shared real-time memory space where complementary agents think collectively, not sequentially.

```
Without CRAF (A2A sequential)        With CRAF (Cluster)

  Agent A ──task──▶ Agent B           ┌──────────────────────┐
  Agent B ◀──reply── Agent A          │  Agent A ↔ Agent B   │
  Agent A ──react──▶ Agent B          │   Shared Memory       │
                                      │   Real-time · < 50ms  │
  Latency: ~seconds                   └──────────────────────┘
  Context: lost between turns          Latency: milliseconds
                                       Context: always shared
```

When the task is done, agents **separate naturally** — each carrying a differentiated imprint of what they collectively learned. No round trips. No context loss.

---

## Core Concepts

### The Biological Model

CRAF is grounded in how living tissue actually works, not in computer science abstractions.

| Biology | CRAF |
|---------|------|
| Cell | Agent |
| Tissue | Cluster (real-time fusion) |
| Organ | Business service (e.g. "Risk", "Legal") |
| Organism | Full multi-agent system |
| Synapse | A2A async between Clusters |

Cells within a tissue share a liquid environment — they don't send each other messages. Organs communicate through blood and nerves — asynchronously. CRAF applies the same two-layer model.

### The Golden Rule

> **Fuse what is complementary. Connect what is different.**

| Agent relationship | Mechanism |
|-------------------|-----------|
| Complementary (same domain, different skills) | **Cluster (real-time fusion)** |
| Different but useful (distinct domains) | **A2A async** |
| Identical / redundant | **Load-balancing** |
| Conflicting | **External arbitration** |

### Shared Memory Space (EMP)

The EMP is not a database. It is a **living semantic CRDT document**.

Writing a belief into the EMP is emitting a signal — not storing a value. Every write triggers immediate propagation to agents that declared relevance to that domain. No polling. Only resonance.

```typescript
interface Belief {
  key: string
  value: any
  confidence: float      // semantic weight
  salience: float        // confidence × log(1 + access_count)
  source: AgentID
  timestamp: Timestamp
}
```

**Conflict resolution:** highest `salience` wins. Equal salience: most recent wins.

### Selective Resonance

An agent only reacts to beliefs that fall within its declared competence domain. It does not poll. It listens — exactly like a receptor cell ignores signals not addressed to it.

### Natural Separation

Clusters do not dissolve by unanimous vote. They dissolve **by entropy** — the same way tissue naturally comes apart when it no longer needs to maintain itself.

Separation triggers (first received wins):
1. Any participant signals task completion
2. EMP silence exceeds `SILENCE_THRESHOLD`
3. Guardian agent forces dissolution

---

## Fusion Classes

CRAF Clusters negotiate their latency contract at handshake time:

| Class | Target latency | Transport | Use case |
|-------|---------------|-----------|----------|
| **Synchronous** | < 100ms | WebSocket | Trading, real-time control |
| **Coherent** | < 2s | QUIC | Analysis, collaborative drafting, M&A |

---

## How It Works — The Fusion Cycle

```
  ┌─────────────┐
  │  DISCOVERY  │  Agents identify each other via A2A metadata
  └──────┬──────┘
         │
  ┌──────▼──────┐
  │  HANDSHAKE  │  FusionOffer → structural compatibility check → FusionAccept
  └──────┬──────┘
         │
  ┌──────▼──────┐
  │COLLABORATION│  Shared EMP · Selective resonance · Belief convergence
  └──────┬──────┘
         │
  ┌──────▼──────┐
  │ SEPARATION  │  Natural entropy signal → each agent generates its Fingerprint
  └─────────────┘
```

### Compatibility is Structural, Not Numerical

There is no magic threshold score. Compatibility between two agents is **binary**, validated by three declarative rules:

```
C(A, B) = VALID if:
  1. role(B) ∈ A.complements_with  AND  role(A) ∈ B.complements_with
  2. domain(A) ∩ domain(B) ≠ ∅  AND  domain(A) ≠ domain(B)
  3. ¬(role(B) ∈ A.conflicts_with)
```

Agents declare their own affinities. No external calibration required.

### The Fingerprint

When an agent leaves a Cluster, it does not copy the EMP — it generates a **differentiated imprint** of what it actually learned:

```json
{
  "fusion_id": "fus_8f3a9b",
  "insights": [
    {
      "key": "risk_assessment_q2",
      "value": "Tech risk underestimated by 15%",
      "confidence": 0.89,
      "integration_type": "reinforced | contradicted | novel"
    }
  ]
}
```

The fingerprint is then **digested** — filtered, reconciled, and indexed into the agent's persistent memory. Not copied. Learned.

---

## Example — Algorithmic Trading Cluster

Three agents. One Cluster. Synchronous class. Target: < 50ms end-to-end.

```
Fed raises rates 25bp

  MacroAnalyst ──writes──▶ EMP: belief(rates, "hike", 0.95)
                                       │
                              [resonance event]
                                       │
  RiskManager  ◀──────────────────────┘
  RiskManager  ──writes──▶ EMP: belief(var, "+12%", 0.92)
                                       │
                              [resonance event]
                                       │
  ExecutionTrader ◀───────────────────┘
  ExecutionTrader ──writes──▶ EMP: belief(strategy, "passive_limit", 0.88)

  MacroAnalyst signals TASK_COMPLETED
  → Natural separation
  → Each agent generates its own Fingerprint
  → Total time: < 50ms, zero A2A messages
```

---

## Specification

The full protocol specification lives in [`SPEC.md`](SPEC.md).

It covers:
- Complete EMP data model and operations
- Structural compatibility rules (section 4)
- Selective resonance model (section 5.2)
- Natural separation protocol (section 7)
- Security model and fault tolerance (section 8)
- Inter-cluster A2A protocol with full message schemas (section 9)
- Reference implementation pseudocode (section 11)

---

## Roadmap

**Phase 1 — Proof of Concept `0.1.0`** ← *current*
- [ ] Minimal Handshake protocol
- [ ] 2-agent EMP over WebSocket
- [ ] Event-driven model (no polling)
- [ ] Basic Fingerprint generation
- [ ] Natural separation by entropy
- [ ] Benchmark vs sequential A2A

**Phase 2 — Clustering `0.2.0`**
- [ ] Up to 5 agents per Cluster
- [ ] Fusion classes (Synchronous / Coherent)
- [ ] Natural garbage collection
- [ ] Guardian Agent
- [ ] Global reciprocity enforcement

**Phase 3 — Ecosystem `0.3.0`**
- [ ] Native inter-cluster A2A
- [ ] Decentralized reputation (DID)
- [ ] Byzantine fault tolerance
- [ ] Adapters: ADK, AutoGen, CrewAI, LangGraph

**Phase 4 — Standard `1.0.0`**
- [ ] A2A community submission
- [ ] IETF RFC draft
- [ ] Reference implementations: Python, TypeScript, Rust

---

## Status

CRAF is an **open specification** in active research. No production implementation exists yet. The goal of v0.1.0 is to validate the core thesis — that real-time cognitive fusion between complementary agents produces meaningfully better outcomes than sequential A2A — through a minimal proof of concept and benchmark.

Contributions, critiques, and forks are welcome.

---

## Contributing

This is an open specification. You can contribute by:

- Opening issues to challenge conceptual decisions
- Proposing alternative formulations for any protocol element
- Building a PoC implementation in any language
- Writing adapters for existing frameworks (ADK, AutoGen, CrewAI)

No contribution is too small. A well-argued critique of a single design decision is as valuable as a full implementation.

---

## References

- [Google A2A Protocol](https://google.github.io/A2A/) (2026)
- [Anthropic MCP](https://modelcontextprotocol.io) — Model Context Protocol
- [Yjs CRDT Framework](https://yjs.dev)
- [Automerge](https://automerge.org)
- Kai Li, *IVY: A Shared Virtual Memory System for Parallel Computing* (1986)
- TreadMarks DSM — Rice University
- Biological inspiration: Mycelial networks, Neural coherence, Hormonal signaling

---

<div align="center">

**MIT License** · Built by [Tryboy229](https://github.com/Tryboy229) / Nexus Studio

*"Thought is not sequential. It is distributed, parallel, and emergent.*
*CRAF is the protocol that lets agents think — truly — together."*

</div>
