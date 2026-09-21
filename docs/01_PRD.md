# M I K A S A
## Product Requirements Document (PRD)

**Document:** `docs/01_PRD.md`

**Project:** M I K A S A

**Version:** 0.1.0

**Status:** DRAFT — Pending architecture research and approval

**Document type:** Product requirements

**Priority:** High

**Last updated:** 2026-09-21

---

# 1. Product Overview

M I K A S A is a modular, autonomous personal AI agent platform inspired by the general concept of JARVIS.

Mikasa should eventually operate as a persistent digital assistant capable of understanding user requests, developing plans, executing tasks, using tools, coordinating specialist agents, maintaining organized memory, and interacting with digital environments.

The system should provide one unified assistant experience while coordinating multiple internal capabilities.

Mikasa is being developed from scratch.

Existing open-source AI agents will be researched for architectural inspiration, reusable concepts, and potential integrations.

The project must not depend on any particular existing agent unless the dependency is explicitly evaluated and approved.

---

# 2. The Problem

Modern AI assistants can answer questions and generate content, but useful real-world tasks frequently require multiple independent steps.

A user might need an assistant to:

- Understand a complicated objective.
- Inspect an existing project.
- Research unfamiliar information.
- Determine which tools are required.
- Develop an execution plan.
- Perform actions using a computer.
- Diagnose failures.
- Continue after interruptions.
- Remember relevant information for future work.
- Verify that the requested result was actually achieved.

Without a reliable agent architecture, these tasks often require constant user intervention.

A second problem is that AI agents built without clear architectural boundaries can become increasingly difficult to maintain as more features are added.

Mikasa aims to address these problems through a coherent, modular agent platform.

---

# 3. Product Vision

Create a persistent personal AI assistant that can transform high-level goals into verified results through structured planning, tool execution, useful memory, and controlled autonomy.

The user should primarily interact with Mikasa rather than manually coordinating multiple models, tools, and specialist agents.

The long-term experience should support natural communication, proactive assistance, extended task execution, and interaction with the user's digital environment.

The product must remain understandable, controllable, and recoverable as its capabilities expand.

---

# 4. Target Users

## 4.1 Primary User

The initial product is intended for a single technical or technically curious user who wants to interact with an AI assistant to accomplish digital tasks.

The user should not need to understand Mikasa's internal architecture to use it.

The initial experience should make it possible to issue natural-language instructions without manually constructing workflows.

## 4.2 Future Users

The architecture may eventually support multiple users, devices, and environments.

However, multi-user infrastructure is not a requirement for the first working version.

The initial implementation must not introduce unnecessary complexity for hypothetical enterprise or large-scale deployment scenarios.

---

# 5. Primary User Experience

The user communicates primarily with Mikasa.

A typical interaction should look like this:

**User:**

"Mikasa, examine this project, identify why the application is failing, and fix the problem."

**Mikasa:**

1. Understands the objective.
2. Checks the relevant project context.
3. Inspects the repository.
4. Identifies possible causes.
5. Creates a plan.
6. Selects the necessary tools.
7. Makes authorized changes.
8. Runs appropriate tests.
9. Investigates unsuccessful results.
10. Continues until the task is completed or blocked.
11. Explains what changed and what was verified.

The user should not need to manually instruct Mikasa to perform every individual step.

However, Mikasa must request approval before actions that require authorization.

---

# 6. Product Objectives

The following objectives define the intended direction of the product.

### OBJ-001 — Unified assistant

Provide one primary assistant interface backed by multiple internal capabilities.

### OBJ-002 — Autonomous execution

Enable Mikasa to perform multi-step tasks without requiring user input at every step.

### OBJ-003 — Persistent context

Maintain useful contextual information across conversations, tasks, and application restarts.

### OBJ-004 — Tool integration

Allow Mikasa to interact with external environments through a structured and extensible tool system.

### OBJ-005 — Recoverability

Enable interrupted work to resume from meaningful checkpoints rather than requiring complete restarts.

### OBJ-006 — Model independence

Support different AI providers and models through replaceable interfaces.

### OBJ-007 — Modularity

Allow major subsystems to evolve independently without repeatedly rebuilding the core.

### OBJ-008 — User control

Provide understandable progress, permission controls, cancellation, and access to task history.

### OBJ-009 — Gradual capability expansion

Make it possible to add coding, research, computer use, voice, and specialist agents without replacing the foundational runtime.

### OBJ-010 — Verifiable outcomes

Require the system to distinguish between attempting an action and successfully completing the requested objective.

---

# 7. Product Versions

Mikasa will be developed incrementally.

The following version labels describe target capability sets, not committed release dates.

## V0 — Research and Foundation

The purpose of V0 is to establish the project architecture before implementing the full agent.

Deliverables:

- Repository research.
- Project specifications.
- Architecture decisions.
- Technology evaluation.
- Initial repository structure.
- Development environment.
- Basic configuration.
- Testing infrastructure.
- Initial application entry point.

**Completion condition:** The foundation is documented, builds successfully, and is ready for implementation of the core runtime.

---

## V1 — First Working Mikasa

V1 must establish a genuinely functional autonomous agent, not simply an interface prototype.

Required capabilities:

- Text-based interaction.
- A working agent execution loop.
- At least one functional model provider.
- Structured tool registration and execution.
- Basic filesystem capabilities.
- Controlled terminal execution.
- A bounded multi-step task workflow.
- Basic planning.
- Initial persistent memory.
- Persistent conversation and task records.
- Task cancellation.
- Structured error handling.
- Basic execution logs.
- User approval for sensitive actions.
- Functional integration tests.

V1 should support a useful task such as inspecting a small code project, identifying a straightforward issue, making an authorized change, and running a test.

It does not need to reproduce every capability of a mature coding agent.

**Completion condition:** A user can provide a multi-step objective, and Mikasa can use real tools to work toward it, track its progress, verify a result, and explain the outcome.

---

## V2 — Extended Agent Capabilities

V2 expands Mikasa beyond the initial working prototype.

Target capabilities:

- More advanced planning.
- Browser integration.
- Web research.
- Dedicated coding workflows.
- Richer memory retrieval.
- Improved task persistence.
- Checkpoint and resume support.
- Additional model providers.
- MCP support.
- Reusable skills.
- Improved observability.

**Completion condition:** Mikasa can perform longer workflows across multiple tools, retain relevant context, and recover from common interruptions.

---

## V3 — Multi-Agent and Persistent Autonomy

V3 introduces more advanced orchestration.

Target capabilities:

- Specialist agents.
- Structured task delegation.
- Shared task state.
- Parallel work where appropriate.
- Persistent background jobs.
- Scheduled tasks.
- Worker supervision.
- Retry and recovery policies.
- Resource and cost limits.
- Enhanced permission management.

**Completion condition:** Mikasa can coordinate specialist capabilities and execute supported long-running tasks without requiring continuous user supervision.

---

## V4 — Computer and Voice Interaction

V4 expands how Mikasa interacts with users and their environments.

Target capabilities:

- Screen understanding.
- Controlled desktop interaction.
- Mouse and keyboard tools.
- Voice input.
- Voice output.
- Interruption handling.
- Desktop or web interface.
- Proactive notifications.
- Optional cinematic startup interface.

**Completion condition:** Supported tasks can be initiated through voice and completed through authorized computer interaction, with appropriate user controls.

---

## V5 — Advanced Learning and Improvement

V5 introduces controlled mechanisms for improving Mikasa over time.

Target capabilities:

- Workflow analysis.
- Failure pattern detection.
- Reusable skill generation.
- Skill evaluation.
- Memory consolidation.
- Tool-selection improvement.
- Model-routing optimization.
- Controlled code improvement.
- Evaluation-based acceptance of changes.
- Rollback of unsuccessful changes.

**Completion condition:** Mikasa can propose and validate improvements in isolated environments without silently modifying its deployed runtime.

---

# 8. Functional Requirements

Each requirement has a unique identifier.

Architecture and implementation documents must reference these identifiers when specifying related functionality.

Requirements are classified as:

**P0:** Required for V1.

**P1:** Required for the next major capability expansion.

**P2:** Long-term product requirement.

---

## 8.1 Core Agent Runtime

### FR-001 — Receive instructions

Priority: P0

Mikasa must accept natural-language instructions through a text interface.

### FR-002 — Maintain conversation context

Priority: P0

Mikasa must maintain relevant conversation context during an active session.

### FR-003 — Execute agent loop

Priority: P0

Mikasa must implement a functional execution loop that can process model outputs, invoke supported tools, receive results, and continue toward a task objective.

### FR-004 — Bounded execution

Priority: P0

The runtime must support configurable execution limits, including maximum steps, tool timeouts, and retry limits.

### FR-005 — Structured results

Priority: P0

The runtime must produce a structured task outcome containing status, result, and relevant errors.

### FR-006 — Cancellation

Priority: P0

The user must be able to cancel an active task.

### FR-007 — Task recovery

Priority: P1

Supported tasks must be recoverable after an application restart.

Recovery must account for partially completed actions and avoid blindly repeating non-idempotent operations.

---

## 8.2 Planning and Autonomy

### FR-008 — Goal interpretation

Priority: P0

Mikasa must identify the requested objective and determine whether it can be handled with available capabilities.

### FR-009 — Basic task planning

Priority: P0

Mikasa must be able to form a simple multi-step plan when the task requires multiple actions.

### FR-010 — Progress tracking

Priority: P0

The system must track meaningful progress during multi-step execution.

### FR-011 — Replanning

Priority: P1

Mikasa must support revising a plan when execution reveals new information or an invalid assumption.

### FR-012 — Persistent autonomous jobs

Priority: P1

Mikasa must eventually support jobs that continue beyond a single foreground interaction.

### FR-013 — Scheduling

Priority: P2

The system must eventually support scheduled and condition-based task execution.

---

## 8.3 Model System

### FR-014 — Model provider abstraction

Priority: P0

Model calls must use a shared interface that is independent of individual providers.

### FR-015 — Configurable model

Priority: P0

The initial model must be selectable through configuration.

### FR-016 — Multiple model providers

Priority: P1

The system must support more than one provider implementation.

### FR-017 — Model routing

Priority: P1

Mikasa must eventually support selecting models based on task requirements, capability, availability, and resource constraints.

### FR-018 — Provider failure handling

Priority: P1

The model layer must distinguish provider errors from normal model responses and support appropriate recovery strategies.

---

## 8.4 Tool System

### FR-019 — Tool registration

Priority: P0

The system must provide a registry for available tools.

### FR-020 — Structured tool contracts

Priority: P0

Tools must define input schemas and structured results.

### FR-021 — Tool execution

Priority: P0

Mikasa must invoke tools through a shared execution interface.

### FR-022 — Initial filesystem tools

Priority: P0

The first version must support authorized file reading, searching, and writing within an explicitly configured workspace.

### FR-023 — Initial terminal tool

Priority: P0

The first version must support controlled terminal execution.

### FR-024 — Tool permissions

Priority: P0

Tool execution must enforce the permissions granted for the task and environment.

### FR-025 — MCP integration

Priority: P1

Mikasa must eventually support discovering and invoking tools exposed through compatible MCP servers.

### FR-026 — Extensible capabilities

Priority: P1

New tools should be addable through established interfaces without modifying the main agent loop.

---

## 8.5 Memory

### FR-027 — Session memory

Priority: P0

Mikasa must maintain context required for the current conversation and task.

### FR-028 — Initial persistent memory

Priority: P0

The system must support storing and retrieving selected information across sessions.

### FR-029 — Memory categorization

Priority: P1

The system must provide explicit memory categories rather than treating all stored information identically.

### FR-030 — Memory retrieval

Priority: P1

The system must support retrieving relevant memories based on the current task and access scope.

### FR-031 — Memory lifecycle

Priority: P1

The system must support updating, correcting, consolidating, and deleting stored memories.

### FR-032 — Memory provenance

Priority: P1

Persistent memories must retain information about their origin and relevant metadata.

### FR-033 — Memory scopes

Priority: P1

The system must distinguish private, project, agent-specific, and shared information.

### FR-034 — Advanced memory

Priority: P2

Mikasa should eventually support structured relationships, learned skills, versioning, and other advanced memory capabilities where they provide demonstrated value.

---

## 8.6 Coding

### FR-035 — Repository inspection

Priority: P0

Mikasa must be able to inspect files and directory structures inside an authorized project workspace.

### FR-036 — File modification

Priority: P0

Mikasa must be able to create and modify project files with appropriate permissions.

### FR-037 — Test execution

Priority: P0

Mikasa must be able to execute an available project test command through its controlled execution environment.

### FR-038 — Coding workflow

Priority: P1

Mikasa must eventually support repository search, debugging, patch generation, diff inspection, test execution, and verification as a coherent workflow.

### FR-039 — Version control

Priority: P1

The coding subsystem must eventually support controlled git operations and change review.

---

## 8.7 Research and Browsing

### FR-040 — Web research

Priority: P1

Mikasa must eventually support searching and reading public web information.

### FR-041 — Source tracking

Priority: P1

Research output must preserve sufficient source information to support verification.

### FR-042 — Browser interaction

Priority: P1

Mikasa must eventually support browser-based navigation and interaction through a dedicated tool interface.

### FR-043 — Multi-source research

Priority: P1

The research subsystem must support comparing sources and handling conflicting information.

---

## 8.8 Multi-Agent System

### FR-044 — Specialist delegation

Priority: P2

The main agent must eventually be able to delegate suitable tasks to specialist agents.

### FR-045 — Structured agent communication

Priority: P2

Specialist agents must communicate through structured task and result contracts.

### FR-046 — Agent supervision

Priority: P2

The main runtime must track delegated work and handle specialist failures.

### FR-047 — Shared information

Priority: P2

Specialist agents must be able to access permitted shared information without automatically receiving unrestricted access to all user or system memory.

---

## 8.9 Interface and Voice

### FR-048 — Initial text interface

Priority: P0

Mikasa must provide a usable text-based interface.

A CLI is sufficient for the initial implementation.

### FR-049 — Graphical interface

Priority: P2

Mikasa must eventually provide a clean graphical interface for conversation, task progress, and user controls.

### FR-050 — Voice interface

Priority: P2

Mikasa must eventually support voice input and speech output.

### FR-051 — Unified runtime

Priority: P2

Text, graphical, and voice interfaces must use the same underlying agent runtime.

### FR-052 — Startup experience

Priority: P2

Mikasa may provide an optional cinematic startup sequence displaying real subsystem initialization states.

A startup animation must not falsely represent unavailable capabilities as operational.

---

## 8.10 Computer Interaction

### FR-053 — Screen perception

Priority: P2

Mikasa must eventually support interpreting screen information through an appropriate perception interface.

### FR-054 — GUI interaction

Priority: P2

Mikasa must eventually support authorized mouse and keyboard interaction.

### FR-055 — Computer-use verification

Priority: P2

Computer-use workflows must verify relevant application state after performing actions whenever practical.

---

## 8.11 Self-Improvement

### FR-056 — Failure analysis

Priority: P2

Mikasa must eventually be able to identify recurring execution problems through operational records and evaluations.

### FR-057 — Skill improvement

Priority: P2

The system should support proposing and evaluating reusable workflows or skills.

### FR-058 — Controlled code improvement

Priority: P2

Any proposed code changes to Mikasa itself must occur in isolated environments with tests, review, and an explicit promotion process.

### FR-059 — Rollback

Priority: P2

Accepted improvements must be traceable and reversible where technically practical.

---

# 9. Non-Functional Requirements

Functional requirements describe what Mikasa does.

Non-functional requirements define how reliably, securely, and maintainably it must perform those functions.

## NFR-001 — Modularity

Major subsystems must communicate through documented interfaces.

Provider-specific implementation details must remain isolated from core business logic.

## NFR-002 — Reliability

The runtime must handle expected tool failures, invalid inputs, and provider errors without unnecessarily terminating the entire application.

## NFR-003 — Recoverability

Persistent state must remain consistent across ordinary application restarts.

Recovery mechanisms must distinguish completed, failed, interrupted, and potentially incomplete operations.

## NFR-004 — Security

External actions and resource access must follow explicit permissions.

Untrusted tool output, web content, repository files, and retrieved memories must not be treated as authoritative instructions.

## NFR-005 — Privacy

Persistent user information must be stored deliberately and be removable through appropriate controls.

Sensitive information must not be included in logs or shared between agents without an authorized purpose.

## NFR-006 — Observability

Important operations must produce structured operational records.

The system should expose task status, tool results, errors, and relevant execution metadata without exposing hidden model reasoning.

## NFR-007 — Maintainability

The codebase must use coherent module boundaries, explicit dependencies, clear naming, and appropriate tests.

## NFR-008 — Performance

The initial architecture should avoid unnecessary model calls, repeated context retrieval, and excessive background processing.

Concrete latency and throughput targets must be established through baseline measurements once the prototype exists.

## NFR-009 — Extensibility

Adding a new model provider or tool implementation must not require substantial changes to unrelated core components.

## NFR-010 — Resource control

Agent execution must support configurable limits for time, steps, retries, resource consumption, and provider usage.

## NFR-011 — Testability

Core runtime behavior must be testable without requiring live model-provider requests for every test.

## NFR-012 — Honest system state

The application must distinguish functional capabilities, experimental capabilities, simulated behavior, and unavailable features.

The interface must not claim that a task succeeded without appropriate evidence.

---

# 10. Initial MVP Scope

The first working version of Mikasa must focus on establishing the core execution architecture.

The MVP must include:

1. A working text interface.
2. A configurable model provider.
3. A basic agent execution loop.
4. A structured tool registry.
5. Controlled filesystem and terminal tools.
6. Basic multi-step planning.
7. Initial persistent memory.
8. Persistent session and task records.
9. Permission enforcement and task cancellation.
10. Logging, error handling, and result verification.

These capabilities should work together as one integrated system.

The MVP is not complete if each feature exists only as an isolated demonstration.

---

# 11. MVP Exclusions

The following are NOT required for V1:

- Voice conversations.
- Animated avatars.
- Cinematic boot sequences.
- Full computer control.
- Autonomous installation of arbitrary tools.
- Advanced model-routing intelligence.
- Hundreds of specialist agents.
- Distributed agent clusters.
- Enterprise deployment.
- Fully autonomous codebase modification.
- Multi-device synchronization.
- Advanced knowledge graphs.
- Large-scale infrastructure.
- Complete integrations with every research repository.

These features may be researched and planned before V1, but their implementation must follow the approved roadmap.

---

# 12. Core MVP User Stories

## US-001 — Basic conversation

As a user, I want to send a message to Mikasa and receive a response so that I can communicate with the assistant.

**Acceptance criteria:**

- The application starts successfully.
- A configured model can receive a user message.
- Mikasa displays a response.
- Provider failures produce understandable errors.

---

## US-002 — Multi-step task

As a user, I want to give Mikasa a goal requiring several steps so that I do not need to issue every instruction manually.

**Acceptance criteria:**

- Mikasa identifies an actionable objective.
- The agent can execute multiple tool calls.
- Tool results influence subsequent actions.
- Execution respects configured limits.
- The user receives a meaningful final outcome.

---

## US-003 — File operations

As a user, I want Mikasa to inspect and modify files within an authorized workspace.

**Acceptance criteria:**

- Mikasa can list or search project files.
- Mikasa can read an authorized file.
- Mikasa can create or modify an authorized file.
- Unauthorized paths are rejected.
- The agent can verify the resulting file state.

---

## US-004 — Basic coding task

As a user, I want Mikasa to solve a straightforward problem in a small code project.

**Acceptance criteria:**

- Mikasa can inspect relevant source files.
- Mikasa can form a basic implementation plan.
- Mikasa can make an authorized change.
- Mikasa can run an appropriate test command.
- Mikasa reports the changes and test results.

---

## US-005 — Persistent information

As a user, I want Mikasa to retain useful information between sessions.

**Acceptance criteria:**

- Selected information can be stored persistently.
- Stored information survives an ordinary application restart.
- The user can retrieve the information in a later session.
- The implementation does not require saving every message as permanent memory.

---

## US-006 — Task control

As a user, I want to understand and control what Mikasa is doing.

**Acceptance criteria:**

- The current task status is accessible.
- An active task can be cancelled.
- Tool execution is limited by configured permissions.
- Actions requiring approval cannot proceed before approval.
- Failures are reported rather than silently ignored.

---

# 13. MVP Demonstration Scenario

The following scenario will serve as an integrated acceptance test for the first working version.

**User request:**

"Mikasa, inspect this small Python project, find the failing test, identify the problem, fix it, and run the tests again."

Expected behavior:

**Step 1 — Understand**

Mikasa identifies the project and the requested outcome.

**Step 2 — Inspect**

Mikasa reads the relevant project files through authorized tools.

**Step 3 — Investigate**

Mikasa runs the available test command and examines the failure.

**Step 4 — Plan**

Mikasa identifies a possible solution and determines the necessary changes.

**Step 5 — Execute**

Mikasa makes an authorized, focused change.

**Step 6 — Verify**

Mikasa runs the relevant tests again and checks the results.

**Step 7 — Report**

Mikasa explains what changed, which tests ran, and whether the requested objective was achieved.

If the task cannot be completed, Mikasa must identify the remaining blocker rather than claim success.

This demonstration must use actual tools and an actual test project.

A scripted simulation does not satisfy this acceptance test.

---

# 14. Success Metrics

Initial success must be evaluated through observable results rather than subjective impressions of intelligence.

Suggested metrics include:

| Metric | Measurement |
|---|---|
| Task completion | Percentage of defined evaluation tasks completed successfully |
| Tool correctness | Percentage of tool operations with valid inputs and verified outputs |
| Error recovery | Percentage of selected recoverable failures handled successfully |
| Permission enforcement | Number of unauthorized actions executed in permission-denial tests |
| Memory persistence | Percentage of selected memory records correctly retrieved after restart |
| Cancellation | Percentage of cancellation tests that terminate active work correctly |
| Coding verification | Percentage of defined coding tasks passing their required tests |
| Runtime stability | Number of unexpected application crashes during the evaluation suite |

Performance targets should be established after the evaluation tasks, baseline environment, and measurement methodology are defined.

Do not invent success percentages before testing.

---

# 15. Research Dependencies

Before finalizing the architecture, the project must research the repositories supplied for Mikasa.

Research should cover:

- AgenticSeek.
- ZCode.
- MemOS.
- TencentDB Agent Memory.
- DeerFlow.
- OpenClaw.
- AgentMemory.
- The supplied ClaudeCode-style repository.
- Decepticon, limited to applicable general-purpose architecture.
- AstrBot.
- eDEX-UI, for interface inspiration.
- Agent-Reach.
- Gemini CLI.
- OpenHands.
- Agency Agents.
- Hive.

The research phase must evaluate:

1. Core architecture.
2. Agent execution model.
3. Planning.
4. Memory.
5. Tool systems.
6. Model integration.
7. Task persistence.
8. Multi-agent coordination.
9. Browser and coding capabilities.
10. Security and permissions.
11. Operational complexity.
12. Maintenance status.
13. Licensing and provenance.

The existence of a repository in the research list does not authorize copying its code or adding it as a dependency.

The project must document why a particular architectural concept or dependency is selected.

Research findings may lead to amendments to this PRD through the project's change-control process.

---

# 16. Technical Decisions Not Yet Finalized

The following decisions must remain open until the relevant research and architecture phases are complete:

- Primary programming language.
- Agent framework versus custom runtime.
- Initial model provider.
- Database technology.
- Memory retrieval strategy.
- Task persistence mechanism.
- Tool protocol implementation.
- Sandbox technology.
- Interface framework.
- Deployment approach.
- Operating-system support.
- Voice technology.
- Browser automation framework.
- Multi-agent execution strategy.

Do not make these decisions implicitly while creating unrelated project files.

Document significant choices in the project's architecture decision log.

---

# 17. Risks and Constraints

## RISK-001 — Scope expansion

Attempting to implement every planned capability simultaneously may produce an incomplete and difficult-to-maintain system.

**Mitigation:** Enforce phase-based implementation.

## RISK-002 — Excessive architecture complexity

Building infrastructure for hypothetical future requirements may delay delivery of a functional core.

**Mitigation:** Implement the smallest coherent architecture that supports the approved milestone.

## RISK-003 — Unreliable model behavior

Model outputs may be incorrect, incomplete, or unsuitable for direct execution.

**Mitigation:** Use structured contracts, validation, permission boundaries, and outcome verification.

## RISK-004 — Memory quality

Uncontrolled memory storage may lead to duplication, outdated information, and inappropriate retrieval.

**Mitigation:** Introduce clear storage rules, memory scopes, provenance, and lifecycle management.

## RISK-005 — Tool security

An agent with unrestricted tools could perform actions outside the intended task.

**Mitigation:** Use explicit permissions, workspace restrictions, execution limits, and approval gates.

## RISK-006 — Dependency complexity

Combining multiple mature agent frameworks may introduce incompatible assumptions and excessive maintenance requirements.

**Mitigation:** Research projects individually and adopt components only when integration benefits justify their complexity.

## RISK-007 — Incomplete recovery

Interrupted operations may leave partially modified files or unfinished external actions.

**Mitigation:** Track task state, distinguish repeatable operations, and introduce checkpoints and recovery logic.

---

# 18. Product Acceptance Rules

A feature must not be considered complete merely because:

- Its files exist.
- Its interface renders.
- A model describes how it should work.
- A mock returns a successful response.
- A unit test passes without exercising the intended behavior.

A feature is complete only when its documented acceptance criteria are satisfied.

Where appropriate, acceptance must include:

- Functional implementation.
- Integration with the existing runtime.
- Relevant tests.
- Permission enforcement.
- Error handling.
- Verified results.
- Documentation updates.

---

# 19. Relationship to Other Documents

This PRD defines product requirements.

It does not independently determine the final implementation architecture.

Related documents:

`AGENTS.md`

Defines the rules for coding agents working on the project.

`docs/00_PROJECT_CHARTER.md`

Defines the long-term vision and foundational principles.

`docs/02_SCOPE_AND_NON_GOALS.md`

Defines implementation boundaries and explicit exclusions.

`docs/03_SYSTEM_ARCHITECTURE.md`

Defines the approved technical architecture.

`docs/05_MEMORY_ARCHITECTURE.md`

Defines memory categories, lifecycle, storage, retrieval, and permissions.

`docs/19_DEVELOPMENT_PHASES.md`

Defines the detailed implementation sequence.

`docs/20_DEFINITION_OF_DONE.md`

Defines completion requirements.

`plans/CURRENT_PHASE.md`

Defines the current approved development phase.

`plans/CURRENT_TASK.md`

Defines the current implementation task.

---

# 20. Change Control

This PRD must not be silently rewritten by the coding agent.

Changes to the product scope or major requirements must be explicitly documented and approved.

When a user explicitly changes a product requirement, the agent should identify any affected documents and update them consistently after approval.

Older requirements must not override newer, explicit user decisions.

Implementation details may evolve without rewriting the PRD when the existing requirements remain satisfied.

---

# 21. Final Product Requirement

M I K A S A must evolve into a unified autonomous AI assistant built on a reliable and modular foundation.

Its capabilities must work together as one coherent system.

Each development phase must deliver functional, verifiable improvements.

The project must prioritize building a working agent before expanding into advanced autonomy, elaborate interfaces, voice, computer control, and self-improvement.

**The central product objective is to transform user goals into useful, verified results through one coordinated assistant.**
