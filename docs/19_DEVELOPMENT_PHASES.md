# M I K A S A
## Development Phases & Build Order

**File:** `docs/19_DEVELOPMENT_PHASES.md`

**Version:** 0.1.0

**Status:** PROPOSED — Pending architecture research and approval

**Authority:** Development sequencing, dependency gates, phase entry criteria, phase exit criteria, and implementation order

**Applies to:** All Mikasa development work, planning files, coding agents, contributors, architecture decisions, testing, and release milestones.

---

# 1. Purpose

This document defines the order in which M I K A S A should be built.

The project contains many ambitious capabilities:

- Autonomous task execution.
- Memory.
- Tool use.
- Coding.
- Research.
- Multi-agent workflows.
- Voice.
- Computer use.
- Self-improvement.
- Long-running tasks.
- Advanced interfaces.

These capabilities must not be developed in arbitrary order.

Later systems depend on earlier systems being reliable.

The purpose of this file is to establish:

- Development phases.
- Phase dependencies.
- Entry criteria.
- Exit criteria.
- Required demonstrations.
- Deferred functionality.
- Rules for moving between phases.
- Rules preventing premature feature work.

---

# 2. Core Development Principle

Mikasa should be built from foundations upward.

```text
FOUNDATION
    |
    v
RUNTIME
    |
    v
MODELS
    |
    v
TOOLS
    |
    v
STATE
    |
    v
MEMORY
    |
    v
AUTONOMY
    |
    v
CODING
    |
    v
RESEARCH
    |
    v
PERSISTENT TASKS
    |
    v
MULTI-AGENT
    |
    v
INTERFACES
    |
    v
VOICE / COMPUTER USE
    |
    v
SELF-IMPROVEMENT
    |
    v
HARDENING
```

Later phases must reuse earlier systems.

They must not build parallel replacements.

---

# 3. Development Rule

A capability appearing in architecture documentation does not authorize its implementation.

Implementation requires:

```text
DOCUMENTED REQUIREMENT

+

APPROVED PHASE

+

APPROVED CURRENT TASK
```

All three conditions should be satisfied.

---

# 4. Source of Active Work

The active development state is controlled through:

```text
plans/CURRENT_PHASE.md

plans/CURRENT_TASK.md
```

`docs/19_DEVELOPMENT_PHASES.md` defines the possible sequence.

It does not automatically make every phase active.

---

# 5. Phase States

A phase may have one of these statuses:

```text
NOT_STARTED

RESEARCHING

READY

ACTIVE

VERIFYING

COMPLETE

BLOCKED

DEFERRED
```

Only one major phase should normally be `ACTIVE` at a time.

Small supporting tasks from earlier phases may continue when necessary.

---

# 6. Phase Promotion

A phase should move to `COMPLETE` only when:

- Required implementation exists.
- Required tests pass.
- Required acceptance scenario works.
- Known blockers are documented.
- Required architecture decisions are recorded.
- No critical unresolved regression remains.

A phase is not complete merely because its files exist.

---

# 7. No Feature Skipping

Coding agents must not skip foundational phases to implement exciting later features.

Examples of prohibited jumps:

```text
NO TASK RUNTIME
        |
        X
   MULTI-AGENT SWARM
```

```text
NO PERMISSION SYSTEM
        |
        X
   COMPUTER CONTROL
```

```text
NO EVALUATION SYSTEM
        |
        X
   AUTONOMOUS SELF-REWRITING
```

```text
NO PERSISTENCE
        |
        X
   LONG-RUNNING AUTONOMY
```

---

# 8. Phase Dependency Principle

Every phase should depend on the smallest stable set of earlier capabilities.

Example:

Voice should depend on:

- Application Gateway.
- Main Agent.
- Task System.
- Permission System.
- UI or client interface.

Voice should not create its own agent stack.

---

# 9. Phase 0 — Research & Architecture

## Objective

Understand the problem space and finalize the minimum architecture required to begin implementation.

## Primary Work

- Study selected open-source systems.
- Inspect relevant repositories.
- Compare architectural patterns.
- Confirm licenses where reuse is considered.
- Finalize core design documents.
- Resolve foundational ADRs.
- Select implementation language and project structure.
- Define MVP boundaries.

## Required Documents

At minimum:

```text
AGENTS.md

docs/00_PROJECT_CHARTER.md
docs/01_PRD.md
docs/02_SCOPE_AND_NON_GOALS.md
docs/03_SYSTEM_ARCHITECTURE.md
docs/04_AGENT_RUNTIME.md
docs/05_MEMORY_ARCHITECTURE.md
docs/06_TOOL_SYSTEM.md
docs/07_AUTONOMY_ENGINE.md
docs/09_MODEL_ROUTER.md
docs/10_SECURITY_PERMISSIONS.md
docs/11_STATE_AND_PERSISTENCE.md
docs/18_TESTING_AND_EVALS.md
```

Other architecture files may remain proposed.

## Exit Criteria

- Foundational architecture understood.
- Initial technology choices documented.
- MVP explicitly scoped.
- Core repository structure approved.
- Phase 1 task list created.
- No major unresolved question blocks project initialization.

---

# 10. Phase 1 — Project Foundation

## Objective

Create a clean, testable project foundation without implementing advanced agent behavior.

## Primary Components

```text
application bootstrap

configuration system

dependency management

logging foundation

core interfaces

test infrastructure

basic command entry point
```

## Required Outcomes

- Project runs locally.
- Configuration loads correctly.
- Tests execute.
- Core modules have clear boundaries.
- Development scripts exist.
- Basic structured logging exists.
- No unnecessary framework complexity.

## Not Included

- Autonomous task loop.
- Memory intelligence.
- Multi-agent system.
- Voice.
- Browser automation.
- Self-improvement.

## Exit Demo

```text
start Mikasa

load configuration

initialize core services

run health command

shutdown cleanly
```

---

# 11. Phase 2 — Task & Agent Runtime

## Objective

Create the execution engine that can receive a task and manage a bounded agent loop.

## Primary Components

```text
ApplicationGateway

TaskManager

AgentRuntime

ExecutionContext

basic ContextManager

cancellation

execution limits

structured errors
```

## Required Capabilities

- Create task.
- Create execution.
- Process agent step.
- Maintain execution state.
- Enforce step limits.
- Cancel execution.
- Produce structured outcome.
- Record runtime events.

## Initial Runtime

The runtime may initially operate with a fake or deterministic model adapter for unit testing.

A real model is introduced in the next phase.

## Exit Criteria

- Task state machine works.
- Runtime loop is bounded.
- Cancellation works.
- Errors are structured.
- Unit and integration tests pass.

---

# 12. Phase 3 — Model Layer

## Objective

Connect the runtime to a real model through a provider-independent interface.

## Primary Components

```text
ModelRouter

ModelProvider interface

ModelRequest

ModelResponse

Model capability metadata

one real provider adapter
```

## Required Capabilities

- Send real model request.
- Receive normalized response.
- Handle timeout.
- Handle provider errors.
- Support required tool-call representation.
- Protect credentials.
- Record model usage where available.

## Exit Demo

User sends a text request.

```text
USER
  |
  v
RUNTIME
  |
  v
MODEL ROUTER
  |
  v
REAL MODEL
  |
  v
RESPONSE
```

## Exit Criteria

One real provider reliably works through the abstraction.

---

# 13. Phase 4 — Tool Runtime

## Objective

Allow the agent to perform controlled real-world actions.

## Initial Tools

Suggested:

```text
filesystem.list

filesystem.read

filesystem.search

filesystem.write

terminal.execute
```

## Primary Components

```text
ToolRegistry

ToolExecutor

ToolDefinition

ToolInvocation

ToolResult

InputValidator

PermissionService integration
```

## Required Capabilities

- Tool registration.
- Schema validation.
- Workspace boundaries.
- Tool permission checks.
- Timeouts.
- Structured results.
- Cancellation.
- Tool observability.

## Exit Demo

```text
MODEL REQUESTS FILE

PERMISSION CHECKS

FILE READ EXECUTES

RESULT RETURNS TO MODEL

MODEL RESPONDS
```

## Exit Criteria

The model can use real tools without bypassing the runtime.

---

# 14. Phase 5 — Security Foundation

Security begins earlier, but this phase hardens it before autonomy expands.

## Objective

Ensure powerful tools remain bounded.

## Required Systems

```text
PermissionService

workspace boundaries

read/write separation

approval mechanism

secret handling

terminal controls

path escape protection
```

## Required Security Tests

- Path traversal.
- Absolute path escape.
- Symlink escape.
- Read-only violation.
- Unauthorized tool.
- Approval denial.
- Secret leakage.
- Prompt-injection attempt.

## Exit Criteria

Security suite passes before broader autonomy work.

---

# 15. Phase 6 — State & Persistence

## Objective

Make task and execution state survive application restart.

## Primary Components

```text
StateRepository

TaskRecord

ExecutionRecord

SessionRecord

PlanRecord

ActionRecord

schema migrations
```

## Required Capabilities

- Persist task.
- Persist execution.
- Persist outcome.
- Restore task after restart.
- Preserve cancellation.
- Record meaningful action state.

## Exit Demo

```text
CREATE TASK

RUN TASK

STOP MIKASA

RESTART MIKASA

TASK STILL EXISTS
```

## Exit Criteria

Persistent task state works reliably.

---

# 16. Phase 7 — Basic Memory

## Objective

Introduce deliberate persistent memory without advanced automatic learning.

## Initial Scope

```text
memory create

memory retrieve

memory update

memory delete

memory scope

memory provenance
```

## Primary Components

```text
MemoryService

MemoryStore

MemoryRecord

MemoryRetriever
```

## Required Categories

At minimum:

```text
semantic

procedural

project
```

Additional categories may be represented as metadata.

## Exit Demo

```text
remember project fact

restart Mikasa

retrieve fact

correct fact

retrieve correction

delete fact
```

## Exit Criteria

Memory lifecycle is real and persistent.

---

# 17. Phase 8 — Planning & Bounded Autonomy

## Objective

Allow Mikasa to pursue multi-step goals independently within strict boundaries.

## Primary Components

```text
Planner

Goal representation

ExecutionPlan

PlanStep

Progress evaluation

bounded retry

basic replanning

verification
```

## Required Behavior

```text
GOAL

PLAN

ACTION

OBSERVE

ADAPT

VERIFY

COMPLETE
```

## Required Protections

- Maximum steps.
- Maximum retries.
- Cancellation.
- Permission checks.
- Stop conditions.
- No infinite replanning.

## Exit Demo

Complete a multi-step filesystem or project task.

---

# 18. Phase 9 — Coding Engine

## Objective

Make Mikasa useful as a disciplined coding agent.

## Primary Capabilities

```text
repository inspection

source search

safe editing

terminal commands

test execution

git status

diff review

verification
```

## Mandatory Workflow

```text
INSPECT

UNDERSTAND

PLAN

EDIT

TEST

DIFF

VERIFY
```

## Exit Demo

Sample repository:

- One real failing test.
- Mikasa diagnoses bug.
- Mikasa changes production code.
- Test passes.
- Diff is reviewed.
- Existing unrelated work is preserved.

## Exit Criteria

Core coding end-to-end evaluation passes.

---

# 19. Phase 10 — Research Engine

## Objective

Give Mikasa reliable source-backed research capability.

## Primary Components

```text
ResearchTask

SearchProvider

SourceRecord

ContentExtractor

EvidenceRecord

Claim

Citation
```

## Required Workflow

```text
QUESTION

SEARCH

SOURCE

EVIDENCE

COMPARE

VERIFY

SYNTHESIZE

CITE
```

## Exit Demo

Research three open-source projects and produce a source-backed comparison.

## Exit Criteria

Citation and source-provenance evaluations pass.

---

# 20. Phase 11 — Skills

## Objective

Create reusable structured workflows.

## Primary Components

```text
SkillService

SkillDefinition

SkillRegistry

Skill execution metadata
```

## Initial Skills

Skills should be derived from real needs.

Examples:

```text
inspect Python project

run known project test workflow

perform repository research
```

Do not create hundreds of speculative skills.

## Exit Criteria

At least one reusable skill improves a real workflow without bypassing runtime controls.

---

# 21. Phase 12 — Long-Running Tasks

## Objective

Allow work to persist beyond one foreground interaction.

## Primary Capabilities

```text
checkpoints

pause

resume

recovery

task queue

background worker
```

## Required Predecessors

This phase depends heavily on:

- Persistence.
- Permissions.
- Observability.
- Cancellation.
- Testing.

## Exit Demo

```text
START TASK

COMPLETE STEP

CHECKPOINT

INTERRUPT PROCESS

RESTART

RESUME SAFELY

COMPLETE TASK
```

## Exit Criteria

No completed external side effect is blindly duplicated during recovery.

---

# 22. Phase 13 — Scheduling

## Objective

Allow explicitly authorized work to execute at future times.

## Capabilities

```text
scheduled tasks

recurring tasks

next run

task template

permission revalidation
```

Scheduling must use real persistent execution infrastructure.

A model saying:

```text
"I'll do this later."
```

does not count.

## Exit Criteria

A scheduled task survives application restart and executes through the normal runtime.

---

# 23. Phase 14 — Multi-Agent System

## Objective

Allow Mikasa to delegate work to bounded specialist agents.

## Initial Specialists

Keep the first system small:

```text
Research Specialist

Coding Specialist

Testing Specialist
```

## Required Capabilities

- Agent Registry.
- Agent Manager.
- Structured delegation.
- Child tasks.
- Scoped permissions.
- Shared runtime.
- Structured result return.
- Cancellation propagation.

## Not Yet Required

- Agent swarms.
- Unlimited recursive delegation.
- Huge permanent agent teams.
- Complex peer-to-peer conversations.

## Exit Demo

Main Agent delegates investigation and testing while retaining parent-task control.

---

# 24. Phase 15 — Web / Desktop Interface

## Objective

Provide a polished graphical interface over the real backend.

## Initial Views

```text
Mikasa

Tasks

Settings

Memory
```

when available.

## Required Behavior

- Real conversation.
- Real task state.
- Approval UI.
- Cancellation.
- Task result.
- Persistence across refresh.

## Visual Philosophy

```text
SIMPLE OUTSIDE

POWERFUL INSIDE
```

## Exit Criteria

UI acceptance tests pass against the real runtime.

---

# 25. Phase 16 — Advanced Browser System

## Objective

Introduce richer web interaction beyond basic research retrieval.

Potential capabilities:

```text
browser navigation

dynamic-page interaction

forms

downloads

screenshots

local-app preview

browser verification
```

## Uses

- Research.
- UI development.
- Web tasks.
- Computer-use workflows.

Browser actions must use the shared permission system.

---

# 26. Phase 17 — Voice

## Objective

Add voice as another interface to the existing Mikasa core.

## First Capabilities

```text
push-to-talk

STT

voice task creation

TTS

stop speech

voice cancellation
```

## Later

```text
wake word

streaming STT

streaming TTS

live conversation
```

## Exit Criteria

Voice and text use identical task, memory, and permission infrastructure.

---

# 27. Phase 18 — Computer Use

## Objective

Allow Mikasa to interact with graphical applications where API/tool access is insufficient.

Potential capabilities:

```text
screen observation

mouse control

keyboard control

window interaction

application-specific automation
```

This phase requires strong security.

## Required Predecessors

- Permission Service.
- Approval system.
- Observability.
- Task cancellation.
- Browser experience.
- Evaluation infrastructure.

Computer control must not be the default way to interact with systems that already expose safer APIs or tools.

---

# 28. Phase 19 — Controlled Self-Improvement

## Objective

Allow Mikasa to learn from task history and improve reusable behavior.

## Initial Capabilities

```text
observe recurring patterns

create improvement candidate

create skill candidate

evaluate candidate

version candidate

promote with approval

rollback
```

## Not Initially Allowed

```text
live runtime rewriting

automatic security changes

automatic core architecture changes

unrestricted tool installation
```

## Exit Criteria

One real workflow improvement is:

- Detected.
- Tested.
- Compared against baseline.
- Promoted safely.
- Rollback-capable.

---

# 29. Phase 20 — Production Hardening

## Objective

Improve reliability, performance, maintainability, and deployment quality after core capabilities exist.

Areas include:

```text
performance

resource control

migration safety

crash recovery

dependency security

packaging

install/update workflows

diagnostic tooling

backup/restore

documentation

release automation
```

This is not a substitute for building correctly earlier.

Security and testing exist throughout development.

---

# 30. Future Experimental Phases

Only after the core system is mature should the project investigate capabilities such as:

```text
advanced proactive assistance

multi-device coordination

distributed workers

large specialist ecosystems

advanced desktop embodiment

3D avatar

complex local model orchestration

autonomous capability discovery

advanced environment simulation
```

These belong to the future roadmap.

They must not distort the early architecture.

---

# 31. Cross-Cutting Systems

Some systems are developed across many phases rather than once.

These include:

```text
SECURITY

TESTING

OBSERVABILITY

DOCUMENTATION

CONFIGURATION

ERROR HANDLING
```

They must evolve alongside the project.

---

# 32. Security Development Timeline

Security does not wait until a final "security phase."

Example:

```text
FILESYSTEM TOOL
     |
     v
FILESYSTEM SECURITY ADDED AT SAME TIME
```

```text
MULTI-AGENT
     |
     v
DELEGATED PERMISSION ISOLATION ADDED AT SAME TIME
```

```text
DESKTOP CONTROL
     |
     v
DESKTOP PERMISSION POLICY ADDED FIRST
```

---

# 33. Testing Development Timeline

Every phase introduces its tests.

Example:

```text
MODEL ROUTER
    +
MODEL CONTRACT TESTS
```

```text
MEMORY
    +
MEMORY LIFECYCLE TESTS
```

```text
CODING ENGINE
    +
CODING ACCEPTANCE EVAL
```

Testing must not be postponed until the end.

---

# 34. Observability Development Timeline

Every major subsystem should emit events from the time it becomes operational.

Do not retrofit observability after the architecture becomes difficult to debug.

---

# 35. Architecture Decision Timing

ADRs should be written when a real decision becomes necessary.

Do not attempt to choose every future technology on day one.

Examples:

Choose:

```text
initial persistence technology
```

before persistence implementation.

Do not choose:

```text
future distributed message broker
```

before distributed infrastructure exists.

---

# 36. Build-Only-What-Is-Needed Rule

For every phase ask:

```text
WHAT DOES THE NEXT ACCEPTANCE SCENARIO REQUIRE?
```

Build that.

Avoid:

```text
WHAT MIGHT WE MAYBE NEED TWO YEARS FROM NOW?
```

unless the decision would be very expensive to reverse later.

---

# 37. Extension Points

Early phases should create clean extension boundaries.

Example:

```text
ModelProvider
```

should allow future providers.

But Phase 3 does not need:

```text
15 provider integrations
```

Likewise:

```text
ToolRegistry
```

should allow MCP later.

But Phase 4 does not need every MCP server.

---

# 38. Thin Vertical Slices

Prefer working end-to-end slices over disconnected infrastructure.

Example:

Better:

```text
USER REQUEST
 -> MODEL
 -> FILE READ TOOL
 -> RESULT
```

than:

```text
40 tool classes
but none connected to the agent
```

Each major phase should produce something demonstrably working.

---

# 39. Vertical Slice 1

First useful internal slice:

```text
TEXT REQUEST

MODEL

RESPONSE
```

---

# 40. Vertical Slice 2

```text
TEXT REQUEST

MODEL

FILESYSTEM TOOL

MODEL

RESULT
```

---

# 41. Vertical Slice 3

```text
TASK

PLAN

MODEL

FILESYSTEM / TERMINAL

VERIFICATION

PERSISTED RESULT
```

---

# 42. Vertical Slice 4

```text
REAL CODING TASK

INSPECT

TEST

EDIT

TEST

VERIFY
```

At this point Mikasa begins becoming practically useful.

---

# 43. Phase Entry Criteria

Every phase should define entry criteria.

General entry requirements:

- Required predecessor phases complete.
- Major blockers resolved.
- Current phase document exists.
- Current task exists.
- Relevant ADRs resolved or explicitly deferred.
- Test infrastructure is available.

---

# 44. Phase Exit Criteria

Every phase should define:

```text
IMPLEMENTATION

TESTS

ACCEPTANCE DEMO

DOCUMENTATION

KNOWN LIMITATIONS
```

All must be evaluated before completion.

---

# 45. Phase Failure

If a phase fails its acceptance scenario:

```text
DO NOT MOVE FORWARD
```

Fix the underlying issue or document a deliberate scope change.

Later phases should not be used to hide foundational failures.

---

# 46. Phase Rollback

Sometimes a chosen architecture may fail.

A phase may need to return to:

```text
RESEARCHING
```

or:

```text
ACTIVE
```

after previously reaching verification.

This is acceptable.

The project must prefer correctness over pretending development is linear.

---

# 47. Parallel Work

Some tasks can run in parallel.

Examples:

```text
documentation

test fixtures

UI design research
```

But core implementation dependencies still matter.

For example:

A UI prototype may be designed early.

It should not be treated as an implemented task system before the backend exists.

---

# 48. Prototype Work

Prototypes are allowed when they answer a specific question.

Every prototype should define:

```text
QUESTION

SCOPE

SUCCESS CRITERIA

DISPOSAL / PROMOTION PLAN
```

A prototype does not automatically become production architecture.

---

# 49. Research Spikes

Example:

```text
Can SQLite support the persistence behavior we need?
```

A small experimental implementation may be created.

The result should answer the question.

Do not expand the spike into unrelated production work.

---

# 50. Technical Debt

Technical debt should be recorded explicitly.

Possible categories:

```text
TEMPORARY_IMPLEMENTATION

MISSING_TEST

SCALABILITY_LIMITATION

PLATFORM_LIMITATION

DEFERRED_REFACTOR
```

Do not silently treat hacks as final architecture.

---

# 51. Refactoring

Refactoring is appropriate when:

- Current architecture blocks the next approved phase.
- Complexity is causing real defects.
- Tests protect behavior.
- The change is scoped.

Do not conduct large rewrites merely because a different architecture looks more elegant.

---

# 52. Repository Restructuring

Large file-tree changes require justification.

Before restructuring:

- Identify problem.
- Explain benefits.
- Assess migration impact.
- Preserve tests.
- Update architecture docs.

The repository must not be reorganized repeatedly because each coding agent prefers a different layout.

---

# 53. Technology Selection

Technology choices should optimize for:

```text
reliability

maintainability

local development

cross-platform support

ecosystem

testing

resource requirements
```

Avoid selecting technology only because it is fashionable.

---

# 54. Dependency Selection

A new dependency should answer:

```text
WHAT PROBLEM DOES THIS SOLVE?

WHY NOT EXISTING PROJECT CAPABILITY?

IS IT MAINTAINED?

WHAT IS ITS LICENSE?

WHAT SECURITY RISK DOES IT ADD?

CAN WE REMOVE IT LATER?
```

Dependencies should not accumulate casually.

---

# 55. Research Repository Usage

The supplied reference repositories are research sources.

They may provide:

- Patterns.
- Concepts.
- Implementations.
- Libraries.

Mikasa must not become an accidental copy of any one project.

Relevant code reuse requires license and provenance review.

---

# 56. Phase Research Mapping

Suggested research focus:

```text
RUNTIME
AgenticSeek
DeerFlow
Hive
OpenHands

MEMORY
MemOS
TencentDB-Agent-Memory
AgentMemory

CODING
ZCode
Gemini CLI
OpenHands

MULTI-AGENT
Hive
DeerFlow
Agency Agents

RESEARCH
Agent-Reach
AgenticSeek
DeerFlow

UI
eDEX-UI
OpenHands
AstrBot
OpenClaw
```

Research priorities may change as evidence develops.

---

# 57. Release Naming

Early project versions should reflect maturity accurately.

Possible progression:

```text
0.0.x
foundation

0.1.x
core runtime

0.2.x
tools + persistence

0.3.x
memory + autonomy

0.4.x
coding

0.5.x
research

0.6.x
long-running

0.7.x
multi-agent

0.8.x
interface

0.9.x
hardening

1.0
defined stable milestone
```

This is conceptual, not yet an approved versioning scheme.

---

# 58. What 1.0 Should Mean

Version `1.0` should not mean:

```text
Mikasa contains every imagined feature.
```

It should mean:

```text
the defined product milestone is reliable enough to be considered stable
```

A stable core may reach 1.0 before advanced voice, avatars, or self-improvement.

---

# 59. Phase Dependency Table

| Phase | Depends On |
|---|---|
| 0 Research | None |
| 1 Foundation | Phase 0 |
| 2 Runtime | Phase 1 |
| 3 Models | Phase 2 |
| 4 Tools | Phases 2–3 |
| 5 Security | Phases 1–4 |
| 6 Persistence | Phases 1–2 |
| 7 Memory | Phase 6 |
| 8 Autonomy | Phases 2–7 |
| 9 Coding | Phases 4–8 |
| 10 Research | Phases 3–8 |
| 11 Skills | Phases 7–10 |
| 12 Long-running | Phases 6–8 |
| 13 Scheduling | Phase 12 |
| 14 Multi-Agent | Phases 8, 11, 12 |
| 15 UI | Core backend phases |
| 16 Browser | Tools + Security + Research |
| 17 Voice | Core backend + Interface |
| 18 Computer Use | Security + Tools + Observability |
| 19 Self-Improvement | Skills + Evals + Observability |
| 20 Hardening | All implemented production-target features |

This table describes logical dependency.

Actual scheduling may overlap when safe.

---

# 60. MVP Cut Line

The first serious MVP should approximately include:

```text
FOUNDATION

TASK RUNTIME

REAL MODEL

TOOL SYSTEM

SECURITY

PERSISTENCE

BASIC MEMORY

BOUNDED AUTONOMY

CODING ENGINE

OBSERVABILITY

TESTING
```

Optional depending on project priority:

```text
BASIC RESEARCH
```

Not required for MVP:

```text
VOICE

3D AVATAR

COMPUTER CONTROL

ADVANCED MULTI-AGENT

SELF-IMPROVEMENT

DISTRIBUTED WORKERS

COMPLEX DASHBOARDS
```

---

# 61. MVP Definition

A good MVP should be capable of this:

```text
USER:
Fix the failing test in this project.
```

Mikasa should:

```text
CREATE TASK

INSPECT PROJECT

RUN TEST

OBSERVE FAILURE

IDENTIFY RELEVANT CODE

PLAN FIX

EDIT FILE

RE-RUN TEST

VERIFY

REPORT RESULT

PERSIST TASK HISTORY
```

with:

- Real model.
- Real tools.
- Real permission checks.
- Real persistence.
- Real verification.

That is a meaningful autonomous agent foundation.

---

# 62. Anti-Wandering Rule

At any moment, a coding agent should be able to answer:

```text
WHAT PHASE ARE WE IN?

WHAT IS THE CURRENT TASK?

WHAT IS THE ACCEPTANCE CRITERION?

WHAT AM I NOT SUPPOSED TO BUILD YET?
```

If these cannot be answered, implementation should not proceed.

---

# 63. CURRENT_PHASE Template

`plans/CURRENT_PHASE.md` should eventually contain:

```text
# Current Phase

Phase:
Phase 2 — Agent Runtime

Status:
ACTIVE

Objective:
Implement the bounded task execution runtime.

In Scope:
- task creation
- execution context
- runtime loop
- cancellation

Out of Scope:
- memory
- multi-agent
- voice
- browser

Exit Criteria:
- runtime unit tests pass
- cancellation works
- bounded execution demo succeeds
```

---

# 64. CURRENT_TASK Template

`plans/CURRENT_TASK.md` should eventually contain:

```text
# Current Task

Task:
Implement TaskManager state transitions.

Why:
Required by Phase 2 Agent Runtime.

Files Expected:
src/runtime/task_manager.*
tests/runtime/test_task_manager.*

Acceptance:
- valid transitions succeed
- invalid transitions fail
- terminal states protected

Not In Scope:
- persistence
- UI
- memory
```

This keeps coding agents focused.

---

# 65. Phase Completion Report

When a phase is complete, record:

```text
WHAT WAS BUILT

WHAT WAS TESTED

ACCEPTANCE RESULT

KNOWN LIMITATIONS

ARCHITECTURE DECISIONS

DEFERRED WORK
```

This provides continuity for future contributors and coding agents.

---

# 66. Development Discipline

Every phase should follow:

```text
RESEARCH

DECIDE

IMPLEMENT

TEST

INTEGRATE

EVALUATE

DOCUMENT

COMPLETE
```

Avoid:

```text
IMPLEMENT

IMPLEMENT

IMPLEMENT

IMPLEMENT

THEN DISCOVER ARCHITECTURE IS WRONG
```

---

# 67. Final Development Principle

Mikasa should become powerful by stacking reliable systems.

Not by stacking features on unstable foundations.

The project must preserve these distinctions:

```text
VISION
    !=
CURRENT SCOPE

DOCUMENTED FEATURE
    !=
IMPLEMENTED FEATURE

IMPLEMENTED FEATURE
    !=
TESTED FEATURE

TESTED FEATURE
    !=
VERIFIED INTEGRATED SYSTEM

BACKLOG IDEA
    !=
ACTIVE TASK
```

The build order should always favor:

```text
SMALL WORKING CORE

BEFORE

LARGE UNVERIFIED SYSTEM
```

The project should reach each new level of capability only after the level below it is trustworthy.

**Build the spine first. Add muscles second. Add complexity only when the system underneath can carry it.**