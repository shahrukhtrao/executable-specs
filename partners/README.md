# Partner Knowledge Base

This directory is the source of truth for all external partner relationships — maintained by the Partner Intelligence Agent.

## Structure

```
partners/
  README.md                      — this file
  _templates/
    profile.md                   — template for new partner profiles
    meeting-adr.md               — template for meeting Architecture Decision Records
  {partner-slug}/
    profile.md                   — partner profile (contacts, commitments, status)
    meetings/
      YYYY-MM-DD-{brief-slug}.md — one ADR per meeting
```

**Partner slug:** lowercase, hyphenated org name (e.g., `b2c2`, `circle`, `fireblocks`).

## How It Works

1. A meeting with an external partner is processed by the Reconciler Agent, which outputs structured commitments.
2. The Classifier Agent labels the item as `Partner Request` and routes it to the Partner Intelligence Agent.
3. The Partner Intelligence Agent:
   - Creates or updates `{partner-slug}/profile.md`
   - Writes a new meeting ADR at `{partner-slug}/meetings/YYYY-MM-DD-{brief-slug}.md`
   - Cross-references commitments for duplicates, conflicts, and overdue items
   - Opens a PR for human review (new partners) or commits directly to main (routine updates)

## Relationship Status Values

| Status     | Meaning                                          |
|------------|--------------------------------------------------|
| `prospect` | Initial conversations, no formal agreement       |
| `active`   | Live relationship with ongoing work              |
| `paused`   | Temporarily inactive, intent to resume           |
| `churned`  | Relationship ended                               |

## Commitment Tracking

All commitments (ours and theirs) are tracked in partner profiles. The Partner Intelligence Agent flags:

- **Duplicate commitments** — same promise restated across multiple meetings
- **Conflicting commitments** — new promise contradicts a prior one
- **Overdue commitments** — deadline passed without resolution

## Rules

- The Partner Intelligence Agent is the only writer of this directory
- Humans review and approve via PR (new partners or significant strategic context)
- Routine profile updates commit directly to main
- Templates in `_templates/` are the canonical schema — do not deviate without a PR
