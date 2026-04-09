# Product Vision & Technical Specification — Eco / PRO

**Rev:** 6 (Company OS Rewrite)
**Owner:** CEO (Paperclip)
**Last updated:** 2026-04-09
**Status:** Living document — updated as board direction evolves

---

## Part 1: Product Vision

### What This System Is

This is a **Company OS for product program management.** It automates the operational layer of Eco's EPD function — everything between "someone said something in a meeting" and "there's a reviewed, approved product spec ready for engineering."

We are not a code execution platform. We are not an engineering pipeline. We do not generate tech specs, deploy code, or manage infrastructure beyond our own. Eco has a separate Linear board and engineering team for execution. Our job ends where engineering's begins.

### The Core Idea

**Humans attend meetings and make decisions. Agents handle everything else.**

"Everything else" means: capturing what was said, structuring it, routing it for triage, scheduling the triage meetings, detecting approvals, creating product specs, auditing those specs for quality, generating customer-facing comms, and tracking the whole thing so nothing falls through the cracks.

### Who We Serve

1. **Shahrukh and Zev** — the decision-makers. They attend meetings, approve/reject items at triage, review product specs. The system ensures they never need to do paperwork, scheduling, or status tracking manually.
2. **Eco's EPD function** — the broader product and engineering team gets clean, reviewed product specs and comms as output from this system.
3. **Partners** (indirectly) — partner requests flow through the same pipeline as feature requests, ensuring nothing gets lost.

### What We Actually Do (The Value Chain)

```
Meetings → Transcripts → Structured Items → Staging Queue → Human Triage
    → Approved Items → Product Specs (PRDs) → Quality Audit → Human Review
    → Approved Specs → Customer Comms → Published
```

Each arrow is automated. Each "Human" step is a decision gate where Shahrukh/Zev exercise judgment. The system schedules those gates, prepares the materials, and captures the decisions.

### What We Do NOT Do

- Generate tech specs or engineering specifications — that's Eco engineering's job
- Deploy code or access production environments
- Make product decisions — we prepare materials and detect decisions, never make them
- Write directly to Linear without human approval at triage
- Manage Eco's engineering execution — a separate Linear board handles that
- Access customer data or production databases

### Product Vision (Target State) — The Stable OS

The target is a **stable, self-improving operating system** for product program management:

1. Every feature idea, partner request, or operational item enters through a single pipeline (meeting → transcript → classify → stage → triage)
2. Humans decide at defined gates (triage approval, PRD review, comms approval)
3. Between gates, the system runs autonomously — scheduling, generating, auditing, routing, tracking
4. The system monitors its own health (COO pulse, Process Optimizer, observability)
5. The system improves itself (Process Optimizer identifies patterns, CTO proposes strategic improvements, CEO decides)
6. Nothing falls through the cracks — every item is tracked from capture to resolution

When stable, this OS should require zero human effort for operational tasks. Humans spend 100% of their time on judgment: approving, rejecting, prioritizing, and deciding.

### Strategic Themes

1. **Pipeline reliability** — every meeting item must flow through the full pipeline without manual intervention
2. **Product spec quality** — PRDs meet template standards, audited before human review, scored objectively
3. **Operational observability** — pulse logs, process optimization, blocker detection, budget monitoring
4. **Linear as visibility layer** — Eco's team sees status in Linear; pipeline runs in Paperclip; sync keeps them aligned

---

## Part 2: Technical Specification — PRO Agent Architecture

### Architectural Principles

1. **Paperclip is the operational source of truth.** Linear is the sync target for human visibility. Eco's team works in Linear; our pipeline runs in Paperclip; sync keeps both aligned.
2. **Humans attend meetings and make decisions.** Agents handle scheduling, capture, generation, audit, routing, and tracking.
3. **Single-writer rule:** Only one agent writes to any given external system. (GitHub exception — branch conventions prevent conflicts.)
4. **Everything flows through triage.** No work enters the approved pipeline without human approval.
5. **Templates are law.** PRDs and comms follow defined templates. Quality gates enforce compliance.
6. **Visibility is non-negotiable.** Every status change, handoff, and decision is logged.
7. **Auto-sync pipeline:** Routine status updates flow to Linear via `[auto-sync]` labeled items.
8. **We stop at product specs.** Our pipeline produces PRDs and product comms. Engineering execution is out of scope.

### Meeting Cadence & Human Touchpoints

| Meeting | Frequency | Human Role | Agent Role |
|---------|-----------|------------|------------|
| Triage | As needed (triggered by staging queue) | Approve/reject items | Calendar Agent schedules, Triage Listener detects decisions |
| PRD Review | Per-feature (after PRD generation) | Approve/request changes | SLM orchestrates, PRD Auditor pre-screens |
| Comms Review | Per-feature (before publish) | Final approval | Product Comms Agent drafts, awaits approval |

### Agent Roster & Responsibilities

#### Executive Layer

| Agent | Role | Reports To | Responsibility |
|-------|------|------------|----------------|
| **CEO** | ceo | Board | Strategic direction, hiring, unblocking, budget, goal-setting |
| **CTO** | pm | CEO | Technical strategy, pipeline architecture, Linear integration |
| **COO** | pm | CEO | Operational health, pulse audits, process compliance, blocker detection |

#### Intake Pipeline (Meeting → Staging Queue)

| Agent | Role | Responsibility |
|-------|------|----------------|
| **Granola Watcher** | engineer | Polls Granola for new meeting transcripts |
| **Reconciler** | engineer | Parses transcripts into structured items (speakers, decisions, action items) |
| **Classifier** | engineer | Categorizes items into 10 request types, routes to staging queue |
| **Calendar Agent** | engineer | Schedules triage and review meetings based on human availability |
| **Triage Listener** | engineer | Detects approval/rejection decisions in triage meetings |

#### Spec-Forward Pipeline (Staging Queue → Published Specs)

| Agent | Role | Responsibility |
|-------|------|----------------|
| **Spec Lifecycle Manager** | pm | Orchestrates PRD → Audit → Comms lifecycle |
| **PRD Generator** | engineer | Creates PRDs from requirements using templates |
| **PRD Auditor** | engineer | Reviews PRDs for quality, completeness, template compliance (>= 75/100) |
| **Product Comms Agent** | engineer | Transforms approved PRDs into customer-facing launch articles |
| **Product Readiness Reviewer** | engineer | Pre-release readiness checks (6-dimension scorecard) |

#### Operations & Integration

| Agent | Role | Responsibility |
|-------|------|----------------|
| **Linear Sync Agent** | engineer | Syncs Paperclip ↔ Linear state for human visibility |
| **Partner Intelligence Agent** | engineer | Maintains partner knowledge base, meeting ADRs |
| **Founding Engineer** | engineer | Generalist IC for infra, credential integration |

#### Oversight & Improvement

| Agent | Role | Responsibility |
|-------|------|----------------|
| **Research Analyst 2** | engineer | Observes agent activity, issue lifecycle analytics |
| **Process Optimizer** | engineer | Staleness monitoring, system health, process improvement |
| **VPS Operations** | engineer | VPS infrastructure, webhook endpoints |

### Data Flow Architecture

```
Meeting (Granola records)
       ↓
Granola Watcher (polls every 30 min, detects new transcript)
       ↓
Reconciler (parses speakers, timestamps, decisions, action items)
       ↓
Classifier (categorizes: Feature Request? Partner Request? Routes to staging)
       ↓
Staging Queue (items wait for human review)
       ↓
Calendar Agent (schedules triage meeting with Shahrukh + Zev)
       ↓
Triage Meeting (humans approve, reject, or defer each item)
       ↓
Triage Listener (detects decisions from triage transcript)
       ↓
├── Approved → Linear Sync Agent → Linear (for engineering visibility)
├── Approved + needs spec → SLM → PRD Generator → PRD Auditor → Human Review
│                                                                    ↓
│                                                    Product Comms Agent → Published
├── Rejected → Archived with reasoning
└── Deferred → Stays in staging for next triage
```

### External Systems

| System | Purpose | Writer Agent | Notes |
|--------|---------|-------------|-------|
| Linear | Engineering work tracking (sync target) | Linear Sync Agent | Eco's team sees status here; we sync from Paperclip |
| Granola | Meeting transcripts | Read-only (Granola Watcher) | |
| Google Calendar | Meeting scheduling | Calendar Agent | |
| GitHub | PRDs, specs, comms docs | SLM, PRD Generator, Product Comms, Partner Intel | Branch conventions prevent conflicts |

### Observability — Pulse System

| Pulse Log | Owner | Purpose |
|-----------|-------|---------|
| COO Pulse Log | COO | Operational health, blocker status, agent utilization |
| COO Pulse Backlog | COO | Standing backlog of operational improvements |
| CTO Pulse Log | CTO | Technical health, pipeline status, architecture decisions |
| VPS Ops Pulse Log | VPS Operations | Infrastructure health, uptime, webhook status |

---

## Part 3: Work Completed To Date

### Infrastructure & Setup (Complete)
- Agent roster hired and configured (17 active, 1 paused)
- Linear API key and Granola API credentials integrated
- Agent instruction files configured across most agents
- Task dedup guard implemented across P0 agents

### Intake Pipeline (Operational)
- Meeting → Granola Watcher → Reconciler → Classifier → Staging Queue — fully exercised
- Triage detection and Linear sync — functional
- Calendar scheduling — functional

### Spec-Forward Pipeline (Partially Complete)
- **Local Intents:** PRD written, reviewed, merged (PR #1). Product comms draft in progress (M5 stage).
- **Stale Rebalance Cron (PROD-19):** PRD ideation meeting completed.
- End-to-end PRD → Audit → Comms flow has been partially exercised but not completed for any single feature.

### Linear Integration (In Progress)
- Phase A-0: Board answers needed (blocked)
- Phase A-1: Linear Agent Guidance configuration (in progress)
- Phases A-2 through A-4: queued

### Operational Health
- COO and CTO pulse systems operational
- Process Optimizer running staleness monitoring
- Staging queue escalation tiers built

---

## Part 4: The 10 Request Types

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

---

## Part 5: Error Recovery & Handoff Protocols

### Error Recovery Playbook

| Failure | Detection | Recovery |
|---------|-----------|----------|
| Agent enters error state | COO pulse audit or Process Optimizer | CEO/COO investigate adapter config; restart via Paperclip |
| Agent exhausts budget (100%) | Auto-pause by Paperclip | CEO reviews spend, adjusts budget or deprioritizes work |
| Granola API unreachable | Granola Watcher heartbeat fails | Calendar Agent still functions; manual meeting notes as fallback |
| Linear API unreachable | Linear Sync heartbeat fails | Work continues in Paperclip; sync resumes on recovery |
| Misclassification | Process Optimizer weekly scan | Manual reclassification via staging queue |
| Stale execution lock | Process Optimizer detection | Release via admin endpoint or CEO manual release |

### Handoff Protocols

| From | To | Trigger | Mechanism |
|------|-----|---------|-----------|
| Granola Watcher | Reconciler | New transcript | Staging queue item created |
| Reconciler | Classifier | Data reconciled | Staging queue item updated |
| Classifier | Triage Listener | Item classified | Staging queue item tagged |
| Triage Listener | Linear Sync Agent | Human approves | Decision metadata written |
| SLM | PRD Generator | Feature enters pipeline | Paperclip subtask assigned |
| PRD Generator | PRD Auditor | Draft complete | Paperclip subtask assigned |
| PRD Auditor | SLM | PRD approved/rejected | Status update + comment |
| SLM | Product Comms Agent | Comms stage reached | Paperclip subtask assigned |

---

## Glossary

| Term | Definition |
|------|------------|
| **Company OS** | This system — the automated operational layer that handles everything between meetings and approved product specs |
| **Staging queue** | Paperclip-internal queue of items awaiting human triage decision |
| **Triage** | Human decision meeting where Shahrukh + Zev approve/reject staging items |
| **PRD** | Product Requirements Document — 100-200 lines, template-compliant, scored >= 75 |
| **Spec-forward pipeline** | PRD → Audit → Comms lifecycle (does NOT include tech specs) |
| **Pulse log** | Standing issue used by executives for running health monitoring |
| **Single-writer rule** | Each external system has one designated writer agent |
| **Gate** | Pipeline point requiring human approval before proceeding |
| **Auto-sync** | Routine status updates pushed to Linear, not human-gated |

---

## Change Log

| Rev | Date | Change | Reason |
|-----|------|--------|--------|
| 1 | 2026-04-09 | Initial version | Board requested a written product vision for CTO instruction auditing |
| 2-4 | 2026-04-09 | CTO investigation, COO audit, CEO synthesis | Multi-agent investigation and consolidation |
| 5 | 2026-04-09 | Vision correction — Company OS framing | Board feedback: reframe from crypto infra pipeline to Company OS for product program management |
| 6 | 2026-04-09 | Full rewrite as Company OS spec | Consolidated vision, architecture, agent roster, data flow, and operational playbooks into single living document |
