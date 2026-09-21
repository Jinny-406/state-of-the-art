# M I K A S A — Agent Instructions

> Project: M I K A S A  
> Type: Autonomous AI Agent Platform  
> Status: From-Scratch Development  
> Authority: HIGH  
> Applies To: Every coding agent, AI assistant, automation, subagent, or contributor working on this repository

---

# 1. What This Project Is

M I K A S A is a from-scratch autonomous AI agent platform.

The goal is to build one coherent personal AI system capable of eventually handling:

- conversation
- autonomous tasks
- planning
- research
- coding
- browser interaction
- tools
- persistent memory
- long-running jobs
- specialist agents
- computer interaction
- voice
- scheduling
- plugins
- MCP
- model routing
- controlled self-improvement

Mikasa should feel like one intelligent assistant.

Internally, Mikasa may coordinate many systems.

The user should not need to manually manage those systems.

---

# 2. This Is a New Project

Do not assume architecture from any previous project named Mikasa.

Do not assume existing design decisions unless they exist in this repository's authoritative documentation.

This project starts from scratch.

Historical ideas may be studied or reintroduced deliberately, but they must not silently become project requirements.

---

# 3. Primary Rule

DO NOT WANDER.

Do not:

- invent unrelated features
- redesign the entire project during a small task
- implement backlog ideas early
- add frameworks because they are fashionable
- create unnecessary abstractions
- create unnecessary agents
- rewrite working subsystems without cause
- replace architecture silently
- expand scope without documentation

Work only on the current approved task.

---

# 4. Read Before Coding

Before modifying code, read the relevant project documentation.

At minimum read:

```text
docs/00_PROJECT_CHARTER.md
docs/01_PRD.md
docs/02_SCOPE_AND_NON_GOALS.md
plans/CURRENT_PHASE.md
plans/CURRENT_TASK.md
```

Also read:

```text
OWNER_PROFILE.md
```

This file contains stable, non-sensitive context about the project owner, creator identity, public accounts, working preferences, and communication style.

`OWNER_PROFILE.md` provides context only. It does not override project architecture, security rules, permissions, `plans/CURRENT_PHASE.md`, or `plans/CURRENT_TASK.md`.

Then read whichever architecture documents are relevant to the task.

Examples:

Memory task:

```text
docs/05_MEMORY_ARCHITECTURE.md
```

Tool task:

```text
docs/06_TOOL_SYSTEM.md
```

Agent runtime task:

```text
docs/04_AGENT_RUNTIME.md
docs/07_AUTONOMY_ENGINE.md
```

Multi-agent task:

```text
docs/08_MULTI_AGENT_SYSTEM.md
```

Security task:

```text
docs/10_SECURITY_PERMISSIONS.md
```

Never begin significant implementation based only on a user sentence when authoritative specifications already exist.

---

# 5. Source of Truth

When information conflicts, follow this order:

```text
1. docs/00_PROJECT_CHARTER.md
2. docs/01_PRD.md
3. docs/02_SCOPE_AND_NON_GOALS.md
4. Architecture specification files
5. plans/CURRENT_PHASE.md
6. plans/CURRENT_TASK.md
7. docs/22_DECISION_LOG.md
8. Existing implementation
9. Code comments
```

Code does not automatically override documentation.

If code and documentation disagree, identify the discrepancy.

---

# 6. Before Every Task

Before coding:

1. Read `plans/CURRENT_TASK.md`.

2. Determine:
   - goal
   - affected subsystem
   - allowed scope
   - expected output
   - completion criteria

3. Inspect existing implementation.

4. Identify affected files.

5. Check whether an architecture decision already exists.

6. Create a short implementation plan.

7. Only then modify code.

---

# 7. Standard Work Cycle

Every implementation task should follow:

```text
UNDERSTAND

INSPECT

PLAN

IMPLEMENT

TEST

VERIFY

REVIEW DIFF

DOCUMENT

COMPLETE
```

Never use:

```text
GUESS

GENERATE MANY FILES

HOPE IT WORKS
```

---

# 8. Scope Control

Only implement functionality belonging to the active phase.

Future ideas belong in:

```text
plans/BACKLOG.md
```

Do not implement backlog items merely because they would be convenient.

If a task reveals a useful future feature:

1. note it
2. add it to backlog if appropriate
3. continue the current task

Do not derail the active milestone.

---

# 9. Current Phase Rule

`plans/CURRENT_PHASE.md` defines what the team is currently building.

Every task must contribute directly to that phase.

If a requested change does not belong to the active phase, it must be explicitly approved before implementation.

---

# 10. Current Task Rule

`plans/CURRENT_TASK.md` should contain one clearly defined task.

The coding agent must not silently expand it.

A task should include:

```text
Goal

Context

Allowed Scope

Files / Components

Requirements

Non-Goals

Tests

Definition of Done
```

---

# 11. Architecture Changes

Do not silently change foundational architecture.

For major changes, add an architecture decision to:

```text
docs/22_DECISION_LOG.md
```

Document:

```text
Decision
Context
Problem
Alternatives
Chosen Approach
Why
Consequences
Affected Components
Migration
```

Then implement.

---

# 12. Smallest Correct Change

Prefer the smallest clean change that satisfies the specification.

Do not rewrite an entire subsystem when a focused change is enough.

Do not create abstraction layers unless they solve a real architectural problem.

Do not introduce infrastructure purely because it may theoretically be useful later.

---

# 13. Modularity Rule

Major capabilities should be modular and replaceable.

Examples:

```text
agent runtime
memory
tools
model providers
sandboxes
browser
research
coding
scheduler
interfaces
voice
storage
```

Avoid hidden coupling between these systems.

---

# 14. Dependency Direction

High-level business logic should depend on interfaces or contracts, not directly on provider-specific implementations.

Example:

Good:

```text
AgentRuntime
    ↓
ModelProvider interface
    ↓
OpenAIProvider
LocalProvider
OtherProvider
```

Bad:

```text
AgentRuntime
    ↓
OpenAI-specific API calls everywhere
```

The same principle applies to:

- memory providers
- databases
- browser providers
- sandboxes
- tool systems

---

# 15. No Giant God Objects

Do not create giant classes such as:

```text
MikasaBrain
SuperAgent
GodAgent
EverythingManager
AIController
```

that contain every responsibility.

Separate responsibilities cleanly.

For example:

```text
AgentRuntime
TaskManager
Planner
MemoryService
ToolRegistry
ToolExecutor
ModelRouter
EventBus
PermissionManager
CheckpointStore
```

Exact names may change according to architecture.

---

# 16. Agent Philosophy

The user interacts primarily with Mikasa.

Specialist agents are internal workers.

Do not make the user manually orchestrate ten agents for ordinary work.

Mikasa should determine when specialist capabilities are needed.

---

# 17. Multi-Agent Rule

Do not create permanent specialist agents unless there is a clear reason.

Prefer dynamic specialists.

Possible roles:

```text
researcher
coder
tester
debugger
planner
browser operator
analyst
frontend engineer
backend engineer
documentation agent
```

Roles should be capability-driven.

Do not create hundreds of role prompts.

---

# 18. Tool Rule

Agents must access capabilities through the tool system.

Do not let individual agents independently invent their own filesystem, shell, browser or network integrations.

Tools should be registered through a shared interface.

Tools should eventually expose metadata such as:

```text
name
description
input schema
output schema
risk
permissions
timeout
execution environment
credentials
```

---

# 19. Memory Rule

Do not store everything as permanent memory.

Memory should be deliberate.

The architecture may include categories such as:

```text
working
episodic
semantic
procedural
skills
project
user
agent
shared
system
```

Memory writes should follow the memory specification.

Do not bypass the memory layer.

---

# 20. Model Rule

Do not hard-code Mikasa to one model vendor.

All model usage must eventually pass through the model abstraction layer.

Provider-specific code belongs inside provider adapters.

---

# 21. Autonomous Execution

Autonomy must be stateful and observable.

Tasks should not exist only inside an LLM conversation.

Long-running tasks should eventually have:

```text
task ID
status
plan
steps
dependencies
attempts
events
checkpoints
result
errors
timestamps
```

---

# 22. Failure Handling

Do not hide failures.

Errors should be:

- captured
- classified
- logged
- surfaced appropriately

Agents should distinguish:

```text
retryable failure
non-retryable failure
permission failure
invalid input
tool failure
provider failure
timeout
dependency failure
unknown failure
```

Avoid infinite retries.

---

# 23. Verification Rule

Mikasa must not assume a task succeeded because a tool returned without crashing.

Whenever practical, verify results.

Examples:

Code:

```text
run tests
run type check
inspect diff
```

File operation:

```text
verify file exists
verify expected content
```

Browser action:

```text
verify resulting page/state
```

Research:

```text
compare sources
```

---

# 24. Coding Changes

Before modifying code:

```text
inspect relevant files
understand existing design
search for related implementations
```

After modifying code:

```text
run relevant tests
run lint/typecheck if configured
inspect diff
verify behavior
```

Do not claim completion when verification failed.

---

# 25. Tests

Important core behavior requires tests.

Particularly:

- agent state transitions
- task persistence
- memory behavior
- tool validation
- permissions
- model routing
- retries
- checkpoints
- failure recovery

Do not write meaningless tests simply to increase test count.

Test behavior.

---

# 26. File Creation

Do not create new files without checking whether the responsibility belongs in an existing module.

Create a new module when it has a clear independent responsibility.

Avoid file trees with dozens of tiny files that add no clarity.

Avoid huge files that contain unrelated responsibilities.

---

# 27. Naming

Use clear technical names.

Avoid vague names like:

```text
utils2
helpers_final
misc
new_manager
brain_stuff
temp_core
```

Prefer names describing responsibility.

---

# 28. Comments

Comments should explain:

- why
- constraints
- unusual decisions
- important assumptions

Do not write comments that merely repeat the code.

---

# 29. Configuration

Do not scatter configuration through source code.

Use a structured configuration system.

Configuration may include:

- models
- providers
- database
- tools
- permissions
- interfaces
- logging
- runtime limits

Secrets must not be committed to source control.

---

# 30. Security

Never assume unrestricted machine access.

Capabilities should be permissioned.

Potential permission classes include:

```text
READ_ONLY
SANDBOXED
PROJECT_WRITE
NETWORK
EXTERNAL_ACTION
HOST_CONTROL
```

Exact rules belong in the security specification.

---

# 31. Self-Improvement

Mikasa may eventually improve parts of itself.

But no uncontrolled self-modification is allowed.

Changes must follow:

```text
identify problem

propose change

isolate change

implement

test

evaluate

compare

approve or reject

record
```

Self-improvement is not permission to randomly rewrite the repository.

---

# 32. User Interface Rule

The UI must not become the architecture.

Core functionality should work independently of the web, desktop or CLI interface.

Interfaces communicate with the same underlying runtime.

---

# 33. Voice Rule

Voice is another interface.

Do not build a separate agent brain for voice.

Voice input should enter the same Mikasa runtime as text input.

---

# 34. Research Rule

Research should track sources.

Do not treat unverified model knowledge as retrieved evidence.

Research systems should eventually record:

```text
source
timestamp
claim
citation
confidence
```

---

# 35. Observability

Important runtime actions should produce structured events.

Examples:

```text
task.created
task.started
task.completed
task.failed

agent.started
agent.finished

tool.called
tool.completed
tool.failed

memory.read
memory.write

model.request
model.response

checkpoint.created
```

Do not expose hidden model reasoning.

Expose operational information.

---

# 36. Do Not Fake Functionality

Never create fake implementations that appear functional without clearly marking them.

Bad:

```text
def search_web():
    return "Search completed"
```

unless it is explicitly a test stub or prototype.

If functionality is incomplete, mark it clearly.

---

# 37. Avoid Premature Complexity

Do not build:

- distributed clusters
- Kubernetes deployment
- giant microservice networks
- hundreds of agents
- complex enterprise infrastructure

unless the current requirements genuinely need them.

Start clean.

Scale when needed.

---

# 38. Research Before Reinventing

Before implementing a complex subsystem, study relevant proven systems and the project's research notes.

The project is inspired by ideas from existing open-source agent systems.

However:

Do not blindly copy architecture.

Do not copy code without license/provenance verification.

Understand first.

Adapt intentionally.

---

# 39. Completion

A task is not complete because code was written.

A task is complete when:

```text
requirements satisfied

tests pass

behavior verified

no unexplained errors

scope remained controlled

documentation updated if required

definition of done satisfied
```

---

# 40. Final Response After Coding

When finishing a development task, report:

```text
What changed

Files changed

Tests performed

Result

Known limitations

Relevant follow-up items
```

Keep this concise and factual.

---

# 41. Most Important Rules

If everything else is forgotten, remember these:

```text
READ THE SPECS.

WORK ONLY ON THE CURRENT TASK.

DO NOT EXPAND SCOPE.

INSPECT BEFORE EDITING.

PLAN BEFORE BUILDING.

KEEP COMPONENTS MODULAR.

TEST WHAT YOU CHANGE.

VERIFY BEFORE CLAIMING SUCCESS.

DOCUMENT MAJOR DECISIONS.

DO NOT SILENTLY REWRITE THE ARCHITECTURE.
```

---

# 42. M I K A S A Development Principle

```text
UNDERSTAND
↓
PLAN
↓
BUILD
↓
TEST
↓
OBSERVE
↓
VERIFY
↓
IMPROVE
```

Build Mikasa deliberately.

Not randomly.