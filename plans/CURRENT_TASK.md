# M I K A S A## Current Task

**Task ID:** TASK-P0-001

**Title:** Research Reference Systems and Resolve Foundational Architecture Decisions

**Type:** RESEARCH / ARCHITECTURE

**Phase:** Phase 0 — Research & Architecture

**Status:** READY

---

# Objective

Study the approved reference repositories and use evidence from their real implementations to validate Mikasa's proposed architecture.

The goal is NOT to copy a single project.

The goal is to identify useful patterns, trade-offs, proven implementation approaches, and mistakes to avoid.

Use the findings to prepare the foundational ADRs required before production development begins.

---

# Reference Repositories

Research these systems:

1. `Fosowl/agenticSeek`
2. `zai-org/ZCode`
3. `MemTensor/MemOS`
4. `TencentCloud/TencentDB-Agent-Memory`
5. `bytedance/deer-flow`
6. `openclaw/openclaw`
7. `rohitg00/agentmemory`
8. `soongenwong/claudecode`
9. `BitterSecurity/Decepticon`
10. `AstrBotDevs/AstrBot`
11. `GitSquared/edex-ui`
12. `Panniantong/Agent-Reach`
13. `google-gemini/gemini-cli`
14. `OpenHands/OpenHands`
15. `msitarzewski/agency-agents`
16. `aden-hive/hive`

For repositories with uncertain provenance or licensing, study architecture only unless reuse rights are verified.

---

# Research Areas

Focus on:

## Agent Runtime

- execution loops
- task lifecycle
- planning
- retries
- recovery
- long-running tasks

## Memory

- storage model
- memory categories
- retrieval
- project/user/shared memory
- consolidation
- persistence

## Tools

- tool registration
- schemas
- permissions
- MCP
- execution isolation
- result handling

## Models

- provider abstraction
- routing
- fallback
- local models
- OpenAI-compatible providers

## Coding

- workspace handling
- file editing
- terminal execution
- testing
- diff review
- git integration

## Research

- browsing
- source tracking
- citations
- evidence extraction

## Multi-Agent

- delegation
- specialist agents
- shared state
- coordination

## Security

- permissions
- sandboxing
- secrets
- tool isolation
- browser/computer-use boundaries

## Persistence

- databases
- checkpoints
- queues
- scheduling
- crash recovery

## Interfaces

- CLI
- web
- desktop
- voice

## Voice Research

Do not implement voice yet.

Research only the technology needed by `docs/15_VOICE_SYSTEM.md`, including:

- Pocket TTS
- Breeze TTS 2
- `Jinny-406/whisper-chan`
- local streaming STT
- VAD
- acoustic echo cancellation
- low-latency audio I/O
- streaming playback
- barge-in

---

# Required Outputs

Produce research findings that clearly separate:

- confirmed facts
- architectural observations
- useful patterns
- risks
- licensing concerns
- assumptions
- recommendations for ADR consideration

Do not treat recommendations as accepted decisions.

---

# Foundational ADRs To Prepare

At minimum investigate enough evidence to resolve:

- `FOUNDATION-ADR-001` — Primary implementation language
- `FOUNDATION-ADR-002` — Package/dependency management
- `FOUNDATION-ADR-003` — Initial repository structure
- `CONFIG-ADR-001` — Configuration architecture
- `TEST-ADR-001` — Primary testing framework
- `STATE-ADR-002` — Initial persistence technology
- `MODEL-ADR-001` — Model provider abstraction
- `MODEL-ADR-002` — Initial real provider
- `TOOLS-ADR-001` — Native tool contract
- `SECURITY-ADR-001` — Permission capability model
- `MEMORY-ADR-001` — Initial memory persistence strategy

Do not resolve later-phase ADRs unless required by these decisions.

---

# In Scope

- Repository research
- Architecture comparison
- Technology comparison
- Licensing/provenance inspection
- Bounded experiments
- ADR proposals
- Documentation corrections
- Identifying contradictions

---

# Out of Scope

Do NOT:

- Build Mikasa Runtime.
- Build UI.
- Build Voice Runtime.
- Build Memory Service.
- Build Model Router.
- Build Tool Executor.
- Build autonomous agents.
- Add arbitrary frameworks.
- Copy large external codebases.
- Start Phase 1 implementation.

---

# Verification

Before marking this task complete:

- Every researched claim should have a source.
- Important conclusions should cite evidence.
- Uncertain facts must be labeled uncertain.
- Major technologies should have alternatives considered.
- Relevant licenses should be checked.
- Proposed ADRs should list trade-offs.
- Architecture changes must be documented.
- No production code should have been added accidentally.

---

# Completion Report

When done, report:

1. Repositories researched.
2. Main findings from each.
3. Patterns worth adopting.
4. Patterns worth avoiding.
5. Licensing/provenance concerns.
6. Foundational ADR proposals.
7. Remaining unresolved questions.
8. Architecture documents that may need revision.
9. Whether Phase 0 is ready to continue or complete.

After reporting, STOP.

Do not automatically begin Phase 1.
