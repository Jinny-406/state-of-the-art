# M I K A S A
## Product & Development Roadmap

**File:** `docs/23_ROADMAP.md`

**Version:** 0.1.0

**Status:** PROPOSED — Living roadmap

**Authority:** High-level product evolution, milestone sequencing, capability maturity, and long-term direction

**Applies to:** Product planning, development phases, backlog prioritization, milestone planning, architecture evolution, and release planning.

---

# 1. Purpose

This document defines the high-level roadmap for M I K A S A.

The roadmap answers:

```text
WHAT ARE WE BUILDING FIRST?

WHAT MAKES THE FIRST VERSION USEFUL?

WHAT COMES AFTER THE CORE?

WHEN DO ADVANCED FEATURES APPEAR?

WHAT IS LONG-TERM EXPLORATION?
```

The roadmap is intentionally higher-level than:

`docs/19_DEVELOPMENT_PHASES.md`

Development Phases defines the technical build order.

This Roadmap defines the product journey.

---

# 2. Product Direction

Mikasa is intended to evolve from:

```text
BASIC ASSISTANT
```

into:

```text
AUTONOMOUS PERSONAL AI AGENT PLATFORM
```

through deliberate capability layers.

The path is:

```text
FOUNDATION

↓

USEFUL AGENT

↓

RELIABLE AUTONOMOUS AGENT

↓

PERSISTENT PERSONAL ASSISTANT

↓

MULTI-CAPABILITY AGENT PLATFORM

↓

ADVANCED MIKASA
```

Each level should be useful before the next one begins.

---

# 3. Core Roadmap Principle

Mikasa should not attempt to become everything immediately.

The project follows:

```text
MAKE IT WORK

↓

MAKE IT RELIABLE

↓

MAKE IT USEFUL

↓

MAKE IT PERSISTENT

↓

MAKE IT BROADER

↓

MAKE IT SMARTER

↓

MAKE IT POLISHED
```

Capability should grow on top of reliability.

---

# 4. Roadmap Levels

The roadmap is divided into six major stages:

```text
STAGE A
Foundation

STAGE B
Mikasa Core MVP

STAGE C
Useful Mikasa

STAGE D
Persistent Mikasa

STAGE E
Advanced Mikasa

STAGE F
Long-Term Exploration
```

These stages group several development phases.

---

# 5. Stage A — Foundation

## Goal

Create a project that is architecturally clear enough to build without constant rewrites.

This stage establishes:

- Product definition.
- Architecture.
- Scope.
- Governance.
- Testing strategy.
- Task discipline.
- Technical decisions.
- Repository foundation.

---

# 6. Stage A Capabilities

The Foundation stage includes:

```text
PROJECT CHARTER

PRD

SCOPE / NON-GOALS

SYSTEM ARCHITECTURE

AGENT RUNTIME DESIGN

TOOL DESIGN

MEMORY DESIGN

SECURITY DESIGN

STATE DESIGN

TESTING DESIGN

TASK PROTOCOL

DECISION SYSTEM
```

And then:

```text
PROJECT BOOTSTRAP

CONFIGURATION

TEST RUNNER

BASIC LOGGING

CORE MODULE STRUCTURE
```

---

# 7. Stage A User Value

This stage does not yet produce a powerful agent.

Its value is preventing:

- Architecture drift.
- Random feature development.
- Duplicate systems.
- Premature complexity.
- Endless rewrites.

The result should be:

```text
A CLEAN FOUNDATION READY FOR REAL IMPLEMENTATION
```

---

# 8. Stage A Completion

Stage A is complete when:

- Core documentation is internally consistent.
- Foundational ADRs needed for implementation are accepted.
- Project runs.
- Tests run.
- Core structure exists.
- First implementation phase can begin confidently.

---

# 9. Stage B — Mikasa Core MVP

## Goal

Create the smallest version of Mikasa that is genuinely an autonomous agent rather than a chatbot.

This is the most important roadmap milestone.

---

# 10. Core MVP Capability

The MVP should support this workflow:

```text
USER GIVES GOAL

↓

MIKASA CREATES TASK

↓

MIKASA UNDERSTANDS GOAL

↓

MIKASA PLANS

↓

MIKASA USES REAL TOOLS

↓

MIKASA OBSERVES RESULTS

↓

MIKASA RECOVERS IF NEEDED

↓

MIKASA VERIFIES OUTCOME

↓

MIKASA REPORTS RESULT

↓

TASK STATE IS SAVED
```

---

# 11. Core MVP Components

Required:

```text
Task Manager

Agent Runtime

Model Router

One real model provider

Tool Registry

Tool Executor

Filesystem tools

Controlled terminal

Permission Service

Operational persistence

Basic memory

Planning

Bounded autonomy

Observability

Testing
```

---

# 12. Core MVP Main Use Case

Primary acceptance use case:

```text
"Inspect this project, find why the test fails,
fix it, and verify the result."
```

Mikasa should:

```text
1. inspect project
2. identify instructions
3. run test
4. observe failure
5. locate relevant code
6. create plan
7. edit source
8. run test again
9. inspect diff
10. verify
11. report
12. preserve task history
```

This single workflow exercises most of the core architecture.

---

# 13. Why Coding Is the First Serious Use Case

Coding is useful for the MVP because it requires:

- Reasoning.
- Tool use.
- File access.
- Terminal execution.
- Planning.
- Error handling.
- Verification.
- Persistence.
- Scope control.

If Mikasa can reliably complete a real coding workflow, much of the core architecture has been proven.

---

# 14. Core MVP Non-Goals

The MVP does not require:

```text
voice

wake word

desktop control

3D avatar

large agent teams

automatic self-modification

distributed workers

mobile ecosystem

complex browser automation

huge plugin marketplace
```

These features are intentionally later.

---

# 15. MVP Success

MVP success means:

```text
MIKASA CAN DO REAL WORK
```

not:

```text
MIKASA HAS MANY FEATURES
```

The key measurement is coherent task completion.

---

# 16. Stage C — Useful Mikasa

## Goal

Expand Mikasa from a coding-capable core into a broadly useful personal AI agent.

This stage adds major practical capabilities without yet requiring advanced persistent autonomy.

---

# 17. Useful Mikasa Capabilities

Likely capabilities:

```text
RESEARCH

RICHER MEMORY

SKILLS

BETTER CODING

BROWSER ACCESS

PROJECT ORGANIZATION

GRAPHICAL INTERFACE

MORE MODEL PROVIDERS

MORE TOOLS
```

---

# 18. Research

Mikasa should become capable of:

```text
SEARCH

BROWSE

READ

COMPARE

VERIFY

CITE

SYNTHESIZE
```

Example:

```text
"Research these five agent frameworks and
tell me how their memory systems differ."
```

The result should use actual sources.

---

# 19. Better Coding

Coding expands beyond small bug fixes.

Possible tasks:

```text
ADD FEATURE

REFACTOR COMPONENT

DEBUG APPLICATION

BUILD SMALL APP

INSPECT REPOSITORY

WRITE TESTS

UPDATE CONFIGURATION

VERIFY UI
```

Mikasa should still follow:

```text
INSPECT → PLAN → EDIT → TEST → VERIFY
```

---

# 20. Project-Aware Memory

Useful Mikasa should remember selected project knowledge.

Examples:

```text
project architecture

build commands

important decisions

verified conventions

reusable procedures
```

This should reduce repeated setup work.

---

# 21. Skills

Repeated successful workflows become reusable skills.

Examples:

```text
inspect a Python project

run Project Alpha tests

research GitHub architecture

verify a web UI
```

Skills should remain versioned and scoped.

---

# 22. Browser Capability

The browser becomes useful for:

- Research.
- Documentation.
- Web application verification.
- Public information gathering.
- Supported web workflows.

Browser automation must remain permission-controlled.

---

# 23. Graphical Interface

At this stage Mikasa should receive a polished primary interface.

Initial navigation may include:

```text
Mikasa

Tasks

Memory

Settings
```

Advanced details remain optional.

---

# 24. Useful Mikasa User Experience

A user should be able to say:

```text
"Research this library, update my project to use it,
run the tests, and tell me what changed."
```

Mikasa may combine:

```text
RESEARCH

CODING

TOOLS

MEMORY

VERIFICATION
```

within one coordinated task.

---

# 25. Stage C Completion

Useful Mikasa is achieved when the system can reliably perform several task families:

```text
coding

research

project work

file operations

multi-step analysis
```

without requiring manual control over every step.

---

# 26. Stage D — Persistent Mikasa

## Goal

Transform Mikasa from an agent that handles one active interaction into one that can safely maintain work over time.

This is where Mikasa begins to feel much more like a persistent personal assistant.

---

# 27. Persistent Capabilities

This stage introduces:

```text
CHECKPOINTS

PAUSE

RESUME

CRASH RECOVERY

BACKGROUND TASKS

TASK QUEUE

SCHEDULING

LONG-RUNNING JOBS

NOTIFICATIONS
```

---

# 28. Long-Running Work

Example:

```text
"Research this project deeply and prepare a
comparison report."
```

The work may:

- Span many steps.
- Save checkpoints.
- Continue independently of the UI.
- Recover from interruption.

---

# 29. Background Tasks

The UI should no longer need to stay open for work to exist.

Conceptually:

```text
USER CREATES TASK

↓

TASK PERSISTED

↓

BACKGROUND WORKER CLAIMS TASK

↓

TASK RUNS

↓

RESULT PERSISTED

↓

USER RETURNS LATER
```

---

# 30. Scheduling

Mikasa may support explicitly scheduled jobs.

Examples:

```text
run project health check each morning

prepare weekly research report

check selected source daily
```

Scheduled jobs must use real persistent infrastructure.

---

# 31. Recovery

A major Stage D capability is:

```text
PROCESS CRASH
     |
     v
RESTART
     |
     v
LOAD TASK
     |
     v
VERIFY LAST STATE
     |
     v
RESUME SAFELY
```

Mikasa must not repeat uncertain external actions blindly.

---

# 32. Persistent Memory Growth

Memory may become more sophisticated.

Possible additions:

```text
semantic retrieval

memory consolidation

memory relationships

skill memory

shared project knowledge
```

Only after the basic memory lifecycle is reliable.

---

# 33. Stage D User Experience

Example:

```text
"Start analyzing this codebase.
I need the report tomorrow."
```

The product should eventually support durable execution rather than relying on an open chat session.

---

# 34. Stage D Completion

Persistent Mikasa is achieved when:

- Important tasks survive restart.
- Tasks can pause/resume.
- Background execution is real.
- Scheduling is durable.
- Recovery is tested.
- User can inspect active and historical tasks.

---

# 35. Stage E — Advanced Mikasa

## Goal

Expand Mikasa's coordination, interaction methods, and adaptive behavior.

This stage builds on a mature core.

---

# 36. Advanced Capabilities

Potential capabilities include:

```text
MULTI-AGENT SPECIALISTS

VOICE

COMPUTER USE

ADVANCED BROWSER

MORE PLUGINS

LOCAL MODELS

MULTIMODAL INPUT

CONTROLLED SELF-IMPROVEMENT
```

---

# 37. Multi-Agent Coordination

Mikasa remains the single user-facing assistant.

Internally:

```text
MIKASA
  |
  +-- Research Specialist
  |
  +-- Coding Specialist
  |
  +-- Testing Specialist
```

Specialists exist only when delegation improves the task.

---

# 38. Multi-Agent Goal

The goal is not:

```text
maximum number of agents
```

It is:

```text
clear specialization + better outcomes
```

A task that one agent can handle should not automatically create five workers.

---

# 39. Voice

Voice should allow:

```text
"Mikasa, run the project tests."

"Mikasa, what happened with my task?"

"Mikasa, cancel that."

"Mikasa, research this for me."
```

Voice remains an interface to the same backend.

---

# 40. Advanced Voice

Later voice capabilities may include:

```text
wake word

streaming speech

barge-in

live conversation

spoken notifications
```

These are quality-of-life features, not core intelligence.

---

# 41. Computer Use

Mikasa may eventually interact with graphical applications.

Possible capabilities:

```text
observe screen

click

type

interact with desktop apps
```

Use cases should prefer structured APIs/tools where available.

GUI automation is less reliable and should be used when necessary.

---

# 42. Advanced Browser

Advanced browser capabilities may include:

```text
interactive forms

authenticated sites

downloads

complex navigation

visual verification

local app testing
```

Security and permission boundaries remain mandatory.

---

# 43. Local Models

Advanced Mikasa may support more local execution.

Potential benefits:

```text
privacy

offline capability

lower external dependency

specialized local tasks
```

Model selection remains routed through Model Router.

---

# 44. Controlled Self-Improvement

Only after mature observability and evaluations should Mikasa begin controlled improvement.

Initial form:

```text
OBSERVE REPEATED FAILURE

↓

PROPOSE SKILL CHANGE

↓

TEST CANDIDATE

↓

COMPARE

↓

PROMOTE WITH APPROVAL
```

Not:

```text
randomly rewrite own runtime
```

---

# 45. Adaptive Skills

Over time Mikasa may develop a library of validated workflows.

Example:

```text
project setup

repository repair

release preparation

research comparison

UI verification
```

Skills should become one of the main ways Mikasa improves.

---

# 46. Stage E Completion

Advanced Mikasa is achieved when selected advanced capabilities are:

- Real.
- Integrated.
- Permission-controlled.
- Observable.
- Tested.
- Useful.

The stage does not require every imaginable feature.

---

# 47. Stage F — Long-Term Exploration

## Goal

Explore ambitious ideas after the core platform is mature.

These are directions, not promises or immediate requirements.

---

# 48. Long-Term Areas

Possible areas include:

```text
PROACTIVE ASSISTANCE

MULTI-DEVICE COORDINATION

ADVANCED PERSONALIZATION

LARGE SPECIALIST ECOSYSTEM

DISTRIBUTED WORKERS

ADVANCED LOCAL AI STACK

RICH DESKTOP PRESENCE

3D AVATAR

ADVANCED MULTIMODAL INTERACTION

SIMULATED ENVIRONMENTS

AUTOMATIC CAPABILITY DISCOVERY
```

Each must go through normal research and architecture review.

---

# 49. Proactive Assistance

Future Mikasa may become more proactive within explicitly configured boundaries.

Example:

```text
detect task blocker

surface reminder

suggest useful action

monitor approved condition
```

Proactivity must not become unrestricted goal invention.

---

# 50. Multi-Device Mikasa

A future system may coordinate across:

```text
desktop

mobile

server

other authorized devices
```

This requires:

- Identity.
- Synchronization.
- Permissions.
- Secure transport.
- Conflict handling.

This is intentionally long-term.

---

# 51. Distributed Workers

Large tasks may eventually run across multiple machines.

Potential architecture:

```text
MIKASA CORE

↓

TASK QUEUE

↓

WORKER A
WORKER B
WORKER C
```

This should only be introduced when real workloads justify it.

---

# 52. 3D Avatar / Embodiment

A visual avatar may eventually provide presence and personality.

Potential uses:

- Desktop companion.
- Voice interaction.
- Expression.
- Status communication.

The avatar is an interface layer.

It must not become necessary for core operation.

---

# 53. Cinematic System Experience

Later visual polish may include:

```text
M I K A S A

INITIALIZING...

CORE ONLINE

SYSTEM READY
```

This should remain short and truthful.

Visual style must not replace functional capability.

---

# 54. Advanced Personalization

Possible future personalization:

- Communication style.
- Preferred workflows.
- Project organization.
- Task presentation.
- Notification behavior.

Personalization must remain separate from security permissions.

---

# 55. Capability Discovery

A future mature Mikasa may identify missing capabilities.

Conceptual workflow:

```text
TASK NEEDS CAPABILITY

↓

SEARCH EXISTING TOOLS

↓

NO TOOL FOUND

↓

RESEARCH APPROVED OPTIONS

↓

SECURITY / LICENSE REVIEW

↓

PROPOSE INSTALLATION

↓

USER APPROVAL

↓

INSTALL + TEST

↓

REGISTER
```

No arbitrary autonomous installation.

---

# 56. Long-Term Self-Modification

Direct self-code improvement remains a very late capability.

Required foundation:

```text
stable coding engine

isolated environments

strong eval suite

version control

rollback

security controls

approval system
```

Even then:

```text
PROPOSE → TEST → REVIEW → PROMOTE
```

remains the model.

---

# 57. Roadmap vs. Backlog

Roadmap:

```text
MAJOR PRODUCT DIRECTION
```

Backlog:

```text
SPECIFIC POSSIBLE WORK ITEMS
```

A roadmap item does not automatically authorize implementation.

---

# 58. Roadmap vs. Development Phases

Roadmap answers:

```text
WHERE IS MIKASA GOING?
```

Development Phases answers:

```text
IN WHAT TECHNICAL ORDER DO WE BUILD IT?
```

Both should remain aligned.

---

# 59. Roadmap vs. Current Phase

The roadmap may describe years of possible evolution.

Current Phase defines what may be worked on now.

Never use long-term roadmap items to justify unrelated current work.

---

# 60. Milestone 0 — Architecture Ready

Desired result:

```text
Mikasa repository exists.

Core docs exist.

Foundational ADRs decided.

Project boots.

Tests run.
```

User value:

Foundation only.

---

# 61. Milestone 1 — Mikasa Talks

Desired result:

```text
text input

real model

normalized response

basic session
```

This is not yet autonomous.

---

# 62. Milestone 2 — Mikasa Acts

Desired result:

```text
real tools

filesystem

terminal

permissions

tool calling
```

Mikasa can now affect a controlled workspace.

---

# 63. Milestone 3 — Mikasa Remembers

Desired result:

```text
persistent tasks

persistent basic memory

sessions survive restart
```

Continuity begins.

---

# 64. Milestone 4 — Mikasa Works

Desired result:

```text
planning

bounded autonomy

verification

coding engine
```

This is the first major useful milestone.

---

# 65. Milestone 5 — Mikasa Researches

Desired result:

```text
search

browse

sources

evidence

citations
```

Mikasa becomes useful beyond local project work.

---

# 66. Milestone 6 — Mikasa Learns Workflows

Desired result:

```text
skills

project procedures

reusable verified workflows
```

Repeated work becomes faster and more consistent.

---

# 67. Milestone 7 — Mikasa Persists

Desired result:

```text
checkpoints

background tasks

pause/resume

recovery

scheduling
```

Mikasa becomes genuinely long-running.

---

# 68. Milestone 8 — Mikasa Delegates

Desired result:

```text
specialist agents

structured delegation

multi-agent verification
```

Complex work can be decomposed internally.

---

# 69. Milestone 9 — Mikasa Has a Home

Desired result:

```text
polished graphical UI

task management

memory interface

settings

advanced diagnostics
```

The system becomes comfortable for regular use.

---

# 70. Milestone 10 — Mikasa Speaks

Desired result:

```text
voice input

voice output

task control

interruptions
```

Voice becomes another natural interface.

---

# 71. Milestone 11 — Mikasa Sees & Interacts

Desired result:

```text
advanced browser

screen observation

controlled computer interaction
```

Mikasa can handle workflows without APIs where appropriate.

---

# 72. Milestone 12 — Mikasa Improves

Desired result:

```text
pattern detection

skill proposals

isolated experiments

evaluation

promotion

rollback
```

Learning becomes systematic.

---

# 73. Product Maturity Levels

A simple maturity model:

## Level 0 — Prototype

```text
model responds
```

## Level 1 — Tool Agent

```text
model + controlled tools
```

## Level 2 — Autonomous Core

```text
planning + execution + verification
```

## Level 3 — Persistent Assistant

```text
memory + long-running tasks
```

## Level 4 — Agent Platform

```text
research + coding + browser + specialists
```

## Level 5 — Adaptive Personal AI

```text
voice + computer use + skills + controlled improvement
```

These levels describe product maturity, not intelligence scores.

---

# 74. MVP Roadmap Cut

The MVP ends approximately here:

```text
FOUNDATION

✓

TEXT MODEL

✓

TOOLS

✓

SECURITY

✓

PERSISTENCE

✓

MEMORY

✓

AUTONOMY

✓

CODING

✓

TESTING / OBSERVABILITY

✓
```

This is the first milestone worth protecting aggressively against scope creep.

---

# 75. Post-MVP Priorities

After MVP, likely priority order:

```text
1. reliability

2. research

3. skills

4. better UX

5. long-running tasks

6. multi-agent

7. browser expansion

8. voice

9. computer use

10. self-improvement
```

Actual priorities may change based on real usage.

---

# 76. Reliability Before Breadth

If the MVP reveals:

```text
task failures

poor recovery

bad tool selection

permission problems

persistence corruption
```

the roadmap should pause feature expansion.

Fix the core first.

---

# 77. User Value Before Feature Count

When deciding between two roadmap items, prefer the one that produces more useful real workflows.

Example:

```text
better coding reliability
```

may be more valuable than:

```text
adding five new specialist agents
```

---

# 78. Roadmap Flexibility

This roadmap is not immutable.

It should evolve based on:

- Research.
- User experience.
- Technical constraints.
- Evaluation results.
- New requirements.

Changes to high-level direction should be documented.

---

# 79. When to Change Roadmap

Good reasons:

```text
core architecture requires change

major dependency fails

users strongly need different capability

evaluation reveals unexpected blocker

new technology materially changes feasibility
```

Poor reason:

```text
a new shiny framework appeared this week
```

---

# 80. Roadmap Status Labels

Roadmap items may use:

```text
NOW

NEXT

LATER

EXPERIMENTAL
```

Suggested meaning:

### NOW

Current approved product focus.

### NEXT

Expected after current milestone.

### LATER

Planned direction without active commitment.

### EXPERIMENTAL

Idea requiring significant research.

---

# 81. Initial Roadmap Status

## NOW

```text
Architecture & governance

Project foundation

Agent Runtime

Model layer

Tools

Security

Persistence

Basic memory

Bounded autonomy

Coding Engine

Testing / observability
```

## NEXT

```text
Research Engine

Skills

Graphical UI

Long-running tasks
```

## LATER

```text
Scheduling

Multi-agent specialists

Advanced browser

Voice

Computer use
```

## EXPERIMENTAL

```text
Controlled self-code improvement

Multi-device coordination

Distributed workers

3D avatar

Advanced proactive assistance
```

Statuses should be updated as development progresses.

---

# 82. Roadmap Health Check

Periodically ask:

```text
Is the current milestone still valuable?

Is the core stable enough to continue?

Are we building features before dependencies?

Has scope expanded unintentionally?

Are evaluations improving?

Are deferred items leaking into current work?
```

---

# 83. What Mikasa Must Not Become

The roadmap must continue protecting against:

```text
FEATURE COLLECTION WITHOUT COHERENCE

HUNDREDS OF PERMANENT AGENTS

FAKE AUTONOMY

UNCONTROLLED MACHINE ACCESS

PROVIDER LOCK-IN

DASHBOARD-FIRST DEVELOPMENT

SELF-REWRITING WITHOUT EVALS

GIANT MONOLITHIC "BRAIN" CLASS

UNVERIFIED SUCCESS CLAIMS
```

---

# 84. What Mikasa Should Become

The intended long-term character of the system is:

```text
ONE COHERENT ASSISTANT

MODULAR

PERSISTENT

CAPABLE

VERIFIABLE

EXTENSIBLE

CONTROLLED

PERSONAL

RELIABLE
```

---

# 85. Ultimate Interaction Model

Long-term, a user should be able to say something like:

```text
"Mikasa, inspect the new version of this project,
research the breaking changes, update our code,
run the tests, and prepare a report."
```

Internally Mikasa may:

```text
CREATE TASK

RECALL PROJECT CONTEXT

RESEARCH

PLAN

DELEGATE

EDIT

TEST

RECOVER

VERIFY

SAVE ARTIFACTS

UPDATE RELEVANT MEMORY

REPORT
```

The user still experiences one Mikasa.

---

# 86. Ultimate Architecture Principle

Even as capabilities expand:

```text
ONE ASSISTANT

ONE TASK SYSTEM

ONE PERMISSION MODEL

ONE TOOL ARCHITECTURE

ONE MEMORY ARCHITECTURE

ONE OBSERVABILITY MODEL

ONE GOVERNANCE SYSTEM
```

Providers and specialists remain replaceable components.

---

# 87. Success Criteria for the Project

Mikasa is successful when it can reliably transform:

```text
USER INTENT
```

into:

```text
VERIFIED REAL-WORLD RESULT
```

while preserving:

- User control.
- Security.
- Traceability.
- Recoverability.
- Maintainability.

---

# 88. Roadmap Summary

```text
STAGE A
FOUNDATION
│
├─ architecture
├─ governance
├─ project bootstrap
└─ tests
      |
      v
STAGE B
CORE MVP
│
├─ runtime
├─ model
├─ tools
├─ permissions
├─ persistence
├─ memory
├─ autonomy
└─ coding
      |
      v
STAGE C
USEFUL MIKASA
│
├─ research
├─ skills
├─ better coding
├─ browser
└─ UI
      |
      v
STAGE D
PERSISTENT MIKASA
│
├─ checkpoints
├─ background tasks
├─ recovery
└─ scheduling
      |
      v
STAGE E
ADVANCED MIKASA
│
├─ specialists
├─ voice
├─ computer use
├─ local models
└─ controlled learning
      |
      v
STAGE F
LONG-TERM
│
├─ multi-device
├─ distributed workers
├─ avatar
├─ proactive assistance
└─ advanced self-improvement
```

---

# 89. Final Roadmap Principle

Mikasa should not be built by chasing the final vision directly.

The final vision should emerge from a sequence of working systems.

```text
FOUNDATION
    ↓
CAPABILITY
    ↓
RELIABILITY
    ↓
AUTONOMY
    ↓
PERSISTENCE
    ↓
EXPANSION
    ↓
ADAPTATION
```

The project must preserve these distinctions:

```text
ROADMAP
    !=
CURRENT TASK

VISION
    !=
CURRENT CAPABILITY

FUTURE FEATURE
    !=
IMPLEMENTATION REQUIREMENT

MORE FEATURES
    !=
BETTER AGENT

AMBITION
    !=
RUSH
```

The long-term target may be ambitious.

The development method must remain disciplined.

**Build a Mikasa that works before building a Mikasa that does everything.**