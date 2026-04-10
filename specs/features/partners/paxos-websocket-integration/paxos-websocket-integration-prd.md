# Paxos WebSocket Intent Settlement Integration PRD

**Feature ID:** FEAT-partners-paxos-websocket-integration

**Status:** Draft

**Last Updated:** 2026-04-09



---



## PART 1: PRODUCT REQUIREMENTS



### Problem

- Paxos Labs needs a programmatic integration with Eco to execute intent-based trades and settlements, but no API or streaming interface currently exists for external partners to submit intents and receive fulfillment commitments

- Without a standardized integration flow, Paxos engineering cannot scope or begin building their side of the integration, blocking the partnership from progressing to MVP

- Manual or ad-hoc settlement coordination between Eco and Paxos would be operationally unsustainable and error-prone as trade volume scales

- Paxos requires a clear technical specification of the intent lifecycle — from submission through fulfillment and settlement — before committing engineering resources to integration work



### Solution

- A persistent WebSocket connection that allows Paxos to stream trade/settlement intents to Eco in real-time

- An intent evaluation system that receives intents, evaluates them against vault liquidity and fulfillment parameters, and returns a fulfillment commitment (promise) with a designated vault address for settlement

- A settlement flow where Paxos sends funds to the returned vault address after Eco has fulfilled the intent, completing the trade lifecycle

- Support for both Ethereum and Solana chains, enabling Paxos to settle on either VM from day one of the MVP



### Value/Impact

- Unlocks Paxos Labs as a new integration partner, expanding Eco's settlement network to include a major regulated infrastructure provider

- Provides a reusable WebSocket-based intent streaming pattern that can serve as the template for future partner integrations

- Reduces time-to-integration for Paxos engineering by giving them a clear, self-contained technical specification to build against

- Establishes the intent-promise-settle lifecycle as the canonical external integration pattern for Eco



### User Stories



#### As a Paxos Integration Engineer

- I want to open a persistent WebSocket connection to Eco, so that I can stream trade intents without managing individual HTTP request lifecycles

- I want to receive a fulfillment promise and vault address in response to each intent, so that I know where and when to settle funds

- I want clear error responses when an intent cannot be fulfilled (e.g., insufficient vault liquidity), so that I can implement appropriate retry or fallback logic on the Paxos side



#### As an Eco Vault Operator

- I want incoming intents to be evaluated against current vault liquidity and fulfillment parameters, so that Eco only commits to intents it can actually fulfill

- I want the system to return a specific vault address per fulfillment, so that settlement funds are routed correctly and can be reconciled



#### As a Paxos Settlement Operator

- I want a clearly defined post-fulfillment settlement step where I send funds to the vault address, so that the trade lifecycle has an unambiguous completion point

- I want the integration to work on both Ethereum and Solana, so that I can settle on whichever chain is operationally preferred for a given trade



#### As an Eco Product Manager

- I want the integration to operate without pre-funding checks on Eco's side (covered by partnership agreement protections), so that the MVP flow is streamlined for Paxos

- I want the MVP to accept both USDG and vault shares as collateral (vault shares as a temporary exception), so that Paxos is not blocked by asset restrictions during initial integration



### Services Impacted

- **WebSocket Gateway** (new inbound connection surface for Paxos intent streaming — manages persistent connections, authentication, and message routing)

- **Intent Evaluation Engine** (receives intents from the WebSocket layer, evaluates against vault state and fulfillment parameters, produces promise responses)

- **Vault Service** (queried for liquidity and fulfillment capacity; returns designated vault address for each accepted intent)

- **Settlement Reconciliation** (tracks post-fulfillment fund arrivals at vault addresses; confirms trade lifecycle completion)

- **Cross-VM Bridge** (enables settlement on both Ethereum and Solana; must support USDG and vault share asset types)



### Hard Requirements & Constraints

- **No pre-funding check for Paxos:** The MVP explicitly waives Eco-side pre-funding verification for Paxos, relying instead on partnership agreement protections — this is a special exception, not a general policy

- **Partnership agreement required:** The integration must not go live until a signed partnership agreement with explicit pre-funding protection clauses is in place

- **Cross-chain support from MVP launch:** Both Ethereum and Solana must be supported at launch — Paxos will choose their preferred chain, but both must be available

- **Vault shares as temporary collateral:** The MVP accepts vault shares as collateral in addition to USDG; this exception should be removable without architectural changes when the long-term policy (underlying asset only) takes effect

- **Hack-first / pre-prod approach:** The MVP follows a pre-production development approach — no formal production pipeline is required at launch, but the architecture must not preclude productionization

- **NDA dependency:** Paxos legal (Dougie) is initiating the NDA process; technical integration details must remain confidential under NDA terms



### Success Criteria

**Paxos engineering can build their integration from this specification alone**
- Measure: Paxos confirms they have sufficient detail to scope integration work after receiving the one-pager
- Measure: No follow-up questions from Paxos that require architectural redesign of the flow

**End-to-end intent lifecycle completes in pre-production**
- Measure: A test intent submitted via WebSocket results in a promise, vault address, fulfillment, and settlement confirmation in a pre-prod environment

**Cross-chain settlement works on both Ethereum and Solana**
- Measure: Successful settlement observed on each chain independently in pre-prod testing

**Partnership agreement is in place before any live traffic**
- Measure: Signed agreement on file with Eco legal before the integration accepts real intents



### Verification Criteria



#### Required Analytics Events

- `REQ-PAXOS-WS-001`: `intent.received` — Fires when an intent is received via the WebSocket connection from Paxos; tracks intent ID, asset type, chain, and timestamp
- `REQ-PAXOS-WS-002`: `intent.evaluated` — Fires when the intent evaluation engine produces a decision (accept or reject); tracks intent ID, decision, rejection reason if applicable
- `REQ-PAXOS-WS-003`: `promise.issued` — Fires when a fulfillment promise and vault address are returned to Paxos; tracks intent ID, vault address, promised fulfillment parameters
- `REQ-PAXOS-WS-004`: `settlement.received` — Fires when funds arrive at the designated vault address post-fulfillment; tracks intent ID, vault address, amount, chain, asset type
- `REQ-PAXOS-WS-005`: `lifecycle.completed` — Fires when the full intent→promise→fulfill→settle cycle completes; tracks intent ID, total lifecycle duration



#### Required Test Scenarios

- `REQ-PAXOS-WS-010`: Happy path — Paxos submits intent via WebSocket, Eco returns promise + vault address, Paxos settles to vault address — Expected: lifecycle completes with all analytics events firing in sequence
- `REQ-PAXOS-WS-011`: Insufficient liquidity — Paxos submits intent that exceeds vault liquidity — Expected: rejection response with clear reason, no promise or vault address issued
- `REQ-PAXOS-WS-012`: WebSocket disconnect during intent evaluation — Expected: intent is not partially committed; Paxos can resubmit on reconnect without duplicate processing
- `REQ-PAXOS-WS-013`: Settlement on Ethereum chain — Expected: funds arrive at vault address on Ethereum; settlement.received event fires with chain=ethereum
- `REQ-PAXOS-WS-014`: Settlement on Solana chain — Expected: funds arrive at vault address on Solana; settlement.received event fires with chain=solana
- `REQ-PAXOS-WS-015`: Vault shares as collateral — Expected: intent specifying vault shares as asset type is accepted during MVP; promise issued normally
- `REQ-PAXOS-WS-016`: Settlement timeout — Paxos does not settle within expected window after fulfillment — Expected: timeout event fires, intent marked as expired, operational alert generated



#### Performance Thresholds

- `REQ-PAXOS-WS-020`: Intent-to-promise latency — Must be fast enough that Paxos does not experience the WebSocket connection as unresponsive; measured by comparing p95 latency against baseline WebSocket round-trip benchmarks in pre-prod
- `REQ-PAXOS-WS-021`: WebSocket connection stability — Persistent connections must remain active without unexpected drops under normal operating conditions; measured by monitoring connection uptime and reconnection frequency over a sustained test period



#### Acceptance Criteria (Machine-Checkable)

- `REQ-PAXOS-WS-030`: Given a valid WebSocket connection, When Paxos sends a well-formed intent message, Then Eco returns a promise response containing a fulfillment commitment and vault address within the expected latency threshold
- `REQ-PAXOS-WS-031`: Given an intent that exceeds current vault liquidity, When the intent is evaluated, Then a rejection response is returned with a machine-readable reason code and no vault address is issued
- `REQ-PAXOS-WS-032`: Given a promise has been issued and Eco has fulfilled, When Paxos sends funds to the returned vault address, Then a settlement.received event fires and the lifecycle is marked complete
- `REQ-PAXOS-WS-033`: Given the MVP configuration, When an intent specifies vault shares as the asset type, Then the intent is accepted and processed identically to USDG intents
- `REQ-PAXOS-WS-034`: Given a settlement request targeting Ethereum, When settlement completes, Then the settlement is confirmed on-chain and the analytics event records chain=ethereum
- `REQ-PAXOS-WS-035`: Given a settlement request targeting Solana, When settlement completes, Then the settlement is confirmed on-chain and the analytics event records chain=solana
- `REQ-PAXOS-WS-036`: Given no signed partnership agreement is on file, When the system is queried for readiness, Then the integration rejects live traffic and returns a configuration error



---



## PART 2: TECHNICAL PLAN (Engineers to Fill In)



### 1. Proposed Architecture

**[BLANK - Engineers to propose technical design]**

Considerations for engineering team:
- How should the WebSocket gateway authenticate and authorize Paxos connections?
- Should intent evaluation be synchronous (inline with WebSocket message handling) or asynchronous (queued)?
- How does the vault service allocate and return a specific vault address per intent — is it a new address each time or a reusable pool?
- What message format (JSON, protobuf, etc.) should the WebSocket protocol use?

### 2. Implementation Approach

**[BLANK - Engineers to propose build strategy]**

Considerations for engineering team:
- Can the existing vault infrastructure support the intent evaluation queries, or does a new read path need to be built?
- How should the cross-VM (Ethereum + Solana) settlement be abstracted so adding chains later is straightforward?
- What is the minimum viable WebSocket server implementation for the hack-first/pre-prod phase?
- How should the temporary vault-shares collateral exception be implemented so it can be removed cleanly?

### 3. Data Schema

**[BLANK - Engineers to define data structures]**

Considerations for engineering team:
- What fields define an intent message (intent ID, asset type, amount, chain, sender identity)?
- What fields define a promise response (promise ID, vault address, fulfillment parameters, expiry)?
- How are settlement confirmations recorded and linked back to the originating intent?

### 4. Performance Strategy

**[BLANK - Engineers to explain how to meet performance constraints]**

Considerations for engineering team:
- What are the expected intent volumes from Paxos during MVP and how does that affect WebSocket connection management?
- How should vault liquidity queries be optimized to keep intent-to-promise latency low?
- Should there be rate limiting on the WebSocket connection to protect vault service availability?

### 5. Risks & Mitigations

**[BLANK - Engineers to identify risks and mitigations]**

Considerations for engineering team:
- What happens if vault liquidity changes between promise issuance and settlement arrival?
- How do we handle the no-pre-funding exception securely — what prevents abuse if the partnership agreement is breached?
- What are the failure modes for cross-chain settlement and how are they detected?
- How do we ensure NDA-protected technical details don't leak into public APIs or logs?

### 6. Implementation Phases

**[BLANK - Engineers to break into phases with dependencies]**

Considerations for engineering team:
- What is the critical path to having a working pre-prod demo for the engineering kickoff call?
- Can WebSocket infrastructure and intent evaluation be developed in parallel?
- What phase should cross-chain support be validated — alongside core flow or as a follow-on?
- When should the transition from hack-first to production pipeline be planned?



---



## Appendix: Context for Engineers

**Partner:** Paxos Labs (paxoslabs.com) — regulated financial infrastructure provider
**Meeting source:** Paxos Labs <> Eco, 2026-04-08
**Commitment owner:** Eco (Shahrukh Rao)
**Target delivery:** One-pager to Paxos by ~2026-04-22; engineering kickoff call to follow
**Eco engineering availability:** Week of 2026-04-21

**Key decisions from meeting:**
- WebSocket architecture selected for MVP (Paxos streams intent → Eco returns promise + vault address → Paxos settles post-fulfillment)
- Pre-funding waived for Paxos (special exception; requires partnership agreement protections)
- MVP accepts USDG or vault shares (vault shares temporary)
- Both Ethereum and Solana supported from MVP
- Hack-first / pre-prod approach — no formal production pipeline yet
- Size limits deferred to post-MVP
- Paxos to confirm MVP collateral asset and first chain priority by ~2026-04-18
- NDA process initiated via Paxos legal (Dougie)

**Paxos commitments:**
- Report back on internal engineering timeline (~2026-04-18, Dashan)
- Decide MVP collateral asset and first chain (~2026-04-18, Dashan)
- Kick off NDA process (Dougie)
- Provide guardrails: max order size limits and clip-based processing (TBD at kickoff)
- B2C2 integration for additional liquidity (in progress)
