# M I K A S A
## Observability, Events & Diagnostics Specification

**File:** `docs/17_OBSERVABILITY.md`

**Version:** 0.1.0

**Status:** PROPOSED — Pending architecture research and approval

**Authority:** Events, logs, traces, metrics, execution timelines, diagnostics, model/tool usage visibility, and system health

**Applies to:** Agent Runtime, Task Manager, Tool System, Model Router, Memory Service, Research Engine, Coding Engine, Voice System, Multi-Agent System, UI, persistence, and future background workers.

---

# 1. Purpose

This document defines how M I K A S A records, exposes, and analyzes operational behavior.

Observability exists so developers and users can answer questions such as:

- What is Mikasa doing?
- What task is currently active?
- Which step failed?
- Which tool was called?
- Which model handled a request?
- Why is a task blocked?
- How long did an action take?
- Which permissions were checked?
- What evidence supports completion?
- Which component is unavailable?
- What happened before a crash?

Observability must not expose hidden chain-of-thought.

It should expose operational facts.

---

# 2. Core Principle

Mikasa should be able to explain system behavior through recorded state and events.

```text
ACTION
  |
  v
EVENT
  |
  v
STRUCTURED RECORD
  |
  v
TIMELINE / TRACE / METRIC
  |
  v
DIAGNOSIS
```

The system should not depend on:

```text
"I think this is probably what happened."
```

when runtime evidence is available.

---

# 3. Observability Layers

Mikasa should eventually support four primary observability layers:

```text
EVENTS

LOGS

TRACES

METRICS
```

Each serves a different purpose.

---

# 4. Events

Events describe something meaningful that happened.

Examples:

```text
task.created

task.started

tool.started

tool.completed

model.requested

model.completed

approval.requested

memory.updated

task.completed
```

Events are structured facts.

They should be machine-readable.

---

# 5. Logs

Logs contain diagnostic messages.

Examples:

```text
Tool registry initialized.

Provider connection failed.

Checkpoint validation failed.
```

Logs may contain more implementation detail than events.

They should still be structured where practical.

---

# 6. Traces

A trace links related operations together.

Example:

```text
USER REQUEST
   |
   v
TASK
   |
   v
PLAN
   |
   v
MODEL REQUEST
   |
   v
TOOL CALL
   |
   v
TOOL RESULT
   |
   v
VERIFICATION
```

A trace helps explain how one request moved through the system.

---

# 7. Metrics

Metrics summarize behavior over time.

Examples:

```text
task completion count

tool failure rate

model request latency

retry rate

memory lookup latency

active task count
```

Metrics should help identify system trends.

They must not replace detailed execution records.

---

# 8. Architecture Overview

```text
                  MIKASA COMPONENTS
                         |
                         v
                    EVENT BUS
                         |
              +----------+----------+
              |          |          |
              v          v          v
          EVENT STORE   LOGGER    METRICS
              |          |          |
              +----------+----------+
                         |
                         v
                    TRACE LAYER
                         |
                         v
               OBSERVABILITY SERVICE
                         |
              +----------+----------+
              |                     |
              v                     v
         USER TIMELINE         DEVELOPER VIEW
```

This is conceptual.

The MVP may use a simpler implementation.

---

# 9. Observability Service

Suggested component:

`ObservabilityService`

Responsibilities:

- Receive structured events.
- Add trace metadata.
- Store durable events when required.
- Send logs to configured sinks.
- Update metrics.
- Support task timeline queries.
- Support system-health queries.
- Apply redaction rules.
- Provide diagnostics to interfaces.

The service must not become the authoritative owner of task state.

---

# 10. Event Bus

Mikasa should use an internal event mechanism.

The MVP may use an in-process event bus.

A distributed event broker is not required initially.

Conceptually:

```text
EventBus:
    publish(event)

    subscribe(event_type, handler)
```

The exact interface depends on implementation language.

---

# 11. Event Structure

A common event schema should exist.

Proposed structure:

```text
Event:
    event_id
    event_type

    timestamp

    task_id
    execution_id
    session_id

    agent_id
    component

    trace_id
    span_id

    severity

    payload
```

Not every field is required on every event.

---

# 12. Event Identity

Every event should have a stable unique identifier.

Example:

```text
evt_01H...
```

Identifier format is implementation-specific.

Stable identifiers help with:

- Debugging.
- Correlation.
- Deduplication.
- Audit.
- Persistence.

---

# 13. Event Types

Event names should follow a consistent pattern.

Recommended:

```text
domain.action
```

Examples:

```text
task.created

tool.completed

model.failed

memory.retrieved

research.source_added

coding.test_failed
```

Avoid inconsistent naming such as:

```text
TASKDONE

ToolCallSuccess

finished_model
```

---

# 14. Event Categories

Suggested categories:

```text
SYSTEM

TASK

EXECUTION

MODEL

TOOL

MEMORY

PERMISSION

RESEARCH

CODING

VOICE

AGENT

SECURITY

PERSISTENCE

IMPROVEMENT
```

These categories may help filtering and metrics.

---

# 15. Event Severity

Possible severity levels:

```text
DEBUG

INFO

WARNING

ERROR

CRITICAL
```

Severity should represent operational importance.

Do not classify normal retries as critical failures unless they threaten system integrity.

---

# 16. Task Events

Important task events may include:

```text
task.created

task.queued

task.started

task.progress_updated

task.waiting_approval

task.blocked

task.paused

task.resumed

task.completed

task.failed

task.cancelled
```

These should correspond to real task state.

---

# 17. Execution Events

Possible execution events:

```text
execution.created

execution.started

execution.step_started

execution.step_completed

execution.retry_started

execution.replan_started

execution.verification_started

execution.completed

execution.failed
```

Execution events provide finer detail than task events.

---

# 18. Model Events

Potential events:

```text
model.requested

model.selected

model.started

model.stream_started

model.completed

model.failed

model.timeout

model.fallback_selected
```

Useful metadata may include:

- Provider.
- Model.
- Latency.
- Usage.
- Capability profile.

Full prompt contents should not be logged by default.

---

# 19. Tool Events

Potential events:

```text
tool.requested

tool.authorized

tool.denied

tool.started

tool.completed

tool.failed

tool.timeout

tool.cancelled
```

Relevant metadata:

- Tool ID.
- Task ID.
- Duration.
- Result status.
- Side-effect class.

Arguments may require redaction.

---

# 20. Permission Events

Potential events:

```text
permission.checked

permission.granted

permission.denied

approval.requested

approval.granted

approval.denied

approval.expired
```

Permission events are particularly useful for security diagnostics.

---

# 21. Memory Events

Potential events:

```text
memory.query_started

memory.retrieved

memory.created

memory.updated

memory.superseded

memory.deleted

memory.retrieval_failed
```

Memory event payloads should avoid unnecessary exposure of private content.

---

# 22. Research Events

Potential events:

```text
research.created

search.started

search.completed

source.discovered

source.retrieved

evidence.created

claim.verified

research.completed
```

These support research timelines and debugging.

---

# 23. Coding Events

Potential events:

```text
coding.workspace_opened

coding.file_read

coding.patch_applied

coding.command_started

coding.command_completed

coding.test_started

coding.test_completed

coding.diff_reviewed
```

These should reflect actual tool operations.

---

# 24. Voice Events

Potential events:

```text
voice.listening_started

voice.transcription_completed

voice.speech_started

voice.speech_stopped

voice.error
```

Raw audio should not be embedded in normal events.

---

# 25. Multi-Agent Events

Possible events:

```text
agent.selected

agent.spawned

agent.task_delegated

agent.completed

agent.failed

agent.cancelled
```

These become relevant only after real multi-agent execution exists.

---

# 26. Improvement Events

Potential events:

```text
improvement.candidate_created

improvement.experiment_started

improvement.experiment_completed

improvement.promoted

improvement.rejected

improvement.rolled_back
```

These should preserve candidate and version references.

---

# 27. Trace IDs

A trace ID connects operations belonging to one logical request or task execution.

Example:

```text
trace_abc123
```

Events across:

- Model Router.
- Tool Executor.
- Memory Service.
- Task Manager.

may share the same trace ID.

---

# 28. Span IDs

A span represents a specific operation within a trace.

Example:

```text
trace: trace_abc123

span:
model_request_01
```

Child spans may represent nested actions.

This is useful for performance diagnosis.

---

# 29. Trace Example

```text
TRACE task_42

├── Context Preparation
│
├── Model Request
│
├── Tool Call: read_file
│
├── Model Request
│
├── Tool Call: terminal.execute
│
├── Verification
│
└── Final Result
```

The UI may show a simplified version.

---

# 30. No Hidden Reasoning Exposure

Observability should expose:

```text
what operation occurred

what input class it received

what tool/model was used

what result status occurred

what operational reason caused a transition
```

It should not expose private hidden chain-of-thought.

Example:

Good:

```text
Replanned because the required test command failed with "command not found".
```

Not required:

```text
full private internal reasoning sequence
```

---

# 31. Operational Reasons

State transitions should have structured reasons where useful.

Example:

```text
reason_code:
TOOL_UNAVAILABLE

message:
Required test command was unavailable.
```

This provides useful explanations without requiring hidden reasoning.

---

# 32. Log Structure

Logs should preferably be structured.

Example:

```text
timestamp=...
level=ERROR
component=ModelRouter
task_id=task_42
message="Provider timeout"
provider=provider_a
```

Structured logs improve filtering and search.

---

# 33. Human-Readable Logs

Human-readable console output may still exist.

Example:

```text
[21:44:02] ERROR ModelRouter provider timeout
```

It should derive from structured information where practical.

---

# 34. Logging Levels

Configuration may support:

```text
DEBUG

INFO

WARNING

ERROR
```

Production-like usage should not require debug logging by default.

Debug mode must still protect secrets.

---

# 35. Debug Mode

Debug mode may expose additional:

- Request metadata.
- Execution timing.
- Tool arguments after redaction.
- Provider responses after sanitization.
- Internal state transitions.

Debug mode must not mean:

```text
log everything including secrets
```

---

# 36. Redaction

Observability output must support redaction.

Sensitive values include:

- API keys.
- Access tokens.
- Passwords.
- Private credentials.
- Secret environment variables.

Possible representation:

```text
Authorization: [REDACTED]
```

Prevention of unnecessary logging is better than relying only on redaction.

---

# 37. Sensitive User Data

Logs should avoid storing unnecessary:

- User messages.
- Memory contents.
- Uploaded documents.
- Private source code.

If detailed content logging is required for development, it should be explicit and configurable.

---

# 38. Tool Argument Logging

Tool arguments should be classified.

Safe example:

```text
tool:
filesystem.read_file

path:
src/auth.py
```

Potentially sensitive example:

```text
tool:
external_api.request

authorization:
secret value
```

Sensitive fields must be removed or redacted.

---

# 39. Model Prompt Logging

Full model prompts should not be logged by default.

Possible safe metadata:

```text
model_id

input token count

message count

tool count

request duration
```

Optional development capture may use a separate protected mechanism.

---

# 40. Tool Result Logging

Large tool outputs should not flood logs.

Instead:

```text
result_status:
SUCCESS

result_size:
182 KB

artifact_ref:
artifact_123
```

Relevant summaries may be included.

Full content may remain in task artifacts when appropriate.

---

# 41. Metrics Architecture

Metrics should summarize operational behavior.

Possible categories:

```text
COUNTERS

GAUGES

HISTOGRAMS

TIMERS
```

Exact telemetry technology is undecided.

---

# 42. Core Task Metrics

Potential metrics:

```text
tasks_created_total

tasks_completed_total

tasks_failed_total

tasks_cancelled_total

tasks_active

task_duration
```

These provide high-level system health.

---

# 43. Model Metrics

Potential metrics:

```text
model_requests_total

model_errors_total

model_request_latency

input_tokens_total

output_tokens_total

fallback_count
```

Where token usage is unavailable, do not fabricate it.

---

# 44. Tool Metrics

Potential metrics:

```text
tool_calls_total

tool_failures_total

tool_timeouts_total

tool_duration

permission_denials_total
```

Metrics may be grouped by tool type.

---

# 45. Memory Metrics

Potential metrics:

```text
memory_reads_total

memory_writes_total

memory_retrieval_latency

memory_conflicts_total
```

Avoid metrics that expose private memory contents.

---

# 46. Research Metrics

Potential metrics:

```text
search_queries_total

sources_retrieved_total

source_failures_total

citations_generated_total

research_duration
```

These support Research Engine evaluation.

---

# 47. Coding Metrics

Potential metrics:

```text
files_read_total

files_modified_total

test_runs_total

test_failures_total

build_runs_total

coding_task_duration
```

These should not become targets that incentivize unnecessary actions.

---

# 48. Voice Metrics

Potential metrics:

```text
voice_sessions_total

stt_latency

tts_latency

transcription_failures_total
```

Do not retain raw audio for metrics.

---

# 49. Metric Cardinality

Metrics must avoid uncontrolled label explosion.

Bad:

```text
task_id as metric label
```

for every task.

Better:

Use task IDs in events/traces.

Metrics should use bounded dimensions such as:

```text
tool_type

status

provider
```

---

# 50. System Health

Mikasa should eventually expose system health.

Possible components:

```text
Task Manager

Persistence

Model Router

Memory Service

Tool Registry

Research Engine

Voice

Browser
```

Possible statuses:

```text
HEALTHY

DEGRADED

UNAVAILABLE

UNCONFIGURED
```

---

# 51. Health Check

Conceptual result:

```text
SystemHealth:
    component
    status
    checked_at
    details
```

Health checks should be lightweight.

Do not repeatedly perform expensive external calls solely for UI decoration.

---

# 52. Readiness vs. Health

A component can be alive but not ready.

Example:

```text
Model Router:
running

Configured provider:
unavailable
```

The system should distinguish:

```text
PROCESS HEALTH

OPERATIONAL READINESS
```

---

# 53. Task Timeline

Each task should eventually provide a timeline.

Example:

```text
21:40 Task created

21:40 Project inspection started

21:41 Tests executed

21:41 Test failure observed

21:42 Patch applied

21:42 Tests passed

21:42 Verification completed

21:42 Task completed
```

Timeline entries should come from actual events.

---

# 54. User Timeline

The normal user timeline should be concise.

It may hide:

- Low-level retries.
- Model request IDs.
- Internal span details.

unless they materially affect the task.

---

# 55. Developer Timeline

Developer mode may expose:

- Event IDs.
- Trace IDs.
- Tool calls.
- Model requests.
- Retry counts.
- Permission decisions.
- Latency.

This provides deeper debugging.

---

# 56. Error Records

Errors should use normalized structures.

Conceptual schema:

```text
ErrorRecord:
    error_id

    category
    code

    component

    message

    task_id
    execution_id

    recoverable

    cause_reference

    created_at
```

Errors should not depend only on free-text messages.

---

# 57. Error Categories

Common categories may include:

```text
VALIDATION

PERMISSION

MODEL

TOOL

NETWORK

PERSISTENCE

RESOURCE

TIMEOUT

CANCELLATION

INTERNAL

UNKNOWN
```

Subsystems may define more specific error codes.

---

# 58. Error Chains

Some failures result from earlier failures.

Example:

```text
Task blocked
  caused by
Tool unavailable
  caused by
Provider authentication failure
```

Error chains can help diagnosis.

Avoid exposing raw stack traces in normal UI.

---

# 59. Stack Traces

Stack traces are useful for developers.

They should be:

- Available in debug contexts.
- Stored securely.
- Sanitized where necessary.

Normal user views should receive understandable error summaries.

---

# 60. Correlation

Every operational error should be traceable to:

```text
TASK

EXECUTION

COMPONENT

EVENT
```

where applicable.

This prevents isolated error messages with no context.

---

# 61. Completion Evidence

Observability should preserve evidence supporting task completion.

Possible references:

```text
test result

diff

file state

research sources

external confirmation
```

A task completion event may include references to verification artifacts.

---

# 62. Verification Record

Conceptual structure:

```text
VerificationRecord:
    verification_id

    task_id

    criteria

    evidence_references

    status

    verified_at
```

Verification should remain separate from general model output.

---

# 63. Unknown Outcome Events

External actions with uncertain outcomes should emit:

```text
action.unknown_outcome
```

The event should preserve:

- Action reference.
- External target.
- Attempt time.
- Verification requirement.

This helps recovery logic.

---

# 64. Retry Visibility

Retries should be observable.

Example:

```text
Attempt 1:
provider timeout

Attempt 2:
success
```

The user does not need to see every retry unless relevant.

Developers should be able to inspect them.

---

# 65. Replan Visibility

Replanning should produce an event.

Example:

```text
plan.revised

reason:
Original test command unavailable.
```

The old plan should remain traceable where persisted.

---

# 66. Permission Audit

Sensitive actions should allow review of:

```text
requested capability

resource scope

decision

approval reference

execution result
```

This supports security audits.

---

# 67. Credential Observability

Observability may report:

```text
credential_ref:
github_primary
```

It must not report:

```text
actual_secret_value
```

Credential use should be observable without revealing the credential.

---

# 68. Audit Log

Some actions may require a stronger audit log than ordinary diagnostics.

Examples:

- Permission changes.
- Credential use.
- External write actions.
- Plugin installation.
- Security-policy changes.
- Self-improvement promotion.

Audit records should be durable when policy requires it.

---

# 69. Audit vs. Debug Logs

Audit records and debug logs are different.

Debug logs:

```text
help developers troubleshoot
```

Audit records:

```text
record important security or state-changing actions
```

Audit records should not disappear merely because debug logging is disabled.

---

# 70. Retention

Different observability data may require different retention.

Possible classes:

```text
TRANSIENT

SHORT_TERM

TASK_LIFETIME

LONG_TERM_AUDIT
```

The exact policy should be decided later.

Do not keep every debug log forever by default.

---

# 71. Log Rotation

Local deployments should prevent unbounded log growth.

Possible controls:

- Maximum file size.
- Maximum age.
- Maximum retained files.

Exact values belong in configuration.

---

# 72. Artifact-Based Diagnostics

Large diagnostic outputs may be stored as artifacts.

Examples:

- Full test logs.
- Browser traces.
- Model debug captures.
- Crash dumps.

Events can reference these artifacts instead of embedding them.

---

# 73. Crash Diagnostics

On unexpected failure, Mikasa should try to preserve:

- Last task.
- Active execution.
- Last event.
- Recent errors.
- Safe stack trace.
- Persistence status.

Crash handling must not itself expose secrets.

---

# 74. Startup Diagnostics

On startup, the system may record:

```text
system.started

database.ready

model_router.ready

tool_registry.ready

memory.ready
```

If a component fails:

```text
system.component_degraded
```

The UI can then report accurate startup status.

---

# 75. Performance Tracing

Tracing may help identify latency bottlenecks.

Example:

```text
Total task step:
5.2s

Context assembly:
0.2s

Model:
3.8s

Tool:
0.9s

Persistence:
0.1s
```

This can guide performance optimization.

---

# 76. Slow Operation Detection

Future systems may flag unusually slow operations.

Examples:

```text
tool call exceeded expected duration

memory query slow

model provider degraded
```

Thresholds should be based on actual baseline data.

---

# 77. Resource Observability

Future local deployments may expose:

```text
CPU

RAM

disk

GPU

network
```

These metrics should be optional.

They matter when running local models or sandbox workloads.

Do not clutter normal UI with them.

---

# 78. Cost Observability

Where provider pricing and usage information are available, Mikasa may show:

```text
task model usage

session usage

estimated or provider-reported cost
```

The UI must distinguish:

```text
REPORTED COST

ESTIMATED COST

UNKNOWN
```

Never fabricate exact cost.

---

# 79. Token Observability

Where supported:

```text
input tokens

output tokens

total tokens
```

Token usage is useful for optimization.

It should not become a primary user-facing metric unless the user wants it.

---

# 80. Privacy Controls

Users should eventually be able to configure some telemetry behavior.

Potential settings:

```text
Local diagnostics:
On

Detailed prompt logging:
Off

Anonymous telemetry:
Off
```

Any external telemetry must be explicitly documented.

The default local-first architecture should avoid unnecessary external reporting.

---

# 81. External Telemetry

If Mikasa ever supports remote telemetry, it must define:

- Destination.
- Data collected.
- Retention.
- User controls.
- Opt-in/opt-out behavior.

External telemetry is not required for the MVP.

---

# 82. Observability API

Internal services should be able to emit events through a simple contract.

Conceptual example:

```text
emit_event(
    type,
    task_id,
    execution_id,
    payload
)
```

Components should not each invent their own incompatible telemetry format.

---

# 83. Query API

Interfaces may need:

```text
get_task_timeline(task_id)

get_trace(trace_id)

get_recent_errors()

get_system_health()

get_metrics_summary()
```

Exact transport remains undecided.

---

# 84. Real-Time Updates

The UI may subscribe to new events.

Conceptually:

```text
task.updated

approval.requested

task.completed
```

Real-time delivery should not change event semantics.

---

# 85. Backpressure

High-volume event streams can overwhelm consumers.

Future event infrastructure may require:

- Buffering.
- Dropping low-priority debug events.
- Batching.
- Rate limiting.

Critical state-change events must not be silently lost.

---

# 86. Event Durability

Events may have durability classes.

Example:

```text
EPHEMERAL:
voice level update

DURABLE:
task.completed

AUDIT:
permission.granted
```

Durability should match importance.

---

# 87. Event Ordering

Within one execution, important state transitions should have consistent ordering.

Example:

```text
tool.started
```

must not normally appear after:

```text
tool.completed
```

Persistence and async processing may require sequence numbers or timestamps.

---

# 88. Clock Handling

Events should use consistent timestamps.

Preferred:

```text
UTC internally
```

Interfaces may render local time.

Do not mix timezone-less timestamps across components.

---

# 89. Sequence Numbers

For some event streams, a monotonically increasing sequence number may help reconstruct order.

This is optional for MVP.

---

# 90. Duplicate Events

Distributed or retried operations may emit duplicates.

Future consumers should use event IDs or idempotency rules where necessary.

The MVP can remain simpler if all event handling is local and synchronous.

---

# 91. Observability and Persistence

Observability data and authoritative state must remain distinct.

```text
EVENT:
task.started

STATE:
Task.status = RUNNING
```

The Task Manager owns task state.

Observability records what happened.

---

# 92. Observability and Memory

Logs and events are not agent memory.

They should not automatically enter Memory Service.

An improvement or memory process may later extract useful information deliberately.

---

# 93. Observability and Self-Improvement

The Self-Improvement System depends on trustworthy operational data.

Examples:

- Repeated tool failures.
- Repeated user corrections.
- Recovery success.
- Model performance.

Improvement analysis should use structured observability rather than vague recollection.

---

# 94. Observability and Security

Security-sensitive observability must support:

- Auditability.
- Redaction.
- Permission-based access to diagnostics.
- Retention controls.

Normal users should not automatically see every internal security event.

---

# 95. Observability and Multi-Agent

Future multi-agent systems should share the same trace context.

Example:

```text
PARENT TRACE
   |
   +-- Research Specialist trace
   |
   +-- Coding Specialist trace
   |
   +-- Testing Specialist trace
```

This allows one task to be inspected coherently.

---

# 96. UI Integration

The main UI should consume simplified observability.

Examples:

```text
Inspecting project

Running tests

Waiting for approval

Task completed
```

Developer Mode may consume detailed events.

The UI should not parse raw logs to infer task state.

---

# 97. CLI Integration

CLI users may see concise structured activity.

Example:

```text
[task] Inspecting repository
[tool] Running tests
[result] 1 test failed
[action] Applying patch
[verify] Tests passed
```

Verbose mode may show additional details.

---

# 98. Developer Mode Filters

Useful filters:

```text
Task

Component

Severity

Event type

Tool

Model

Time range
```

Filtering makes large traces manageable.

---

# 99. Search

Developer observability should support search across:

- Events.
- Errors.
- Tool names.
- Task IDs.
- Trace IDs.

Do not build advanced full-text infrastructure before it is needed.

---

# 100. Export

Future developer tools may export:

- Task trace.
- Error report.
- Diagnostic bundle.

Exports must apply redaction.

They should never accidentally include secrets.

---

# 101. Diagnostic Bundle

A diagnostic bundle might contain:

```text
system version

component health

task timeline

errors

sanitized logs

configuration summary
```

It should exclude:

```text
raw credentials

unnecessary private memory

full private documents
```

---

# 102. MVP Scope

The first observability milestone should support:

| Capability | MVP |
|---|---|
| Structured events | Required |
| Task events | Required |
| Execution events | Required |
| Tool events | Required |
| Model events | Required |
| Error records | Required |
| Basic trace IDs | Required |
| Human-readable logs | Required |
| Secret redaction | Required |
| Task timeline | Required |
| System health | Basic |
| Basic duration metrics | Required |
| Persistent audit events | Basic where security requires |
| Advanced distributed tracing | Deferred |
| External telemetry | Deferred |
| Complex dashboards | Deferred |
| Large-scale metrics backend | Deferred |
| Crash bundle export | Deferred |

---

# 103. MVP Acceptance Scenario

A coding task runs:

```text
Inspect project, fix failing test, verify result.
```

The observability system must allow reconstruction of:

```text
1. task created

2. execution started

3. project files inspected

4. model request made

5. test command executed

6. test failed

7. file modification executed

8. test executed again

9. test passed

10. verification completed

11. task completed
```

Each important operation must be associated with the correct task and execution.

---

# 104. Failure Acceptance Scenario

A tool times out.

Expected records:

```text
tool.started

tool.timeout

execution.recovery_started
```

If retry succeeds:

```text
tool.completed
```

The trace should show both attempts.

---

# 105. Security Acceptance Scenario

A tool requests unauthorized filesystem access.

Expected:

```text
permission.checked

permission.denied

tool.denied
```

No file contents should appear in logs.

---

# 106. Redaction Acceptance Scenario

A configured provider uses an API secret.

Expected:

Observability may show:

```text
credential_ref:
provider_primary
```

but must not show the raw API key.

---

# 107. Testing Requirements

## OBS-TEST-001 — Event Structure

Verify emitted events follow the approved schema.

## OBS-TEST-002 — Task Correlation

Verify task events reference the correct task ID.

## OBS-TEST-003 — Trace Correlation

Verify model/tool events within one execution share trace context.

## OBS-TEST-004 — Tool Failure

Verify tool errors produce structured events.

## OBS-TEST-005 — Model Failure

Verify provider errors produce normalized observability records.

## OBS-TEST-006 — Redaction

Verify configured secrets never appear in standard logs.

## OBS-TEST-007 — Task Timeline

Verify a task timeline can be reconstructed from runtime events.

## OBS-TEST-008 — Completion Evidence

Verify completion events reference actual verification evidence where available.

## OBS-TEST-009 — Cancellation

Verify task cancellation appears correctly in the event stream.

## OBS-TEST-010 — Audit

Verify approval/security events are durably recorded when required.

## OBS-TEST-011 — Restart

Verify durable critical events remain inspectable after restart.

## OBS-TEST-012 — UI State

Verify the UI receives real task status rather than deriving it from display text.

---

# 108. Development Sequence

**OBS-0 — Event Contract**

Define common event structure and naming.

**OBS-1 — Event Bus**

Implement in-process event delivery.

**OBS-2 — Structured Logging**

Integrate logs with task/execution metadata.

**OBS-3 — Task Timeline**

Persist and query important task events.

**OBS-4 — Tool & Model Instrumentation**

Add standard events around runtime integrations.

**OBS-5 — Error Records**

Normalize diagnostics.

**OBS-6 — Trace Context**

Introduce trace/span correlation.

**OBS-7 — Redaction**

Apply secret and sensitive-data protection.

**OBS-8 — System Health**

Expose basic component status.

**OBS-9 — UI Integration**

Connect task timelines and system status to interfaces.

**OBS-10 — Evaluation**

Verify traces accurately reflect real executions.

Later phases may add distributed tracing, richer metrics, performance profiling, and exportable diagnostic bundles.

---

# 109. Research Requirements

Before finalizing the design, research observability patterns from:

## OpenHands

Study:

- Agent event streams.
- Runtime events.
- Task histories.
- Coding execution visibility.

## DeerFlow

Study:

- Long-running workflow visibility.
- Subagent events.
- Checkpoint observability.

## Gemini CLI

Study:

- Tool execution display.
- Error presentation.
- Usage tracking.

## Hive

Study:

- Worker tracking.
- Task ledger.
- Execution history.

## OpenClaw

Study:

- Gateway/system observability.
- Multi-channel execution state.
- Integration diagnostics.

Research should prioritize architectural patterns rather than copying project-specific telemetry stacks.

---

# 110. Architecture Decisions Required

The following decisions must be resolved:

```text
OBS-ADR-001
Event schema.

OBS-ADR-002
Event naming convention.

OBS-ADR-003
Event durability classes.

OBS-ADR-004
Logging library / format.

OBS-ADR-005
Trace representation.

OBS-ADR-006
Metrics strategy.

OBS-ADR-007
Redaction mechanism.

OBS-ADR-008
Audit-log persistence.

OBS-ADR-009
Retention policy.

OBS-ADR-010
Developer diagnostics API.

OBS-ADR-011
Future performance tracing.

OBS-ADR-012
Future external telemetry policy.
```

These are planning identifiers.

Approved decisions belong in:

`docs/22_DECISION_LOG.md`

---

# 111. Definition of Done

The first Observability milestone is complete when:

- Runtime components emit structured events.
- Tasks and executions have traceable timelines.
- Tool operations are observable.
- Model operations are observable.
- Errors use normalized records.
- Important permission decisions are observable.
- Relevant durations are measured.
- Secret values are redacted.
- The UI can show meaningful real task progress.
- Developer Mode can inspect task history.
- Task completion references real evidence.
- Durable important events survive restart where required.
- The integrated acceptance scenario succeeds.
- Relevant automated tests pass.

---

# 112. Final Observability Principle

Mikasa should never be a black box where the only explanation is:

```text
"The AI did something."
```

The system should be able to answer:

```text
WHAT HAPPENED?

WHEN?

WHICH TASK?

WHICH COMPONENT?

WHICH TOOL OR MODEL?

WHAT RESULT?

WHAT FAILED?

WHAT VERIFIED SUCCESS?
```

Observability must preserve these distinctions:

```text
LOG
   !=
STATE

EVENT
   !=
MEMORY

MODEL OUTPUT
   !=
EVIDENCE

ACTIVITY
   !=
SUCCESS

TRACEABILITY
   !=
EXPOSING HIDDEN REASONING
```

**Record the operational facts. Correlate the system. Protect sensitive data. Make failures diagnosable and success verifiable.**