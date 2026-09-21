# M I K A S A
## Task Protocol & Execution Rules

**File:** `docs/21_TASK_PROTOCOL.md`

**Version:** 0.1.0

**Status:** PROPOSED — Pending architecture research and approval

**Authority:** Development-task creation, task scoping, execution workflow, implementation boundaries, verification, reporting, and closure

**Applies to:** Coding agents, human contributors, implementation tasks, bug fixes, refactors, architecture work, research spikes, tests, documentation tasks, and maintenance work.

---

# 1. Purpose

This document defines how development work is performed inside the M I K A S A repository.

Every implementation task should have:

- A clear objective.
- A reason for existing.
- Explicit scope.
- Explicit non-scope.
- Expected files or components.
- Acceptance criteria.
- Verification requirements.
- Completion evidence.

The Task Protocol exists to prevent:

- Wandering development.
- Accidental scope expansion.
- Unplanned architecture changes.
- Unrelated refactoring.
- Duplicate systems.
- Fake completion.
- Coding without understanding.
- Coding agents independently inventing product direction.

---

# 2. Core Task Principle

Every task should answer:

```text
WHAT ARE WE CHANGING?

WHY?

WHERE?

WHAT MUST WORK AFTERWARD?

WHAT MUST NOT CHANGE?

HOW WILL WE PROVE IT WORKS?
```

If these cannot be answered, the task is not ready for implementation.

---

# 3. Task Hierarchy

Development work follows this hierarchy:

```text
PROJECT VISION
      |
      v
APPROVED PHASE
      |
      v
CURRENT TASK
      |
      v
IMPLEMENTATION
      |
      v
TESTING
      |
      v
VERIFICATION
      |
      v
COMPLETION
```

A lower level must not silently redefine a higher level.

For example:

A coding task may not redefine the system architecture without going through the architecture decision process.

---

# 4. Authoritative Task File

The currently approved development task lives in:

```text
plans/CURRENT_TASK.md
```

Coding agents must treat this file as the primary implementation instruction after project-wide architecture and governance documents.

Only work required by the active task is authorized by default.

---

# 5. Current Phase

Before reading the current task, the agent must inspect:

```text
plans/CURRENT_PHASE.md
```

The task must belong to the active phase unless explicitly approved otherwise.

Example:

```text
Current Phase:
Phase 4 — Tool Runtime

Current Task:
Implement ToolRegistry
```

Valid.

Example:

```text
Current Phase:
Phase 4 — Tool Runtime

Current Task:
Build 3D avatar animation system
```

Invalid unless the phase itself has been intentionally changed.

---

# 6. Required Reading Order

Before implementation, a coding agent should read:

```text
AGENTS.md

docs/00_PROJECT_CHARTER.md

docs/01_PRD.md

docs/02_SCOPE_AND_NON_GOALS.md

plans/CURRENT_PHASE.md

plans/CURRENT_TASK.md
```

Then read the architecture documents directly relevant to the task.

Example:

For Tool Registry:

```text
docs/03_SYSTEM_ARCHITECTURE.md

docs/06_TOOL_SYSTEM.md

docs/10_SECURITY_PERMISSIONS.md

docs/17_OBSERVABILITY.md

docs/18_TESTING_AND_EVALS.md
```

Do not reread the entire documentation tree for every tiny change unless necessary.

---

# 7. Task Types

A development task should be classified.

Suggested types:

```text
FEATURE

BUG_FIX

REFACTOR

TEST

RESEARCH_SPIKE

ARCHITECTURE

DOCUMENTATION

MAINTENANCE

SECURITY

PERFORMANCE
```

Task type influences required evidence.

---

# 8. Task Identity

Each meaningful task should eventually have a stable identifier.

Example:

```text
TASK-TOOL-004
```

or another approved format.

The exact ID scheme will be finalized later.

Task IDs help connect:

- Plans.
- Commits.
- Tests.
- Architecture decisions.
- Completion reports.
- Backlog items.

---

# 9. Task Template

`plans/CURRENT_TASK.md` should use a structure similar to:

```text
# Current Task

ID:
TASK-...

Title:
Implement ToolRegistry

Type:
FEATURE

Phase:
Phase 4 — Tool Runtime

Status:
ACTIVE

Objective:
Implement a registry capable of registering,
validating, resolving, and listing tool definitions.

Why:
The Agent Runtime needs a centralized
capability registry before real tool execution.

In Scope:
- ToolDefinition contract
- Tool registration
- duplicate ID handling
- tool lookup
- tool listing
- tool availability state
- unit tests

Out of Scope:
- executing tools
- MCP
- plugins
- browser tools
- automatic tool installation

Expected Components:
- ToolRegistry
- ToolDefinition

Expected Files:
- src/...
- tests/...

Acceptance Criteria:
- ...
- ...

Required Verification:
- unit tests
- integration test

Relevant Docs:
- docs/06_TOOL_SYSTEM.md
- docs/10_SECURITY_PERMISSIONS.md

Known Constraints:
- ...

Completion Report:
Pending
```

Exact formatting may evolve.

The information must remain explicit.

---

# 10. Task Status

Development tasks may use:

```text
DRAFT

READY

ACTIVE

BLOCKED

VERIFYING

COMPLETE

CANCELLED

DEFERRED
```

Suggested lifecycle:

```text
DRAFT
  |
  v
READY
  |
  v
ACTIVE
  |
  v
VERIFYING
  |
  +----> ACTIVE
  |
  v
COMPLETE
```

A task may become:

```text
BLOCKED
```

from `ACTIVE` or `VERIFYING`.

---

# 11. DRAFT

A task is `DRAFT` when:

- Objective is still forming.
- Scope may change.
- Acceptance criteria are incomplete.
- Architecture decisions may still be unresolved.

Implementation should not begin.

---

# 12. READY

A task becomes `READY` when:

- Objective is clear.
- Scope is defined.
- Dependencies are satisfied.
- Acceptance criteria exist.
- Relevant architecture is understood.
- No unresolved blocker prevents implementation.

---

# 13. ACTIVE

A task becomes `ACTIVE` when implementation begins.

At this point:

- Scope should remain stable.
- Changes should be traceable.
- Discoveries may refine implementation details.
- Major scope changes require explicit review.

---

# 14. VERIFYING

A task enters `VERIFYING` when implementation is believed to satisfy requirements.

During verification:

- Tests run.
- Diff is reviewed.
- Acceptance criteria are checked.
- Documentation impact is checked.
- Security impact is checked.

If a criterion fails:

```text
VERIFYING
   |
   v
ACTIVE
```

The task returns to implementation.

---

# 15. COMPLETE

A task becomes `COMPLETE` only when it satisfies:

`docs/20_DEFINITION_OF_DONE.md`

Completion must be evidence-backed.

---

# 16. BLOCKED

A task becomes `BLOCKED` when progress requires something unavailable.

Examples:

```text
missing architecture decision

missing permission

missing dependency

unavailable environment

external system unavailable

required information unavailable
```

A blocked task should record:

- What is blocked.
- Why.
- What has already been completed.
- What would unblock it.

---

# 17. CANCELLED

A development task may be cancelled if:

- It is no longer needed.
- Product direction changes.
- A better approach replaces it.
- Phase scope changes.

Cancelled work should not silently remain half-integrated.

Any partial implementation should be:

- Reverted.
- Isolated.
- Or explicitly documented.

---

# 18. One Primary Objective

Each current task should have one primary objective.

Bad task:

```text
Implement tool system,
redesign UI,
add memory,
refactor config,
and support MCP.
```

Better:

```text
Implement ToolRegistry.
```

Large work should be decomposed.

---

# 19. Task Decomposition

A large feature may become several tasks.

Example:

```text
TOOL SYSTEM

TASK 1
Define ToolDefinition

TASK 2
Implement ToolRegistry

TASK 3
Implement ToolExecutor

TASK 4
Integrate PermissionService

TASK 5
Implement filesystem tools

TASK 6
Integrate AgentRuntime

TASK 7
Run tool acceptance scenario
```

This makes scope and verification clearer.

---

# 20. Task Dependencies

Tasks may depend on earlier work.

Example:

```text
ToolExecutor
    depends on
ToolRegistry
```

The dependency should be explicit.

Do not begin dependent work using temporary architecture that will immediately need replacement unless the temporary approach is intentional.

---

# 21. Preconditions

A task may list preconditions.

Example:

```text
Preconditions:

- ToolDefinition finalized
- PermissionService interface available
- test runner configured
```

If preconditions are not satisfied, the task should not be marked ready.

---

# 22. Scope

Every task must contain:

```text
IN SCOPE
```

and:

```text
OUT OF SCOPE
```

Both matter.

In-scope defines required work.

Out-of-scope protects the project from accidental expansion.

---

# 23. Scope Example

Task:

```text
Implement filesystem.read_file
```

In scope:

```text
read files inside authorized workspace

path normalization

permission check

structured result

timeout/error handling

tests
```

Out of scope:

```text
write files

directory deletion

remote files

cloud storage

MCP filesystem

desktop file picker
```

---

# 24. Scope Is Not File Count

A task may legitimately affect several files.

Scope means behavior and responsibility, not:

```text
only touch one file
```

However, unexpected large changes are a signal to reevaluate the implementation.

---

# 25. Expected Files

Tasks may list files likely to change.

This helps focus agents.

Example:

```text
Expected Files:

src/tools/registry.py

src/tools/models.py

tests/tools/test_registry.py
```

This is guidance, not necessarily an absolute restriction.

---

# 26. Unexpected File Changes

If implementation requires an unexpected file, the agent should determine:

```text
IS THIS REQUIRED TO COMPLETE THE TASK?
```

If yes:

Proceed if the change is small and architecture-consistent.

If no:

Do not modify it.

For significant expansion, update the task scope or create a follow-up task.

---

# 27. Read Before Edit

Before changing a file:

1. Read the relevant file.
2. Understand nearby code.
3. Check dependencies/references where relevant.
4. Check project conventions.
5. Then edit.

Do not overwrite files based on assumptions.

---

# 28. Inspect Before Create

Before creating a new component, search for existing equivalents.

Example:

Before creating:

```text
ToolManager
```

search for:

```text
ToolRegistry
ToolService
CapabilityRegistry
ToolManager
```

Avoid duplicate abstractions.

---

# 29. No Duplicate Systems

If the project already has an approved system:

```text
PermissionService
```

do not create:

```text
ToolPermissionManager
```

that independently implements permission logic unless architecture explicitly requires it.

Reuse shared infrastructure.

---

# 30. Architecture Boundary Rule

Tasks must respect subsystem ownership.

Example:

`ToolExecutor` may ask:

```text
PermissionService
```

for authorization.

It must not read permission configuration and invent authorization logic internally.

---

# 31. Smallest Correct Change

The implementation should make the smallest correct architectural change.

This does not mean:

```text
minimum characters changed
```

It means:

```text
minimum unnecessary impact
```

A clean small component may be better than a hack inside an unrelated module.

---

# 32. Incidental Fixes

A small incidental fix may be made when all are true:

- It is directly encountered during current work.
- The correction is obvious.
- Risk is low.
- It does not materially expand scope.
- It does not require architecture decisions.

Example:

Fixing a typo in the exact configuration schema being edited.

---

# 33. Unrelated Defects

If the agent discovers an unrelated bug:

```text
DO NOT AUTOMATICALLY FIX IT
```

Instead:

- Record it.
- Add to backlog if appropriate.
- Continue current task.

This prevents endless scope creep.

---

# 34. Architecture Discovery

Sometimes implementation reveals an architectural issue.

Example:

Current interface cannot support required cancellation.

The agent should not silently redesign the subsystem.

Instead:

```text
DISCOVERY
   |
   v
EXPLAIN PROBLEM
   |
   v
CHECK EXISTING DOCS
   |
   v
CREATE / UPDATE ADR IF NEEDED
   |
   v
ADJUST TASK
```

---

# 35. Major Architecture Change

A major architecture change includes:

- New core service.
- Removing a core service.
- Changing subsystem ownership.
- Changing major data flow.
- Replacing persistence technology.
- Changing security architecture.
- Changing public contracts across subsystems.

These require:

`docs/22_DECISION_LOG.md`

---

# 36. Implementation Loop

Every coding task should approximately follow:

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

This is the project's normal development loop.

---

# 37. UNDERSTAND

Before coding, identify:

- Task objective.
- Relevant phase.
- Acceptance criteria.
- Scope.
- Existing architecture.
- Required tests.

The agent should be able to summarize the intended result internally before editing.

---

# 38. INSPECT

Inspect:

- Relevant source files.
- Existing tests.
- Existing patterns.
- Configuration.
- Git state when applicable.

Do not perform broad repository exploration without reason.

---

# 39. PLAN

For non-trivial tasks, form a short implementation plan.

Example:

```text
1. Define ToolDefinition model.

2. Implement ToolRegistry registration and lookup.

3. Add duplicate-ID validation.

4. Add tests.

5. Run tool test suite.
```

The plan should remain tied to acceptance criteria.

---

# 40. IMPLEMENT

During implementation:

- Preserve existing conventions.
- Avoid unrelated refactors.
- Add error handling.
- Respect dependency boundaries.
- Keep changes testable.

Do not leave unnecessary placeholder behavior.

---

# 41. TEST

Run the narrowest useful test first.

Then broader relevant checks as needed.

Example:

```text
specific unit test

tool module test suite

core integration test
```

The required test level depends on the task.

---

# 42. VERIFY

Testing and verification are related but different.

Testing asks:

```text
DO TESTS PASS?
```

Verification asks:

```text
DID WE SATISFY THE ACTUAL TASK?
```

A passing test suite does not automatically prove the requested behavior exists.

---

# 43. REVIEW DIFF

For source-code changes, inspect the final diff.

Check:

```text
unexpected files

accidental deletions

debug output

temporary code

secrets

scope creep

formatting noise
```

---

# 44. DOCUMENT

Update documentation when the task changes:

- Architecture.
- Configuration.
- Public API.
- Security behavior.
- User-visible behavior.
- Development instructions.

Do not update unrelated documentation.

---

# 45. COMPLETE

Only after verification should the task enter `COMPLETE`.

Provide a completion report.

---

# 46. Acceptance Criteria

Acceptance criteria define observable success.

Good criterion:

```text
Registering a tool with an existing tool_id
returns a duplicate-tool error and preserves
the existing registry entry.
```

Weak criterion:

```text
Tool registry works well.
```

Criteria should be testable.

---

# 47. Behavioral Criteria

Prefer behavior over implementation detail.

Example:

Better:

```text
Attempting to read outside the workspace is denied.
```

Rather than:

```text
Use if statement on line 43.
```

unless implementation constraint genuinely matters.

---

# 48. Machine-Checkable Criteria

Where possible, criteria should be verifiable automatically.

Examples:

```text
test returns success

exception type matches expected error

database record exists

file outside workspace remains unchanged

API returns defined response
```

---

# 49. Manual Criteria

Some tasks require manual verification.

Examples:

- UI visual quality.
- Audio playback.
- OS integration.

Manual criteria should still be explicit.

Example:

```text
At 375px width, message input remains visible
without horizontal scrolling.
```

---

# 50. Security Criteria

Security-sensitive tasks should define explicit negative tests.

Example:

```text
read inside workspace -> allowed

read outside workspace -> denied

read through symlink escape -> denied
```

Security success requires proving what the system refuses as well as what it allows.

---

# 51. Failure Criteria

Tasks should define important expected failure behavior.

Example:

```text
If model provider times out,
ModelRouter returns MODEL_TIMEOUT
rather than crashing AgentRuntime.
```

---

# 52. No Fake Acceptance

Acceptance criteria must use the real system where required.

Example:

UI requirement:

```text
Cancel button cancels active task.
```

A button displaying:

```text
Cancelled
```

without contacting TaskManager fails the criterion.

---

# 53. Test Mapping

Where practical, acceptance criteria should map to tests.

Example:

```text
AC-01:
Tool registration succeeds.

Test:
test_register_tool
```

```text
AC-02:
Duplicate IDs rejected.

Test:
test_duplicate_tool_id_rejected
```

This makes verification easier.

---

# 54. Required Verification Section

Every task should contain:

```text
Required Verification:
```

Example:

```text
- unit tests
- ToolRegistry contract tests
- lint relevant files
```

or:

```text
- run targeted project test
- inspect Git diff
```

---

# 55. Test Failure

If required tests fail:

```text
TASK IS NOT COMPLETE
```

The agent must determine:

- Did the change cause the failure?
- Was it pre-existing?
- Is the environment broken?

The result must be documented accurately.

---

# 56. Pre-Existing Test Failure

If a required test already failed before the task:

The agent should distinguish:

```text
PRE-EXISTING FAILURE
```

from:

```text
NEW REGRESSION
```

where possible.

A pre-existing failure may or may not block the task depending on its relevance.

---

# 57. Verification Evidence

Completion evidence may include:

```text
test output

exit code

diff

artifact

screenshot

database state

API response

source citation
```

Claims should match evidence strength.

---

# 58. Completion Report Format

Suggested:

```text
## Completion Report

Changed:
- ...

Verified:
- ...

Tests:
- ...

Result:
- ...

Not Verified:
- ...

Known Limitations:
- ...

Follow-Up:
- ...
```

Keep it factual.

---

# 59. No Exaggerated Completion

Avoid:

```text
Everything works perfectly.
```

Prefer:

```text
ToolRegistry unit and integration tests pass.
MCP discovery was not part of this task.
```

---

# 60. Task Closure

Before closing a task:

```text
[ ] objective satisfied

[ ] scope respected

[ ] relevant files reviewed

[ ] required tests run

[ ] acceptance criteria verified

[ ] diff reviewed

[ ] architecture docs updated if necessary

[ ] completion report written
```

---

# 61. Task Handoff

If a task cannot be finished in one development session, preserve a clear handoff.

Handoff should contain:

```text
completed work

remaining work

current blocker

files changed

tests already run

next recommended action
```

Do not rely on an agent remembering its previous hidden reasoning.

---

# 62. Handoff Example

```text
Task:
Implement ToolRegistry

Completed:
- ToolDefinition
- registration
- lookup
- duplicate detection

Remaining:
- availability filtering
- integration tests

Tests:
12 unit tests passing

Current blocker:
None

Next:
Implement availability filtering.
```

---

# 63. Task Resume

When resuming work:

1. Read `CURRENT_PHASE`.
2. Read `CURRENT_TASK`.
3. Inspect repository state.
4. Review previous completion/handoff notes.
5. Re-run relevant test if necessary.
6. Continue.

Do not assume repository state is unchanged.

---

# 64. Interrupted Work

If execution stops unexpectedly, the next agent should determine actual repository state before continuing.

Check:

- Git status.
- Changed files.
- Test status.
- Current task file.

Do not blindly repeat previous edits.

---

# 65. Parallel Development

Parallel tasks should only occur when their ownership is clear.

Example:

```text
Task A:
ToolRegistry

Task B:
Tool test fixtures
```

may be parallel.

But:

```text
Task A:
Redesign ToolDefinition

Task B:
Implement ToolExecutor against current ToolDefinition
```

may conflict.

Dependencies should be explicit.

---

# 66. Shared File Ownership

When multiple tasks touch the same files, coordinate changes.

Avoid simultaneous independent rewrites of:

```text
core contracts

configuration

task state machine

security policy
```

Conflict-prone work should be serialized where practical.

---

# 67. Research Tasks

A research task should define:

```text
QUESTION

WHY IT MATTERS

SOURCES / TARGETS

OUTPUT

DECISION IT ENABLES
```

Example:

```text
Question:
Should MVP persistence use SQLite?

Output:
Comparison of requirements against SQLite capabilities.

Decision enabled:
STATE-ADR-002
```

Research should not become endless browsing.

---

# 68. Research Task Completion

A research task is complete when:

- The question is answered as far as evidence permits.
- Relevant evidence is recorded.
- Uncertainty is explicit.
- Result enables or informs the intended decision.

Research does not require implementation.

---

# 69. Architecture Tasks

An architecture task should define:

```text
PROBLEM

OPTIONS

TRADE-OFFS

DECISION NEEDED

AFFECTED COMPONENTS
```

Its output may be an ADR rather than source code.

---

# 70. Documentation Tasks

Documentation tasks must have scope too.

Example:

```text
Update Tool System docs to reflect
approved MCP adapter contract.
```

Do not rewrite unrelated docs for stylistic preference.

---

# 71. Refactor Tasks

A refactor task should state:

```text
CURRENT PROBLEM

TARGET STRUCTURE

BEHAVIOR THAT MUST REMAIN UNCHANGED

TESTS PROTECTING BEHAVIOR
```

A refactor must not quietly introduce unrelated product changes.

---

# 72. Bug-Fix Tasks

A bug-fix task should include:

```text
OBSERVED BEHAVIOR

EXPECTED BEHAVIOR

REPRODUCTION

LIKELY AREA

VERIFICATION
```

Where practical, reproduce before modifying.

---

# 73. Security Tasks

Security tasks should define:

```text
THREAT

BOUNDARY

EXPECTED DENIAL

AUTHORIZED POSITIVE CASE

REGRESSION TEST
```

Security fixes usually require regression coverage.

---

# 74. Performance Tasks

Performance tasks must have a baseline.

Example:

```text
Current:
memory lookup median 500ms

Target:
investigate and materially reduce avoidable latency
without correctness regression
```

Do not claim performance improvement without measurement.

---

# 75. Task Size

Tasks should generally be small enough to:

- Understand.
- Implement.
- Test.
- Review.
- Roll back.

If the task description requires several unrelated paragraphs of objectives, it may need decomposition.

---

# 76. Micro-Tasks

Not every tiny edit needs a full formal task file.

Examples:

- Fix typo.
- Update one comment.
- Correct obvious formatting.

However, anything affecting runtime behavior should normally be connected to an approved development task.

---

# 77. Task Escalation

A small task may reveal larger work.

Example:

```text
TASK:
Add timeout to terminal tool.

DISCOVERY:
Current ToolExecutor has no cancellation architecture.
```

Do not quietly turn the task into:

```text
Rewrite Tool System.
```

Escalate the discovery.

---

# 78. Task Splitting

If scope expands substantially:

Current task may be completed only for original scope.

Create follow-up:

```text
TASK A
Add terminal timeout.

TASK B
Design shared cancellation architecture.
```

unless Task B is a prerequisite for A.

---

# 79. Backlog Integration

Discovered work should be recorded in:

```text
plans/BACKLOG.md
```

when it is useful but not currently approved.

Backlog entries do not authorize implementation.

---

# 80. Backlog Entry

Suggested structure:

```text
ID:
BACKLOG-...

Title:
Add browser screenshot tool

Reason:
Needed for future UI verification

Related Phase:
Phase 16

Status:
IDEA
```

Keep backlog entries concise until prioritized.

---

# 81. Backlog Status

Use the project statuses:

```text
IDEA

RESEARCH_NEEDED

PROPOSED

APPROVED

DEFERRED

REJECTED

IMPLEMENTED
```

Only `APPROVED` work should normally become an active development task.

---

# 82. No Backlog Fishing

Coding agents must not browse the backlog and begin interesting tasks after finishing early.

After current task completion:

```text
STOP
```

or move to the next explicitly approved task.

---

# 83. Dependency Additions

If the task needs a new dependency:

1. Confirm existing dependencies cannot reasonably solve it.
2. Check compatibility.
3. Check license where relevant.
4. Consider maintenance/security.
5. Add only if justified.

Unexpected major dependencies may require explicit approval.

---

# 84. Configuration Changes

Task scope must include configuration changes when they alter behavior.

Examples:

```text
new environment variable

new provider setting

new permission default

new database setting
```

Configuration changes need validation and documentation.

---

# 85. Database Changes

Tasks changing persistent schemas must include:

- Migration.
- Migration test.
- Backward compatibility considerations.
- Rollback/recovery plan where relevant.

Do not modify persistence schema casually.

---

# 86. Public Contract Changes

Changes to shared interfaces require special care.

Examples:

```text
ToolResult

ModelResponse

TaskRecord

MemoryRecord
```

Before changing them:

- Search consumers.
- Update tests.
- Assess migration impact.
- Update architecture docs if semantics change.

---

# 87. Security Contract Changes

Changes to:

```text
permissions

approval behavior

secret handling

sandboxing
```

should be treated as architecture/security work, not incidental refactors.

---

# 88. Temporary Hacks

Temporary implementation is permitted only when:

- Clearly marked.
- Required for current exploration.
- Not represented as final behavior.
- Removal or replacement path is known.

Temporary code must not silently become core architecture.

---

# 89. TODO Policy

A TODO is acceptable when it references legitimately deferred work.

Bad:

```text
TODO: actually check permissions
```

inside a feature claimed complete.

Good:

```text
TODO: add parallel execution in Phase 14
```

where sequential behavior is the approved current scope.

---

# 90. Stub Policy

Stubs may exist during implementation.

Before task completion, required code paths must not contain fake behavior.

Example prohibited final stub:

```text
return ToolResult(success=True)
```

without actually executing the tool.

---

# 91. Mock Policy

Mocks are allowed for:

- Unit tests.
- Deterministic component development.
- Failure injection.

Mocks do not satisfy real integration acceptance criteria.

---

# 92. Generated Code

AI-generated implementation receives the same requirements as human-written code.

It must be:

- Reviewed.
- Tested.
- Integrated.
- Verified.

Generated code is not automatically trusted.

---

# 93. External Code

If code is adapted from another source:

- Confirm license/provenance where necessary.
- Preserve notices where required.
- Ensure architectural compatibility.
- Test it.

Do not copy code from uncertain sources simply because it solves the task quickly.

---

# 94. User Data

Development tasks must not use unrelated real user data for testing.

Use synthetic fixtures where practical.

---

# 95. Credentials

Credentials must not appear in:

```text
CURRENT_TASK.md

test fixtures

source files

logs

completion reports
```

Use secret references.

---

# 96. Git Before Work

For coding tasks in a Git repository:

Inspect repository status before substantial edits.

Determine:

```text
clean repository
```

or:

```text
pre-existing changes present
```

Preserve the user's existing work.

---

# 97. Git After Work

Before task completion:

Inspect:

```text
git status

git diff
```

or equivalent.

Make sure the final diff corresponds to the task.

---

# 98. Commit Policy

A task does not automatically authorize:

```text
git commit

git push
```

These are separate workflow actions.

Commit/push behavior should be defined by the active development environment and permissions.

---

# 99. Destructive Commands

Destructive commands require strong justification.

Examples:

```text
git reset --hard

rm -rf

drop database

delete migration history
```

Never use them simply because they make the workspace easier to manage.

---

# 100. Test Selection

Use proportional verification.

Example:

Small isolated parser fix:

```text
targeted parser tests

related suite
```

Major runtime change:

```text
unit tests

integration tests

core acceptance evaluation
```

---

# 101. Verification Ladder

Use the strongest practical evidence.

```text
STATIC CHECK

UNIT TEST

INTEGRATION TEST

SYSTEM TEST

ACCEPTANCE SCENARIO

REAL END-TO-END EVALUATION
```

Not every task requires all levels.

---

# 102. Failure During Verification

If verification reveals a defect:

```text
DO NOT CLOSE TASK
```

Return to implementation.

If fixing the defect exceeds task scope, reassess the task and dependencies.

---

# 103. Completion Evidence Storage

Important completion evidence may be stored as:

- Test output.
- Evaluation result.
- Artifact.
- Task completion report.

The project should eventually make this traceable to task IDs.

---

# 104. Current Task Update

During implementation, `CURRENT_TASK.md` may be updated when:

- A blocker is discovered.
- Scope is formally revised.
- Acceptance criteria need clarification.
- Verification status changes.

Do not continuously rewrite it for every implementation detail.

---

# 105. Current Task Closure

When complete, update task status:

```text
COMPLETE
```

and include the completion report.

Then the next approved task may replace it.

Completed task history may later move to a dedicated archive if needed.

---

# 106. CURRENT_TASK Must Not Lie

If tests are blocked:

Do not write:

```text
Status: COMPLETE
```

Write:

```text
Status: BLOCKED
```

or:

```text
Status: VERIFYING
```

with the actual issue.

---

# 107. Agent Self-Check

Before finishing, every coding agent should ask:

```text
Did I solve the requested task?

Did I build anything not requested?

Did I bypass an existing subsystem?

Did I invent architecture?

Did I run the required tests?

Did I verify the actual result?

Did I preserve existing work?

Did I leave fake or temporary functionality?

Did I update required documentation?
```

---

# 108. Task Anti-Patterns

Avoid:

```text
"While I'm here, I'll rewrite this."

"I'll add this future feature too."

"This architecture looks cleaner to me."

"No tests exist, but it should work."

"The button appears, so UI is done."

"The model says success."

"I changed the tests so they pass."

"I couldn't run it, but the code looks correct."
```

---

# 109. Agent Stop Conditions

The coding agent should stop implementation when:

- Task is complete.
- Task is blocked.
- Required architecture decision is missing.
- Continuing would require significant scope expansion.
- Continuing would violate permissions or project rules.

Stopping is preferable to uncontrolled expansion.

---

# 110. Decision vs. Implementation

If the task requires answering:

```text
WHICH TECHNOLOGY SHOULD WE USE?
```

that is usually a decision/research task.

Do not implement multiple production technologies merely to choose between them unless a prototype is specifically approved.

---

# 111. Experimental Branches

Experiments should remain isolated.

If an experiment fails:

- Do not merge its architecture into production.
- Record findings.
- Remove or preserve it as a deliberate prototype.

---

# 112. Task Quality Standard

Good tasks are:

```text
small enough to understand

large enough to produce useful progress

clear enough to verify

bounded enough to review
```

---

# 113. Example — Good Feature Task

```text
Title:
Implement ToolRegistry

Objective:
Provide the central registry for ToolDefinitions.

In Scope:
- register
- resolve
- list
- duplicate detection
- availability state
- tests

Out of Scope:
- tool execution
- MCP
- plugin installation

Acceptance:
- registered tool resolves by ID
- duplicates rejected
- unknown tool returns defined error
- unavailable tool distinguishable from unknown tool

Verification:
- ToolRegistry unit suite
```

---

# 114. Example — Bad Feature Task

```text
Title:
Make Mikasa's tools better

Do:
Implement all tools, MCP, browser, plugins,
automatic installs, better AI decisions,
and maybe redesign the dashboard.
```

This is not a usable development task.

---

# 115. Example — Good Bug Task

```text
Title:
Reject filesystem symlink escape

Observed:
Authorized workspace symlink allows reading a file outside root.

Expected:
Resolved target outside root must be denied.

In Scope:
- canonical path resolution
- symlink boundary enforcement
- regression test

Out of Scope:
- redesign permission model

Acceptance:
- normal in-workspace file read succeeds
- symlink escape fails
- direct outside path fails
```

---

# 116. Example — Good Research Task

```text
Title:
Evaluate SQLite for initial state persistence

Question:
Does SQLite satisfy Phase 6 requirements?

Evaluate:
- transactions
- local durability
- migrations
- concurrent access needs
- backup
- portability

Output:
Evidence-backed architecture recommendation.

Decision:
STATE-ADR-002
```

---

# 117. Example — Good Refactor Task

```text
Title:
Separate ToolRegistry from ToolExecutor

Problem:
Current module mixes capability discovery and execution.

Goal:
Split responsibilities without changing external behavior.

Required:
- existing tests remain passing
- new component boundaries documented
- no tool behavior change
```

---

# 118. Relationship to Definition of Done

This file defines:

```text
HOW WORK IS PERFORMED
```

`docs/20_DEFINITION_OF_DONE.md` defines:

```text
WHEN WORK MAY BE CALLED COMPLETE
```

Both apply.

---

# 119. Relationship to AGENTS.md

`AGENTS.md` provides the high-level behavior rules for coding agents.

This document provides the detailed task lifecycle.

If conflict exists:

Higher-authority project governance and architecture documents take precedence according to the source-of-truth order in `AGENTS.md`.

---

# 120. Relationship to Development Phases

`docs/19_DEVELOPMENT_PHASES.md` answers:

```text
WHAT SHOULD WE BUILD NOW?
```

This document answers:

```text
HOW DO WE EXECUTE EACH PIECE OF THAT WORK?
```

---

# 121. Relationship to Decision Log

When implementation reveals an architectural decision:

Use:

```text
docs/22_DECISION_LOG.md
```

Do not bury major decisions inside `CURRENT_TASK.md`.

---

# 122. Relationship to Backlog

Backlog captures:

```text
POSSIBLE FUTURE WORK
```

Current Task captures:

```text
AUTHORIZED PRESENT WORK
```

They must remain distinct.

---

# 123. Initial Development Protocol

During the first implementation phase, the coding agent should use this minimal protocol:

```text
1. READ CURRENT PHASE

2. READ CURRENT TASK

3. INSPECT RELEVANT DOCS

4. INSPECT REPOSITORY STATE

5. FORM SHORT PLAN

6. IMPLEMENT SMALLEST CORRECT CHANGE

7. RUN REQUIRED TESTS

8. REVIEW DIFF

9. VERIFY ACCEPTANCE CRITERIA

10. WRITE COMPLETION REPORT

11. STOP
```

This is the default Mikasa development loop.

---

# 124. Definition of Done

The Task Protocol is sufficiently established when:

- `CURRENT_PHASE.md` exists.
- `CURRENT_TASK.md` uses an approved template.
- Active tasks contain explicit scope.
- Active tasks contain non-goals.
- Acceptance criteria are explicit.
- Verification requirements are explicit.
- Coding agents inspect relevant architecture before editing.
- Major architecture discoveries trigger the decision process.
- Unrelated discoveries go to backlog.
- Completion reports are produced.
- Tasks are not marked complete without evidence.
- Coding agents stop after approved work rather than selecting arbitrary backlog items.

---

# 125. Final Task Principle

Every Mikasa development task should move through a controlled path:

```text
DEFINE

SCOPE

UNDERSTAND

INSPECT

PLAN

IMPLEMENT

TEST

VERIFY

REVIEW

DOCUMENT

COMPLETE

STOP
```

The project must preserve:

```text
IDEA
   !=
APPROVED TASK

BACKLOG
   !=
CURRENT WORK

CODING
   !=
PROGRESS

IMPLEMENTED
   !=
VERIFIED

DISCOVERY
   !=
AUTHORIZATION TO REBUILD
```

**One phase. One approved task. One clear objective. Build only what is needed, verify it, document the result, then stop.**