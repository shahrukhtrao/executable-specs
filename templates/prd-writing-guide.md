# PRD Template & Writing Guide



**Purpose:** This template codifies best practices for writing Product Requirements Documents (PRDs) at Eco based on real feedback from engineering teams.



**Target Audience:** Product managers creating PRDs for new features or capabilities.



**Last Updated:** 2025-12-15



---



## TABLE OF CONTENTS



1\. [Template Overview & Philosophy]\(#section-1-template-overview--philosophy)

2\. [Section-by-Section Guidance]\(#section-2-section-by-section-guidance)

3\. [Blank Template (Ready to Use)]\(#section-3-blank-template-ready-to-use)

4\. [Anti-Patterns to Avoid]\(#section-4-anti-patterns-to-avoid)

5\. [Real-World Example: Analytics PRD]\(#section-5-real-world-example-analytics-prd)



---



# SECTION 1: Template Overview & Philosophy



## What is a PRD vs Spec?



**PRD (Product Requirements Document):**

- Defines **WHAT** needs to be built and **WHY** it matters

- Written by product team

- Focuses on user needs, business value, and requirements

- Target length: **1-2 pages (100-200 lines)**

- Audience: Product managers, stakeholders, engineers (initial read)



**Spec (Technical Specification):**

- Defines **HOW** to build it

- Written by engineering team

- Focuses on architecture, implementation details, technical decisions

- Target length: Can be longer (500+ lines) with technical details

- Audience: Engineers implementing the feature



**This template is for PRDs only.** Engineers create specs based on PRD requirements.



---



## The Collaborative Fill-in-Blank Model



**Traditional Approach (PROBLEMS):**

- Product writes everything including architecture

- Engineers redesign architecture anyway (wasted effort)

- PRD becomes too long (1000+ lines)

- Engineers don't read long PRDs ("too heavy, not approachable")



**Collaborative Approach (SOLUTION):**



**Step 1: Product writes Part 1 (\~100 lines)**

- Problem statement

- Solution overview (WHAT not HOW)

- User stories

- Requirements and constraints

- Success criteria



**Step 2: Engineers read Part 1**

- Discuss requirements and ask clarifying questions

- Validate constraints are feasible

- Propose technical approach



**Step 3: Engineers fill in Part 2 (\~50 lines)**

- Propose architecture based on requirements

- Identify risks and mitigations

- Define implementation phases

- Specify data schemas and performance strategies



**Why This Works:**

- **Separation of concerns:** Product focuses on WHAT/WHY (their expertise), engineers focus on HOW (their expertise)

- **Empowers engineers:** Engineers propose architecture instead of having it dictated

- **Efficient:** No wasted effort on technical details that get redesigned

- **Readable:** 100-200 lines can be read in 5 minutes

- **Actionable:** Clear fill-in-blank structure guides engineering planning



---



## The 5 Critical Rules



These rules are **ABSOLUTE** and based on real engineering feedback. Violating them creates problems.



### Rule #1: NO NUMBERS (ABSOLUTE)



**The Rule:**

Never put specific numbers in PRDs: no percentages, dollar amounts, time targets, or metrics.



**Why:**

\> "THIS IS CRITICAL NEVER PUT NUMBERS BECAUSE THERE IS NO WAY TO VALIDATE"

\> — Engineering team feedback



Numbers in PRDs create false precision. You cannot validate "$40K/year RPC costs" or "90% self-service adoption" upfront. They become stale, unverifiable claims that undermine credibility.



**What to Do Instead:**

Use qualitative language + suggest how to measure post-launch.



**Examples:**



| ❌ BAD (Specific Numbers) | ✅ GOOD (Qualitative + Measurement) |

|----------------------------|--------------------------------------|

| "$40,000/year RPC costs" | "Substantial RPC infrastructure costs" + Measure: "Track monthly RPC invoice totals comparing baseline vs post-launch" |

| "90% self-service adoption" | "High self-service adoption" + Measure: "Track reduction in engineering analytics requests (Slack, tickets) pre/post launch" |

| "\<3 seconds execution time" | "Must not add latency to solver" + Measure: "Benchmark solver execution times pre/post integration" |

| "50%+ cost reduction" | "Significant cost reduction" + Measure: "Calculate reduction percentage after establishing 3-month baseline" |

| "\<500ms query performance" | "Fast query performance" + Measure: "Track query response times (p50, p95, p99 latencies)" |

| "80% reduction in analytics backlog" | "Substantial reduction in analytics dependency" + Measure: "Compare engineering request volumes pre/post Metabase launch" |



**Key Insight:**

Replace numbers with qualitative descriptors + measurement methodology. This is honest ("we'll measure this") vs false precision ("it will be exactly 90%").



---



### Rule #2: Requirements Not Architecture



**The Rule:**

Product defines WHAT needs to exist and WHY. Engineers define HOW to build it.



**Why:**

\> "Product should just come in with product requirements, going into the architecture this deep might be a waste of time for product as the engineers will come in and propose their own architecture"

\> — Engineering team feedback



Engineers will redesign the architecture based on technical constraints, existing systems, and implementation trade-offs. Time spent by product defining architecture is wasted effort.



**What to Do Instead:**

Describe capabilities and requirements; let engineers propose implementation.



**Examples:**



| ❌ BAD (Product Defines Architecture) | ✅ GOOD (Product Defines Requirements) |

|---------------------------------------|----------------------------------------|

| "Balance Tracker Service with Raft consensus RPC polling at 1-2 second intervals" | "Real-time balance monitoring establishing single source of truth" + Constraint: "Must handle RPC failures gracefully" |

| "PostgreSQL database with TimescaleDB extension for time-series data, 500ms query SLA" | "Unified analytics database consolidating operational data" + Constraint: "Must support fast query performance for dashboards" |

| "Metabase connected via read replica with materialized views refreshing every 5 minutes" | "Self-service analytics interface enabling product/ops queries" + Constraint: "Must not impact production database performance" |

| "Circuit Breaker using Redis pub/sub for real-time balance deviation events, \<100ms trigger latency" | "Automated circuit breaker protecting against balance discrepancies" + Constraint: "Must detect deviations quickly and pause solver automatically" |



**Key Insight:**

Describe the end state (what exists) and constraints (requirements) rather than implementation details (polling intervals, specific technologies, exact architectures).



---



### Rule #3: User Story Format



**The Rule:**

Organize requirements by user type, not by component/service. Use "As a [role], I want [capability], so that [business value]" structure.



**Why:**

\> "When describing priorities, try to scope them in terms of user stories not per service as per service doesn't really make sense. In this example: we could create the full balance service start to finish but until it's hooked up to anything, nothing is actually done from the users perspective."

\> — Engineering team feedback



Organizing by components creates a technical view. User stories create an outcome view focused on actual value delivery.



**Examples:**



| ❌ BAD (Component-Based Organization) | ✅ GOOD (User Story Format) |

|---------------------------------------|------------------------------|

| **Component 1: Balance Tracker Service**\<br>- Purpose: Real-time balance monitoring\<br>- Features: RPC polling, consensus mechanism\<br>- Acceptance criteria: \<1s data freshness | **As an Operations Lead:**\<br>- I want real-time balance monitoring, so that I can detect anomalies quickly\<br>- I want automated alerts on balance deviations, so that I can prevent financial loss |

| **Component 2: Unified Analytics DB**\<br>- Purpose: Consolidate operational data\<br>- Features: Fulfillment tracking, rebalancing logs\<br>- Acceptance criteria: \<500ms query latency | **As a Product Manager:**\<br>- I want to see fulfillment history and trends, so that I can analyze performance patterns\<br>- I want to query operational data myself, so that I don't depend on engineers |

| **Component 3: Circuit Breaker**\<br>- Purpose: Automated solver shutdown\<br>- Features: Balance deviation detection, solver pause\<br>- Acceptance criteria: \<100ms trigger time | **As an Engineer:**\<br>- I want automated circuit breakers, so that financial risk is contained without manual intervention\<br>- I want observability into balance calculations, so that I can debug discrepancies |



**User Story Structure:**

```

As a [USER ROLE],

I want [CAPABILITY],

so that [BUSINESS VALUE/OUTCOME].

```



**Common User Roles at Eco:**

- Product Manager

- Operations Lead

- Quant Analyst

- Engineer

- End User (solver users, liquidity providers, etc.)

- Executive/Leadership



---



### Rule #4: 1-2 Pages Maximum



**The Rule:**

PRDs must be **100-200 lines total** (roughly 1-2 pages).



**Why:**

\> "It's very heavy, long, not approachable to read"

\> — Engineering team feedback



Long PRDs don't get read. Engineers skim or skip them entirely. A 1391-line PRD wastes product effort and fails to communicate effectively.



**Target Breakdown:**

- **Part 1 (Product Requirements):** \~100 lines

  - Problem: 5-10 lines

  - Solution: 5-10 lines

  - Value/Impact: 5-10 lines

  - User Stories: 20-40 lines

  - Services Impacted: 5-10 lines

  - Hard Requirements: 10-15 lines

  - Success Criteria: 10-15 lines

- **Part 2 (Technical Plan - Blank):** \~50 lines

  - 6 section headers with brief prompts (engineers fill in)



**If Your PRD is Longer:**

You're going too deep technically. Delete architecture details, remove appendices, simplify explanations.



---



### Rule #5: Services Impacted List



**The Rule:**

Include explicit "Services Impacted" section listing all services this feature touches.



**Why:**

\> "One thing that I think is missing here which would be beneficial for engineers is a list of services that this feature would impact"

\> — Engineering team feedback



Engineers need to understand scope immediately. An explicit list helps with effort estimation, dependency analysis, and integration planning.



**Format:**

```markdown

### Services Impacted



- **Service Name** (what's impacted: integration points, data flows, etc.)

- **Another Service** (how it's affected)

- **NEW: New Service Name** (to be built - purpose)

```



**Example:**

```markdown

### Services Impacted



- **Solver** (fulfillment operations tracking, pending withdrawals tracking, circuit breaker integration)

- **Liquidity Manager** (rebalancing operations tracking, pending rebalances tracking)

- **Quote Service** (quote logging for analytics)

- **Portal** (potential future RPC consolidation opportunity)

- **NEW: Balance Tracker Service** (to be built - real-time balance monitoring)

- **NEW: Circuit Breaker Service** (to be built - automated solver shutdown)

```



**Key Points:**

- List ALL services touched (existing + new)

- Mark new services as "NEW:"

- Briefly explain impact (1 line per service)

- Helps engineers identify integration complexity



---



## Target Audiences



**Part 1: Product Requirements**

- **Product managers** reviewing product direction

- **Stakeholders** understanding business value

- **Engineers** learning what needs to be built and why



**Part 2: Technical Plan (Blank for Engineers to Fill)**

- **Engineers** proposing architecture

- **Technical leads** reviewing implementation approach

- **Product managers** understanding how it will be built (after engineers fill in)



---



# SECTION 2: Section-by-Section Guidance



This section provides detailed guidance for each section of the PRD with examples, common mistakes, and target lengths.



---



## Problem Section



### Purpose

Clearly articulate the problems being solved. Focus on pain points and their business impact.



### Format

- **3-5 concise bullets**

- Each bullet describes one specific problem

- Focus on business/user impact, not technical details

- **NO NUMBERS** (Rule #1)



### Target Length

**5-10 lines**



### Good Examples



✅ **Good - Clear problem statements without numbers:**

```markdown

### Problem



- Redundant RPC queries across services creating substantial infrastructure costs and reliability risks

- No single source of truth for balances creating financial risk and operational uncertainty

- Product and operations teams blocked on engineers for analytics queries, limiting autonomy and slowing decision-making

- Cannot scale liquidity deployment without automated balance monitoring and risk controls

```



Why this works:

- Clear business impact (costs, financial risk, team bottlenecks)

- Qualitative language (substantial, limiting, cannot)

- No specific numbers

- Concise and scannable



---



### Bad Examples



❌ **Bad - Too many numbers:**

```markdown

### Problem



- Services make 50,000+ redundant RPC queries daily costing $40,000/year

- Balance discrepancies have caused 3 incidents in past 6 months totaling $12K in losses

- Product team submits 15-20 analytics requests/week consuming 8 hours of engineering time

- Cannot scale beyond $5M liquidity without 10% deviation circuit breaker

```



Why this fails:

- Specific numbers everywhere (violates Rule #1)

- Numbers can't be validated upfront

- Numbers become stale quickly

- False precision undermines credibility



---



❌ **Bad - Too vague:**

```markdown

### Problem



- System has performance issues

- Data is inconsistent

- Teams need better tools

- Scalability is limited

```



Why this fails:

- Too generic (which performance issues? what data?)

- No business impact explained

- Could describe almost any feature

- Doesn't help engineers understand urgency



---



❌ **Bad - Too long and detailed:**

```markdown

### Problem



- Our current architecture requires each service (Solver, Liquidity Manager, Portal) to independently query multiple RPC providers (Alchemy, Infura, QuickNode) to retrieve on-chain balance data. This creates several cascading problems: First, we're seeing increased latency in our fulfillment operations because each balance check requires multiple network calls with retry logic. Second, we're incurring substantial costs from RPC providers who charge per-call, and our redundant architecture means we're paying for the same data multiple times. Third, when different services query at slightly different times, they can see inconsistent balance states, which has led to operational incidents. Fourth, we lack a unified view of historical balance data, making it difficult to perform root cause analysis when issues occur...

```



Why this fails:

- Way too long (should be 5-10 lines, not a paragraph)

- Too technical (mentions specific RPC providers, retry logic)

- Buries the key problems in detail

- Engineers won't read this



---



### Common Mistakes



1\. **Including specific numbers** - Use qualitative language instead

2\. **Being too vague** - Be specific about which problems and their impact

3\. **Writing paragraphs** - Use concise bullets

4\. **Technical architecture details** - Focus on business impact, not technical causes

5\. **Too many problems** - Limit to 3-5 key problems



---



### Writing Checklist



- [ ] 3-5 bullet points

- [ ] Each bullet describes clear business impact

- [ ] No specific numbers (percentages, dollar amounts, metrics)

- [ ] 5-10 lines total

- [ ] Readable in 30 seconds



---



## Solution Section



### Purpose

Describe WHAT you're building at a high level. Focus on capabilities, not implementation details.



### Format

- **3-5 concise bullets**

- Each bullet describes one key capability being built

- Describe WHAT exists, not HOW it's built

- **NO ARCHITECTURE DETAILS** (Rule #2)



### Target Length

**5-10 lines**



### Good Examples



✅ **Good - High-level capabilities without architecture:**

```markdown

### Solution



- Real-time balance monitoring service establishing single source of truth for all balance data

- Unified analytics database consolidating operational data from all services

- Self-service analytics interface (Metabase) empowering product and operations teams

- Automated circuit breaker service for balance deviation protection

```



Why this works:

- Describes end state (what exists)

- High-level capabilities, not technical details

- Clear value (single source of truth, self-service, automated protection)

- Engineers understand what needs to exist



---



### Bad Examples



❌ **Bad - Too much technical architecture:**

```markdown

### Solution



- Balance Tracker Service with Raft consensus mechanism polling 3 RPC providers (Alchemy, Infura, QuickNode) every 1-2 seconds with majority-vote validation

- PostgreSQL database with TimescaleDB extension for time-series optimization, materialized views refreshing every 5 minutes, read replicas for query scaling

- Metabase instance connected via read replica with pre-built dashboards for fulfillment history, balance monitoring, and profitability analysis

- Circuit Breaker Service using Redis pub/sub for real-time balance deviation events with \<100ms trigger latency and automatic solver shutdown via IPC

```



Why this fails:

- Defines technical architecture in detail (violates Rule #2)

- Specifies exact technologies (PostgreSQL, Redis, TimescaleDB)

- Includes performance targets (1-2 seconds, \<100ms, 5 minutes)

- Engineers will redesign this anyway

- Wasted product effort



---



❌ **Bad - Too vague:**

```markdown

### Solution



- Improve balance monitoring

- Build analytics capabilities

- Add safety controls

- Enhance data infrastructure

```



Why this fails:

- Too generic (improve how? build what capabilities?)

- Doesn't communicate what actually gets built

- Engineers can't estimate complexity or scope



---



❌ **Bad - Mixed WHAT and HOW:**

```markdown

### Solution



- Build a balance monitoring system that checks balances in real-time by polling RPCs, storing results in a database, and exposing an API for services to query

- Create an analytics platform by consolidating data from multiple services into a central warehouse, then connecting Metabase to it for visualization

- Implement safety controls by detecting when balances diverge from expected values and triggering an automated shutdown of the solver service

```



Why this fails:

- Mixes WHAT (monitoring system, analytics platform, safety controls) with HOW (polling RPCs, consolidating data, detecting divergence)

- Too implementation-focused

- Should describe end state, not process



---



### Common Mistakes



1\. **Specifying technologies** - Don't say "PostgreSQL with TimescaleDB" - say "Unified analytics database"

2\. **Defining architecture** - Don't say "polling RPCs every 2 seconds" - say "Real-time balance monitoring"

3\. **Performance numbers** - Don't say "\<100ms latency" - say "fast response times" or just omit performance (it's a constraint, not solution)

4\. **Being too vague** - Don't say "improve monitoring" - say "Real-time balance monitoring service establishing single source of truth"



---



### Writing Checklist



- [ ] 3-5 bullet points describing capabilities

- [ ] Describes WHAT exists, not HOW it's built

- [ ] No specific technologies or architectures

- [ ] No performance numbers

- [ ] 5-10 lines total

- [ ] Clear enough for engineers to understand scope



---



## Value/Impact Section



### Purpose

Articulate why this matters - business value, user impact, strategic importance.



### Format

- **3-5 bullets**

- Focus on business outcomes and user benefits

- Qualitative language describing impact

- Connect to strategic goals if relevant



### Target Length

**5-10 lines**



### Good Examples



✅ **Good - Business value without numbers:**

```markdown

### Value/Impact



- Significant reduction in RPC infrastructure costs through query consolidation

- Enables safe liquidity scaling with automated risk controls preventing financial loss

- Unblocks product and operations teams for autonomous analytics and faster decision-making

- Establishes foundation for operational observability and data-driven optimization

```



Why this works:

- Clear business value (cost reduction, risk mitigation, team empowerment)

- Qualitative language (significant, safe, faster, foundation)

- Strategic outcomes (scaling, observability)

- No false precision with numbers



---



### Bad Examples



❌ **Bad - Too many numbers:**

```markdown

### Value/Impact



- 50-60% reduction in RPC costs ($40K/year → $16-20K/year) with 12-month payback

- Enables scaling from $5M → $8M liquidity (60% increase) with 5% deviation threshold

- 90%+ self-service adoption reducing engineering analytics burden by 15 hours/week

- 3-5x improvement in decision velocity (3-day request cycle → same-day autonomous queries)

```



Why this fails:

- Numbers everywhere (violates Rule #1)

- False precision (50-60%, $40K, 90%+, 3-5x)

- Can't validate these claims upfront

- Numbers become stale and unverifiable



---



❌ **Bad - Too technical:**

```markdown

### Value/Impact



- Reduced RPC query volume through consolidated polling architecture

- Improved data consistency via single source of truth design pattern

- Lower latency dashboards enabled by materialized view optimization

- Enhanced system reliability through circuit breaker implementation

```



Why this fails:

- Too technical (polling architecture, design patterns, materialized views)

- Focuses on HOW rather than business value

- Doesn't communicate why stakeholders should care



---



❌ **Bad - Too vague:**

```markdown

### Value/Impact



- Cost savings

- Better risk management

- Improved team productivity

- Enhanced system capabilities

```



Why this fails:

- Too generic (what cost savings? how much better?)

- Could describe almost any feature

- No clear connection to business strategy



---



### Common Mistakes



1\. **Specific numbers and percentages** - Use qualitative language instead

2\. **Technical benefits** - Focus on business outcomes, not technical improvements

3\. **Being too vague** - Be specific about which business value

4\. **Missing strategic connection** - Connect to broader company goals if relevant



---



### Writing Checklist



- [ ] 3-5 bullet points describing business value

- [ ] Qualitative language (significant, substantial, enables, unblocks)

- [ ] No specific numbers or percentages

- [ ] Clear business outcomes

- [ ] 5-10 lines total



---



## User Stories Section



### Purpose

Describe requirements from user perspective organized by role. This is the heart of the PRD.



### Format

- **Grouped by user role** (not by component/service)

- **"As a [role], I want [capability], so that [business value]" structure**

- 3-5 user roles typical

- 2-4 stories per role



### Target Length

**20-40 lines**



### Structure Template

```markdown

### User Stories



#### As a [USER ROLE 1]

- I want [capability], so that [business value/outcome]

- I want [capability], so that [business value/outcome]

- I want [capability], so that [business value/outcome]



#### As a [USER ROLE 2]

- I want [capability], so that [business value/outcome]

- I want [capability], so that [business value/outcome]



#### As a [USER ROLE 3]

- I want [capability], so that [business value/outcome]

- I want [capability], so that [business value/outcome]

```



### Good Examples



✅ **Good - User story format grouped by role:**

```markdown

### User Stories



#### As a Product Manager

- I want to see fulfillment history and trends, so that I can analyze performance patterns and identify optimization opportunities

- I want to view profitability metrics across chains and assets, so that I can assess business health and prioritize investments

- I want to query operational data myself without engineering support, so that I can move faster and reduce dependency bottlenecks



#### As an Operations Lead

- I want real-time balance monitoring across all chains and assets, so that I can detect anomalies quickly and prevent issues

- I want automated alerts on balance deviations, so that I can respond to financial risks before they escalate

- I want to see rebalancing operations and effectiveness, so that I can optimize liquidity management strategies



#### As an Engineer

- I want consolidated balance tracking reducing duplicate RPC queries, so that services don't wastefully poll the same data

- I want automated circuit breakers protecting against balance discrepancies, so that financial risk is contained without manual intervention

- I want observability into balance calculations and data flow, so that I can debug discrepancies and validate correctness

```



Why this works:

- Organized by user role (Rule #3)

- Uses "As a... I want... so that..." format

- Clear business value in "so that" clause

- Covers multiple user types

- Focus on outcomes, not implementation



---



### Bad Examples



❌ **Bad - Organized by component instead of user:**

```markdown

### User Stories



#### Component 1: Balance Tracker Service

- Real-time balance monitoring across all chains

- RPC polling with consensus mechanism

- Balance deviation detection

- API for service integration



#### Component 2: Unified Analytics DB

- Fulfillment history storage

- Rebalancing operations tracking

- Quote data consolidation

- Fast query performance (\<500ms)

```



Why this fails:

- Organized by technical component (violates Rule #3)

- No "As a... I want... so that..." format

- No business value articulated

- Focuses on features, not user needs

- Can't tell who benefits from each capability



---



❌ **Bad - Missing "so that" business value:**

```markdown

### User Stories



#### As a Product Manager

- I want to see fulfillment history

- I want to view profitability metrics

- I want to query operational data



#### As an Operations Lead

- I want real-time balance monitoring

- I want automated alerts

- I want to see rebalancing operations

```



Why this fails:

- Missing "so that [business value]" clause

- Can't tell why these capabilities matter

- No connection to outcomes

- Incomplete user story format



---



❌ **Bad - Too technical/implementation-focused:**

```markdown

### User Stories



#### As an Engineer

- I want a PostgreSQL database with TimescaleDB extension, so that I can store time-series balance data efficiently

- I want Metabase connected via read replica, so that analytics queries don't impact production database

- I want Redis pub/sub for circuit breaker events, so that I can trigger solver shutdown with \<100ms latency

```



Why this fails:

- Too technical (specific technologies)

- Engineers wouldn't write user stories about database technology

- These are implementation details, not user needs

- Violates Rule #2 (requirements not architecture)



---



### Common Mistakes



1\. **Organizing by component** - Organize by user role instead

2\. **Missing "so that" clause** - Always include business value

3\. **Too technical** - Focus on user needs, not implementation

4\. **Too few roles** - Cover all relevant user types

5\. **Too many stories per role** - 2-4 stories per role is typical



---



### Writing Checklist



- [ ] Organized by user role (not component)

- [ ] Uses "As a [role], I want [capability], so that [business value]" format

- [ ] Covers 3-5 relevant user roles

- [ ] 2-4 stories per role

- [ ] Business value clear in "so that" clause

- [ ] 20-40 lines total



---



## Services Impacted Section



### Purpose

Explicit list of all services this feature touches. Helps engineers understand scope immediately.



### Format

- **Bulleted list**

- One line per service

- Briefly explain impact

- Mark new services as "NEW:"



### Target Length

**5-10 lines**



### Good Examples



✅ **Good - Explicit list with brief impact explanation:**

```markdown

### Services Impacted



- **Solver** (fulfillment operations tracking, pending withdrawals tracking, circuit breaker integration for automated protection)

- **Liquidity Manager** (rebalancing operations tracking, pending rebalances tracking, balance monitoring integration)

- **Quote Service** (quote logging for analytics, slippage calculation integration)

- **Portal** (potential future RPC consolidation opportunity, currently separate but could leverage balance service)

- **NEW: Balance Tracker Service** (to be built - real-time balance monitoring establishing single source of truth)

- **NEW: Circuit Breaker Service** (to be built - automated solver shutdown on balance deviation detection)

- **NEW: Analytics Database** (to be built - unified operational data repository)

- **NEW: Metabase Instance** (to be deployed - self-service analytics interface)

```



Why this works:

- Clear list of ALL impacted services

- Explains how each service is affected

- Marks new services explicitly

- Engineers can quickly estimate integration complexity



---



### Bad Examples



❌ **Bad - Missing services or too vague:**

```markdown

### Services Impacted



- Solver

- Liquidity Manager

- Some new services to be built

```



Why this fails:

- Too vague (how is solver impacted?)

- Doesn't list all services

- "Some new services" not specific

- Doesn't help engineers understand scope



---



❌ **Bad - Too much implementation detail:**

```markdown

### Services Impacted



- **Solver** will need to integrate Balance Tracker API by adding a new BalanceClient class with getBalance(chain, asset) method called before each fulfillment operation, with fallback logic to existing RPC polling for backwards compatibility during rollout

- **Liquidity Manager** requires refactoring of RebalancingEngine to emit events to Analytics DB via new EventBus interface, replacing current in-memory tracking with persistent storage layer

```



Why this fails:

- Way too detailed for a PRD

- Defines implementation approach (violates Rule #2)

- Should just list services and brief impact

- This level of detail belongs in engineering spec



---



### Common Mistakes



1\. **Missing services** - List ALL services touched

2\. **Too vague** - Briefly explain impact

3\. **Too detailed** - Don't define implementation approach

4\. **Not marking new services** - Use "NEW:" prefix

5\. **Forgetting infrastructure** - Include databases, deployments, etc.



---



### Writing Checklist



- [ ] Lists ALL impacted services

- [ ] Brief explanation of impact (1 line per service)

- [ ] New services marked with "NEW:"

- [ ] 5-10 lines total

- [ ] Helps engineers understand scope



---



## Hard Requirements & Constraints Section



### Purpose

Define non-negotiable constraints that engineers must satisfy. These are requirements, not implementation details.



### Format

- **Bulleted list of constraints**

- Use qualitative language for performance (Rule #1)

- Focus on business/technical constraints, not implementation

- Each constraint should be verifiable



### Target Length

**10-15 lines**



### Good Examples



✅ **Good - Clear constraints without specific numbers:**

```markdown

### Hard Requirements & Constraints



- **Must not add latency to solver execution:** Solver has strict performance requirements for competitive fulfillment; balance tracking must be asynchronous and non-blocking

- **Must provide single source of truth for balances:** All services query Balance Tracker (not individual RPCs) to ensure consistency and reduce financial risk

- **Must support self-service analytics for product/ops teams:** Metabase interface enabling queries without engineering dependency

- **Must enable automated circuit breaker:** System must detect balance deviations and automatically pause solver to prevent financial loss

- **Must consolidate RPC usage where possible:** Services should leverage Balance Tracker instead of redundant RPC queries

- **Must handle RPC failures gracefully:** Balance tracking must continue functioning when individual RPC providers have issues (consensus mechanism required)

- **Must provide audit trail:** All balance updates, fulfillments, and rebalances must be recorded for financial reconciliation

```



Why this works:

- Clear constraints engineers must satisfy

- Qualitative performance language (must not add latency, fast, gracefully)

- Explains WHY each constraint matters (strict performance requirements, financial risk, etc.)

- Verifiable (can test if latency added, if single source of truth exists)

- No specific numbers (Rule #1)



---



### Bad Examples



❌ **Bad - Specific numbers everywhere:**

```markdown

### Hard Requirements & Constraints



- **Must not add >1 second latency to solver:** Solver execution must remain under 3 seconds total

- **Balance freshness \<2 seconds:** Balance data must be updated every 1-2 seconds via RPC polling

- **Query performance \<500ms:** Analytics dashboard queries must return results in under 500ms (p95)

- **Circuit breaker trigger \<100ms:** Balance deviation detection must trigger solver shutdown within 100ms

- **99.9% uptime SLA:** System must maintain 99.9% availability (max 43 minutes downtime/month)

- **$20K/year maximum cost:** RPC costs must not exceed $20,000 annually

```



Why this fails:

- Numbers everywhere (violates Rule #1)

- False precision (>1 second, \<500ms, \<100ms, 99.9%, $20K)

- Can't validate these numbers upfront

- Engineers will determine actual performance targets based on testing



---



❌ **Bad - Implementation details instead of constraints:**

```markdown

### Hard Requirements & Constraints



- **Use PostgreSQL with TimescaleDB extension:** Analytics database must be PostgreSQL with TimescaleDB for time-series optimization

- **Poll 3 RPC providers minimum:** Balance Tracker must poll at least 3 RPC providers (Alchemy, Infura, QuickNode) with majority-vote consensus

- **Deploy Metabase on separate instance:** Metabase must run on dedicated EC2 instance to avoid impacting production

- **Use Redis for circuit breaker pub/sub:** Circuit Breaker must use Redis pub/sub for real-time event distribution

```



Why this fails:

- Specifies technologies (PostgreSQL, TimescaleDB, Redis, EC2)

- Defines architecture (polling, pub/sub, majority-vote)

- These are implementation decisions, not constraints (violates Rule #2)

- Engineers should choose technologies based on requirements



---



### Common Mistakes



1\. **Specific performance numbers** - Use qualitative language (fast, low, minimal)

2\. **Specifying technologies** - State requirements, let engineers choose tech

3\. **Implementation details** - Focus on constraints, not how to satisfy them

4\. **Unverifiable constraints** - Each constraint should be testable

5\. **Missing "why"** - Explain why each constraint matters



---



### Writing Checklist



- [ ] Lists all non-negotiable constraints

- [ ] Qualitative language for performance (no specific numbers)

- [ ] No technology specifications

- [ ] No implementation details

- [ ] Explains why each constraint matters

- [ ] Each constraint is verifiable

- [ ] 10-15 lines total



---



## Success Criteria Section



### Purpose

Define how to measure success post-launch. Focus on measurement methodology, not specific targets.



### Format

- **Qualitative success statements**

- **"Measure:" methodology for quantification**

- Focus on how to measure, not specific targets

- Suggested baseline/comparison periods



### Target Length

**10-15 lines**



### Good Examples



✅ **Good - Measurement methodology without specific targets:**

```markdown

### Success Criteria



**High self-service adoption for analytics queries**

- Measure: Track reduction in engineering analytics requests (Slack, tickets) comparing pre-launch vs post-launch periods

- Measure: Monitor Metabase query volume and unique users over time



**Low RPC cost trajectory**

- Measure: Track monthly RPC invoice totals and per-service query volumes comparing baseline (pre-launch) vs post-launch trends

- Target: Substantial reduction in redundant queries (quantify after establishing baseline)



**Zero balance discrepancy incidents**

- Measure: Track incidents where reported balance diverges from actual on-chain balance

- Measure: Monitor circuit breaker activations and false positive rate



**Liquidity scaling enabled**

- Measure: Confirm circuit breaker operational and integrated with solver before increasing liquidity deployment

- Measure: Track maximum safe liquidity deployment over time (circuit breaker provides safety net)



**Fast query performance for analytics dashboards**

- Measure: Track Metabase query response times (p50, p95, p99 latencies)

- Measure: User feedback on analytics interface responsiveness and usability

```



Why this works:

- Qualitative success statements (high, low, zero, fast)

- Clear measurement methodology ("Track X comparing Y vs Z")

- Honest about when targets will be known ("quantify after baseline")

- Focus on HOW to measure, not specific numbers

- Multiple measurement approaches per success criterion



---



### Bad Examples



❌ **Bad - Specific targets without measurement:**

```markdown

### Success Criteria



- 90% self-service adoption within 3 months

- 50% reduction in RPC costs from $40K/year to $20K/year

- Zero balance discrepancy incidents in first 6 months

- Liquidity scaled from $5M to $8M (60% increase)

- \<500ms query latency (p95) for all Metabase dashboards

```



Why this fails:

- Specific numbers everywhere (90%, 50%, $40K, \<500ms)

- No measurement methodology

- Arbitrary targets (why 90%? why 3 months?)

- Can't validate these upfront

- Violates Rule #1



---



❌ **Bad - Too vague without measurement approach:**

```markdown

### Success Criteria



- High adoption of self-service analytics

- Reduced costs

- Improved balance accuracy

- Increased liquidity capacity

- Good performance

```



Why this fails:

- Too vague (how do you measure "high" or "good"?)

- No measurement methodology

- No comparison baseline

- Can't verify success objectively



---



### Common Mistakes



1\. **Specific numeric targets** - Use qualitative + measurement methodology

2\. **Arbitrary deadlines** - Focus on measurement, not "within 3 months"

3\. **No measurement approach** - Always include "Measure:" methodology

4\. **Unverifiable criteria** - Each criterion must have clear measurement

5\. **Missing baseline** - Specify comparison period (pre vs post launch)



---



### Writing Checklist



- [ ] Qualitative success statements

- [ ] "Measure:" methodology for each criterion

- [ ] No specific numeric targets

- [ ] No arbitrary deadlines

- [ ] Comparison baseline specified (pre vs post)

- [ ] 10-15 lines total

- [ ] Verifiable and actionable



---



## Part 2: Technical Plan (Blank Sections for Engineers)



### Purpose

Provide structure for engineers to fill in with their technical proposal.



### Format

- **6 standard sections with prompts**

- Brief 2-3 sentence description of what engineers should fill in

- Considerations/questions to guide engineering thinking

- Each section is BLANK for engineers to complete



### Target Length

**\~50 lines of section headers and prompts**



### Structure



```markdown

## PART 2: TECHNICAL PLAN (Engineers to Fill In)



### 1. Proposed Architecture



**[BLANK - Engineers to propose technical design]**



Considerations for engineering team:

- [3-5 guiding questions about architecture decisions]



### 2. Implementation Approach



**[BLANK - Engineers to propose build strategy]**



Considerations for engineering team:

- [3-5 guiding questions about implementation]



### 3. Data Schema



**[BLANK - Engineers to define data structures]**



Considerations for engineering team:

- [3-5 guiding questions about data modeling]



### 4. Performance Strategy



**[BLANK - Engineers to explain how to meet performance constraints]**



Considerations for engineering team:

- [3-5 guiding questions about performance]



### 5. Risks & Mitigations



**[BLANK - Engineers to identify risks and mitigations]**



Considerations for engineering team:

- [3-5 guiding questions about risks]



### 6. Implementation Phases



**[BLANK - Engineers to break into phases with dependencies]**



Considerations for engineering team:

- [3-5 guiding questions about phasing]

```



---



### Good Examples



✅ **Good - Clear section headers with guiding questions:**

```markdown

### 1. Proposed Architecture



**[BLANK - Engineers to propose technical design]**



Considerations for engineering team:

- How should Balance Tracker poll RPCs? (polling frequency, consensus mechanism, RPC provider selection)

- How should services integrate with Balance Tracker? (API design, push vs pull model)

- How should Circuit Breaker receive balance data and trigger solver shutdown? (event-driven vs polling)

- How should Metabase connect to Analytics Database? (direct DB access vs API layer)

- What database technology for Analytics Database? (PostgreSQL, TimescaleDB, etc.)

```



Why this works:

- Clear section purpose

- Blank for engineers to fill in

- Guiding questions help engineers think through decisions

- Questions don't dictate answers

- Helps engineers structure their proposal



---



### Bad Examples



❌ **Bad - Product pre-fills with architecture:**

```markdown

### 1. Proposed Architecture



**Balance Tracker Service:**

- Poll 3 RPC providers (Alchemy, Infura, QuickNode) every 1-2 seconds

- Use Raft consensus for majority-vote validation

- Expose REST API for service integration

- PostgreSQL with TimescaleDB extension for Analytics Database

- Metabase connected via read replica

```



Why this fails:

- Product is defining architecture (violates Rule #2)

- Not blank for engineers to fill in

- Specifies exact technologies and approaches

- Engineers will redesign this anyway

- Defeats purpose of collaborative model



---



❌ **Bad - Too vague, no guidance:**

```markdown

### 1. Proposed Architecture



**[BLANK - Engineers to propose technical design]**

```



Why this fails:

- No guiding questions

- Engineers don't know what product wants to understand

- Missed opportunity to highlight important trade-offs

- Should include 3-5 considerations to guide thinking



---



### Common Mistakes



1\. **Pre-filling with architecture** - Keep it BLANK for engineers

2\. **No guiding questions** - Include 3-5 considerations per section

3\. **Prescriptive questions** - Ask open-ended questions, don't dictate answers

4\. **Too many sections** - 6 sections is standard (architecture, implementation, schema, performance, risks, phases)

5\. **Missing sections** - Include all 6 standard sections



---



### Writing Checklist



- [ ] 6 standard sections included

- [ ] Each section is BLANK (not pre-filled)

- [ ] 3-5 guiding questions/considerations per section

- [ ] Questions are open-ended, not prescriptive

- [ ] \~50 lines total for Part 2



---



# SECTION 3: Blank Template (Ready to Use)



**Copy this template to start a new PRD. Replace bracketed placeholders with your content.**



```markdown

# [Feature Name] PRD



**Status:** Draft

**Last Updated:** [Date]



---



## PART 1: PRODUCT REQUIREMENTS



### Problem



- [Problem statement 1 - describe specific pain point and business impact, NO NUMBERS]

- [Problem statement 2]

- [Problem statement 3]

- [Optional: Problem statement 4-5 if needed]



### Solution



- [Solution capability 1 - describe WHAT exists, not HOW it's built, NO ARCHITECTURE]

- [Solution capability 2]

- [Solution capability 3]

- [Optional: Solution capability 4-5 if needed]



### Value/Impact



- [Business value 1 - describe qualitative impact, NO SPECIFIC NUMBERS]

- [Business value 2]

- [Business value 3]

- [Optional: Business value 4-5 if needed]



### User Stories



#### As a [User Role 1]

- I want [capability], so that [business value/outcome]

- I want [capability], so that [business value/outcome]

- I want [capability], so that [business value/outcome]



#### As a [User Role 2]

- I want [capability], so that [business value/outcome]

- I want [capability], so that [business value/outcome]

- I want [capability], so that [business value/outcome]



#### As a [User Role 3]

- I want [capability], so that [business value/outcome]

- I want [capability], so that [business value/outcome]



#### As a [User Role 4]

- I want [capability], so that [business value/outcome]

- I want [capability], so that [business value/outcome]



### Services Impacted



- **[Service Name 1]** ([brief explanation of impact - integration points, data flows, etc.])

- **[Service Name 2]** ([brief explanation])

- **[Service Name 3]** ([brief explanation])

- **NEW: [New Service Name]** (to be built - [purpose])

- **NEW: [Another New Service]** (to be built - [purpose])



### Hard Requirements & Constraints



- **[Constraint 1]:** [Explain requirement and why it matters - use qualitative language for performance]

- **[Constraint 2]:** [Explain requirement and why it matters]

- **[Constraint 3]:** [Explain requirement and why it matters]

- **[Constraint 4]:** [Explain requirement and why it matters]

- **[Constraint 5]:** [Explain requirement and why it matters]



### Success Criteria



**[Success criterion 1 - qualitative statement]**

- Measure: [How to measure this - comparison methodology, baseline period]

- Measure: [Alternative/additional measurement approach]



**[Success criterion 2 - qualitative statement]**

- Measure: [How to measure this]

- Measure: [Alternative/additional measurement approach]



**[Success criterion 3 - qualitative statement]**

- Measure: [How to measure this]



**[Success criterion 4 - qualitative statement]**

- Measure: [How to measure this]



---



## PART 2: TECHNICAL PLAN (Engineers to Fill In)



### 1. Proposed Architecture



**[BLANK - Engineers to propose technical design]**



Considerations for engineering team:

- [Guiding question about architecture decision 1]

- [Guiding question about architecture decision 2]

- [Guiding question about architecture decision 3]

- [Guiding question about architecture decision 4]



### 2. Implementation Approach



**[BLANK - Engineers to propose build strategy]**



Considerations for engineering team:

- [Guiding question about implementation approach 1]

- [Guiding question about implementation approach 2]

- [Guiding question about implementation approach 3]

- [Guiding question about implementation approach 4]



### 3. Data Schema



**[BLANK - Engineers to define data structures]**



Considerations for engineering team:

- [Guiding question about data modeling 1]

- [Guiding question about data modeling 2]

- [Guiding question about data modeling 3]



### 4. Performance Strategy



**[BLANK - Engineers to explain how to meet performance constraints]**



Considerations for engineering team:

- [Guiding question about performance 1]

- [Guiding question about performance 2]

- [Guiding question about performance 3]



### 5. Risks & Mitigations



**[BLANK - Engineers to identify risks and mitigations]**



Considerations for engineering team:

- [Guiding question about risk category 1]

- [Guiding question about risk category 2]

- [Guiding question about risk category 3]

- [Guiding question about risk category 4]



### 6. Implementation Phases



**[BLANK - Engineers to break into phases with dependencies]**



Considerations for engineering team:

- [Guiding question about critical path]

- [Guiding question about phase dependencies]

- [Guiding question about milestones]

- [Guiding question about scope/MVP]



---



## Appendix: Context for Engineers



[Optional: Add any additional context that helps engineers understand background, related decisions, or important constraints they should know about]

```



---



# SECTION 4: Anti-Patterns to Avoid



Learn from these common mistakes that waste time and reduce PRD quality.



---



## Anti-Pattern #1: Specific Numbers Everywhere



### What It Looks Like

```markdown

### Problem

- Services make 50,000+ redundant RPC queries daily costing $40,000/year

- Balance discrepancies caused 3 incidents in 6 months totaling $12K losses

- Product team submits 15-20 analytics requests/week consuming 8 hours of engineering time

- Cannot scale beyond $5M liquidity without 10% deviation circuit breaker



### Success Criteria

- 90% self-service adoption within 3 months

- 50% reduction in RPC costs from $40K/year to $20K/year

- \<500ms query latency (p95) for all dashboards

- Zero incidents in first 6 months

```



### Why It's Wrong

\> "THIS IS CRITICAL NEVER PUT NUMBERS BECAUSE THERE IS NO WAY TO VALIDATE"



- Can't verify $40K/year or 50,000 queries upfront

- Numbers become stale quickly (what if usage grows?)

- False precision undermines credibility

- Creates unrealistic expectations



### How to Fix It

Use qualitative language + measurement methodology:

```markdown

### Problem

- Redundant RPC queries creating substantial infrastructure costs

- Balance discrepancies creating financial risk and operational incidents

- Product team blocked on engineers for analytics, limiting autonomy

- Cannot scale liquidity without automated balance monitoring



### Success Criteria

**High self-service adoption**

- Measure: Track reduction in engineering analytics requests comparing pre/post launch

- Measure: Monitor Metabase query volume and unique users



**Low RPC cost trajectory**

- Measure: Track monthly RPC invoices comparing baseline vs post-launch

- Target: Substantial reduction (quantify after baseline)

```



---



## Anti-Pattern #2: Product Defines Architecture



### What It Looks Like

```markdown

### Solution

- Balance Tracker Service with Raft consensus polling 3 RPC providers (Alchemy, Infura, QuickNode) every 1-2 seconds

- PostgreSQL with TimescaleDB extension and materialized views refreshing every 5 minutes

- Metabase connected via read replica with pre-built dashboards

- Circuit Breaker using Redis pub/sub with \<100ms trigger latency

```



### Why It's Wrong

\> "Product should just come in with product requirements... engineers will propose their own architecture"



- Engineers will redesign architecture based on technical constraints

- Wasted product effort defining implementation

- Specifies exact technologies engineers may not choose

- Too implementation-focused for a PRD



### How to Fix It

Describe WHAT exists, not HOW:

```markdown

### Solution

- Real-time balance monitoring establishing single source of truth

- Unified analytics database consolidating operational data

- Self-service analytics interface empowering product/ops teams

- Automated circuit breaker for balance deviation protection

```



Engineers fill in Part 2 with their proposed architecture.



---



## Anti-Pattern #3: Component-Based Organization



### What It Looks Like

```markdown

### Requirements



#### Component 1: Balance Tracker Service

- Purpose: Real-time balance monitoring

- Features: RPC polling, consensus, deviation detection

- Acceptance criteria: \<1s data freshness, 99.9% uptime



#### Component 2: Unified Analytics DB

- Purpose: Operational data consolidation

- Features: Fulfillment logs, rebalancing history, quote storage

- Acceptance criteria: \<500ms query latency, 1TB storage capacity



#### Component 3: Circuit Breaker

- Purpose: Automated solver shutdown

- Features: Balance deviation detection, solver pause API

- Acceptance criteria: \<100ms trigger time, \<1% false positives

```



### Why It's Wrong

\> "Try to scope them in terms of user stories not per service"



- Organized by technical component, not user needs

- Can't tell who benefits from each component

- Doesn't prioritize by user value

- Hard to understand what users can actually do



### How to Fix It

Use user story format grouped by role:

```markdown

### User Stories



#### As an Operations Lead

- I want real-time balance monitoring, so that I can detect anomalies quickly

- I want automated alerts on deviations, so that I can prevent financial loss



#### As a Product Manager

- I want to see fulfillment history, so that I can analyze performance trends

- I want to query data myself, so that I don't depend on engineers



#### As an Engineer

- I want automated circuit breakers, so that financial risk is contained

- I want observability into balance calculations, so that I can debug issues

```



---



## Anti-Pattern #4: PRD Over 200 Lines



### What It Looks Like

- 1391-line PRD with detailed mermaid diagrams

- Multiple appendices with meeting notes

- Detailed component architecture sections

- Line-by-line acceptance criteria per service

- Extensive technical implementation details



### Why It's Wrong

\> "It's very heavy, long, not approachable to read"



- Engineers don't read 1391-line PRDs

- Product wastes time writing content that gets skipped

- Buried key requirements in excessive detail

- Violates 1-2 page guideline



### How to Fix It

- Target: **100-200 lines total**

- Delete: Architecture details, appendices, diagrams, meeting notes

- Keep: Problem, solution, user stories, requirements, success criteria

- Part 2: Blank sections (\~50 lines) for engineers to fill in



---



## Anti-Pattern #5: Stakeholder Ownership Tables



### What It Looks Like

```markdown

### Stakeholder Ownership



| Component | Owner | Backup | Reviewer |

|-----------|-------|--------|----------|

| Balance Tracker | Alice | Bob | Carol |

| Analytics DB | Bob | Carol | Alice |

| Circuit Breaker | Carol | Alice | Bob |

```



### Why It's Wrong

\> "This doesn't need to be here. Nothing to do with the product, this is more of a development component"



- Ownership is engineering concern, not product

- Constrains developers to specific assignments

- Becomes stale as team composition changes

- Not relevant to PRD readers



### How to Fix It

Delete it entirely from PRD. Engineering team handles ownership assignment separately.



---



## Anti-Pattern #6: Missing Services Impacted



### What It Looks Like

PRD doesn't include explicit list of impacted services. Services mentioned scattered throughout document.



### Why It's Wrong

\> "One thing that I think is missing here which would be beneficial for engineers is a list of services that this feature would impact"



- Engineers can't quickly assess integration scope

- Missed services discovered late in implementation

- No clear view of cross-service dependencies



### How to Fix It

Add explicit "Services Impacted" section:

```markdown

### Services Impacted



- **Solver** (fulfillment tracking, circuit breaker integration)

- **Liquidity Manager** (rebalancing tracking, balance monitoring)

- **Quote Service** (quote logging for analytics)

- **NEW: Balance Tracker** (to be built - balance monitoring)

- **NEW: Circuit Breaker** (to be built - automated shutdown)

```



---



## Anti-Pattern #7: Vague Success Criteria



### What It Looks Like

```markdown

### Success Criteria

- High adoption

- Reduced costs

- Better performance

- Improved accuracy

```



### Why It's Wrong

- Too vague (how do you measure "high" or "better"?)

- No measurement methodology

- Can't verify success objectively

- Doesn't help post-launch assessment



### How to Fix It

Define measurement methodology:

```markdown

### Success Criteria



**High self-service adoption**

- Measure: Track reduction in engineering analytics requests (pre vs post)

- Measure: Monitor Metabase query volume and unique users



**Low RPC cost trajectory**

- Measure: Track monthly RPC invoices comparing baseline vs post-launch

- Target: Substantial reduction (quantify after baseline established)

```



---



## Anti-Pattern #8: Technical Jargon in Problem Statement



### What It Looks Like

```markdown

### Problem

- Current microservice architecture lacks centralized observability layer for cross-service balance reconciliation

- Distributed RPC polling with no consensus mechanism creates data consistency issues

- Absence of event-driven analytics ingestion pipeline limits real-time metric computation

```



### Why It's Wrong

- Too technical for stakeholders

- Focuses on technical causes, not business impact

- Uses jargon (microservice architecture, consensus mechanism, ingestion pipeline)

- Doesn't communicate why this matters



### How to Fix It

Focus on business impact in plain language:

```markdown

### Problem

- Redundant RPC queries creating substantial infrastructure costs

- No single source of truth for balances creating financial risk

- Product and ops teams blocked on engineers for analytics

- Cannot scale liquidity without automated monitoring

```



---



## Anti-Pattern #9: Hedging Language



### What It Looks Like

```markdown

### Value/Impact

- Could potentially reduce RPC costs by a substantial amount

- Might enable safer liquidity scaling if circuit breaker works well

- Should improve team autonomy assuming Metabase adoption is good

```



### Why It's Wrong

- Hedging undermines confidence

- "Could", "might", "should", "potentially" sound uncertain

- Reduces clarity and impact

- Makes value proposition weak



### How to Fix It

Use direct, confident language:

```markdown

### Value/Impact

- Significant reduction in RPC infrastructure costs

- Enables safe liquidity scaling with automated risk controls

- Unblocks product and operations teams for autonomous analytics

```



---



## Anti-Pattern #10: Defensive Tone



### What It Looks Like

```markdown

### Solution

Note: This is just a preliminary proposal and we're open to completely different approaches. The architecture suggested here is based on limited information and may not be optimal. Engineers should feel free to redesign as they see fit. We're not attached to any of these ideas.

```



### Why It's Wrong

- Undermines confidence in proposal

- Wastes space with disclaimers

- Collaborative model already empowers engineers to propose architecture

- Defensive tone suggests lack of conviction



### How to Fix It

Be direct and confident:

```markdown

### Solution

- Real-time balance monitoring establishing single source of truth

- Unified analytics database consolidating operational data

- Self-service analytics interface empowering teams

- Automated circuit breaker for financial protection

```



Part 2 structure inherently empowers engineers to propose architecture.



---



## Summary: Top 10 Anti-Patterns



| # | Anti-Pattern | Fix |

|---|-------------|-----|

| 1 | Specific numbers everywhere | Use qualitative language + measurement methodology |

| 2 | Product defines architecture | Describe WHAT exists, let engineers define HOW |

| 3 | Component-based organization | Use user story format grouped by role |

| 4 | PRD over 200 lines | Target 100-200 lines; delete architecture/appendices |

| 5 | Stakeholder ownership tables | Delete entirely (engineering concern, not product) |

| 6 | Missing Services Impacted | Add explicit list of all touched services |

| 7 | Vague success criteria | Define measurement methodology, not just outcomes |

| 8 | Technical jargon in problem | Focus on business impact in plain language |

| 9 | Hedging language | Use direct, confident language |

| 10 | Defensive tone | Be confident; collaborative model empowers engineers |



---



# SECTION 5: Real-World Example: Analytics PRD



This section shows the before/after transformation of Eco's Analytics PRD based on engineering feedback.



---



## Before: Original Analytics PRD



**Problems:**

- **1391 lines** (way too long)

- **40+ specific numbers** (violates NO NUMBERS rule)

- **Component-based organization** (not user stories)

- **Product-defined architecture** (Raft consensus, polling intervals, DB schemas)

- **Stakeholder ownership table** (engineering concern, not product)

- **Missing Services Impacted** (no explicit list)



**Example Excerpt:**

```markdown

## Component 2: Balance Tracker Service



### Purpose

Real-time balance monitoring across all chains and assets, establishing single source of truth.



### Technical Architecture

- Poll 3 RPC providers (Alchemy, Infura, QuickNode) every 1-2 seconds

- Raft consensus mechanism for majority-vote balance validation

- Detect deviations >10% and trigger circuit breaker within 100ms

- Store historical balance data in PostgreSQL with TimescaleDB extension

- Expose REST API for service integration



### Cost Model

Current RPC costs: $40,000/year across all services

Projected RPC costs: $18,000-$20,000/year (50-55% reduction)

Payback period: 18-24 months based on development costs



### Performance Requirements

- \<1s data freshness for balance updates

- \<500ms query latency for analytics (p95)

- 99.9% uptime SLA

- \<100ms circuit breaker trigger time



### Acceptance Criteria

- Balance Tracker polls RPCs with 1-2 second frequency

- Raft consensus correctly handles 1 RPC provider failure

- Circuit breaker triggers within 100ms of deviation detection

- False positive rate \<1% for circuit breaker

- Historical balance data retained for 365 days minimum

```



**Total Length:** 1391 lines with mermaid diagrams, appendices, meeting notes



---



## After: Revised Analytics PRD



**Improvements:**

- **187 lines** (within 100-200 target)

- **Zero specific numbers** (all qualitative language)

- **User story format** (grouped by role)

- **Requirements only** (no architecture)

- **Services Impacted section** (explicit list)

- **Part 2 blank for engineers** (collaborative fill-in-blank)



**Example Excerpt:**

```markdown

## PART 1: PRODUCT REQUIREMENTS



### Problem



- Redundant RPC queries across services creating substantial infrastructure costs and reliability risks

- No single source of truth for balances creating financial risk and operational uncertainty

- Product and operations teams blocked on engineers for analytics queries, limiting autonomy

- Cannot scale liquidity deployment without automated balance monitoring and risk controls



### Solution



- Real-time balance monitoring service establishing single source of truth for all balance data

- Unified analytics database consolidating operational data from all services

- Self-service analytics interface (Metabase) empowering product and operations teams

- Automated circuit breaker service for balance deviation protection



### User Stories



#### As an Operations Lead

- I want real-time balance monitoring across all chains and assets, so that I can detect anomalies quickly and prevent issues

- I want automated alerts on balance deviations, so that I can respond to financial risks before they escalate

- I want to see rebalancing operations and effectiveness, so that I can optimize liquidity management strategies



#### As a Product Manager

- I want to see fulfillment history and trends, so that I can analyze performance patterns and identify optimization opportunities

- I want to query operational data myself without engineering support, so that I can move faster and reduce dependency bottlenecks



### Services Impacted



- **Solver** (fulfillment operations tracking, pending withdrawals tracking, circuit breaker integration for automated protection)

- **Liquidity Manager** (rebalancing operations tracking, pending rebalances tracking, balance monitoring integration)

- **Quote Service** (quote logging for analytics, slippage calculation integration)

- **NEW: Balance Tracker Service** (to be built - real-time balance monitoring establishing single source of truth)

- **NEW: Circuit Breaker Service** (to be built - automated solver shutdown on balance deviation detection)



### Hard Requirements & Constraints



- **Must not add latency to solver execution:** Solver has strict performance requirements for competitive fulfillment; balance tracking must be asynchronous and non-blocking

- **Must provide single source of truth for balances:** All services query Balance Tracker (not individual RPCs) to ensure consistency and reduce financial risk

- **Must handle RPC failures gracefully:** Balance tracking must continue functioning when individual RPC providers have issues (consensus mechanism required)



### Success Criteria



**High self-service adoption for analytics queries**

- Measure: Track reduction in engineering analytics requests (Slack, tickets) comparing pre-launch vs post-launch periods

- Measure: Monitor Metabase query volume and unique users over time



**Low RPC cost trajectory**

- Measure: Track monthly RPC invoice totals and per-service query volumes comparing baseline (pre-launch) vs post-launch trends

- Target: Substantial reduction in redundant queries (quantify after establishing baseline)



## PART 2: TECHNICAL PLAN (Engineers to Fill In)



### 1. Proposed Architecture



**[BLANK - Engineers to propose technical design]**



Considerations for engineering team:

- How should Balance Tracker poll RPCs? (polling frequency, consensus mechanism, RPC provider selection)

- How should services integrate with Balance Tracker? (API design, push vs pull model)

- How should Circuit Breaker receive balance data and trigger solver shutdown? (event-driven vs polling)

- What database technology for Analytics Database? (PostgreSQL, TimescaleDB, etc.)

```



**Total Length:** 187 lines



---



## Key Transformations



| Aspect | Before (1391 lines) | After (187 lines) |

|--------|---------------------|-------------------|

| **Length** | 1391 lines | 187 lines (87% reduction) |

| **Numbers** | 40+ specific numbers ($40K, 90%, \<500ms, etc.) | Zero numbers (qualitative language) |

| **Organization** | Component-based (Component 1, 2, 3, 4) | User story format (by role) |

| **Architecture** | Product-defined (Raft consensus, polling intervals, DB tech) | Requirements only (engineers fill Part 2) |

| **Services** | Scattered mentions | Explicit "Services Impacted" section |

| **Success Criteria** | Specific targets (90%, $20K, \<500ms) | Measurement methodology |



---



## Number Replacements



| Before (Specific Numbers) | After (Qualitative + Measurement) |

|---------------------------|-----------------------------------|

| "$40,000/year RPC costs" | "Substantial RPC infrastructure costs" + Measure: "Track monthly RPC invoice totals" |

| "90% self-service adoption" | "High self-service adoption" + Measure: "Track reduction in engineering requests" |

| "\<3 seconds execution time" | "Must not add latency to solver" |

| "50%+ cost reduction" | "Significant cost reduction" + "Quantify after baseline" |

| "\<500ms query performance" | "Fast query performance" + Measure: "Track query response times (p50, p95, p99)" |

| "1-2 second polling interval" | "Frequent polling interval" (engineers propose exact frequency) |



---



## Engineering Feedback Integration



**Feedback:** "It's very heavy, long, not approachable to read"

- **Fix:** 1391 lines → 187 lines (87% reduction)



**Feedback:** "THIS IS CRITICAL NEVER PUT NUMBERS"

- **Fix:** Replaced all 40+ numbers with qualitative language + measurement methodology



**Feedback:** "Product should just come in with product requirements... engineers will propose their own architecture"

- **Fix:** Removed architecture details, added blank Part 2 for engineers to fill in



**Feedback:** "Try to scope them in terms of user stories not per service"

- **Fix:** Reorganized from component-based to user story format grouped by role



**Feedback:** "Missing: list of services that this feature would impact"

- **Fix:** Added explicit "Services Impacted" section



**Feedback:** "Stakeholder ownership doesn't need to be here"

- **Fix:** Deleted stakeholder ownership table entirely



---



## Lessons Learned



**1. Separation of Concerns Works**

- Product defines WHAT/WHY (100 lines)

- Engineers define HOW (fill in Part 2)

- No wasted effort on architecture that gets redesigned



**2. Qualitative Language is Honest**

- Numbers create false precision

- "Substantial reduction (measure after baseline)" is honest

- "$40K → $20K (50% reduction)" is unverifiable speculation



**3. User Stories Create Clarity**

- Organizing by user role shows who benefits

- Component organization hides user value

- Engineers can prioritize by user impact



**4. Brevity Increases Readership**

- 187 lines can be read in 5 minutes

- 1391 lines don't get read at all

- Concise PRDs are more effective



**5. Collaborative Model Empowers Engineers**

- Blank Part 2 invites engineering input

- Engineers propose architecture based on requirements

- Creates true collaboration, not dictation



---



## Before/After Comparison



**Before PRD Readability Test:**

- Length: 1391 lines

- Reading time: 30-40 minutes

- Engineer feedback: "Too heavy, not approachable"

- Numbers: 40+ instances

- Architecture: Fully defined by product

- Result: **Engineers didn't read it**



**After PRD Readability Test:**

- Length: 187 lines

- Reading time: 5 minutes

- Structure: Clean, scannable, user-focused

- Numbers: Zero (all qualitative)

- Architecture: Engineers to propose

- Result: **Readable and actionable**



---



## Template Success Metrics



The analytics PRD revision demonstrates the template's effectiveness:



✅ **Length:** 187 lines (within 100-200 target)

✅ **Numbers:** Zero specific numbers (all replaced with qualitative language)

✅ **Structure:** Part 1 (requirements) + Part 2 (blank for engineers)

✅ **User Stories:** Organized by role with proper format

✅ **Services List:** Explicit "Services Impacted" section

✅ **Approachability:** Can be read in 5 minutes



This template codifies these lessons for future PRDs, ensuring consistent quality and engineering satisfaction.



---



**END OF PRD TEMPLATE & WRITING GUIDE**