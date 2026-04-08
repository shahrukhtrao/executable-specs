# Local Intents PRD

**Feature ID:** FEAT-protocol-local-intents

**Status:** Draft

**Last Updated:** 2026-04-08

---

## PART 1: PRODUCT REQUIREMENTS

### Problem

- Cross-chain intent protocols currently lack a reliable mechanism for same-chain operations, forcing simple swaps and vault deposits through cross-chain pathways that add unnecessary complexity and failure modes
- Applications needing multi-step on-chain actions (swap then deposit, swap then stake) cannot orchestrate these atomically through existing intent infrastructure, requiring custom integration work
- Order size is constrained by individual solver liquidity — there is no standard way to blend solver capital with orchestrated routes to fill larger orders
- Same-chain transaction execution has no open marketplace for gas bidding, leaving relayer selection opaque and uncompetitive

### Solution

- A new prover type in the Eco routes protocol that enables local (same-chain) intent resolution alongside existing cross-chain provers
- Flash fulfillment capability that draws funds from the intent vault and executes operations atomically within a single transaction, provided intent criteria are satisfied — similar to flash loan mechanics
- Same-chain orchestration that composes swaps, vault deposits, and other multi-step actions into a single atomic transaction on the origin chain
- Interop stitching that blends solver liquidity with orchestrated routes, enabling applications to fill larger orders than any single solver could handle alone
- A local relayer gas market — an open marketplace where relayers bid competitively to execute same-chain intents

### Value/Impact

- Better reliability for same-chain operations by eliminating unnecessary cross-chain round-trips and executing atomically within a single transaction
- More complex orchestration patterns become possible — applications can compose multi-step on-chain actions (swap + deposit, swap + stake) without custom integration
- Larger order sizes through interop stitching, blending solver liquidity with protocol-orchestrated routes to fill orders that exceed individual solver capacity
- Competitive gas pricing through the open relayer market, reducing execution costs for same-chain intents

### User Stories

#### As an Application Developer

- I want to submit same-chain intents through the Eco routes protocol, so that my users can execute swaps, deposits, and multi-step actions reliably without cross-chain complexity
- I want to compose multi-step on-chain actions into a single atomic transaction, so that my application can offer complex workflows (swap-then-deposit, swap-then-stake) without custom orchestration code
- I want access to interop stitching for large orders, so that my application can fill order sizes beyond what a single solver provides

#### As a Solver

- I want to compete in the local relayer gas market by bidding on same-chain intent execution, so that I can earn fees for relaying transactions
- I want my liquidity to be blended with orchestrated routes via interop stitching, so that I can participate in filling larger orders than I could alone
- I want clear intent criteria for flash fulfillment, so that I understand the conditions under which vault funds are used within a transaction

#### As an Eco Protocol Team Member

- I want local intents to extend the existing prover framework, so that the protocol supports same-chain operations without fragmenting the architecture
- I want observability into the local relayer gas market, so that I can monitor execution quality and market health

#### As an End User (via Applications)

- I want same-chain operations to complete atomically in a single transaction, so that I am not exposed to partial execution or intermediate failure states
- I want competitive gas pricing on same-chain transactions, so that I pay fair execution costs

### Services Impacted

- **Eco Routes Protocol** (existing prover framework — adds a new local prover type for same-chain intent resolution)
- **Intent Vault** (existing — flash fulfillment draws and returns funds atomically within transactions)
- **Solver/Filler Infrastructure** (existing — gains interop stitching capability to blend liquidity with orchestrated routes)
- **NEW: Local Relayer Gas Market** (to be built — open marketplace for competitive same-chain execution bidding)
- **NEW: Same-Chain Orchestration Engine** (to be built — composes multi-step on-chain actions into atomic transactions)

### Hard Requirements & Constraints

- **Atomicity:** All flash fulfillment operations must satisfy intent criteria within a single transaction — partial execution must revert entirely
- **Protocol compatibility:** Local intents must integrate as a new prover type within the existing Eco routes protocol framework without breaking cross-chain flows
- **Vault security:** Flash fulfillment must guarantee that intent vault funds are returned or correctly allocated within the same transaction — no net loss to the vault under any execution path
- **Open market access:** The local relayer gas market must be permissionless for relayer participation — no gatekeeping of who can bid on execution
- **Composability:** Same-chain orchestration must support arbitrary sequencing of supported on-chain actions (swaps, deposits, stakes) without hardcoding specific workflows

### Success Criteria

**Same-chain operations execute reliably through the local intent pathway**
- Measure: Track success rate of local intent transactions vs. equivalent operations routed through cross-chain pathways over a representative period
- Measure: Monitor revert rate for flash fulfillment transactions to validate atomicity guarantees

**Applications adopt local intents for same-chain workflows**
- Measure: Count distinct applications submitting local intents per period and track growth in local intent volume relative to total protocol volume

**Order sizes increase through interop stitching**
- Measure: Compare median and p95 order sizes for routes using interop stitching vs. single-solver fills over equivalent token pairs

**The relayer gas market achieves competitive pricing**
- Measure: Track the number of active relayer bidders per intent and compare realized gas costs against a baseline of non-competitive (single relayer) execution

### Verification Criteria

#### Required Analytics Events

- `REQ-LI-001`: `local_intent.submitted` — Fires when an application submits a same-chain intent; includes intent type, origin chain, and order size
- `REQ-LI-002`: `local_intent.flash_fulfilled` — Fires when flash fulfillment completes successfully within a transaction; includes vault draw amount and return status
- `REQ-LI-003`: `local_intent.orchestration_completed` — Fires when a multi-step same-chain orchestration executes; includes action sequence and final state
- `REQ-LI-004`: `local_intent.relayer_bid_accepted` — Fires when a relayer wins a gas market bid; includes bid price, relayer ID, and competing bid count
- `REQ-LI-005`: `local_intent.interop_stitched` — Fires when interop stitching blends solver liquidity with an orchestrated route; includes solver count and total fill size

#### Required Test Scenarios

- `REQ-LI-010`: Application submits a same-chain swap intent — intent resolves via local prover and completes atomically in one transaction
- `REQ-LI-011`: Application submits a multi-step orchestration (swap then vault deposit) — both actions execute atomically and the final state reflects completed deposit
- `REQ-LI-012`: Flash fulfillment transaction fails to satisfy intent criteria mid-execution — entire transaction reverts with no net vault loss
- `REQ-LI-013`: An order exceeds single solver capacity — interop stitching blends solver liquidity with orchestrated route to fill the full amount
- `REQ-LI-014`: Multiple relayers bid on the same intent — the gas market selects the lowest qualifying bid and the winning relayer executes

#### Performance Thresholds

- `REQ-LI-020`: Local intent transactions must not add meaningful latency compared to direct on-chain execution — measured by comparing local intent settlement time against equivalent direct transactions on the same chain
- `REQ-LI-021`: The relayer gas market bidding round must complete within a timeframe that does not delay intent execution perceptibly — measured by tracking time-to-bid-acceptance across a sample of intents

#### Acceptance Criteria (Machine-Checkable)

- `REQ-LI-030`: Given an application submitting a same-chain swap intent, When the local prover processes it, Then the swap completes in a single atomic transaction and `local_intent.submitted` and `local_intent.flash_fulfilled` events fire
- `REQ-LI-031`: Given a multi-step orchestration intent (swap + deposit), When the orchestration engine executes it, Then both actions complete atomically and `local_intent.orchestration_completed` fires with the correct action sequence
- `REQ-LI-032`: Given a flash fulfillment where intent criteria cannot be satisfied, When execution is attempted, Then the entire transaction reverts and vault balances remain unchanged
- `REQ-LI-033`: Given an order that exceeds any single solver's liquidity, When interop stitching is available, Then the order fills by blending solver liquidity with orchestrated routes and `local_intent.interop_stitched` fires
- `REQ-LI-034`: Given multiple relayers bidding on a same-chain intent, When the bidding round closes, Then the lowest qualifying bid wins and `local_intent.relayer_bid_accepted` fires with accurate competing bid count
- `REQ-LI-035`: Given the local prover type is registered in Eco routes, When a cross-chain intent is submitted, Then existing cross-chain provers handle it with no change in behavior

---

## PART 2: TECHNICAL PLAN (Engineers to Fill In)

### 1. Proposed Architecture

**[BLANK - Engineers to propose technical design]**

Considerations for engineering team:
- How should the local prover type integrate with the existing Eco routes prover framework?
- What is the contract architecture for flash fulfillment — how does the vault interaction work within a single transaction?
- How does the same-chain orchestration engine compose actions — is it a smart contract router, a meta-transaction builder, or something else?
- What is the trust model for the local relayer gas market — on-chain auction, off-chain commit-reveal, or hybrid?

### 2. Implementation Approach

**[BLANK - Engineers to propose build strategy]**

Considerations for engineering team:
- Which chain(s) should local intents launch on first?
- What is the minimum viable feature set — flash fulfillment only, or orchestration + gas market together?
- How do we onboard initial relayers to seed the gas market?
- What SDK or API changes are needed for application developers to submit local intents?

### 3. Data Schema

**[BLANK - Engineers to define data structures]**

Considerations for engineering team:
- What fields define a local intent (origin chain, action sequence, criteria, vault parameters)?
- What is the on-chain data structure for relayer bids in the gas market?
- How are interop stitching routes represented — what data links solver fills to orchestrated routes?

### 4. Performance Strategy

**[BLANK - Engineers to explain how to meet performance constraints]**

Considerations for engineering team:
- What is the gas overhead of flash fulfillment vs. direct execution?
- How can the orchestration engine minimize gas costs for multi-step actions?
- What is the latency impact of the relayer bidding round on end-to-end intent settlement?

### 5. Risks & Mitigations

**[BLANK - Engineers to identify risks and mitigations]**

Considerations for engineering team:
- What are the smart contract security risks of flash fulfillment — can vault funds be drained through reentrancy or criteria manipulation?
- What happens if the relayer gas market has insufficient bidders — does execution stall or fall back to a default relayer?
- How do we handle orchestration failures mid-sequence given atomicity constraints?
- What is the economic risk if interop stitching creates MEV opportunities?

### 6. Implementation Phases

**[BLANK - Engineers to break into phases with dependencies]**

Considerations for engineering team:
- What is the critical path — does the local prover type need to ship before the gas market?
- Which components can be developed in parallel (vault integration, orchestration engine, gas market)?
- What defines the MVP milestone vs. general availability?
- What testing and audit requirements gate each phase?

---

## Appendix: Context for Engineers

Local Intents represent a new prover type in the Eco routes protocol that enables same-chain intent resolution. The core mechanism — flash fulfillment — is analogous to flash loans: funds are drawn from the intent vault and operations are executed atomically within a single transaction, provided intent criteria are satisfied. This unlocks same-chain orchestration (composing swaps, deposits, and other actions), interop stitching (blending solver liquidity with orchestrated routes for larger fills), and a competitive relayer gas market for same-chain execution. The two primary customer segments are applications integrating local intents and solvers participating in the relay market.
