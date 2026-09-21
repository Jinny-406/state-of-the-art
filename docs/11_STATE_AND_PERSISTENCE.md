# M I K A S A
## State, Persistence & Recovery Architecture

**File:** `docs/11_STATE_AND_PERSISTENCE.md`

**Version:** 0.1.0

**Status:** PROPOSED — Pending architecture research and approval

**Authority:** Task state, session state, persistence, checkpoints, resumability, recovery, event durability, and long-running execution state

**Applies to:** Agent Runtime, Task Manager, Planner, Memory Service, Scheduler, specialist agents, background workers, interfaces, and persistence infrastructure.

---

# 1. Purpose

This document defines how M I K A S A stores, restores, and manages operational state.

The purpose of the persistence system is to allow Mikasa to maintain continuity across:

- Multiple agent steps.
- Multiple conversations.
- Application restarts.
- Interrupted tasks.
- Long-running workflows.
- Specialist-agent execution.
- Future background workers.
- Future scheduled tasks.

State persistence must remain separate from conversational context and agent memory.

A model's context window must never become the only source of truth for task state.

---

# 2. Core Persistence Principle

Mikasa must preserve important operational facts outside the model.

Examples:

```text
WHAT TASK IS RUNNING?

WHAT HAS ALREADY COMPLETED?

WHAT IS CURRENTLY BLOCKED?

WHAT ACTIONS HAVE BEEN ATTEMPTED?

WHAT WAS THE LAST VERIFIED RESULT?

WHAT APPROVALS ARE ACTIVE?

WHAT SHOULD HAPPEN NEXT?
```

The model may reason about this information.

It must not be the only place where the information exists.

---

# 3. State Categories

Mikasa must distinguish between several kinds of state.

```text
OPERATIONAL STATE

CONVERSATION STATE

MEMORY

ARTIFACTS

CONFIGURATION

EPHEMERAL RUNTIME STATE
```

These must not be treated as interchangeable.

---

# 4. Operational State

Operational state describes what Mikasa is doing.

Examples:

- Task records.
- Execution attempts.
- Plans.
- Plan steps.
- Checkpoints.
- Pending approvals.
- Retry counters.
- Cancellation state.
- Worker state.
- Scheduled jobs.

Operational state must be stored according to the Task Manager and runtime architecture.

---

# 5. Conversation State

Conversation state contains information needed to maintain an interaction.

Examples:

- Session identifier.
- User messages.
- Assistant responses.
- Tool-result references.
- Conversation metadata.

Conversation state may help reconstruct context.

It is not the authoritative source of task execution state.

A task should not need to infer its status by rereading an entire chat transcript.

---

# 6. Persistent Memory

Agent memory contains selected information intended for future reasoning.

Examples:

- Project facts.
- User-approved preferences.
- Reusable workflows.
- Verified decisions.

Memory behavior belongs in:

`docs/05_MEMORY_ARCHITECTURE.md`

Operational persistence must not be implemented by abusing the memory subsystem.

---

# 7. Artifacts

Artifacts are files or structured outputs created during tasks.

Examples:

- Reports.
- Generated code.
- Test results.
- Research documents.
- Screenshots.
- Diffs.
- Exported data.

Artifacts should be referenced by stable identifiers or paths.

Large artifacts should not be embedded directly into every task record.

---

# 8. Ephemeral Runtime State

Some information exists only while the application is running.

Examples:

- Open network connections.
- Active streams.
- Temporary parsed data.
- Current in-memory buffers.
- Running process handles.
- Temporary context assembly.

Ephemeral state may disappear during restart.

If a task depends on information after restart, that information must be persisted separately.

---

# 9. Persistence Architecture Overview

```text
                 M I K A S A
                      |
                      v
                 TASK MANAGER
                      |
                      v
                 STATE SERVICE
                      |
        +-------------+-------------+
        |             |             |
        v             v             v
      TASKS        EXECUTIONS      PLANS
        |             |             |
        +-------------+-------------+
                      |
                      v
               CHECKPOINT STORE
                      |
                      v
               PERSISTENCE LAYER
                      |
        +-------------+-------------+
        |                           |
        v                           v
   STRUCTURED DATA               ARTIFACTS
```

This diagram describes logical ownership.

The initial implementation may use one database for several record types.

---

# 10. State Service

The State Service provides controlled access to persisted operational state.

Suggested component:

`StateService`

Responsibilities:

- Store task state.
- Store execution state.
- Store plan state.
- Store checkpoints.
- Load state during recovery.
- Validate state transitions.
- Coordinate persistence transactions.
- Expose durable state to authorized components.

The Task Manager should remain the authoritative owner of task lifecycle.

The State Service is the persistence mechanism.

---

# 11. Task Record

Every task must have a durable record.

Proposed structure:

```text
TaskRecord:
    task_id

    user_id
    session_id

    objective
    status

    created_at
    updated_at
    completed_at

    active_plan_id
    active_execution_id

    parent_task_id

    outcome
    blocker

    metadata
```

Not every field is required for MVP.

Task IDs must remain stable.

---

# 12. Task Status

Canonical task statuses should remain small and clear.

Suggested states:

```text
CREATED

QUEUED

RUNNING

WAITING_APPROVAL

PAUSED

BLOCKED

COMPLETED

FAILED

CANCELLED
```

The exact set must be finalized through an architecture decision.

Avoid creating dozens of nearly identical states.

Detailed internal execution states may remain within the Agent Runtime.

---

# 13. Task State Machine

Task transitions must be explicit.

Example:

```text
CREATED
   |
   v
QUEUED
   |
   v
RUNNING
   |
   +------> WAITING_APPROVAL
   |
   +------> PAUSED
   |
   +------> BLOCKED
   |
   +------> COMPLETED
   |
   +------> FAILED
   |
   +------> CANCELLED
```

Not every transition should be allowed.

For example:

```text
COMPLETED -> RUNNING
```

should not silently happen on the same task execution without an explicitly defined reopening mechanism.

---

# 14. Execution Record

A task may have one or more execution attempts.

Proposed structure:

```text
ExecutionRecord:
    execution_id
    task_id

    attempt_number

    agent_id
    parent_execution_id

    status

    started_at
    updated_at
    completed_at

    step_count
    retry_count

    last_action_id
    last_observation_id

    checkpoint_id

    error
    metadata
```

This allows Mikasa to distinguish:

```text
TASK EXISTS

EXECUTION ATTEMPT 1 FAILED

EXECUTION ATTEMPT 2 RESUMED

TASK EVENTUALLY COMPLETED
```

---

# 15. Plan Persistence

Plans should be persistable once planning becomes significant.

Suggested structure:

```text
PlanRecord:
    plan_id
    task_id
    version

    objective
    status

    created_at
    updated_at
```

Plan steps may be stored separately.

---

# 16. Plan Step Record

Conceptual structure:

```text
PlanStepRecord:
    step_id
    plan_id

    description

    dependencies

    status

    attempts

    expected_result
    verification_criteria

    result_reference

    started_at
    completed_at
```

This helps Mikasa know which work has already happened.

The system must not rely solely on the model remembering which plan steps were completed.

---

# 17. Plan Versioning

Plans may change during execution.

When replanning occurs, Mikasa should preserve enough history to understand how the plan changed.

Possible model:

```text
PLAN VERSION 1
    |
    v
OBSERVATION INVALIDATES STEP
    |
    v
PLAN VERSION 2
```

The system should preserve:

- Previous version.
- Reason for revision.
- Completed steps.
- New or removed steps.

Do not rewrite plan history as though the original plan never existed.

---

# 18. Action Records

Important actions should have stable identities.

Suggested structure:

```text
ActionRecord:
    action_id
    execution_id

    action_type

    tool_id
    arguments_reference

    status

    requested_at
    started_at
    completed_at

    result_reference

    side_effect_class

    error
```

Action IDs are especially important for recovery.

The system must be able to determine whether an action was:

- Proposed.
- Authorized.
- Started.
- Completed.
- Failed.
- Cancelled.
- Left with unknown outcome.

---

# 19. Action Status

Suggested statuses:

```text
REQUESTED

AUTHORIZED

RUNNING

SUCCEEDED

FAILED

DENIED

CANCELLED

TIMED_OUT

UNKNOWN_OUTCOME
```

`UNKNOWN_OUTCOME` is critical.

It means:

Mikasa cannot safely prove whether an external action completed.

Such actions must not be automatically repeated.

---

# 20. Idempotency

Recovery requires understanding whether an action can safely run again.

Possible idempotency categories:

```text
SAFE_TO_REPEAT

VERIFY_BEFORE_REPEAT

DO_NOT_REPEAT_AUTOMATICALLY
```

Examples:

Reading a file:

```text
SAFE_TO_REPEAT
```

Writing an exact file state may sometimes be:

```text
VERIFY_BEFORE_REPEAT
```

Sending an external message:

```text
DO_NOT_REPEAT_AUTOMATICALLY
```

Action retry behavior must use this information.

---

# 21. Checkpoints

A checkpoint captures enough state to continue a supported task later.

A checkpoint is not simply a conversation summary.

It should represent actual execution progress.

Possible checkpoint contents:

```text
Checkpoint:
    checkpoint_id

    task_id
    execution_id

    plan_id
    plan_version

    completed_steps

    current_step

    pending_steps

    last_verified_state

    unresolved_actions

    permission_reference

    created_at
```

Exact fields depend on the task type.

---

# 22. Checkpoint Purpose

Checkpoints may be used for:

- Pause and resume.
- Application restart.
- Worker failure.
- Long-running tasks.
- Manual recovery.
- Self-improvement experiments.

A checkpoint must not falsely claim that external state is still unchanged.

Resumption should revalidate important assumptions.

---

# 23. Checkpoint Frequency

Not every model message needs a checkpoint.

Useful checkpoint moments may include:

- After a plan is accepted.
- After a significant verified action.
- Before a high-impact change.
- Before pausing.
- After completing a major task stage.
- Before switching execution environments.

Checkpoint frequency should balance reliability and overhead.

---

# 24. Recovery Principle

Recovery must begin by understanding what actually happened.

Correct recovery:

```text
LOAD TASK
    |
    v
LOAD LAST CHECKPOINT
    |
    v
INSPECT LAST ACTIONS
    |
    v
VERIFY IMPORTANT EXTERNAL STATE
    |
    v
DETERMINE SAFE NEXT STEP
    |
    v
RESUME
```

Incorrect recovery:

```text
APP CRASHED
    |
    v
START ENTIRE TASK AGAIN
```

The latter can create duplicate side effects and wasted work.

---

# 25. Recovery Workflow

Proposed process:

```text
APPLICATION START
      |
      v
FIND INTERRUPTED TASKS
      |
      v
LOAD DURABLE STATE
      |
      v
CLASSIFY LAST EXECUTION
      |
      v
CHECK UNKNOWN OUTCOMES
      |
      v
REVALIDATE PERMISSIONS
      |
      v
VERIFY REQUIRED ENVIRONMENT
      |
      v
CREATE NEW EXECUTION ATTEMPT
      |
      v
RESUME FROM SAFE POINT
```

A resumed task should generally use a new execution attempt identifier.

---

# 26. Crash Recovery

An unexpected crash may occur during:

- Model request.
- Tool execution.
- File write.
- External API request.
- Database transaction.
- Plan update.

Recovery behavior must depend on where the crash occurred.

The system must not assume every interrupted action failed.

---

# 27. Transaction Boundaries

Persistence operations that must remain consistent should use appropriate transaction semantics where supported.

Example:

When marking an action successful, the system may need to atomically persist:

- Action result.
- Action status.
- Execution update.
- Relevant event.

Partial persistence can create ambiguous recovery state.

The exact transaction strategy depends on the selected storage technology.

---

# 28. Durable vs. Non-Durable Events

Mikasa may produce many operational events.

Not every event needs permanent storage.

The system should distinguish:

```text
EPHEMERAL EVENT

DURABLE EVENT

AUTHORITATIVE STATE
```

Example:

A UI typing indicator may be ephemeral.

Task completion should be durable.

Task state must not depend entirely on replaying optional transient events.

---

# 29. Event Persistence

Durable events may include:

```text
task.created

task.started

task.completed

task.failed

task.cancelled

execution.started

execution.completed

tool.completed

approval.granted

checkpoint.created
```

Event persistence may support:

- Auditing.
- Debugging.
- Timeline reconstruction.
- Observability.

However, the event log does not automatically need to be the canonical database.

---

# 30. Event Sourcing

Full event sourcing is not an MVP requirement.

Mikasa may use ordinary state tables plus selected durable events.

Do not introduce a complex event-sourced architecture unless research demonstrates clear value.

The project should prefer understandable state ownership.

---

# 31. Session Record

Conversation sessions should have stable identities.

Conceptual structure:

```text
SessionRecord:
    session_id
    user_id

    created_at
    updated_at

    status

    title
    metadata
```

Sessions may contain multiple tasks.

A session is not a task.

---

# 32. Session and Task Relationship

Example:

```text
SESSION
   |
   +---- TASK A
   |
   +---- TASK B
   |
   +---- TASK C
```

A conversation may discuss several objectives.

Each significant autonomous objective should be tracked independently when appropriate.

---

# 33. Parent and Child Tasks

Multi-agent or decomposed workflows may create child tasks.

Example:

```text
PARENT TASK
   |
   +---- RESEARCH TASK
   |
   +---- CODING TASK
   |
   +---- TESTING TASK
```

Child records should contain:

```text
parent_task_id
```

The parent task must remain authoritative for the overall user objective.

---

# 34. Task Artifacts

Tasks may produce artifacts.

Suggested structure:

```text
ArtifactRecord:
    artifact_id
    task_id

    artifact_type

    location

    created_at

    checksum
    metadata
```

Examples:

```text
REPORT

SOURCE_PATCH

TEST_LOG

SCREENSHOT

RESEARCH_EXPORT

GENERATED_DOCUMENT
```

Large artifacts should normally live outside structured task rows.

---

# 35. Artifact Integrity

Where appropriate, artifacts may include:

- Checksums.
- Versions.
- Source task references.
- Creation timestamps.
- Creator execution ID.

This helps verify which output belongs to which execution.

Not every temporary file requires formal artifact registration.

---

# 36. Persistence Store Abstraction

Core services must not depend directly on a specific database implementation.

Conceptual interface:

```text
StateRepository:
    create_task()

    get_task()

    update_task()

    create_execution()

    update_execution()

    save_plan()

    save_checkpoint()
```

The exact interface must be defined during implementation.

A storage adapter may use:

- Embedded database.
- Relational database.
- Other approved durable storage.

The choice remains pending research.

---

# 37. Initial Storage Strategy

The MVP should prefer a simple local persistent store unless research identifies a compelling alternative.

Desired characteristics:

- Reliable local persistence.
- Transactions.
- Simple backups.
- Easy development.
- Reasonable querying.
- Low operational complexity.

Do not deploy distributed database infrastructure for the initial local agent.

---

# 38. Schema Versioning

Persistent data structures will evolve.

Mikasa must support schema migrations.

Every durable schema should have a known version.

Changes must not silently reinterpret old records.

Migration strategy should support:

- Upgrade.
- Validation.
- Failure reporting.
- Backup where appropriate.

---

# 39. Migration Safety

Before destructive schema migration, the system should support an appropriate recovery strategy.

Possible steps:

```text
BACKUP
    |
    v
APPLY MIGRATION
    |
    v
VERIFY
    |
    v
COMMIT
```

Migration behavior depends on the selected database.

Failed migrations must not silently continue with partially incompatible state.

---

# 40. State Validation

Loaded state must be validated.

Possible problems:

- Unknown status.
- Missing required field.
- Invalid task reference.
- Broken parent relationship.
- Unsupported schema version.
- Corrupted checkpoint.

The system should report invalid state rather than blindly feeding it into the agent.

---

# 41. Persistence Failure

If durable state cannot be written when persistence is required:

- Do not claim the operation was persisted.
- Report the failure.
- Preserve in-memory state when safe.
- Avoid continuing long-running work that depends on unavailable persistence.
- Mark recovery limitations accurately.

The system must not say:

```text
Task saved.
```

if the database write failed.

---

# 42. Concurrency Control

Future background workers or parallel specialists may attempt to modify the same task.

The persistence architecture must eventually support concurrency protection.

Possible mechanisms:

- Optimistic version numbers.
- Transactions.
- Row locking.
- Compare-and-swap semantics.
- Task ownership leases.

The exact mechanism depends on the chosen store.

The MVP may enforce one active execution per task.

---

# 43. Optimistic Versioning

A record may include:

```text
version: 7
```

When updating:

```text
UPDATE IF CURRENT VERSION = 7
```

If another execution already changed it, the update fails and state must be reloaded.

This can prevent silent overwrites.

Optimistic concurrency is one possible strategy, not yet an approved implementation requirement.

---

# 44. Task Ownership Lease

Future worker systems may use task leases.

Conceptually:

```text
worker_a owns task_x until timestamp_y
```

This prevents two workers from independently executing the same task.

Leases require:

- Expiration.
- Renewal.
- Recovery after worker failure.
- Ownership checks.

This is not required for MVP.

---

# 45. Background Workers

Future persistent autonomy may use background workers.

Conceptually:

```text
TASK QUEUE
    |
    v
WORKER
    |
    v
EXECUTION
    |
    v
CHECKPOINT
    |
    v
TASK UPDATE
```

Workers must use the same:

- Task Manager.
- Agent Runtime.
- Permission Service.
- Tool System.
- Persistence layer.

Background workers must not become a separate version of Mikasa.

---

# 46. Task Queue

A future queue may track runnable work.

Potential task states:

```text
QUEUED

LEASED

RUNNING

WAITING

RETRY_SCHEDULED
```

Queue infrastructure is not required for MVP.

Do not introduce a message broker merely because long-running tasks may exist later.

---

# 47. Scheduler Integration

Future scheduled work may need durable schedule records.

Possible structure:

```text
ScheduledTask:
    schedule_id

    task_template

    schedule

    enabled

    last_run
    next_run

    permission_reference
```

Scheduled execution must not assume permissions remain valid forever.

Sensitive scheduled operations may require reauthorization.

---

# 48. Approval Persistence

Pending approvals may need persistence.

Possible record:

```text
ApprovalRecord:
    approval_id

    task_id
    execution_id

    action_reference

    requested_scope

    status

    requested_at
    decided_at

    expires_at
```

If Mikasa restarts while waiting for approval, it should not forget what action was pending.

---

# 49. Approval Recovery

After restart:

```text
LOAD TASK
    |
    v
DETECT WAITING APPROVAL
    |
    v
LOAD APPROVAL RECORD
    |
    v
VERIFY IT IS STILL VALID
    |
    v
CONTINUE WAITING OR EXPIRE
```

The system must not automatically approve a previously pending action.

---

# 50. Permission Revalidation

Persistent tasks may outlive their original permission context.

Before resuming significant work, Mikasa should verify:

- Permission still exists.
- Approval has not expired.
- Credential remains available.
- Resource scope has not changed.
- Task has not been cancelled.

Persisting an old permission record does not guarantee authorization remains valid forever.

---

# 51. External State Verification

Some task state exists outside Mikasa.

Examples:

- Git repository state.
- Files.
- Browser session.
- External API resources.
- Cloud documents.
- Database state.

Persisted Mikasa state may become stale if those environments change.

Recovery must verify important external assumptions before resuming.

---

# 52. Unknown Outcome Recovery

Consider:

```text
Mikasa sends API request.

Remote service receives request.

Connection fails before response.

Mikasa crashes.
```

The action outcome is unknown.

After restart:

Mikasa must not blindly repeat the action.

Instead:

```text
CHECK REMOTE STATE
    |
    +---- FOUND ---> RECORD SUCCESS
    |
    +---- NOT FOUND ---> CONSIDER RETRY
    |
    +---- CANNOT VERIFY ---> REQUEST INTERVENTION
```

This pattern is essential for reliable autonomous execution.

---

# 53. Resumable Tasks

A task can be considered resumable only if Mikasa has enough durable information to continue safely.

Resumability may require:

- Task objective.
- Current plan.
- Completed steps.
- Last verified outcome.
- Pending actions.
- Relevant artifacts.
- Current permission state.
- Required environment references.

Not every task can be resumed automatically.

---

# 54. Non-Resumable Operations

Some executions may rely on volatile state that cannot be safely reconstructed.

Examples:

- Temporary interactive shell sessions.
- Browser states without durable session support.
- External operations with unknown result.
- Expired authentication sessions.

The recovery process must identify these cases.

It must not fake resumability.

---

# 55. Pause

Future pause functionality should:

1. Stop scheduling new actions.
2. Allow current safe operations to settle or cancel.
3. Persist necessary state.
4. Create a checkpoint.
5. Mark the task paused.

A paused task should not continue performing new actions.

---

# 56. Resume

Resume should:

1. Load the latest valid state.
2. Validate schema.
3. Revalidate task permissions.
4. Verify important external state.
5. Resolve unknown actions.
6. Create a new execution attempt.
7. Continue from a safe point.

Resume must not simply feed the last conversation message back to the model and hope it remembers.

---

# 57. Cancellation Persistence

Cancellation must be durable.

If a user cancels a task and Mikasa restarts, the task must not unexpectedly resume.

Cancellation state must survive restart.

A new user request may explicitly create a new task or reopen work through a defined workflow.

---

# 58. Completed Tasks

Completed tasks should have immutable or strongly protected outcome records.

A completed task may still receive annotations or references.

However, its historical execution outcome should not silently change.

If additional work is needed, create a new task or explicit continuation.

---

# 59. Failed Tasks

Failure records should preserve:

- Failure category.
- Last successful step.
- Relevant error.
- Retry history.
- Remaining blocker.
- Available artifacts.

This information may support debugging and future retries.

A failed task must not automatically restart after application launch unless explicitly configured.

---

# 60. Task History

The user should eventually be able to inspect task history.

Potential fields:

```text
Task

Created

Status

Duration

Outcome

Artifacts

Errors

Related tasks
```

A fancy task dashboard is not required for MVP.

The underlying data model should make it possible later.

---

# 61. Retention

Operational state does not necessarily need to live forever.

Future retention policies may apply to:

- Old execution traces.
- Temporary artifacts.
- Expired approvals.
- Debug logs.
- Historical task records.

Retention must remain separate from explicit user memory controls.

The exact policy should be documented later.

---

# 62. Cleanup

The system should eventually clean up temporary runtime resources.

Examples:

- Old temporary directories.
- Abandoned sandbox environments.
- Expired lock files.
- Completed worker leases.
- Cached context files.

Cleanup must not remove durable task artifacts or memory accidentally.

---

# 63. Backup

Important persistent state should support backup.

Potential backup targets:

- Task database.
- Memory database.
- Configuration.
- Important artifacts.
- Decision records.

Backup strategy depends on deployment.

The MVP only needs a practical local backup story, not enterprise disaster recovery.

---

# 64. Restore

Restore procedures should account for:

- Schema versions.
- Missing artifacts.
- Deleted memories.
- Expired credentials.
- Invalid approvals.
- Incomplete tasks.

Restoring a database snapshot must not automatically reactivate historical tasks.

Task execution should remain explicitly controlled.

---

# 65. Observability

Persistence operations should emit useful events.

Potential events:

```text
state.loaded

state.saved

state.save_failed

checkpoint.created

checkpoint.loaded

recovery.started

recovery.completed

recovery.blocked

task.resumed

task.pause_requested

task.paused
```

These events should integrate with:

`docs/17_OBSERVABILITY.md`

---

# 66. MVP Scope

The first working version requires:

| Capability | MVP |
|---|---|
| Durable task IDs | Required |
| Durable task records | Required |
| Durable session records | Required |
| Execution records | Required |
| Task status persistence | Required |
| Plan persistence | Basic |
| Action records | Required for meaningful external actions |
| Cancellation persistence | Required |
| Structured final outcomes | Required |
| Application restart persistence | Required |
| Database migrations | Required |
| Full checkpoints | Deferred |
| Automatic crash recovery | Deferred |
| Pause/resume | Deferred |
| Background workers | Deferred |
| Persistent task queue | Deferred |
| Scheduler persistence | Deferred |
| Worker leases | Deferred |
| Advanced event replay | Deferred |

The MVP should create the foundation required for future recovery without pretending advanced recovery already exists.

---

# 67. MVP Persistence Scenario

The first implementation must support:

## Step 1 — Create Task

User submits:

```text
Inspect this small project and fix its failing test.
```

Mikasa creates a durable task.

## Step 2 — Execute

Mikasa performs supported actions.

Task and execution state are updated.

## Step 3 — Stop Application

The application is shut down after the task completes or at an approved test point.

## Step 4 — Restart

Mikasa restarts.

## Step 5 — Load State

The previously created task remains available.

Its:

- Task ID.
- Objective.
- Status.
- Outcome.
- Relevant execution record.

remain intact.

## Step 6 — Cancellation Test

A task marked cancelled before shutdown remains cancelled after restart.

It must not automatically resume.

---

# 68. Recovery Acceptance Scenario

When checkpoint recovery is later implemented:

1. Start a multi-step task.
2. Complete at least one verified step.
3. Create a checkpoint.
4. Interrupt the application.
5. Restart.
6. Load the task.
7. Revalidate permissions and environment.
8. Resume from the checkpoint.
9. Do not repeat completed side effects unnecessarily.
10. Complete or accurately report the blocker.

This scenario must use real persisted state.

---

# 69. Testing Requirements

## STATE-TEST-001 — Task Persistence

Verify that a task survives restart.

## STATE-TEST-002 — Session Persistence

Verify that persisted session metadata survives restart.

## STATE-TEST-003 — Execution Record

Verify that execution attempts are associated with the correct task.

## STATE-TEST-004 — Task Status

Verify that task status is stored and restored correctly.

## STATE-TEST-005 — Cancellation Persistence

Verify that cancelled tasks remain cancelled after restart.

## STATE-TEST-006 — Invalid Transition

Verify that unsupported task-state transitions are rejected.

## STATE-TEST-007 — Schema Migration

Verify that a supported migration upgrades old state correctly.

## STATE-TEST-008 — Failed Persistence

Verify that the system does not claim state was saved when persistence fails.

## STATE-TEST-009 — Parent Task

When child tasks are implemented, verify parent-child relationships persist.

## STATE-TEST-010 — Unknown Outcome

When external side effects are tracked, verify unknown outcomes are not automatically retried.

## STATE-TEST-011 — Checkpoint

When checkpoints are implemented, verify that they preserve the required state.

## STATE-TEST-012 — Recovery

When recovery is implemented, verify that completed actions are not blindly repeated.

## STATE-TEST-013 — Concurrency

When parallel execution exists, verify conflicting state writes are detected or serialized.

---

# 70. Development Sequence

**STATE-0 — Research**

Study persistence and recovery architecture from the approved research repositories.

**STATE-1 — Contracts**

Define task, execution, plan, action, and session records.

**STATE-2 — Storage**

Implement an approved durable local persistence layer.

**STATE-3 — Task Persistence**

Persist task lifecycle and outcomes.

**STATE-4 — Execution Persistence**

Persist execution attempts and relevant action state.

**STATE-5 — Session Persistence**

Persist session metadata and relationships.

**STATE-6 — Migrations**

Introduce schema-version handling.

**STATE-7 — Runtime Integration**

Connect Agent Runtime and Task Manager to persistence.

**STATE-8 — Restart Evaluation**

Verify state survives application restart.

Later phases:

**STATE-9 — Checkpoints**

**STATE-10 — Pause/Resume**

**STATE-11 — Recovery**

**STATE-12 — Background Workers**

**STATE-13 — Scheduling**

---

# 71. Research Requirements

Study relevant existing systems before finalizing the persistence model.

## Hive

Research:

- Persistent plans.
- Worker state.
- Crash recovery.
- Shared task ledger.
- Long-running executions.
- Worker ownership.

## DeerFlow

Research:

- Long-running task persistence.
- Subagent state.
- Checkpointing.
- Recovery.
- Sandbox lifecycle.

## OpenHands

Research:

- Conversation persistence.
- Workspace state.
- Agent-server lifecycle.
- Task/execution storage.

## AgenticSeek

Research:

- Session handling.
- Task state.
- Agent execution continuity.

## Gemini CLI

Research:

- Session restoration.
- Context persistence.
- Tool-call history.

The research must distinguish:

- Conversation restoration.
- Operational recovery.
- Agent memory.

They are separate concerns.

---

# 72. Architecture Decisions Required

The following must be resolved before implementation:

```text
STATE-ADR-001
Canonical task-status model.

STATE-ADR-002
Initial persistence technology.

STATE-ADR-003
Task and execution schemas.

STATE-ADR-004
Plan persistence model.

STATE-ADR-005
Action-record strategy.

STATE-ADR-006
Schema migration mechanism.

STATE-ADR-007
Concurrency-control strategy.

STATE-ADR-008
Checkpoint format.

STATE-ADR-009
Recovery model.

STATE-ADR-010
Artifact storage.

STATE-ADR-011
Future task-queue architecture.

STATE-ADR-012
Future scheduler persistence.
```

These are planning identifiers.

Approved decisions belong in:

`docs/22_DECISION_LOG.md`

---

# 73. Definition of Done

The MVP state and persistence layer is complete when:

- Tasks have durable stable identifiers.
- Sessions have durable records.
- Task state survives application restart.
- Execution attempts are recorded.
- Task outcomes are persisted.
- Cancellation state survives restart.
- Invalid task-state transitions are rejected.
- Persistence errors are surfaced accurately.
- The selected storage layer supports schema migrations.
- Agent Runtime interacts with persistence through approved interfaces.
- Conversation history, operational state, and agent memory remain architecturally distinct.
- The MVP acceptance scenario succeeds.
- Relevant automated tests pass.

Advanced recovery functionality must not be presented as complete until checkpointing, verification, and resumability have actually been implemented.

---

# 74. Final Persistence Principle

Mikasa must never depend on a model's temporary context to remember what the system has actually done.

```text
MODEL CONTEXT
     !=
TASK STATE

CHAT HISTORY
     !=
MEMORY

MEMORY
     !=
OPERATIONAL STATE

ACTION REQUEST
     !=
ACTION COMPLETED

ACTION COMPLETED
     !=
TASK COMPLETED
```

The persistence system exists so Mikasa can answer one crucial question after any interruption:

```text
WHAT ACTUALLY HAPPENED,
AND WHAT IS SAFE TO DO NEXT?
```

**Persist facts. Track actions. Checkpoint progress. Verify external state. Resume deliberately.**