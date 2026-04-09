---
feature: local-intents
version: v0.1.0
status: draft
approved_by: null
approved_date: null
---

# Local Intents — Product Launch Brief

**Status:** Draft
**Author:** Shahrukh Rao
**CMO Review:** Pending
**Template Version:** 2.0

---

## Launch Summary

**Headline:** Eco launches Local Intents to enable applications to execute same-chain operations with atomic reliability and competitive gas pricing

**Summary:** Eco's Local Intents introduces a new prover type in the Eco routes protocol that brings intent-based execution to same-chain operations. Applications can now compose multi-step on-chain actions — swaps, deposits, staking — into single atomic transactions, fill larger orders by blending solver liquidity with orchestrated routes, and access competitive gas pricing through an open relayer marketplace. Local Intents eliminates the need to route simple same-chain operations through cross-chain pathways, reducing complexity and failure modes for application developers and their end users.

---

## 1. Product Overview

**One-liner:** Local Intents enables same-chain intent resolution with flash fulfillment, atomic orchestration, and a competitive relayer gas market within the Eco routes protocol.

**Category:** Infrastructure Primitive
**Type:** Intent execution layer
**Business Model:** Transaction fees from intent execution and relayer gas market participation; increased protocol volume drives ecosystem value.

**ELI5:** Right now, if an app wants to do a simple swap on one blockchain, it often has to go through a complicated cross-chain process — like taking a detour through another city to get to the shop next door. Local Intents lets apps do same-chain transactions directly, reliably, and cheaply, all in one step.

**How components work together:**
A new **local prover** integrates into the existing Eco routes prover framework to handle same-chain intents. When an application submits a local intent, **flash fulfillment** draws funds from the intent vault and executes operations atomically within a single transaction — similar to flash loan mechanics. The **same-chain orchestration engine** composes multi-step actions (swap then deposit, swap then stake) into that atomic transaction. For orders larger than any single solver can fill, **interop stitching** blends solver liquidity with orchestrated routes. The **local relayer gas market** provides an open, permissionless marketplace where relayers bid competitively to execute same-chain intents, ensuring fair gas pricing.

---

## Company Perspective

**Why we are building this:**
> "[NEEDS INPUT: leadership quote on strategic rationale for Local Intents — should connect to Eco's vision of becoming the universal intent infrastructure layer and explain why same-chain operations are the natural next step after cross-chain success]"

-- [Name], [Role] at Eco

[ENRICHABLE: additional detail available if transcript is provided]

---

## 2. Target Audience

**Company Type:** Applications building on-chain products that require reliable same-chain transaction execution — DeFi protocols, wallets, aggregators, and any application integrating swaps, deposits, or multi-step workflows.

**Decision-makers:** Engineering leads and protocol architects at application teams who choose intent infrastructure; solver operators evaluating new revenue opportunities.

**Jobs to be Done:**
- "I need to execute same-chain swaps and deposits reliably without routing through cross-chain infrastructure"
- "I need to compose multi-step on-chain actions into a single atomic transaction without building custom orchestration"
- "I need to fill large orders that exceed any single solver's liquidity capacity"

**Use Cases:**
- A DeFi application submitting same-chain swap intents that resolve atomically via the local prover, eliminating cross-chain round-trip complexity
- An application composing a swap-then-vault-deposit workflow into a single atomic transaction through the orchestration engine
- A solver participating in the relayer gas market to earn execution fees by bidding competitively on same-chain intents
- An aggregator filling a large order through interop stitching, blending its solver liquidity with protocol-orchestrated routes

---

## 3. Problems and Pain Points

**Top problems (ranked by severity):**
1. **Same-chain operations forced through cross-chain pathways** — Impact: unnecessary complexity, additional failure modes, and higher latency for operations that should be simple on-chain transactions. Applications absorb integration burden and users experience unreliable execution.
2. **No atomic multi-step orchestration through intent infrastructure** — Impact: applications needing swap-then-deposit or swap-then-stake workflows must build custom integration code, increasing development cost and exposing users to partial execution risk.
3. **Order size constrained by individual solver liquidity** — Impact: large orders cannot be filled through a single solver, limiting the size of transactions applications can reliably execute and leaving value on the table.
4. **No open marketplace for same-chain gas bidding** — Impact: relayer selection is opaque, gas pricing is uncompetitive, and execution costs for same-chain intents are higher than they need to be.

**Why Alternatives Fail:**
- Direct on-chain execution (without intents) fails to solve problems #2 and #3 because it offers no orchestration composability or liquidity aggregation — each application must build these capabilities from scratch
- Existing cross-chain intent protocols fail to solve problem #1 because they route same-chain operations through cross-chain pathways, adding unnecessary hops and failure points
- Custom relayer arrangements fail to solve problem #4 because they lack open competition, resulting in opaque pricing and no market pressure on gas costs

**Emotional Tension:** Application developers are frustrated by the absurdity of routing a simple same-chain swap through cross-chain infrastructure — it feels like overkill, adds risk they shouldn't have to manage, and slows down their product development.

---

## 4. Competitive Landscape

**Direct Competitors:** Cross-chain intent protocols (Li.Fi, Socket, Across, LayerZero, Wormhole, Axelar) that may extend to same-chain use cases.

**Secondary Competitors:** DEX aggregators (1inch, Paraswap, 0x) that handle same-chain swaps but lack intent-based orchestration and atomic multi-step composition.

**Indirect Competitors:** Application-specific relayer networks and custom transaction builders that teams build in-house to handle same-chain workflows.

[ENRICHABLE: additional competitive detail available if transcript is provided]

---

## 5. Differentiation

**What we do differently:** Local Intents extends the existing Eco routes prover framework with a native same-chain prover type — it is not a bolt-on feature but a first-class primitive in the protocol. Flash fulfillment provides atomic guarantees analogous to flash loans, and interop stitching is a novel mechanism that blends solver liquidity with orchestrated routes.

**Why it's better:**
- **Atomic same-chain execution** — flash fulfillment guarantees all-or-nothing transaction execution, eliminating partial execution risk that plagues non-intent-based approaches
- **Composable orchestration** — multi-step actions are composed without hardcoding specific workflows, supporting arbitrary sequencing of supported on-chain actions
- **Interop stitching** — no other protocol offers a standard mechanism to blend solver liquidity with orchestrated routes for larger fills
- **Open relayer gas market** — permissionless bidding creates real price competition, unlike closed relayer networks

---

## 6. Switching Dynamics

**What would get someone to switch:**
> "You're already routing same-chain operations through cross-chain infra that adds latency and failure modes. Local Intents gives you atomic execution, multi-step composition, and competitive gas pricing — all through the same Eco routes protocol you may already integrate with. One prover type, no cross-chain detour."

**Key terms to introduce:**
- **Local Intents** — same-chain intent resolution within the Eco routes protocol
- **Flash fulfillment** — atomic execution using intent vault funds within a single transaction
- **Interop stitching** — blending solver liquidity with orchestrated routes for larger order fills
- **Local relayer gas market** — open, permissionless marketplace for competitive same-chain execution bidding

---

## 7. Customer Language

**Verbatim phrases customers use:**
[NEEDS INPUT: exact customer/partner language from interviews or transcript — what words do application developers use when describing same-chain execution pain points?]

[ENRICHABLE: additional detail available if transcript is provided]

**Words to use:** atomic execution, same-chain, composable, intent resolution, flash fulfillment, orchestration, competitive gas pricing, permissionless

**Words to avoid:** bridging (implies cross-chain), relay (too generic — use "relayer" specifically), simple (undersells the technical sophistication)

**Glossary:**
- **Local prover** — a new prover type in Eco routes that resolves intents on the same chain where they originate
- **Flash fulfillment** — mechanism that draws funds from the intent vault and executes operations atomically within one transaction, returning or allocating funds per intent criteria
- **Interop stitching** — technique that combines solver liquidity with protocol-orchestrated routes to fill orders larger than any single solver could handle
- **Same-chain orchestration** — composing multiple on-chain actions (swap, deposit, stake) into a single atomic transaction

---

## Customer Voice

**Hypothetical customer quote:**
> "We were routing same-chain swaps through cross-chain infrastructure and dealing with unnecessary failure modes and latency. With Local Intents, our swap-then-deposit workflows execute atomically in a single transaction. Flash fulfillment means our users never see partial executions, and interop stitching lets us handle order sizes we couldn't fill before."

-- Protocol Lead, DeFi Application

[ENRICHABLE: additional detail available if transcript is provided — customer voice section would benefit from S7 verbatim language]

---

## 8. Proof Points

**Metrics:**
[NEEDS INPUT: specific performance benchmarks — expected latency reduction vs. cross-chain routing, expected gas cost savings from relayer market competition, target success rate for local intent transactions]

**Logos:**
[NEEDS INPUT: early adopter applications or integration partners committed to Local Intents]

**Testimonials:**
[NEEDS INPUT: quotes from application developers or solver operators who have validated the concept]

**Value themes:**
- Reliability: atomic execution eliminates partial failure states
- Simplicity: same-chain operations stay on-chain without cross-chain detours
- Scale: interop stitching enables larger order fills than single-solver approaches
- Cost efficiency: open relayer gas market drives competitive pricing

---

## 9. Goals

**Business Goal:** Establish Local Intents as the default same-chain execution primitive for applications already using or evaluating the Eco routes protocol, increasing total protocol volume and expanding the solver/relayer ecosystem.

**Key Conversion Action:** Application developers integrate Local Intents for same-chain workflows; solvers register as relayers in the local gas market.

**Current Metrics:**
[NEEDS INPUT: baseline metrics — current same-chain operation volume routed through cross-chain pathways, current solver participation rates, current average gas costs for same-chain execution]

---

## Gaps Summary

| Section | Status | Needed |
|---------|--------|--------|
| Launch Summary | Complete | — |
| Product Overview | Complete | — |
| Company Perspective | Gap | Leadership quote on strategic rationale |
| Target Audience | Complete | — |
| Problems and Pain Points | Complete | — |
| Competitive Landscape | Partial | Transcript color on competitive positioning |
| Differentiation | Complete | — |
| Switching Dynamics | Complete | — |
| Customer Language | Partial | Verbatim customer phrases from interviews |
| Customer Voice | Partial | S7 verbatim language to ground the quote |
| Proof Points | Gap | Metrics, logos, testimonials |
| Goals | Partial | Baseline metrics |
| FAQ | Skipped | Not tagged partner-facing; revisit if needed |

---

## Linkage to Product Narrative

**Position in narrative:** Infrastructure Primitive — Local Intents sits at the base of the Eco protocol stack, extending the prover framework to cover same-chain resolution. It enables higher-level capabilities (multi-step orchestration, interop stitching) and products (application-specific workflows) built on top.

**Related features:** Cross-chain intent resolution (existing Eco routes provers), intent vault infrastructure, solver/filler ecosystem.
