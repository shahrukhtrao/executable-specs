# Product Vision — Eco / PRO

**Owner:** CEO (Paperclip)
**Last updated:** 2026-04-09
**Rev:** 6
**Status:** Living document — CEO owns, updated daily

---

## What Eco Is

Eco is crypto infrastructure. The product lets users interact with blockchain protocols through a simplified interface — converting complexity into accessible financial actions. Users get outcomes (yield, transfers, rebalances) without needing to understand protocol mechanics.

## Who We Serve

1. **End users** — retail and semi-institutional users who want crypto yield and portfolio management without protocol expertise
2. **Partners** — B2B integrators (exchanges, wallets, fintechs) who embed Eco's infrastructure into their products
3. **Internal operations** — Eco's EPD team that builds and maintains the platform

## What the PRO Organization Is

PRO is a **Company OS for product program management.** It automates the operational layer of Eco's EPD function — everything between "someone said something in a meeting" and "there's a reviewed, approved product spec ready for engineering."

We are not a code execution platform. We are not an engineering pipeline. We do not generate tech specs, deploy code, or manage infrastructure beyond our own. Eco has a separate Linear board and engineering team for execution. Our job ends where engineering's begins.

### The Core Idea

**Humans attend meetings and make decisions. Agents handle everything else.**

"Everything else" means: capturing what was said, structuring it, routing it for triage, scheduling the triage meetings, detecting approvals, creating product specs, auditing those specs for quality, generating customer-facing comms, and tracking the whole thing so nothing falls through the cracks.

### What We Do

```
Meetings → Transcripts → Structured Items → Staging Queue → Human Triage
    → Approved Items → Product Specs (PRDs) → Quality Audit → Human Review
    → Approved Specs → Customer Comms → Published
```

Each arrow is automated. Each "Human" step is a decision gate where Shahrukh and Zev exercise judgment. The system schedules those gates, prepares the materials, and captures the decisions.

### What We Do NOT Do

- Generate tech specs or engineering specifications — Eco engineering handles that
- Deploy code or access production environments
- Make product decisions — we prepare materials and detect decisions, never make them
- Write directly to Linear without human approval at triage
- Manage Eco's engineering execution — a separate Linear board handles that
- Access customer data or production databases

### System Boundaries

| Boundary | Inside (PRO) | Outside (Eco Engineering) |
|----------|-------------|--------------------------|
| Specs | Product specs (PRDs) | Tech specs, architecture docs |
| Tracking | Paperclip issues, staging queue | Linear board (sync target only) |
| Execution | Agent orchestration | Code, deploys, infra |
| Decisions | Prepare materials, detect decisions | N/A — humans decide at both layers |

## Current Product Priorities

### Active Features in Pipeline

- **Local Intents** — infrastructure primitive for transaction routing. First feature through the full spec-forward workflow. PRD written, reviewed, merged (PR #1). Product comms in progress.
- **Stale Rebalance Cron (PROD-19)** — operational tooling for detecting and resolving stale balance states. Phase 2B pilot feature. PRD ideation meeting completed.

### Strategic Themes

1. **Pipeline reliability** — every meeting item flows through the full pipeline without manual intervention
2. **Product spec quality** — PRDs meet template standards, audited before human review, scored objectively
3. **Operational observability** — pulse logs, process optimization, blocker detection, budget monitoring
4. **Linear as visibility layer** — Eco's team sees status in Linear; pipeline runs in Paperclip; sync keeps them aligned

## Architectural Principles

1. **Paperclip is the operational source of truth.** Linear is the sync target for human visibility.
2. **Humans attend meetings and make decisions.** Agents handle scheduling, capture, generation, audit, routing, and tracking.
3. **Single-writer rule:** Only one agent writes to any given external system. (GitHub uses branch conventions to prevent conflicts.)
4. **Everything flows through triage.** No work enters the approved pipeline without human approval.
5. **Templates are law.** PRDs and comms follow defined templates. Quality gates enforce compliance.
6. **Visibility is non-negotiable.** Every status change, handoff, and decision is logged.
7. **Auto-sync pipeline:** Routine status updates flow to Linear via `[auto-sync]` labeled items.
8. **We stop at product specs.** Our pipeline produces PRDs and product comms. Engineering execution is out of scope.

## The Value Chain (Detailed)

### Intake Pipeline (Meeting → Staging Queue)

```
Meeting (Granola records)
       ↓
Granola Watcher (polls every 30 min, detects new transcript)
       ↓
Reconciler (parses speakers, timestamps, decisions, action items)
       ↓
Classifier (categorizes into 10 request types, routes to staging)
       ↓
Staging Queue (items wait for human review)
       ↓
Calendar Agent (schedules triage meeting with Shahrukh + Zev)
       ↓
Triage Meeting (humans approve, reject, or defer each item)
       ↓
Triage Listener (detects decisions from triage transcript)
```

### Spec-Forward Pipeline (Staging Queue → Published Specs)

```
Approved item
       ↓
├── Linear Sync Agent → Linear (for engineering visibility)
├── Needs spec → Spec Lifecycle Manager → PRD Generator → PRD Auditor → Human Review
│                                                                          ↓
│                                                          Product Comms Agent → Published
├── Rejected → Archived with reasoning
└── Deferred → Stays in staging for next triage
```

### The 10 Request Types

| # | Type | Example |
|---|------|---------|
| 1 | Partner Request | "Circle wants us to support USDC on Arbitrum" |
| 2 | BD/Strategic | "GSR is interested in a market-making partnership" |
| 3 | Feature Request | "Users want batch transfers" |
| 4 | Internal Tooling | "We need a counterparty tracker for OTC" |
| 5 | Data/Reporting | "Monthly volume reports for the board" |
| 6 | Pricing/Commercial | "Revisit fee structure for enterprise tier" |
| 7 | Content/Writing | "Blog post about the new SDK" |
| 8 | Research/Discovery | "Investigate zero-knowledge proof options" |
| 9 | Process/Operational | "Standardize deployment checklist" |
| 10 | Bug/Product Gap | "Transfer confirmations are delayed" |

## Target State — The Stable OS

The target is a **stable, self-improving operating system** for product program management:

1. Every feature idea, partner request, or operational item enters through a single pipeline
2. Humans decide at defined gates (triage approval, PRD review, comms approval)
3. Between gates, the system runs autonomously — scheduling, generating, auditing, routing, tracking
4. The system monitors its own health (COO pulse, Process Optimizer, observability)
5. The system improves itself (Process Optimizer identifies patterns, CTO proposes strategic improvements, CEO decides)
6. Nothing falls through the cracks — every item is tracked from capture to resolution

When stable, this OS requires zero human effort for operational tasks. Humans spend 100% of their time on judgment: approving, rejecting, prioritizing, and deciding.

## What the Board Cares About

- **Process working end-to-end**: Features flow from ideation to release without manual intervention beyond defined gates
- **No silent failures**: When something breaks, it's visible and escalated
- **Cost discipline**: Agents use the minimum model/heartbeat for their role
- **Experimentation > optimization** at this stage: 70/30 split — still building the system, not yet optimizing it

## External Systems

| System | Purpose | Writer Agent | Access |
|--------|---------|-------------|--------|
| Linear | Engineering work tracking (sync target) | Linear Sync Agent | Read/write via API |
| Granola | Meeting transcripts | Granola Watcher (read-only) | API polling |
| Google Calendar | Meeting scheduling | Calendar Agent | API |
| GitHub | PRDs, specs, comms docs | SLM, PRD Generator, Product Comms, Partner Intel | Branch conventions |

## Meeting Cadence

| Meeting | Frequency | Human Role | Agent Role |
|---------|-----------|------------|------------|
| Triage | As needed (triggered by staging queue) | Approve/reject items | Calendar Agent schedules, Triage Listener detects decisions |
| PRD Review | Per-feature (after PRD generation) | Approve/request changes | SLM orchestrates, PRD Auditor pre-screens |
| Comms Review | Per-feature (before publish) | Final approval | Product Comms Agent drafts, awaits approval |

---

## Change Log

| Date | Rev | Change | Reason |
|------|-----|--------|--------|
| 2026-04-09 | 1 | Initial version | Board requested written product vision |
| 2026-04-09 | 6 | Full rewrite: Company OS framing, removed tech spec pipeline, clarified system boundaries, added value chain detail | Board feedback: we are a Company OS, not an engineering pipeline. Living document going forward. |
