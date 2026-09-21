# M I K A S A
## Agent Runtime Specification

**File:** `docs/04_AGENT_RUNTIME.md`

**Version:** 0.1.0

**Status:** PROPOSED — Pending architecture research and approval

**Authority:** Agent execution lifecycle and runtime behavior

**Applies to:** Main agent, specialist agents, execution loops, tool coordination, task execution, and runtime state management.

---

# 1. Purpose

This document defines the architecture and operational behavior of the M I K A S A Agent Runtime.

The Agent Runtime is responsible for converting an accepted task into a controlled sequence of model interactions, tool operations, observations, and verified results.

It is the central execution component of Mikasa.

The runtime must support a progression from a basic single-agent implementation to a more capable autonomous system without requiring a complete architectural rewrite.

This document defines:

- Runtime responsibilities.
- Agent execution lifecycle.
- Internal execution states.
- Model interaction.
- Context preparation.
- Tool invocation.
- Planning integration.
- Execution limits.
- Permission enforcement.
- Error handling.
- Retry and recovery behavior.
- Task cancellation.
- Result verification.
- Memory integration.
- Specialist-agent integration.
- Runtime observability.
- Testing requirements.

This document must remain consistent with the Project Charter, Product Requirements Document, and System Architecture Specification.

---

# 2. Core Runtime Principle

The Agent Runtime must operate through a controlled execution cycle.

```text
RECEIVE GOAL
    ↓
INITIALIZE EXECUTION
    ↓
PREPARE CONTEXT
    ↓
SELECT NEXT ACTION
    ↓
VALIDATE ACTION
    ↓
EXECUTE ACTION
    ↓
OBSERVE RESULT
    ↓
UPDATE STATE
    ↓
VERIFY PROGRESS
    ↓
CONTINUE / RECOVER / FINISH
```

The runtime must not simply pass a user message to a model and treat the resulting text as completed work.

It must support real execution.

The runtime must maintain explicit state outside the model's conversation context.

Model-generated content is not authoritative application state.

---

# 3. Separation of Responsibilities

The Agent Runtime coordinates execution.

It must not independently implement every supporting capability.

| Component | Responsibility |
|---|---|
| Main Agent | User-facing assistant behavior |
| Agent Runtime | Controls the execution loop |
| Task Manager | Owns task lifecycle and status |
| Planner | Creates and revises plans |
| Context Manager | Prepares context for model requests |
| Model Router | Provides access to configured models |
| Tool Registry | Describes available tools |
| Tool Executor | Validates and executes tool requests |
| Permission Service | Authorizes actions |
| Memory Service | Stores and retrieves agent memory |
| Event Service | Records and distributes operational events |
| Persistence Layer | Stores durable application state |
| Agent Manager | Coordinates future specialist agents |

The Agent Runtime may communicate with these components through their approved interfaces.

It must not bypass their responsibilities.

The MVP may combine closely related responsibilities within a small number of modules, provided the architectural boundaries remain clear.

---

# 4. Runtime Architecture

The proposed internal structure is:

```text
                 USER REQUEST
                      │
                      ▼
                TASK MANAGER
                      │
                      ▼
                AGENT RUNTIME
                      │
          ┌───────────┼───────────┐
          │           │           │
          ▼           ▼           ▼
       CONTEXT      PLANNER      MEMORY
       MANAGER                    SERVICE
          │
          ▼
      MODEL ROUTER
          │
          ▼
      MODEL RESPONSE
          │
          ▼
     ACTION INTERPRETER
          │
          ▼
      ACTION VALIDATOR
          │
          ├── FINAL RESPONSE
          │
          ├── TOOL REQUEST
          │
          ├── APPROVAL REQUIRED
          │
          └── ERROR
                 │
                 ▼
           TOOL EXECUTOR
                 │
                 ▼
           TOOL RESULT
                 │
                 ▼
         OBSERVE AND UPDATE
                 │
                 ▼
         CONTINUE OR FINISH
```

This diagram represents logical components.

It does not require each component to become a separate process, class, or service.

---

# 5. Runtime Execution Context

Every agent execution must have an explicit execution context.

The execution context contains the information necessary to coordinate the current task.

Suggested structure:

```text
AgentExecutionContext:
    execution_id
    task_id
    session_id
    agent_id
    parent_execution_id

    objective
    current_plan
    execution_state

    model_configuration
    available_capabilities
    permission_context

    step_count
    retry_count
    resource_limits

    cancellation_state
    started_at
    updated_at
```

These fields are conceptual.

Actual types, persistence requirements, and optional fields must be defined during implementation.

## Requirements

Each execution must have a unique identifier.

An execution must be associated with a task.

Specialist executions must eventually be traceable to their parent execution.

Execution context must not automatically contain unrestricted credentials or all stored user memories.

Sensitive information must be accessed only through authorized services.

---

# 6. Task vs. Execution

A task and an execution are different concepts.

**Task**

Represents the user's objective and its lifecycle.

Example:

```text
Task:
    task_id: task-001
    objective: Fix the failing tests
    status: RUNNING
```

**Execution**

Represents one attempt to perform work toward that objective.

Example:

```text
Execution:
    execution_id: exec-001
    task_id: task-001
    attempt: 1
    state: EXECUTING
```

One task may eventually have multiple executions because of:

- Retries.
- Interruptions.
- Resumption.
- Specialist delegation.
- Recovery after failure.

The Task Manager owns the task lifecycle.

The Agent Runtime owns its execution attempts.

The two must not be treated as interchangeable.

---

# 7. Agent Execution Lifecycle

Every new execution must follow a defined lifecycle.

## Stage 1 — Initialization

When a task enters execution, the runtime must:

1. Receive a valid task reference.
2. Create a unique execution identifier.
3. Load the relevant task state.
4. Resolve the agent configuration.
5. Resolve available capabilities.
6. Obtain the applicable permission context.
7. Initialize execution limits.
8. Initialize cancellation handling.
9. Record the execution start.

The runtime must reject or block execution when required configuration is missing.

It must not silently substitute unrestricted permissions or unknown providers.

---

## Stage 2 — Goal Understanding

The runtime prepares the user's objective for execution.

Mikasa should determine:

- What outcome is requested?
- What information is already available?
- Which capabilities may be needed?
- Does the objective require external actions?
- Are there missing prerequisites?
- Does the task require a plan?
- What evidence would demonstrate completion?

If necessary information is missing, the runtime may request clarification.

However, the system should not unnecessarily interrupt work when it can safely proceed using available information.

Goal understanding must not itself authorize tool access.

---

## Stage 3 — Context Preparation

The Context Manager prepares the information supplied to the model.

Possible context sources include:

```text
SYSTEM INSTRUCTIONS

AGENT ROLE

CURRENT USER REQUEST

SESSION CONTEXT

CURRENT TASK

ACTIVE PLAN

RELEVANT MEMORY

AVAILABLE TOOLS

RECENT TOOL RESULTS
```

The runtime must use the Context Manager rather than independently assembling unrestricted information from every storage system.

Context construction must respect memory scopes, permissions, and configured context limits.

---

## Stage 4 — Planning

For tasks requiring multiple actions, the runtime may request a plan.

The plan should identify:

- Objective.
- Required actions.
- Dependencies.
- Expected outcomes.
- Verification requirements.
- Potential blockers.

The initial implementation may use a simple plan.

Complex hierarchical planning is not required for the MVP.

The Planner must not independently execute tools.

It creates or revises plans that the runtime can execute.

---

## Stage 5 — Model Request

The Agent Runtime submits a structured request through the Model Router.

The request may contain:

- Prepared context.
- Current objective.
- Active plan.
- Available tools.
- Recent observations.
- Applicable execution constraints.

The Model Router selects the configured provider and obtains the model response.

The Agent Runtime must not contain provider-specific API logic.

---

## Stage 6 — Response Interpretation

The runtime interprets the normalized model response.

Supported response categories may include:

```text
FINAL_RESPONSE

TOOL_REQUEST

CONTINUE

CLARIFICATION_REQUEST

ERROR
```

Future response categories may include:

```text
DELEGATION_REQUEST

PLAN_REVISION_REQUEST

WAIT_REQUEST
```

The exact response contract must be defined during implementation.

A model response must not be executed without validation.

---

## Stage 7 — Action Validation

Before executing a proposed action, the runtime must verify:

- The requested action type is supported.
- The requested tool exists.
- Arguments match the tool schema.
- Required capabilities are available.
- Execution limits have not been exceeded.
- The task has not been cancelled.
- The action belongs to the current objective.
- Applicable permissions allow the action.

Invalid actions must produce structured errors.

The runtime may allow the agent to correct a malformed request within configured limits.

It must not repeatedly execute invalid actions without a bounded recovery strategy.

---

## Stage 8 — Action Execution

If the action is valid and authorized, the runtime submits it to the appropriate service.

For tool execution:

```text
AGENT RUNTIME
      │
      ▼
TOOL EXECUTOR
      │
      ▼
VALIDATION
      │
      ▼
PERMISSION ENFORCEMENT
      │
      ▼
APPROVED EXECUTION ENVIRONMENT
      │
      ▼
TOOL IMPLEMENTATION
      │
      ▼
STRUCTURED RESULT
```

The runtime must not bypass the Tool Executor to perform filesystem, terminal, browser, or other external operations.

Tool execution must be observable and subject to appropriate timeouts.

---

## Stage 9 — Observation

After an action, the runtime receives a structured result.

An observation should contain information such as:

```text
Observation:
    action_id
    tool_name
    status
    result
    error
    duration
    verification_data
```

The runtime must distinguish between:

- Successful tool invocation.
- Successful tool execution.
- Successful completion of the user's actual objective.

These are not necessarily the same.

For example, successfully editing a source file does not prove that the software issue was fixed.

---

## Stage 10 — Progress Evaluation

After receiving an observation, the runtime determines the next step.

Possible outcomes:

```text
CONTINUE EXECUTION

REQUEST ANOTHER TOOL

REVISE PLAN

RETRY AN ACTION

REQUEST APPROVAL

REPORT BLOCKER

VERIFY COMPLETION

TERMINATE EXECUTION
```

The runtime should avoid repeatedly performing identical actions without obtaining new information.

Repeated unsuccessful actions must contribute to failure detection and recovery.

---

## Stage 11 — Completion

When Mikasa determines that the objective may be complete, the runtime must evaluate the available evidence.

Completion should consider:

- Were the required actions performed?
- Were the acceptance criteria satisfied?
- Were the necessary results verified?
- Are any steps still incomplete?
- Were any failures ignored?
- Does the final result actually address the user's objective?

When practical, completion must be supported by observable results rather than model-generated claims.

The runtime then submits the proposed outcome to the Task Manager.

The Task Manager records the final task status.

---

# 8. Execution State Machine

The runtime must have explicit execution states.

Proposed states:

```text
INITIALIZING

PREPARING_CONTEXT

PLANNING

REQUESTING_MODEL

INTERPRETING_RESPONSE

VALIDATING_ACTION

WAITING_APPROVAL

EXECUTING_ACTION

PROCESSING_OBSERVATION

VERIFYING_RESULT

RETRYING

PAUSED

COMPLETED

FAILED

CANCELLED
```

These are internal execution states.

They must not be confused with the higher-level task statuses defined in the Task Manager.

The initial implementation may use fewer states when that simplifies the runtime without sacrificing correctness.

## State transition rule

Only valid state transitions may occur.

Examples:

```text
INITIALIZING
    ↓
PREPARING_CONTEXT
    ↓
REQUESTING_MODEL
    ↓
INTERPRETING_RESPONSE
    ↓
EXECUTING_ACTION
    ↓
PROCESSING_OBSERVATION
    ↓
REQUESTING_MODEL
```

An execution may also transition to a valid terminal or waiting state when appropriate.

Once an execution reaches a terminal state, it must not silently resume under the same completed execution attempt.

Resumption should use an explicitly defined recovery mechanism.

---

# 9. Runtime Action Contract

Every action requested by a model should be converted into a normalized internal representation.

Suggested structure:

```text
AgentAction:
    action_id
    execution_id
    action_type
    tool_name
    arguments
    required_capabilities
    metadata
```

Not every action requires a tool.

A final response may contain only the information required to communicate the result.

## Action validation

The runtime must not trust arbitrary model-generated metadata.

A model cannot grant itself permissions by declaring them in an action.

The registered tool definition and the trusted permission context determine the actual authorization requirements.

---

# 10. Tool Result Contract

Tool results must be normalized before returning to the Agent Runtime.

Suggested structure:

```text
ToolResult:
    action_id
    tool_name

    status
    output
    error

    started_at
    completed_at
    duration

    metadata
```

Suggested statuses:

```text
SUCCESS

FAILURE

TIMEOUT

DENIED

CANCELLED
```

Tool implementations may provide additional domain-specific information.

However, the Agent Runtime must be able to determine whether an action succeeded, failed, timed out, or was rejected.

An empty output must not automatically be interpreted as successful execution.

---

# 11. Model Interaction Contract

The runtime must communicate with models through the approved Model Router.

A conceptual model request:

```text
ModelRequest:
    execution_id
    messages
    tools
    model_configuration
    response_constraints
```

A conceptual response:

```text
ModelResponse:
    response_id
    content
    tool_calls
    finish_reason
    usage
    provider_metadata
```

These are illustrative structures.

The final schema must account for differences between providers.

The runtime must not depend on one vendor's exact response format.

---

# 12. Context Window Management

An agent may execute more steps than can fit into a single model context window.

The runtime must not assume unlimited context.

The Context Manager should eventually support:

- Recent message retention.
- Relevant memory retrieval.
- Important task-state retention.
- Tool-output truncation.
- Context summarization.
- Removal of redundant information.
- Context-budget enforcement.

## Critical rule

Context compression must not erase authoritative operational state.

The task objective, approved permissions, execution limits, active task status, and important unresolved blockers must remain available through trusted application state.

A summary generated by a model must not replace the authoritative task record.

---

# 13. Planning Integration

The Agent Runtime must support both direct and planned execution.

## Direct execution

Suitable for simple tasks requiring few actions.

Example:

```text
USER REQUEST
    ↓
MODEL
    ↓
TOOL
    ↓
RESULT
```

## Planned execution

Suitable for tasks requiring multiple dependent actions.

Example:

```text
USER GOAL
    ↓
PLANNER
    ↓
PLAN
    ↓
STEP 1
    ↓
OBSERVE
    ↓
STEP 2
    ↓
OBSERVE
    ↓
VERIFY
    ↓
COMPLETE
```

The runtime should not require a lengthy planning process for every simple request.

However, tasks requiring multiple actions must maintain enough execution state to prevent losing track of progress.

Detailed planning behavior belongs in:

`docs/07_AUTONOMY_ENGINE.md`

---

# 14. Execution Limits

Autonomous execution must be bounded.

The runtime must support configurable limits.

Possible limits include:

```text
maximum_steps

maximum_tool_calls

maximum_retries

maximum_execution_time

maximum_model_requests

maximum_parallel_actions

maximum_resource_usage

maximum_provider_cost
```

Not every limit is required in the first implementation.

The MVP must enforce at least:

- Maximum execution steps.
- Tool timeouts.
- Bounded retries.
- Task cancellation.

Additional limits should be introduced according to the approved phase.

## Limit behavior

When a limit is reached:

1. Stop scheduling new actions.
2. Preserve relevant task state.
3. Record why execution stopped.
4. Return a structured result.
5. Inform the user appropriately.

The runtime must not silently increase its own execution limits.

---

# 15. Infinite Loop Prevention

The runtime must detect repeated unsuccessful behavior.

Examples:

- Calling the same failing tool repeatedly.
- Repeating identical invalid arguments.
- Requesting the same unavailable capability.
- Revising a plan without making progress.
- Continuously generating tool calls after the objective is complete.

The runtime should track execution progress and repeated failure patterns.

When progress stops, the runtime may:

- Allow a bounded retry.
- Request a different strategy.
- Revise the plan.
- Report a blocker.
- Terminate the execution.

Repeated identical failures must not trigger unlimited retries.

---

# 16. Error Classification

Errors must be represented consistently.

Suggested categories:

```text
MODEL_ERROR

TOOL_ERROR

VALIDATION_ERROR

PERMISSION_ERROR

TIMEOUT_ERROR

CONTEXT_ERROR

PLANNING_ERROR

PERSISTENCE_ERROR

RESOURCE_LIMIT_ERROR

CANCELLATION_ERROR

UNEXPECTED_ERROR
```

Every error should include enough information to determine:

- Which operation failed.
- Which task or execution was affected.
- Whether the error is retryable.
- Whether user intervention is required.
- Whether the runtime can continue safely.

Sensitive information must not be exposed unnecessarily in error messages or logs.

---

# 17. Retry Strategy

Retries must be deliberate.

The runtime must distinguish between retryable and non-retryable failures.

## Potentially retryable failures

Examples:

- Temporary network interruption.
- Transient provider error.
- Temporary service unavailability.
- Tool timeout where retrying is safe.
- Invalid model-generated tool arguments that can be corrected.

## Normally non-retryable failures

Examples:

- Explicit permission denial.
- Cancelled task.
- Unsupported capability.
- Invalid credentials requiring user intervention.
- Destructive operation with an unknown completion state.

## Retry rules

Every retry must:

1. Respect configured retry limits.
2. Record the previous failure.
3. Preserve task cancellation.
4. Avoid repeating actions with uncertain side effects.
5. Stop when the underlying problem cannot be resolved automatically.

Retries must not be used to bypass permission boundaries.

---

# 18. Result Verification

Mikasa must distinguish action execution from objective completion.

## Verification categories

**Filesystem verification**

Confirm the expected file or content exists.

**Coding verification**

Run appropriate tests, inspect results, and check relevant changes.

**Research verification**

Check source relevance and distinguish verified information from unsupported claims.

**Browser verification**

Confirm the expected page or application state when supported.

**Task verification**

Compare the observed outcome against the task's acceptance criteria.

## Completion statuses

The runtime must distinguish outcomes such as:

```text
COMPLETED

PARTIALLY_COMPLETED

FAILED

BLOCKED

CANCELLED
```

If the Task Manager uses a smaller canonical status set, partial completion must be represented through structured outcome metadata rather than inventing an incompatible task state.

The runtime must not report a task as successfully completed when required verification failed.

---

# 19. Permission Enforcement

The Agent Runtime must operate within the permissions granted to the task.

Before tool execution, authorization must be enforced through trusted application code.

The runtime must not allow a model to:

- Grant itself additional permissions.
- Disable permission checks.
- Access unrestricted filesystem paths.
- Read unrelated private memory.
- Retrieve credentials without authorization.
- Execute prohibited actions through alternative tools.

## Approval flow

```text
ACTION REQUEST
      ↓
PERMISSION CHECK
      ↓
APPROVED?
      │
      ├── YES
      │    ↓
      │  EXECUTE
      │
      ├── REQUIRES APPROVAL
      │    ↓
      │  WAIT FOR USER
      │
      └── DENIED
           ↓
         REJECT
```

A denial must not automatically trigger attempts to perform the same prohibited action through a different tool.

Detailed rules belong in:

`docs/10_SECURITY_PERMISSIONS.md`

---

# 20. Cancellation

Cancellation must be supported from the first working version.

When a cancellation request is received:

1. Mark the execution as cancellation-requested.
2. Stop scheduling new model and tool actions.
3. Attempt to cancel active operations where supported.
4. Allow necessary cleanup.
5. Record the resulting execution state.
6. Inform the Task Manager.
7. Return the actual cancellation outcome.

Cancellation does not guarantee that an already completed external action can be reversed.

The runtime must not claim that an operation was undone unless the reversal was performed and verified.

---

# 21. Pause and Resume

Pause and resume are future runtime capabilities.

The architecture must leave room for them.

A paused execution should preserve enough information to resume safely.

Possible retained information:

```text
task_id

execution_id

current_plan

completed_steps

pending_steps

last_observation

checkpoint_reference

permission_context

resource_limits
```

Resuming a task must not blindly repeat operations that may already have completed.

The recovery mechanism must inspect task and action state before deciding what to execute next.

Full pause/resume support is not required for the initial MVP.

---

# 22. Persistence and Recovery

The runtime must distinguish transient execution state from durable task state.

## Transient execution state

Examples:

- Current in-memory model response.
- Active streaming output.
- Temporary context assembly.
- An in-progress function call.

## Durable operational state

Examples:

- Task ID.
- Objective.
- Task status.
- Accepted plan.
- Completed steps.
- Important tool results.
- Checkpoint references.
- Cancellation state.
- Final outcome.

Not every temporary model interaction must be stored permanently.

However, information necessary for supported recovery must be persisted.

## Recovery principle

After an interruption, Mikasa must determine what actually happened before repeating an operation.

For operations with unknown external outcomes, the system may need verification or user intervention.

The runtime must not assume that retrying every interrupted action is safe.

Detailed persistence rules belong in:

`docs/11_STATE_AND_PERSISTENCE.md`

---

# 23. Memory Integration

The Agent Runtime must access memory through the Memory Service.

It must not directly modify memory databases.

## Before execution

Mikasa may retrieve relevant information needed for the task.

## During execution

Mikasa may maintain temporary working context.

## After execution

Mikasa may store selected information according to the approved memory policy.

Potential memory candidates include:

- Useful project knowledge.
- Important user-approved preferences.
- Verified task outcomes.
- Reusable workflows.
- Relevant corrections.

Not every tool result or conversation message should become permanent memory.

## Memory boundaries

Memory retrieval must respect:

- User permissions.
- Project scope.
- Agent scope.
- Privacy requirements.
- Memory ownership.
- Retention policies.

Detailed requirements belong in:

`docs/05_MEMORY_ARCHITECTURE.md`

---

# 24. Specialist-Agent Integration

The initial runtime must support one main agent.

Future versions may support delegated specialist executions.

Specialist agents should use the same core execution infrastructure wherever practical.

## Conceptual delegation

```text
MAIN AGENT
     ↓
DELEGATION REQUEST
     ↓
AGENT MANAGER
     ↓
SPECIALIST EXECUTION
     ↓
STRUCTURED RESULT
     ↓
MAIN AGENT
```

A specialist execution must have:

- A defined objective.
- A scoped context.
- Assigned capabilities.
- Appropriate permissions.
- Execution limits.
- A result contract.

Specialists must not automatically inherit every tool, permission, or memory available to the main agent.

## Delegation limits

Future implementations must support limits on:

- Delegation depth.
- Concurrent specialists.
- Model usage.
- Tool execution.
- Total task duration.

Recursive delegation must not be unlimited.

Advanced multi-agent orchestration is outside the MVP scope.

---

# 25. Parallel Execution

Parallel execution is a future capability.

It may improve performance when independent tasks can be executed concurrently.

However, parallel work introduces additional concerns:

- Shared state.
- Conflicting file modifications.
- Permission scopes.
- Cancellation.
- Resource limits.
- Result aggregation.
- Task dependencies.
- Failure propagation.

The initial runtime may execute actions sequentially.

Parallel execution must be introduced only when task coordination and state ownership are sufficiently defined.

---

# 26. Background Execution

Mikasa should eventually support long-running and scheduled jobs.

Background execution must not depend on keeping one conversational model request open indefinitely.

Future architecture may include:

```text
TASK QUEUE

SCHEDULER

WORKER MANAGER

CHECKPOINT STORE

RECOVERY MANAGER
```

Background jobs must operate within explicit permissions and resource limits.

The ability to run a task in the background does not grant permission to perform unrestricted actions.

Full background execution belongs to a later implementation phase.

---

# 27. Runtime Observability

The Agent Runtime must expose useful operational information.

Examples:

```text
execution.started

execution.step_started

model.requested

model.completed

tool.requested

tool.completed

tool.failed

execution.retrying

execution.waiting_approval

execution.cancelled

execution.completed

execution.failed
```

Events should include appropriate identifiers to associate them with tasks and execution attempts.

Operational traces may include:

- Current task.
- Current execution state.
- Current plan step.
- Tool name.
- Tool result status.
- Error category.
- Retry count.
- Execution duration.
- Resource usage.

Do not expose hidden model reasoning.

Use structured events and meaningful state descriptions instead.

---

# 28. Runtime Interface

The Agent Runtime should expose a small, coherent public interface.

Conceptual operations:

```text
start_execution(task, context)
    -> ExecutionHandle
```

```text
get_execution(execution_id)
    -> ExecutionState
```

```text
request_cancel(execution_id)
    -> CancellationResult
```

Future operations may include:

```text
pause_execution(execution_id)
```

```text
resume_execution(execution_id)
```

```text
submit_approval(execution_id, decision)
```

These signatures are illustrative.

The final interface must be defined according to the selected programming language and runtime architecture.

Avoid exposing unnecessary internal implementation details through the public runtime API.

---

# 29. Concurrency and State Consistency

The runtime must prevent conflicting operations from corrupting task state.

For the MVP, one active execution attempt per task is a reasonable default.

Future parallel execution must define:

- State transition ownership.
- Concurrency limits.
- Shared-resource coordination.
- Conflict detection.
- Cancellation propagation.
- Durable result aggregation.

Two execution attempts must not independently mark the same task complete without an approved coordination mechanism.

Task state updates must pass through the Task Manager.

---

# 30. Prompt and Instruction Boundaries

The runtime must distinguish between:

**Authoritative instructions**

Trusted application rules, approved system configuration, and applicable user instructions.

**Task data**

Repository files, web pages, documents, tool responses, and retrieved information.

Task data may contain text that resembles instructions.

Such content must not automatically override the authoritative instructions governing Mikasa.

The runtime must not allow an external document or tool result to change execution permissions, disable safeguards, or redefine the user's objective.

---

# 31. Runtime Configuration

The runtime should expose structured configuration.

Possible settings:

```text
RuntimeConfig:
    default_model
    maximum_steps
    maximum_tool_calls
    tool_timeout
    maximum_retries
    context_limits
    logging_level
    permission_policy
```

These are conceptual configuration fields.

Exact defaults must be selected and tested during implementation.

Do not scatter runtime limits as unrelated hard-coded constants across multiple modules.

Configuration must be validated before execution.

---

# 32. MVP Runtime Requirements

The first working version must implement:

| Component | MVP requirement |
|---|---|
| Agent execution loop | Required |
| Model-provider integration | Required |
| Basic context preparation | Required |
| Tool-call interpretation | Required |
| Tool argument validation | Required |
| Tool execution | Required |
| Basic multi-step planning | Required |
| Task status tracking | Required |
| Bounded execution | Required |
| Structured error handling | Required |
| Permission enforcement | Required |
| Task cancellation | Required |
| Basic result verification | Required |
| Execution logging | Required |
| Initial memory integration | Required |
| Advanced specialist orchestration | Deferred |
| Parallel agent execution | Deferred |
| Full pause/resume | Deferred |
| Background worker system | Deferred |
| Dynamic agent creation | Deferred |
| Automated self-improvement | Deferred |

Do not implement deferred capabilities merely because their future interfaces appear in this specification.

---

# 33. Runtime Testing Requirements

The Agent Runtime requires tests covering its behavior.

Tests should use deterministic model and tool fixtures wherever practical.

## TEST-001 — Basic response

Verify that Mikasa can receive a user request and return a model-generated response.

## TEST-002 — Single tool call

Verify that a valid tool request is executed and its result is returned to the runtime.

## TEST-003 — Multi-step execution

Verify that the runtime can execute several dependent steps toward one objective.

## TEST-004 — Invalid tool

Verify that an unavailable tool request produces an appropriate error.

## TEST-005 — Invalid arguments

Verify that malformed tool arguments are rejected before execution.

## TEST-006 — Permission denial

Verify that unauthorized tool operations are rejected.

## TEST-007 — Execution limit

Verify that execution stops when the configured step limit is reached.

## TEST-008 — Retry behavior

Verify that retryable failures use bounded retries and non-retryable failures do not trigger inappropriate repeated execution.

## TEST-009 — Cancellation

Verify that cancellation stops additional actions and produces the correct task outcome.

## TEST-010 — Provider failure

Verify that model-provider failures are surfaced and handled according to the configured recovery policy.

## TEST-011 — Tool timeout

Verify that a timed-out tool operation produces a structured error and does not result in an endless execution loop.

## TEST-012 — Result verification

Verify that the runtime does not mark a task successfully completed when required acceptance criteria remain unsatisfied.

## TEST-013 — State ownership

Verify that task lifecycle changes are performed through the approved Task Manager interface.

## TEST-014 — Memory boundaries

Verify that runtime memory access respects the assigned access scope.

## TEST-015 — Prompt injection boundary

Verify that instructions embedded in untrusted tool output cannot grant permissions or override authoritative runtime instructions.

---

# 34. Integrated Runtime Evaluation

The first working runtime must pass an integrated evaluation using a real, authorized project workspace.

Example objective:

"Inspect this small code project, identify the failing test, fix the problem, and verify that the tests pass."

Expected execution:

```text
USER REQUEST
      ↓
TASK CREATED
      ↓
CONTEXT PREPARED
      ↓
PROJECT INSPECTED
      ↓
TEST FAILURE OBSERVED
      ↓
SOLUTION PLANNED
      ↓
AUTHORIZED FILE CHANGE
      ↓
TEST EXECUTED
      ↓
RESULT VERIFIED
      ↓
TASK COMPLETED
      ↓
USER INFORMED
```

The evaluation must use real filesystem and execution tools within an approved environment.

A hard-coded success response does not satisfy this requirement.

The runtime must also demonstrate an appropriate failure outcome when the task cannot be completed.

---

# 35. Development Sequence

The Agent Runtime should be implemented incrementally.

Suggested internal milestones:

**R0 — Runtime contracts**

Define execution context, action, result, and error contracts.

**R1 — Model interaction**

Implement the simplest functional model request and response cycle through the approved provider interface.

**R2 — Agent loop**

Support repeated model interactions and structured tool-call handling.

**R3 — Tool coordination**

Integrate the Tool Registry, Tool Executor, validation, and permissions.

**R4 — Task state**

Connect the runtime to the Task Manager.

**R5 — Context and memory**

Integrate context preparation and the initial Memory Service.

**R6 — Basic planning**

Support execution of simple multi-step objectives.

**R7 — Execution controls**

Implement cancellation, limits, and bounded recovery.

**R8 — Verification**

Introduce structured task outcomes and practical result verification.

**R9 — Integrated evaluation**

Verify that the runtime completes a representative multi-step task using real tools.

Each milestone must have explicit acceptance criteria in the active task plan.

Completing one milestone does not authorize implementation of unrelated future capabilities.

---

# 36. Research Requirements

Before finalizing the runtime implementation, examine the relevant agent systems identified in the project's research requirements.

Priority research areas:

**AgenticSeek**

Study agent execution, planning, and tool coordination.

**ZCode**

Study coding-agent execution loops, context management, and tool invocation.

**DeerFlow**

Study orchestration, agent execution, and long-running workflows.

**Gemini CLI**

Study model interaction, tool calls, execution control, and extensibility.

**OpenHands**

Study agent execution environments, coding workflows, and runtime isolation.

**Hive**

Study persistent execution, delegation, failure recovery, and worker coordination.

The research phase must compare implementation trade-offs.

Do not assume that an existing framework should become Mikasa's runtime merely because it already provides many features.

The selected runtime approach must fit the approved architecture and MVP requirements.

---

# 37. Architecture Decisions Required

The following decisions must be resolved before their respective implementation work begins:

```text
RUNTIME-001
Primary runtime language and asynchronous execution model.

RUNTIME-002
Custom agent loop versus approved framework.

RUNTIME-003
Normalized model response contract.

RUNTIME-004
Tool execution and validation contract.

RUNTIME-005
Task and execution state representation.

RUNTIME-006
Cancellation and timeout mechanisms.

RUNTIME-007
Initial execution limits.

RUNTIME-008
Runtime persistence boundaries.

RUNTIME-009
Error classification and retry policy.

RUNTIME-010
Result verification contract.
```

These identifiers are planning references, not approved Architecture Decision Record numbers.

Approved decisions must be recorded in:

`docs/22_DECISION_LOG.md`

---

# 38. Definition of Done

The Agent Runtime MVP is complete when:

- A user can submit a task.
- The runtime initializes a valid execution.
- A configured model can produce responses.
- The runtime can interpret supported tool requests.
- Tool arguments are validated.
- Authorized tools execute successfully.
- Tool results are returned to the agent.
- The agent can perform multiple dependent actions.
- Execution remains within configured limits.
- Cancellation works.
- Errors are handled consistently.
- Task status is recorded accurately.
- The runtime can identify and report an unsuccessful task.
- A completed task provides meaningful verification evidence.
- Relevant automated tests pass.
- An integrated evaluation demonstrates real end-to-end task execution.

All required behavior must be implemented through the approved subsystem interfaces.

The runtime must not rely on mocked functionality to claim completion of the integrated evaluation.

---

# 39. Final Runtime Principle

Mikasa's Agent Runtime must be capable of coordinating useful work without surrendering control of execution to unpredictable model output.

The model proposes actions.

The runtime coordinates execution.

The permission system authorizes actions.

The tool system performs actions.

The task system records progress.

The verification process evaluates outcomes.

The user remains in control.

```text
M I K A S A

UNDERSTAND
    ↓
PREPARE
    ↓
PLAN
    ↓
ACT
    ↓
OBSERVE
    ↓
VERIFY
    ↓
RECOVER OR CONTINUE
    ↓
COMPLETE
```

**The runtime is the execution engine. The model is one component within it, not the entire system.**
