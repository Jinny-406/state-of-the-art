# M I K A S A
## Coding Engine Architecture Specification

**File:** `docs/13_CODING_ENGINE.md`

**Version:** 0.1.0

**Status:** PROPOSED — Pending architecture research and approval

**Authority:** Repository inspection, coding workflows, editing, testing, debugging, verification, version-control integration, and software-development task execution

**Applies to:** Main Agent, Coding Specialist, Testing Specialist, Agent Runtime, Tool System, Terminal Tools, Filesystem Tools, Browser Tools, Git integration, and project workspaces.

---

# 1. Purpose

This document defines the architecture and operational behavior of Mikasa's Coding Engine.

The Coding Engine allows Mikasa to work on software projects in a controlled, inspectable, and verifiable way.

Its purpose is to support tasks such as:

- Understanding an unfamiliar codebase.
- Locating relevant files.
- Fixing defects.
- Adding features.
- Refactoring code.
- Running tests.
- Debugging failures.
- Inspecting diffs.
- Working with project documentation.
- Running development commands.
- Reviewing implementation quality.
- Creating project artifacts.
- Supporting future browser-based UI verification.

The Coding Engine must integrate with the Agent Runtime, Tool System, Permission Service, State Service, Model Router, and future Multi-Agent System.

It must not become an independent agent architecture.

---

# 2. Core Coding Principle

Mikasa should not begin by editing.

She should begin by understanding.

The default software-development workflow is:

```text id="mkvvve"
RECEIVE TASK
    |
    v
UNDERSTAND OBJECTIVE
    |
    v
INSPECT PROJECT
    |
    v
IDENTIFY RELEVANT AREA
    |
    v
UNDERSTAND EXISTING BEHAVIOR
    |
    v
FORM IMPLEMENTATION PLAN
    |
    v
MAKE SMALLEST CORRECT CHANGE
    |
    v
RUN RELEVANT CHECKS
    |
    v
INSPECT RESULT / DIFF
    |
    v
VERIFY TASK REQUIREMENTS
    |
    v
REPORT OUTCOME
```

Editing before understanding the project should be treated as an exception, not the default.

---

# 3. Coding Engine Goals

The Coding Engine should eventually support:

- Repository discovery.
- Project classification.
- Source-tree inspection.
- Dependency inspection.
- Configuration inspection.
- Code search.
- Symbol search.
- File reading.
- Structured editing.
- Patch application.
- New file creation.
- Controlled deletion.
- Test execution.
- Linting.
- Type checking.
- Build execution.
- Debugging.
- Git inspection.
- Diff review.
- UI preview and browser verification.
- Coding specialist delegation.
- Testing specialist delegation.
- Checkpoints and rollback.
- Long-running development tasks.

The first implementation should focus on a reliable local coding workflow.

---

# 4. Architecture Overview

```text id="50ut7d"
                   USER TASK
                       |
                       v
                  MAIN AGENT
                       |
                       v
                 CODING ENGINE
                       |
      +----------------+----------------+
      |                |                |
      v                v                v
 PROJECT INSPECTOR   CODE SEARCH     CHANGE PLANNER
      |                |                |
      +----------------+----------------+
                       |
                       v
                  EDITING LAYER
                       |
                       v
                 TOOL EXECUTOR
                       |
         +-------------+-------------+
         |             |             |
         v             v             v
     FILESYSTEM      TERMINAL        GIT
         |             |             |
         +-------------+-------------+
                       |
                       v
                 TESTING LAYER
                       |
                       v
               RESULT VERIFICATION
                       |
                       v
                  DIFF REVIEW
                       |
                       v
                 FINAL OUTCOME
```

This diagram represents logical responsibilities.

The MVP may implement them using a small number of focused modules.

---

# 5. Coding Task

A coding task should have a structured objective.

Conceptual structure:

```text id="dof3xh"
CodingTask:
    task_id

    workspace_id
    objective

    scope
    constraints

    acceptance_criteria

    allowed_changes

    required_checks

    status
```

The task must preserve the user's requested outcome.

Examples:

```text id="m8zm2m"
Fix the failing login test.

Add a settings page.

Refactor this parser without changing behavior.

Make the dashboard responsive.

Investigate why the build fails.
```

Each requires a different workflow.

---

# 6. Workspace

Every coding task must operate inside an explicitly approved workspace.

A workspace may contain:

- Repository root.
- Project files.
- Configuration.
- Dependencies.
- Tests.
- Generated artifacts.

The Coding Engine must obtain workspace access through the Tool System and Permission Service.

It must not assume unrestricted filesystem access.

---

# 7. Workspace Record

Conceptual structure:

```text id="338d52"
Workspace:
    workspace_id

    root_path

    project_type

    vcs_type

    permissions

    environment_reference

    metadata
```

The exact structure must be defined later.

Workspace paths must be resolved securely according to:

`docs/10_SECURITY_PERMISSIONS.md`

---

# 8. Project Inspection

Before making substantial changes, Mikasa should inspect the project.

Inspection may include:

```text id="s90jjl"
ROOT FILES

DIRECTORY STRUCTURE

README

PACKAGE / DEPENDENCY FILES

BUILD CONFIGURATION

TEST CONFIGURATION

ENTRY POINTS

SOURCE DIRECTORIES

EXISTING DOCUMENTATION

VERSION-CONTROL STATUS
```

The objective is not to read every file.

The objective is to understand enough of the project to act safely and correctly.

---

# 9. Project Classification

Mikasa should identify relevant project characteristics where possible.

Examples:

```text id="72cyev"
LANGUAGE

FRAMEWORK

PACKAGE MANAGER

BUILD SYSTEM

TEST FRAMEWORK

LINTER

TYPE CHECKER

APPLICATION TYPE

VERSION CONTROL
```

This information may influence which commands and workflows are appropriate.

Project classification must come from actual project evidence.

Mikasa must not assume a stack based only on filenames when stronger evidence is available.

---

# 10. Repository Map

For non-trivial projects, the Coding Engine should build a lightweight mental or structured map.

Example:

```text id="wfsvm2"
PROJECT
|
+-- src/
|   +-- api/
|   +-- ui/
|   +-- core/
|
+-- tests/
|
+-- config/
|
+-- package.json
|
+-- README.md
```

The repository map may include:

- Important directories.
- Entry points.
- Core modules.
- Test locations.
- Configuration files.
- Generated directories to avoid.

The map should remain focused.

Do not scan huge dependency or generated folders unnecessarily.

---

# 11. Excluded Paths

The Coding Engine should respect common and project-specific exclusion rules.

Examples:

```text id="p26ll8"
.git/

node_modules/

venv/

.venv/

dist/

build/

coverage/

generated/

cache/
```

These are examples only.

Project configuration should determine the actual exclusions.

Excluded paths may still be inspected when specifically required.

---

# 12. Source Search

Mikasa needs efficient code search.

Potential capabilities:

```text id="56scvy"
code.search_text

code.find_file

code.find_symbol

code.find_references
```

Initial implementations may reuse filesystem search and available language tooling.

Search should help narrow relevant code before large-scale reading.

---

# 13. Search Strategy

A good coding investigation often proceeds:

```text id="jgnkud"
ERROR MESSAGE
    |
    v
SEARCH EXACT TEXT
    |
    v
LOCATE TEST / CALL SITE
    |
    v
TRACE RELEVANT SYMBOLS
    |
    v
READ LOCAL CONTEXT
```

or:

```text id="s8c2g7"
FEATURE NAME
    |
    v
SEARCH ROUTES / COMPONENTS
    |
    v
FIND EXISTING PATTERN
    |
    v
IDENTIFY FILES TO CHANGE
```

The Coding Engine should prefer targeted search over reading the entire repository.

---

# 14. Code Understanding

Before editing a relevant file, Mikasa should understand:

- What the file does.
- How it is called.
- What assumptions it makes.
- Which tests cover it.
- What neighboring modules depend on it.
- Whether similar patterns already exist elsewhere.

The goal is to minimize accidental architectural inconsistency.

---

# 15. Architecture Awareness

Coding changes should respect the project's existing architecture unless the task explicitly authorizes architectural modification.

Examples:

If the project already uses:

```text id="71efvv"
service layer
repository abstraction
shared UI components
central configuration
```

Mikasa should generally follow those patterns.

She should not introduce a second competing architecture simply because another pattern is familiar.

---

# 16. Source-of-Truth Files

Some repositories contain governance or instruction files.

Examples:

```text id="pq69oq"
AGENTS.md

CONTRIBUTING.md

README.md

architecture docs

style guides

project configuration
```

The Coding Engine must inspect relevant instruction files before modifying the project.

Project-specific instructions take precedence over generic coding preferences unless they conflict with higher-level trusted rules.

---

# 17. Change Planning

Before editing non-trivial code, Mikasa should create a focused implementation plan.

A change plan may contain:

```text id="vi9bp7"
ChangePlan:
    objective

    files_to_inspect
    files_expected_to_change

    proposed_changes

    validation_steps

    risks
```

Plans should not invent files before inspection has shown they are needed.

The plan may evolve as new information is discovered.

---

# 18. Smallest Correct Change

The Coding Engine should prefer the smallest change that correctly satisfies the requirement.

Avoid unnecessary:

- Rewrites.
- Refactors.
- Dependency changes.
- File renaming.
- Architectural changes.
- Formatting of unrelated files.

A task to fix one bug should not normally produce a project-wide rewrite.

---

# 19. Scope Expansion

During implementation, Mikasa may discover adjacent issues.

The Coding Engine must classify them.

```text id="j2hh0w"
REQUIRED FOR CURRENT TASK

SMALL INCIDENTAL FIX

UNRELATED ISSUE

FUTURE IMPROVEMENT
```

Only necessary work and justified incidental changes belong in the current task.

Unrelated improvements should be reported or added to backlog rather than silently implemented.

---

# 20. Editing Layer

All source changes must use approved filesystem or patch tools.

Potential operations:

```text id="np8eeu"
code.apply_patch

filesystem.write_file

filesystem.create_file

filesystem.rename

filesystem.delete
```

Direct modification must remain inside the authorized workspace.

The Coding Engine should prefer structured patches for existing files when practical.

---

# 21. Patch Model

A patch should clearly represent intended changes.

Conceptually:

```text id="6j2cmg"
Patch:
    target_file

    base_reference

    changes

    expected_result
```

The exact representation depends on the tool.

Patches should fail safely when the target file no longer matches expected context.

Silent approximate editing can create corruption.

---

# 22. Concurrent Modification

A file may change between inspection and edit.

Before applying a change, the tool layer should support detecting stale assumptions where practical.

Possible mechanisms:

- File hash.
- Modification time.
- Expected text context.
- Version-control state.

If a conflict is detected, Mikasa should re-read the file before modifying it.

---

# 23. New Files

Creating a new file should be justified by project structure and task requirements.

Before creating one, Mikasa should check:

- Whether an equivalent file already exists.
- Where similar files are located.
- Naming conventions.
- Module registration requirements.
- Test placement.
- Import/export patterns.

Avoid creating duplicate functionality.

---

# 24. File Deletion

Deleting source files is higher risk than ordinary modification.

Before deletion, verify:

- The file is no longer required.
- References have been updated.
- The task authorizes removal.
- Version control or another recovery mechanism exists where appropriate.

Large or uncertain deletions may require stronger approval.

---

# 25. Dependency Changes

Adding or changing dependencies should be deliberate.

Before adding a dependency, consider:

- Is it actually necessary?
- Can existing project functionality solve the problem?
- Is the package compatible with the project?
- Is its license appropriate?
- Is it maintained?
- What additional complexity does it introduce?

Do not add large dependencies to solve trivial problems.

---

# 26. Dependency Installation

Installing dependencies must use the approved environment.

Prefer:

```text id="9vj2yt"
PROJECT-LOCAL ENVIRONMENT
```

over:

```text id="jt957r"
GLOBAL SYSTEM INSTALLATION
```

where practical.

Global environment modifications should require separate authorization.

---

# 27. Environment Discovery

Before running commands, Mikasa should determine the project's expected environment.

Potential indicators:

```text id="ukhozv"
package.json

pyproject.toml

requirements.txt

Cargo.toml

go.mod

pom.xml

build.gradle

Makefile

Dockerfile

lock files
```

The Coding Engine should follow project evidence rather than guessing command syntax.

---

# 28. Terminal Execution

Commands must run through:

`terminal.execute`

or an equivalent approved tool.

Every command should have:

- Working directory.
- Timeout.
- Permission context.
- Captured stdout.
- Captured stderr.
- Exit code.

The Coding Engine must not equate command execution with command success.

---

# 29. Command Selection

Prefer existing project scripts.

Example:

If `package.json` provides:

```text id="vltwvf"
"test": "vitest"
```

prefer:

```text id="esvtm8"
npm test
```

or the project's documented command rather than inventing a different test invocation.

Likewise, use existing build, lint, and formatting scripts where possible.

---

# 30. Testing Strategy

Testing should be proportional to the change.

Possible validation levels:

```text id="akf8rb"
TARGETED TEST

RELATED TEST SUITE

LINT

TYPE CHECK

BUILD

FULL TEST SUITE

MANUAL / UI VERIFICATION
```

A one-line isolated change may not require every available check.

A major refactor requires stronger validation.

---

# 31. Test Selection

Mikasa should prefer the narrowest reliable verification first.

Example:

```text id="dq62qz"
CHANGED LOGIN VALIDATION
      |
      v
RUN LOGIN TESTS
      |
      v
RUN RELATED AUTH TESTS
      |
      v
RUN BROADER SUITE IF NEEDED
```

This can reduce iteration time while still ending with appropriate confidence.

---

# 32. Tests Are Evidence

A passing test is evidence that specific behavior works.

It is not proof that the entire application is correct.

Mikasa must interpret test results according to their coverage.

Example:

```text id="e7v4ml"
1 UNIT TEST PASSES
```

does not automatically imply:

```text id="tzwdse"
APPLICATION VERIFIED
```

---

# 33. Test Failure

When a test fails after a change, Mikasa should determine whether:

- The implementation is wrong.
- The test exposes a pre-existing failure.
- The test environment is broken.
- The test expectation is outdated.
- Another dependency is missing.

The Coding Engine must not automatically modify tests simply to make them pass.

---

# 34. Test Modification

Changing a test may be legitimate when requirements change.

Before changing tests, Mikasa should identify:

- Why the existing expectation is incorrect.
- Which requirement changed.
- Whether production behavior or test behavior is authoritative.
- Whether additional tests are needed.

A failing test must not be weakened merely because it blocks completion.

---

# 35. Debugging Loop

Debugging should follow an evidence-driven process.

```text id="yfhs3b"
REPRODUCE FAILURE
    |
    v
OBSERVE ERROR
    |
    v
NARROW LOCATION
    |
    v
FORM HYPOTHESIS
    |
    v
CHECK HYPOTHESIS
    |
    v
IMPLEMENT FIX
    |
    v
RE-RUN TEST
    |
    v
VERIFY
```

Avoid changing multiple unrelated variables simultaneously when a narrower experiment can isolate the problem.

---

# 36. Error Reproduction

A bug should ideally be reproduced before being modified.

Reproduction provides:

- Baseline evidence.
- Clear failure signal.
- Verification target.
- Protection against fixing the wrong problem.

When reproduction is impossible, Mikasa should state that limitation.

---

# 37. Root Cause vs. Symptom

The Coding Engine should attempt to distinguish the root cause from symptoms.

Example:

```text id="36pmpz"
TEST FAILS BECAUSE API RETURNS NULL

WHY?

CONFIG VALUE NEVER INITIALIZED
```

Fixing the assertion may hide the symptom.

Initializing the required value may fix the cause.

Root-cause investigation should remain proportional to the task.

---

# 38. Diff Inspection

After editing, Mikasa should inspect the resulting changes.

Potential tool:

```text id="p8u3w1"
git.diff
```

or equivalent file comparison.

Diff review should check:

- Only intended files changed.
- No accidental deletions.
- No debug code remains.
- No secrets were introduced.
- Formatting changes are reasonable.
- Generated files were not unintentionally modified.

Diff review is required before declaring significant coding tasks complete.

---

# 39. Version Control

The Coding Engine should integrate with version-control systems when available.

Initial focus may be Git.

Potential capabilities:

```text id="6ai0fu"
git.status

git.diff

git.log

git.show

git.branch
```

Write operations such as commit, branch creation, reset, or rebase should be introduced according to task needs and permissions.

---

# 40. Git Safety

Mikasa must not assume a clean repository.

Before modifying a version-controlled project, inspect:

```text id="owmbyg"
git status
```

or equivalent.

Existing user changes must not be silently overwritten.

Mikasa must distinguish:

```text id="abv6o0"
PRE-EXISTING CHANGES

MIKASA CHANGES
```

This distinction is critical for safe editing.

---

# 41. Existing User Changes

If the workspace already contains modifications, Mikasa should avoid destructive operations that could erase them.

Do not automatically use:

```text id="ln2u39"
git reset --hard
```

or similar destructive commands.

If the user's existing changes materially conflict with the task, report the conflict and choose a safe path.

---

# 42. Checkpoint Strategy

For significant coding tasks, future versions may create checkpoints.

Possible mechanisms:

- Git branch.
- Git commit.
- Patch file.
- File snapshot.
- Workspace copy.

The appropriate checkpoint depends on repository state and user authorization.

Do not create commits automatically unless the active workflow permits it.

---

# 43. Commit Behavior

Committing changes is distinct from editing files.

A future permission may be:

```text id="32612p"
git.commit
```

The system should not assume permission to commit merely because it has project write access.

Likewise:

```text id="fhyuxo"
git.push
```

is an external action and must be treated separately.

---

# 44. Remote Repository Actions

Future Git hosting integrations may support:

```text id="k9v9vj"
repository.read

repository.create_branch

repository.push

repository.open_pr

repository.comment
```

These are external actions.

They require provider authentication, permissions, and appropriate approvals.

Local coding access must not imply remote repository write access.

---

# 45. Build Verification

Some projects require compilation or bundling.

The Coding Engine should identify the project's build command and run it when relevant.

Build success should be captured as structured evidence.

Build warnings should not automatically be treated as fatal unless the project policy requires it.

---

# 46. Linting

Linting may detect:

- Syntax issues.
- Style violations.
- Unsafe patterns.
- Unused code.
- Project-specific standards.

Mikasa should use the project's existing lint configuration where available.

Do not introduce an entirely new linter merely to validate one task without explicit need.

---

# 47. Type Checking

For typed projects, type checking may be an important verification step.

Examples:

```text id="8o3tr7"
tsc

mypy

pyright

cargo check
```

Actual commands must come from project configuration or documented conventions.

Do not invent type-check commands when the project does not use them.

---

# 48. Formatting

Mikasa should follow the project's formatter.

If automatic formatting is needed, prefer:

- Project scripts.
- Existing formatter configuration.
- Targeted formatting when possible.

Avoid reformatting the entire repository for a small change.

Large formatting diffs make review harder.

---

# 49. Static Analysis

Future coding workflows may use:

- Security scanners.
- Dependency analysis.
- Dead-code analysis.
- Complexity analysis.

These are additional tools.

They should be introduced only when relevant to task requirements.

---

# 50. Code Review Pass

Before final completion, Mikasa should perform a brief review of her own change.

Questions:

```text id="9ws0ar"
DOES THIS SOLVE THE ACTUAL TASK?

IS THERE A SIMPLER CHANGE?

DID I MODIFY UNRELATED CODE?

ARE FAILURE CASES HANDLED?

DO THE TESTS ACTUALLY VERIFY THE CHANGE?

ARE THERE OBVIOUS REGRESSIONS?

DID I LEAVE TEMPORARY DEBUG CODE?

DID I INTRODUCE NEW DEPENDENCIES?
```

This review should use observable code and diff state, not just self-confidence.

---

# 51. UI Development

Frontend tasks may require visual verification.

The Coding Engine should eventually integrate with a browser or application preview system.

Potential flow:

```text id="j6rc9k"
EDIT UI
   |
   v
START DEVELOPMENT SERVER
   |
   v
OPEN PREVIEW
   |
   v
INSPECT RENDERED UI
   |
   v
COMPARE AGAINST REQUIREMENT
   |
   v
ADJUST
```

Visual verification is distinct from unit testing.

---

# 52. Browser Preview

Future browser preview capabilities may include:

```text id="wxxw5m"
open_local_app

inspect_page

capture_screenshot

inspect_console

inspect_network

interact_with_ui
```

These should use the shared Browser System rather than a separate hidden coding browser.

---

# 53. Visual Editing Workflow

For UI tasks, Mikasa may eventually support a loop such as:

```text id="930k7j"
USER REQUEST
    |
    v
INSPECT PAGE
    |
    v
IDENTIFY COMPONENT
    |
    v
EDIT SOURCE
    |
    v
REFRESH PREVIEW
    |
    v
VISUALLY VERIFY
    |
    v
RUN RELEVANT TESTS
```

Visual feedback must come from actual rendered output.

Do not fabricate browser state.

---

# 54. Coding Specialist

Future multi-agent workflows may delegate implementation work to a Coding Specialist.

The specialist should receive:

- Scoped objective.
- Workspace.
- Relevant project context.
- Allowed tools.
- Permission scope.
- Acceptance criteria.
- Execution budget.

It should return:

- Summary.
- Changed files.
- Diff reference.
- Tests run.
- Verification result.
- Blockers.

---

# 55. Testing Specialist

A Testing Specialist may independently verify work.

Potential responsibilities:

- Determine relevant checks.
- Run tests.
- Inspect failures.
- Verify acceptance criteria.
- Report regressions.

A Testing Specialist should not automatically modify production code.

Its permissions may be read-only plus controlled test execution unless the delegated task requires more.

---

# 56. Separation of Implementation and Verification

For higher-confidence workflows, implementation and verification may be separated.

Example:

```text id="4r53ue"
CODING SPECIALIST
       |
       v
PROPOSED CHANGE
       |
       v
TESTING SPECIALIST
       |
       v
INDEPENDENT VERIFICATION
       |
       v
MAIN AGENT
```

This is a future multi-agent capability.

The MVP may use one agent with a disciplined verification pass.

---

# 57. Coding Artifacts

A coding task may produce:

```text id="klzi98"
PATCH

DIFF

NEW SOURCE FILE

TEST RESULT

BUILD LOG

SCREENSHOT

DEBUG REPORT

MIGRATION

DOCUMENTATION
```

Important artifacts should be associated with the task.

Large logs should be stored as references rather than embedded directly into task metadata.

---

# 58. Generated Files

Some commands generate files.

The Coding Engine should distinguish intentional source changes from generated artifacts.

Examples:

```text id="aod8do"
lock files

compiled output

coverage reports

generated clients

snapshots
```

Generated changes should only be kept when required.

Unexpected generated modifications should be reviewed before completion.

---

# 59. Secrets in Code

Mikasa must avoid introducing secrets into source code.

Before completion, relevant changes should be checked for accidental inclusion of:

- API keys.
- Tokens.
- Passwords.
- Private certificates.
- Secret environment values.

Credentials must use the approved secrets architecture.

---

# 60. External Code and Dependencies

Mikasa may inspect public source code or documentation for guidance.

External code must not be copied blindly.

Before incorporating third-party code, consider:

- License.
- Compatibility.
- Security.
- Maintainability.
- Whether a smaller original implementation is appropriate.

The Research Engine may support these checks.

---

# 61. Code Provenance

For substantial third-party-derived implementation ideas, Mikasa should retain enough provenance to understand their source when required.

This is especially important for:

- External libraries.
- Copied algorithms.
- Generated code from external templates.
- Imported modules.

The exact provenance policy should be defined later.

---

# 62. Failure Handling

Coding tasks may fail because of:

```text id="ykvvyd"
MISSING DEPENDENCY

INVALID ENVIRONMENT

TEST FAILURE

BUILD FAILURE

PERMISSION DENIAL

FILE CONFLICT

UNAVAILABLE TOOL

UNSUPPORTED PLATFORM

AMBIGUOUS REQUIREMENT

RESOURCE LIMIT
```

The Coding Engine must classify the failure before choosing a recovery strategy.

---

# 63. Recovery

Possible recovery actions include:

- Re-read changed files.
- Inspect logs.
- Re-run a narrower test.
- Restore a checkpoint.
- Revert Mikasa's own change.
- Modify the implementation.
- Revise the plan.
- Report a blocker.

Recovery must not destroy pre-existing user work.

---

# 64. Reverting Mikasa's Own Changes

The Coding Engine should eventually support reverting changes made during the current task.

This requires distinguishing:

```text id="mz35af"
USER'S EXISTING CHANGES
```

from:

```text id="jo1bxs"
MIKASA'S TASK CHANGES
```

Reversion mechanisms must never blindly reset the entire repository.

---

# 65. Failed Experiment Workflow

A safe experiment may proceed:

```text id="b63j5v"
CHECKPOINT
    |
    v
MAKE CHANGE
    |
    v
RUN TEST
    |
    +---- SUCCESS ---> KEEP
    |
    +---- FAILURE ---> REVIEW
                         |
                         +---- MODIFY
                         |
                         +---- REVERT TASK CHANGE
```

Experiments must remain bounded.

Do not repeatedly mutate code without understanding the outcomes.

---

# 66. Long-Running Coding Tasks

Future coding tasks may span many steps or sessions.

They may require:

- Persistent plan.
- Stable workspace.
- Checkpoints.
- Task artifacts.
- Execution history.
- Pause/resume.
- Specialist coordination.

Long-running work must use the State and Persistence architecture.

---

# 67. Context Management for Coding

Large repositories cannot fit entirely into model context.

The Coding Engine should provide targeted context.

Potential context:

```text id="jghf8p"
TASK OBJECTIVE

PROJECT RULES

RELEVANT FILES

RELATED SYMBOLS

CURRENT DIFF

TEST OUTPUT

ACTIVE PLAN
```

Do not repeatedly include huge unrelated files.

Code context selection must preserve the information needed for correct editing.

---

# 68. Repository Memory

Useful project information may eventually be stored as project memory.

Examples:

- Build commands.
- Architecture decisions.
- Important module locations.
- Verified recurring issues.

Authoritative repository files remain the source of truth.

Stored project memory must not override current source code or current project documentation.

---

# 69. Coding Skills

Future skill memory may contain validated procedures such as:

```text id="knd12e"
Run project test suite.

Create database migration.

Add a route according to project conventions.

Build release artifact.
```

Skills must include:

- Applicability.
- Required tools.
- Procedure.
- Verification.

A workflow should not become a reusable skill merely because it worked once.

---

# 70. Coding Observability

Potential coding events:

```text id="uad4xb"
coding.workspace_inspected

coding.file_read

coding.search_completed

coding.plan_created

coding.patch_applied

coding.command_started

coding.command_completed

coding.test_started

coding.test_completed

coding.diff_reviewed

coding.verification_completed
```

Events should reference actual task and execution identifiers.

---

# 71. Coding Report

At task completion, Mikasa should report useful implementation information.

Suggested structure:

```text id="5b3hq0"
RESULT

Changed:
- files or components affected

Verified:
- tests/build/lint checks run

Outcome:
- what now works

Limitations:
- anything not verified

Follow-up:
- only relevant remaining issues
```

Do not claim checks were run if they were not.

---

# 72. No Fake Verification

The following statements are prohibited unless actually supported by execution evidence:

```text id="yhlth5"
"All tests pass."

"The app builds successfully."

"The UI works perfectly."

"There are no bugs."

"The fix is production-ready."
```

Mikasa should instead report exactly what was verified.

Example:

```text id="gf73h9"
The targeted authentication tests passed. I did not run the full application test suite.
```

---

# 73. MVP Coding Scope

The first Coding Engine milestone should support:

| Capability | MVP |
|---|---|
| Approved workspace | Required |
| Project inspection | Required |
| File search | Required |
| File reading | Required |
| File modification | Required |
| Basic patching | Required |
| Controlled terminal execution | Required |
| Test execution | Required |
| Test-result capture | Required |
| Git status inspection | Required when Git exists |
| Diff inspection | Required |
| Task verification | Required |
| Existing-change protection | Required |
| Browser UI verification | Deferred |
| Remote repository writes | Deferred |
| Git commits | Deferred unless separately approved |
| Parallel coding agents | Deferred |
| Automated PR creation | Deferred |
| Advanced language-server integration | Deferred |
| Automatic dependency upgrades | Deferred |

---

# 74. MVP Acceptance Scenario

The Coding Engine must complete a real coding task.

Example project:

A small repository containing one intentionally failing test.

User objective:

```text id="m5n4zk"
Inspect this project, find why the test fails, fix the bug, and verify it.
```

Expected execution:

```text id="ldqffq"
1. VERIFY WORKSPACE

2. READ PROJECT INSTRUCTIONS

3. INSPECT REPOSITORY

4. CHECK VERSION-CONTROL STATUS

5. IDENTIFY TEST COMMAND

6. RUN TEST

7. CAPTURE FAILURE

8. SEARCH RELEVANT CODE

9. IDENTIFY ROOT CAUSE

10. PLAN CHANGE

11. MODIFY RELEVANT FILE

12. RUN TARGETED TEST

13. INSPECT DIFF

14. VERIFY ACCEPTANCE CRITERIA

15. REPORT RESULT
```

All actions must use real tools.

Hard-coded test results or simulated file modifications do not satisfy the scenario.

---

# 75. Dirty Repository Scenario

The acceptance suite must include a repository containing pre-existing user changes.

Expected behavior:

- Detect existing modifications.
- Preserve them.
- Make only task-relevant changes.
- Distinguish Mikasa's changes from existing changes.
- Avoid destructive reset operations.

The task must not erase the user's work.

---

# 76. Failed Fix Scenario

The acceptance suite should include a proposed fix that does not solve the problem.

Expected behavior:

```text id="k3x0b1"
APPLY CHANGE
    |
    v
RUN TEST
    |
    v
TEST STILL FAILS
    |
    v
DO NOT CLAIM SUCCESS
    |
    v
INVESTIGATE / REVISE / REVERT
```

This demonstrates that verification controls completion.

---

# 77. Testing Requirements

## CODE-TEST-001 — Workspace Enforcement

Verify coding operations remain within the approved workspace.

## CODE-TEST-002 — Project Inspection

Verify Mikasa can identify relevant project structure.

## CODE-TEST-003 — Instruction Discovery

Verify project instruction files are inspected when relevant.

## CODE-TEST-004 — Search

Verify source search identifies relevant code.

## CODE-TEST-005 — File Modification

Verify authorized edits modify the intended file.

## CODE-TEST-006 — Existing Changes

Verify pre-existing modifications are not silently overwritten.

## CODE-TEST-007 — Terminal Execution

Verify coding commands run within the approved environment.

## CODE-TEST-008 — Failed Command

Verify non-zero command exit codes are captured correctly.

## CODE-TEST-009 — Test Verification

Verify the agent does not mark a task complete when relevant tests fail.

## CODE-TEST-010 — Diff Review

Verify completion includes inspection of task-related changes.

## CODE-TEST-011 — Scope Preservation

Verify unrelated refactors are not silently added to a focused bug fix.

## CODE-TEST-012 — Dependency Change

Verify dependency additions occur only when the task requires them.

## CODE-TEST-013 — Secret Protection

Verify source modifications do not intentionally embed configured secrets.

## CODE-TEST-014 — Failed Experiment

Verify Mikasa can recover from or clearly report an unsuccessful task change.

## CODE-TEST-015 — Integrated Coding

Verify a real failing test can be diagnosed, fixed, and verified end-to-end.

---

# 78. Development Sequence

**CODE-0 — Research**

Study coding-agent architectures in ZCode, Gemini CLI, OpenHands, and relevant systems.

**CODE-1 — Workspace Contracts**

Define workspace, project, change, command, and verification records.

**CODE-2 — Project Inspector**

Implement repository inspection and project classification.

**CODE-3 — Search**

Implement efficient code and file search.

**CODE-4 — Editing**

Implement safe file modification and patching.

**CODE-5 — Terminal**

Integrate controlled project command execution.

**CODE-6 — Testing**

Implement test-command discovery and result handling.

**CODE-7 — Git Awareness**

Add Git status and diff inspection.

**CODE-8 — Verification**

Create coding-task completion rules.

**CODE-9 — Runtime Integration**

Connect the Coding Engine to the Agent Runtime.

**CODE-10 — Evaluation**

Run real debugging and feature-development tasks.

Future milestones may add browser preview, language-server tooling, specialist collaboration, Git automation, and remote repository workflows.

---

# 79. Research Requirements

Before finalizing implementation, inspect the following systems.

## ZCode

Research:

- Repository context management.
- Coding-agent loops.
- Tool use.
- File editing.
- Testing.
- Error recovery.
- Planning.

## Gemini CLI

Research:

- Filesystem tools.
- Shell execution.
- Project instruction handling.
- Tool confirmation.
- Coding workflows.
- Diff or change management.

## OpenHands

Research:

- Coding environments.
- Sandbox design.
- Workspace handling.
- Agent-computer interaction.
- Test execution.
- Long-running coding tasks.

## Claude-Code-Like Repository Reference

If researching the supplied repository claimed to contain Claude Code-related source material, treat provenance and licensing as unresolved until independently verified.

Research only architecture and workflow concepts unless legal provenance is clear.

Do not assume it can be copied into Mikasa.

## DeerFlow

Research:

- Coding subagents.
- Sandbox integration.
- Skill use.
- Multi-step developer workflows.

Research findings must distinguish actual source-code behavior from documentation claims.

---

# 80. Architecture Decisions Required

The following decisions must be resolved:

```text id="3tiaq5"
CODE-ADR-001
Workspace representation.

CODE-ADR-002
Repository inspection strategy.

CODE-ADR-003
Search tooling.

CODE-ADR-004
File editing and patch format.

CODE-ADR-005
Stale-file conflict detection.

CODE-ADR-006
Command execution environment.

CODE-ADR-007
Test discovery strategy.

CODE-ADR-008
Git integration level.

CODE-ADR-009
Checkpoint and rollback strategy.

CODE-ADR-010
Coding artifact representation.

CODE-ADR-011
Future browser-preview integration.

CODE-ADR-012
Future language-server integration.
```

These are planning identifiers.

Approved decisions belong in:

`docs/22_DECISION_LOG.md`

---

# 81. Definition of Done

The first Coding Engine milestone is complete when:

- Mikasa can operate inside an explicitly approved project workspace.
- Relevant project instructions are discovered.
- Repository structure can be inspected.
- Files can be searched and read.
- Appropriate files can be edited safely.
- Commands execute through the controlled terminal.
- Test failures can be captured and interpreted.
- Mikasa can modify code in response to actual evidence.
- Relevant tests can be re-run.
- Version-control status is inspected when Git is available.
- Existing user modifications are preserved.
- The resulting diff is reviewed.
- Completion depends on actual verification.
- Failures are reported accurately.
- The integrated debugging scenario succeeds.
- Relevant automated tests pass.

Advanced coding capabilities must not be presented as implemented before their own requirements are satisfied.

---

# 82. Final Coding Principle

Mikasa must behave like a careful engineer, not a text generator with filesystem access.

The Coding Engine must preserve these distinctions:

```text id="m7g17a"
READING CODE
    !=
UNDERSTANDING THE PROJECT

EDITING A FILE
    !=
FIXING THE BUG

COMMAND EXECUTED
    !=
COMMAND SUCCEEDED

TEST PASSED
    !=
EVERYTHING WORKS

MODEL THINKS IT IS DONE
    !=
TASK VERIFIED
```

The core development loop is:

```text id="72n04q"
INSPECT
   |
   v
UNDERSTAND
   |
   v
PLAN
   |
   v
EDIT
   |
   v
TEST
   |
   v
REVIEW DIFF
   |
   v
VERIFY
   |
   v
REPORT
```

**Understand first. Change deliberately. Test what matters. Preserve the user's work. Verify before claiming success.**