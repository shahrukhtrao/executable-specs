# Product Vision — Eco

**Owner:** CEO (Paperclip)
**Last updated:** 2026-04-09
**Status:** Living document — updated as board direction evolves

---

## What Eco Is

Eco is crypto infrastructure. The product enables users to interact with blockchain protocols through a simplified interface — converting complexity into accessible financial actions. The core value is abstraction: users get outcomes (yield, transfers, rebalances) without needing to understand the underlying protocol mechanics.

## Who We Serve

1. **End users** — retail and semi-institutional users who want crypto yield and portfolio management without protocol expertise
2. **Partners** — B2B integrators (exchanges, wallets, fintechs) who embed Eco's infrastructure into their products (Beam, B2C2, others)
3. **Internal operations** — Eco's own EPD team that builds and maintains the platform

## Current Product Priorities

### Active Features

- **Local Intents** — infrastructure primitive for transaction routing. Currently in PRD/comms pipeline. This is the first feature going through the full spec-forward workflow.
- **Stale Rebalance Cron** — operational tooling for detecting and resolving stale balance states in the rebalancing system. Phase 2B pilot feature in the spec-forward pipeline (PROD-19).

### Strategic Themes

1. **Protocol infrastructure reliability** — operational tooling, monitoring, and automated recovery (stale rebalance cron is an example)
2. **Transaction routing optimization** — Local Intents and the broader intent-based architecture
3. **Partner enablement** — building integration surfaces and maintaining partner relationships (B2C2, Beam, others)

## How the Paperclip Organization Supports This

The Paperclip org (PRO) is the automated product program management layer. It does not build the product — it manages the process of building the product.

### What PRO Does

- **Spec-forward pipeline**: Triage → PRD Ideation → PRD Generation → PRD Audit → Tech Spec → Implementation Review → Release → Measurement
- **Product comms**: Transforms PRDs into customer-facing launch articles via the 9-question brief workflow
- **Partner intelligence**: Maintains a git-backed knowledge base of partner relationships, meeting ADRs, and strategic commitments
- **Linear sync**: Keeps Linear (source of truth for engineering work) in sync with Paperclip state via auto-sync pipeline
- **Triage**: Human-gated staging queue where all work enters before being triaged into Linear or assigned to agents

### Key Architectural Principles

1. **Linear is the source of truth** for all tracked engineering work. Agents never bypass Linear.
2. **Humans attend meetings and make decisions.** Agents handle scheduling, document generation, tracking, and communication.
3. **Single-writer rule**: Only one agent writes to any given external system (Triage Listener owns Linear writes, via Linear Sync Agent).
4. **Everything flows through triage.** No work enters the pipeline without human approval in the staging queue.
5. **Templates are law.** PRDs, tech specs, and comms follow defined templates. Deviations require explicit board approval.
6. **Visibility is non-negotiable.** Every PR, every status change, every handoff must be visible in the staging queue or Linear.
7. **Auto-sync pipeline**: Routine status updates (PR opened → in_review, PR merged → done) flow through `[auto-sync]` labeled staging items processed automatically by the Triage Listener + Linear Sync Agent.

### What the Board Cares About

- **Process working end-to-end**: Features should flow from ideation to release without manual intervention beyond the defined human gates
- **No silent failures**: When something breaks in the pipeline, it should be visible and escalated
- **Cost discipline**: Agents should use the minimum model/heartbeat needed for their role
- **Experimentation > optimization** at this stage: 70/30 split. We're still building the system, not yet optimizing it.

## Product Vision (What We're Building Toward)

An automated product program management system where:

1. A feature idea enters via a triage meeting or Linear ticket
2. The pipeline automatically orchestrates: PRD creation, review, tech spec scaffolding, implementation review, readiness checks, and comms generation
3. Humans make decisions at defined gates (PRD approval, implementation review, release go/no-go)
4. Everything else — scheduling, document generation, tracking, cross-functional communication — is automated
5. The system improves itself: observers detect patterns, COO flags operational issues, CTO proposes strategic improvements, CEO decides

The goal is not to replace human judgment. The goal is to make human judgment the bottleneck — because everything else moves at machine speed.

---

## Change Log

| Date | Change | Reason |
|------|--------|--------|
| 2026-04-09 | Initial version | Board requested a written product vision for CTO instruction auditing |
