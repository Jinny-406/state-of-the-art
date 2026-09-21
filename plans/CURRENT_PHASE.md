# M I K A S A## Current Development Phase

**Phase:** Phase 0 — Research & Architecture

**Status:** ACTIVE

**Purpose:** Validate Mikasa's proposed architecture, study reference repositories, resolve foundational technical decisions, and prepare the project for implementation.

---

# Current Goal

Before production coding begins, Mikasa must:

- Research the selected open-source agent systems and supporting projects.
- Validate the proposed system architecture.
- Identify reusable architectural patterns.
- Identify licensing or provenance concerns.
- Resolve foundational ADRs required for Phase 1.
- Confirm the initial technology stack.
- Confirm the initial repository structure.
- Confirm testing and development tooling.
- Confirm persistence, model, tool, memory, and security foundations.

---

# Allowed Work

During Phase 0, agents may:

- Research reference repositories.
- Read source code.
- Compare architectures.
- Create research notes.
- Create bounded prototypes when necessary to answer a technical question.
- Propose ADRs.
- Update architecture documents when evidence justifies a change.
- Identify contradictions or missing requirements.

---

# Not Allowed Yet

Do NOT begin production implementation of:

- Agent Runtime
- Tool System
- Memory System
- Model Router
- Coding Engine
- Research Engine
- Voice Runtime
- UI
- Multi-Agent System
- Computer Use
- Self-Improvement

Prototype code is allowed only when explicitly required for research and must remain isolated from production implementation.

---

# Phase Exit Requirements

Phase 0 is complete only when:

- Required reference repositories have been researched.
- Important architecture assumptions have been validated.
- Foundational ADRs needed for Phase 1 are ACCEPTED.
- Major contradictions between architecture documents are resolved.
- The initial implementation language is selected.
- Package/dependency management is selected.
- Repository structure is selected.
- Configuration strategy is selected.
- Testing framework is selected.
- Initial persistence strategy is selected.
- Initial model-provider architecture is selected.
- Native tool contract is selected.
- Initial permission architecture is selected.
- Initial memory persistence strategy is selected.
- Phase 1 has a clear `CURRENT_TASK.md`.

---

# Primary Reference Documents

Agents working in this phase should read:

- `AGENTS.md`
- `OWNER_PROFILE.md`
- `docs/00_PROJECT_CHARTER.md`
- `docs/01_PRD.md`
- `docs/02_SCOPE_AND_NON_GOALS.md`
- `docs/03_SYSTEM_ARCHITECTURE.md`
- `docs/18_TESTING_AND_EVALS.md`
- `docs/19_DEVELOPMENT_PHASES.md`
- `docs/20_DEFINITION_OF_DONE.md`
- `docs/21_TASK_PROTOCOL.md`
- `docs/22_DECISION_LOG.md`
- `docs/23_ROADMAP.md`

Subsystem documents should be read when relevant.

---

# Phase Rule

Research first.

Decide second.

Implement later.

Do not turn Phase 0 into an unofficial implementation phase.
