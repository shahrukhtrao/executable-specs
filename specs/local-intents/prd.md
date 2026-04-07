# Local Intents PRD

**Feature ID:** FEAT-infra-local-intents

**Status:** Draft

**Last Updated:** 2026-04-07



---



## PART 1: PRODUCT REQUIREMENTS



### Problem

- Agent-based workflows currently route all intent resolution through centralized cloud services, adding latency and creating a single point of failure for time-sensitive operations

- When the central intent service is degraded or unreachable, agents cannot process even simple, well-understood requests — causing full workflow stalls

- Intent routing logic is opaque to downstream consumers; services cannot inspect, override, or extend intent resolution without modifying the central service

- Cross-agent coordination suffers when every intent must round-trip through a shared resolver, limiting throughput during burst workloads



### Solution

- A local intent resolution layer that allows agents and services to register, match, and fulfill intents within their own execution context before falling back to centralized resolution

- A declarative intent schema that services publish to advertise which intents they can resolve locally, enabling automatic discovery and matching

- A fallback chain that attempts local resolution first, then escalates to the central intent service only when no local handler matches — preserving existing behavior as the default

- An intent audit log that records resolution path (local vs. centralized) for every intent, enabling observability into routing decisions



### Value/Impact

- Reduced end-to-end latency for common intents by avoiding unnecessary network hops to the central service

- Increased system resilience — agents continue to resolve high-frequency intents during central service degradation

- Improved extensibility — teams can register domain-specific intents without modifying shared infrastructure

- Better observability into intent routing, enabling data-driven decisions about which intents to localize



### User Stories



#### As an Agent Developer

- I want to register intents that my agent can resolve locally, so that common operations complete without waiting for centralized resolution
- I want a declarative schema to define local intent handlers, so that I can onboard new intents without writing routing boilerplate
- I want local intent resolution to fall back to centralized resolution automatically, so that I do not need to handle fallback logic manually



#### As a Platform Operator

- I want to see which intents are resolved locally vs. centrally, so that I can identify candidates for localization and monitor system health
- I want local intent resolution to degrade gracefully, so that a misconfigured local handler does not block the centralized fallback path
- I want to set policies on which intents are eligible for local resolution, so that security-sensitive operations always route through the central service



#### As a Service Owner

- I want to publish an intent manifest declaring what my service handles, so that the platform can route matching intents to me without manual configuration
- I want intent resolution metrics broken down by path, so that I can measure the latency and reliability impact of local resolution



#### As an End User (Indirect)

- I want agent responses to arrive faster for routine requests, so that my workflow is not bottlenecked by infrastructure round-trips
- I want the system to remain responsive during partial outages, so that I can continue working even when some services are degraded



### Services Impacted

- **Intent Resolution Service** (existing centralized resolver — gains a fallback role; must accept forwarded intents from local resolvers that cannot match)
- **Agent Runtime** (hosts the local intent resolution layer; must load and evaluate intent manifests at startup and on schema refresh)
- **Service Registry** (stores and distributes intent manifests; read path expands to serve manifest queries from agent runtimes)
- **Observability Pipeline** (ingests intent audit log events; schema extended with resolution-path metadata)
- **NEW: Local Intent SDK** (to be built — client library that agent developers use to register handlers and declare intent schemas)



### Hard Requirements & Constraints

- **Backward compatibility:** All existing intents must continue to resolve through the centralized service when no local handler is registered — zero behavior change for non-adopters
- **Security boundary:** Intents marked as privileged in the central service must never be resolved locally, regardless of local handler registration
- **Schema versioning:** Intent manifests must carry a version identifier so that stale schemas are detected and rejected during resolution
- **Audit completeness:** Every intent resolution must produce an audit event capturing the resolution path, latency, and handler identity
- **Graceful degradation:** If the local resolution layer fails to initialize, the agent must fall back to centralized resolution without operator intervention



### Success Criteria

**Routine intents resolve faster when handled locally**
- Measure: Compare p50 and p95 latency for the same intent type resolved locally vs. centrally over a representative traffic window
- Measure: Track the percentage of total intent volume resolved locally over time as adoption grows

**System remains functional during central service degradation**
- Measure: Inject controlled central-service failures and confirm that locally-resolvable intents continue to succeed without error
- Measure: Monitor error rate for local-eligible intents during real degradation incidents compared to baseline

**Adoption by internal teams grows steadily after launch**
- Measure: Count the number of distinct intent types registered with local handlers per month
- Measure: Survey agent developers on onboarding friction and time-to-first-local-intent

**No increase in unresolved or misrouted intents**
- Measure: Compare unresolved-intent rate before and after local resolution rollout using existing observability dashboards



### Verification Criteria

<!-- This section defines how to verify that the shipped feature matches the PRD.
     The Product Readiness Agent uses this to assemble a completeness scorecard.
     Every item must be machine-checkable or explicitly linked to a test artifact. -->



#### Required Analytics Events

- `REQ-LOCAL-INTENTS-001`: `intent.resolved` — Fires on every intent resolution; includes `resolution_path` (local|centralized), handler ID, and latency
- `REQ-LOCAL-INTENTS-002`: `intent.local_fallback` — Fires when local resolution fails and the intent is forwarded to the central service
- `REQ-LOCAL-INTENTS-003`: `intent.manifest_loaded` — Fires when an agent runtime loads or refreshes an intent manifest; includes manifest version and handler count
- `REQ-LOCAL-INTENTS-004`: `intent.privileged_blocked` — Fires when a privileged intent is rejected by the local resolver and routed centrally



#### Required Test Scenarios

- `REQ-LOCAL-INTENTS-010`: Agent registers a local handler and resolves a matching intent locally — intent completes without contacting the central service
- `REQ-LOCAL-INTENTS-011`: Agent receives an intent with no local handler registered — intent falls back to the central service and resolves normally
- `REQ-LOCAL-INTENTS-012`: Agent registers a local handler for a privileged intent — local resolution is rejected and intent routes to central service
- `REQ-LOCAL-INTENTS-013`: Local resolution layer fails to initialize at startup — agent falls back to centralized-only resolution with no user-visible error
- `REQ-LOCAL-INTENTS-014`: Intent manifest contains a stale version — local resolver rejects the manifest and logs a warning



#### Performance Thresholds

- `REQ-LOCAL-INTENTS-020`: Locally resolved intents complete measurably faster than the same intent type resolved centrally — validated by A/B latency comparison over a representative sample
- `REQ-LOCAL-INTENTS-021`: Local resolution layer initialization adds negligible overhead to agent startup time — measured as delta in agent boot time with and without the local layer enabled



#### Acceptance Criteria (Machine-Checkable)

- `REQ-LOCAL-INTENTS-030`: Given an agent with a registered local handler for intent type X, When intent type X is received, Then the intent resolves locally and the `intent.resolved` event shows `resolution_path=local`
- `REQ-LOCAL-INTENTS-031`: Given an agent with no local handler for intent type Y, When intent type Y is received, Then the intent falls back to centralized resolution and the `intent.local_fallback` event fires
- `REQ-LOCAL-INTENTS-032`: Given an intent marked as privileged in the central service, When a local handler is registered for that intent, Then local resolution is blocked and the `intent.privileged_blocked` event fires
- `REQ-LOCAL-INTENTS-033`: Given the local resolution layer fails to initialize, When any intent is received, Then the intent resolves through the central service and no error is surfaced to the end user
- `REQ-LOCAL-INTENTS-034`: Given a manifest with a version older than the currently loaded version, When the agent runtime attempts to load it, Then the manifest is rejected and a warning is logged
- `REQ-LOCAL-INTENTS-035`: Given an agent with local resolution enabled, When the agent starts, Then the `intent.manifest_loaded` event fires with the correct manifest version and handler count



---



## PART 2: TECHNICAL PLAN (Engineers to Fill In)



### 1. Proposed Architecture

**[BLANK - Engineers to propose technical design]**

Considerations for engineering team:
- How should the local resolution layer integrate with the existing agent runtime — embedded library, sidecar, or middleware?
- What is the schema format for intent manifests (e.g., JSON Schema, protobuf, custom DSL)?
- How are intent manifests distributed and refreshed — pull-based polling, push-based event, or hybrid?
- How does the privileged-intent blocklist propagate to local resolvers securely?



### 2. Implementation Approach

**[BLANK - Engineers to propose build strategy]**

Considerations for engineering team:
- Should the Local Intent SDK be a standalone package or part of the existing agent framework?
- What is the minimum viable handler registration API surface?
- How do we migrate existing high-frequency intents to local resolution without disrupting current consumers?
- What feature flags or rollout controls are needed for incremental adoption?



### 3. Data Schema

**[BLANK - Engineers to define data structures]**

Considerations for engineering team:
- What fields belong in the intent manifest schema (intent type, version, handler endpoint, priority)?
- How are audit log events structured for the observability pipeline — what fields are required vs. optional?
- What is the storage model for intent manifests in the service registry?



### 4. Performance Strategy

**[BLANK - Engineers to explain how to meet performance constraints]**

Considerations for engineering team:
- What is the expected overhead of local intent matching per request?
- How should manifests be cached in the agent runtime to avoid repeated registry lookups?
- What is the latency budget for the local-to-centralized fallback path?



### 5. Risks & Mitigations

**[BLANK - Engineers to identify risks and mitigations]**

Considerations for engineering team:
- What happens if local handlers return incorrect results — how do we detect and remediate?
- How do we prevent manifest version drift across a fleet of agents?
- What is the blast radius if the local resolution layer has a bug — does it affect centralized resolution?
- How do we handle schema evolution for intent manifests without breaking existing handlers?



### 6. Implementation Phases

**[BLANK - Engineers to break into phases with dependencies]**

Considerations for engineering team:
- What is the minimum viable slice — local resolution for a single high-frequency intent type?
- Which teams need to be involved in each phase (platform, agent, observability)?
- What are the dependencies between the SDK, runtime integration, and manifest distribution?
- What milestones define Phase 1 (MVP) vs. Phase 2 (general availability)?



---



## Appendix: Context for Engineers

This PRD was bootstrapped to unblock the Product Comms pilot (PRO-184). The Local Intents feature is an infrastructure primitive for Eco that enables decentralized intent resolution. Engineering teams should review Part 1, validate the requirements against their understanding of the current intent resolution architecture, and propose the technical plan in Part 2. Feedback on the problem framing and requirements is welcome as part of the PRD review cycle.
