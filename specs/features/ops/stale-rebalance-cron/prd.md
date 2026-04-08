# Stale Rebalance Cron Job PRD

**Feature ID:** FEAT-ops-stale-rebalance-cron

**Status:** Draft

**Last Updated:** 2026-04-07



---



## PART 1: PRODUCT REQUIREMENTS



### Problem

- Rebalance jobs that encounter infrastructure failures, timeouts, or uncaught errors get stuck in an in-progress state indefinitely, with no automatic timeout or failure detection mechanism to resolve them

- Stale jobs surface incorrect status to end users — jobs appear as still running when they have effectively failed — degrading trust in platform accuracy

- Manual cleanup requires engineers to run direct database queries to identify and patch stuck jobs, creating unsustainable operational toil as job volume grows

- Monitoring and alerting systems fire continuously on known-stale records, producing noise that obscures genuine incidents and reduces on-call effectiveness



### Solution

- A scheduled background process that automatically detects rebalance jobs stuck in an in-progress state beyond a configurable staleness threshold

- Qualifying stale jobs are transitioned to a terminal failed state with a system-generated reason indicating they were resolved by the stale-job cron

- Structured logs and metrics are emitted for every job the cron resolves, providing full operational visibility without manual investigation

- The staleness threshold is configurable to accommodate different environments and operational requirements without code changes



### Value/Impact

- Eliminates a class of persistent incorrect state visible to end users, restoring accuracy of rebalance job status across the platform

- Removes recurring operational toil from engineering and on-call teams by automating a manual database intervention workflow

- Restores monitoring and alerting reliability by clearing stale records that cause continuous false alarms

- Unblocks downstream systems (notifications, analytics, reporting) that depend on accurate terminal job state



### User Stories



#### As an end user Eco operations

- I want rebalance jobs that have failed to show an accurate failed status, so that I am not misled into thinking a job is still processing
- I want timely resolution of stuck jobs, so that Eco operations can maintain a representative, precise, and accurate balance state
- I want timely resolution of stuck jobs, so that I can take corrective action (e.g., re-trigger a rebalance) without waiting indefinitely



#### As an Engineer / On-Call Operator

- I want stale jobs to be automatically detected and resolved, so that I do not need to manually query the database to find and patch stuck records
- I want structured logs emitted for every job the cron transitions, so that I can observe cron activity and audit its decisions without manual investigation
- I want monitoring alerts to stop firing on known-stale jobs, so that alert noise is reduced and genuine incidents are surfaced clearly



#### As a Platform / Infrastructure System

- I want job state to accurately reflect terminal outcomes, so that downstream systems (notifications, analytics, reporting) operate on reliable data
- I want the cron to be idempotent, so that running it multiple times against the same job produces no unintended side effects



#### As an Operations Lead

- I want a configurable staleness threshold, so that the detection window can be tuned per environment without requiring code changes
- I want metrics on cron execution (jobs resolved per run, run duration), so that I can confirm the cron is operating as expected over time



### Services Impacted

- **Rebalance Job Service** (primary — reads job state to detect stale records; writes state transitions from in-progress to failed)
- **Cron Infrastructure** (new cron job registration and scheduling)
- **Observability / Monitoring** (structured log and metric emission for each resolved job and each cron run)
- **Notification Service** (may need to deliver failure notifications to affected users when jobs are marked failed)
- **Job State Database** (read queries on `status`, `updated_at`/`started_at` columns for staleness detection; write updates to transition state)



### Hard Requirements & Constraints

- **Detection only, no retries:** The cron marks stale jobs as failed; it must not retry or re-queue them. Retry logic is a separate concern
- **No deletion:** Jobs must never be deleted — the full audit trail of all job records must be preserved
- **Idempotency:** Running the cron multiple times on the same stale job must be safe and produce the same outcome
- **Configurable threshold:** The staleness duration must be configurable (e.g., via environment variable or configuration file), not hardcoded
- **Structured logging:** Every job state transition performed by the cron must emit a structured log entry including job ID, previous state, new state, and reason
- **No root-cause remediation:** This cron resolves symptoms (stale state); it does not address the underlying causes of jobs getting stuck



### Success Criteria

**No rebalance jobs persist in an in-progress state beyond the staleness threshold**
- Measure: Query job state database for in-progress jobs exceeding the threshold; compare count before and after cron deployment over a baseline period

**On-call engineers can confirm cron activity without manual database queries**
- Measure: Verify structured logs and metrics are accessible in the observability platform; confirm engineers use dashboards instead of ad-hoc queries

**Customer-visible job status accurately reflects terminal state for stuck jobs**
- Measure: Audit user-facing job status for previously-stale jobs; compare pre-launch vs post-launch accuracy

**Operational alert noise from stale jobs is eliminated**
- Measure: Track alert volume for stale-job-related alerts before and after deployment; compare frequency over a baseline period



### Verification Criteria



#### Required Analytics Events

- `REQ-STALE-REBALANCE-001`: `stale_job_detected` — Emitted when the cron identifies a job exceeding the staleness threshold, before state transition
- `REQ-STALE-REBALANCE-002`: `stale_job_marked_failed` — Emitted after a stale job is successfully transitioned to failed state, includes job ID and elapsed duration
- `REQ-STALE-REBALANCE-003`: `stale_cron_run_completed` — Emitted at the end of each cron execution, includes count of jobs resolved and total run duration



#### Required Test Scenarios

- `REQ-STALE-REBALANCE-010`: Happy path — A job in in-progress state exceeding the threshold is detected and marked as failed with a system-generated reason
- `REQ-STALE-REBALANCE-011`: No stale jobs — Cron runs when no jobs exceed the threshold; completes with zero transitions and emits a completion event
- `REQ-STALE-REBALANCE-012`: Idempotency — Cron runs twice on the same stale job; the second run does not re-transition or duplicate log entries
- `REQ-STALE-REBALANCE-013`: Recently started job — A job in in-progress state within the threshold is not transitioned
- `REQ-STALE-REBALANCE-014`: Multiple stale jobs — Several jobs exceed the threshold in a single run; all are transitioned and individually logged
- `REQ-STALE-REBALANCE-015`: Database write failure — Cron fails to update a job's state; emits an error log and continues processing remaining jobs



#### Performance Thresholds

- `REQ-STALE-REBALANCE-020`: Cron execution duration — Must complete within a reasonable time relative to the scheduling interval; measure by tracking p50 and p95 run durations over a baseline period
- `REQ-STALE-REBALANCE-021`: Database query impact — Staleness detection queries must not degrade production database performance; measure by comparing query latency and database load metrics before and after deployment



#### Acceptance Criteria (Machine-Checkable)

- `REQ-STALE-REBALANCE-030`: Given a rebalance job in in-progress state with last update time exceeding the staleness threshold, When the cron executes, Then the job status is set to failed with reason containing "stale-job-cron"
- `REQ-STALE-REBALANCE-031`: Given a rebalance job in in-progress state with last update time within the staleness threshold, When the cron executes, Then the job status remains unchanged
- `REQ-STALE-REBALANCE-032`: Given a rebalance job already in failed state, When the cron executes, Then no state transition occurs and no duplicate log is emitted
- `REQ-STALE-REBALANCE-033`: Given the cron resolves one or more stale jobs, When the run completes, Then a structured log entry is emitted for each transitioned job containing job ID, previous state, new state, and timestamp
- `REQ-STALE-REBALANCE-034`: Given the staleness threshold is changed via configuration, When the cron executes, Then it uses the updated threshold without requiring a code deployment
- `REQ-STALE-REBALANCE-035`: Given a database write failure during state transition, When the cron encounters the error, Then it logs the failure and continues processing remaining stale jobs without crashing



---



## Appendix: Context for Engineers

This feature addresses Linear issue PROD-19. The primary motivation is eliminating manual operational toil and restoring accurate job state for end users and downstream systems. The cron is intentionally scoped to detection and marking only — retry logic, root-cause fixes for stuck jobs, and notification delivery are separate workstreams. The staleness threshold should be conservative initially and tunable without redeployment.
