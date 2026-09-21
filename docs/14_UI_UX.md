# M I K A S A
## UI / UX Architecture Specification

**File:** `docs/14_UI_UX.md`

**Version:** 0.1.0

**Status:** PROPOSED — Pending architecture research and approval

**Authority:** User interface structure, interaction design, task visibility, approvals, memory controls, developer views, responsiveness, and interface consistency

**Applies to:** Web UI, desktop UI, CLI, future mobile interface, future voice interface, task views, memory views, settings, and observability surfaces.

---

# 1. Purpose

This document defines the user-experience and interface architecture for M I K A S A.

The UI must allow users to interact with a complex autonomous system without requiring them to understand its internal architecture.

The interface should make it easy to:

- Talk to Mikasa.
- Give goals.
- See what she is doing.
- Approve sensitive actions.
- Cancel work.
- Inspect task progress.
- Review results.
- Manage memory.
- Configure models and tools.
- Inspect errors.
- View advanced system activity when desired.

The UI must not become the place where agent logic lives.

The core agent runtime must continue functioning independently of any particular graphical interface.

---

# 2. Core UX Principle

Mikasa should present:

```text id="w9c4nm"
SIMPLE OUTSIDE

POWERFUL INSIDE
```

The default experience should not expose every internal subsystem.

A user should be able to begin with:

```text id="98jtap"
USER
  |
  v
M I K A S A
  |
  v
"What do you want me to do?"
```

Advanced information should appear progressively when it becomes useful.

---

# 3. Primary UX Goal

The main experience should feel like communicating with one capable assistant.

Users should not need to decide:

```text id="5qc31f"
Which agent do I talk to?

Which model should perform this step?

Which worker gets this subtask?

Which memory database should be searched?

Which internal tool should run?
```

Mikasa should coordinate those decisions internally according to system configuration and task requirements.

The user may inspect or override advanced behavior when appropriate.

---

# 4. Interface Architecture

All interfaces should communicate with the same application gateway.

```text id="3gsqfe"
                  M I K A S A CORE
                        |
                        v
                APPLICATION GATEWAY
                        |
          +-------------+-------------+
          |             |             |
          v             v             v
         CLI           WEB         DESKTOP
                                      |
                                      v
                                  FUTURE VOICE
```

No interface should maintain a separate copy of Mikasa's core intelligence.

---

# 5. Interface Categories

Mikasa may eventually support:

```text id="aomkp7"
CLI

WEB APPLICATION

DESKTOP APPLICATION

MOBILE CLIENT

VOICE INTERFACE
```

The first user-facing graphical implementation should target the simplest platform that fits the approved architecture.

The core UX model should remain transferable between interfaces.

---

# 6. Primary Screen

The default screen should center on Mikasa.

The primary UI should contain:

- Conversation.
- Task input.
- Current activity.
- Important approvals.
- Relevant task status.
- Results.

It should not initially overwhelm the user with:

- Model metrics.
- Raw event streams.
- Worker trees.
- Internal IDs.
- Database records.
- Token counts.
- System diagnostics.

Those belong in optional advanced views.

---

# 7. Proposed Main Layout

Conceptually:

```text id="r6yy6i"
+----------------------------------------------------------+
| M I K A S A                                      Status  |
+------------------+---------------------------------------+
|                  |                                       |
| Conversations    |              Mikasa                   |
|                  |                                       |
| Tasks            |   User: Fix this project              |
|                  |                                       |
| Memory           |   Mikasa: I'm inspecting the repo...  |
|                  |                                       |
| Tools            |                                       |
|                  |   [ Current task activity ]            |
| Settings         |                                       |
|                  |                                       |
+------------------+---------------------------------------+
|                Ask Mikasa...                     [Send]   |
+----------------------------------------------------------+
```

This is conceptual.

Visual design should be refined later.

---

# 8. Navigation

The initial navigation should remain small.

Suggested primary sections:

```text id="0erc1n"
Mikasa

Tasks

Memory

Settings
```

Optional advanced sections may include:

```text id="a8dzlo"
Tools

Models

Activity

Developer
```

Do not create navigation sections simply because backend modules exist.

A system component does not automatically deserve a visible page.

---

# 9. Main Assistant View

The Main Assistant View should be the primary destination.

It should allow the user to:

- Send messages.
- Attach supported context.
- Start tasks.
- See responses.
- See meaningful progress.
- Review approvals.
- Cancel active work.
- Open relevant artifacts.

The conversation should remain readable even when Mikasa performs many internal operations.

---

# 10. Conversation vs. Task

A conversation and a task are different.

The UI should reflect that distinction without making the user manage it manually.

Example:

```text id="t10yr9"
USER:
Can you inspect my project and fix the failing tests?

MIKASA:
I'll take a look.

[ Task created: Fix failing tests ]
```

The user remains in the conversation.

The system internally creates a structured task.

---

# 11. Task Cards

Active tasks may appear as compact cards.

Example:

```text id="f3hkea"
Fix failing tests

Status: Running

Current:
Inspecting authentication module

Progress:
3 steps completed

[View] [Cancel]
```

Avoid fake precise percentages when remaining work is uncertain.

Prefer meaningful state descriptions.

---

# 12. Task Status Language

Task status shown to users should be understandable.

Internal:

```text id="i8ihlv"
WAITING_APPROVAL
```

UI:

```text id="t8fup1"
Waiting for your approval
```

Internal:

```text id="i82km4"
BLOCKED
```

UI:

```text id="8vzq4f"
Blocked — missing project dependency
```

Internal identifiers may be available in Developer Mode.

---

# 13. Progress Communication

Mikasa should communicate progress without flooding the interface.

Good progress:

```text id="ioiqzf"
Inspecting project structure

Running targeted tests

Investigating failure

Applying fix

Verifying result
```

Poor progress:

```text id="yxliie"
Thinking...

Thinking...

Still thinking...

Processing...

Working...
```

Progress should correspond to actual runtime state.

---

# 14. No Fake Progress

The UI must not display fake activity.

Do not show:

```text id="rq3hl2"
Research Agent researching...
Coding Agent coding...
Security Agent scanning...
```

unless those workers actually exist and are executing.

Decorative animation must remain visually distinguishable from operational status.

---

# 15. Activity Timeline

A task may expose an optional activity timeline.

Example:

```text id="d688de"
19:03  Task started

19:03  Project inspected

19:04  Tests executed

19:04  Failure found in auth.py

19:05  File updated

19:05  Tests passed

19:05  Task verified
```

The timeline should be based on runtime events.

It must not reveal hidden model reasoning.

---

# 16. Detail Levels

Task information should support progressive disclosure.

## Level 1 — Simple

```text id="jt5ewt"
Fixing your project...
```

## Level 2 — Operational

```text id="pvrk56"
Running tests
Investigating auth module
```

## Level 3 — Developer

```text id="sbdxti"
Tool: terminal.execute

Command: project test command

Exit: 1

Execution: exec_004
```

Users should not be forced into Level 3 to understand whether a task is working.

---

# 17. Approvals

Approval prompts must be clear and specific.

Example:

```text id="50giqz"
Mikasa wants to install a dependency inside this project's virtual environment.

Package:
example-package

Reason:
Required by the project's existing test configuration.

Scope:
Current project only

[Allow once] [Deny]
```

Avoid:

```text id="a58gng"
Mikasa needs permission to continue.

[Allow]
```

The user should know what they are authorizing.

---

# 18. Approval Placement

Approval requests should appear:

- In the current conversation.
- In the active task.
- In a central approvals area if necessary.

Critical approval requests must not be hidden inside logs.

---

# 19. Approval States

The UI should distinguish:

```text id="vvwa7s"
PENDING

APPROVED

DENIED

EXPIRED

CANCELLED
```

The user should be able to see the status of a recent approval when it affects task execution.

---

# 20. Cancel

Active autonomous tasks must expose cancellation.

Cancellation controls should be easy to find.

Example:

```text id="dl15ev"
[ Cancel task ]
```

The interface should communicate actual cancellation state.

Examples:

```text id="oqe9v1"
Cancelling...

Cancelled.

Cancellation requested — one external operation had already completed.
```

Do not immediately show "Cancelled" if active work has not actually stopped.

---

# 21. Pause / Resume

Future long-running tasks may expose:

```text id="a4ly3c"
[Pause]

[Resume]
```

These controls should appear only when the runtime genuinely supports pause/resume.

Do not create buttons that simulate unsupported backend functionality.

---

# 22. Task View

The dedicated Task View should eventually show:

```text id="370cbf"
OBJECTIVE

STATUS

CURRENT STEP

PLAN

ACTIVITY

ARTIFACTS

APPROVALS

RESULT

ERRORS
```

Advanced views may also expose:

- Specialist agents.
- Model usage.
- Tool calls.
- Checkpoints.
- Runtime IDs.

---

# 23. Plan Display

When a task has a meaningful plan, the UI may show it.

Example:

```text id="is4cr8"
Plan

✓ Inspect project

✓ Run tests

✓ Identify failing module

→ Implement fix

○ Re-run tests

○ Verify result
```

Plans must reflect actual task state.

A revised plan should update visibly.

---

# 24. Replanning UX

When the plan changes materially, Mikasa may explain why.

Example:

```text id="dtmdr7"
The original issue is caused by a missing configuration value, not the parser itself.

I've updated the remaining plan:

1. Fix configuration loading
2. Re-run the failing tests
3. Verify the affected workflow
```

Do not expose internal chain-of-thought.

Show the operational reason and updated plan.

---

# 25. Task Results

Completed tasks should have a clear result section.

Example:

```text id="f9b3u2"
Completed

Changed:
- src/auth.py

Verified:
- authentication tests passed
- project build completed

Not verified:
- full end-to-end browser flow
```

The result should distinguish what actually happened from what was not checked.

---

# 26. Failure UX

Failures should be understandable.

Poor:

```text id="eb8okp"
Error 5002
```

Better:

```text id="4b5gjs"
I couldn't complete the test run because the project's Python environment is missing a required dependency.

What completed:
- project inspected
- failing test command identified

Blocked by:
- missing package: X
```

Technical details may be expandable.

---

# 27. Artifact UX

Tasks may create artifacts.

Examples:

- Code patches.
- Reports.
- Documents.
- Screenshots.
- Logs.

The UI should present them as identifiable objects.

Example:

```text id="nxjmgu"
Artifacts

Research report
test-results.txt
ui-preview.png
```

Artifacts should link to actual persisted outputs.

---

# 28. Memory View

Mikasa should eventually provide a dedicated Memory interface.

Possible sections:

```text id="6jgk9w"
USER

PROJECTS

KNOWLEDGE

PROCEDURES

SKILLS
```

Memory must not simply display an unorganized list of embeddings or chat messages.

---

# 29. Memory Record Display

A memory item may show:

```text id="9a5bxa"
Project Alpha uses TypeScript

Type:
Semantic

Scope:
Project Alpha

Source:
User correction

Updated:
September 21, 2026

[Edit] [Delete]
```

Advanced metadata can remain hidden unless requested.

---

# 30. Memory Search

The Memory View should allow searching memories.

Possible filters:

- Project.
- Type.
- Scope.
- Date.
- Source.

The UI must distinguish search results from current verified project files.

Stored memory should not be visually represented as an unquestionable fact when its status is uncertain.

---

# 31. Memory Corrections

Users should be able to correct stored information.

Example:

```text id="sdc4ef"
Old:
Project Alpha uses Python

New:
Project Alpha uses TypeScript
```

The UI should explain whether the memory was:

- Updated.
- Superseded.
- Deleted.
- Unable to be changed.

---

# 32. Memory Deletion

Deletion should clearly identify what is being removed.

Example:

```text id="x7068e"
Delete this saved project memory?

"Project Alpha uses TypeScript"

This removes it from Mikasa's active memory store.

It does not automatically delete the original conversation or project files.
```

The backend's actual deletion capabilities must determine the wording.

---

# 33. Tools View

A future Tools View may show available capabilities.

Example:

```text id="b8n7ng"
Filesystem

Status:
Available

Permissions:
Project workspace

Tools:
read_file
search_files
write_file
```

External integrations may display:

```text id="ggdwyy"
GitHub MCP

Connected

12 available tools
```

This is primarily an advanced user/developer feature.

---

# 34. Tool Status

The UI should distinguish:

```text id="tbif0y"
AVAILABLE

UNAVAILABLE

UNCONFIGURED

DISABLED

DEGRADED
```

Do not show a capability as ready merely because its package is installed.

---

# 35. Model Settings

A future Model Settings view may allow:

- Configure provider.
- Select default model.
- Configure profiles.
- Check provider status.
- View supported capabilities.
- Configure local endpoints.

The main conversation should not require manual model selection for every message.

---

# 36. Model Display

Default UI may simply show:

```text id="2fmh4u"
Model:
Default
```

Advanced view may show:

```text id="rpnnmq"
Profile:
Coding

Provider:
Configured provider

Model:
Configured model
```

Provider-specific technical information should be optional.

---

# 37. Settings

Settings should be organized by user intent rather than internal module names.

Possible groups:

```text id="zwu2vn"
General

Models

Permissions

Memory

Tools & Integrations

Appearance

Advanced
```

Avoid exposing hundreds of configuration values in the primary settings screen.

---

# 38. Permission Settings

Users should eventually be able to configure defaults.

Example:

```text id="2c7azf"
Project file reading:
Allow

Project file editing:
Ask / Allow / Deny

Terminal commands:
Ask

External writes:
Always ask

Desktop control:
Deny
```

Actual options must map to the Permission Service.

UI toggles must not implement a separate security system.

---

# 39. Developer Mode

An optional Developer Mode should expose deeper runtime information.

Possible information:

```text id="l5b0s9"
Task IDs

Execution IDs

Tool calls

Model requests

Events

Timing

Token usage

Memory retrieval

Permission decisions
```

Developer Mode must not expose raw credentials or unnecessary private information.

---

# 40. Developer Console

Future versions may include a runtime console.

Conceptual example:

```text id="t573vt"
TASK task_202

STATE RUNNING

MODEL coding-profile

ACTION terminal.execute

RESULT FAILURE

RETRY 1/2
```

This view is for diagnosis.

It should not replace the normal user experience.

---

# 41. Raw Logs

Raw logs should not dominate the UI.

If provided, they should be:

- Searchable.
- Filterable.
- Sanitized.
- Clearly labeled.
- Optional.

Logs must not expose secrets.

---

# 42. Specialist-Agent View

When multi-agent execution exists, the UI may show specialists.

Example:

```text id="q87u9m"
Specialists

Research
Completed
Found dependency documentation

Coding
Running
Investigating parser

Testing
Waiting
Blocked on coding result
```

This must represent actual registered executions.

Do not create decorative fake specialists.

---

# 43. Specialist Details

Advanced view may show:

- Assigned objective.
- Status.
- Tools.
- Task budget.
- Outputs.
- Errors.
- Parent task.

The user should not have to chat separately with every worker to manage normal workflows.

---

# 44. Command Palette

A future command palette may provide fast access to actions.

Examples:

```text id="1kvq8s"
New task

Open memory

Open current project

View active tasks

Open settings

Switch model profile
```

Command palettes should improve navigation.

They should not bypass permission checks.

---

# 45. Keyboard Navigation

Desktop interfaces should support efficient keyboard use.

Potential shortcuts:

```text id="ri4szc"
focus prompt

new conversation

open task panel

open search

cancel generation
```

Exact shortcuts should be chosen during implementation.

Avoid conflicts with operating-system conventions.

---

# 46. Responsive Design

The interface must adapt to different screen sizes.

Desktop may show:

```text id="0ob062"
SIDEBAR + MAIN CONTENT + TASK PANEL
```

Mobile may show:

```text id="jttzqo"
MAIN CONTENT

DRAWER NAVIGATION

BOTTOM INPUT
```

Do not shrink a desktop dashboard into an unusable mobile layout.

---

# 47. Desktop Layout

Desktop should prioritize:

- Conversation width.
- Readability.
- Task visibility.
- Efficient navigation.
- Optional secondary panels.

Avoid excessive card grids.

Mikasa should feel like an assistant interface first, not a corporate analytics dashboard.

---

# 48. Mobile Layout

Future mobile interfaces should prioritize:

- Conversation.
- Voice access.
- Active task status.
- Approval actions.
- Notifications.
- Memory lookup.

Complex developer diagnostics may remain available through separate views.

---

# 49. Visual Hierarchy

Visual priority should roughly follow:

```text id="kop99d"
1. USER / MIKASA INTERACTION

2. CURRENT TASK

3. APPROVALS / ERRORS

4. IMPORTANT RESULTS

5. SECONDARY NAVIGATION

6. TECHNICAL DETAILS
```

Critical status must not be hidden beneath decorative elements.

---

# 50. Design Language

Mikasa should use a clean, restrained interface.

Desired characteristics:

- Dark mode supported.
- Strong typography.
- Spacious layout.
- Minimal unnecessary borders.
- Clear status indicators.
- Subtle motion.
- Focused use of accent color.
- High readability.

Avoid:

- Excessive gradients.
- Every panel glowing.
- Tiny terminal-style text everywhere.
- Constant animations.
- Dense cyberpunk dashboards.
- Decorative system metrics with no user value.

---

# 51. Branding

Primary product name:

```text id="f8b9dk"
M I K A S A
```

The spaced title may be used in prominent branding surfaces.

Technical contexts may use:

```text id="k6sm3p"
Mikasa
```

or:

```text id="39lzok"
mikasa
```

depending on naming conventions.

Branding must not interfere with readability.

---

# 52. Cinematic Startup

A future desktop application may include a short startup experience inspired by cinematic system interfaces.

Example:

```text id="fpilbl"
M I K A S A

INITIALIZING CORE...

MEMORY ONLINE

TOOLS READY

SYSTEM ONLINE
```

The startup sequence must represent real application initialization where practical.

It should remain short.

It must not delay application use unnecessarily.

---

# 53. Startup Honesty

If a subsystem is unavailable, the boot UI must not claim:

```text id="tpu7eq"
MEMORY ONLINE
```

when memory initialization actually failed.

Better:

```text id="d9t677"
MEMORY
DEGRADED
```

or keep detailed component initialization out of the boot animation entirely.

The cinematic layer must remain cosmetic, not deceptive.

---

# 54. Motion

Motion may communicate:

- Loading.
- Task progress.
- Panel transitions.
- State changes.

Motion should be subtle.

Avoid animation that prevents users from reading or interacting efficiently.

Support reduced-motion preferences where appropriate.

---

# 55. Status Indicators

Status indicators should use both text and visual representation.

Do not depend on color alone.

Examples:

```text id="n8z7zw"
Running

Waiting for approval

Blocked

Completed

Failed
```

Icons or color may supplement the text.

---

# 56. Accessibility

The UI should support:

- Keyboard navigation.
- Sufficient contrast.
- Screen-reader-compatible controls.
- Clear focus states.
- Text scaling.
- Meaningful labels.
- Reduced motion.

Accessibility should be part of component design, not an afterthought.

---

# 57. Streaming Responses

When model streaming is supported, the interface may display responses incrementally.

However:

- Tool calls should not appear as completed before execution.
- Partial structured outputs must not be treated as final.
- Cancellation should stop streaming where supported.
- Task status should remain separate from streaming text.

The UI should not confuse:

```text id="hl61ge"
Mikasa is typing
```

with:

```text id="ljx0wp"
Mikasa is executing a task
```

---

# 58. Thinking Indicators

The UI may display neutral processing states such as:

```text id="wfjobu"
Planning...

Inspecting project...

Checking results...
```

Do not expose hidden chain-of-thought.

Operational states are sufficient.

---

# 59. Notifications

Future background tasks may generate notifications.

Useful notifications:

- Task completed.
- Task blocked.
- Approval required.
- Scheduled task failed.
- Important monitored condition detected.

Avoid notifications for every internal step.

---

# 60. Notification Actions

Notifications may include direct actions.

Example:

```text id="x70us5"
Mikasa needs approval to continue.

[Review]
```

or:

```text id="hk807z"
Task completed.

[View result]
```

Notification actions must communicate with the real task system.

---

# 61. Empty States

Empty screens should help the user begin.

Example Memory empty state:

```text id="nln98p"
Mikasa hasn't saved any project memories yet.
```

Example Tasks:

```text id="2l9l8g"
No active tasks.

Give Mikasa something to work on.
```

Avoid overly technical empty-state messaging.

---

# 62. First-Run Experience

The first-run experience should remain short.

Potential steps:

```text id="n2uzab"
WELCOME

SELECT MODEL PROVIDER

CONFIGURE REQUIRED CREDENTIAL

CHOOSE DEFAULT PROJECT PERMISSIONS

START USING MIKASA
```

Optional advanced configuration should be skippable.

Do not force users to configure future features they do not need.

---

# 63. Connection Setup

External tools or integrations should use guided setup.

Example:

```text id="o08nif"
Connect provider

1. Choose provider
2. Add credential
3. Validate connection
4. Review requested permissions
5. Enable
```

The UI must report validation failures accurately.

---

# 64. Error Recovery UX

When an integration fails, the interface should give actionable information.

Example:

```text id="or0pv4"
Model provider unavailable

Reason:
Authentication failed

Action:
Update provider credentials
```

Avoid dumping stack traces into normal UI.

Technical diagnostics can be expandable.

---

# 65. Offline / Degraded State

The UI should eventually communicate degraded operation.

Example:

```text id="2tzt80"
Mikasa is online

Web research unavailable
```

or:

```text id="rjqj2o"
Local mode

Cloud providers disabled
```

One failing integration should not make the entire interface appear broken if other capabilities still work.

---

# 66. Search

Global search may eventually search:

- Conversations.
- Tasks.
- Memories.
- Artifacts.
- Settings.

Search results should identify which category each result belongs to.

A memory result and a historical conversation message should not look identical.

---

# 67. Projects

Future versions may provide a project selector.

Example:

```text id="v3w5bn"
Projects

Mikasa
School App
Portfolio
```

Project selection may influence:

- Workspace.
- Project memory.
- Task history.
- Default permissions.

Project switching must not silently expose one project's private context to another.

---

# 68. Project View

A project view may eventually show:

```text id="r57ybb"
PROJECT OVERVIEW

ACTIVE TASKS

RECENT ACTIVITY

MEMORY

ARTIFACTS

WORKSPACE

SETTINGS
```

This is a future organizational surface.

It should not duplicate every global screen.

---

# 69. File Attachments

The conversation interface may allow file attachment.

Files should display:

- Name.
- Type.
- Size when relevant.
- Upload status.

Attached files must not automatically be granted broad filesystem access.

They become authorized task inputs according to the application's file handling rules.

---

# 70. Voice UX

Future voice interaction should reuse the same core conversation and task model.

Voice should allow:

- Asking Mikasa questions.
- Starting tasks.
- Receiving concise progress.
- Handling approvals where appropriate.
- Interrupting or cancelling.

The Voice System is defined separately in:

`docs/15_VOICE_SYSTEM.md`

---

# 71. UI State Ownership

The UI should not invent application state.

For example:

```text id="47tl15"
Task status
```

must come from the Task Manager.

```text id="e1pkml"
Memory saved
```

must come from the Memory Service.

```text id="v64c0h"
Tool available
```

must come from the Tool Registry.

The UI displays state.

It does not become the authoritative owner of backend state.

---

# 72. Optimistic UI

Optimistic updates may improve responsiveness for low-risk UI actions.

However, important operations should not be displayed as completed until acknowledged by the backend.

Examples requiring confirmation:

- Memory deletion.
- Task cancellation.
- Permission changes.
- Integration connection.
- External actions.

---

# 73. API Contracts

Interfaces should communicate through structured application contracts.

Examples:

```text id="ppuhx2"
CreateTask

SendMessage

GetTask

ListTasks

CancelTask

SubmitApproval

SearchMemory

DeleteMemory

GetSystemStatus
```

The exact API style is not yet selected.

It may be:

- In-process application calls.
- Local HTTP API.
- WebSocket.
- IPC.
- Another approved transport.

The UX architecture must remain transport-independent.

---

# 74. Real-Time Events

The UI should eventually subscribe to operational events.

Examples:

```text id="uowce4"
task.updated

approval.requested

tool.completed

task.completed

task.failed
```

Real-time events may update the interface without constant polling.

The transport implementation remains undecided.

---

# 75. Event Throttling

Some runtime components may produce many events.

The UI should not rerender on every low-level event.

Events may need:

- Aggregation.
- Filtering.
- Throttling.
- Priority levels.

A terminal command producing thousands of output chunks should not freeze the interface.

---

# 76. Terminal Output

When useful, the UI may show terminal output in an expandable panel.

Example:

```text id="k0f92j"
Tests

> npm test

12 passed
1 failed
```

Full logs may be expandable or linked as artifacts.

Normal users should not need to inspect raw terminal output for every task.

---

# 77. Diff View

Coding tasks should eventually support a diff viewer.

Useful capabilities:

- Files changed.
- Added lines.
- Removed lines.
- Side-by-side or unified diff.
- Existing vs. Mikasa changes where possible.

The Diff View should reflect actual version-control or file-comparison data.

---

# 78. Research View

Research tasks may eventually provide:

```text id="n72swf"
QUESTION

PROGRESS

SOURCES

FINDINGS

CITATIONS

CONFLICTS

REPORT
```

The user should be able to inspect which sources support important findings.

---

# 79. Source Cards

Source cards may show:

```text id="cvk0rm"
Title

Publisher

Date

URL

Used for:
Memory architecture findings
```

Do not display unsupported trust scores as objective facts.

---

# 80. System Status

An optional system-status view may show:

```text id="17xs30"
Model Provider      Available

Memory              Available

Tool Runtime         Available

Research             Not configured

Browser              Disabled
```

This helps diagnose system capability.

It should not clutter the main assistant screen.

---

# 81. System Health

Future advanced views may show:

- Provider latency.
- Task queue.
- Worker state.
- Storage health.
- Tool availability.

These belong in Developer or System Status views.

Do not turn them into decorative gauges.

---

# 82. Privacy Indicators

When useful, the UI may indicate data-processing boundaries.

Examples:

```text id="f8pxgm"
Local-only task

Cloud model active

External research enabled
```

These indicators must reflect real routing and permission state.

---

# 83. Local-Only Mode UX

If a task or session is local-only, the UI should make that understandable.

Example:

```text id="5blut4"
Local-only

Cloud models and external tools are disabled for this task.
```

A failed local model must not silently switch to cloud without the UI and policy layer knowing.

---

# 84. Theme

Mikasa should support at least:

```text id="gvtrdr"
DARK

LIGHT

SYSTEM
```

A premium dark theme may be the primary visual direction.

Theme choice must not reduce readability.

---

# 85. Accent Color

Accent color should be used selectively.

Suggested uses:

- Primary action.
- Active navigation.
- Focus indicator.
- Selected state.
- Important status.

Avoid applying accent color to every border and icon.

---

# 86. Typography

Typography should prioritize readability.

The spaced brand title:

```text id="03k482"
M I K A S A
```

may use distinctive typography.

Body content should use a highly readable interface font.

Code and technical IDs should use a monospaced font where appropriate.

---

# 87. Density

Default UI density should be comfortable.

Developer views may support higher information density.

The interface should avoid:

- Huge unused dashboards.
- Extremely narrow chat columns.
- Tiny status text.
- Excessive nested cards.

---

# 88. Component Consistency

Repeated actions should use consistent controls.

Examples:

```text id="wc5qu7"
Cancel task
```

should behave consistently wherever displayed.

```text id="yn6gg3"
Approve
```

should use the same approval semantics across conversation and task views.

Visual consistency must match backend behavior.

---

# 89. Destructive UI Actions

Destructive UI actions should be visually and behaviorally distinct.

Examples:

- Delete memory.
- Disconnect provider.
- Delete task history.
- Remove integration.

Confirmation should reflect actual risk.

Do not add confirmation dialogs to every harmless action.

---

# 90. Loading States

Loading indicators should communicate what the UI is waiting for where useful.

Examples:

```text id="sv9246"
Loading task history...

Connecting to provider...

Retrieving memory...
```

Avoid indefinite generic spinners with no state information.

---

# 91. Long Operations

For long-running tasks, the UI should allow the user to leave the current page without losing task state.

Task progress belongs to the backend.

Navigating away must not cancel a task unless explicitly requested.

---

# 92. Refresh / Restart

Refreshing or restarting the interface should reload durable task state.

A running task should not disappear from the interface merely because the page was refreshed.

This depends on:

`docs/11_STATE_AND_PERSISTENCE.md`

---

# 93. Failure After Refresh

If a task cannot reconnect after refresh, the interface should query the authoritative task state.

It must not assume:

```text id="efaz1o"
Task disappeared = task failed.
```

The backend determines the actual status.

---

# 94. UX for Unknown Outcome

Some actions may have uncertain results.

The UI must represent this honestly.

Example:

```text id="vg16xx"
Action status unknown

The connection ended before Mikasa could confirm whether the external operation completed.

[Verify status]
```

Do not show success or failure without evidence.

---

# 95. UX for Partial Completion

Partial outcomes should be clear.

Example:

```text id="cwafgz"
Partially completed

Completed:
- identified the bug
- prepared a fix

Blocked:
- test environment could not start
```

Partial success must not be visually identical to full completion.

---

# 96. UX for Verification

Tasks should optionally show verification.

Example:

```text id="3vx56d"
Verified

✓ targeted tests passed
✓ changed file inspected
○ full browser flow not tested
```

This helps users understand what the completion claim actually means.

---

# 97. First UI Milestone

The first graphical UI should remain deliberately small.

Suggested screens:

```text id="l721b7"
MIKASA

TASKS

SETTINGS
```

Optional:

```text id="m61egj"
MEMORY
```

if the Memory Service is already implemented.

Do not build every future dashboard before core runtime functionality exists.

---

# 98. First UI Features

Initial UI should support:

| Feature | Initial UI |
|---|---|
| Send message | Required |
| Receive response | Required |
| Create autonomous task | Required |
| View active task | Required |
| View task status | Required |
| Cancel task | Required |
| Handle approval request | Required |
| View final result | Required |
| View meaningful error | Required |
| Basic settings | Required |
| Memory management | When memory milestone is available |
| Specialist visualization | Deferred |
| Advanced tool manager | Deferred |
| Developer event viewer | Deferred |
| Voice | Deferred |
| 3D avatar | Deferred |
| Cinematic startup | Deferred |
| Complex analytics dashboard | Deferred |

---

# 99. First UI Acceptance Scenario

The graphical interface should demonstrate:

## Step 1

User opens Mikasa.

The interface clearly shows the primary assistant view.

## Step 2

User requests:

```text id="d6x4la"
Inspect this project and fix the failing test.
```

## Step 3

The task begins.

The interface shows actual status:

```text id="w24sje"
Running

Inspecting project
```

## Step 4

If approval is required, the user receives a clear prompt.

## Step 5

The task progresses.

The user can inspect meaningful activity.

## Step 6

The task completes.

The UI shows:

- Result.
- Files changed.
- Verification.
- Limitations.

## Step 7

Refreshing the UI retains the completed task through persisted backend state.

No fake task data may be used for the acceptance test.

---

# 100. Responsive Acceptance

The initial UI should also be verified at:

- Typical desktop width.
- Narrow laptop width.
- Mobile-width viewport.

The mobile view should preserve access to:

- Conversation.
- Task status.
- Approval.
- Cancellation.
- Results.

---

# 101. UX Testing Requirements

## UI-TEST-001 — Main Conversation

Verify messages can be submitted and displayed correctly.

## UI-TEST-002 — Task Creation

Verify a user request can create a real backend task.

## UI-TEST-003 — Status Synchronization

Verify UI task status matches the authoritative Task Manager state.

## UI-TEST-004 — Approval

Verify approval requests display the actual requested action and scope.

## UI-TEST-005 — Approval Decision

Verify approval and denial are sent to the correct backend operation.

## UI-TEST-006 — Cancellation

Verify cancellation controls affect the actual task.

## UI-TEST-007 — Failure

Verify backend failures produce understandable UI feedback.

## UI-TEST-008 — Refresh

Verify durable task state remains visible after interface refresh.

## UI-TEST-009 — Responsive Layout

Verify primary workflows remain usable at supported viewport sizes.

## UI-TEST-010 — Accessibility

Verify keyboard access and meaningful labels for critical controls.

## UI-TEST-011 — Fake State Prevention

Verify the UI does not show unavailable tools, workers, or completed actions as operational.

## UI-TEST-012 — Memory

When memory UI exists, verify memory edits and deletions reflect actual Memory Service results.

---

# 102. UI Development Sequence

**UI-0 — Research**

Study simple assistant interfaces and relevant open-source agent UIs.

**UI-1 — Information Architecture**

Define screens, navigation, and state hierarchy.

**UI-2 — Design System**

Define typography, spacing, components, and themes.

**UI-3 — Main Assistant**

Implement conversation and task input.

**UI-4 — Task Status**

Add real task progress and result rendering.

**UI-5 — Approval UX**

Add approval requests and responses.

**UI-6 — Task View**

Add dedicated task inspection.

**UI-7 — Settings**

Add basic model and permission configuration.

**UI-8 — Persistence Integration**

Ensure task state survives interface refresh.

**UI-9 — Responsive Design**

Support desktop and mobile layouts.

**UI-10 — Evaluation**

Run end-to-end usability tests against real backend state.

Future phases may add memory dashboards, tools, specialists, developer views, voice, cinematic startup, and desktop-specific features.

---

# 103. Research Requirements

Before selecting the final UI architecture, research useful patterns from the provided projects.

## eDEX-UI

Repository:

https://github.com/GitSquared/edex-ui

Research only for:

- Startup atmosphere.
- Terminal-inspired presentation.
- System initialization visuals.
- Motion and sound concepts.

Do not use eDEX-UI as the primary interaction architecture.

The goal is inspiration for an optional startup layer, not to turn Mikasa into an eDEX clone.

---

## OpenHands

Research:

- Agent task visibility.
- Coding workspace UX.
- Event presentation.
- Task status.
- Agent-computer interaction.

---

## Gemini CLI

Research:

- Minimal agent interaction.
- Permission prompts.
- Tool-call visibility.
- Terminal-first UX.

---

## OpenClaw

Research:

- Multi-interface patterns.
- Session organization.
- Configuration.
- Tools and integration management.

---

## AstrBot

Research:

- Multi-channel assistant interfaces.
- Plugin configuration.
- Extension discovery.
- Chat organization.

Research findings should focus on UX patterns rather than copying visual identity.

---

# 104. Architecture Decisions Required

The following decisions must eventually be resolved:

```text id="s26csq"
UI-ADR-001
Initial interface platform.

UI-ADR-002
UI framework.

UI-ADR-003
Core layout and navigation.

UI-ADR-004
Application communication transport.

UI-ADR-005
Real-time event transport.

UI-ADR-006
Design-system approach.

UI-ADR-007
Task-progress representation.

UI-ADR-008
Approval interaction pattern.

UI-ADR-009
Memory management interface.

UI-ADR-010
Developer mode architecture.

UI-ADR-011
Future desktop packaging.

UI-ADR-012
Future cinematic startup implementation.
```

These are planning identifiers.

Approved decisions belong in:

`docs/22_DECISION_LOG.md`

---

# 105. Definition of Done

The first UI milestone is complete when:

- The user can interact with Mikasa through a clean primary assistant view.
- Messages use the real backend.
- Autonomous tasks can be started.
- Active task status is visible.
- Task state comes from the Task Manager.
- Approval requests can be reviewed and answered.
- Tasks can be cancelled.
- Errors are understandable.
- Completed results clearly show what was done.
- Verification information is presented accurately.
- Refreshing the interface preserves durable task state.
- The layout works on supported desktop and mobile widths.
- Critical controls are keyboard accessible.
- The UI does not pretend unavailable backend capabilities exist.
- The end-to-end acceptance scenario succeeds.

---

# 106. Final UX Principle

Mikasa should feel simple even when the system behind her is complex.

The interface must preserve these distinctions:

```text id="3z474n"
ACTIVITY
    !=
PROGRESS

MODEL OUTPUT
    !=
TASK RESULT

TASK RESULT
    !=
VERIFIED RESULT

DECORATIVE UI
    !=
SYSTEM STATE

INTERNAL COMPLEXITY
    !=
USER COMPLEXITY
```

The default experience should answer four questions clearly:

```text id="pdyfdn"
WHAT DID I ASK MIKASA TO DO?

WHAT IS SHE DOING NOW?

DOES SHE NEED ANYTHING FROM ME?

WHAT ACTUALLY HAPPENED?
```

Everything else is secondary.

**One assistant at the surface. Deep capability underneath. Show complexity only when it helps the user.**