# M I K A S A## Project Backlog

This backlog contains possible future work.

Backlog items are NOT authorized implementation tasks.

Only work explicitly moved into `plans/CURRENT_TASK.md` may be implemented.

---

# Status Values

```text
IDEA
RESEARCH_NEEDED
PROPOSED
APPROVED
DEFERRED
REJECTED
IMPLEMENTED
```

---

# Phase 0 — Research & Architecture

## APPROVED

- Research reference agent repositories.
- Validate current architecture.
- Resolve foundational ADRs.
- Research initial implementation language.
- Research package/dependency management.
- Research initial persistence technology.
- Research model provider abstraction.
- Research native tool contract.
- Research permission architecture.
- Research memory persistence strategy.
- Research testing framework.
- Research configuration architecture.

## RESEARCH_NEEDED

- Pocket TTS evaluation.
- Breeze TTS 2 evaluation.
- Whisper-Chan architecture evaluation.
- Local streaming STT options.
- Voice activity detection options.
- Acoustic echo cancellation options.
- Low-latency cross-platform audio I/O.
- Browser automation candidates.
- MCP client strategy.
- Local-model runtime options.

---

# Foundation

## PROPOSED

- Bootstrap repository structure.
- Configuration system.
- Test framework.
- Logging foundation.
- Core shared types.
- Application startup.
- Dependency boundaries.

---

# Agent Runtime

## PROPOSED

- Task Manager.
- Agent Runtime loop.
- Execution context.
- Planner interface.
- Cancellation.
- Retry handling.
- Step limits.
- Verification pipeline.

---

# Model System

## PROPOSED

- ModelProvider interface.
- Provider registry.
- One real provider.
- Streaming response support.
- Tool-call normalization.
- Usage tracking.
- Provider health.
- Future local-model adapters.

---

# Tools

## PROPOSED

- Tool Registry.
- Tool Executor.
- Filesystem tools.
- Search/read/write tools.
- Controlled terminal.
- Permission checks.
- Timeouts.
- Cancellation.
- MCP adapter.

---

# Security

## PROPOSED

- Permission Service.
- Workspace boundaries.
- Path validation.
- Secrets system.
- Controlled terminal policy.
- Approval workflow.
- Audit events.

---

# Persistence

## PROPOSED

- Durable task records.
- Execution records.
- Plan records.
- Action records.
- Cancellation state.
- Session state.
- Schema migrations.

---

# Memory

## PROPOSED

- Memory Service.
- Working memory.
- Episodic memory.
- Semantic memory.
- Procedural memory.
- Project memory.
- User memory.
- Stable memory IDs.
- CRUD.
- Basic retrieval.

## DEFERRED

- Semantic embeddings.
- Graph memory.
- Automated consolidation.
- Shared multi-agent memory.
- Automatic skill extraction.

---

# Autonomy

## PROPOSED

- Structured planning.
- Bounded multi-step execution.
- Progress evaluation.
- Replanning.
- Stuck detection.
- Completion verification.

---

# Coding Engine

## PROPOSED

- Project inspection.
- Code search.
- File editing.
- Terminal commands.
- Test execution.
- Git diff/status.
- Verification.
- Preserve user changes.

---

# Research Engine

## PROPOSED

- Search providers.
- Browser retrieval.
- Source records.
- Evidence extraction.
- Claim tracking.
- Citation generation.
- Contradiction handling.

---

# Skills

## DEFERRED

- Reusable workflows.
- Skill registry.
- Skill versioning.
- Skill evaluation.
- Skill promotion.

---

# Long-Running Tasks

## DEFERRED

- Checkpoints.
- Pause/resume.
- Crash recovery.
- Background workers.
- Queues.
- Durable task execution.

---

# Scheduling

## DEFERRED

- Scheduled jobs.
- Recurring tasks.
- Notifications.
- Condition monitoring.

---

# Multi-Agent

## DEFERRED

- Agent Manager.
- Research specialist.
- Coding specialist.
- Testing specialist.
- Delegation records.
- Parent/child tasks.
- Shared task context.

---

# UI

## DEFERRED

- Web/desktop interface.
- Main Mikasa screen.
- Tasks view.
- Memory view.
- Settings.
- Approval UI.
- Developer diagnostics.
- Realtime event stream.

---

# Voice

## DEFERRED

- Audio Front-End.
- Streaming STT.
- Turn Manager.
- Speech Director.
- Local TTS Router.
- Voice Identity.
- Streaming playback.
- Barge-in.
- Echo cancellation.
- Conversation mode.
- Wake word.

---

# Browser

## DEFERRED

- Advanced navigation.
- Authenticated sessions.
- Downloads.
- Visual verification.
- Local web-app testing.

---

# Computer Use

## DEFERRED

- Screen observation.
- Controlled mouse input.
- Controlled keyboard input.
- Desktop app interaction.
- Permission boundaries.
- Emergency stop.

---

# Self-Improvement

## DEFERRED

- Failure pattern detection.
- Improvement proposals.
- Skill experiments.
- Prompt experiments.
- Routing experiments.
- Evaluation comparisons.
- Promotion and rollback.

---

# Long-Term Ideas

## IDEA

- Multi-device Mikasa.
- Mobile companion.
- Distributed workers.
- 3D avatar.
- Rich desktop presence.
- Proactive assistance.
- Advanced local AI stack.
- Automatic capability discovery.
- More expressive voice behavior.
- Advanced multimodal interaction.

---

# Rule

Backlog:

```text
!= CURRENT TASK
```

An item enters implementation only when it is explicitly approved and moved into:

`plans/CURRENT_TASK.md`