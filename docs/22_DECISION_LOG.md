# M I K A S A
## Architecture Decision Log

**File:** `docs/22_DECISION_LOG.md`

**Version:** 0.1.0

**Status:** ACTIVE GOVERNANCE DOCUMENT

**Authority:** Architectural decisions, technical choices, major trade-offs, superseded decisions, and decision history

**Applies to:** All Mikasa architecture, implementation, technology selection, subsystem contracts, security decisions, persistence, interfaces, tools, models, testing, and future major refactors.

---

# 1. Purpose

This document defines how major technical and architectural decisions are recorded in M I K A S A.

The Decision Log exists so contributors and coding agents do not repeatedly ask:

```text id="h36g77"
Why are we using this?

Who decided this?

Was another option considered?

Can I replace it?

Is this temporary?

What breaks if this changes?
```

Important decisions must have durable context.

---

# 2. Core Principle

Architecture decisions should not live only in:

- Chat history.
- Source-code comments.
- One developer's memory.
- Old prompts.
- Commit messages.
- Assumptions made by coding agents.

They should be recorded explicitly.

```text id="cu6b5m"
PROBLEM
   |
   v
OPTIONS
   |
   v
TRADE-OFFS
   |
   v
DECISION
   |
   v
RATIONALE
   |
   v
CONSEQUENCES
   |
   v
RECORDED
```

---

# 3. What Requires a Decision Record

A Decision Record should be created when a choice:

- Affects multiple components.
- Is difficult or expensive to reverse.
- Changes architecture boundaries.
- Introduces an important dependency.
- Establishes a project-wide convention.
- Changes security behavior.
- Changes persistence semantics.
- Changes provider contracts.
- Changes core data models.
- Changes deployment architecture.
- Replaces a previously approved design.

---

# 4. Examples of Decisions Requiring Records

Examples include:

```text id="jewyk8"
Programming language

Application framework

Initial persistence technology

Task state model

Memory backend

Tool protocol

MCP strategy

Sandbox strategy

Secrets storage

Model provider abstraction

Browser automation technology

UI framework

Desktop packaging technology

Event architecture

Testing framework

Plugin architecture

Multi-agent communication model
```

---

# 5. What Usually Does Not Require an ADR

Examples:

- Variable names.
- Small private helper functions.
- Minor CSS tweaks.
- Test-data naming.
- One-line implementation fixes.
- Small internal refactors that preserve architecture.

Not every technical choice should become bureaucracy.

---

# 6. Decision Record vs. Task

A task answers:

```text id="0mf4x7"
WHAT ARE WE BUILDING?
```

A Decision Record answers:

```text id="8r8baq"
WHY ARE WE BUILDING IT THIS WAY?
```

A task may depend on one or more decisions.

---

# 7. Decision IDs

Decisions should use stable identifiers.

Recommended format:

```text id="l9ivnt"
<AREA>-ADR-<NUMBER>
```

Examples:

```text id="vctg6s"
STATE-ADR-002

MODEL-ADR-001

SECURITY-ADR-004

UI-ADR-002
```

Existing architecture documents already reference planned ADR IDs.

Those IDs should be preserved where practical.

---

# 8. Decision Status

Each Decision Record should use one of:

```text id="o1x5zd"
PROPOSED

ACCEPTED

REJECTED

DEFERRED

SUPERSEDED

DEPRECATED
```

---

# 9. PROPOSED

The decision is under consideration.

Implementation should not depend on it as final unless the active task explicitly permits a prototype.

---

# 10. ACCEPTED

The decision is approved and should guide implementation.

Coding agents should treat accepted decisions as authoritative.

---

# 11. REJECTED

The option was considered and intentionally not selected.

The reason should remain recorded.

This helps prevent future agents from repeatedly proposing the same rejected design without new evidence.

---

# 12. DEFERRED

The decision is real but does not need to be made yet.

Example:

```text id="6z9guc"
Future distributed task broker
```

may remain deferred until distributed workers exist.

---

# 13. SUPERSEDED

A newer decision replaced this one.

A superseded record must reference its replacement.

Example:

```text id="5ewizp"
SUPERSEDED BY:
STATE-ADR-014
```

Do not delete the old record.

Historical context matters.

---

# 14. DEPRECATED

The decision still describes historical/current behavior but the architecture intends to remove it.

Use this sparingly.

---

# 15. Decision Lifecycle

```text id="2nfl24"
QUESTION IDENTIFIED
      |
      v
PROPOSED
      |
      +----> REJECTED
      |
      +----> DEFERRED
      |
      v
ACCEPTED
      |
      v
IMPLEMENTED
      |
      +----> SUPERSEDED
      |
      +----> DEPRECATED
```

Implementation state may be tracked separately from decision status.

---

# 16. Decision Record Template

Use the following structure.

```text id="88fs2j"
## <ADR-ID> — <Decision Title>

Status:
PROPOSED / ACCEPTED / REJECTED / DEFERRED / SUPERSEDED

Date:
YYYY-MM-DD

Area:
Runtime / Tools / Memory / Security / UI / etc.

Related Phase:
Phase X

Related Tasks:
TASK-...

Related Docs:
docs/...

### Context

What problem requires a decision?

### Requirements

What must the chosen option satisfy?

### Options Considered

1. Option A
2. Option B
3. Option C

### Decision

What option was selected?

### Rationale

Why?

### Consequences

Positive and negative consequences.

### Risks

Known risks introduced.

### Revisit When

Conditions that would justify reconsidering this decision.

### Implementation Notes

Optional implementation guidance.

### Supersedes

ADR ID if applicable.

### Superseded By

ADR ID if applicable.
```

---

# 17. Context

The Context section should describe the actual problem.

Bad:

```text id="8qj878"
We need a database.
```

Better:

```text id="pvcqr8"
Mikasa requires local durable persistence for
tasks, executions, plans, cancellations, and
schema migrations. The first deployment target
is a local single-user application, and the
storage system should minimize operational
complexity.
```

---

# 18. Requirements

Requirements should come from architecture and product needs.

Example:

```text id="964xda"
- local durability
- transactions
- simple backup
- low operational overhead
- schema migrations
- cross-platform support
- future abstraction behind StateRepository
```

This prevents technology selection from becoming preference-driven.

---

# 19. Options Considered

Document realistic options.

Do not create fake alternatives simply to make the chosen option appear superior.

Example:

```text id="b0pxmr"
Option A — SQLite

Option B — PostgreSQL

Option C — document database
```

---

# 20. Option Analysis

Each option may include:

```text id="97zw43"
Benefits

Drawbacks

Complexity

Dependencies

Security

Performance

Portability

Migration impact
```

Only include dimensions relevant to the decision.

---

# 21. Decision

The Decision section should be direct.

Example:

```text id="cpo881"
Use SQLite as the initial operational state
database behind the StateRepository abstraction.
```

Avoid vague wording such as:

```text id="i1bp1c"
SQLite seems pretty good for now.
```

---

# 22. Rationale

Rationale should explain why the chosen option best satisfies current requirements.

It should not imply the chosen technology is universally superior.

Architecture decisions are contextual.

---

# 23. Consequences

Every decision has consequences.

Record both positive and negative.

Example:

```text id="c07a3n"
Positive:
- simple local deployment
- transactional
- minimal setup

Negative:
- not intended as final distributed database
- concurrency model may limit future worker scale
```

This makes future migration discussions more rational.

---

# 24. Risks

Risks should identify where the decision might fail.

Examples:

```text id="fyd7lw"
future concurrent workers may exceed chosen storage model

library may be poorly maintained

provider API may change

platform packaging may become difficult
```

---

# 25. Revisit Conditions

Every significant decision should define when it deserves reconsideration.

Example:

```text id="y3zt1e"
Revisit when:
- multiple remote workers are required
- write contention becomes a measured problem
- multi-device state synchronization becomes a product requirement
```

This prevents constant architecture churn.

---

# 26. No Reopening Without New Evidence

An accepted decision should not be reopened simply because another coding agent prefers a different tool.

A revisit should normally require:

- New requirement.
- New evidence.
- Measured limitation.
- Security issue.
- Maintenance issue.
- Significant ecosystem change.

---

# 27. Rejected Decisions

Rejected options should remain documented.

Example:

```text id="j7bcf1"
Rejected:
Run a full distributed database for the first
local MVP.

Reason:
Operational complexity is unnecessary for
current requirements.
```

Future contributors can then see that the option was considered.

---

# 28. Temporary Decisions

Some decisions are intentionally temporary.

Example:

```text id="ytig0m"
Use one model provider for MVP.
```

The ADR should state:

```text id="lr83ft"
This is an implementation scope decision,
not provider lock-in.
```

---

# 29. Decision Scope

A decision should clearly identify its scope.

Examples:

```text id="8yi252"
PROJECT-WIDE

SUBSYSTEM

MVP ONLY

PROJECT-SPECIFIC

EXPERIMENTAL
```

A project-local decision should not silently become a global architecture rule.

---

# 30. Evidence

Where a decision depends on research, link or summarize evidence.

Possible evidence:

- Repository research.
- Benchmarks.
- Prototype results.
- Documentation.
- Security analysis.
- Evaluation results.

Do not base important choices only on model familiarity.

---

# 31. Research Before Decision

When evidence is missing:

```text id="d0dw0w"
CREATE RESEARCH TASK
```

before forcing an architectural decision.

Example:

```text id="xzikgg"
RESEARCH:
Compare browser automation candidates.

THEN:

BROWSER-ADR-002
Select browser technology.
```

---

# 32. Prototype Before Decision

When architecture cannot be evaluated through documentation alone, use a bounded prototype.

Example:

```text id="qdx2fu"
Question:
Can candidate framework support streaming
tool events without excessive complexity?
```

Prototype only enough to answer the question.

---

# 33. Benchmark-Based Decisions

Performance decisions should use measurements.

Bad:

```text id="2ep8ud"
Framework A is faster.
```

Better:

```text id="ibyzog"
Under benchmark X on environment Y,
Framework A had median latency Z.
```

Do not fabricate benchmark precision.

---

# 34. Security Decisions

Security-related ADRs require extra care.

Examples:

- Sandbox architecture.
- Permission model.
- Secret storage.
- External action approval.
- Plugin isolation.
- Desktop-control boundaries.

Security decisions must not be silently changed by ordinary implementation tasks.

---

# 35. Security Decision Override

If implementation discovers a security flaw in an accepted design:

```text id="xalx49"
STOP / CONTAIN
```

then create a new security decision.

Security evidence may justify urgent supersession.

---

# 36. Data Model Decisions

Changes to canonical records such as:

```text id="yqf1bt"
TaskRecord

MemoryRecord

ToolDefinition

ModelResponse

PermissionContext
```

may require ADRs when semantics or ownership change materially.

Minor additive implementation details may not.

---

# 37. Provider Decisions

Selecting an initial provider does not mean hardcoding architecture around it.

Example ADR should distinguish:

```text id="6mt1dn"
INITIAL PROVIDER
```

from:

```text id="3kq7ne"
PROVIDER ABSTRACTION
```

---

# 38. Framework Decisions

Framework decisions should consider:

- Required capabilities.
- Project complexity.
- Cross-platform behavior.
- Testability.
- Maintenance.
- Ecosystem.
- Lock-in.

Avoid choosing a large framework solely because it provides many unused features.

---

# 39. Dependency Decisions

Not every dependency requires an ADR.

A major dependency likely does when it:

- Shapes architecture.
- Controls persistence.
- Controls agent runtime.
- Controls UI.
- Controls browser execution.
- Controls plugin system.

---

# 40. Build-vs-Adopt Decisions

Mikasa may either:

```text id="r7fbiv"
BUILD

ADOPT

ADAPT

HYBRID
```

a subsystem.

This is often an ADR-worthy choice.

Example:

```text id="pgy8to"
MEMORY-ADR-001
Custom memory service vs adapting MemOS concepts.
```

---

# 41. External Repository Reuse

If code from a research repository is considered for direct reuse, the decision must include:

- License.
- Provenance.
- Maintenance.
- Integration cost.
- Architectural fit.
- Modification requirements.

Architecture inspiration alone does not require code reuse.

---

# 42. Uncertain Provenance

If a repository has uncertain provenance or licensing:

```text id="edb65l"
DO NOT DIRECTLY COPY
```

until verified.

The project may still study general architectural ideas when appropriate.

---

# 43. Decision Conflicts

Two accepted ADRs must not contradict each other.

If conflict emerges:

1. Identify conflict.
2. Determine which decision is newer or broader.
3. Create a superseding ADR if needed.
4. Update relevant documentation.

Do not let implementation choose silently.

---

# 44. Decision Hierarchy

Decision records must still follow the project source-of-truth hierarchy.

They cannot override:

- Project Charter.
- PRD.
- Scope and Non-Goals.

without those documents also being formally revised.

---

# 45. Documentation Updates

After accepting an ADR, update affected architecture documentation.

Example:

If `STATE-ADR-002` selects the persistence technology, update relevant sections of:

```text id="qx8flg"
docs/03_SYSTEM_ARCHITECTURE.md

docs/11_STATE_AND_PERSISTENCE.md
```

The Decision Log should not become the only place current architecture is described.

---

# 46. Code Updates

Implementation should reference decisions through architecture, not litter every source file with ADR comments.

ADR references are useful where a design constraint is non-obvious.

---

# 47. Task Integration

`CURRENT_TASK.md` may reference:

```text id="j12kew"
Required Decisions:
STATE-ADR-002
```

If the decision is not accepted:

The task should normally remain blocked.

---

# 48. Decision Review

Before accepting an ADR, review:

```text id="ce54it"
Does this solve a current problem?

Are requirements explicit?

Are realistic alternatives considered?

Does it respect existing architecture?

Is it prematurely optimizing?

What does it make harder?

Can we reverse it?

What new dependency does it create?
```

---

# 49. Decision Ownership

During early development, major architecture decisions should be explicitly approved by the project owner.

Coding agents may:

- Research.
- Compare.
- Propose.
- Prototype.

They should not silently finalize high-impact architecture choices.

---

# 50. Coding Agent Behavior

If a coding agent encounters an unresolved decision:

Bad:

```text id="ntuzm4"
I'll choose Redis because I like it.
```

Correct:

```text id="ww3dd3"
This task requires a decision about persistence
technology. The relevant ADR is unresolved.
```

The agent should follow the Task Protocol.

---

# 51. Emergency Implementation Choice

Sometimes a task needs a small reversible choice before a full ADR is justified.

This may be allowed if:

- Scope is local.
- Choice is easy to replace.
- No security impact.
- No public contract impact.
- It is recorded as temporary.

Do not use this exception for major architecture.

---

# 52. Decision Batching

Related decisions may be evaluated together.

Example:

```text id="yi8hd2"
UI framework

desktop wrapper

UI/backend transport
```

may influence one another.

However, they should remain separable if their consequences differ.

---

# 53. Decision Size

Avoid ADRs so broad that they decide the entire project at once.

Bad:

```text id="x0j5nr"
ADR-001:
Use Python, SQLite, React, MCP, Docker,
WebSockets, Playwright, Redis, and Kubernetes.
```

Better:

Separate decisions by architectural concern.

---

# 54. Decision Granularity

Good decision:

```text id="oj9lai"
Select initial persistence technology.
```

Too small:

```text id="0dzs7k"
Use `i` instead of `index` in loop.
```

Too large:

```text id="3z4bve"
Define all future infrastructure forever.
```

---

# 55. Decision Log Organization

This file may initially contain all ADRs.

If it becomes large, decisions may later move into:

```text id="a67axl"
docs/decisions/
```

for example:

```text id="b3ff2v"
docs/decisions/
├─ STATE-ADR-002.md
├─ MODEL-ADR-001.md
└─ UI-ADR-002.md
```

`docs/22_DECISION_LOG.md` would then become an index.

Do not introduce this split until useful.

---

# 56. Decision Index

Maintain an index.

Suggested table:

| ID | Title | Status | Date |
|---|---|---|---|
| FOUNDATION-ADR-001 | Primary implementation language | PROPOSED | — |
| FOUNDATION-ADR-002 | Initial project structure | PROPOSED | — |
| STATE-ADR-002 | Initial persistence technology | PROPOSED | — |
| MODEL-ADR-001 | Initial model provider strategy | PROPOSED | — |

The table should reflect actual decisions as they are created.

---

# 57. Initial Decision Backlog

Before implementation, likely foundational decisions include:

```text id="j6dmgh"
FOUNDATION-ADR-001
Primary implementation language

FOUNDATION-ADR-002
Package/dependency management

FOUNDATION-ADR-003
Initial repository structure

CONFIG-ADR-001
Configuration system

TEST-ADR-001
Primary testing framework

STATE-ADR-002
Initial operational persistence technology

MODEL-ADR-001
Initial model provider abstraction

MODEL-ADR-002
Initial real provider

TOOLS-ADR-001
Tool contract representation

SECURITY-ADR-001
Permission capability model
```

Not all must be decided simultaneously.

---

# 58. Runtime Decision Backlog

Likely decisions:

```text id="sbl4pb"
RUNTIME-ADR-001
Canonical task state model

RUNTIME-ADR-002
Execution context representation

RUNTIME-ADR-003
Planner integration model

RUNTIME-ADR-004
Cancellation propagation

RUNTIME-ADR-005
Execution limit configuration
```

---

# 59. Memory Decision Backlog

Likely decisions:

```text id="0phix3"
MEMORY-ADR-001
Initial memory storage strategy

MEMORY-ADR-002
Retrieval strategy

MEMORY-ADR-003
Memory record schema

MEMORY-ADR-004
Semantic search timing

MEMORY-ADR-005
Memory consolidation strategy
```

---

# 60. Tool Decision Backlog

Likely decisions:

```text id="sxmr57"
TOOLS-ADR-001
Native tool API

TOOLS-ADR-002
Tool execution result contract

TOOLS-ADR-003
Filesystem workspace model

TOOLS-ADR-004
Terminal isolation model

TOOLS-ADR-005
MCP adapter design
```

---

# 61. Security Decision Backlog

Likely decisions:

```text id="y6gcni"
SECURITY-ADR-001
Permission capability representation

SECURITY-ADR-002
Approval model

SECURITY-ADR-003
Secret storage

SECURITY-ADR-004
Filesystem boundary enforcement

SECURITY-ADR-005
Sandbox strategy
```

---

# 62. Coding Decision Backlog

Likely decisions:

```text id="rv0cze"
CODE-ADR-001
Workspace representation

CODE-ADR-003
Source-search approach

CODE-ADR-004
Patch/edit mechanism

CODE-ADR-008
Git integration level
```

---

# 63. Research Decision Backlog

Likely decisions:

```text id="m12z96"
RESEARCH-ADR-001
Search provider architecture

RESEARCH-ADR-002
Browser automation technology

RESEARCH-ADR-003
Source schema

RESEARCH-ADR-005
Citation representation
```

---

# 64. UI Decision Backlog

Likely decisions:

```text id="h8f6wm"
UI-ADR-001
Initial UI platform

UI-ADR-002
UI framework

UI-ADR-004
Application transport

UI-ADR-005
Real-time event transport
```

These should be made when the relevant phase approaches.

---

# 65. Decision Revisit Procedure

To revisit an accepted decision:

```text id="v4zjgr"
1. identify new evidence

2. reference existing ADR

3. describe why assumptions changed

4. evaluate alternatives

5. create superseding ADR

6. update architecture docs

7. migrate implementation deliberately
```

Do not directly edit the old ADR to pretend history was different.

---

# 66. Supersession Example

Old:

```text id="yvajhs"
STATE-ADR-002
Use SQLite for local MVP.
Status: SUPERSEDED
```

New:

```text id="d942aa"
STATE-ADR-014
Introduce PostgreSQL for distributed deployment.
Status: ACCEPTED

Supersedes:
STATE-ADR-002 for distributed deployments.
```

The old decision was not necessarily wrong.

The requirements changed.

---

# 67. Decision Review After Implementation

After implementation, verify whether assumptions were correct.

If a decision produces unexpected problems:

Record them.

This may become evidence for future reconsideration.

---

# 68. Failed Decision

Architecture decisions can turn out poorly.

That is acceptable.

What matters is:

```text id="qjulch"
evidence

traceability

ability to revise

migration plan
```

Do not defend a bad decision merely because it was documented.

---

# 69. Decision Metrics

The project does not need to score ADRs.

However, useful historical signals may include:

- Frequency of reversals.
- Repeated architecture churn.
- Dependencies causing major incidents.

These may inform process improvement later.

---

# 70. No Architecture by Popularity

A technology should not be selected only because:

```text id="r7346p"
everyone uses it

it is trending

another agent project uses it

the model knows it well
```

Selection should reflect Mikasa's requirements.

---

# 71. No Architecture by Fear

Likewise, do not overbuild merely because:

```text id="61a1rp"
we might need massive scale someday
```

The current architecture deliberately favors a strong modular local core before distributed complexity.

---

# 72. Reversibility

Prefer reversible early decisions when two options satisfy current needs similarly.

Example:

Use:

```text id="f3ihf4"
StateRepository abstraction
```

so persistence implementation can change later.

Reversibility does not eliminate the need for good initial decisions.

---

# 73. Abstraction vs. Over-Abstraction

Do not create interfaces for every tiny function merely to make decisions reversible.

Create abstraction boundaries around genuinely replaceable systems:

```text id="4hi11k"
ModelProvider

MemoryStore

StateRepository

SearchProvider

SpeechToTextProvider
```

Avoid speculative abstraction everywhere.

---

# 74. Decision and Scope

Architecture decisions must respect:

`docs/02_SCOPE_AND_NON_GOALS.md`

Example:

Selecting Kubernetes during a local MVP would conflict with the explicit non-goal of premature enterprise infrastructure unless strong evidence changes project scope.

---

# 75. Decision and Development Phase

Choose technology when the phase needs it.

Example:

```text id="tip0zj"
VOICE-ADR-002
```

does not need resolution during Task Runtime development.

Deferred decisions reduce wasted planning.

---

# 76. Decision and Testing

Important architecture assumptions should be tested.

Example:

Decision:

```text id="4zc7j8"
SQLite is sufficient for MVP persistence.
```

Tests should validate:

- Transactions.
- Restart persistence.
- Required concurrency behavior.
- Migrations.

Architecture decisions should survive contact with implementation.

---

# 77. Decision and Security

If a technology cannot satisfy required security constraints, convenience does not override those constraints.

Security requirements remain higher priority.

---

# 78. Decision and Performance

Avoid performance-driven architecture decisions without measured need.

However, once a performance requirement becomes real, measurements should inform the ADR.

---

# 79. Decision and Maintainability

Evaluate whether the project can realistically maintain a technology.

A highly capable dependency with extreme operational complexity may be a worse choice for Mikasa than a simpler adequate alternative.

---

# 80. Decision and Resource Usage

Mikasa may target consumer machines.

Technology decisions should consider:

- RAM.
- CPU.
- Storage.
- Startup overhead.
- Background resource use.

Especially for local models, browser automation, sandboxes, and desktop apps.

---

# 81. Decision and Cross-Platform Support

If Mikasa intends to support multiple operating systems, decisions should consider portability.

Platform-specific dependencies may still be used where isolated behind adapters.

---

# 82. Decision and User Experience

Architecture decisions can affect UX.

Example:

Persistent background worker design affects whether tasks survive UI closure.

UI architecture should reflect actual backend capability.

---

# 83. Decision and Deployment

Initial deployment should remain simple.

Future deployment requirements may justify different decisions.

Record scope explicitly.

---

# 84. Decision and Versioning

When an ADR changes a public or persisted contract, consider:

- Schema migration.
- API versioning.
- Backward compatibility.
- Stored data.

Architecture is not only code structure.

---

# 85. Decision and Self-Improvement

The Self-Improvement System may propose an architecture change.

It may not approve it.

Core architecture modifications still require this decision process.

---

# 86. Decision and Multi-Agent

Specialist agents may research options.

The Main Agent or developer may synthesize evidence.

Architectural authority remains in the project governance process.

---

# 87. Decision and Generated Recommendations

AI-generated recommendations should be treated as analysis.

They become architecture only after being accepted and recorded.

---

# 88. Decision Completeness

An ADR does not need a twenty-page essay.

It does need enough context that someone months later can understand:

```text id="vvq29x"
what problem existed

what was selected

why

what trade-offs were accepted
```

---

# 89. Decision Quality Checklist

Before accepting:

```text id="1pr94h"
[ ] problem clearly defined

[ ] current requirements listed

[ ] realistic alternatives considered

[ ] decision explicit

[ ] rationale documented

[ ] drawbacks documented

[ ] risks documented

[ ] scope defined

[ ] revisit conditions defined

[ ] affected docs identified
```

---

# 90. Decision Acceptance Checklist

After acceptance:

```text id="n1yu7a"
[ ] status set to ACCEPTED

[ ] date recorded

[ ] related task updated

[ ] architecture docs updated

[ ] implementation follows decision

[ ] tests validate important assumptions
```

---

# 91. Decision Supersession Checklist

When replacing an ADR:

```text id="bh16vl"
[ ] old decision remains preserved

[ ] old status becomes SUPERSEDED

[ ] new ADR references old ADR

[ ] migration impact documented

[ ] affected architecture updated

[ ] affected implementation updated deliberately
```

---

# 92. Initial Decision Index

No implementation technologies are approved merely by this initial index.

| ID | Decision | Status |
|---|---|---|
| FOUNDATION-ADR-001 | Primary implementation language | PROPOSED |
| FOUNDATION-ADR-002 | Package and dependency management | PROPOSED |
| FOUNDATION-ADR-003 | Initial repository structure | PROPOSED |
| CONFIG-ADR-001 | Configuration architecture | PROPOSED |
| TEST-ADR-001 | Primary test framework | PROPOSED |
| RUNTIME-ADR-001 | Canonical task state model | PROPOSED |
| STATE-ADR-002 | Initial persistence technology | PROPOSED |
| MODEL-ADR-001 | Model provider abstraction | PROPOSED |
| MODEL-ADR-002 | Initial real model provider | PROPOSED |
| TOOLS-ADR-001 | Native tool contract | PROPOSED |
| SECURITY-ADR-001 | Permission capability model | PROPOSED |
| MEMORY-ADR-001 | Initial memory persistence strategy | PROPOSED |

Additional ADRs should be added only as they become relevant.

---

# 93. Example ADR

## STATE-ADR-002 — Initial Operational Persistence Technology

**Status:** PROPOSED

**Date:** —

**Area:** State & Persistence

**Related Phase:** Phase 6

### Context

Mikasa requires durable local storage for tasks, executions, plans, action records, cancellations, and schema versions.

The initial deployment target is a local single-user application.

### Requirements

- Durable local persistence.
- Transaction support.
- Simple setup.
- Cross-platform operation.
- Low resource overhead.
- Backup support.
- Migration support.
- Compatibility with repository abstraction.

### Options Considered

1. SQLite
2. PostgreSQL
3. Document database

### Decision

Not yet selected.

### Rationale

Pending Phase 0 research.

### Consequences

Pending.

### Risks

Pending.

### Revisit When

Decision will be made before Phase 6 implementation.

---

# 94. Example Accepted ADR

Hypothetical example only:

```text id="dyw2th"
FOUNDATION-ADR-001
Primary implementation language

Status:
ACCEPTED

Decision:
Use Language X for the Mikasa core.

Reason:
It best satisfies requirements A, B, C.

Consequences:
...
```

Do not treat hypothetical examples as current project decisions.

---

# 95. Decision Log Maintenance

Whenever an ADR is created or changed:

Update the decision index.

Do not allow accepted decisions to become buried and undiscoverable.

---

# 96. Decision Searchability

Decision titles should use terms contributors are likely to search.

Better:

```text id="zr71gu"
Initial Persistence Technology
```

than:

```text id="yzjmbv"
Storage Thoughts
```

---

# 97. Decision History

Do not erase architecture history simply to keep documents tidy.

History provides valuable context.

Superseded decisions may explain current compatibility code or migrations.

---

# 98. Final Decision Principle

Mikasa should not repeatedly reinvent its architecture.

The project must preserve these distinctions:

```text id="1qa8ik"
IDEA
   !=
DECISION

PREFERENCE
   !=
EVIDENCE

PROTOTYPE
   !=
ARCHITECTURE

ACCEPTED DECISION
   !=
PERMANENT FOREVER

NEW OPINION
   !=
REASON TO REWRITE
```

Architecture should evolve through:

```text id="vto2zv"
QUESTION

RESEARCH

OPTIONS

TRADE-OFFS

DECISION

IMPLEMENTATION

EVALUATION

REVISIT WHEN EVIDENCE CHANGES
```

**Make important decisions deliberately. Record why. Build consistently. Revisit them when the evidence—not boredom—changes.**