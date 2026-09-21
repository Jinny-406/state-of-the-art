# M I K A S A
## Multi-Agent System Architecture

**File:** `docs/08_MULTI_AGENT_SYSTEM.md`

**Version:** 0.1.0

**Status:** PROPOSED — Pending architecture research and approval

**Authority:** Specialist agents, delegation, coordination, worker lifecycle, communication, and multi-agent execution

**Applies to:** Main Agent, Agent Manager, specialist agents, Agent Runtime, Task Manager, Memory Service, Tool System, and future long-running worker infrastructure.

---

# 1. Purpose

This document defines the proposed multi-agent architecture for M I K A S A.

The purpose of the multi-agent system is to enable Mikasa to coordinate specialized capabilities when a task benefits from delegation.

The system must allow Mikasa to:

- Identify work that benefits from specialization.
- Select appropriate specialist capabilities.
- Create scoped specialist executions.
- Delegate clearly defined tasks.
- Coordinate dependent work.
- Collect and verify results.
- Manage specialist failures.
- Track delegated work.
- Preserve task ownership.
- Control resource usage.
- Maintain appropriate memory and permission boundaries.

The multi-agent system must remain part of one coordinated assistant platform.

It must not evolve into a collection of independent agents with conflicting objectives, incompatible runtimes, or unrestricted access to shared resources.

---

# 2. Core Principle

The user primarily communicates with Mikasa.

Mikasa may internally coordinate multiple specialists.

```text
                     USER
                       |
                       v
                  M I K A S A
                       |
                       v
                  MAIN AGENT
                       |
                       v
                  AGENT MANAGER
                       |
          +------------+------------+
          |            |            |
          v            v            v
       RESEARCH      CODING       TESTING
        AGENT        AGENT        AGENT
          |            |            |
          +------------+------------+
                       |
                       v
                 TASK RESULTS
                       |
                       v
                  MAIN AGENT
                       |
                       v
                     USER
```

The main agent is responsible for coordinating work and communicating with the user.

Specialists are internal execution capabilities.

The user must not be required to manually manage a team of agents for ordinary tasks.

---

# 3. Multi-Agent Philosophy

The multi-agent system must follow these principles:

**Specialization must serve a real purpose.**

Create a specialist when it provides a meaningful advantage over direct execution.

**One task must have clear ownership.**

Delegation must not create conflicting authorities over the same task.

**Every specialist must have a bounded objective.**

A worker must know what it is expected to produce and when to stop.

**Permissions must remain scoped.**

Specialists must not automatically inherit unrestricted access to the user's environment.

**Communication must be structured.**

Workers must exchange task instructions, artifacts, and results through defined contracts.

**Execution must remain observable.**

Mikasa must be able to determine which specialist is doing what.

**Results must be verified.**

A specialist's claim of completion does not automatically mean the parent task is complete.

**Resource usage must be controlled.**

Spawning more agents must not become an uncontrolled source of model calls, tool operations, or execution cost.

---

# 4. Multi-Agent Architecture Overview

The proposed architecture uses a main agent supported by an Agent Manager and a shared execution infrastructure.

```text
                         USER
                           |
                           v
                      MAIN AGENT
                           |
                           v
                       PLANNER
                           |
                           v
                     AGENT MANAGER
                           |
               +-----------+-----------+
               |           |           |
               v           v           v
           SPECIALIST  SPECIALIST  SPECIALIST
              A           B           C
               |           |           |
               +-----------+-----------+
                           |
                           v
                   SHARED INFRASTRUCTURE
                           |
          +----------------+----------------+
          |                |                |
          v                v                v
      AGENT RUNTIME    TOOL SYSTEM      MODEL ROUTER
          |                |                |
          +----------------+----------------+
                           |
          +----------------+----------------+
          |                |                |
          v                v                v
      TASK MANAGER     MEMORY SERVICE   PERMISSIONS
                           |
                           v
                     RESULT AGGREGATION
                           |
                           v
                       MAIN AGENT
```

This represents logical responsibilities.

The initial implementation should reuse Mikasa's core runtime rather than creating an independent framework for every specialist.

---

# 5. Main Agent

The Main Agent represents Mikasa during user interaction and task coordination.

Its responsibilities include:

- Understanding the user's objective.
- Determining whether delegation is useful.
- Identifying required specialist capabilities.
- Defining specialist objectives.
- Establishing delegation boundaries.
- Monitoring delegated work.
- Handling specialist failures.
- Combining specialist results.
- Evaluating overall task completion.
- Communicating progress and final outcomes.

The Main Agent must not blindly accept specialist-generated conclusions.

It must evaluate returned results against the parent task's acceptance criteria.

---

# 6. Agent Manager

The Agent Manager is the central coordinator for specialist agents.

Suggested component:

`AgentManager`

Its responsibilities include:

- Maintaining specialist definitions.
- Discovering available specialist capabilities.
- Validating delegation requests.
- Creating specialist execution contexts.
- Assigning delegated tasks.
- Tracking worker lifecycle.
- Enforcing delegation limits.
- Coordinating cancellation.
- Receiving structured results.
- Reporting specialist status.

The Agent Manager must integrate with the existing Task Manager and Agent Runtime.

It must not establish a conflicting, independent task-state system.

---

# 7. Specialist Agent

A specialist agent is an agent execution configured for a particular responsibility.

Examples:

```text
Research Specialist

Coding Specialist

Testing Specialist

Debugging Specialist

Browser Specialist

Planning Specialist

Analysis Specialist

Documentation Specialist
```

Specialists may differ in:

- Instructions.
- Available tools.
- Model configuration.
- Relevant context.
- Assigned task.
- Memory access.
- Execution limits.
- Expected output.

These differences do not require each specialist to have its own independent runtime implementation.

---

# 8. Specialist Agents vs. Tools

A specialist agent and a tool are different concepts.

A tool performs a defined operation.

A specialist agent may plan and execute multiple operations to achieve a delegated objective.

Example tool:

```text
filesystem.read_file
```

Example specialist:

```text
Coding Specialist

Objective:
Investigate a failing test and propose a verified fix.
```

The Coding Specialist may use several authorized tools during its execution.

Do not create a specialist agent for a task that can be completed through one straightforward tool call.

Specialization should be justified by the complexity or nature of the work.

---

# 9. Agent Definition

Each specialist should have a structured definition.

Proposed conceptual schema:

```text
AgentDefinition:
    agent_type_id
    name
    description

    capabilities
    instructions

    required_tools
    optional_tools

    memory_access_policy
    permission_requirements

    preferred_model_profile
    default_execution_limits

    input_contract
    output_contract

    version
```

These fields describe a reusable specialist type.

An agent definition is not the same as an active agent instance.

The final schema must be defined during implementation.

---

# 10. Agent Instance

An agent instance represents one active or historical specialist execution.

Proposed structure:

```text
AgentInstance:
    agent_instance_id
    agent_type_id

    parent_task_id
    delegated_task_id
    execution_id

    objective
    configuration

    status
    started_at
    completed_at

    result_reference
```

An instance must have a stable identity.

The system must be able to associate every specialist with its parent task and execution.

Agent instances must not automatically persist indefinitely after their assigned work is complete.

---

# 11. Agent Lifecycle

A specialist should follow a defined lifecycle.

```text
DEFINED
   |
   v
SELECTED
   |
   v
INITIALIZING
   |
   v
RUNNING
   |
   +------> WAITING_APPROVAL
   |
   +------> FAILED
   |
   +------> CANCELLED
   |
   v
COMPLETED
   |
   v
RESULT RETURNED
   |
   v
RELEASED
```

These are conceptual lifecycle states.

The final implementation must distinguish specialist-instance state from task and execution state.

Completed workers should release unnecessary runtime resources.

Their useful outputs may remain available through the appropriate task, artifact, or memory store.

---

# 12. Dynamic Specialist Creation

Mikasa should eventually support selecting or creating specialist configurations based on task requirements.

Dynamic creation does not mean generating arbitrary unrestricted agents.

A specialist must be created from an approved execution template or validated configuration.

The Agent Manager must determine:

1. What capability is required?
2. Is an existing specialist suitable?
3. Is delegation justified?
4. What tools are necessary?
5. What context is relevant?
6. What permissions are available?
7. What execution limits apply?
8. What output is expected?

The system should prefer reusing existing specialist definitions when suitable.

A new specialist definition should be created only when there is a clear need.

---

# 13. Capability-Based Specialist Selection

Specialists should be selected according to their capabilities.

Example:

```text
USER OBJECTIVE:
Build a small application.

REQUIRED CAPABILITIES:
    Requirements analysis
    Software implementation
    Testing
```

Possible delegation:

```text
MAIN AGENT
    |
    +----> REQUIREMENTS SPECIALIST
    |
    +----> CODING SPECIALIST
    |
    +----> TESTING SPECIALIST
```

The Main Agent may also perform some of these responsibilities directly.

The system must not automatically spawn three specialists when one agent can complete the task efficiently and reliably.

---

# 14. Specialist Selection Criteria

Potential selection criteria include:

- Required capabilities.
- Available tools.
- Task complexity.
- Existing specialist definitions.
- Model capability.
- Expected resource usage.
- Execution environment.
- Permission requirements.
- Previous evaluated performance.

Selection must not rely exclusively on a specialist's name or its self-description.

Future performance-based selection must use observable evaluation results rather than unsupported model-generated rankings.

---

# 15. Delegation Decision

Before delegating, the Main Agent should determine whether delegation is useful.

Delegation is appropriate when:

- The task requires specialized capabilities.
- A bounded independent subtask can be identified.
- The specialist has an appropriate execution environment.
- The result can be returned through a clear contract.
- The coordination overhead is justified.
- Available permissions support the delegated work.

Delegation may be unnecessary when:

- The task is simple.
- The work cannot be meaningfully separated.
- The specialist would duplicate the Main Agent's actions.
- Additional agents would create avoidable coordination complexity.
- The required resources are unavailable.

Multi-agent execution is a tool for completing work, not an objective by itself.

---

# 16. Delegation Contract

Every delegated task must use a structured contract.

Proposed schema:

```text
DelegationRequest:
    delegation_id

    parent_task_id
    parent_execution_id

    specialist_type

    objective
    context

    required_capabilities
    allowed_tools

    permission_scope

    constraints
    execution_budget

    expected_output
    verification_criteria

    created_at
```

The request must clearly identify what the specialist should accomplish.

The specialist must not reinterpret the delegated task as permission to pursue unrelated objectives.

---

# 17. Delegation Scope

A specialist must receive the minimum scope needed to complete its assignment.

Example:

```text
PARENT OBJECTIVE:
Fix the application's failing tests.

SPECIALIST:
Testing Agent

DELEGATED OBJECTIVE:
Run the relevant tests and identify the failing cases.

ALLOWED:
Read project files.
Execute the approved test command.
Return structured test results.

NOT ALLOWED:
Rewrite the application.
Modify unrelated files.
Install unrelated dependencies.
Change project architecture.
```

The parent task's permissions form an upper bound.

Delegation may narrow permissions but must not silently expand them.

---

# 18. Delegation Depth

The system must control recursive delegation.

Example:

```text
MAIN AGENT
    |
    v
CODING SPECIALIST
    |
    v
TESTING SPECIALIST
```

Recursive delegation can be useful, but it introduces additional complexity and resource usage.

The system must eventually support a configurable maximum delegation depth.

The initial multi-agent implementation should use a simple hierarchy.

Unlimited specialist spawning is prohibited.

---

# 19. Specialist Execution Context

Every specialist must receive an isolated execution context.

It should contain:

```text
SpecialistExecutionContext:
    agent_instance_id

    delegated_task_id
    parent_task_id

    objective
    relevant_context

    assigned_capabilities
    allowed_tools

    permission_context
    memory_scope

    execution_limits
    cancellation_context
```

A specialist must not automatically inherit:

- Every user memory.
- All project memories.
- Unrestricted credentials.
- Every registered tool.
- The parent agent's complete conversation history.
- Permissions unrelated to the delegated objective.

The context must be assembled according to the specialist's actual requirements.

---

# 20. Shared Runtime Infrastructure

Specialists should reuse Mikasa's existing infrastructure.

```text
SPECIALIST AGENT
       |
       v
AGENT RUNTIME
       |
       +----> MODEL ROUTER
       |
       +----> TOOL SYSTEM
       |
       +----> MEMORY SERVICE
       |
       +----> TASK MANAGER
       |
       +----> PERMISSION SERVICE
       |
       +----> EVENT SERVICE
```

Specialists may have different configurations, but they should not independently implement duplicate versions of these systems.

For example, the Coding Specialist must use the same permission-aware Tool Executor as the Main Agent.

---

# 21. Agent Communication

Agent communication should use structured task messages and results.

Do not use unrestricted conversational exchanges as the only coordination mechanism.

Possible communication types:

```text
TASK_ASSIGNMENT

TASK_PROGRESS

TASK_RESULT

BLOCKER_REPORT

APPROVAL_REQUEST

CANCELLATION_REQUEST

ARTIFACT_REFERENCE
```

Every communication should be associated with the relevant task and execution.

Messages must not automatically grant additional permissions.

---

# 22. Communication Contract

Proposed message structure:

```text
AgentMessage:
    message_id

    sender_agent_id
    recipient_agent_id

    parent_task_id
    delegated_task_id

    message_type
    payload

    created_at
```

The implementation should distinguish between:

- Instructions from an authorized coordinating agent.
- Informational messages from specialists.
- Tool results.
- Untrusted external content.

A specialist's message must not automatically override higher-priority user instructions or application permissions.

---

# 23. Direct Agent-to-Agent Communication

Direct communication between specialists may eventually be useful.

However, uncontrolled peer-to-peer conversations can create:

- Conflicting instructions.
- Duplicate work.
- Unclear task ownership.
- Excessive model usage.
- Missing operational records.
- Circular delegation.

The initial multi-agent implementation should prefer communication through the Agent Manager and structured task records.

Direct specialist communication may be introduced later when an explicit use case justifies it.

---

# 24. Delegated Task Ownership

Each delegated task must have one authoritative lifecycle.

The Task Manager owns task state.

The Agent Manager coordinates specialist assignment and execution.

The specialist performs the assigned work.

The Main Agent remains responsible for the parent objective.

A specialist must not independently mark the parent task complete.

Completion of a delegated task means only that the specialist's assigned objective has reached a recorded outcome.

The Main Agent must evaluate whether the overall parent objective is satisfied.

---

# 25. Task Hierarchy

The system should support parent-child relationships between tasks.

Conceptually:

```text
PARENT TASK
    |
    +---- CHILD TASK A
    |
    +---- CHILD TASK B
    |
    +---- CHILD TASK C
```

Each child task should have:

- A unique task ID.
- A parent task reference.
- A defined objective.
- A responsible execution.
- A status.
- An expected result.
- A completion record.

The parent task must not be marked complete solely because all child tasks report success.

The combined results must satisfy the parent task's acceptance criteria.

---

# 26. Task Dependencies

Some delegated tasks may depend on others.

Example:

```text
RESEARCH
    |
    v
IMPLEMENTATION
    |
    v
TESTING
    |
    v
FINAL VERIFICATION
```

The Agent Manager must not schedule dependent work before its prerequisites are satisfied.

Future versions may support dependency graphs.

The initial implementation may use simple sequential delegation.

---

# 27. Parallel Specialist Execution

Independent specialist tasks may eventually run concurrently.

Example:

```text
                   MAIN AGENT
                        |
             +----------+----------+
             |                     |
             v                     v
       RESEARCH AGENT        DOCUMENTATION AGENT
             |                     |
             +----------+----------+
                        |
                        v
                  RESULT REVIEW
```

Parallel execution must account for:

- Shared resources.
- File modification conflicts.
- Task dependencies.
- Permission scopes.
- Resource budgets.
- Cancellation.
- Result consistency.

Two specialists must not independently modify the same resource without an approved coordination mechanism.

Parallel execution is not required for the first multi-agent milestone.

---

# 28. Shared Workspace Coordination

Specialists may need access to the same project.

The system must avoid conflicting modifications.

Possible strategies include:

- Read-only specialist access.
- Separate working directories.
- Isolated version-control branches.
- Explicit resource ownership.
- Serialized write operations.
- Controlled change integration.

The final strategy must be selected during the relevant implementation phase.

The Agent Manager must not assume concurrent modifications are safe merely because they originate from different agents.

---

# 29. Specialist Result Contract

Every delegated execution must return a structured result.

Proposed schema:

```text
SpecialistResult:
    delegation_id

    delegated_task_id
    agent_instance_id

    status

    summary
    outputs
    artifact_references

    verification_results
    errors
    blockers

    completed_at
```

The result must distinguish between:

- Completed work.
- Partially completed work.
- Failed work.
- Blocked work.
- Cancelled work.

The result must not claim success solely because the specialist generated a plausible response.

---

# 30. Result Aggregation

The Main Agent must be able to combine specialist results into one coherent task outcome.

Conceptually:

```text
SPECIALIST A RESULT
          |
SPECIALIST B RESULT
          |
SPECIALIST C RESULT
          |
          v
    RESULT AGGREGATION
          |
          v
    CONSISTENCY CHECK
          |
          v
     FINAL VERIFICATION
          |
          v
       MAIN AGENT
          |
          v
         USER
```

Aggregation should identify:

- Completed delegated objectives.
- Remaining work.
- Conflicting results.
- Missing artifacts.
- Failed verification.
- Unresolved blockers.

The Main Agent must not hide specialist failures when they affect the parent objective.

---

# 31. Result Verification

Specialist output must be verified according to the delegated task's acceptance criteria.

Examples:

**Coding Specialist**

Verify relevant file changes and test results.

**Research Specialist**

Check required source references and whether the research questions were answered.

**Testing Specialist**

Inspect actual test execution records.

**Browser Specialist**

Check the resulting application or page state.

**Documentation Specialist**

Check that required sections and constraints are satisfied.

A specialist's self-reported confidence must not be treated as independent proof of correctness.

---

# 32. Specialist Failure Handling

Specialists may fail for various reasons.

Examples:

- Model-provider failure.
- Tool failure.
- Missing capability.
- Permission denial.
- Execution timeout.
- Invalid output.
- Resource exhaustion.
- Failed verification.

The Agent Manager must record the actual outcome.

The Main Agent may then:

- Retry the delegated task within approved limits.
- Revise the task.
- Select a suitable alternative specialist.
- Complete the remaining work directly.
- Report a blocker.
- Request user intervention.

Recovery must remain within the parent task's authorized scope.

---

# 33. Specialist Replacement

Mikasa may eventually replace an unsuccessful specialist execution with another suitable execution.

Replacement may be appropriate when:

- The specialist repeatedly fails to produce a valid result.
- A required capability is unavailable.
- The selected configuration is incompatible with the task.
- A different approved specialist is better suited to the required work.

Replacement must not automatically imply creating an entirely new permanent agent definition.

The system should first determine whether the failure resulted from:

- An invalid task.
- Missing permissions.
- Insufficient context.
- A tool failure.
- A model limitation.
- A specialist configuration problem.

A specialist must not be replaced simply to bypass a legitimate permission denial.

---

# 34. Worker Supervision

Future versions should include worker supervision.

Possible responsibilities:

- Track active specialist executions.
- Detect stalled workers.
- Enforce execution limits.
- Propagate cancellation.
- Handle worker failures.
- Release resources.
- Record results.
- Prevent duplicate task execution.

Worker supervision must integrate with the Task Manager and Agent Runtime.

Do not introduce a completely separate task system for specialist workers.

---

# 35. Cancellation Propagation

When the user cancels a parent task, the system must handle associated specialist executions.

The cancellation process should:

1. Stop scheduling new delegated work.
2. Notify active specialist executions.
3. Request cancellation of supported operations.
4. Record actual worker outcomes.
5. Preserve completed artifacts where appropriate.
6. Update parent task status.
7. Report any operations that could not be cancelled.

Cancellation must not be represented as successful rollback of already completed external actions.

---

# 36. Resource Budgets

Multi-agent execution must use explicit resource limits.

Potential limits include:

```text
MAX_ACTIVE_SPECIALISTS

MAX_DELEGATION_DEPTH

MAX_TOTAL_MODEL_REQUESTS

MAX_TOTAL_TOOL_CALLS

MAX_EXECUTION_TIME

MAX_RETRIES

MAX_RESOURCE_USAGE

MAX_PROVIDER_COST
```

Child tasks must operate within the resource budget allocated by their parent task.

Delegation must not automatically multiply the total resource budget.

The Agent Manager must prevent unlimited recursive specialist creation.

---

# 37. Model Selection

Different specialists may eventually use different models.

Example:

```text
RESEARCH SPECIALIST
    -> Configured research-capable model

CODING SPECIALIST
    -> Configured coding-capable model

TESTING SPECIALIST
    -> Configured model appropriate to test analysis
```

Model selection must use the Model Router.

Specialists must not independently hard-code provider-specific API calls.

The initial multi-agent implementation may use the same configured model for all specialists.

Advanced model routing is a separate capability.

---

# 38. Memory Integration

Specialists may require memory to complete their work.

Possible memory sources include:

- Relevant project memory.
- Approved task context.
- Selected procedural memory.
- Validated skills.
- Authorized shared memory.

Memory must be retrieved through the Memory Service.

Specialists must not access memory databases directly.

Memory permissions must be checked before records are returned.

---

# 39. Agent-Specific Memory

A specialist may maintain temporary working information during its execution.

Examples:

- Research notes.
- Intermediate coding observations.
- Test results.
- Temporary task context.

This information should normally remain scoped to the specialist execution or its delegated task.

Selected information may be promoted to project or shared memory through the approved memory-writing process.

A specialist's temporary context must not automatically become permanent user memory.

---

# 40. Shared Memory

Shared memory allows authorized specialists to access relevant information.

Conceptually:

```text
                 MEMORY SERVICE
                       |
          +------------+------------+
          |            |            |
          v            v            v
       RESEARCH      CODING       TESTING
        AGENT        AGENT        AGENT
```

Shared memory must have explicit ownership and access policies.

The existence of a shared namespace does not authorize unrestricted access to every stored record.

Specialists should receive only the information necessary for their assigned objectives.

---

# 41. Memory Write Conflicts

Multiple specialists may produce conflicting information.

Example:

A research specialist records one dependency version while a coding specialist observes another installed version.

The Memory Service must not blindly overwrite one record with the other.

Conflict handling should consider:

- Source.
- Timestamp.
- Scope.
- Verification status.
- Existing memory version.
- Whether one record supersedes another.

Shared memory must not become a collection of contradictory claims treated as equally authoritative facts.

---

# 42. Specialist Instructions

Each specialist should have a focused instruction set.

The instruction set should define:

- Role.
- Objective.
- Capabilities.
- Constraints.
- Expected output.
- Verification requirements.
- Escalation conditions.

Avoid enormous static prompts containing irrelevant responsibilities.

Specialist instructions must not override higher-priority runtime rules or user authorization.

A specialist definition must not contain hidden permission escalation instructions.

---

# 43. Dynamic Role Definitions

Future versions may allow Mikasa to propose new specialist definitions.

Example:

A task requires an unusual combination of capabilities that existing specialists do not provide.

The Main Agent may propose a temporary specialist configuration.

The configuration must be validated before execution.

It must specify:

- Actual required capabilities.
- Approved tools.
- Permission scope.
- Execution limits.
- Input contract.
- Output contract.

Dynamically generated role descriptions must not grant additional authority.

The initial multi-agent implementation should use a small set of approved specialist definitions.

---

# 44. Specialist Registry

The Agent Manager should maintain a registry of available specialist definitions.

Conceptual interface:

```text
AgentRegistry:
    register(agent_definition)

    get(agent_type_id)

    list_available(capabilities)

    disable(agent_type_id)
```

A specialist definition must not be considered executable merely because it exists in the registry.

The system must also verify:

- Required tools are available.
- Model configuration is valid.
- Dependencies are satisfied.
- Permissions can be granted.
- Execution limits are configured.

---

# 45. Specialist Availability

Specialists may have different availability states.

Possible states:

```text
AVAILABLE

UNAVAILABLE

UNCONFIGURED

DISABLED

DEGRADED
```

The Agent Manager must distinguish between registered specialists and operational specialists.

If a specialist cannot execute, the Main Agent should receive an accurate availability result.

The system must not simulate successful specialist execution when the underlying capability is unavailable.

---

# 46. Multi-Agent Observability

Every specialist execution must be traceable.

Potential events:

```text
agent.selected

agent.created

agent.started

agent.delegated

agent.progress_updated

agent.completed

agent.failed

agent.cancelled

agent.released
```

Operational records should include:

- Parent task ID.
- Delegated task ID.
- Agent instance ID.
- Execution ID.
- Specialist type.
- Start and completion times.
- Execution status.
- Relevant resource usage.
- Result reference.

The interface may display specialist activity without exposing hidden model reasoning.

---

# 47. User Experience

Multi-agent execution must not require the user to manage internal agent communication.

The user should primarily see:

- The main objective.
- Meaningful task progress.
- Important delegated work.
- Pending approvals.
- Relevant blockers.
- Verified results.

For example:

```text
M I K A S A

Current task:
Fix the application's failing tests.

Active work:
Research Specialist — Checking dependency documentation.

Coding Specialist — Investigating the failing module.

Testing Specialist — Waiting for the proposed fix.
```

This information should represent real runtime state.

Do not display fake workers or decorative progress as actual agent activity.

---

# 48. Multi-Agent vs. Single-Agent Decision

The system must avoid unnecessary delegation.

A conceptual decision process:

```text
TASK RECEIVED
      |
      v
ANALYZE REQUIREMENTS
      |
      v
IS SPECIALIZATION USEFUL?
      |
      +---- NO ----> SINGLE-AGENT EXECUTION
      |
      v
CAN WORK BE DELEGATED CLEARLY?
      |
      +---- NO ----> SINGLE-AGENT EXECUTION
      |
      v
ARE CAPABILITIES AVAILABLE?
      |
      +---- NO ----> DIRECT EXECUTION OR BLOCKER
      |
      v
IS DELEGATION AUTHORIZED?
      |
      +---- NO ----> DIRECT EXECUTION OR BLOCKER
      |
      v
DELEGATE SCOPED TASK
```

The system should not equate more agents with better performance.

---

# 49. Relationship to Other Subsystems

The Multi-Agent System must integrate with existing Mikasa components.

**Agent Runtime**

Executes specialist tasks.

**Task Manager**

Owns task and execution state.

**Planner**

Identifies work suitable for delegation.

**Model Router**

Provides model access.

**Tool System**

Provides authorized capabilities.

**Memory Service**

Provides scoped memory access.

**Permission Service**

Enforces delegated permissions.

**Event Service**

Provides operational observability.

**Persistence Layer**

Stores supported task and execution state.

No specialist should independently bypass these components.

---

# 50. MVP Boundary

The first working version of Mikasa does not require a complete multi-agent system.

The MVP must focus on establishing a reliable single-agent runtime.

The architecture should preserve future delegation compatibility through:

- Stable task identifiers.
- Structured execution contexts.
- Explicit permission contexts.
- Reusable model interfaces.
- Reusable tool interfaces.
- Clear task outcomes.

Do not implement specialist workers, agent swarms, or parallel orchestration during the initial MVP unless a separate approved task explicitly changes the scope.

---

# 51. First Multi-Agent Milestone

The first multi-agent implementation should remain deliberately small.

Suggested initial configuration:

```text
MAIN AGENT
    |
    +----> RESEARCH SPECIALIST
    |
    +----> CODING SPECIALIST
    |
    +----> TESTING SPECIALIST
```

The implementation should support:

- A small approved specialist registry.
- Structured delegation requests.
- One delegated task at a time.
- Shared runtime infrastructure.
- Scoped permissions.
- Structured specialist results.
- Parent-task result integration.
- Cancellation propagation.
- Basic operational logging.

Dynamic specialist generation, recursive delegation, and parallel execution should remain deferred until this simpler system is verified.

---

# 52. First Multi-Agent Acceptance Scenario

The initial multi-agent system must demonstrate a real delegated workflow.

**User objective:**

"Mikasa, investigate this application's failing tests, identify the problem, implement a fix, and verify the result."

Expected behavior:

```text
USER REQUEST
      |
      v
MAIN AGENT
      |
      v
CREATE PLAN
      |
      v
DELEGATE INVESTIGATION
      |
      v
RESEARCH / CODING SPECIALIST
      |
      v
RETURN FINDINGS
      |
      v
MAIN AGENT EVALUATES FINDINGS
      |
      v
IMPLEMENT APPROVED CHANGE
      |
      v
DELEGATE TESTING
      |
      v
TESTING SPECIALIST
      |
      v
RETURN TEST RESULTS
      |
      v
MAIN AGENT VERIFIES OUTCOME
      |
      v
REPORT TO USER
```

The test must use actual registered specialist executions.

Hard-coded specialist messages do not satisfy the acceptance criteria.

The system must also handle a specialist failure without incorrectly marking the parent task complete.

---

# 53. Multi-Agent Testing Requirements

## MA-TEST-001 — Specialist Registration

Verify that a valid specialist definition can be registered.

## MA-TEST-002 — Specialist Selection

Verify that the Agent Manager can identify a suitable specialist based on required capabilities.

## MA-TEST-003 — Delegation Contract

Verify that delegation requests contain the required objective, context, permissions, and output requirements.

## MA-TEST-004 — Parent-Child Relationship

Verify that delegated tasks retain the correct parent-task reference.

## MA-TEST-005 — Permission Isolation

Verify that a specialist cannot access capabilities outside its delegated permission scope.

## MA-TEST-006 — Memory Isolation

Verify that specialist memory access respects the assigned task, project, and agent scopes.

## MA-TEST-007 — Result Return

Verify that specialist executions return structured results to the coordinating agent.

## MA-TEST-008 — Failure Propagation

Verify that specialist failures are reported without incorrectly completing the parent task.

## MA-TEST-009 — Cancellation Propagation

Verify that cancellation of a parent task stops scheduling new delegated work and requests cancellation of active specialists.

## MA-TEST-010 — Resource Limits

Verify that delegated executions respect the allocated execution budget.

## MA-TEST-011 — Recursive Delegation

When recursive delegation is implemented, verify that configured delegation-depth limits are enforced.

## MA-TEST-012 — Parallel Conflict Handling

When parallel execution is implemented, verify that conflicting resource modifications are prevented or handled correctly.

## MA-TEST-013 — Integration

Verify that the Main Agent can delegate a real task, receive results, and complete the parent objective through the shared runtime.

---

# 54. Development Sequence

The Multi-Agent System should be implemented incrementally.

**MA-0 — Research**

Study agent coordination and worker-management patterns from the approved research repositories.

**MA-1 — Agent Definitions**

Define specialist roles, capability metadata, and execution contracts.

**MA-2 — Agent Registry**

Implement specialist registration and availability checks.

**MA-3 — Delegation Contracts**

Define structured delegation requests and results.

**MA-4 — Agent Manager**

Implement specialist selection and lifecycle coordination.

**MA-5 — Shared Runtime Integration**

Execute delegated tasks through Mikasa's existing Agent Runtime.

**MA-6 — Task and Memory Integration**

Connect delegated execution to the Task Manager and scoped Memory Service.

**MA-7 — Permission Enforcement**

Ensure specialist permissions remain within their delegated scope.

**MA-8 — Result Aggregation**

Allow the Main Agent to incorporate specialist results into the parent task.

**MA-9 — Evaluation**

Run integration tests for delegation, failure handling, cancellation, and result verification.

Future milestones may introduce parallel execution, recursive delegation, dynamic specialist generation, and advanced worker supervision.

---

# 55. Research Requirements

Before finalizing the multi-agent implementation, research the following repositories.

## Hive

https://github.com/aden-hive/hive

Research:

- Main-agent and worker coordination.
- Persistent task plans.
- Worker lifecycle.
- Shared task state.
- Recovery.
- Resource management.
- Human oversight.

## DeerFlow

https://github.com/bytedance/deer-flow

Research:

- Lead-agent architecture.
- Subagent delegation.
- Agent execution contexts.
- Tool and skill sharing.
- Long-running workflows.
- Sandboxing.

## Agency Agents

https://github.com/msitarzewski/agency-agents

Research:

- Specialist role definitions.
- Capability descriptions.
- Role selection.
- Reusable agent configurations.

Do not assume that a large collection of static specialist prompts must be imported.

## OpenHands

https://github.com/OpenHands/OpenHands

Research:

- Agent execution environments.
- Multi-agent hosting.
- Task isolation.
- Structured execution.

## AgenticSeek

https://github.com/Fosowl/agenticSeek

Research:

- Agent selection.
- Specialist capabilities.
- Task routing.
- Tool coordination.

Research findings must distinguish verified implementation details from project documentation and unverified assumptions.

---

# 56. Architecture Decisions Required

The following decisions must be resolved before their respective implementation work begins:

```text
MULTIAGENT-001
Specialist definition schema.

MULTIAGENT-002
Agent Registry implementation.

MULTIAGENT-003
Delegation request and result contracts.

MULTIAGENT-004
Task hierarchy and ownership.

MULTIAGENT-005
Specialist execution isolation.

MULTIAGENT-006
Memory-sharing policy.

MULTIAGENT-007
Permission inheritance rules.

MULTIAGENT-008
Worker lifecycle and supervision.

MULTIAGENT-009
Resource budget allocation.

MULTIAGENT-010
Parallel execution strategy.

MULTIAGENT-011
Dynamic specialist creation.

MULTIAGENT-012
Failure recovery and specialist replacement.
```

These are planning references.

Approved decisions must be recorded in:

`docs/22_DECISION_LOG.md`

---

# 57. Definition of Done

The first Multi-Agent System milestone is complete when:

- A specialist definition can be registered.
- The Agent Manager can select an appropriate specialist.
- A valid delegation request can be created.
- A specialist executes through the existing Agent Runtime.
- Delegated permissions are enforced.
- Relevant memory scopes are enforced.
- Parent-child task relationships are preserved.
- Specialists return structured results.
- The Main Agent can incorporate delegated results.
- Specialist failures are handled correctly.
- Cancellation propagates appropriately.
- Resource limits are enforced.
- Specialist activity is observable.
- The integrated acceptance scenario succeeds.
- Relevant automated tests pass.

Advanced functionality must not be presented as implemented unless it has been separately developed and verified.

---

# 58. Final Multi-Agent Principle

Mikasa must remain one coherent assistant, even when multiple specialists are involved.

Specialist agents are internal capabilities that help complete user objectives.

They are not independent authorities, unrestricted workers, or permanent personalities that must exist for every possible task.

```text
USER
  |
  v
M I K A S A
  |
  v
UNDERSTAND GOAL
  |
  v
DETERMINE REQUIRED CAPABILITIES
  |
  v
DELEGATE WHEN USEFUL
  |
  v
COORDINATE SPECIALISTS
  |
  v
VERIFY RESULTS
  |
  v
REPORT TO USER
```

**One assistant. Clear task ownership. Scoped specialists. Shared infrastructure. Verified outcomes.**

The system should become more capable through specialization without becoming unnecessarily complicated.
