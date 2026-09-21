# Project Charter

> Status: AUTHORITATIVE  
> Change Frequency: LOW  
> Priority: HIGHEST  
> Applies To: Entire Project

---

# 1. Purpose

This project is a new autonomous AI agent platform built from scratch.

The goal is to create a persistent personal AI system inspired by the general experience of fictional assistants such as JARVIS, while using practical modern agent architecture.

This is not intended to be:

- a chatbot with many tools attached
- a clone of another AI-agent repository
- a collection of unrelated scripts
- a giant prompt pretending to be an autonomous system
- a demo that only works for one predefined workflow

The system must be designed as a real software platform with independent, replaceable subsystems.

---

# 2. Core Vision

The final system should behave like one persistent intelligent assistant capable of understanding goals, planning work, using tools, delegating tasks, remembering useful information, recovering from failure, and continuing long-running tasks.

From the user's perspective, there should primarily be ONE assistant.

Internally, that assistant may use:

- specialist agents
- tools
- models
- memory systems
- sandboxes
- browsers
- coding environments
- schedulers
- external services

The complexity should remain behind the main assistant.

---

# 3. Product Philosophy

The system should feel like:

Personal AI Assistant

+

Autonomous Task Agent

+

Research Agent

+

Coding Agent

+

Computer-Use Agent

+

Long-Running Worker

+

Multi-Agent Runtime

+

Memory System

+

AI Operating Layer

The project should not optimize for looking impressive before the underlying system works.

Functionality, reliability, observability, modularity and recoverability come before visual polish.

---

# 4. Fundamental Architecture Principle

The project MUST be modular.

Major capabilities must be independent subsystems rather than tightly coupled code.

Conceptually:

```text
USER
  │
  ▼
INTERFACE LAYER
  │
  ▼
AGENT GATEWAY
  │
  ▼
MAIN AGENT
  │
  ├── MEMORY
  ├── PLANNER
  ├── TASK RUNTIME
  ├── MODEL ROUTER
  ├── SPECIALIST AGENTS
  └── TOOL RUNTIME
          │
          ▼
       SANDBOX
          │
          ├── FILESYSTEM
          ├── TERMINAL
          ├── BROWSER
          ├── CODE
          ├── EXTERNAL APIS
          └── COMPUTER CONTROL
```

Cross-cutting infrastructure includes:

```text
EVENT BUS
STATE STORAGE
SCHEDULER
PERMISSIONS
SECRETS
LOGGING
OBSERVABILITY
CHECKPOINTS
EVALUATION
```

The exact architecture may evolve through documented architecture decisions.

The overall modular principle may not be discarded.

---

# 5. Main Agent Principle

The user interacts primarily with one main agent.

The main agent is responsible for:

- understanding user goals
- retrieving relevant context
- creating or selecting plans
- determining necessary capabilities
- assigning work
- coordinating specialist agents
- evaluating results
- communicating with the user
- maintaining task state

Specialist agents are implementation details.

The user should not normally need to manually manage a collection of agents.

---

# 6. Autonomy Principle

Autonomy means the ability to continue meaningful work without requiring user interaction at every step.

It does NOT mean uncontrolled execution.

The autonomous execution cycle should conceptually follow:

```text
RECEIVE GOAL

UNDERSTAND GOAL

RETRIEVE RELEVANT MEMORY

CREATE / UPDATE PLAN

IDENTIFY REQUIRED CAPABILITIES

EXECUTE ACTION

OBSERVE RESULT

VERIFY RESULT

IF FAILURE:
    diagnose
    retry or replan

IF MORE WORK:
    continue

IF COMPLETE:
    validate outcome
    summarize
    persist appropriate state
```

The runtime must eventually support:

- retries
- replanning
- task dependencies
- parallel work
- sequential work
- cancellation
- pause
- resume
- checkpoints
- recovery
- deadlines
- resource limits

---

# 7. Memory Principle

Memory is a first-class subsystem.

Memory must not simply mean storing entire conversations in a vector database.

The memory architecture should eventually distinguish categories such as:

```text
WORKING MEMORY
EPISODIC MEMORY
SEMANTIC MEMORY
PROCEDURAL MEMORY
SKILL MEMORY
PROJECT MEMORY
USER MEMORY
AGENT MEMORY
SHARED MEMORY
SYSTEM MEMORY
```

Memory entries should be capable of containing metadata such as:

- origin
- timestamp
- confidence
- importance
- scope
- owner
- namespace
- relationships
- version
- access permissions

The system should support:

- retrieval
- consolidation
- correction
- updating
- deduplication
- summarization
- expiration
- deletion
- versioning
- rollback

Memory architecture is defined separately in:

`docs/05_MEMORY_ARCHITECTURE.md`

---

# 8. Tool Principle

Tools must not be hard-coded directly into the main agent.

There must be a tool abstraction.

A tool should describe things such as:

```text
name
description
input schema
output schema
permissions
risk level
timeout
required credentials
execution environment
capabilities
```

The project should eventually support:

- native tools
- MCP tools
- plugins
- external integrations

Tools may include:

- filesystem
- terminal
- browser
- web search
- APIs
- git
- code execution
- databases
- messaging
- applications
- computer control

---

# 9. Model Independence

The system must not depend permanently on one model or provider.

All model access must eventually pass through a model abstraction layer.

The system should support combinations of:

- cloud models
- local models
- OpenAI-compatible APIs
- specialized models

Different models may be selected for different tasks.

Examples:

```text
FAST MODEL
GENERAL MODEL
REASONING MODEL
CODING MODEL
VISION MODEL
LOCAL MODEL
```

The agent runtime should not require major architectural changes when the underlying model provider changes.

---

# 10. Long-Running Tasks

The system must eventually support work that lasts longer than one request/response interaction.

Long-running work should use persistent task state.

Tasks should eventually support states such as:

```text
CREATED
QUEUED
PLANNING
RUNNING
WAITING
PAUSED
RETRYING
BLOCKED
FAILED
CANCELLED
COMPLETED
```

Tasks must have stable IDs.

Task progress should survive application restarts where practical.

---

# 11. Multi-Agent Philosophy

Multi-agent behavior should be capability-driven.

Do NOT create dozens or hundreds of permanently running agents.

The main system should instantiate or select specialists when their capabilities are useful.

Possible specialists include:

- researcher
- coder
- tester
- debugger
- browser operator
- planner
- analyst
- frontend engineer
- backend engineer
- documentation agent

Specialists should communicate through structured task contracts rather than uncontrolled conversations whenever possible.

---

# 12. Research Philosophy

The system should eventually contain a dedicated research capability.

Research should support:

- query planning
- web search
- browsing
- information extraction
- multiple sources
- source tracking
- citation handling
- contradiction detection
- confidence assessment
- research memory

Research results should distinguish:

```text
FACT
INFERENCE
ASSUMPTION
OPINION
UNKNOWN
```

where appropriate.

---

# 13. Coding Philosophy

The coding subsystem should eventually be capable of:

- understanding repositories
- searching files
- reading code
- editing code
- creating files
- deleting files safely
- using terminals
- installing dependencies
- running tests
- linting
- type checking
- debugging
- running development servers
- inspecting git diffs
- creating branches
- using checkpoints
- rolling back changes

Coding changes should be verifiable.

The coding agent must prefer:

```text
inspect
→ understand
→ plan
→ modify
→ test
→ inspect diff
→ verify
```

over:

```text
guess
→ rewrite everything
```

---

# 14. Computer Control

Computer control is a future capability.

It must not be tightly coupled with the agent core.

Computer interaction should be exposed through controlled tools or capabilities.

Possible future abilities include:

- screenshot understanding
- UI element detection
- mouse interaction
- keyboard interaction
- application control

High-impact external actions must support permission controls.

---

# 15. Voice

Voice is an interface to the agent, not a separate intelligence.

The same main agent should power:

- text
- CLI
- web
- desktop
- voice

Future voice components may include:

- wake word
- voice activity detection
- speech-to-text
- streaming responses
- text-to-speech
- interruption handling

---

# 16. User Experience

The system should eventually provide a simple primary interface.

Do not build an overwhelming mission-control dashboard as the default UI.

The main interface should prioritize:

- conversation
- current task
- progress
- notifications
- important controls

Advanced debugging information may exist in separate developer views.

---

# 17. Startup Experience

The project may eventually include a cinematic startup sequence inspired by futuristic terminal interfaces.

Possible startup stages:

```text
INITIALIZING CORE

LOADING CONFIGURATION

CONNECTING MODEL ROUTER

RESTORING MEMORY

REGISTERING TOOLS

CHECKING SANDBOX

RESTORING TASKS

INITIALIZING AGENT RUNTIME

STARTING INTERFACES

SYSTEM ONLINE
```

This is a UI experience only.

The actual architecture must not depend on decorative startup animations.

---

# 18. Self-Improvement Principle

The system may eventually improve its:

- prompts
- workflows
- tool selection
- routing
- skills
- memory strategies
- code

However, uncontrolled self-modification is forbidden.

Improvement should follow a controlled pipeline:

```text
OBSERVE PROBLEM

IDENTIFY REPEATED FAILURE

PROPOSE CHANGE

CREATE ISOLATED ENVIRONMENT

IMPLEMENT CHANGE

TEST CHANGE

EVALUATE CHANGE

COMPARE BEFORE / AFTER

ACCEPT OR REJECT

RECORD DECISION
```

Changes must be traceable and reversible.

---

# 19. Security Philosophy

No subsystem should automatically receive unrestricted machine access.

Use capability-based permissions.

Example permission levels may eventually include:

```text
READ_ONLY

SANDBOXED

PROJECT_WRITE

NETWORK_ACCESS

EXTERNAL_ACTIONS

HOST_CONTROL
```

The exact security model is defined separately.

---

# 20. Observability Principle

Autonomous systems must be inspectable.

The project should eventually expose operational information including:

- current goal
- current plan
- task status
- agent status
- tool execution
- errors
- retries
- model usage
- resource usage
- memory operations
- events

Do not attempt to expose hidden internal model reasoning.

Expose useful operational state instead.

---

# 21. Engineering Principles

All implementation must prefer:

- modular components
- typed interfaces
- explicit dependencies
- small focused modules
- clear boundaries
- testability
- replaceable providers
- structured errors
- auditable actions
- predictable state transitions
- versioned schemas where appropriate

Avoid:

- giant files
- circular dependencies
- global mutable state
- hidden side effects
- tightly coupled UI/backend code
- provider-specific business logic
- duplicated infrastructure
- prompt-only architecture

---

# 22. Build Philosophy

The project WILL NOT be built all at once.

Development must occur in phases.

The standard sequence is:

```text
RESEARCH

SPECIFICATION

FOUNDATION

CORE RUNTIME

MODEL LAYER

TOOLS

MEMORY

PLANNING

CODING

RESEARCH

PERSISTENT TASKS

MULTI-AGENT

INTERFACE

VOICE

COMPUTER USE

CONTROLLED IMPROVEMENT

PRODUCTION HARDENING
```

The exact phase plan is maintained in:

`docs/19_DEVELOPMENT_PHASES.md`

---

# 23. Scope Control

Ideas that are not part of the active development phase must NOT be implemented merely because they sound useful.

They belong in:

`plans/BACKLOG.md`

Only work listed in:

`plans/CURRENT_PHASE.md`

and subsequently approved in:

`plans/CURRENT_TASK.md`

may be implemented.

This rule exists specifically to prevent scope drift.

---

# 24. Change Control

Major architecture changes require a documented decision.

Before changing a foundational design, create an entry in:

`docs/22_DECISION_LOG.md`

The entry should contain:

```text
Decision
Context
Alternatives
Chosen Approach
Reason
Consequences
Affected Components
Migration Required
```

Do not silently replace architecture during unrelated tasks.

---

# 25. Source of Truth Priority

When project documents conflict, use the following priority:

```text
1. docs/00_PROJECT_CHARTER.md
2. docs/01_PRD.md
3. docs/02_SCOPE_AND_NON_GOALS.md
4. architecture specifications
5. plans/CURRENT_PHASE.md
6. plans/CURRENT_TASK.md
7. implementation
8. comments
```

Implementation does not automatically become the specification.

If implementation and specification disagree, investigate the discrepancy.

---

# 26. Core Rule

Never solve a future problem by destroying the architecture needed for the long-term system.

At the same time:

Never over-engineer the current milestone merely because a capability may exist someday.

Build the smallest clean foundation that supports the next stages.

---

# 27. Ultimate Goal

The goal is a personal autonomous AI platform that feels coherent.

Not a pile of features.

Not hundreds of agents.

Not thousands of prompts.

Not one enormous framework.

One assistant.

One coordinated runtime.

Many replaceable capabilities behind it.

The system should become more capable over time without requiring the foundation to be repeatedly rewritten.

---

# 28. Project Motto

```text
UNDERSTAND
PLAN
ACT
OBSERVE
VERIFY
LEARN
CONTINUE
```