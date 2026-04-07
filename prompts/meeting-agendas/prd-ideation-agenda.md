# PRD Ideation Meeting Agenda

**Duration:** 60 minutes
**Attendees:** Relevant stakeholders, product, engineering lead
**Purpose:** Generate the raw material for a PRD through structured discussion

---

## Pre-Meeting (shared async, 24h before)

- [ ] Triage context summary (from classified item)
- [ ] Any prior research or customer feedback
- [ ] Related existing features or systems

---

## Agenda

### 1. Problem Framing (10 min)

- What problem are we solving and for whom?
- What evidence do we have that this is a real problem? (customer feedback, data, incidents)
- What is the business impact of NOT solving it?

### 2. User Stories Walkthrough (15 min)

- Who are the distinct user roles affected?
- For each role: what do they need to accomplish? What does success look like?
- What are the edge cases or failure modes users will hit?

### 3. Solution Direction (10 min)

- At a high level, WHAT should the solution do? (not HOW — no architecture)
- What existing capabilities does this build on?
- What should this explicitly NOT do? (scope boundaries)

### 4. Services & Dependencies (10 min)

- Which services will this feature read from or write to?
- Are there upstream or downstream systems affected?
- Any external dependencies (APIs, vendors, data sources)?

### 5. Requirements & Constraints (10 min)

- What are the hard requirements (must-haves vs nice-to-haves)?
- What constraints exist? (timeline, compliance, performance, backwards compatibility)
- What does "done" look like? How will we measure success qualitatively?

### 6. Open Questions & Next Steps (5 min)

- What do we still not know?
- Who owns resolving each open question?
- Confirm: does this need product comms? (tag for CMO alignment if yes)

---

## Post-Meeting

- Granola captures full transcript
- PRD Generator builds draft PRD from transcript + triage context
- PRD Auditor reviews against template and scoring rubric
