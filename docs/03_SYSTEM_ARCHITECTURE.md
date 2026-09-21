# M I K A S A
## System Architecture Specification

**File:** `docs/03_SYSTEM_ARCHITECTURE.md`

**Version:** 0.1.0

**Status:** PROPOSED — Pending repository research and architecture approval

**Authority:** System architecture and component boundaries

**Applies to:** All architectural design, implementation, integration, and future development of M I K A S A.

---

# 1. Purpose

This document defines the proposed high-level architecture of M I K A S A.

Its purpose is to establish a coherent technical foundation that supports the product requirements without prematurely implementing every planned capability.

The architecture must allow Mikasa to evolve from a basic functional agent into a persistent autonomous AI platform.

This document defines:

- Overall system structure.
- Major architectural layers.
- Core components.
- Component responsibilities.
- Communication boundaries.
- Dependency rules.
- Agent execution lifecycle.
- Data and state ownership.
- Extension mechanisms.
- Security boundaries.
- Error handling.
- Reliability requirements.
- Initial implementation boundaries.
- Future expansion requirements.

Detailed subsystem specifications are maintained in separate documents.

This document must not become a replacement for those specifications.

---

# 2. Architecture Status

The architecture described here is a proposed target architecture.

The following decisions are not yet finalized:

- Primary programming language.
- Agent framework.
- Database technology.
- Model SDK.
- Tool execution framework.
- Browser automation framework.
- Desktop interface technology.
- Message transport.
- Sandbox technology.
- Process deployment strategy.

These decisions must be informed by the repository research and documented through the project's architecture decision process.

The conceptual component boundaries established here may guide research and prototypes.

Do not treat illustrative technology choices as approved implementation requirements.

---

# 3. Fundamental Architecture

Mikasa should use a modular architecture built around one primary agent runtime.

The runtime coordinates planning, model interaction, tool execution, task state, and memory access through dedicated services.

The user-facing assistant should not directly implement every subsystem.

Conceptually:

```text
                    USER
                      │
                      ▼
             M I K A S A
              INTERFACES
                      │
                      ▼
                 GATEWAY
                      │
                      ▼
              MAIN AGENT
                      │
         ┌────────────┼────────────┐
         │            │            │
         ▼            ▼            ▼
      CONTEXT       PLANNER     TASK RUNTIME
         │            │            │
         └────────────┼────────────┘
                      │
                      ▼
               AGENT RUNTIME
                      │
        ┌─────────────┼─────────────┐
        │             │             │
        ▼             ▼             ▼
   MODEL ROUTER   TOOL SYSTEM   AGENT MANAGER
        │             │             │
        ▼             ▼             ▼
     MODELS      EXECUTION      SPECIALISTS
                  ENVIRONMENT

              SHARED SERVICES
       ┌──────────────┬──────────────┐
       │              │              │
     MEMORY       PERSISTENCE     EVENT SYSTEM
       │              │              │
       └──────────────┼──────────────┘
                      │
             SYSTEM SERVICES
                      │
           ┌──────────┼──────────┐
           │          │          │
       SECURITY    LOGGING    SCHEDULER
```

This diagram represents logical responsibilities.

It does not require each component to run in a separate process.

---

# 4. Architecture Style

Mikasa should initially use a modular application architecture.

A modular monolith is the preferred starting approach, subject to research validation.

This means:

- One primary application.
- Clearly separated internal modules.
- Explicit interfaces between major subsystems.
- Shared lifecycle management.
- Replaceable external integrations.
- Dedicated execution isolation where necessary.

Do not introduce distributed microservices solely because Mikasa has multiple logical components.

A planner, tool registry, memory service, and agent runtime do not automatically require separate servers.

Independent services may be introduced later when isolation, scalability, deployment, or operational requirements justify them.

---

# 5. Architectural Layers

Mikasa is divided into six logical layers.

```text
LAYER 1 — INTERFACES

LAYER 2 — APPLICATION / GATEWAY

LAYER 3 — AGENT ORCHESTRATION

LAYER 4 — DOMAIN SERVICES

LAYER 5 — INFRASTRUCTURE ADAPTERS

LAYER 6 — EXTERNAL ENVIRONMENTS
```

Each layer has a defined responsibility.

Components should communicate through approved contracts.

Avoid direct access across unrelated layers.

---

# 6. Layer 1 — Interfaces

The interface layer is responsible for user interaction.

Potential interfaces include:

```text
CLI

WEB

DESKTOP

VOICE

EXTERNAL MESSAGING
```

The initial version requires a text interface.

Other interfaces are future capabilities.

## Responsibilities

Interfaces may:

- Accept user input.
- Display assistant responses.
- Show task progress.
- Display approval requests.
- Submit cancellation requests.
- Display relevant operational information.
- Present completed results.

## Restrictions

Interfaces must not independently implement:

- Agent reasoning loops.
- Memory persistence logic.
- Model-provider routing.
- Tool execution.
- Task orchestration.
- Permission enforcement.

Interfaces must communicate with the application layer.

The same agent runtime must support all interfaces.

---

# 7. Layer 2 — Application Gateway

The application gateway connects user-facing interfaces to Mikasa's internal runtime.

It is responsible for translating external requests into structured application operations.

Suggested component:

`ApplicationGateway`

## Responsibilities

- Accept incoming requests.
- Validate request structure.
- Resolve user and session context.
- Create or locate tasks.
- Submit work to the agent runtime.
- Return structured responses.
- Expose task status.
- Handle cancellation requests.
- Route approval decisions.
- Connect interfaces to operational events.

## Conceptual request

```text
UserRequest:
    request_id
    session_id
    user_id
    message
    attachments
    metadata
```

Fields that are unavailable in the initial implementation may be optional.

## Important rule

The gateway must not contain the main agent's reasoning logic.

Its responsibility is communication and application coordination.

---

# 8. Layer 3 — Agent Orchestration

This layer is responsible for coordinating intelligent task execution.

It contains the main agent and the components required to execute goals.

Proposed components:

```text
MainAgent

AgentRuntime

TaskManager

Planner

ContextManager

AgentManager
```

Not all components need independent implementations in the MVP.

They represent separate responsibilities that must remain distinguishable as the system grows.

---

# 9. Main Agent

The Main Agent is the primary user-facing assistant.

It represents Mikasa during task execution.

## Responsibilities

The Main Agent should:

- Understand the user's objective.
- Determine whether available capabilities can satisfy it.
- Obtain relevant context.
- Select an appropriate execution strategy.
- Use planning when necessary.
- Request tool execution through the runtime.
- Coordinate specialist agents when supported.
- Assess task progress.
- Communicate results.

## Restrictions

The Main Agent must not directly:

- Open unrestricted filesystem handles.
- Execute arbitrary host commands.
- Access credentials outside approved services.
- Bypass tool permissions.
- Modify task storage without the designated task service.
- Bypass memory access controls.

All external actions must pass through the appropriate capability interfaces.

---

# 10. Agent Runtime

The Agent Runtime is the central execution coordinator.

It controls how an agent progresses from an instruction to a result.

Suggested component:

`AgentRuntime`

## Responsibilities

- Initialize execution.
- Retrieve relevant context.
- Request model responses.
- Process supported model outputs.
- Validate proposed tool calls.
- Submit actions to the tool system.
- Receive tool results.
- Update execution state.
- Handle cancellation.
- Enforce execution limits.
- Handle recoverable errors.
- Determine when execution should continue or terminate.
- Produce structured task outcomes.

## Conceptual execution loop

```text
START
  │
  ▼
LOAD TASK
  │
  ▼
PREPARE CONTEXT
  │
  ▼
REQUEST MODEL RESPONSE
  │
  ▼
INTERPRET RESPONSE
  │
  ├── FINAL ANSWER
  │       │
  │       ▼
  │    COMPLETE
  │
  ├── TOOL REQUEST
  │       │
  │       ▼
  │    VALIDATE
  │       │
  │       ▼
  │    AUTHORIZE
  │       │
  │       ▼
  │    EXECUTE TOOL
  │       │
  │       ▼
  │    OBSERVE RESULT
  │       │
  │       ▼
  │    UPDATE STATE
  │       │
  │       └──────► CONTINUE LOOP
  │
  └── ERROR / LIMIT
          │
          ▼
    RECOVER OR TERMINATE
```

The runtime must distinguish a model-generated final answer from a verified task completion.

A model declaring success does not automatically prove that the requested external result occurred.

The detailed runtime specification belongs in:

`docs/04_AGENT_RUNTIME.md`

---

# 11. Task Manager

The Task Manager owns task lifecycle and task state.

Suggested component:

`TaskManager`

## Responsibilities

- Create task IDs.
- Record task objectives.
- Maintain task status.
- Track execution attempts.
- Associate tasks with sessions.
- Record task outcomes.
- Handle cancellation.
- Coordinate task persistence.
- Expose task progress.

## Proposed task states

```text
CREATED

QUEUED

PLANNING

RUNNING

WAITING_APPROVAL

PAUSED

RETRYING

BLOCKED

COMPLETED

FAILED

CANCELLED
```

Not every state must be implemented in the first version.

Additional states must have documented meanings and valid transitions.

## State ownership

The Task Manager is the authoritative owner of task lifecycle state.

Other components may request task updates through its public interface.

They must not directly overwrite task records.

---

# 12. Planner

The Planner converts goals into executable plans when planning is useful.

Suggested component:

`Planner`

## Responsibilities

- Interpret objectives.
- Identify task dependencies.
- Propose execution steps.
- Identify required capabilities.
- Define success criteria.
- Update plans when assumptions fail.
- Support sequential and future parallel execution.

## Conceptual plan

```text
Plan:
    plan_id
    task_id
    objective
    steps
    dependencies
    status
    version
```

Individual steps may contain:

```text
Step:
    step_id
    description
    required_capabilities
    dependencies
    status
    expected_result
    verification
```

## Architectural rule

Planning must not become a second independent agent runtime.

The Planner provides plans.

The Agent Runtime executes them.

The Task Manager owns their associated task lifecycle.

Planning may initially be implemented as a small service or runtime capability rather than a separate complex framework.

---

# 13. Context Manager

The Context Manager prepares information required for model interaction.

Suggested component:

`ContextManager`

## Responsibilities

- Assemble relevant conversation context.
- Retrieve permitted memories.
- Include current task information.
- Include appropriate tool definitions.
- Apply context-size limits.
- Summarize older context when necessary.
- Separate trusted system instructions from untrusted external content.
- Maintain continuity across execution steps.

## Context sources

```text
SYSTEM INSTRUCTIONS

CURRENT USER REQUEST

SESSION HISTORY

CURRENT TASK

ACTIVE PLAN

RELEVANT MEMORY

AVAILABLE TOOLS

RECENT TOOL RESULTS
```

## Important distinction

Context is not the same as persistent memory.

Context is the information prepared for a particular model request.

Memory is a separate subsystem that stores and retrieves information according to explicit lifecycle rules.

The Context Manager may retrieve information through the Memory Service.

It must not independently implement permanent memory storage.

---

# 14. Agent Manager

The Agent Manager is a future component responsible for specialist agents.

Suggested component:

`AgentManager`

## Responsibilities

- Maintain specialist capability definitions.
- Select an appropriate specialist.
- Create authorized specialist execution contexts.
- Assign scoped tasks.
- Track delegated work.
- Receive structured results.
- Enforce delegation limits.
- Handle specialist failure and cancellation.

## Architectural rule

Specialist agents must reuse the core runtime, tool system, model interfaces, and permission infrastructure wherever practical.

Do not create completely separate agent frameworks for each specialist.

## Delegation model

```text
MAIN AGENT
    │
    ▼
AGENT MANAGER
    │
    ▼
SPECIALIST TASK
    │
    ▼
SPECIALIST EXECUTION CONTEXT
    │
    ▼
SHARED AGENT RUNTIME
    │
    ▼
STRUCTURED RESULT
    │
    ▼
MAIN AGENT
```

Specialists may have different prompts, capabilities, models, and permissions.

They must not automatically inherit every permission available to the main agent.

Advanced delegation is outside the MVP implementation scope.

---

# 15. Layer 4 — Domain Services

Domain services provide reusable capabilities to the orchestration layer.

Proposed services:

```text
MemoryService

ModelRouter

ToolRegistry

ToolExecutor

PermissionService

EventService

SchedulerService

SkillService
```

The initial version only requires the services necessary to satisfy its approved scope.

Future components may remain documented interfaces until their implementation phase.

---

# 16. Memory Service

The Memory Service is the sole architectural entry point for persistent agent memory operations.

Suggested component:

`MemoryService`

## Responsibilities

- Store selected information.
- Retrieve relevant information.
- Organize memory categories.
- Enforce memory scopes.
- Apply memory lifecycle rules.
- Support correction and deletion.
- Maintain memory metadata.
- Coordinate approved storage providers.

## Conceptual interface

```text
MemoryService:
    store()
    retrieve()
    get()
    update()
    delete()
    list()
```

This interface is illustrative.

Actual method signatures must be specified in the dedicated memory architecture.

## Memory categories

The intended design may include:

```text
WORKING

EPISODIC

SEMANTIC

PROCEDURAL

SKILLS

PROJECT

USER

AGENT

SHARED

SYSTEM
```

These categories must not automatically become ten separate databases.

Their storage strategy must be selected according to retrieval, lifecycle, privacy, and performance requirements.

## Research dependencies

Before finalizing memory implementation, research:

- MemOS.
- TencentDB Agent Memory.
- AgentMemory.

Detailed requirements:

`docs/05_MEMORY_ARCHITECTURE.md`

---

# 17. Model Router

The Model Router provides a provider-independent interface to AI models.

Suggested component:

`ModelRouter`

## Responsibilities

- Select configured models.
- Resolve provider adapters.
- Validate model capabilities.
- Handle provider-specific request translation.
- Normalize supported responses.
- Track model usage.
- Report provider failures.
- Support future routing strategies.

## Conceptual architecture

```text
AGENT RUNTIME
      │
      ▼
  MODEL ROUTER
      │
      ▼
 MODEL PROVIDER INTERFACE
      │
      ├── PROVIDER A
      │
      ├── PROVIDER B
      │
      └── LOCAL MODEL ADAPTER
```

The MVP requires one functional provider.

Multiple provider adapters and intelligent routing belong to later implementation tasks.

## Important rule

Provider-specific API structures must not leak throughout the Agent Runtime.

Provider adapters must normalize responses into common internal contracts.

Detailed requirements:

`docs/09_MODEL_ROUTER.md`

---

# 18. Tool Registry

The Tool Registry maintains the capabilities available to Mikasa.

Suggested component:

`ToolRegistry`

## Responsibilities

- Register tools.
- Maintain tool definitions.
- Expose available capabilities.
- Validate registration metadata.
- Support tool discovery.
- Associate tools with permission requirements.

## Conceptual tool definition

```text
ToolDefinition:
    name
    description
    input_schema
    output_schema
    capability
    permissions
    timeout
    execution_environment
```

The Registry describes available tools.

It does not independently execute them.

---

# 19. Tool Executor

The Tool Executor controls tool invocation.

Suggested component:

`ToolExecutor`

## Responsibilities

- Resolve a registered tool.
- Validate requested arguments.
- Verify required permissions.
- Select an approved execution environment.
- Execute the tool.
- Enforce timeouts.
- Capture results and errors.
- Produce structured execution records.
- Return results to the Agent Runtime.

## Tool execution flow

```text
AGENT REQUEST
      │
      ▼
TOOL REGISTRY
      │
      ▼
SCHEMA VALIDATION
      │
      ▼
PERMISSION CHECK
      │
      ├── DENIED
      │      │
      │      ▼
      │   REPORT / REQUEST APPROVAL
      │
      ▼
EXECUTION ENVIRONMENT
      │
      ▼
TOOL IMPLEMENTATION
      │
      ▼
RESULT VALIDATION
      │
      ▼
STRUCTURED TOOL RESULT
      │
      ▼
AGENT RUNTIME
```

MCP tools and future plugin tools must use the same permission and execution boundaries as native tools.

An external tool's declaration of its own permissions is not sufficient authorization.

Detailed requirements:

`docs/06_TOOL_SYSTEM.md`

---

# 20. Permission Service

The Permission Service determines which actions an agent is authorized to perform.

Suggested component:

`PermissionService`

## Responsibilities

- Evaluate capability requests.
- Enforce workspace restrictions.
- Apply user-granted permissions.
- Determine whether approval is required.
- Record authorization decisions.
- Reject unauthorized actions.
- Support permission revocation.

## Core rule

The model must not be the sole authority deciding whether its own proposed action is permitted.

Permission decisions must be enforced by application code.

A model response cannot grant itself additional access.

---

# 21. Layer 5 — Infrastructure Adapters

Infrastructure adapters connect Mikasa's internal contracts to actual technologies.

Examples:

```text
ModelProviderAdapter

MemoryStorageAdapter

TaskStorageAdapter

FilesystemAdapter

TerminalAdapter

BrowserAdapter

SandboxAdapter

EventStoreAdapter
```

Adapters may depend on external SDKs, databases, operating-system APIs, and service clients.

Core domain logic must not depend directly on these concrete implementations.

---

# 22. Dependency Direction

The architecture must enforce the following principle:

High-level application logic depends on interfaces.

Concrete infrastructure implements those interfaces.

Example:

```text
AgentRuntime
     │
     ▼
ModelProvider
     ▲
     │
CloudModelAdapter
```

The Agent Runtime must not depend directly on the implementation details of a particular provider.

The same rule applies to:

- Memory storage.
- Task persistence.
- Tool backends.
- Sandboxes.
- Browser automation.
- Speech engines.

## Dependency restrictions

The following dependencies are prohibited without an approved architecture decision:

- Memory Service directly importing the web interface.
- Model adapters directly changing task state.
- Specialist agents bypassing the shared tool system.
- Tool implementations directly editing agent memory.
- UI components invoking execution backends outside the approved application interfaces.
- Infrastructure adapters importing unrelated high-level application modules.

Dependencies must remain understandable and testable.

---

# 23. Layer 6 — External Environments

External environments are systems outside Mikasa's trusted core.

Examples:

```text
LOCAL FILESYSTEM

HOST OPERATING SYSTEM

EXECUTION SANDBOX

WEB BROWSER

PUBLIC INTERNET

EXTERNAL APIS

DATABASE SERVERS

MODEL PROVIDERS

MCP SERVERS

DESKTOP APPLICATIONS
```

Access to these environments must occur through approved interfaces and permissions.

External data must not automatically be treated as trusted instructions.

---

# 24. Data Ownership

Every important kind of state must have one authoritative owner.

| Data | Authoritative owner |
|---|---|
| Conversation records | Session service or approved session store |
| Task lifecycle | Task Manager |
| Active plans | Planner / plan repository |
| Persistent memories | Memory Service |
| Tool definitions | Tool Registry |
| Tool execution records | Tool Executor / event storage |
| Permissions | Permission Service |
| Model configuration | Model configuration service |
| Scheduled jobs | Scheduler |
| Specialist execution records | Agent Manager / Task Manager |

One database may physically store several types of data.

However, sharing a database does not mean all components may directly modify each other's records.

Components must access data through the appropriate owning service.

---

# 25. Persistence Architecture

Mikasa requires persistence for information that must survive process termination.

Potential persistent data includes:

```text
SESSIONS

TASKS

PLANS

CHECKPOINTS

MEMORY RECORDS

CONFIGURATION

EXECUTION EVENTS

SCHEDULES
```

The architecture should distinguish between:

**Operational persistence**

Information needed to operate and recover the application.

**Agent memory**

Selected information retained for future reasoning and task execution.

These are not identical responsibilities.

For example:

A task record may be stored permanently for operational tracking without automatically becoming a semantic memory.

A conversation may be retained as session history without automatically generating permanent user-profile memories.

The choice of storage technology remains pending research.

---

# 26. Event Architecture

Mikasa should use structured operational events to support observability and future asynchronous execution.

Potential events include:

```text
task.created

task.started

task.completed

task.failed

task.cancelled

plan.created

plan.updated

tool.started

tool.completed

tool.failed

memory.created

memory.updated

memory.deleted

agent.delegated

agent.completed

approval.requested

approval.granted

approval.denied
```

## Event structure

```text
Event:
    event_id
    event_type
    timestamp
    task_id
    correlation_id
    source
    payload
```

Exact schemas will be defined in the persistence and observability specifications.

## Important distinction

Emitting an event does not automatically make it durable.

The architecture must distinguish:

- In-process notifications.
- Persisted operational events.
- Durable task state.
- External messages.

The MVP may use an in-process event mechanism.

A distributed message broker is not required.

Task state must not depend solely on transient events when persistent state is required.

---

# 27. Task Execution Architecture

A task should have one authoritative lifecycle.

The proposed flow is:

```text
USER REQUEST
      │
      ▼
APPLICATION GATEWAY
      │
      ▼
TASK MANAGER
      │
      ▼
AGENT RUNTIME
      │
      ▼
CONTEXT MANAGER
      │
      ▼
MODEL / PLANNER
      │
      ▼
TOOL EXECUTION
      │
      ▼
RESULT OBSERVATION
      │
      ▼
TASK STATE UPDATE
      │
      ▼
CONTINUE OR FINISH
      │
      ▼
PERSIST RESULT
      │
      ▼
RESPOND TO USER
```

The Task Manager owns the task state.

The Agent Runtime owns the execution loop.

The Planner owns plan construction and revision.

The Tool Executor owns tool invocation.

The Memory Service owns memory operations.

The Application Gateway owns external request handling.

These responsibilities must not be collapsed into one uncontrolled component.

---

# 28. Long-Running Execution

The architecture must allow future long-running tasks.

Potential supporting components:

```text
TaskQueue

Scheduler

WorkerManager

CheckpointManager

RecoveryManager
```

These are future implementation concepts, not mandatory MVP services.

## Recovery principle

A task must not be blindly restarted from the beginning after an interruption.

The system should eventually persist sufficient information to determine:

- Which steps completed.
- Which steps failed.
- Which actions are incomplete.
- Which actions may safely be repeated.
- Which actions require verification before retrying.
- Whether the task should resume or request user intervention.

External actions may not be reversible or safely repeatable.

The recovery system must account for this.

Detailed requirements:

`docs/11_STATE_AND_PERSISTENCE.md`

---

# 29. Security Architecture

Security and permissions are cross-cutting responsibilities.

They apply to:

- Main-agent actions.
- Specialist-agent actions.
- Native tools.
- MCP tools.
- External plugins.
- Filesystem access.
- Terminal execution.
- Browser actions.
- Credentials.
- Memory access.
- External integrations.

## Security principle

Authorization must occur at the actual execution boundary.

A permission check performed only in a prompt is insufficient.

## Untrusted inputs

The following must be treated as potentially untrusted:

```text
WEB PAGES

REPOSITORY FILES

TOOL OUTPUT

EXTERNAL API RESPONSES

DOCUMENTS

PLUGIN DESCRIPTIONS

RETRIEVED MEMORY CONTENT
```

Untrusted content may provide task-relevant data.

It must not automatically override project instructions, user permissions, or system policies.

## Sensitive actions

Actions involving credentials, external communication, destructive changes, or elevated system access must use the appropriate approval and authorization mechanisms.

Detailed requirements:

`docs/10_SECURITY_PERMISSIONS.md`

---

# 30. Execution Isolation

Mikasa should separate its trusted runtime from environments used to execute generated code or untrusted operations.

Conceptually:

```text
TRUSTED MIKASA CORE
        │
        ▼
PERMISSION SERVICE
        │
        ▼
TOOL EXECUTOR
        │
        ▼
ISOLATED EXECUTION ENVIRONMENT
        │
        ▼
RESULT
```

Sandboxing is not interchangeable with permission checks.

A sandbox limits the environment in which an action occurs.

Permissions determine whether an action is authorized.

Both mechanisms must be considered.

The initial sandbox implementation must be chosen through architecture research.

---

# 31. Memory and Agent Isolation

Memory scopes must be enforced by the Memory Service.

Future specialist agents may receive:

- Task-specific working context.
- Approved project memories.
- Selected shared memories.
- Relevant tool results.

They must not automatically receive:

- All personal memories.
- Unrelated project memories.
- Raw credentials.
- Other agents' private working context.
- Unrestricted system configuration.

Delegation must preserve or narrow permissions.

It must not silently increase them.

---

# 32. Model Output Handling

Model responses must be interpreted as proposed content or actions.

They are not authoritative application state.

The runtime must validate:

- Tool names.
- Tool arguments.
- Supported response formats.
- Requested capabilities.
- Execution limits.
- Permission requirements.

Malformed responses must not be executed blindly.

Provider adapters should normalize their responses into common internal structures.

---

# 33. Error Handling

Errors should be represented through structured application results.

Suggested categories:

```text
VALIDATION_ERROR

MODEL_ERROR

TOOL_ERROR

PERMISSION_ERROR

TIMEOUT_ERROR

STORAGE_ERROR

EXECUTION_ERROR

CANCELLATION

UNEXPECTED_ERROR
```

Each error should contain sufficient operational information for diagnosis.

Avoid exposing credentials or unnecessary sensitive information in logs.

## Error ownership

A component should handle failures within its responsibility.

Examples:

- Model adapter handles provider-specific errors.
- Tool Executor handles tool invocation errors.
- Task Manager records task failure.
- Agent Runtime determines whether execution can continue.
- Application Gateway presents the outcome to the user.

Do not silently swallow failures.

---

# 34. Cancellation Architecture

Cancellation must be a first-class runtime capability.

When cancellation is requested:

1. The application records the cancellation request.
2. The runtime stops scheduling new actions.
3. Active operations receive cancellation where supported.
4. Cleanup is performed when appropriate.
5. Task state is updated.
6. The user receives the actual cancellation outcome.

Some external actions may already have completed before cancellation is processed.

Mikasa must not claim that such actions were undone unless reversal was actually performed and verified.

---

# 35. Observability

The system must support inspecting operational behavior.

The initial implementation should make it possible to observe:

- Task creation.
- Execution steps.
- Tool calls.
- Tool results.
- Errors.
- Task completion.
- Cancellation.
- Relevant model usage.

Future observability may include:

- Specialist-agent activity.
- Memory retrieval.
- Checkpoint history.
- Model routing decisions.
- Resource consumption.
- Execution timelines.

Do not expose hidden model reasoning.

Provide meaningful operational traces instead.

Detailed requirements:

`docs/17_OBSERVABILITY.md`

---

# 36. Interface Independence

Mikasa's core must function without requiring the graphical interface.

The initial application may use a CLI.

Future interfaces should communicate through the same application contracts.

```text
CLI ────────┐
            │
WEB ────────┤
            ▼
         GATEWAY
            │
DESKTOP ────┤
            │
VOICE ──────┘
            │
            ▼
       AGENT RUNTIME
```

The implementation must not create a separate intelligence system for each interface.

Interface-specific state may exist.

Core task execution, memory, permissions, and tool handling must remain shared.

---

# 37. Extension Architecture

Mikasa should eventually support extending its capabilities without modifying the core runtime for every integration.

Extension categories may include:

```text
MODEL PROVIDERS

TOOLS

MCP CONNECTORS

MEMORY ADAPTERS

SPECIALIST AGENTS

SKILLS

INTERFACES

EVENT HANDLERS
```

Extensions must declare their required capabilities.

Third-party extensions must not automatically receive unrestricted application access.

## MVP boundary

The initial implementation needs clean interfaces for core capabilities.

It does not require a fully developed public plugin marketplace or dynamic extension installation system.

---

# 38. Self-Improvement Architecture

Self-improvement is a future capability.

It must be separate from ordinary task execution.

Conceptually:

```text
OPERATIONAL OBSERVATION
          │
          ▼
    IMPROVEMENT PROPOSAL
          │
          ▼
  ISOLATED IMPLEMENTATION
          │
          ▼
      AUTOMATED TESTS
          │
          ▼
        EVALUATION
          │
          ▼
      HUMAN APPROVAL
          │
          ▼
    CONTROLLED DEPLOYMENT
          │
          ▼
         MONITORING
```

Self-improvement must not bypass:

- Project specifications.
- Permission controls.
- Test requirements.
- Architecture decisions.
- Version control.
- Deployment approval.

The runtime must not silently replace its own implementation during normal task execution.

Detailed requirements:

`docs/16_SELF_IMPROVEMENT.md`

---

# 39. Initial Deployment Architecture

The first working Mikasa should target one local application instance unless research identifies a compelling reason otherwise.

Conceptually:

```text
LOCAL MACHINE
│
├── MIKASA APPLICATION
│   │
│   ├── CLI
│   ├── AGENT RUNTIME
│   ├── MODEL ADAPTER
│   ├── TOOL SYSTEM
│   ├── MEMORY SERVICE
│   ├── TASK MANAGER
│   └── PERMISSION SERVICE
│
├── LOCAL PERSISTENCE
│
└── CONTROLLED EXECUTION ENVIRONMENT
```

The actual model may be accessed through a configured external provider or a compatible local runtime.

Remote deployment, distributed execution, and cross-device synchronization are not MVP requirements.

---

# 40. Proposed Repository Structure

The following structure illustrates the intended separation of responsibilities.

It is not yet an approved language-specific file tree.

```text
mikasa/
│
├── AGENTS.md
│
├── docs/
│   ├── 00_PROJECT_CHARTER.md
│   ├── 01_PRD.md
│   ├── 02_SCOPE_AND_NON_GOALS.md
│   ├── 03_SYSTEM_ARCHITECTURE.md
│   └── ...
│
├── plans/
│   ├── CURRENT_PHASE.md
│   ├── CURRENT_TASK.md
│   └── BACKLOG.md
│
├── src/
│   │
│   └── mikasa/
│       │
│       ├── app/
│       │   ├── gateway/
│       │   └── lifecycle/
│       │
│       ├── interfaces/
│       │   └── cli/
│       │
│       ├── core/
│       │   ├── agent/
│       │   ├── tasks/
│       │   ├── planning/
│       │   └── context/
│       │
│       ├── services/
│       │   ├── memory/
│       │   ├── models/
│       │   ├── tools/
│       │   ├── permissions/
│       │   └── events/
│       │
│       ├── infrastructure/
│       │   ├── persistence/
│       │   ├── model_providers/
│       │   ├── tool_adapters/
│       │   └── execution/
│       │
│       └── contracts/
│
├── tests/
│   ├── unit/
│   ├── integration/
│   └── evaluations/
│
├── config/
│
└── scripts/
```

Do not create every directory automatically.

Create modules when their responsibilities enter the approved implementation scope.

Do not create placeholder files solely to make the repository resemble the target architecture.

The final structure must reflect the selected programming language and its conventions.

---

# 41. Module Contracts

Major modules should expose explicit contracts.

For example:

```text
ModelProvider:
    generate(request)
        -> ModelResponse
```

```text
ToolExecutor:
    execute(tool_request, execution_context)
        -> ToolResult
```

```text
TaskManager:
    create_task(request)
        -> Task

    get_task(task_id)
        -> Task

    transition(task_id, transition)
        -> Task
```

```text
MemoryService:
    store(memory_request, access_context)
        -> MemoryRecord

    retrieve(query, access_context)
        -> MemoryResults
```

These are conceptual examples.

Actual types, signatures, error contracts, and asynchronous behavior must be defined during detailed subsystem design.

Do not implement an interface merely because it appears in this document.

---

# 42. Testing Architecture

Core components must be testable independently.

The proposed testing structure includes:

**Unit tests**

Test individual components and state transitions.

**Integration tests**

Test interactions between services and adapters.

**Agent evaluations**

Test whether Mikasa completes representative multi-step tasks.

**Permission tests**

Verify that unauthorized operations are rejected.

**Recovery tests**

Verify that supported tasks behave correctly after interruption or restart.

The first version does not need an enormous evaluation framework.

It does need meaningful tests covering its implemented capabilities.

Detailed requirements:

`docs/18_TESTING_AND_EVALS.md`

---

# 43. Architecture Research Requirements

Before finalizing this architecture, the research phase must evaluate the supplied AI-agent repositories.

The research must determine whether their implementations reveal better approaches to:

- Agent execution.
- Planning.
- Context management.
- Tool integration.
- Memory.
- Task persistence.
- Multi-agent coordination.
- Model abstraction.
- Failure recovery.
- Security.
- Extensibility.

Particular attention should be given to:

**DeerFlow and Hive**

Long-running execution, agent coordination, task state, and worker management.

**MemOS, TencentDB Agent Memory, and AgentMemory**

Memory organization, retrieval, lifecycle, and sharing.

**Gemini CLI, ZCode, and OpenHands**

Agent loops, coding workflows, tool execution, and execution environments.

**AgenticSeek and Agent-Reach**

Research capabilities, browsing, and external information access.

**OpenClaw and AstrBot**

Persistent assistant interfaces, communication channels, and extensions.

Research findings must be documented.

The architecture must be revised where verified evidence justifies a change.

Do not copy implementation details without evaluating their technical suitability and licensing.

---

# 44. Architecture Decision Process

Major architecture decisions must be recorded in:

`docs/22_DECISION_LOG.md`

Each decision should include:

```text
DECISION ID

STATUS

CONTEXT

REQUIREMENTS

ALTERNATIVES

CHOSEN APPROACH

RATIONALE

TRADE-OFFS

AFFECTED COMPONENTS

MIGRATION REQUIREMENTS
```

Example decisions:

```text
ADR-001:
Primary programming language.

ADR-002:
Agent runtime implementation strategy.

ADR-003:
Initial persistence technology.

ADR-004:
Memory storage and retrieval approach.

ADR-005:
Execution isolation strategy.

ADR-006:
Model provider abstraction.

ADR-007:
Tool protocol and registry design.
```

The identifiers are illustrative.

The decision log will establish the authoritative numbering.

---

# 45. MVP Architecture Boundaries

The first implementation must focus on the minimum components required for a useful working agent.

Required MVP responsibilities:

```text
APPLICATION ENTRY POINT

TEXT INTERFACE

AGENT EXECUTION LOOP

MODEL PROVIDER ADAPTER

TASK MANAGER

BASIC PLANNER

CONTEXT MANAGEMENT

TOOL REGISTRY

TOOL EXECUTOR

INITIAL MEMORY

BASIC PERSISTENCE

PERMISSION ENFORCEMENT

LOGGING

TESTING
```

Future components such as distributed workers, advanced specialist orchestration, voice, and graphical computer control must not be implemented merely because their positions appear in the system diagram.

The initial architecture may consolidate closely related responsibilities into a small number of focused modules.

The boundaries matter more than the number of classes or services.

---

# 46. Architecture Acceptance Criteria

This architecture may be considered approved for implementation when:

- The required repository research is complete.
- Major architecture decisions are documented.
- Core component responsibilities are clear.
- Dependency direction is defined.
- Task and memory state ownership is defined.
- Tool execution boundaries are defined.
- Permission enforcement points are identified.
- The MVP component set is established.
- The technology stack is approved.
- The initial repository structure is agreed upon.
- No unresolved architectural contradiction blocks the first implementation phase.

Approval of this document does not automatically authorize implementation of every component described within it.

Implementation remains controlled by the active phase and current task.

---

# 47. Final Architectural Principle

M I K A S A must operate as one coherent assistant supported by independent, well-defined capabilities.

The system must not evolve into a collection of disconnected agent frameworks, duplicated services, or tightly coupled integrations.

Every major component must have:

- A clear responsibility.
- A defined owner.
- Explicit inputs and outputs.
- Known dependencies.
- Appropriate permission boundaries.
- Testable behavior.

The architecture must support adding capabilities without repeatedly rebuilding the foundation.

**One assistant. One coordinated runtime. Many replaceable capabilities.**
