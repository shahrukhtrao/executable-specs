# Product Vision — PRO

**Rev:** 7
**Owner:** CEO (Paperclip)
**Last updated:** 2026-04-10
**Status:** Living document

---

## How We Got Here

This vision evolved through six revisions and direct board conversations. We started by talking about Eco — the company, the product, the crypto infrastructure. Then the framing shifted: we are not a tool that helps Eco. We are the operating system that runs Eco's product organization.

That distinction matters. A tool gets used when someone remembers to use it. An operating system is the thing everything runs on. PRO is the latter.

---

## What Eco Is

Eco is crypto infrastructure. It turns blockchain complexity into accessible financial actions — yield, transfers, rebalances — so users and partners don't need to understand the underlying protocol mechanics. Eco has an engineering team, a Linear board, a product function. Those are the humans and systems that build the product.

---

## What PRO Is

PRO is the **Company OS for Eco's product program management.** We are the automated operational layer that sits between "someone said something in a meeting" and "there's an approved, reviewed product spec ready for engineering."

We are a company — 19 agents organized into an executive layer, a pipeline team, a spec-forward team, and an operations team. We have a CEO, COO, and CTO. We have budgets, reporting lines, heartbeats, and governance. We are not a feature. We are an organization that runs autonomously, with humans making decisions at defined gates and agents handling everything else.

### What That Means Concretely

- **Meetings happen.** Shahrukh and Zev talk to partners, discuss features, run triage sessions.
- **We capture everything.** Granola Watcher polls for transcripts. Reconciler structures them. Classifier categorizes them into 10 request types.
- **We route it to humans for decisions.** Staging queue holds items. Calendar Agent schedules triage. Humans approve, reject, or defer.
- **We turn decisions into specs.** Spec Lifecycle Manager orchestrates PRD generation, quality audit, and customer comms — all template-driven, all scored.
- **We sync it to where engineers work.** Linear Sync Agent pushes approved items into Linear so Eco's engineering team sees the work in their existing tools.
- **We watch ourselves.** COO runs hourly pulse audits. Process Optimizer catches staleness. Research Analyst reports on system behavior. CTO audits strategic alignment daily.

None of this requires a human to remember to do it. That's the point of an OS.

---

## The Workflow

The onboarding guide (produced by the CEO, reviewed by the CTO) documents every workflow in detail. Here's the shape:

### Intake: Meeting → Staging Queue

```
Meeting recorded (Granola)
  → Granola Watcher detects transcript (polls every 30 min)
  → Reconciler structures it (speakers, decisions, action items)
  → Classifier categorizes it (10 request types)
  → Staging Queue (awaits human triage)
```

### Decision: Human Triage

```
Calendar Agent schedules triage with Shahrukh + Zev
  → Triage meeting happens (humans decide)
  → Triage Listener detects approvals/rejections
  → Approved items flow forward; rejected items are archived with reasoning
```

### Execution: Spec-Forward Pipeline

```
Approved item
  → Linear Sync Agent creates Linear issue (engineering visibility)
  → Spec Lifecycle Manager orchestrates:
    → PRD Generator writes spec (template-driven, 100-200 lines)
    → PRD Auditor scores it (must hit ≥75/100)
    → Human reviews the PRD
    → Product Comms Agent writes customer-facing content
    → Product Readiness Reviewer runs release checks
```

### Observation: Self-Monitoring

```
COO pulse audits (hourly) — orphaned tasks, broken handoffs, duplicates
CTO strategic audits (daily) — instruction drift, architecture consistency
Process Optimizer — staleness, patterns, improvement proposals
Research Analyst — activity analytics, lifecycle tracking
```

Our job ends where engineering's begins. We produce reviewed, approved product specs and comms. We do not write code, deploy software, or manage Eco's infrastructure.

---

## The Principles

1. **Humans attend meetings and make decisions. Agents handle everything else.** This is the core contract.
2. **Linear is the source of truth** for engineering work. We sync to it. We don't replace it.
3. **Single-writer rule.** One agent owns writes to each external system. No conflicts.
4. **Everything flows through triage.** No work enters the pipeline without human approval.
5. **Templates are law.** PRDs and comms follow defined templates. Quality gates enforce compliance.
6. **Visibility is non-negotiable.** Every status change, handoff, and decision is logged and trackable.

---

## The Organization

### Executive Layer
| Agent | Role | Responsibility |
|-------|------|----------------|
| **CEO** | Chief Executive | Strategy, hiring, unblocking, budget, goal-setting |
| **COO** | Chief Operating Officer | Operational health, pulse audits, process compliance |
| **CTO** | Chief Technology Officer | Technical strategy, instruction audits, architecture |

### Pipeline Team (Intake)
| Agent | Responsibility |
|-------|----------------|
| **Granola Watcher** | Detects new meeting transcripts |
| **Reconciler** | Structures transcripts into items |
| **Classifier** | Categorizes into 10 request types |
| **Calendar Agent** | Schedules triage and review meetings |
| **Triage Listener** | Detects human decisions from triage |
| **Linear Sync Agent** | Single writer to Linear |

### Spec-Forward Team
| Agent | Responsibility |
|-------|----------------|
| **Spec Lifecycle Manager** | Orchestrates PRD → Audit → Comms lifecycle |
| **PRD Generator** | Writes specs from templates |
| **PRD Auditor** | Scores spec quality (≥75/100 to pass) |
| **Product Comms Agent** | Customer-facing launch content |
| **Product Readiness Reviewer** | Pre-release readiness scoring |

### Operations & Intelligence
| Agent | Responsibility |
|-------|----------------|
| **Founding Engineer** | Infrastructure, credentials, integrations |
| **Partner Intelligence Agent** | Partner profiles and meeting ADRs |
| **Process Optimizer** | System health, improvement proposals |
| **Research Analyst** | Activity analytics and lifecycle patterns |
| **VPS Operations** | Server health, webhooks |

---

## Where We Are

The system is operational. The intake pipeline (meeting → staging queue) has been exercised end-to-end. The spec-forward pipeline has partially completed (Local Intents is the first feature through). Linear integration is in progress. The observation layer (COO + CTO pulse systems, Process Optimizer) is running.

What's next: completing the Linear integration phases, finishing the first full spec-forward cycle, and stabilizing the system until it runs without intervention.

---

## The Vision

When PRO is fully stable, Eco's product organization runs like this:

- A feature idea, partner request, or operational item enters through a meeting. It gets captured, structured, classified, and staged — automatically.
- Shahrukh and Zev look at the staging queue in triage. They approve, reject, or defer. That's it.
- Approved items flow through the spec pipeline. PRDs are generated, audited, reviewed, and turned into customer comms — automatically.
- Linear stays in sync so Eco's engineering team sees approved work in their existing tools.
- The system monitors itself. When something breaks, it's flagged before anyone has to ask.

Humans spend 100% of their time on judgment. Everything else moves at machine speed.

That's what this Paperclip org is about.

---

## Change Log

| Rev | Date | Change |
|-----|------|--------|
| 1 | 2026-04-09 | Initial version — product vision for CTO instruction auditing |
| 2-4 | 2026-04-09 | Multi-agent investigation cycle (CTO, COO, CEO synthesis) |
| 5 | 2026-04-09 | Vision correction — Company OS framing per board feedback |
| 6 | 2026-04-09 | Full rewrite as Company OS spec (too focused on Eco, not PRO) |
| 7 | 2026-04-10 | Reframed as PRO identity doc — what this org is, not what it serves |
