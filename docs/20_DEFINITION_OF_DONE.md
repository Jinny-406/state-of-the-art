# M I K A S A
## Definition of Done

**File:** `docs/20_DEFINITION_OF_DONE.md`

**Version:** 0.1.0

**Status:** PROPOSED — Pending architecture research and approval

**Authority:** Project-wide completion criteria for tasks, features, subsystems, phases, and releases

**Applies to:** All Mikasa implementation work, architecture work, tests, evaluations, documentation, integrations, UI, tools, providers, and future releases.

---

# 1. Purpose

This document defines what "done" means in M I K A S A.

The project must not mark work complete simply because:

- Code was written.
- Files were created.
- A UI exists.
- A model responded.
- A tool returned success.
- A test passed once.
- A coding agent said the task was finished.

Completion must be based on observable evidence.

This document establishes completion rules for:

- Individual coding tasks.
- Bug fixes.
- Features.
- Integrations.
- Subsystems.
- Architecture work.
- Development phases.
- Release milestones.

---

# 2. Core Principle

The project must preserve this distinction:

```text
IMPLEMENTED
    !=
DONE
```

"Done" requires:

```text
IMPLEMENTED

+

INTEGRATED

+

TESTED

+

VERIFIED

+

DOCUMENTED

+

WITHIN SCOPE
```

where applicable.

---

# 3. Universal Completion Rule

Before any work is marked complete, the responsible agent or developer must be able to answer:

```text
WHAT WAS REQUESTED?

WHAT CHANGED?

WHAT WAS TESTED?

WHAT EVIDENCE SHOWS IT WORKS?

WHAT WAS NOT VERIFIED?

WHAT LIMITATIONS REMAIN?
```

If those questions cannot be answered, the work is not complete.

---

# 4. Levels of Completion

Mikasa should distinguish:

```text
CODE COMPLETE

INTEGRATION COMPLETE

VERIFICATION COMPLETE

TASK COMPLETE

PHASE COMPLETE

RELEASE COMPLETE
```

These are different milestones.

---

# 5. Code Complete

A change is code complete when:

- Required implementation exists.
- Code follows project architecture.
- No obvious placeholders remain.
- No fake implementation remains.
- Relevant interfaces are implemented.
- Local static checks pass where applicable.

Code complete does not mean the feature is verified.

---

# 6. Integration Complete

A feature is integration complete when:

- It is connected to the real system.
- Upstream components can call it.
- Downstream components receive its results.
- It does not depend on hard-coded demo behavior.
- Required persistence/configuration is connected.
- Permission checks are connected where relevant.

A standalone module with no real runtime path is not integration complete.

---

# 7. Verification Complete

A feature is verification complete when:

- Required tests pass.
- Required acceptance scenario succeeds.
- Failure behavior is tested where relevant.
- Security boundaries are tested where relevant.
- No known blocker invalidates the feature claim.

Verification must use real evidence.

---

# 8. Task Complete

A task is complete when:

```text
requested objective satisfied

required tests passed

scope respected

result verified

relevant artifacts reviewed

known limitations reported
```

The Task Manager may then record:

```text
COMPLETED
```

---

# 9. Partial Completion

Some work may be useful but incomplete.

Example:

```text
Completed:
- diagnosed the failure

Not completed:
- could not verify the fix
```

This must not be marked as full completion.

Possible outcome representation:

```text
PARTIAL RESULT
```

or:

```text
BLOCKED WITH COMPLETED SUBWORK
```

depending on the canonical task-state model.

---

# 10. Blocked Work

A task is blocked when:

- Required permission is unavailable.
- Required dependency is missing.
- Required environment is unavailable.
- Essential information is missing.
- Required capability does not exist.
- External service prevents continuation.

Blocked is not failed by default.

The blocker must be recorded accurately.

---

# 11. Failed Work

A task is failed when the system cannot satisfy the requested objective under the current conditions and no appropriate recovery path remains.

Failure must preserve:

- Last successful state.
- Error category.
- Relevant evidence.
- Remaining blocker.
- Any useful partial artifacts.

---

# 12. Cancelled Work

A task is cancelled when the cancellation request has been processed and the system has stopped scheduling new work.

Cancellation does not imply:

- Rollback.
- Undo.
- Deletion of artifacts.
- Reversal of external actions.

The actual post-cancellation state must be reported.

---

# 13. Bug Fix Definition of Done

A bug fix is done when:

1. The bug is reproduced where practical.
2. The root cause or sufficient causal explanation is identified.
3. The relevant implementation is changed.
4. The original failure no longer reproduces.
5. Relevant tests pass.
6. Regression coverage exists when appropriate.
7. No unrelated user work was destroyed.
8. The final diff is reviewed.

Do not mark a bug fixed because:

```text
the error disappeared once
```

without sufficient evidence.

---

# 14. Feature Definition of Done

A feature is done when:

- Requirements are implemented.
- UI/backend paths are connected where relevant.
- Required permissions are enforced.
- Persistence works where required.
- Tests exist.
- Acceptance criteria pass.
- Failure behavior is handled.
- Documentation reflects the implementation.

A mock UI alone is not a finished feature.

---

# 15. UI Feature Definition of Done

A UI feature is done when:

- It displays real backend state.
- User actions call real backend behavior.
- Loading states work.
- Failure states work.
- Empty states work.
- Responsive behavior is verified where required.
- Accessibility requirements are met.
- No fake status or fake data is shown as operational.

---

# 16. Tool Definition of Done

A tool is done when:

- It has a stable definition.
- Input schema exists.
- Output schema exists.
- Permission requirements exist.
- Input validation works.
- Execution works.
- Timeouts work.
- Errors are structured.
- Cancellation works where supported.
- Observability events exist.
- Integration tests pass.

---

# 17. Model Provider Definition of Done

A model provider integration is done when:

- Configuration loads.
- Authentication works.
- Requests normalize correctly.
- Responses normalize correctly.
- Timeouts are handled.
- Provider errors are normalized.
- Tool calling works if required.
- Usage is recorded where available.
- Credentials remain protected.
- Real-provider integration tests pass.

---

# 18. Memory Feature Definition of Done

Memory functionality is done when:

```text
CREATE

RESTART

RETRIEVE

CORRECT

RETRIEVE CORRECTION

DELETE

VERIFY DELETION
```

works for the supported memory type and scope.

Additionally:

- Provenance is preserved.
- Scope is enforced.
- Invalid cross-scope retrieval is prevented.
- False save confirmations do not occur.

---

# 19. Persistence Definition of Done

Persistence is done when:

- Task state survives restart.
- Execution records survive restart.
- Cancellation survives restart.
- Schema migrations work.
- Persistence failures are surfaced.
- Corrupted/invalid state is handled appropriately.
- No state is silently fabricated after restart.

---

# 20. Autonomy Definition of Done

Bounded autonomy is done when Mikasa can:

- Accept a multi-step goal.
- Build a usable plan.
- Execute steps.
- Observe results.
- Recover from a bounded failure.
- Replan when needed.
- Respect permissions.
- Respect execution limits.
- Detect completion.
- Verify the outcome.
- Stop.

An endless loop with tool access is not autonomy.

---

# 21. Coding Engine Definition of Done

The Coding Engine is done for its initial milestone when Mikasa can:

```text
inspect project

read instructions

run failing test

find relevant code

modify code

re-run test

inspect diff

verify result

report accurately
```

using real tools.

A code generator that cannot verify its changes does not satisfy this milestone.

---

# 22. Research Engine Definition of Done

The initial Research Engine is done when Mikasa can:

- Define a research objective.
- Generate searches.
- Retrieve actual sources.
- Extract evidence.
- Compare sources.
- Preserve provenance.
- Identify meaningful conflicts.
- Produce claims with citations.
- Distinguish source facts from interpretation.

Fabricated citations automatically fail the requirement.

---

# 23. Multi-Agent Definition of Done

The first multi-agent milestone is done when:

- Specialists are real registered executions.
- Delegation uses structured contracts.
- Child permissions are scoped.
- Parent-child task relationships persist.
- Results return to the main agent.
- Specialist failure propagates correctly.
- Cancellation propagates.
- Parent completion requires verification.

Decorative specialist labels are not multi-agent functionality.

---

# 24. Voice Definition of Done

The first voice milestone is done when:

- Real microphone input works.
- Real STT works.
- Transcript goes through the normal gateway.
- Spoken tasks create real tasks.
- Real TTS works.
- Speech can be interrupted.
- Task cancellation works through voice.
- Approval uses the normal Permission Service.
- Voice errors fall back gracefully.

---

# 25. Self-Improvement Definition of Done

A self-improvement feature is not done because Mikasa modified something.

It is done when:

```text
candidate identified

baseline defined

experiment isolated

candidate tested

result compared

approval applied where required

version promoted

rollback available
```

for the supported improvement class.

---

# 26. Security Definition of Done

Security functionality is done only when it is enforced in code.

Examples:

Permission UI without backend enforcement:

```text
NOT DONE
```

Prompt telling model not to access a path:

```text
NOT DONE
```

Actual filesystem boundary rejecting path escape:

```text
DONE WHEN TESTED
```

---

# 27. Security Completion Gate

Any feature with privileged capabilities must satisfy relevant security tests before being called complete.

Examples:

- Filesystem → path boundary tests.
- Terminal → execution boundary tests.
- Browser → permission tests.
- Desktop → control authorization tests.
- Plugins → capability tests.
- Credentials → leakage tests.

---

# 28. Observability Definition of Done

A subsystem is sufficiently observable when:

- Important operations emit events.
- Errors can be traced to tasks/executions.
- Relevant durations are recorded.
- Secrets are redacted.
- Completion evidence can be located.
- A developer can reconstruct major execution flow.

---

# 29. Documentation Definition of Done

Documentation work is done when:

- It reflects the intended architecture.
- Scope is clear.
- Requirements are clear.
- Open decisions are identified.
- Contradictions with other docs are resolved.
- References to implementation status are accurate.

Architecture documentation must not claim future features are implemented.

---

# 30. Architecture Decision Definition of Done

An architecture decision is done when:

- Problem is defined.
- Options are identified.
- Decision is recorded.
- Rationale is documented.
- Important trade-offs are documented.
- Consequences are understood.
- Related docs are updated.

Use:

`docs/22_DECISION_LOG.md`

---

# 31. Research Spike Definition of Done

A research spike is done when it answers its original question.

Example:

```text
Question:
Can SQLite support our initial task persistence requirements?
```

Spike output:

```text
YES / NO / CONDITIONAL

evidence

limitations

recommendation
```

The spike does not need to become production code.

---

# 32. Prototype Definition of Done

A prototype is complete when:

- Its question is answered.
- Success criteria are evaluated.
- Findings are documented.
- The prototype is either:
  - discarded,
  - retained for reference,
  - or deliberately promoted.

Prototypes must not silently become production architecture.

---

# 33. Refactor Definition of Done

A refactor is done when:

- Intended structure improves.
- External behavior remains correct unless explicitly changed.
- Tests still pass.
- No required capability is lost.
- Documentation is updated if architecture changed.
- Diff remains within approved scope.

---

# 34. Dependency Change Definition of Done

A new dependency is done when:

- Need is justified.
- License is acceptable.
- Version is pinned/managed appropriately.
- Integration works.
- Security considerations are reviewed.
- Tests pass.
- Dependency is documented where appropriate.

Installing a package successfully is not enough.

---

# 35. Configuration Change Definition of Done

A configuration change is done when:

- Schema is valid.
- Defaults are defined.
- Invalid configuration fails clearly.
- Secrets remain separate.
- Migration/backward compatibility is considered where required.
- Relevant tests pass.

---

# 36. Migration Definition of Done

A data migration is done when:

- Backup/recovery strategy exists where needed.
- Migration succeeds on supported previous state.
- Resulting schema validates.
- Failure path is tested.
- Application starts successfully afterward.
- No unexpected data loss occurs.

---

# 37. CLI Definition of Done

A CLI command is done when:

- Help text exists.
- Input validation works.
- Exit codes are meaningful.
- Errors are understandable.
- Output reflects real backend state.
- Tests cover important paths.

---

# 38. API Definition of Done

An API operation is done when:

- Contract is defined.
- Input validation works.
- Authorization works.
- Error response is defined.
- Success response is defined.
- Integration tests exist.
- Observability exists.

---

# 39. Background Task Definition of Done

A background task capability is done when:

- Task is persisted.
- Worker can execute it.
- Task survives frontend disconnect.
- Cancellation works.
- Restart/recovery behavior is defined.
- Status is queryable.
- Result is persisted.

A promise in model text to "keep working" is not background execution.

---

# 40. Scheduler Definition of Done

Scheduling is done when:

- Schedule is durable.
- Execution actually occurs.
- Restart preserves schedule.
- Cancellation/disabling works.
- Permissions are revalidated where required.
- Results are stored.

---

# 41. Computer-Use Definition of Done

Computer use is done only when:

- Observation is real.
- Input control is real.
- Permission scopes exist.
- Cancellation works.
- Actions are observable.
- High-impact operations are gated.
- Verification works.
- Tests use a controlled environment.

---

# 42. Acceptance Scenario Requirement

Every major subsystem should have at least one defined acceptance scenario.

The scenario must:

- Start from realistic input.
- Use real subsystem integrations.
- Produce machine-checkable evidence where practical.
- Test the main successful path.

---

# 43. Failure Scenario Requirement

Major subsystems should also test failure.

Examples:

```text
provider unavailable

tool timeout

permission denied

database write failure

missing source

failed test
```

Only testing the happy path is insufficient.

---

# 44. Integration Requirement

A subsystem is not done while it exists only as an isolated library.

Example:

```text
MemoryService unit tests pass
```

but the Agent Runtime never retrieves memory.

Result:

```text
MEMORY MODULE IMPLEMENTED

MEMORY FEATURE NOT DONE
```

---

# 45. Real-System Requirement

Mocks may prove component behavior.

At least one relevant real integration must eventually prove assumptions.

Examples:

- Real filesystem.
- Real persistence.
- Real configured model.
- Real terminal command.
- Real research source.

---

# 46. No Fake Completion

The following do not count as completed implementation:

```text
TODO comments

placeholder methods

fake progress bars

hard-coded model responses

hard-coded tool results

mock-only acceptance demos

buttons with no backend action

screens displaying invented worker states

sample memory that is not actually stored
```

---

# 47. No Silent Failure

A feature is not complete if failures disappear silently.

Failures must result in:

- Structured error.
- Appropriate state.
- Useful user-facing result.
- Observability record.

---

# 48. No False Success

A feature is not complete if it can report success when verification fails.

Examples:

```text
tests failed
+
task says complete
```

is a release-blocking bug for coding workflows.

---

# 49. Test Requirement

Relevant automated tests must exist before completion where the behavior is deterministic enough to test.

Typical requirement:

```text
UNIT

+

INTEGRATION

+

ACCEPTANCE / EVAL
```

depending on the subsystem.

---

# 50. Regression Requirement

When a significant bug is fixed, ask:

```text
CAN A TEST PREVENT THIS FROM RETURNING?
```

If yes, add one.

Do not rely on memory of the bug.

---

# 51. Test Honesty

Completion reports must distinguish:

```text
PASSED

FAILED

NOT RUN

NOT APPLICABLE

BLOCKED
```

Do not collapse these into vague language such as:

```text
looks good
```

---

# 52. Diff Review Requirement

For source-code tasks, the final diff must be inspected before completion.

Check:

- Unexpected files.
- Accidental deletion.
- Debug code.
- Secrets.
- Unrelated formatting.
- Scope creep.

---

# 53. User Work Protection

A coding task cannot be considered done if it accidentally destroys unrelated existing user changes.

Pre-existing changes must be preserved unless the task explicitly requires replacing them.

---

# 54. Scope Requirement

A task must satisfy the requested objective without unnecessary expansion.

Example:

Requested:

```text
fix button alignment
```

Delivered:

```text
complete design-system rewrite
```

This does not satisfy disciplined completion merely because the button happens to be aligned.

---

# 55. Architecture Compliance

Completion requires compliance with approved architecture.

For example:

A feature that works but bypasses:

```text
PermissionService
```

to gain filesystem access is not done.

It is an architectural defect.

---

# 56. Dependency Direction

New implementation must preserve the dependency direction established in:

`docs/03_SYSTEM_ARCHITECTURE.md`

A successful feature that creates circular subsystem dependencies may still require correction before completion.

---

# 57. Observability Requirement

Major operations must be visible enough to diagnose.

If a feature fails and nobody can determine:

```text
what happened
```

its observability may be insufficient for completion.

---

# 58. Error Handling Requirement

Expected failures must be handled explicitly.

Examples:

- Invalid configuration.
- Missing tool.
- Permission denied.
- Provider timeout.
- Missing file.
- Persistence error.

Unhandled expected failures should block completion.

---

# 59. Cancellation Requirement

Long-running or multi-step operations requiring cancellation are not complete until cancellation has been implemented and tested.

---

# 60. Resource Limit Requirement

Autonomous loops must have limits.

An agent loop capable of running indefinitely cannot be considered production-ready.

---

# 61. Security Requirement

Security-critical tests have higher priority than convenience.

A feature must not be marked complete while known privilege escalation or secret leakage exists.

---

# 62. Secret Handling Requirement

No completed implementation may intentionally leave credentials in:

```text
source code

ordinary logs

normal memory

model prompts

test fixtures committed to repository
```

---

# 63. Documentation Update Requirement

If a task changes:

- Architecture.
- Public interface.
- Configuration.
- Workflow.
- Security behavior.

then relevant documentation must be updated before completion.

---

# 64. Decision Log Requirement

A major architectural decision must not exist only in source code.

If the change qualifies as an architectural decision, record it in:

`docs/22_DECISION_LOG.md`

before the work is considered fully complete.

---

# 65. Completion Report

Each meaningful implementation task should end with a concise completion report.

Suggested format:

```text
Changed:
- ...

Tested:
- ...

Result:
- ...

Not verified:
- ...

Known limitations:
- ...
```

This helps future agents understand actual system state.

---

# 66. Feature Completion Checklist

Before marking a feature done:

```text
[ ] requirement implemented
[ ] scope respected
[ ] architecture respected
[ ] permissions enforced
[ ] integration connected
[ ] error handling implemented
[ ] tests added
[ ] required tests passing
[ ] acceptance scenario passing
[ ] relevant failure scenario tested
[ ] observability available
[ ] documentation updated
[ ] no fake functionality
[ ] limitations documented
```

Not every checkbox applies to every tiny change.

Use judgment based on feature type.

---

# 67. Task Completion Checklist

Before closing an active task:

```text
[ ] objective satisfied
[ ] output exists
[ ] output verified
[ ] no required work remains
[ ] no unresolved blocker invalidates success
[ ] task state correct
[ ] result reported accurately
```

---

# 68. Phase Completion Checklist

Before marking a phase complete:

```text
[ ] all required phase capabilities implemented
[ ] architecture decisions resolved
[ ] required unit tests pass
[ ] required integration tests pass
[ ] required security tests pass
[ ] phase acceptance scenario passes
[ ] known limitations documented
[ ] docs match actual implementation
[ ] backlog updated
[ ] next phase entry criteria satisfied
```

---

# 69. Release Completion Checklist

Before a milestone release:

```text
[ ] required phases complete
[ ] release gate passes
[ ] regression suite passes
[ ] security suite passes
[ ] critical end-to-end evals pass
[ ] migrations tested
[ ] startup/shutdown tested
[ ] installation path tested
[ ] known limitations documented
[ ] release notes prepared
```

---

# 70. Severity of Incomplete Work

Open issues should be classified.

Suggested:

```text
BLOCKER

CRITICAL

MAJOR

MINOR

DEFERRED
```

Definitions must be agreed later.

A blocker prevents completion.

A deferred item must truly be outside current required scope.

---

# 71. Known Limitation

A known limitation is acceptable when:

- It does not violate current acceptance criteria.
- It is documented.
- It does not create unacceptable security risk.
- It is intentionally deferred.

Example:

```text
MVP supports sequential tool calls only.
Parallel execution is deferred.
```

This is valid.

---

# 72. Known Bug

A known bug differs from a limitation.

A bug means expected behavior is incorrect.

Significant known bugs may block completion depending on severity.

Do not relabel defects as limitations merely to close a phase.

---

# 73. Deferred Feature

A deferred feature is something intentionally outside the active scope.

Example:

```text
Wake-word activation
```

during the first voice milestone.

Its absence does not make the milestone incomplete.

---

# 74. Quality vs. Completeness

A feature may be complete but still have room for improvement.

Example:

```text
working functional CLI
```

may be complete for MVP even if:

```text
advanced autocomplete
```

is deferred.

Completion means current requirements are satisfied.

It does not mean no future improvement exists.

---

# 75. Completion and Performance

Performance optimization is required only where current requirements demand it.

Do not block an MVP because:

```text
a theoretical future workload could be faster
```

However, unusable performance can invalidate functional completion.

---

# 76. Completion and UX Polish

Visual polish should match the current phase.

Early internal tools may remain simple.

User-facing release surfaces require stronger UX quality.

Avoid spending weeks polishing a UI for a backend capability that does not yet work.

---

# 77. Completion and Platform Support

A feature should only claim support for platforms actually tested.

Example:

If tested only on:

```text
Windows
```

do not claim:

```text
Windows, macOS, Linux
```

without evidence.

---

# 78. Completion and Provider Support

Likewise:

One working model provider means:

```text
one provider supported
```

not:

```text
universal model compatibility complete
```

The abstraction may support future providers without those providers being implemented.

---

# 79. Completion and Local Models

A local-model adapter is not complete because it can connect to an endpoint.

It must verify required behavior such as:

- Text generation.
- Tool calling when needed.
- Context limits.
- Errors.
- Model availability.

---

# 80. Completion and Plugins

A plugin architecture is not done because a plugin folder exists.

It must demonstrate:

- Discovery.
- Validation.
- Permission handling.
- Activation.
- Real capability.
- Removal/disable behavior.

when those are part of the approved milestone.

---

# 81. Completion and MCP

MCP support is done only when:

- A configured server can connect.
- Tools can be discovered.
- Tool schemas normalize.
- Permission checks apply.
- Tool calls execute.
- Errors are handled.
- Results return through the normal Tool System.

---

# 82. Completion and Research Sources

Research is not complete if the final report lacks enough evidence to support material claims.

The report may still be complete when some questions remain unresolved, but those uncertainties must be explicitly reported if the research objective allows partial resolution.

---

# 83. Completion and Verification Evidence

Verification should produce durable or inspectable evidence where practical.

Examples:

```text
test log

exit code

diff

source citation

artifact checksum

external-state confirmation
```

The stronger the task claim, the stronger the required evidence should be.

---

# 84. Completion and Unknown Outcome

An operation with:

```text
UNKNOWN_OUTCOME
```

cannot be treated as successful.

The task must either:

- Verify it,
- resolve it another way,
- or report the uncertainty.

---

# 85. Completion and Repeated Failure

If the same step repeatedly fails without progress:

```text
STOP
```

or replan.

Do not call endless retry behavior "working."

---

# 86. Completion and Self-Improvement

A self-improvement experiment is not done because a candidate appears better.

Promotion requires the defined evaluation and approval process.

---

# 87. Completion and Backlog

Moving unfinished required work to backlog does not automatically make the current task complete.

Backlog deferral must be valid under the approved scope.

---

# 88. Completion and TODOs

A TODO is acceptable only if it belongs to deferred work.

A TODO for a required acceptance criterion means the feature is incomplete.

---

# 89. Completion and Stubs

Stubs are useful during development.

They must not survive in code paths claimed to be production-ready unless the stub behavior itself is intentional.

---

# 90. Completion and Mocks

Mocks can satisfy:

```text
unit test requirement
```

They cannot alone satisfy:

```text
real integration acceptance requirement
```

---

# 91. Completion and Manual Testing

Manual verification may supplement automation.

For some UI or hardware functionality it may be necessary.

Manual testing should record:

- What was tested.
- Environment.
- Outcome.

Automate repeatable critical behavior when practical.

---

# 92. Completion and External Dependencies

If completion depends on an unavailable external service, the task may be blocked.

Do not fabricate a successful integration.

---

# 93. Completion and Flaky Results

A feature with unreliable acceptance results should not be treated as stable.

If:

```text
passes 3 times
fails 3 times
```

then reliability remains unresolved.

---

# 94. Completion Evidence Hierarchy

Prefer stronger evidence when available.

Example hierarchy:

```text
DIRECT SYSTEM STATE

DETERMINISTIC TEST

REAL INTEGRATION RESULT

STRUCTURED TOOL RESULT

MODEL EVALUATION

MODEL CLAIM
```

Model claims alone are weakest.

---

# 95. Definition of "Verified"

A result is verified when evidence directly supports the relevant acceptance criterion.

Example:

Criterion:

```text
Tests pass.
```

Evidence:

```text
actual test runner returned success.
```

Not:

```text
model says tests probably pass.
```

---

# 96. Definition of "Integrated"

A component is integrated when real application flow reaches and uses it.

Example:

Memory Service exists but is never called:

```text
NOT INTEGRATED
```

Agent Runtime retrieves memory and uses returned context:

```text
INTEGRATED
```

---

# 97. Definition of "Working"

"Working" should mean:

```text
successful under the tested conditions
```

It must not imply universal reliability.

Use precise statements.

---

# 98. Definition of "Stable"

"Stable" should require:

- Defined regression coverage.
- Repeated evaluation.
- No known critical issues.
- Acceptable reliability on supported workflows.

Do not use "stable" casually for freshly implemented experimental code.

---

# 99. Definition of "Production-Ready"

Do not use this label until the project explicitly defines a production-readiness milestone.

Likely requirements include:

- Security review.
- Reliable install/update.
- Backup/recovery.
- Mature evaluation.
- Diagnostics.
- Release process.

MVP does not automatically mean production-ready.

---

# 100. Definition of "MVP"

MVP means:

```text
minimum coherent product proving the core value
```

For Mikasa, that means a working agent loop with real model, tools, permissions, persistence, bounded autonomy, verification, and a useful task workflow.

MVP does not mean:

```text
every planned feature
```

---

# 101. Definition of "Complete Project"

The entire Mikasa project may never be permanently "complete" because software evolves.

Instead use milestone completion.

Example:

```text
Mikasa Core 1.0 complete
```

with a specific published feature set and acceptance criteria.

---

# 102. Anti-Completion Patterns

The project must reject these patterns:

```text
"It compiled, so it's done."

"The model responded, so it works."

"The button is visible, so the feature exists."

"The test didn't run, but the code looks right."

"The API returned 200 once, so integration is complete."

"The agent said it succeeded."

"We'll fix verification later."
```

---

# 103. Completion Reporting Language

Preferred:

```text
Implemented and verified X.

Tests run:
- A
- B

Not verified:
- C

Known limitation:
- D
```

Avoid:

```text
Everything is perfect.
```

unless literally justified, which is unlikely.

---

# 104. Coding Agent Completion Protocol

Before a coding agent says a task is finished, it must:

```text
1. review CURRENT_TASK

2. inspect diff

3. run required tests

4. verify acceptance criteria

5. check scope

6. identify limitations

7. update relevant docs if needed

8. provide completion report
```

---

# 105. "Almost Done"

If one required acceptance criterion remains unresolved:

```text
NOT DONE
```

The agent may say:

```text
Implementation is complete, but verification is blocked by X.
```

That is accurate and useful.

---

# 106. Definition of Done for Documentation Phase

The architecture/documentation phase is complete when:

- Required core docs exist.
- They are internally consistent.
- Open decisions are identified.
- Research targets are defined.
- Scope boundaries are clear.
- Development sequence exists.
- Task protocol exists.
- Decision process exists.
- Roadmap exists.

This does not mean implementation has started.

---

# 107. Enforcement

`AGENTS.md` should reference this document.

Coding agents must use this document when deciding whether work is complete.

`plans/CURRENT_TASK.md` may add task-specific completion rules.

Task-specific rules may be stricter.

They must not weaken core security or verification requirements.

---

# 108. Final Definition of Done Principle

Mikasa must never use completion as a feeling.

Completion is an evidence-backed state.

```text
REQUEST
   |
   v
IMPLEMENT
   |
   v
INTEGRATE
   |
   v
TEST
   |
   v
VERIFY
   |
   v
REVIEW
   |
   v
DOCUMENT
   |
   v
DONE
```

The system must preserve these distinctions:

```text
WRITTEN
    !=
WORKING

WORKING
    !=
INTEGRATED

INTEGRATED
    !=
VERIFIED

VERIFIED ONCE
    !=
RELIABLE

DONE
    =
REQUIREMENTS SATISFIED
+
EVIDENCE
+
HONEST REPORTING
```

**Do not call it done until the evidence says it is done.**