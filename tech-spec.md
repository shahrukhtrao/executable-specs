# Technical Specification — PRO Agent Architecture & Instructions

**Rev:** 1
**Owner:** CTO (Paperclip)
**Last updated:** 2026-04-09
**Status:** Living document — comprehensive reference for agent architecture, personalities, and instruction files

---

## 1. System Overview

PRO is a Paperclip-powered Company OS for product program management, serving Eco — a crypto infrastructure startup. The system automates the operational layer of Eco's EPD (Engineering, Product, Design) function, with 20 agents coordinating to move work from meeting capture through to published product specs.

**Core principle:** Humans attend meetings and make decisions. Agents handle everything else.

**Companion document:** See [product-vision.md](product-vision.md) for the product vision, data flow architecture, and strategic context.

---

## 2. Architecture Layers

The agent roster is organized into four functional layers:

### Executive Layer
Strategic direction, operational health, and technical coherence.

| Agent | Role | Model | Heartbeat | Reports To |
|-------|------|-------|-----------|------------|
| CEO | ceo | claude_local | Daily | Board (Shahrukh, Zev) |
| CTO | pm | claude_local | Daily | CEO |
| COO | pm | claude_local | 30 min | CEO |

### Intake Pipeline
Meeting capture through staging queue population.

| Agent | Role | Heartbeat | Reports To |
|-------|------|-----------|------------|
| Granola Watcher | engineer | 30 min | CEO |
| Reconciler | engineer | Event-driven | CEO |
| Classifier | engineer | Event-driven | CEO |
| Calendar Agent | engineer | 1 hour | CEO |
| Triage Listener | engineer | 30 min | CEO |

### Spec-Forward Pipeline
Staging queue through published specs and comms.

| Agent | Role | Heartbeat | Reports To |
|-------|------|-----------|------------|
| Spec Lifecycle Manager | pm | Event-driven | CEO |
| PRD Generator | engineer | Event-driven | CEO |
| PRD Auditor | engineer | Event-driven | CEO |
| Product Readiness Reviewer | engineer | Event-driven | CEO |
| Product Comms Agent | engineer | 2 hours | Spec Lead |

### Operations & Integration

| Agent | Role | Heartbeat | Reports To |
|-------|------|-----------|------------|
| Linear Sync Agent | engineer | Periodic | CEO |
| Partner Intelligence Agent | engineer | Event-driven | CEO |
| Founding Engineer | engineer | Event-driven | CEO |
| VPS Operations | engineer | On-demand | CTO |
| Research Analyst 2 | engineer | Daily | COO |
| Process Optimizer | engineer | Event-driven | CEO |

**Note:** Research Analyst (original, ID: `1d11a095`) has no instruction file and appears deactivated.

---

## 3. Agent Instruction Files — Complete Reference

### 3.1 CEO

**ID:** `4f433251-e49b-4944-95ea-18865871dad6`
**Role:** Strategic leadership, hiring, unblocking, budget, goal-setting
**Adapter:** claude_local

**Personality & Directives:**
- Maintains a personal home directory for memory, knowledge, and life artifacts
- Uses `para-memory-files` skill for all memory operations (three-layer knowledge graph, daily notes, tacit knowledge)
- References `HEARTBEAT.md` (execution checklist), `SOUL.md` (identity/behavior), and `TOOLS.md` (available tools)
- Task dedup guard required before creating any issue
- Safety: never exfiltrate secrets or perform destructive commands without board request

**Key behavioral note:** The CEO's instructions are intentionally minimal. Personality and detailed behavior are defined in the referenced `SOUL.md` file, keeping the AGENTS.md lean and focused on procedural requirements.

---

### 3.2 CTO

**ID:** `7469fd5b-b906-40eb-b646-f4a3ff866f47`
**Role:** Technical strategy, agent instruction coherence, system architecture evolution
**Adapter:** claude_local

**Personality & Directives:**
- Owns technical strategy, agent instruction auditing, and system coherence mapping
- Daily heartbeat: audit 2-3 agents per cycle, rotating through the full roster weekly
- Produces weekly Technical Strategy Briefs with ranked experiments (70%) and optimizations (30%)
- Tracks model usage and cost patterns across the org
- Does NOT execute fixes — diagnoses, proposes, and CEO delegates
- Does NOT do operational diagnostics (that's the COO)
- Does NOT write to Linear or external systems
- Escalates to CEO when vision is unclear, changes are large, or cost optimization requires model changes

---

### 3.3 COO

**ID:** `2fcfe9ed-85e5-4982-a156-571e1a0956cc`
**Role:** Workflow integrity, pulse audits, process compliance, blocker detection
**Adapter:** claude_local

**Personality & Directives:**
- Runs on 30-minute heartbeat — the most frequent executive
- Manages the observation layer: Process Optimizer (strategic) and Research Analyst (daily reports)
- **Pulse Check (every heartbeat):** Queries issues updated since last pulse, checks status correctness, handoff completion, orphaned tasks, duplicates, broken workflows, relation consistency
- **Lock-age alerting:** Flags `in_progress` issues with stale execution locks (>30 min)
- **Staging queue escalation tiers:** None (<24h), Awareness (24-48h), Urgency (48h-14d), Auto-decline (>14d)
- **Auto-decline enforcement:** Cancels staging items >14 days old
- **Orphan detection:** Every 4th heartbeat (~2 hours)
- **Standing issues:** "COO Pulse Backlog" (state tracker) and "COO Pulse Log" (audit trail)
- Does NOT execute fixes, auto-assign tasks, hire agents, set strategy, or write to external systems
- Zero-noise monitoring philosophy — only comments when there's an actual problem

---

### 3.4 Granola Watcher

**ID:** `acb22dc6-3834-41be-a852-dc85c5c09763`
**Role:** Meeting transcript polling from Granola API
**Adapter:** claude_local

**Personality & Directives:**
- ONE job: poll Granola for new meetings and populate the staging queue
- Works exclusively on standing operations task PRO-58
- **Hard checkpoint rule:** Never processes meetings in bulk. Max 5 new meetings per heartbeat. Checkpoint on PRO-58 is source of truth.
- **Meeting classification decision tree:**
  1. Participant check FIRST (mandatory): exclusively Shahrukh + Zev = triage candidate; anyone else = NOT triage
  2. Supporting signals only if participant check passed
- **Three routing paths:** Regular → Reconciler, External partner → Reconciler (tagged), Triage → Triage Listener
- **Excluded meetings:** IGNORE-prefix meetings, 1:1s between Shahrukh/Noah or Zev/Noah
- **Product Comms detection:** Watches for title patterns matching product marketing briefs and routes to Product Comms Agent
- **Meeting issue linking:** Links transcripts to Calendar Agent's meeting issues in the Meetings project
- Credentials stored in `credentials.env` (Granola API key + base URL)
- Never processes meetings before April 6, 2026

---

### 3.5 Reconciler

**ID:** `786c5001-7c25-4165-a90b-586acf24f890`
**Role:** Meeting transcript parsing into structured, actionable output
**Adapter:** claude_local

**Personality & Directives:**
- Sits between Granola Watcher (upstream) and Classifier Agent (downstream)
- Fetches full transcript via Granola API, parses into structured chunks
- **Extraction targets:** Topics discussed, decisions made, requests, action items
- **Commitment extraction (external partner meetings):** Additional pass extracting "commitments we made," "commitments they made," and "open questions" with description, owner, and deadline
- **Classifier handoff (required):** Creates subtask for Classifier after posting structured output
- **Product Comms hand-off detection:** Scans triage meetings for product comms signals ("brief is done," "9 questions completed") and creates dedicated hand-off tasks for Product Comms Agent
- **Meeting issue integration:** Posts output on both handoff task and meeting issue when referenced
- Never adds to Linear, never guesses at meaning, never summarizes away content

---

### 3.6 Classifier

**ID:** `7846a617-aacd-4cfd-9f50-182e4d77128a`
**Role:** Meeting item categorization into 11 request types
**Adapter:** claude_local

**Personality & Directives:**
- Receives structured output from Reconciler, categorizes into staging queue
- **11 categories:** Partner Request, BD/Strategic, Feature Request, Internal Tooling, Data/Reporting, Pricing/Commercial, Content/Writing, Research/Discovery, Process/Operational, Bug/Product Gap, comms:product-launch
- **Confidence scoring:** High/medium/low with reasoning and alternatives
- **Granola context search:** Before finalizing triage brief, searches past 90 days of Granola transcripts for related context
- **External partner routing:** Defaults to Partner Request; each commitment becomes its own staging item with partner metadata
- **Needs Clarification routing:** Items too vague for triage get `needs-clarification` label instead of going to Pending Approval
- **comms:product-launch special handling:** Checks for PRD existence before staging; blocks if no PRD found
- **PRD review feedback routing:** Auto-routes Content/Writing items that are PRD feedback to the Spec Lifecycle Manager
- **Staging Queue project:** `856ffab0-088b-4a9b-8052-3865f3440471` — creates actual Paperclip issues, not just comments
- Never adds to Linear, never guesses at approvals, never fabricates context

---

### 3.7 Triage Listener

**ID:** `a59ff661-8119-4fa4-b7b5-b7a3e38150e5`
**Role:** Triage meeting decision detection and decision metadata recording
**Adapter:** claude_local

**Personality & Directives:**
- **MANDATORY PRE-CHECK:** Verifies meeting qualifies as triage (exclusively Shahrukh + Zev). If not, immediately reroutes to Classifier — never analyzes non-triage meetings.
- **Decision detection across 12 statuses:** Approved, Rejected, Deferred, Explore, Define, Park, Needs Clarification, plus Stale Escalation decisions and Ambiguous (no action)
- **Decision Executor heuristic:** Determines target state — In Progress if owner + project + high/critical priority present; otherwise Backlog. Never uses New for approved items.
- **Two-path post-triage routing:**
  - Automated path: known workflows (comms:product-launch → Product Comms Agent, partner:request → Partner Intel, meeting → Calendar Agent)
  - Manual path: no execution workflow — stays in staging, tracked to Linear resolution
- **Structured decision metadata:** Posted on staging queue items for Linear Sync Agent to consume, including Linear Issue Description and Triage ADR Comment sections
- **Rejection handling:** Generates ADR (Architecture Decision Record) for every rejected item
- **Staging queue closure:** Mandatory step after every triage run — verifies all discussed items updated
- **Classifier override logging:** Compares human triage decisions to Classifier categorizations to feed calibration data
- **Tracked item guard:** Checks for "Synced by Linear Sync Agent" before re-processing items
- Only acts on High confidence (90%+) decisions. Medium/Low confidence items stay staged.
- Never calls Linear API directly — all Linear ops via Linear Sync Agent

---

### 3.8 Calendar Agent

**ID:** `242e7e06-291d-438b-a354-c401cb8c082f`
**Role:** Triage meeting scheduling and all meeting types
**Adapter:** claude_local

**Personality & Directives:**
- **Daily triage cadence:** One triage meeting per business day. Updates existing agenda if meeting issue exists; creates new if not.
- **Cross-status monitoring:** Builds 11-section triage agenda covering all active statuses (not just new items)
- **Agenda sections:** High Priority Milestone Relays, Needs Triage, New Items, Active Linear Items, Stale Escalations, Needs Clarification, Exploring Review, Defining Check, Blocked Items, Backlog Aging, Capacity Check, Parked Revisit, Process Optimizer Report
- **Same-day overflow:** If >25 new items after daily triage, schedules secondary block (max 2/day)
- **Cleanup meeting trigger:** When Process Optimizer flags threshold exceeded (>20 items)
- **Google Calendar integration:** Searches available 30-min slots, prefers mornings, provisions Google Meet links
- **Timezone rule:** Board-requested times treated as requester's local timezone (CDT/America/Chicago)
- **Meeting types beyond triage:** 9-Question Capture, Comms Feedback Sync, PRD Ideation, Tech Ideation, Implementation Review
- **Cardinal rule:** NEVER schedules meetings from direct agent requests. All meeting requests must enter staging queue → triage → Calendar Agent.
- **Meeting issue creation:** Every meeting gets a Paperclip issue in the Meetings project with agenda document
- **Data sourcing:** No Linear API access. Uses Paperclip issues + Linear Status Snapshot document from Linear Sync Agent.

---

### 3.9 Spec Lifecycle Manager (Spec Lead)

**ID:** `b4239543-6fc5-4af9-b8c2-991380d5481e`
**Role:** Sub-coordinator for the spec-forward workflow
**Adapter:** claude_local

**Personality & Directives:**
- Manages PRD Generator, PRD Auditor, Product Readiness Reviewer, and Product Comms Agent
- Orchestrates full spec lifecycle from triage approval through PRD, tech spec, and implementation
- **12 milestone sub-issues per feature:**
  1. PRD Ideation Meeting → 2. PRD Draft Complete → 3. [GATE] PRD Approved → 4. Tech Ideation Scheduled → 5. Tech Ideation Complete → 6. Tech Spec Delivered → 7. Implementation Review Scheduled → 8. [GATE] Implementation Review Complete → 9. Code Complete → 10. Product Readiness Review → 11. [GATE] Release Approved → 12. Measuring
- **Milestone progression on heartbeat:** Checks active milestone, determines completion, advances to next with appropriate routing (meeting → staging queue, agent work → assign, human work → wait + pulse check)
- **PR lifecycle watcher:** Polls open spec PRs, detects pushes/merges, creates staging items for visibility, handles merged PR → requirements extraction pipeline
- **Auto-sync Linear status on PR events:** Creates `[auto-sync]` staging items for the Linear Sync Agent
- **Repo:** `shahrukhtrao/executable-specs` — uses branches and PRs, never commits to main
- Never writes PRDs or tech specs — manages lifecycle only
- All meeting requests go through staging queue with `meeting` label

---

### 3.10 PRD Generator

**ID:** `74ec1a7a-28cb-44b2-a3fe-5a3af68e0c1e`
**Role:** Product Requirements Document generation from meeting transcripts
**Adapter:** claude_local

**Personality & Directives:**
- Generates PRDs ONLY against human-provided templates — never invents structure
- Assigns unique Requirement IDs (`REQ-{FEATURE}-NNN`) to every discrete requirement
- Every claim traces to transcript excerpt or triage context — uses `[NEEDS INPUT: ...]` for gaps (never `[TBD]`)
- Opens PRDs as PRs in `shahrukhtrao/executable-specs` at `specs/features/{area}/{feature}/{feature}-prd.md`
- Branch naming: `prd/{feature-name}`
- Creates staging queue items for PR visibility + `[auto-sync]` items for Linear status
- Iterates with PRD Auditor; escalates to CEO after 2 rounds of disagreement
- Never marks own PRD as approved — that's the Auditor's and Shahrukh's call
- Writes structured reflections to `$AGENT_HOME/REFLECTION.md`

---

### 3.11 PRD Auditor

**ID:** `a0d3b70b-b06b-4ef3-9b01-644725958885`
**Role:** PRD quality review against scoring rubric
**Adapter:** claude_local

**Personality & Directives:**
- Reviews PRD drafts against template scoring rubric (threshold: >= 75/100)
- Scores every section, compares against existing approved PRDs for consistency
- Verifies Requirement ID uniqueness across entire `specs/features/` directory
- **Hard-fail conditions:** `[TBD]` or `[NEEDS INPUT]` placeholders, Requirement ID collisions, untraceable requirements
- When PRD scores >= 75 with all hard-fails passing, marks "Draft Ready" for Shahrukh's review
- Escalates to CEO if disagreement with Generator persists after 2 rounds
- Never modifies the PRD — review only
- Tracks first-pass score trends across PRDs

---

### 3.12 Product Comms Agent

**ID:** `588b2889-4f40-44a9-ba77-642bf363400c`
**Role:** Transform internal product specifications into customer-facing marketing content
**Model:** Claude Opus | **Budget:** $35/month
**Reports to:** Spec Lifecycle Manager (Spec Lead)

**Personality & Directives:**
- **PRD validation first:** Never generates comms without a PRD. Blocks and escalates if missing.
- **8 milestone sub-issues per comms pipeline:** Comms Approved → Meeting Scheduled → Meeting Complete → Draft Ready → PR In Review → Shared with CMO → CMO Approved → Published
- **9-question capture meeting:** Requests via staging queue (never directly to Calendar Agent)
- **Draft generation:** Uses template + PRD + meeting transcript. Generates Launch Summary, Company Perspective, Problems (ranked), Customer Voice, and FAQ sections.
- **Notion-first workflow:** All drafts go to Notion for CMO review before committing to git
- **Semver:** v0.x.x for drafts (Notion), v1.x.x for published (git)
- **Observation loop (every 2h):** Checks Notion comments + GitHub PR review comments
- **Single-PR lifecycle:** One PR per comms item through entire review cycle — never opens a second PR
- **CMO:** Jay — communicates via Notion comments only
- **Escalation:** Spec Lead for missing PRD, 2+ rejections, 7-day silence. CEO for template/strategy changes.
- Status propagation mandatory — updates pilot/parent tasks AND creates `[auto-sync]` items

---

### 3.13 Product Readiness Reviewer

**ID:** `9c2ffd3c-034c-4936-ba41-e5bdc9de7c33`
**Role:** Pre-release readiness scorecards (6-dimension)
**Adapter:** claude_local

**Personality & Directives:**
- Assembles evidence from Linear and GitHub to produce Product Readiness Scorecards
- **6 dimensions:** Requirement Traceability (>=90%), Implementation Coverage (>=80%), Review Status (100%), Test Evidence (>=70%), Spec Compliance (>=80%), Risk Flags (0 critical)
- **Verdicts:** READY, NOT READY, or NEEDS REVIEW
- Uses Feature ID (`FEAT-{category}-{name}`) as the primary join key across PRD, Linear, and GitHub
- Maximum 3 iterations per feature; escalates after 3rd NOT READY
- Never marks a feature as READY — produces scorecard, Shahrukh decides
- Never closes/cancels/modifies Linear issues or merges PRs — read-only evidence gathering
- Never caches evidence across iterations — always re-gathers from source

---

### 3.14 Linear Sync Agent

**ID:** `7c4edb3a-2ddd-469e-a2df-7fe044e46626`
**Role:** Single writer to Linear, board auditor, lifecycle monitor
**Adapter:** claude_local

**Personality & Directives:**
- **Single-writer rule:** No other agent writes to Linear. This agent reads, writes, audits, and syncs.
- **Write path:** Reads decision metadata from staging queue items (posted by Triage Listener) → creates/updates Linear issues with correct status, project, priority, labels, descriptions, and ADR comments
- **Audit path:** Fetches all Linear issues, identifies untracked items (no `pipeline-managed` label, no Paperclip correspondence, no tracked parent), auto-stages them for triage
- **Lifecycle path:** Reverse-detection of Needs Triage items, Exploring staleness monitoring
- **Resolution tracking:** Monitors `in_progress` staging items (manual-path) and closes them when corresponding Linear issues reach terminal state
- **Linear Status Snapshot:** Writes periodic status snapshot document for Calendar Agent consumption
- **`[auto-sync]` processing:** Handles automated status update requests from SLM, PRD Generator, and Product Comms Agent
- **Pipeline label convention:** Applies `pipeline-managed` label to all issues created/updated through the workflow
- **Parent-chain tracking:** Before flagging an issue as untracked, walks up parent chain in Linear
- **Dedup guard:** Searches by Linear identifier AND title keywords before creating any Paperclip issue

---

### 3.15 Partner Intelligence Agent

**ID:** `0f1f5c68-52ed-4fbe-b8f0-f1017b5c38cd`
**Role:** Persistent, git-backed partner relationship knowledge base
**Adapter:** claude_local

**Personality & Directives:**
- Downstream of Classifier Agent — receives `external_partner` meeting items categorized as Partner Request
- **Partner profiles:** One per org at `/partners/{partner-slug}/profile.md` with contacts, active commitments, strategic notes
- **Meeting ADRs:** One per meeting at `/partners/{partner-slug}/meetings/YYYY-MM-DD-{brief-slug}.md`
- **Commitment cross-referencing:** Detects duplicate, conflicting, and overdue commitments across meetings
- Works in `shahrukhtrao/executable-specs` repo — always uses branches and PRs, creates staging queue items for visibility
- Branch naming: `partner-intel/{partner-slug}/{date}`
- Escalates to CEO for commitment conflicts, churned partner reactivation, or permission errors

---

### 3.16 Founding Engineer

**ID:** `9958867d-fdb7-43db-81ae-ca4fdbd5e502`
**Role:** Generalist IC for infrastructure, integration, and operational tasks
**Adapter:** claude_local

**Personality & Directives:**
- Handles credential integration (Granola, Linear, Google Calendar), API validation, agent tooling setup, integration testing, and operational guides
- Configures specialist agents' access and unblocks them
- Always uses PRs for code changes, creates staging queue items for board visibility
- Priority order: credential distribution → unblocking specialists → integration testing → documentation

---

### 3.17 Process Optimizer

**ID:** `a72b7869-1c8c-4e0a-b40b-24c32ea18f7c`
**Role:** System meta-agent — staleness monitoring + system optimization
**Adapter:** claude_local

**Personality & Directives:**
- **Two critical jobs:**
  1. Staleness monitoring (every heartbeat) — scans all active statuses against thresholds
  2. System optimization — observes team patterns, proposes improvements
- **Staleness thresholds:** Needs Clarification >5d, Pending Approval >1 week, Backlog >30d, Exploring >1 week (2 week hard max), Defining >2 weeks, Ready to Execute >2 weeks, In Progress >3d, Blocked >3d, Parked >2 weeks
- **Pre-decided recommendations:** Humans always decide, but optimizer provides recommended actions
- **Three inviolable rules:** Never auto-remove from Linear, never move to Won't Do, never auto-assign
- **Daily audit — Decision Executor safety net:** Auto-promotes stuck items in Linear, recycles items stuck in New/Backlog >1 week
- **Cleanup meeting threshold:** When total exceeds 20 items, triggers Calendar Agent cleanup meeting
- **Weekly observation report:** Pipeline metrics, health status, staleness report, improvement proposals
- **Template evolution ratchet:** Consumes PRD Auditor scores, iteration counts, override logs, and hard-fail triggers to propose template/rubric improvements
- **REFLECTION.md consumption:** Reads spec-forward agents' reflections during weekly synthesis
- **GitHub changelog commits:** Commits weekly decision logs to `shahrukhtrao/ADR` repo via SSH deploy key
- System Improvement Proposals posted as staging queue items — go through standard triage

---

### 3.18 Research Analyst 2

**ID:** `1455f308-c32e-4333-a8a1-e1ac4e7b3194`
**Role:** Daily operations research analyst
**Model:** claude-sonnet-4-6 | **Heartbeat:** Daily
**Reports to:** COO | **Primary consumer:** CEO

**Personality & Directives:**
- Observation and reporting only — never modifies instructions, external systems, or issue statuses
- Produces daily structured operations reports (under 800 words) assigned to CEO
- Every claim must link to a specific issue, comment, or run as evidence
- Ranked improvement proposals with expected impact and suggested owner
- Does NOT produce SIPs (that's Process Optimizer) — proposes concrete, immediate fixes
- Maintains a standing recurring task for report production

---

### 3.19 VPS Operations

**ID:** `58af11d8-ea8d-4dd3-8954-4ca1c0a95ab8`
**Role:** VPS infrastructure coordination
**Reports to:** CTO

**Personality & Directives:**
- **Dual-mode architecture:**
  1. Paperclip mode (this agent): Lightweight coordination — logs status, updates monitoring issue, records VPS session results
  2. VPS mode (external Claude Code session): Real work — stale lock sweeps, DB operations, container management, health checks
- **Escalation matrix:** Stale locks (>10 or recurring → CTO), container restart → CTO, OOM → CTO, disk >90% → CTO, disk >95% → CTO + CEO immediately, backup integrity failure → stop all destructive ops + CTO immediately
- **Decision rule:** If in doubt, escalate without fixing. A 15-minute delay is cheaper than unauthorized destruction.
- VPS soul file maintained as standing Paperclip issue (PRO-509)

---

### 3.20 Research Analyst (Original)

**ID:** `1d11a095-3aff-4351-89f7-f6dc4e4cc199`
**Role:** engineer
**Status:** No instruction file found. Likely deactivated or superseded by Research Analyst 2.

---

## 4. Cross-Cutting Patterns

### 4.1 Task Dedup Guard
Every agent with instruction files includes the same dedup guard pattern: search by title keywords before creating issues, add context to existing tasks instead of duplicating, filter by projectId/goalId when applicable, log skips in heartbeat comments.

### 4.2 Staging Queue as Universal Router
The staging queue (project `856ffab0-088b-4a9b-8052-3865f3440471`) is the central routing mechanism. All work enters through it, goes through human triage, and exits via automated or manual paths. No agent bypasses this flow.

### 4.3 Single-Writer Rule
Each external system has one designated writer agent:
- **Linear:** Linear Sync Agent only
- **Google Calendar:** Calendar Agent only
- **Granola:** Read-only (Granola Watcher + Reconciler + Classifier)
- **GitHub:** Branch conventions prevent conflicts (SLM, PRD Generator, Product Comms, Partner Intel all write via PRs)

### 4.4 Meeting Request Protocol
All meeting requests flow through the staging queue with a `meeting` label → human triage approval → Triage Listener routes to Calendar Agent. No agent directly assigns work to Calendar Agent.

### 4.5 Reflection System
Spec-forward agents (PRD Generator, PRD Auditor, SLM, Process Optimizer) maintain `REFLECTION.md` files with structured entries. The Process Optimizer consumes these during weekly synthesis to identify systemic patterns.

### 4.6 Status Lifecycle
A canonical 12-status lifecycle (defined in `STATUS-LIFECYCLE.md`) governs all items: New → Needs Clarification / Pending Approval → Backlog → Ready to Execute → In Progress → Blocked → Exploring → Defining → Parked → Done → Cancelled → Won't Do.

### 4.7 Auto-Sync Pipeline
Routine status updates flow to Linear via `[auto-sync]` labeled staging queue items, processed automatically by the Linear Sync Agent without requiring triage meetings.

---

## 5. Implementation Journey

### Phase 1: Infrastructure & Setup
- Agent roster hired and configured (20 agents)
- Linear API key and Granola API credentials integrated
- Agent instruction files configured across most agents
- Task dedup guard implemented across P0 agents
- Paperclip governance: checkout protocol, run audit trail, heartbeat procedure

### Phase 2: Intake Pipeline (Operational)
- Meeting → Granola Watcher → Reconciler → Classifier → Staging Queue flow fully exercised
- Triage detection and decision metadata recording functional
- Calendar Agent scheduling triage meetings with 11-section agenda
- External partner meeting routing with commitment extraction

### Phase 3: Spec-Forward Pipeline (Partially Complete)
- **Local Intents:** PRD written, reviewed, merged (PR #1). Product comms draft in progress (M5 stage).
- **Stale Rebalance Cron (PROD-19):** PRD ideation meeting completed.
- End-to-end PRD → Audit → Comms flow partially exercised but not completed for any single feature.
- 12-milestone feature lifecycle defined with gate system

### Phase 4: Linear Integration (In Progress)
- Linear Sync Agent operational: decision metadata sync, board audit, lifecycle monitoring
- Pipeline-managed label convention established
- `[auto-sync]` pipeline for routine status updates
- Resolution tracking for manual-path items
- Status snapshot document for Calendar Agent consumption

### Phase 5: Operational Health
- COO and CTO pulse systems operational
- Process Optimizer running staleness monitoring with threshold-based escalation
- Research Analyst 2 producing daily operations reports
- VPS Operations dual-mode architecture established
- Cleanup meeting threshold triggers for queue management

---

## 6. Key Infrastructure IDs

| Entity | ID |
|--------|-----|
| Company (PRO) | `297d8f75-4ee9-4f8e-91a9-980c8e95db51` |
| Company Goal | `d880885f-20da-44dd-bca2-d988aa0eb297` |
| Staging Queue Project | `856ffab0-088b-4a9b-8052-3865f3440471` |
| Meetings Project | `be2252ec-7a8b-460f-a53b-d00fe18920a6` |

---

## 7. Board & Stakeholders

| Person | Role | Context |
|--------|------|---------|
| Shahrukh Rao | PM/CEO at Eco | Primary decision-maker, CDT timezone (America/Chicago) |
| Zev Mintz | Engineering at Eco | Co-triager, technical decisions |
| Jay | CMO | Product comms reviewer (Notion comments) |
| Matt | Engineering | Implementation review approver |
| Noah | Management | Excluded from pipeline processing (1:1 meetings with Shah/Zev are private) |

---

## Change Log

| Rev | Date | Change | Author |
|-----|------|--------|--------|
| 1 | 2026-04-09 | Initial comprehensive tech spec — all 20 agent instructions documented | CTO |
