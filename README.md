# Executable Specs

Product specifications and templates for Eco's spec-forward development workflow.

## Structure

```
templates/              # Human-provided, evolve via PRs
  prd-template.md       # Blank PRD template (Part 1 + Part 2)
  prd-writing-guide.md  # Full PRD writing guide with examples and anti-patterns
  scoring/
    prd-scoring-rubric.json      # Weights, thresholds, hard-fail criteria
    tech-spec-scoring-rubric.json

specs/
  features/             # One directory per feature
    {category}/
      {feature-name}/
        {feature}-prd.md
        {feature}-tech-spec.md
        {feature}-comms-brief.md

roadmap/
  current-quarter.md
  backlog-priorities.md

adr/                    # Architecture Decision Records
  {NNN}-{title}.md

prompts/
  meeting-agendas/
    prd-ideation-agenda.md
```

## Workflow

1. Triage approves an item
2. Calendar Agent schedules a PRD Ideation Meeting
3. Meeting happens, Granola captures transcript
4. PRD Generator builds draft from transcript against `templates/prd-template.md`
5. PRD Auditor reviews against `templates/scoring/prd-scoring-rubric.json` (threshold: >= 75)
6. PRD opened as a PR for human review
7. On approval, Spec Lifecycle Manager creates linked doc for engineers to fill in tech spec
8. Tech spec scored against `templates/scoring/tech-spec-scoring-rubric.json` (threshold: >= 80)

## Rules

- PRDs follow the [5 Critical Rules](templates/prd-writing-guide.md#the-5-critical-rules) from the writing guide
- Every PRD requirement gets a unique Requirement ID (REQ-001, REQ-002, ...)
- Agents open PRs — never commit directly to main
- Human approval gates (marked with lock) cannot be bypassed by agents
- Templates evolve through PRs with board approval
