# M I K A S A
## Scope, Boundaries & Non-Goals

**File:** `docs/02_SCOPE_AND_NON_GOALS.md`

**Version:** 0.1.0

**Status:** DRAFT — Pending approval

**Authority:** Product scope and implementation boundaries

**Applies to:** All coding agents, contributors, subagents, and development workflows

---

# 1. Purpose

This document defines the scope of M I K A S A.

Its purpose is to prevent:

- Uncontrolled feature expansion.
- Premature implementation of future capabilities.
- Unnecessary architecture changes.
- Unrelated modifications during active tasks.
- Overengineering.
- Duplicate subsystems.
- Unapproved dependencies.
- Accidental replacement of existing functionality.
- Confusion between product vision and current implementation requirements.

Mikasa is intended to become a highly capable autonomous AI platform.

However, the existence of a long-term capability in the Product Requirements Document does not authorize its immediate implementation.

Development must follow the approved phases.

---

# 2. Three Levels of Scope

Every proposed feature or change must belong to one of three categories.

## LEVEL 1 — Product Scope

Features that belong to the long-term vision of Mikasa.

Examples:

- Autonomous task execution.
- Organized persistent memory.
- Research.
- Coding.
- Browser interaction.
- Voice communication.
- Computer use.
- Specialist agents.
- Long-running tasks.
- Controlled self-improvement.

These capabilities may be researched and planned.

They are not automatically approved for implementation.

## LEVEL 2 — Phase Scope

Features explicitly approved for the active development phase.

The authoritative phase document is:

`plans/CURRENT_PHASE.md`

The coding agent may implement features within the active phase only when an approved task authorizes the work.

## LEVEL 3 — Task Scope

The exact work approved for the current task.

The authoritative task document is:

`plans/CURRENT_TASK.md`

The task scope defines:

- What must be implemented.
- Which components may be modified.
- What functionality is required.
- What is explicitly excluded.
- Which tests must pass.
- What constitutes completion.

The coding agent must not expand beyond this scope without authorization.

---

# 3. Scope Hierarchy

```text
LONG-TERM PRODUCT VISION
          │
          ▼
      APPROVED PHASE
          │
          ▼
      APPROVED TASK
          │
          ▼
     IMPLEMENTATION
          │
          ▼
       VERIFICATION
```

A task must satisfy the current phase.

The current phase must contribute to the product requirements.

The implementation must satisfy the approved task.

An idea in the long-term vision is not sufficient authorization to modify the codebase.

---

# 4. Initial Product Scope

The first working version of Mikasa must establish a functioning autonomous agent.

The initial scope includes:

- Text-based interaction.
- A working agent execution loop.
- A configurable model provider.
- Model-provider abstraction.
- Structured tool registration.
- Controlled filesystem tools.
- Controlled terminal execution.
- Basic multi-step task planning.
- Initial persistent memory.
- Session and task records.
- Task cancellation.
- Permission enforcement.
- Error handling.
- Basic execution logging.
- Outcome verification.
- Automated tests for core functionality.

The first version must demonstrate that these capabilities work together as one system.

A graphical interface alone does not satisfy the initial product scope.

---

# 5. Research Scope

Before committing to major architecture decisions, the project must research the repositories identified in the initial research requirements.

Research must examine:

- Agent runtime architecture.
- Planning strategies.
- Tool execution.
- Memory systems.
- Model integration.
- Multi-agent coordination.
- Task persistence.
- Failure recovery.
- Sandboxing.
- Permission management.
- Extensibility.
- Licensing.
- Maintenance and integration complexity.

Research may produce:

- Comparison reports.
- Architectural recommendations.
- Dependency evaluations.
- Proof-of-concept experiments.
- Architecture decision proposals.

Research does not automatically authorize adopting the code, dependencies, or architecture of another project.

Any external code reuse must have verified provenance and compatible licensing.

---

# 6. MVP In-Scope Capabilities

## 6.1 Core Runtime

The MVP must provide an actual agent execution loop.

The runtime must support:

- Receiving user instructions.
- Maintaining relevant task context.
- Requesting model responses.
- Processing supported tool calls.
- Returning tool results to the agent.
- Continuing execution within configured limits.
- Producing structured final results.
- Handling errors.
- Supporting cancellation.

The runtime must not be implemented as a collection of unrelated scripts.

---

## 6.2 Model Integration

The MVP must support at least one working model provider.

The implementation must use an abstraction that allows additional providers to be introduced later.

Advanced model selection, automatic provider discovery, and cost optimization are not required for the MVP.

---

## 6.3 Tool System

The MVP must contain a functional tool system.

Initial tools should support:

- Listing authorized directories.
- Reading authorized files.
- Searching project files.
- Creating authorized files.
- Modifying authorized files.
- Executing permitted terminal commands.

Every tool must use the approved tool interface and permission model.

---

## 6.4 Basic Planning

The MVP must support simple multi-step task execution.

A task may be broken into steps when necessary.

The agent must be able to use information obtained during one step to decide what to do next.

Advanced hierarchical planning, multi-agent planning, and distributed execution are not required initially.

---

## 6.5 Initial Memory

The MVP must support:

- Active conversation context.
- Persistent session records.
- Selected persistent information.
- Retrieval of stored information.
- Basic memory lifecycle controls.

Advanced memory graphs, automated skill learning, and multi-agent memory synchronization are not required for the initial version.

The architecture must leave room for them.

---

## 6.6 Basic Task Management

The MVP must track task identity and execution status.

It must support:

- Task creation.
- Task execution.
- Task completion.
- Task failure.
- Task cancellation.
- Basic persistent task records.

Automatic recovery of arbitrary interrupted workflows is a later milestone.

---

## 6.7 Verification

The MVP must be able to verify supported actions.

Examples:

- Check whether a file was created.
- Inspect the result of a file modification.
- Check a terminal command's exit status.
- Execute a project's available tests.
- Report errors when verification fails.

The system must not claim success solely because an action was attempted.

---

# 7. Features Explicitly Outside MVP Scope

The following capabilities are part of Mikasa's possible future development but are not required in the first working version.

| Capability | Initial scope |
|---|---|
| Voice assistant | Future phase |
| Animated avatar | Future phase |
| Cinematic startup screen | Future phase |
| Full desktop control | Future phase |
| Advanced browser automation | Future phase |
| Large-scale multi-agent orchestration | Future phase |
| Autonomous specialist creation | Future phase |
| Distributed execution | Future phase |
| Advanced memory graphs | Future phase |
| Automated skill evolution | Future phase |
| Autonomous modification of Mikasa's own code | Future phase |
| Multi-device synchronization | Future phase |
| Large-scale plugin ecosystem | Future phase |
| Advanced model routing | Future phase |
| Enterprise deployment | Outside current product scope |

These features may be documented and researched without being implemented.

Their implementation requires an approved phase and task.

---

# 8. Architectural Preparation vs. Premature Implementation

Mikasa must be designed with future expansion in mind.

However, preparing for a future capability does not mean implementing that capability immediately.

**Example: Voice**

Allowed during initial architecture:

Define how a future voice interface would communicate with the main agent runtime.

Not allowed during MVP implementation:

Build a complete speech engine, wake-word system, streaming voice pipeline, and animated avatar without an approved task.

**Example: Multi-agent orchestration**

Allowed:

Design task contracts that could later support delegation.

Not allowed:

Create dozens of specialist agents before the core single-agent runtime works.

**Example: Memory**

Allowed:

Define a memory-provider interface and basic memory categories.

Not allowed:

Introduce multiple memory databases, a knowledge graph, and distributed synchronization before basic persistence is reliable.

The guiding principle is:

Build the simplest functional implementation that preserves necessary extension points.

---

# 9. Explicit Product Non-Goals

The following are not objectives of the current product.

## NG-001 — Creating a clone

Mikasa is not intended to be a renamed copy of another AI-agent project.

Existing systems may provide architectural inspiration or approved dependencies.

## NG-002 — Building artificial general intelligence

The project must not claim to have achieved artificial general intelligence.

Its capabilities must be described according to what has actually been implemented and evaluated.

## NG-003 — Building every feature immediately

The project must not attempt to implement its entire long-term roadmap in one development phase.

## NG-004 — Creating hundreds of permanent agents

The number of agents is not a measure of product quality.

Specialists must serve identifiable purposes.

## NG-005 — Unrestricted computer control

Unrestricted host access is not a default product requirement.

Computer interaction must follow defined authorization and execution boundaries.

## NG-006 — Uncontrolled self-modification

Mikasa must not silently rewrite its own runtime or bypass review and evaluation requirements.

## NG-007 — Permanent storage of everything

Mikasa must not treat every conversation, tool result, and temporary observation as permanent memory.

## NG-008 — Dependence on a single model provider

The architecture must not permanently require one proprietary or open-source model.

## NG-009 — Dependence on one interface

Mikasa's core functionality must not depend on a particular web, desktop, CLI, or voice interface.

## NG-010 — Enterprise infrastructure before demonstrated need

Large-scale distributed systems, organizational tenancy, and enterprise administration are not required for the initial product.

---

# 10. Task Boundary Rules

Before beginning a task, the coding agent must identify:

1. The requested outcome.
2. The active development phase.
3. The approved task scope.
4. Relevant specifications.
5. Affected modules.
6. Required tests.
7. Explicit exclusions.

The agent must then produce a focused implementation plan.

If additional work is discovered during implementation, classify it as:

- Required to complete the approved task.
- A blocking dependency.
- A related but optional improvement.
- An unrelated improvement.
- A future capability.

Only work genuinely required for the approved task may be included automatically.

Optional improvements and future capabilities belong in the backlog.

---

# 11. Allowed Incidental Changes

A coding agent may make a small incidental change when all the following conditions are satisfied:

- It is necessary to complete the approved task.
- It directly affects the same subsystem.
- It does not introduce new product functionality.
- It does not alter foundational architecture.
- It does not weaken security or permission boundaries.
- It can be verified alongside the original task.

Example:

A memory feature requires correcting a small interface mismatch in the memory adapter.

That correction may be included.

However, the agent must not use a memory task as justification to replace the entire database layer.

---

# 12. Scope Expansion Rules

If an implementation requires work outside the approved scope, the agent must:

1. Identify the additional requirement.
2. Explain why it is necessary.
3. Determine whether a simpler solution exists.
4. Evaluate its impact on other components.
5. Record it in the backlog or task blocker.
6. Obtain approval before expanding scope.

The agent must not silently redefine the task.

If the user explicitly authorizes the expanded work, the relevant task and phase documentation must be updated.

---

# 13. Architecture Change Boundaries

The coding agent must not introduce major architecture changes during ordinary feature development.

Major changes include:

- Replacing the agent runtime.
- Replacing the memory architecture.
- Changing the primary persistence strategy.
- Replacing the tool execution system.
- Introducing a new orchestration framework.
- Splitting the application into microservices.
- Changing the primary programming language.
- Introducing a new system-wide dependency.
- Changing core permission boundaries.

Such changes require an architecture decision.

The decision must be recorded in:

`docs/22_DECISION_LOG.md`

Approval must occur before implementation.

---

# 14. Dependency Boundaries

Do not introduce dependencies simply because they appear useful.

Before adding a significant dependency, evaluate:

- Whether it solves a current requirement.
- Whether equivalent functionality already exists.
- Its maintenance status.
- Its license.
- Its security implications.
- Its resource requirements.
- Its compatibility with the existing architecture.
- Its long-term maintenance cost.

For experimental dependencies, prefer isolated proof-of-concept branches or environments.

Do not permanently integrate a dependency solely because a research repository uses it.

---

# 15. File Modification Boundaries

The coding agent must inspect relevant existing files before editing them.

The agent must not:

- Rewrite unrelated modules.
- Rename files unnecessarily.
- Reorganize the entire repository during a focused task.
- Delete existing functionality without authorization.
- Replace working implementations with unverified alternatives.
- Modify secrets or credentials outside the approved task.
- Change deployment configuration without a clear requirement.

Changes must remain focused on the approved objective.

---

# 16. Prototype Boundaries

Prototypes are allowed when they answer a specific technical question.

Examples:

- Testing a model-provider interface.
- Evaluating memory retrieval.
- Comparing execution sandbox approaches.
- Testing a tool protocol.
- Demonstrating a basic agent loop.

A prototype must have:

- A clearly stated objective.
- A limited scope.
- Defined success criteria.
- An isolated implementation area.
- A documented conclusion.

A prototype must not automatically become production architecture.

Prototype code may be discarded when its research purpose is complete.

---

# 17. No Fake Completion

The coding agent must not mark a feature complete when its actual behavior is missing.

Examples of unacceptable completion claims:

- A model router exists but supports no functioning providers.
- A memory interface exists but does not persist information.
- A task manager exists but only stores tasks in temporary process memory when persistence is required.
- A browser tool returns hard-coded results.
- A coding agent creates files but cannot verify them.
- A user interface displays fake agent activity.
- A test suite passes because meaningful assertions were removed.

Mocks and stubs are permitted for testing and clearly identified prototypes.

They must not be presented as completed functionality.

---

# 18. Research Boundary

Research and implementation are separate activities.

During a research-only task, the agent may:

- Read repositories.
- Examine source code.
- Inspect documentation.
- Compare implementations.
- Evaluate licenses.
- Produce architecture reports.
- Run approved isolated experiments.

It must not modify Mikasa's production code unless implementation is explicitly part of the task.

Research conclusions must distinguish:

- Verified source-code behavior.
- Documented project claims.
- Architectural inference.
- Unverified assumptions.

---

# 19. Security Boundaries

Mikasa's development and runtime must respect explicit resource boundaries.

Examples include:

- Authorized workspace paths.
- Permitted terminal operations.
- Configured network access.
- Credential access.
- External service permissions.
- Computer-control permissions.

The agent must not bypass permission checks to complete a task.

A permission failure is not authorization to find an alternative route around the restriction.

Sensitive or destructive operations require the appropriate authorization.

---

# 20. Memory Boundaries

Memory must not be used as an unrestricted information store.

The agent must not:

- Save credentials as ordinary memory.
- Permanently store every conversation by default.
- Treat unverified claims as confirmed facts.
- Share private information with unrelated specialist agents.
- Convert temporary task instructions into permanent user preferences without justification.
- Ignore explicit memory deletion or correction requests.

Memory behavior must follow:

`docs/05_MEMORY_ARCHITECTURE.md`

---

# 21. Self-Improvement Boundaries

Future self-improvement features must not grant Mikasa unrestricted authority to modify itself.

Self-improvement must use:

- Isolated development environments.
- Explicit change proposals.
- Defined evaluation criteria.
- Appropriate tests.
- Permission checks.
- Traceable version history.
- Approval before deployment.
- Rollback procedures.

The existence of a self-improvement subsystem does not authorize modifications outside the current task.

---

# 22. Current Phase Enforcement

The active phase is defined in:

`plans/CURRENT_PHASE.md`

Before starting any implementation, the coding agent must determine whether the task belongs to that phase.

If it does, continue according to the approved task.

If it does not, do not begin implementation without explicit authorization.

The user may approve a change to the active phase.

When that happens, update the phase documentation before proceeding.

---

# 23. Current Task Enforcement

The active task is defined in:

`plans/CURRENT_TASK.md`

There should be one primary active implementation task for a given coding-agent execution.

The task must identify:

- Goal.
- Requirements.
- Allowed scope.
- Relevant files.
- Exclusions.
- Dependencies.
- Acceptance criteria.
- Verification requirements.

The agent must not treat unrelated conversation content as permission to expand the current task.

A new explicit user instruction can supersede the current task.

When this occurs, update the relevant task documentation so the repository remains consistent.

---

# 24. Backlog Rules

Future ideas belong in:

`plans/BACKLOG.md`

Backlog entries should include:

- Feature name.
- Short description.
- Reason for inclusion.
- Relevant subsystem.
- Dependencies.
- Proposed milestone.
- Status.

Suggested statuses:

```text
IDEA

RESEARCH_NEEDED

PROPOSED

APPROVED

DEFERRED

REJECTED

IMPLEMENTED
```

A backlog entry does not authorize implementation.

Implementation requires promotion into an approved phase and task.

---

# 25. Definition of Done

A task must satisfy the criteria defined in:

`docs/20_DEFINITION_OF_DONE.md`

At minimum, completion requires:

- Approved requirements satisfied.
- Relevant tests executed.
- Results verified.
- No unexplained critical failures.
- No unauthorized scope expansion.
- Relevant documentation updated.
- Known limitations recorded.

The coding agent must not claim completion when required verification has not been performed.

---

# 26. Conflict Resolution

If requirements conflict, the agent must identify the conflict rather than inventing an interpretation that silently changes the project.

Examples:

- A task requires behavior prohibited by the security specification.
- A phase document permits a feature excluded by the PRD.
- An architecture document requires a dependency rejected by a later approved decision.
- An existing implementation behaves differently from the authoritative specification.

The agent must determine whether the conflict can be resolved within the documented source-of-truth hierarchy.

If not, the affected decision requires explicit resolution before implementation.

A newer explicit user decision may supersede an older project requirement, but the affected documents must be updated consistently.

---

# 27. Completion and Phase Advancement

Completing one task does not automatically authorize the next task.

Completing one phase does not automatically authorize implementation of the next phase.

The coding agent must:

1. Verify the current task.
2. Record its outcome.
3. Confirm the phase acceptance criteria.
4. Identify remaining blockers.
5. Report readiness for the next milestone.

The user controls major phase transitions.

The agent may recommend the next logical task but must not silently begin unrelated implementation.

---

# 28. Anti-Wandering Checklist

Before making any significant change, the coding agent must answer:

```text
[ ] Does this change directly support the current task?

[ ] Is the change permitted by the active phase?

[ ] Have I read the relevant specifications?

[ ] Have I inspected the existing implementation?

[ ] Am I solving a current problem rather than a hypothetical one?

[ ] Am I avoiding unrelated changes?

[ ] Does the change preserve approved architecture?

[ ] Have I checked relevant security boundaries?

[ ] Can the result be tested or verified?

[ ] Does the change avoid unnecessary dependencies?

[ ] Am I documenting important decisions?

[ ] Can I explain why every modified file is necessary?
```

If any answer reveals a scope violation, resolve it before implementation.

---

# 29. Example: Correct Scope Handling

**Current task:**

Implement persistent task records.

**Allowed work:**

- Define the task storage interface.
- Implement the approved storage adapter.
- Persist task IDs and statuses.
- Add necessary database migrations.
- Add relevant tests.
- Update task persistence documentation.

**Not allowed without separate approval:**

- Replace the entire memory system.
- Introduce a distributed task queue.
- Build a graphical task dashboard.
- Implement autonomous multi-agent scheduling.
- Add voice notifications.
- Rewrite the agent runtime.

The coding agent must complete the requested persistence work before proposing optional expansions.

---

# 30. Example: Handling a New Idea

During implementation, the agent discovers that a graphical dashboard could make task monitoring easier.

The dashboard is not part of the current task.

Correct behavior:

```text
1. Record the idea in the backlog.

2. Explain the potential benefit briefly.

3. Continue the current task.

4. Do not implement the dashboard.

5. Revisit the idea when its phase is approved.
```

Incorrect behavior:

```text
1. Stop implementing task persistence.

2. Install a frontend framework.

3. Create a dashboard.

4. Redesign the event architecture.

5. Claim the original task is complete.
```

---

# 31. Non-Negotiable Development Principles

The following rules apply throughout development:

**One active objective.**

Work toward the approved task.

**Research before major architectural commitments.**

Do not select foundational technology through guesswork.

**Build functional systems.**

Do not substitute convincing simulations for real capabilities.

**Preserve modularity.**

Keep major subsystems independently maintainable.

**Control complexity.**

Do not build unnecessary infrastructure.

**Verify results.**

Attempted execution is not proof of success.

**Respect permissions.**

Never bypass authorization boundaries.

**Keep documentation consistent.**

Important architectural and product decisions must be recorded.

**Do not implement the entire roadmap at once.**

Future capabilities must wait for their approved milestones.

---

# 32. Final Scope Rule

M I K A S A is a long-term autonomous AI platform.

Its vision may be broad.

Its current implementation scope must always be narrow, explicit, and verifiable.

The coding agent must distinguish between:

```text
WHAT MIKASA WILL EVENTUALLY BECOME

WHAT THE CURRENT PHASE MUST DELIVER

WHAT THE CURRENT TASK AUTHORIZES
```

These are three different things.

Never confuse them.

**Build the current task correctly. Preserve the foundation. Expand deliberately.**
