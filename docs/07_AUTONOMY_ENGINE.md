# M I K A S A
## Autonomy Engine Specification

**File:** `docs/07_AUTONOMY_ENGINE.md`

**Version:** 0.1.0

**Status:** PROPOSED — Pending architecture research and approval

**Authority:** Autonomous task planning, decision-making, execution coordination, progress evaluation, and goal completion

**Applies to:** Main agent, Agent Runtime, Planner, Task Manager, specialist agents, task orchestration, and long-running autonomous workflows.

---

# 1. Purpose

This document defines the architecture and operational behavior of Mikasa's Autonomy Engine.

The Autonomy Engine enables Mikasa to transform a high-level user goal into a sequence of coordinated actions while maintaining progress, respecting permissions, adapting to new information, and verifying outcomes.

Its purpose is to allow Mikasa to perform meaningful multi-step work without requiring the user to manually direct every individual action.

This document defines:

- Autonomous goal execution.
- Goal interpretation.
- Task decomposition.
- Planning and replanning.
- Action selection.
- Dependency management.
- Execution coordination.
- Progress monitoring.
- Failure detection.
- Recovery strategies.
- Verification.
- Completion criteria.
- User intervention.
- Long-running autonomy.
- Integration with memory and specialist agents.
- Execution budgets.
- Autonomy evaluation.

The implementation must remain consistent with the Project Charter, PRD, System Architecture, and Agent Runtime specifications.

---

# 2. Core Autonomy Principle

Mikasa should operate according to the following conceptual lifecycle:

```text
USER GOAL
    |
    v
UNDERSTAND OBJECTIVE
    |
    v
IDENTIFY REQUIRED OUTCOME
    |
    v
RETRIEVE RELEVANT CONTEXT
    |
    v
CREATE EXECUTION PLAN
    |
    v
IDENTIFY REQUIRED CAPABILITIES
    |
    v
EXECUTE NEXT ACTION
    |
    v
OBSERVE RESULT
    |
    v
EVALUATE PROGRESS
    |
    +---- FAILURE ------> RECOVER / REPLAN
    |
    +---- BLOCKED ------> REQUEST INTERVENTION
    |
    +---- INCOMPLETE ---> CONTINUE
    |
    v
VERIFY FINAL OUTCOME
    |
    v
COMPLETE AND REPORT
```

This lifecycle represents the intended autonomous behavior of Mikasa.

The actual implementation may use a simpler execution process for straightforward tasks.

Not every user request requires a detailed plan or multiple tool calls.

---

# 3. Definition of Autonomy

Within Mikasa, autonomy means the ability to pursue an authorized objective through multiple actions without requiring unnecessary user intervention.

Autonomy includes:

- Interpreting goals.
- Selecting appropriate capabilities.
- Determining the next useful action.
- Creating and updating plans.
- Coordinating dependent operations.
- Responding to tool results.
- Identifying failures.
- Selecting recovery strategies.
- Tracking progress.
- Determining when completion criteria are satisfied.

Autonomy does not grant unlimited authority.

The system must operate within the permissions, resource budgets, task boundaries, and user instructions applicable to the current execution.

---

# 4. Autonomy Levels

Mikasa should support progressively more capable forms of autonomous execution.

These levels describe product capabilities, not unrestricted permission levels.

## Level 0 — Direct Response

Mikasa responds without executing external actions.

Examples:

- Answering a general question.
- Explaining a concept.
- Summarizing information already provided.

No multi-step execution plan is required.

---

## Level 1 — Single-Action Execution

Mikasa performs one authorized tool operation.

Example:

"Read this project configuration file."

Execution:

```text
UNDERSTAND
    |
    v
SELECT TOOL
    |
    v
EXECUTE
    |
    v
RETURN RESULT
```

---

## Level 2 — Multi-Step Execution

Mikasa performs several dependent actions to complete one objective.

Example:

"Find the configuration file, update this setting, and verify the change."

The agent must use the result of each relevant action to determine what to do next.

This is the initial autonomy target for the MVP.

---

## Level 3 — Adaptive Execution

Mikasa can revise its execution strategy when observations invalidate the original plan.

Example:

A coding task encounters a failing dependency.

Mikasa investigates the failure, determines whether a correction is within the approved task scope, and adjusts its plan accordingly.

Adaptive execution must remain bounded.

---

## Level 4 — Persistent Autonomous Tasks

Mikasa can maintain tasks beyond a single foreground interaction.

Capabilities may include:

- Persistent task queues.
- Checkpoints.
- Pause and resume.
- Scheduled execution.
- Recovery after interruption.
- Notifications.
- Resource budgets.

This level requires durable state and appropriate background execution infrastructure.

---

## Level 5 — Coordinated Multi-Agent Execution

Mikasa can delegate appropriately scoped work to specialist agents.

Possible capabilities:

- Specialist selection.
- Structured delegation.
- Dependency management.
- Parallel execution.
- Result aggregation.
- Worker supervision.

This is a future capability.

It must not be introduced before the core single-agent runtime is reliable.

---

# 5. Architecture Overview

The Autonomy Engine is a logical coordination capability built on top of Mikasa's existing runtime services.

It must not become a second independent agent framework.

```text
                  USER GOAL
                      |
                      v
                MAIN AGENT
                      |
                      v
               AUTONOMY LOGIC
                      |
        +-------------+-------------+
        |             |             |
        v             v             v
      GOAL         PLANNER       PROGRESS
    ANALYSIS                     EVALUATION
        |             |             |
        +-------------+-------------+
                      |
                      v
                TASK MANAGER
                      |
                      v
                AGENT RUNTIME
                      |
          +-----------+-----------+
          |           |           |
          v           v           v
       MODELS        TOOLS       MEMORY
          |           |           |
          +-----------+-----------+
                      |
                      v
                 OBSERVATIONS
                      |
                      v
               AUTONOMY LOGIC
                      |
                      v
             CONTINUE / FINISH
```

The diagram represents responsibilities rather than mandatory separate services.

The MVP may implement goal analysis and progress evaluation as focused functions within the Agent Runtime.

---

# 6. Component Responsibilities

The autonomy architecture must preserve clear ownership boundaries.

| Component | Responsibility |
|---|---|
| Main Agent | Represents Mikasa to the user |
| Goal Analyzer | Interprets the objective and its constraints |
| Planner | Creates and revises execution plans |
| Task Manager | Owns task lifecycle and canonical status |
| Agent Runtime | Coordinates model and tool execution |
| Progress Evaluator | Compares observed results against the plan |
| Recovery Controller | Selects permitted responses to failures |
| Verification Controller | Evaluates completion evidence |
| Memory Service | Retrieves and stores selected information |
| Permission Service | Determines whether proposed actions are authorized |
| Agent Manager | Coordinates future specialist delegation |

These are logical responsibilities.

Do not implement all of them as independent classes or services before their complexity justifies separation.

---

# 7. Goal Representation

Every autonomous task must have a clearly defined objective.

A goal should identify what the user wants Mikasa to accomplish.

Proposed conceptual schema:

```text
Goal:
    goal_id
    task_id

    objective
    context

    constraints
    required_outcomes
    success_criteria

    allowed_capabilities
    permission_reference

    created_at
    status
```

The actual implementation schema must be defined during the relevant development phase.

The goal must preserve the user's requested outcome without unnecessarily expanding its scope.

---

# 8. Goal Interpretation

When Mikasa receives a goal, she should determine:

1. What is the requested outcome?
2. What information is already available?
3. What information is missing?
4. Which capabilities are required?
5. Which actions are authorized?
6. What constraints apply?
7. What evidence would demonstrate success?
8. Does the goal require a plan?
9. Can the available system realistically perform the requested work?

The goal interpretation process should avoid unnecessary clarification when the request is sufficiently clear.

However, Mikasa must not invent missing authorization, credentials, or critical requirements.

---

# 9. Goal Constraints

Each autonomous task must operate within explicit constraints.

Possible constraints include:

```text
ALLOWED WORKSPACE

AVAILABLE TOOLS

PERMISSIONS

EXECUTION BUDGET

TIME LIMIT

RESOURCE LIMIT

TASK SCOPE

USER REQUIREMENTS

VERIFICATION REQUIREMENTS
```

The Autonomy Engine must not remove or weaken these constraints merely because doing so could make task completion easier.

Constraints must remain available throughout execution.

They must not depend solely on the model remembering them.

---

# 10. Goal Decomposition

Complex goals may be decomposed into smaller tasks or steps.

Example objective:

"Inspect this project, identify why the tests fail, fix the issue, and verify that the application works."

Possible decomposition:

```text
GOAL
 |
 +-- STEP 1: INSPECT PROJECT
 |
 +-- STEP 2: IDENTIFY TEST COMMAND
 |
 +-- STEP 3: RUN TESTS
 |
 +-- STEP 4: ANALYZE FAILURE
 |
 +-- STEP 5: DETERMINE SOLUTION
 |
 +-- STEP 6: IMPLEMENT CHANGE
 |
 +-- STEP 7: VERIFY RESULT
 |
 +-- STEP 8: REPORT OUTCOME
```

The plan must remain focused on the user's actual objective.

Decomposition must not create unnecessary tasks merely to make the plan appear sophisticated.

---

# 11. Task Decomposition Rules

Every generated step should satisfy the following requirements:

- It contributes directly to the parent objective.
- Its expected outcome is understandable.
- Its dependencies can be identified.
- Its required capabilities are available or explicitly marked as missing.
- Its completion can be evaluated.
- It remains within the task's authorized scope.

The Planner must not silently introduce unrelated product features or additional objectives.

A newly discovered dependency may be added to the plan if it is necessary and authorized.

Unrelated improvements must remain outside the active task.

---

# 12. Plan Representation

Plans should use structured data rather than relying exclusively on free-form model-generated prose.

Proposed schema:

```text
ExecutionPlan:
    plan_id
    task_id
    version

    objective

    steps
    dependencies

    current_step_id

    status
    created_at
    updated_at
```

Each step may contain:

```text
PlanStep:
    step_id
    description

    dependencies
    required_capabilities

    expected_result
    verification_criteria

    status
    attempts

    result_reference
```

The final schemas must be compatible with the Agent Runtime and Task Manager.

The Planner must not independently maintain a conflicting authoritative task lifecycle.

---

# 13. Plan Granularity

Plans must be detailed enough to guide execution without becoming unnecessarily rigid.

For simple tasks:

```text
READ FILE
    |
    v
RETURN CONTENT
```

For complex tasks:

```text
INSPECT
    |
    v
RESEARCH
    |
    v
PLAN
    |
    v
IMPLEMENT
    |
    v
TEST
    |
    v
VERIFY
```

Do not require dozens of tiny planning steps for trivial work.

The level of detail should reflect task complexity, risk, and dependencies.

---

# 14. Planning Strategy

The initial implementation may use a simple planning strategy.

Future implementations may introduce:

- Hierarchical planning.
- Dependency-aware planning.
- Incremental planning.
- Parallel task planning.
- Replanning based on observations.
- Planning across multiple specialist agents.

The architecture must not assume that every plan can be generated completely before execution begins.

Some tasks reveal important information only after initial actions.

Mikasa must be able to incorporate those observations into subsequent decisions.

---

# 15. Execution Strategy

The Autonomy Engine must support two primary execution patterns.

## Direct Execution

Suitable for straightforward tasks.

The Agent Runtime selects and performs the required action without generating an elaborate plan.

## Planned Execution

Suitable for tasks containing multiple meaningful steps.

The Planner provides a structured plan.

The Agent Runtime executes actions toward that plan.

The Autonomy Engine evaluates observations and determines whether the plan should continue or be revised.

The initial version should use the simplest strategy appropriate to the objective.

---

# 16. Action Selection

At each execution step, Mikasa must determine the next appropriate action.

Possible actions include:

```text
REQUEST MODEL RESPONSE

INVOKE TOOL

RETRIEVE MEMORY

UPDATE PLAN

VERIFY RESULT

REQUEST APPROVAL

REPORT BLOCKER

COMPLETE TASK
```

The selected action must be supported by the current runtime.

A model-generated action that references an unavailable capability must not be treated as executable.

The system must not invent successful tool results for unavailable capabilities.

---

# 17. Capability Matching

The Autonomy Engine should determine which capabilities are required to complete the current step.

Example:

```text
TASK:
    Fix the failing tests.

REQUIRED CAPABILITIES:
    Read project files.
    Search source code.
    Execute tests.
    Modify project files.
```

The tool system determines which implementations are available and authorized.

Capability matching must distinguish:

- Available capabilities.
- Missing capabilities.
- Unauthorized capabilities.
- Temporarily unavailable capabilities.

If a required capability is missing, the engine may select an authorized alternative.

It must not bypass a denied permission by choosing another tool with equivalent access.

---

# 18. Dependency Management

A plan may contain steps that depend on previous steps.

Example:

```text
INSPECT PROJECT
      |
      v
IDENTIFY TEST COMMAND
      |
      v
EXECUTE TESTS
      |
      v
ANALYZE FAILURE
      |
      v
IMPLEMENT FIX
      |
      v
VERIFY
```

A dependent step must not execute before its required prerequisites are satisfied.

Future implementations may support parallel execution of independent steps.

Parallel execution must account for shared resources, conflicting changes, permission scopes, and task-state consistency.

The MVP may execute steps sequentially.

---

# 19. Progress Tracking

Autonomous execution must maintain meaningful progress information.

Progress should be based on observed task state rather than the model's subjective claim that it has made progress.

Possible progress data:

```text
Progress:
    task_id
    plan_id

    completed_steps
    active_step
    pending_steps

    last_successful_action
    current_blocker

    attempts
    updated_at
```

Progress should be accessible to the user through the available interface.

The system should not invent a precise completion percentage when the amount of remaining work is unknown.

---

# 20. Observation Processing

After an action, Mikasa must evaluate the resulting observation.

An observation may indicate:

```text
ACTION SUCCEEDED

ACTION FAILED

ACTION TIMED OUT

ACTION WAS DENIED

ACTION WAS CANCELLED

ACTION OUTCOME UNKNOWN

NEW INFORMATION DISCOVERED
```

The system must distinguish an action's execution result from the overall task outcome.

For example:

A web search returning results does not mean the research objective is complete.

A file modification succeeding does not mean the application defect has been fixed.

Observations should inform the next action rather than automatically concluding the task.

---

# 21. Progress Evaluation

The Progress Evaluator compares the current task state against the plan and expected outcomes.

It should consider:

- Which steps are complete?
- Which steps remain?
- Were expected results observed?
- Did new information invalidate the plan?
- Is the task making meaningful progress?
- Has the execution encountered a blocker?
- Are resource limits approaching?
- Is the objective ready for final verification?

The evaluator must use available execution evidence.

It must not treat unsupported model confidence as evidence of success.

---

# 22. Replanning

Replanning allows Mikasa to adapt when the original plan is no longer appropriate.

Potential triggers:

- A required tool becomes unavailable.
- A prerequisite is missing.
- A test reveals an unexpected failure.
- The current approach repeatedly fails.
- New information changes the understanding of the problem.
- A planned action is no longer necessary.
- The user changes the objective or constraints.

Replanning must preserve the original authorized goal unless the user explicitly changes it.

---

# 23. Replanning Procedure

The proposed replanning procedure:

```text
OBSERVE PROBLEM
      |
      v
IDENTIFY FAILED ASSUMPTION
      |
      v
REVIEW CURRENT GOAL
      |
      v
REVIEW COMPLETED STEPS
      |
      v
IDENTIFY AVAILABLE OPTIONS
      |
      v
CHECK SCOPE AND PERMISSIONS
      |
      v
REVISE REMAINING PLAN
      |
      v
PRESERVE PLAN HISTORY
      |
      v
CONTINUE EXECUTION
```

Completed external actions must not be assumed reversible.

The revised plan must account for the actual environment state.

Plan revisions should maintain version information or an equivalent audit record when persistence is supported.

---

# 24. Replanning Limits

Mikasa must not replan indefinitely.

The runtime should support a configurable limit on repeated replanning.

Repeated plan revisions that fail to produce progress should trigger a blocker assessment.

Possible responses:

- Investigate a different root cause.
- Request missing information.
- Report an unavailable capability.
- Explain a permission limitation.
- Return a partial result.
- Terminate the execution.

Replanning must not silently increase the task's resource budget or authorization.

---

# 25. Failure Detection

The Autonomy Engine must detect failures that prevent useful progress.

Possible failures include:

```text
TOOL FAILURE

MODEL FAILURE

INVALID ACTION

MISSING DEPENDENCY

PERMISSION DENIAL

TIMEOUT

RESOURCE LIMIT

FAILED VERIFICATION

PLAN INCONSISTENCY

REPEATED UNPRODUCTIVE ACTIONS
```

The engine should distinguish between an individual failed action and a task that cannot continue.

A failed action may be recoverable.

A task becomes blocked when the available authorized strategies cannot satisfy its requirements.

---

# 26. Failure Classification

Failures should be classified according to their effect on execution.

## Recoverable Failure

The system has a reasonable, authorized recovery strategy.

Example:

A temporary model-provider error may be retried within configured limits.

## Non-Recoverable Failure

The system cannot safely or reasonably continue under the current conditions.

Example:

A required capability is unavailable and no authorized alternative exists.

## Permission Blocker

The requested action requires permission that has not been granted.

## Missing Information

Essential information is unavailable.

## Resource Exhaustion

Execution has reached an applicable limit.

## Unknown Outcome

An external action may have completed, but its result cannot be confirmed.

Unknown-outcome actions require appropriate verification before retrying.

---

# 27. Recovery Strategy

The Autonomy Engine should select recovery actions according to the failure type.

Potential recovery actions:

```text
RETRY

CORRECT ARGUMENTS

SELECT AUTHORIZED ALTERNATIVE

RETRIEVE ADDITIONAL INFORMATION

REVISE PLAN

REQUEST USER INPUT

REPORT BLOCKER

TERMINATE TASK
```

Recovery must remain within the approved task scope.

The engine must not attempt to bypass security restrictions or conceal failures.

---

# 28. Retry Policy

Retries must be bounded and appropriate to the operation.

Before retrying, Mikasa should determine:

- Why did the action fail?
- Is the failure temporary?
- Is repeating the action safe?
- Could the first action already have succeeded?
- Would retrying produce a duplicate external effect?
- Has the retry limit been reached?

Read-only operations may often be safely repeated.

External operations with side effects may require result verification or an idempotency mechanism.

Permission denials must not be treated as ordinary retryable failures.

---

# 29. Stuck Detection

Mikasa must eventually recognize when execution is no longer making meaningful progress.

Potential stuck conditions:

- The same tool fails repeatedly with equivalent arguments.
- Replanning produces the same unsuccessful plan.
- The agent repeatedly requests unavailable capabilities.
- The current step cannot satisfy its expected outcome.
- Execution continues without producing new relevant information.
- A task repeatedly alternates between the same unsuccessful actions.

The engine must avoid interpreting repeated activity as meaningful progress.

When stuck, it should attempt a bounded alternative strategy or report the blocker.

---

# 30. Permission-Aware Autonomy

Autonomy must operate within trusted authorization boundaries.

The Autonomy Engine may propose actions.

The Permission Service determines whether those actions are allowed.

The Tool Executor enforces permissions at the execution boundary.

A model-generated plan does not grant permission to perform every step it contains.

If approval is required, Mikasa must request it before executing the affected action.

A denied operation must not trigger an attempt to reach the same prohibited result through another tool.

---

# 31. Human Intervention

Mikasa should request user intervention when necessary.

Examples:

- The task requires a new permission.
- Essential information is missing.
- A sensitive external action needs approval.
- Multiple materially different outcomes require a user decision.
- A required action exceeds the existing task scope.
- Recovery cannot safely continue.

The assistant should explain what decision or information is required.

It should preserve relevant task state where the current implementation supports waiting or resumption.

User intervention must not be requested for every ordinary tool call when the action is already authorized.

---

# 32. Task Completion

A task must not be marked completed solely because the model states that it is finished.

Completion requires evidence that the approved objective has been satisfied.

The completion procedure should evaluate:

1. Required plan steps.
2. Expected outcomes.
3. Verification results.
4. Outstanding errors.
5. Unresolved blockers.
6. User-defined acceptance criteria.

The Task Manager records the authoritative final task status.

The Autonomy Engine may propose completion but must not bypass the task-state contract.

---

# 33. Partial Completion

Some tasks may be partially completed.

Example:

Mikasa successfully identifies the cause of a software problem but cannot apply the required fix because a dependency is unavailable.

The system should preserve the successful findings and report the remaining blocker.

A partial outcome must not be presented as full completion.

If the canonical Task Manager does not include a separate partial-completion status, partial results must be represented through structured outcome metadata.

The system should not invent incompatible task states.

---

# 34. Verification Strategy

Verification must be appropriate to the task.

Examples:

**Coding**

Run relevant tests and inspect the resulting changes.

**Filesystem**

Read the modified file or confirm the expected resource state.

**Research**

Check source relevance and whether the required questions were answered.

**Browser**

Inspect the resulting page or application state.

**External service**

Confirm the resulting operation through an approved verification method when available.

Verification must distinguish observed evidence from model-generated conclusions.

---

# 35. Execution Budgets

Autonomous work must use explicit execution limits.

Potential limits:

```text
MAXIMUM_STEPS

MAXIMUM_MODEL_REQUESTS

MAXIMUM_TOOL_CALLS

MAXIMUM_RETRIES

MAXIMUM_REPLANS

MAXIMUM_EXECUTION_TIME

MAXIMUM_COST

MAXIMUM_PARALLEL_TASKS
```

Not every limit must be implemented in the MVP.

The initial runtime must enforce bounded steps, tool timeouts, retries, and cancellation.

Additional controls should be introduced as the runtime becomes more capable.

The agent must not silently increase its own limits.

---

# 36. Budget Exhaustion

When a task reaches its execution budget, Mikasa must:

1. Stop scheduling new actions.
2. Preserve supported task state.
3. Record the reason execution stopped.
4. Report completed work.
5. Identify remaining work.
6. Return a structured outcome.

The assistant may explain what additional resources or permissions would be needed to continue.

It must not silently restart the same task with a new budget.

---

# 37. Cancellation

The user must be able to cancel active autonomous work.

Cancellation should:

- Stop new action scheduling.
- Request termination of active operations where supported.
- Preserve completed results.
- Record the actual cancellation outcome.
- Release appropriate resources.
- Inform the user.

Cancellation does not automatically reverse completed external actions.

The system must not claim an operation was undone unless reversal was performed and verified.

---

# 38. Pause and Resume

Pause and resume are future capabilities.

The architecture should allow an autonomous task to enter a paused state while preserving sufficient execution information.

Possible retained information:

```text
TASK ID

CURRENT PLAN

COMPLETED STEPS

PENDING STEPS

LAST OBSERVATION

CHECKPOINT

EXECUTION BUDGET

PERMISSION CONTEXT
```

Resumption must evaluate the current environment before repeating previously attempted actions.

Full pause/resume functionality is not required for the first working version.

---

# 39. Long-Running Tasks

Mikasa should eventually support tasks extending beyond a single conversation turn.

Long-running tasks may require:

- Durable task records.
- A persistent task queue.
- Worker lifecycle management.
- Checkpoints.
- Recovery.
- Scheduling.
- Notifications.
- Resource limits.
- Permission persistence rules.

The architecture must not depend on keeping one model response open for the entire duration of a long-running task.

These capabilities belong to later implementation phases.

---

# 40. Background Execution

Future versions may allow Mikasa to continue authorized tasks independently of an active user interface.

Background execution must use actual persistent execution infrastructure.

A model claiming that it will continue working does not constitute a background task.

Background jobs must have:

- Stable task identifiers.
- Explicit authorization.
- An execution owner.
- Resource limits.
- A cancellation mechanism.
- Observable status.
- Persistent results.

The initial MVP does not require a complete background worker system.

---

# 41. Proactive Behavior

Mikasa may eventually support proactive assistance.

Examples:

- Reporting completion of a scheduled task.
- Notifying the user about a relevant task failure.
- Identifying a previously authorized recurring maintenance need.
- Presenting useful follow-up information.

Proactive behavior must respect user preferences, task scope, permissions, and notification settings.

The system must not initiate unrestricted external actions merely because a model believes they may be useful.

Proactive execution is a future capability.

---

# 42. Memory Integration

The Autonomy Engine should retrieve relevant memory when it contributes to the current objective.

Potential memory sources:

```text
PROJECT MEMORY

USER MEMORY

EPISODIC MEMORY

SEMANTIC MEMORY

PROCEDURAL MEMORY

SKILL MEMORY
```

Memory retrieval must respect the authorized task context.

Retrieved memory may inform planning and action selection.

It must not override trusted runtime instructions or permission policies.

After task completion, selected information may be stored according to the Memory Architecture specification.

---

# 43. Learning From Execution

Future versions may use completed task outcomes to improve recurring workflows.

Potential learning signals include:

- Verified success.
- Repeated failures.
- Tool reliability.
- Workflow efficiency.
- Successful recovery strategies.
- User corrections.
- Evaluation results.

These signals may produce memory or skill candidates.

The system must not automatically promote every completed workflow into an executable skill.

Learning must use the approved memory and self-improvement mechanisms.

---

# 44. Specialist-Agent Integration

The Autonomy Engine should eventually support delegating suitable work to specialist agents.

Conceptually:

```text
USER GOAL
    |
    v
MAIN AGENT
    |
    v
TASK DECOMPOSITION
    |
    v
SPECIALIST SELECTION
    |
    v
SCOPED DELEGATION
    |
    v
SPECIALIST EXECUTION
    |
    v
RESULT VALIDATION
    |
    v
MAIN AGENT CONTINUES
```

Specialists must receive scoped objectives, relevant context, appropriate permissions, and execution limits.

Delegation does not remove the main task's authorization or verification requirements.

Advanced delegation is outside the MVP scope.

---

# 45. Delegation Decision

Future versions should delegate when specialist execution offers a meaningful advantage.

Possible reasons:

- A task requires specialized capabilities.
- Independent subtasks can be performed concurrently.
- A specialist has a relevant approved workflow.
- The task benefits from isolated execution.
- Delegation reduces unnecessary context complexity.

Do not spawn specialist agents for every trivial operation.

A single-agent workflow is preferable when it can complete the task reliably without additional coordination overhead.

---

# 46. Delegation Boundaries

Every delegated task must define:

```text
PARENT TASK

SPECIALIST OBJECTIVE

ALLOWED CAPABILITIES

PERMISSION SCOPE

RELEVANT CONTEXT

EXECUTION BUDGET

EXPECTED OUTPUT

VERIFICATION CRITERIA
```

Specialists must not automatically inherit every permission or memory available to the main agent.

Recursive delegation must have explicit depth and resource limits.

The main agent remains responsible for integrating specialist results into the parent task.

---

# 47. Parallel Execution

Future versions may execute independent plan steps in parallel.

Parallel execution requires:

- Dependency validation.
- Shared-resource coordination.
- Conflict handling.
- Task-state consistency.
- Cancellation propagation.
- Permission enforcement.
- Result aggregation.

For example, two specialists should not independently overwrite the same project file without an approved coordination mechanism.

The initial autonomy implementation may execute actions sequentially.

---

# 48. Autonomy and Tool Discovery

The Autonomy Engine should cooperate with the Tool System to identify available capabilities.

The engine must distinguish between:

```text
CAPABILITY REQUIRED

CAPABILITY AVAILABLE

CAPABILITY AUTHORIZED

CAPABILITY EXECUTED

RESULT VERIFIED
```

A missing capability may create a blocker.

The engine may search for an approved alternative within the current task scope.

Future dynamic extension discovery must follow the Tool System's installation and permission rules.

---

# 49. Autonomy and Scope Control

The Autonomy Engine must preserve the user's actual objective.

It must not interpret autonomy as permission to expand tasks indefinitely.

Example:

**User objective:**

"Fix the login bug."

Authorized autonomous behavior may include:

- Inspecting relevant code.
- Reproducing the issue.
- Identifying the cause.
- Implementing an appropriate fix.
- Running relevant tests.

Unauthorized scope expansion would include:

- Redesigning the entire application.
- Replacing the authentication architecture without approval.
- Adding unrelated features.
- Rewriting the frontend.
- Migrating the database without an approved requirement.

Autonomy means independently completing the authorized objective.

It does not mean choosing unrelated objectives.

---

# 50. Autonomy and User Control

The user must remain able to understand and control Mikasa's work.

The system should expose:

- Current objective.
- Task status.
- Meaningful progress.
- Pending approvals.
- Important blockers.
- Available cancellation controls.
- Final results.

Future interfaces may provide pause/resume, task history, execution timelines, and specialist activity.

Operational transparency must not require exposing hidden model reasoning.

---

# 51. Observability

Important autonomy decisions should produce useful operational records.

Potential events:

```text
goal.accepted

plan.created

plan.revised

step.started

step.completed

step.failed

progress.updated

recovery.started

recovery.failed

approval.requested

task.blocked

task.verification_started

task.verification_failed

task.completed
```

Events should contain enough information to associate them with the relevant task and execution.

Logs must not unnecessarily expose sensitive data.

---

# 52. Autonomy Metrics

Autonomy should be evaluated using observable task outcomes.

Suggested metrics:

| Metric | Measurement |
|---|---|
| Goal completion | Percentage of evaluation goals completed successfully |
| Verification success | Percentage of completed tasks satisfying their acceptance criteria |
| Recovery success | Percentage of selected recoverable failures handled successfully |
| Step efficiency | Actions used relative to an appropriate reference workflow |
| Repeated failure rate | Frequency of repeated unsuccessful actions |
| Permission compliance | Unauthorized actions executed during evaluation |
| Cancellation correctness | Successful termination of cancellable tasks |
| Resource compliance | Executions remaining within configured limits |
| Plan consistency | Frequency of unsupported or contradictory step transitions |

Numerical targets must be established after defining evaluation tasks and measuring baseline performance.

---

# 53. MVP Autonomy Scope

The first working version must support a functional but bounded autonomy workflow.

Required capabilities:

| Capability | MVP |
|---|---|
| Goal interpretation | Required |
| Basic multi-step planning | Required |
| Sequential execution | Required |
| Tool selection | Required |
| Observation processing | Required |
| Progress tracking | Required |
| Basic failure handling | Required |
| Bounded retries | Required |
| Execution limits | Required |
| Task cancellation | Required |
| Basic result verification | Required |
| Structured task outcomes | Required |
| Advanced hierarchical planning | Deferred |
| Persistent background jobs | Deferred |
| Parallel execution | Deferred |
| Dynamic specialist delegation | Deferred |
| Automatic extension installation | Deferred |
| Autonomous self-modification | Deferred |

Do not implement deferred features simply because they appear in this document.

---

# 54. MVP Acceptance Scenario

The initial Autonomy Engine must demonstrate a real multi-step workflow.

**User objective:**

"Mikasa, inspect this small Python project, identify why its tests are failing, fix the problem, and verify the result."

Expected behavior:

```text
1. ACCEPT OBJECTIVE

2. INSPECT PROJECT

3. IDENTIFY TEST COMMAND

4. RUN TESTS

5. OBSERVE FAILURE

6. IDENTIFY POSSIBLE CAUSE

7. CREATE OR UPDATE PLAN

8. IMPLEMENT AUTHORIZED CHANGE

9. RUN TESTS AGAIN

10. VERIFY RESULT

11. REPORT OUTCOME
```

The test must use actual tools and an approved project workspace.

The implementation must also demonstrate an appropriate response when:

- The test command fails unexpectedly.
- The required file is missing.
- An unauthorized operation is requested.
- The execution budget is exhausted.
- The task is cancelled.

The engine must report the actual outcome rather than fabricate completion.

---

# 55. Autonomy Testing Requirements

The following tests must cover the relevant implemented capabilities.

## AUT-TEST-001 — Goal Interpretation

Verify that the system preserves the requested objective and relevant constraints.

## AUT-TEST-002 — Basic Decomposition

Verify that a multi-step goal produces a usable sequence of actions.

## AUT-TEST-003 — Dependency Ordering

Verify that dependent steps are not executed before their prerequisites are satisfied.

## AUT-TEST-004 — Tool Selection

Verify that the engine selects available tools appropriate to the current step.

## AUT-TEST-005 — Missing Capability

Verify that unavailable capabilities produce an accurate blocker or authorized alternative.

## AUT-TEST-006 — Observation Processing

Verify that tool results influence subsequent actions.

## AUT-TEST-007 — Plan Revision

When replanning is implemented, verify that new observations can produce a revised plan without losing completed work.

## AUT-TEST-008 — Retry Limits

Verify that repeated failures cannot trigger unlimited retries.

## AUT-TEST-009 — Stuck Detection

When stuck detection is implemented, verify that repeated unproductive behavior triggers an appropriate recovery or termination outcome.

## AUT-TEST-010 — Permission Enforcement

Verify that autonomous planning cannot authorize actions outside the granted permission scope.

## AUT-TEST-011 — Cancellation

Verify that cancellation prevents additional actions and records the correct outcome.

## AUT-TEST-012 — Budget Exhaustion

Verify that execution stops when the applicable resource or step limit is reached.

## AUT-TEST-013 — Verification

Verify that the task is not marked completed when required acceptance criteria remain unsatisfied.

## AUT-TEST-014 — Partial Outcome

Verify that completed work and unresolved blockers are represented accurately.

## AUT-TEST-015 — Scope Preservation

Verify that the engine does not execute unrelated work merely because a model proposed an additional feature.

## AUT-TEST-016 — Integrated Execution

Verify that the Agent Runtime and Autonomy Engine can complete a representative multi-step task using actual registered tools.

---

# 56. Development Sequence

Autonomy should be implemented incrementally.

**AUT-0 — Research**

Study relevant existing agents and determine the initial planning and execution approach.

**AUT-1 — Goal Contracts**

Define objective representation, constraints, required outcomes, and acceptance criteria.

**AUT-2 — Basic Planning**

Implement simple task decomposition and plan representation.

**AUT-3 — Runtime Integration**

Connect planning and next-action selection to the existing Agent Runtime.

**AUT-4 — Progress Tracking**

Record meaningful step status and execution progress.

**AUT-5 — Error Handling**

Implement bounded recovery and appropriate blocker reporting.

**AUT-6 — Verification**

Evaluate observed results against the goal's acceptance criteria.

**AUT-7 — Execution Controls**

Enforce cancellation, limits, and scope boundaries.

**AUT-8 — Integrated Evaluation**

Demonstrate a real multi-step task from objective to verified outcome.

Future milestones may introduce advanced planning, checkpoint recovery, background execution, specialist delegation, and parallel workflows.

---

# 57. Architecture Research Requirements

Before finalizing the implementation strategy, research the relevant autonomous agent systems.

Priority areas:

**AgenticSeek**

Investigate autonomous task execution, planning, agent selection, and tool coordination.

**DeerFlow**

Investigate long-running agent workflows, planning, orchestration, and execution architecture.

**Hive**

Investigate persistent task plans, worker coordination, recovery, and supervision.

**OpenHands**

Investigate autonomous coding workflows, task execution, and environment interaction.

**Gemini CLI**

Investigate agent execution loops, tool invocation, and context management.

**ZCode**

Investigate coding-agent planning, execution, and error handling.

Research must distinguish verified implementation details from documentation claims and architectural assumptions.

The final architecture should adopt relevant ideas without automatically importing an entire external framework.

---

# 58. Architecture Decisions Required

The following decisions must be resolved before their respective implementation work begins:

```text
AUTONOMY-001
Initial planning strategy.

AUTONOMY-002
Goal and plan schemas.

AUTONOMY-003
Plan ownership and persistence.

AUTONOMY-004
Progress evaluation approach.

AUTONOMY-005
Recovery and retry policy.

AUTONOMY-006
Execution budget configuration.

AUTONOMY-007
Verification contract.

AUTONOMY-008
Long-running execution strategy.

AUTONOMY-009
Specialist delegation strategy.

AUTONOMY-010
Parallel execution and dependency coordination.
```

These are planning references rather than approved Architecture Decision Record identifiers.

Approved decisions must be recorded in:

`docs/22_DECISION_LOG.md`

---

# 59. Definition of Done

The MVP Autonomy Engine is complete when:

- A user can provide a meaningful multi-step objective.
- Mikasa preserves the objective and its constraints.
- The system can form a usable execution plan.
- The Agent Runtime can execute the plan through real tools.
- Observations influence subsequent actions.
- Meaningful progress is tracked.
- Execution respects permissions.
- Retries are bounded.
- Execution limits are enforced.
- Task cancellation works.
- Failures produce accurate outcomes.
- The system can report blockers.
- Completion is evaluated against defined acceptance criteria.
- The Task Manager records the correct final task status.
- An integrated evaluation demonstrates real multi-step execution.
- Relevant automated tests pass.
- Future capabilities are not falsely presented as implemented.

---

# 60. Final Autonomy Principle

Mikasa's autonomy must be based on purposeful execution rather than uncontrolled activity.

The engine should understand what needs to be accomplished, choose appropriate actions, observe actual results, and adapt when necessary.

It must recognize the difference between making progress and merely performing more actions.

```text
M I K A S A

UNDERSTAND THE GOAL
        |
        v
CREATE A PLAN
        |
        v
SELECT AN ACTION
        |
        v
EXECUTE
        |
        v
OBSERVE
        |
        v
EVALUATE
        |
        +---- FAILURE ---> RECOVER
        |
        +---- BLOCKED ---> REQUEST INTERVENTION
        |
        +---- INCOMPLETE -> CONTINUE
        |
        v
VERIFY SUCCESS
        |
        v
REPORT RESULTS
```

**Autonomy means independently pursuing an authorized goal, not independently inventing new goals.**

Mikasa must be capable of continuing useful work while preserving user control, execution boundaries, and verifiable outcomes.
