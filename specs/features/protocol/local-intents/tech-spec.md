# Local Intents — Tech Spec

**Feature ID:** FEAT-protocol-local-intents
**PRD:** [specs/features/protocol/local-intents/prd.md](./prd.md)
**Status:** In Progress — Engineers to complete
**Last Updated:** 2026-04-09

---

## Requirement Summary

All requirements below must be satisfied before this feature is considered complete. Requirement IDs are the join key for the Product Readiness Review.

### Hard Requirements

| ID | Requirement |
|---|---|
| REQ-LI-H01 | **Atomicity:** All flash fulfillment operations must satisfy intent criteria within a single transaction — partial execution must revert entirely |
| REQ-LI-H02 | **Protocol compatibility:** Local intents must integrate as a new prover type within the existing Eco routes protocol framework without breaking cross-chain flows |
| REQ-LI-H03 | **Vault security:** Flash fulfillment must guarantee that intent vault funds are returned or correctly allocated within the same transaction — no net loss to the vault under any execution path |
| REQ-LI-H04 | **Open market access:** The local relayer gas market must be permissionless for relayer participation — no gatekeeping of who can bid on execution |
| REQ-LI-H05 | **Composability:** Same-chain orchestration must support arbitrary sequencing of supported on-chain actions (swaps, deposits, stakes) without hardcoding specific workflows |

### Required Analytics Events

| ID | Event | Payload |
|---|---|---|
| REQ-LI-001 | `local_intent.submitted` | intent type, origin chain, order size |
| REQ-LI-002 | `local_intent.flash_fulfilled` | vault draw amount, return status |
| REQ-LI-003 | `local_intent.orchestration_completed` | action sequence, final state |
| REQ-LI-004 | `local_intent.relayer_bid_accepted` | bid price, relayer ID, competing bid count |
| REQ-LI-005 | `local_intent.interop_stitched` | solver count, total fill size |

### Required Test Scenarios

| ID | Scenario |
|---|---|
| REQ-LI-010 | Application submits same-chain swap intent → resolves via local prover and completes atomically in one transaction |
| REQ-LI-011 | Application submits multi-step orchestration (swap then vault deposit) → both actions execute atomically, final state reflects completed deposit |
| REQ-LI-012 | Flash fulfillment transaction fails to satisfy intent criteria mid-execution → entire transaction reverts with no net vault loss |
| REQ-LI-013 | Order exceeds single solver capacity → interop stitching blends solver liquidity with orchestrated route to fill the full amount |
| REQ-LI-014 | Multiple relayers bid on the same intent → gas market selects lowest qualifying bid and winning relayer executes |

### Performance Thresholds

| ID | Threshold |
|---|---|
| REQ-LI-020 | Local intent transactions must not add meaningful latency compared to direct on-chain execution (measure: local intent settlement time vs. equivalent direct transactions) |
| REQ-LI-021 | Relayer gas market bidding round must complete within a timeframe that does not delay intent execution perceptibly (measure: time-to-bid-acceptance across sample of intents) |

### Acceptance Criteria (Machine-Checkable)

| ID | Given | When | Then |
|---|---|---|---|
| REQ-LI-030 | Application submits same-chain swap intent | Local prover processes it | Swap completes in single atomic transaction; `local_intent.submitted` and `local_intent.flash_fulfilled` fire |
| REQ-LI-031 | Multi-step orchestration intent (swap + deposit) | Orchestration engine executes | Both actions complete atomically; `local_intent.orchestration_completed` fires with correct action sequence |
| REQ-LI-032 | Flash fulfillment where intent criteria cannot be satisfied | Execution is attempted | Entire transaction reverts; vault balances unchanged |
| REQ-LI-033 | Order exceeds any single solver's liquidity | Interop stitching is available | Order fills by blending solver liquidity with orchestrated routes; `local_intent.interop_stitched` fires |
| REQ-LI-034 | Multiple relayers bidding on same-chain intent | Bidding round closes | Lowest qualifying bid wins; `local_intent.relayer_bid_accepted` fires with accurate competing bid count |
| REQ-LI-035 | Local prover type is registered in Eco routes | Cross-chain intent is submitted | Existing cross-chain provers handle it with no change in behavior |

---

## 1. Proposed Architecture

**[ENGINEERS: Fill in this section]**

Guiding questions:
- How should the local prover type integrate with the existing Eco routes prover framework?
- What is the contract architecture for flash fulfillment — how does the vault interaction work within a single transaction?
- How does the same-chain orchestration engine compose actions — smart contract router, meta-transaction builder, or something else?
- What is the trust model for the local relayer gas market — on-chain auction, off-chain commit-reveal, or hybrid?

---

## 2. Implementation Approach

**[ENGINEERS: Fill in this section]**

Guiding questions:
- Which chain(s) should local intents launch on first?
- What is the minimum viable feature set — flash fulfillment only, or orchestration + gas market together?
- How do we onboard initial relayers to seed the gas market?
- What SDK or API changes are needed for application developers to submit local intents?

---

## 3. Data Schema

**[ENGINEERS: Fill in this section]**

Guiding questions:
- What fields define a local intent (origin chain, action sequence, criteria, vault parameters)?
- What is the on-chain data structure for relayer bids in the gas market?
- How are interop stitching routes represented — what data links solver fills to orchestrated routes?

---

## 4. Performance Strategy

**[ENGINEERS: Fill in this section]**

Guiding questions:
- What is the gas overhead of flash fulfillment vs. direct execution?
- How can the orchestration engine minimize gas costs for multi-step actions?
- What is the latency impact of the relayer bidding round on end-to-end intent settlement?

---

## 5. Risks & Mitigations

**[ENGINEERS: Fill in this section]**

Guiding questions:
- Smart contract security risks of flash fulfillment — can vault funds be drained through reentrancy or criteria manipulation?
- What happens if the relayer gas market has insufficient bidders — does execution stall or fall back to a default relayer?
- How do we handle orchestration failures mid-sequence given atomicity constraints?
- What is the economic risk if interop stitching creates MEV opportunities?

---

## 6. Implementation Phases

**[ENGINEERS: Fill in this section]**

Guiding questions:
- What is the critical path — does the local prover type need to ship before the gas market?
- Which components can be developed in parallel (vault integration, orchestration engine, gas market)?
- What defines the MVP milestone vs. general availability?
- What testing and audit requirements gate each phase?

---

*Scaffolded by Spec Lifecycle Manager — Paperclip [automated]*
