# M I K A S A
## Memory Architecture Specification

**File:** `docs/05_MEMORY_ARCHITECTURE.md`

**Version:** 0.1.0

**Status:** PROPOSED — Pending repository research and architecture approval

**Authority:** Memory architecture, organization, storage, retrieval, lifecycle, and access control

**Applies to:** Main agent, specialist agents, memory services, context management, persistent knowledge, learned skills, and user information.

---

# 1. Purpose

This document defines the memory architecture for M I K A S A.

The purpose of the memory system is to allow Mikasa to retain, organize, retrieve, update, and appropriately use information across conversations, tasks, projects, and application restarts.

Memory must be a first-class subsystem.

It must not be implemented as an uncontrolled collection of conversation logs, embeddings, and miscellaneous files.

The memory system must support the gradual development of a persistent AI assistant without requiring the complete memory architecture to be implemented during the first development phase.

This document defines:

- Memory philosophy.
- Memory categories.
- Memory ownership.
- Memory scopes.
- Memory record structure.
- Storage and retrieval.
- Memory creation.
- Memory consolidation.
- Memory correction.
- Forgetting and deletion.
- Privacy and security.
- Cross-agent memory sharing.
- Knowledge relationships.
- Skill memory.
- Memory lifecycle.
- Persistence.
- Memory evaluation.
- Initial implementation requirements.

---

# 2. Core Memory Principle

Mikasa must distinguish between:

**Information that is available right now.**

**Information that should be remembered temporarily.**

**Information worth retaining for future use.**

**Information that should not be stored.**

These are separate decisions.

Not every conversation message deserves permanent storage.

Not every tool result deserves permanent storage.

Not every model-generated conclusion is a reliable memory.

The memory system must preserve useful information while minimizing duplication, outdated information, and unnecessary storage.

---

# 3. Memory Architecture Overview

The proposed memory architecture consists of several cooperating components.

```text
                  M I K A S A
                       │
                       ▼
                 MEMORY SERVICE
                       │
          ┌────────────┼────────────┐
          │            │            │
          ▼            ▼            ▼
       MEMORY       MEMORY       MEMORY
       WRITER      RETRIEVER     MANAGER
          │            │            │
          └────────────┼────────────┘
                       │
                       ▼
                 MEMORY STORAGE
                       │
         ┌─────────────┼─────────────┐
         │             │             │
         ▼             ▼             ▼
      RECORDS        INDEXES       RELATIONSHIPS
         │             │             │
         └─────────────┼─────────────┘
                       │
                       ▼
                 PERSISTENCE
```

These are logical responsibilities.

They do not require separate processes, databases, or services.

The initial implementation may use a small number of focused modules.

---

# 4. Memory Is Not Conversation History

The system must distinguish between conversation history, operational state, and agent memory.

## Conversation History

Contains messages exchanged during a conversation.

It may include:

- User messages.
- Assistant responses.
- Tool calls.
- Tool results.
- Conversation metadata.

Conversation history may be persisted to support session continuity.

However, storing conversation history does not automatically mean every message becomes a permanent memory.

## Operational State

Contains information required for running Mikasa.

Examples:

- Task status.
- Execution identifiers.
- Checkpoints.
- Pending approvals.
- Retry counts.
- Scheduled jobs.

Operational state belongs to the appropriate runtime or persistence service.

It must not depend on the memory system being able to retrieve an embedding.

## Agent Memory

Contains selected information that may improve future reasoning or task execution.

Examples:

- A project architecture decision.
- A useful coding technique.
- An explicitly saved user preference.
- A verified solution to a recurring problem.
- A previously completed task relevant to current work.

These distinctions must be maintained throughout the architecture.

---

# 5. Memory Classification Model

Mikasa must organize memory using multiple independent dimensions.

A memory record should not be categorized using only one label.

The proposed classification dimensions are:

```text
MEMORY TYPE

MEMORY SCOPE

MEMORY OWNER

MEMORY SOURCE

MEMORY LIFETIME

MEMORY IMPORTANCE

MEMORY CONFIDENCE

MEMORY ACCESS POLICY
```

For example:

A project architecture decision may be:

```text
Type: SEMANTIC

Scope: PROJECT

Owner: Project A

Source: Approved architecture document

Lifetime: Persistent

Access: Project-authorized agents
```

A user preference may be:

```text
Type: SEMANTIC

Scope: USER

Owner: User

Source: Explicit user instruction

Lifetime: Persistent until changed or deleted

Access: Authorized personal assistant context
```

A temporary tool result may be:

```text
Type: WORKING

Scope: TASK

Owner: Current execution

Source: Tool output

Lifetime: Current task

Access: Current execution context
```

This classification approach prevents memory types and access scopes from becoming confused.

---

# 6. Memory Categories

The intended memory system contains the following categories.

These are logical categories, not automatically separate physical databases.

---

## 6.1 Working Memory

**Purpose:** Maintain information required during the current conversation or task.

Working memory may include:

- Current user objective.
- Relevant conversation context.
- Active task information.
- Recent tool results.
- Temporary observations.
- Current plan.
- Unresolved problems.
- Pending actions.

Working memory should be optimized for immediate use.

### Lifecycle

Working memory may be updated continuously during active execution.

It should normally expire when the relevant execution or session ends.

Selected information may be promoted into persistent memory when appropriate.

### Important rule

Working memory is not automatically permanent.

Temporary context should not be retained indefinitely merely because it was available to the model.

---

## 6.2 Episodic Memory

**Purpose:** Remember relevant previous events and experiences.

Episodic memory describes what happened.

Examples:

- Mikasa previously completed a particular development task.
- A project encountered a recurring dependency problem.
- A research task produced an important finding.
- A previous workflow failed for a particular reason.
- The user explicitly asked Mikasa to retain a significant event.

### Suggested metadata

```text
event_type

timestamp

task_reference

participants

outcome

source

relevance
```

Episodic memory should preserve meaningful experiences without permanently retaining every execution detail.

Detailed operational records belong in the appropriate task or event store.

---

## 6.3 Semantic Memory

**Purpose:** Store selected facts, concepts, and knowledge.

Examples:

- A project's approved technology stack.
- A software library's documented purpose.
- An explicitly stated user preference.
- A verified relationship between project components.
- A useful technical explanation.

Semantic memory should distinguish:

```text
VERIFIED FACT

USER-PROVIDED INFORMATION

SUPPORTED INFERENCE

UNVERIFIED CLAIM

OUTDATED INFORMATION
```

An inference must not automatically become a verified fact.

Semantic memories should retain enough provenance to evaluate their reliability.

---

## 6.4 Procedural Memory

**Purpose:** Remember how to perform useful tasks.

Procedural memory may contain reusable workflows.

Examples:

- How to run tests for a specific project.
- How to prepare a development environment.
- How to perform a recurring maintenance task.
- How to structure a common research workflow.

A procedural memory should ideally include:

```text
name

purpose

applicable_conditions

required_capabilities

procedure

expected_result

verification

known_limitations
```

Procedural memory must not automatically execute itself.

Execution remains subject to the active task, available tools, and permissions.

---

## 6.5 Skill Memory

**Purpose:** Store reusable, structured capabilities that have been explicitly created or validated.

A skill may contain:

- Skill name.
- Description.
- Trigger conditions.
- Required tools.
- Input parameters.
- Execution steps.
- Expected outputs.
- Verification requirements.
- Version.
- Evaluation history.

Skill memory overlaps conceptually with procedural memory.

The distinction is that procedural memory represents knowledge of how to perform a task, while a skill is a structured capability that may be selected and executed by the agent runtime.

A procedural memory does not automatically become an executable skill.

Skill execution must use the approved tool and permission systems.

---

# 7. Memory Scopes

Memory type describes what information represents.

Memory scope describes where that information belongs and who may access it.

The following scopes are proposed.

---

## 7.1 Task Scope

Information belonging to one specific task.

Examples:

- Temporary observations.
- Task-specific instructions.
- Intermediate results.
- Current execution context.

Task-scoped memory should not automatically be shared with unrelated tasks.

---

## 7.2 Session Scope

Information associated with one conversation session.

Examples:

- Recent conversational context.
- Temporary preferences stated for the current conversation.
- Session-specific decisions.

Session-scoped information must not automatically be treated as a permanent user preference.

---

## 7.3 Project Scope

Information belonging to a particular project.

Examples:

- Project architecture.
- Technology stack.
- Repository structure.
- Development decisions.
- Known bugs.
- Important dependencies.
- Project-specific procedures.

Each project should have a stable identifier.

Project A must not automatically receive memories belonging to Project B.

The system may retrieve cross-project information when explicitly authorized and relevant.

---

## 7.4 User Scope

Information intentionally retained for a particular user.

Examples:

- Explicitly saved preferences.
- Preferred workflows.
- User-approved recurring instructions.
- Information the user has requested Mikasa to remember.

User memory must remain distinguishable from temporary conversational context.

Sensitive information requires appropriate storage controls and explicit authorization where applicable.

---

## 7.5 Agent Scope

Information belonging to an individual specialist agent or execution.

Examples:

- Temporary specialist observations.
- Agent-specific task context.
- Intermediate analysis artifacts.
- Local working notes.

Agent-scoped information must not automatically become shared memory.

---

## 7.6 Shared Scope

Information explicitly available to multiple authorized agents or components.

Examples:

- An approved project decision.
- A verified research result.
- A relevant task artifact.
- A reusable workflow.

Shared memory must use defined access policies.

A specialist's need to collaborate does not automatically grant access to every user memory or unrelated project.

---

## 7.7 System Scope

Information about Mikasa's own configuration and operation.

Examples:

- Approved architecture decisions.
- Runtime configuration references.
- Tool capability definitions.
- Internal component documentation.
- System maintenance procedures.

System configuration is not ordinary agent-generated memory.

Authoritative system rules and security policies must remain under trusted configuration management.

A model-generated memory must never be able to overwrite them.

---

# 8. Memory Ownership

Every persistent memory record must have an identifiable owner or owning scope.

A proposed ownership model:

```text
MemoryOwnership:
    owner_type
    owner_id
    namespace
```

Possible owner types:

```text
USER

PROJECT

TASK

SESSION

AGENT

SYSTEM
```

Ownership must be enforced through trusted application code.

A model cannot claim ownership of another user's information by generating a different owner identifier.

Ownership determines which access rules apply.

It does not automatically grant every operation to the owner.

---

# 9. Memory Record Schema

Persistent memories must use structured records.

Proposed conceptual schema:

```text
MemoryRecord:
    memory_id
    memory_type
    scope

    owner_type
    owner_id
    namespace

    title
    content
    summary

    source_type
    source_reference

    confidence
    importance

    created_at
    updated_at
    expires_at

    version
    status

    access_policy

    metadata
```

This is a conceptual schema.

Actual field names, database types, indexes, and validation rules must be finalized during implementation.

Not every record requires every optional field.

---

# 10. Memory Identity

Every persistent memory must have a stable unique identifier.

Example:

```text
memory_id: mem_001
```

The identifier must remain stable across ordinary updates.

When a memory is corrected, the system should preserve its identity or maintain an explicit relationship between the original and replacement records.

Identifiers must not rely on memory titles being unique.

---

# 11. Memory Provenance

Persistent memories should retain information about their origin.

Possible sources include:

```text
EXPLICIT_USER_INSTRUCTION

USER_MESSAGE

VERIFIED_TOOL_RESULT

APPROVED_PROJECT_DOCUMENT

RESEARCH_SOURCE

COMPLETED_TASK

AGENT_INFERENCE

GENERATED_SKILL
```

A memory derived from external information should preserve an appropriate source reference.

Examples:

- Document identifier.
- File path and version.
- Task identifier.
- Source URL.
- Retrieval timestamp.
- Related memory identifier.

The system should distinguish between information directly supplied by a user and information inferred by a model.

Model-generated confidence must not be treated as proof that a claim is true.

---

# 12. Memory Storage Architecture

The architecture must separate logical memory operations from physical storage.

Conceptually:

```text
                  MEMORY SERVICE
                         │
          ┌──────────────┼──────────────┐
          │              │              │
          ▼              ▼              ▼
      RECORD STORE   SEARCH INDEX   RELATIONSHIP INDEX
          │              │              │
          └──────────────┼──────────────┘
                         │
                         ▼
                  STORAGE ADAPTERS
```

The initial implementation may use one storage engine.

Different logical responsibilities do not require separate databases.

The system must avoid introducing unnecessary infrastructure before retrieval requirements justify it.

---

# 13. Storage Components

## 13.1 Canonical Record Store

The canonical record store contains authoritative persistent memory records.

It must support:

- Creating records.
- Reading records.
- Updating records.
- Deleting records.
- Filtering records.
- Maintaining stable identifiers.
- Persisting relevant metadata.

The canonical record store is the authoritative source for memory content and access policies.

Search indexes are derived from this store.

---

## 13.2 Search Index

A search index allows efficient retrieval.

Possible strategies include:

- Exact matching.
- Metadata filtering.
- Full-text search.
- Semantic vector search.
- Hybrid retrieval.

The initial implementation should prioritize a simple, functional retrieval strategy.

Vector search should only be introduced when evaluation demonstrates a meaningful need.

---

## 13.3 Relationship Index

A future relationship index may represent connections between memories.

Examples:

```text
PROJECT
    └── HAS_DECISION
            └── ARCHITECTURE DECISION
```

```text
TASK
    └── PRODUCED
            └── REUSABLE SKILL
```

```text
MEMORY A
    └── SUPERSEDED_BY
            └── MEMORY B
```

A separate graph database is not required merely because memories may have relationships.

Relationships may initially be represented through ordinary structured records.

---

# 14. Memory Provider Abstraction

The Memory Service must not depend permanently on one database or memory framework.

Proposed interface:

```text
MemoryStore:
    create(record)

    get(memory_id)

    update(memory_id, changes)

    delete(memory_id)

    query(filters)
```

A search interface may be defined separately:

```text
MemoryRetriever:
    search(query, access_context)
```

These signatures are illustrative.

Actual implementation contracts must define:

- Input validation.
- Return types.
- Error handling.
- Pagination.
- Access enforcement.
- Consistency requirements.
- Transaction boundaries.

The Agent Runtime must interact with the Memory Service rather than directly with these storage adapters.

---

# 15. Memory Creation Pipeline

New persistent memories must pass through a controlled creation pipeline.

```text
INFORMATION RECEIVED
         │
         ▼
MEMORY CANDIDATE
         │
         ▼
CLASSIFY INFORMATION
         │
         ▼
CHECK STORAGE ELIGIBILITY
         │
         ▼
CHECK OWNERSHIP AND PERMISSIONS
         │
         ▼
CHECK EXISTING MEMORIES
         │
         ▼
VALIDATE SOURCE AND CONTENT
         │
         ▼
STORE MEMORY
         │
         ▼
UPDATE SEARCH INDEX
         │
         ▼
RETURN MEMORY REFERENCE
```

The memory writer must not simply store every generated sentence.

---

# 16. Memory Candidate Selection

Information may become a memory candidate when it has potential future value.

Examples:

- The user explicitly requests that information be remembered.
- A significant project decision is approved.
- A recurring problem is solved and verified.
- A useful workflow is validated.
- An important correction is provided.
- A completed task produces reusable knowledge.

Information should not automatically become permanent memory when it is:

- A temporary tool response.
- Unrelated to future tasks.
- Duplicated.
- Unsupported speculation.
- Sensitive information without an appropriate storage purpose or authorization.
- A temporary conversational instruction.
- An incomplete intermediate result.

Memory selection rules must be defined in the Memory Manager rather than independently implemented by every agent.

---

# 17. Explicit Memory Requests

When a user explicitly requests that Mikasa remember information, the system should process that request through the Memory Service.

Examples:

"Remember that this project uses Python."

"Save this workflow for future use."

"Remember my preference for concise progress reports."

The system must:

1. Identify the information being requested for storage.
2. Determine its appropriate memory type and scope.
3. Check applicable permissions and storage rules.
4. Store the information.
5. Return an accurate storage result.

The assistant must not claim that memory was saved before the storage operation succeeds.

If storage fails, the user must receive an appropriate explanation.

---

# 18. Automatic Memory Creation

Future versions may automatically identify information worth remembering.

However, automatic memory creation must follow an explicit policy.

The policy should consider:

- Relevance.
- Expected future usefulness.
- Reliability.
- Duplication.
- Privacy.
- Retention requirements.
- User preferences.
- Storage permissions.

Automatic memory creation must not promote temporary instructions into permanent user preferences merely because they appeared in a conversation.

The initial MVP does not require sophisticated automatic memory extraction.

---

# 19. Memory Retrieval

Memory retrieval must provide relevant information without overwhelming the model with unrelated data.

The proposed retrieval pipeline:

```text
CURRENT TASK
     │
     ▼
RETRIEVAL REQUEST
     │
     ▼
RESOLVE ACCESS CONTEXT
     │
     ▼
FILTER AUTHORIZED SCOPES
     │
     ▼
SEARCH CANDIDATE MEMORIES
     │
     ▼
REMOVE INVALID / EXPIRED RECORDS
     │
     ▼
RANK RELEVANT RESULTS
     │
     ▼
DETECT CONFLICTS
     │
     ▼
APPLY CONTEXT BUDGET
     │
     ▼
RETURN MEMORY RESULTS
```

Authorization filters must be enforced by trusted application code.

Post-processing alone must not be relied upon to remove unauthorized memories after they have already been exposed.

---

# 20. Retrieval Strategies

The system may eventually support multiple retrieval strategies.

## Exact Retrieval

Retrieve a memory through its stable identifier.

Suitable for:

- Known project decisions.
- Direct memory references.
- Specific stored preferences.

## Metadata Retrieval

Filter records using metadata.

Examples:

```text
scope = PROJECT

owner_id = project_001

type = SEMANTIC
```

## Keyword Retrieval

Search for relevant words or phrases.

Suitable for straightforward technical and project knowledge.

## Semantic Retrieval

Use embeddings or other semantic representations to retrieve conceptually related information.

Semantic retrieval is a future capability unless selected for an approved implementation task.

## Hybrid Retrieval

Combine lexical, semantic, and metadata-based retrieval.

The exact ranking strategy must be selected through testing.

No retrieval strategy should bypass access controls.

---

# 21. Memory Relevance

The retriever should prioritize information useful for the current objective.

Possible relevance factors:

- Semantic similarity.
- Keyword relevance.
- Project relationship.
- User intent.
- Source reliability.
- Memory recency.
- Memory importance.
- Task-specific applicability.

The system must not assume that the newest memory is always the most useful.

It must also not assume that the most frequently retrieved memory is necessarily correct.

Retrieval scores are ranking signals, not proof of truth.

---

# 22. Context Budget

Mikasa must avoid inserting unlimited memory into model context.

The Context Manager should apply a configurable memory budget.

The budget may consider:

- Current task complexity.
- Model context capacity.
- Available token budget.
- Number of relevant results.
- Required source details.

When too many memories are relevant, the system may select or summarize information.

However, summarization must preserve important distinctions, uncertainties, and source references.

The canonical memory records must not be replaced merely because a shorter context summary was generated.

---

# 23. Memory Result Contract

Memory retrieval should return structured results.

Conceptual structure:

```text
MemoryResult:
    memory_id
    content
    memory_type
    scope
    source
    relevance
    confidence
    updated_at
```

The agent must be able to distinguish retrieved memory content from authoritative system instructions.

A memory record may contain useful information.

It must not automatically become a higher-priority instruction merely because the Memory Service retrieved it.

---

# 24. Memory Updating

Persistent memories must support updates.

An update may be required when:

- The user changes a preference.
- A project decision is revised.
- A previously stored fact becomes outdated.
- New evidence corrects an earlier conclusion.
- A workflow is improved.
- A memory's ownership or relevance changes.

The update process must:

1. Identify the existing record.
2. Verify modification permissions.
3. Validate the proposed change.
4. Preserve appropriate provenance.
5. Update the canonical record.
6. Maintain version history where required.
7. Update relevant indexes.
8. Return the resulting memory state.

A memory update must not silently modify unrelated records.

---

# 25. Memory Correction

Mikasa must support correcting incorrect or outdated memories.

Example:

An existing memory states:

```text
Project language: JavaScript
```

The user later explicitly corrects it:

```text
The project now uses TypeScript.
```

The system should identify the relevant record and update or supersede it.

It should not retain both statements as equally current facts without recognizing the conflict.

The correction process should preserve enough information to determine why the active memory changed.

However, correction history must not override explicit deletion requests or applicable retention policies.

---

# 26. Conflicting Memories

Two memories may disagree.

Examples:

- Different project architecture decisions.
- Outdated and current configurations.
- Contradictory research results.
- Inconsistent user preferences.
- Conflicting agent observations.

The system must not automatically select a memory solely because it has a higher semantic search score.

Conflict handling should consider:

- Source authority.
- Timestamps.
- Project or user scope.
- Whether one record supersedes another.
- Whether the information is independently verified.
- Whether clarification is required.

When uncertainty remains, the memory system should preserve the uncertainty rather than manufacturing a definitive answer.

---

# 27. Deduplication

The memory system should avoid storing unnecessary duplicates.

Potential duplicate cases:

- The same explicit preference saved twice.
- Repeated storage of an identical project fact.
- Multiple summaries of the same completed task.
- Equivalent knowledge extracted from overlapping sources.

Deduplication may use:

- Exact content matching.
- Source references.
- Stable identifiers.
- Metadata.
- Future semantic similarity.

Similar wording does not always mean identical meaning.

Deduplication must not merge records across users, projects, or permission scopes without authorization.

---

# 28. Memory Consolidation

Future versions may consolidate related memories into more useful representations.

Example:

Several verified observations about a recurring project issue may be summarized into one reusable troubleshooting memory.

Consolidation may:

- Combine redundant records.
- Create higher-level summaries.
- Extract reusable procedures.
- Link related memories.
- Identify outdated information.

Consolidation must preserve provenance.

It must not convert speculation into verified facts.

It must respect access scopes and deletion requirements.

Automatic consolidation is not required for the initial MVP.

---

# 29. Memory Lifecycle

Every memory should have a defined lifecycle.

Possible states:

```text
CANDIDATE

ACTIVE

SUPERSEDED

EXPIRED

DELETED
```

These states have different meanings.

**Candidate:** Information being evaluated for storage.

**Active:** A memory available for authorized retrieval.

**Superseded:** Information replaced by a more current record.

**Expired:** Information that has reached its configured retention limit.

**Deleted:** Information removed through an authorized deletion process.

Candidate records do not necessarily need to be persisted.

Deleted records must not be returned by ordinary retrieval.

The implementation must define how lifecycle state is enforced across indexes and storage systems.

---

# 30. Memory Retention

Not all memories should have the same lifetime.

Suggested retention categories:

```text
EXECUTION_LIFETIME

SESSION_LIFETIME

PROJECT_LIFETIME

USER_CONTROLLED

TIME_LIMITED

SYSTEM_MANAGED
```

Retention should depend on the type and purpose of the information.

Examples:

Working memory may expire after execution.

Project memory may remain while the project is active.

Explicitly saved user preferences may remain until changed or deleted, subject to user controls and applicable policies.

Temporary research information may use a shorter retention period.

Exact default retention periods must be defined during implementation.

Do not invent universal expiration periods without evaluating the product requirements.

---

# 31. Forgetting and Deletion

Mikasa must support authorized memory deletion.

Deletion may target:

- One memory record.
- A selected category.
- A project namespace.
- An agent-specific namespace.
- Selected user memories.
- An entire authorized memory collection.

A deletion request must identify the intended scope.

The Memory Service must enforce permissions before deletion.

## Deletion behavior

The system must:

1. Identify affected records.
2. Validate authorization.
3. Apply the approved deletion procedure.
4. Remove the affected information from active retrieval.
5. Update or remove derived search indexes.
6. Handle related cached representations.
7. Report the actual deletion result.

If relevant information also exists independently in conversation history, source documents, backups, or another authorized store, memory deletion must not falsely claim those separate copies were deleted.

Retention and backup limitations must be documented accurately.

---

# 32. Memory Index Consistency

The canonical memory store is authoritative.

Derived search indexes must remain consistent with it.

When a memory is updated or deleted, the system must update the corresponding index entries.

The retrieval layer must not return deleted or unauthorized information from a stale index.

If an index becomes unavailable or inconsistent, the system should report the limitation or use an approved fallback.

It must not silently return unreliable results while claiming retrieval succeeded normally.

---

# 33. Memory Versioning

Important memories may require version history.

Examples:

- Project architecture decisions.
- Reusable skills.
- Approved workflows.
- Important system knowledge.

Versioning may support:

- Reviewing changes.
- Understanding why a record changed.
- Identifying superseded information.
- Restoring an appropriate earlier version.

Version history must not become a mechanism for bypassing an explicit deletion request.

Detailed versioning and rollback behavior is a later implementation decision.

---

# 34. Memory Relationships

Mikasa should eventually support explicit relationships between memories.

Possible relationship types:

```text
RELATED_TO

PART_OF

DERIVED_FROM

SUPERSEDES

CONTRADICTS

PRODUCED_BY

APPLIES_TO

DEPENDS_ON
```

Examples:

```text
Project A
    │
    └── HAS_DECISION
            │
            ▼
      Database Selection
```

```text
Troubleshooting Procedure
    │
    └── DERIVED_FROM
            │
            ▼
      Completed Debugging Task
```

Relationships must not automatically expand the access rights of related memories.

A link between two records does not authorize unrestricted access to both.

A dedicated graph database is not required for the initial implementation.

---

# 35. Project Memory

Project memory is particularly important for software development and other long-running work.

Each project should have a distinct namespace.

Suggested project memory organization:

```text
PROJECT MEMORY
│
├── OVERVIEW
│
├── REQUIREMENTS
│
├── ARCHITECTURE
│
├── TECHNOLOGY
│
├── DECISIONS
│
├── REPOSITORY KNOWLEDGE
│
├── DEVELOPMENT HISTORY
│
├── KNOWN ISSUES
│
├── VERIFIED SOLUTIONS
│
└── REUSABLE WORKFLOWS
```

These are logical groupings.

They do not require separate tables or directories.

## Critical rule

Authoritative project files remain authoritative.

If project memory conflicts with an approved project specification, Mikasa must consult the actual specification rather than treating its stored recollection as more reliable.

Memory should help locate and understand project information.

It should not silently replace the project's source of truth.

---

# 36. User Memory

User memory stores selected information relevant to future assistance.

Potential categories:

```text
PREFERENCES

WORKFLOWS

EXPLICIT INSTRUCTIONS

PROJECT ASSOCIATIONS

USER-APPROVED CONTEXT
```

The system must distinguish explicit user instructions from agent-inferred preferences.

For example:

A user requesting a short answer once does not automatically establish a permanent preference for short answers.

User memory must not be shared with specialist agents unless its use is authorized and relevant to their assigned work.

Sensitive information must use appropriate consent, storage, and access controls.

---

# 37. Agent Memory

Specialist agents may eventually maintain scoped memory.

Examples:

A coding specialist may retain selected project-specific implementation knowledge.

A research specialist may retain approved research artifacts and source references.

A testing specialist may retain verified evaluation procedures.

Agent memory must not automatically become permanent personal memory.

Specialist memory should be associated with an agent identity, task, project, or approved shared namespace.

The lifetime of temporary specialist memory should be bounded.

---

# 38. Shared Memory

Multiple agents may need access to common information.

Shared memory should support cooperation without exposing unrelated data.

Conceptually:

```text
                    MAIN AGENT
                         │
                         ▼
                SHARED MEMORY SERVICE
                         │
          ┌──────────────┼──────────────┐
          │              │              │
          ▼              ▼              ▼
      CODING AGENT   RESEARCH AGENT   TESTING AGENT
```

Every shared memory must retain its access policy.

The system must support limiting access according to:

- User authorization.
- Project membership.
- Task assignment.
- Agent identity.
- Required capability.

Specialists should receive only the information needed for their assigned work.

A shared namespace must not function as an unrestricted dump of all personal and system information.

---

# 39. Cross-Agent Memory Communication

Specialist agents should normally communicate task results through structured task contracts.

Shared memory may support longer-term collaboration.

However, it must not replace explicit task communication.

Example:

A research specialist completes a task.

Its verified findings are returned to the main agent.

Selected approved findings may be stored in project memory.

A coding specialist may later retrieve those findings if they are relevant and authorized.

The coding specialist should not need direct access to the research specialist's entire working context.

---

# 40. Skill Memory

Skill memory should eventually support learning and reusing effective workflows.

A proposed skill record:

```text
SkillRecord:
    skill_id
    name
    description

    trigger_conditions
    required_capabilities

    input_schema
    procedure
    expected_output

    verification
    failure_handling

    provenance
    version
    evaluation_history

    permissions
```

Skills should be evaluated before being promoted into reusable capabilities.

A successful task does not automatically prove that every step should become a permanent skill.

Skill execution must respect the current task's permissions.

Stored skills must not contain unrestricted credentials or automatically grant capabilities.

---

# 41. Learning From Completed Tasks

Future versions may extract reusable information from completed work.

Proposed pipeline:

```text
TASK COMPLETED
       │
       ▼
REVIEW VERIFIED OUTCOME
       │
       ▼
IDENTIFY REUSABLE INFORMATION
       │
       ▼
GENERATE MEMORY CANDIDATE
       │
       ▼
VALIDATE RELEVANCE AND SOURCE
       │
       ▼
CHECK EXISTING MEMORIES
       │
       ▼
APPLY STORAGE POLICY
       │
       ▼
STORE APPROVED MEMORY
```

Potential outputs:

- Episodic memory.
- Technical knowledge.
- Project decision.
- Reusable procedure.
- Skill candidate.

Failed tasks may also produce useful learning.

However, an unverified solution must not be stored as a proven procedure.

Advanced automatic learning belongs to a later implementation phase.

---

# 42. Memory Importance

Memory importance may help determine retention, consolidation, and retrieval priority.

Possible importance factors:

- Explicit user request.
- Relevance to active projects.
- Repeated usefulness.
- Significance of a verified decision.
- Relationship to ongoing work.
- Expected future applicability.

Importance is not the same as truth.

A highly important memory may still be incorrect or outdated.

The importance mechanism must not override access permissions or explicit deletion requests.

---

# 43. Memory Confidence

Persistent memories may include confidence or verification metadata.

The system should distinguish between:

```text
USER_STATED

SOURCE_SUPPORTED

INFERRED

UNVERIFIED

DISPUTED

OUTDATED
```

These are possible evidence classifications, not a claim that confidence can always be expressed accurately as a numerical score.

If numerical confidence is introduced, its meaning and calibration must be documented.

A model-generated confidence score must not automatically be treated as verified reliability.

---

# 44. Memory Privacy

Memory must be stored and retrieved according to explicit privacy boundaries.

The memory system must not automatically:

- Store passwords or API keys as ordinary memories.
- Share private user information across unrelated projects.
- Expose all personal memories to every specialist.
- Include private information in unrelated tool calls.
- Permanently retain unnecessary sensitive data.
- Reveal unauthorized records through search results or logs.

Credentials must be handled through the approved secrets-management system.

Memory may store a reference to an authorized credential when necessary.

It must not expose the credential itself as ordinary model context.

---

# 45. Memory Security

Memory operations must enforce authorization through trusted application code.

The system must validate:

```text
WHO IS REQUESTING ACCESS?

WHICH RECORDS ARE REQUESTED?

WHAT OPERATION IS REQUESTED?

WHAT SCOPE IS AUTHORIZED?

WHAT PERMISSIONS APPLY?
```

A model must not be able to grant itself memory access through generated text.

The access context must be derived from trusted application state.

Memory permissions must be checked before returning protected content.

---

# 46. Memory and Prompt Injection

Stored memories may contain information originating from untrusted sources.

Examples:

- Web pages.
- Repository files.
- External documents.
- Tool outputs.
- Third-party integrations.

Such information must not be treated as authoritative instructions.

The system must distinguish between:

**Trusted instructions governing Mikasa's behavior.**

**Retrieved information used to perform the current task.**

A retrieved document that instructs Mikasa to disable permissions, reveal credentials, or alter system rules must not gain authority merely because it was stored in memory.

Memory content must not override:

- System instructions.
- Runtime permissions.
- User authorization.
- Project security boundaries.
- Approved application configuration.

---

# 47. Memory and Context Management

The Memory Service and Context Manager have separate responsibilities.

**Memory Service**

Stores, manages, and retrieves memory records.

**Context Manager**

Selects and assembles information for a particular model request.

Conceptually:

```text
CURRENT TASK
     │
     ▼
CONTEXT MANAGER
     │
     ▼
MEMORY RETRIEVAL REQUEST
     │
     ▼
MEMORY SERVICE
     │
     ▼
AUTHORIZED MEMORY RESULTS
     │
     ▼
CONTEXT MANAGER
     │
     ▼
MODEL REQUEST
```

The Context Manager must not independently bypass memory permissions.

The Memory Service must not decide the entire model prompt.

Both components must remain replaceable and testable.

---

# 48. Memory Observability

Memory operations should produce appropriate operational records.

Potential events:

```text
memory.created

memory.retrieved

memory.updated

memory.superseded

memory.expired

memory.deleted

memory.access_denied

memory.index_updated

memory.operation_failed
```

Operational logs should include useful identifiers and statuses.

They must not unnecessarily duplicate private memory content.

The system should make it possible to investigate why a memory was selected, modified, or rejected without exposing unrelated sensitive information.

---

# 49. Memory Management Interface

The system should eventually provide user-facing memory controls.

Potential capabilities:

- View saved memories.
- Search saved memories.
- Inspect memory categories.
- Review memory sources.
- Correct inaccurate memories.
- Delete selected memories.
- Control project memory.
- Manage shared-memory permissions.
- Review memory-related errors.

A graphical memory dashboard is not required for the MVP.

The initial implementation may expose basic memory operations through a CLI or application interface.

---

# 50. Memory Backup and Recovery

Persistent memory should support an appropriate backup and recovery strategy.

The strategy must consider:

- Canonical record integrity.
- Database migrations.
- Index rebuilding.
- Corruption detection.
- Recovery after interrupted writes.
- Protection of private information.
- Deleted-record handling.

Derived search indexes should be rebuildable from authorized canonical records when technically practical.

Backups must not silently reintroduce records that were intentionally deleted.

The final backup and deletion policy must be documented before implementing production persistence.

---

# 51. Memory Research Requirements

Before finalizing memory implementation, research the following projects in detail.

## MemOS

Repository:

https://github.com/MemTensor/MemOS

Research questions:

- How is memory represented?
- How are memories organized and indexed?
- What retrieval strategies are supported?
- How are memories created and updated?
- How is memory relevance determined?
- How does the system handle consolidation?
- What mechanisms support persistent memory?
- How does its architecture support multiple agents?
- Which components could be integrated independently?
- What are the maintenance and deployment costs?

Do not assume that the entire framework must be adopted.

---

## TencentDB Agent Memory

Repository:

https://github.com/TencentCloud/TencentDB-Agent-Memory

Research questions:

- How are different categories of agent information organized?
- How are chat memory, knowledge, skills, and code-related information represented?
- How are records indexed and retrieved?
- What storage dependencies are required?
- How are updates and deletions handled?
- Which categorization concepts could be adapted for Mikasa?

Determine whether its architectural ideas justify a dependency or can be implemented more simply.

---

## AgentMemory

Repository:

https://github.com/rohitg00/agentmemory

Research questions:

- What memory interfaces are exposed?
- How is persistent knowledge managed?
- How are memory relationships represented?
- What retrieval and provenance mechanisms exist?
- How is shared memory handled?
- How are corrections and deletions performed?
- What infrastructure is required?

Compare its architecture with the other memory systems.

---

# 52. Memory Framework Selection

The research phase must not assume Mikasa needs to combine multiple complete memory frameworks.

Possible implementation approaches include:

**Approach A — Custom memory service**

Implement a focused memory service using an approved storage backend.

**Approach B — Adopt a memory framework**

Use one existing framework behind Mikasa's memory interface.

**Approach C — Hybrid implementation**

Use selected compatible components from one or more frameworks while retaining Mikasa's own memory contracts.

The final decision must consider:

- Functional requirements.
- Reliability.
- Integration complexity.
- Performance.
- Resource usage.
- Licensing.
- Maintenance.
- Privacy.
- Extensibility.
- Long-term ownership.

The chosen approach must be documented through an Architecture Decision Record.

---

# 53. MVP Memory Scope

The initial memory implementation must remain focused.

Required capabilities:

| Capability | MVP |
|---|---|
| Active working context | Required |
| Persistent memory records | Required |
| Basic memory classification | Required |
| Stable memory identifiers | Required |
| Memory creation | Required |
| Memory retrieval | Required |
| Memory updating | Required |
| Memory deletion | Required |
| Basic ownership and access checks | Required |
| Source metadata | Required |
| Memory persistence after restart | Required |
| Advanced semantic retrieval | Deferred |
| Knowledge graph | Deferred |
| Automatic memory consolidation | Deferred |
| Multi-agent shared memory | Deferred |
| Automatic skill generation | Deferred |
| Advanced memory ranking | Deferred |
| Distributed synchronization | Deferred |

The MVP must provide real persistence and retrieval.

A temporary in-memory dictionary does not satisfy persistent memory requirements.

The MVP may use simple classification and metadata-based retrieval.

It does not require multiple databases or complex retrieval infrastructure.

---

# 54. MVP Memory Acceptance Scenario

The following scenario must work in the first implementation.

**Step 1 — Store**

The user instructs Mikasa:

"Remember that Project Alpha uses Python."

Mikasa stores the information under the appropriate authorized scope.

**Step 2 — Restart**

The application is stopped and started again.

**Step 3 — Retrieve**

The user asks:

"What programming language does Project Alpha use?"

Mikasa retrieves the relevant record and answers correctly.

**Step 4 — Correct**

The user says:

"Project Alpha has switched to TypeScript. Update that memory."

Mikasa updates or supersedes the existing record.

**Step 5 — Verify correction**

The user asks the same question.

Mikasa returns the current information rather than presenting the old and new language as equally current.

**Step 6 — Delete**

The user requests deletion of the project-language memory.

Mikasa performs the authorized deletion.

**Step 7 — Verify deletion**

The deleted memory is no longer returned through ordinary memory retrieval.

Mikasa does not claim to have deleted independent source documents or conversation records unless those were also explicitly included in the request and the corresponding operations succeeded.

This scenario must use actual persistent storage.

---

# 55. Memory Testing Requirements

The memory subsystem must include tests covering its implemented functionality.

## TEST-MEM-001 — Create

Verify that a valid memory record can be stored.

## TEST-MEM-002 — Retrieve

Verify that a stored record can be retrieved.

## TEST-MEM-003 — Persistence

Verify that stored information survives an application restart.

## TEST-MEM-004 — Update

Verify that authorized updates modify the intended record.

## TEST-MEM-005 — Delete

Verify that deleted records are not returned by ordinary retrieval.

## TEST-MEM-006 — Scope isolation

Verify that records belonging to one project or user are not exposed to unauthorized contexts.

## TEST-MEM-007 — Ownership enforcement

Verify that unauthorized updates and deletions are rejected.

## TEST-MEM-008 — Provenance

Verify that relevant source metadata is preserved.

## TEST-MEM-009 — Duplicate handling

Verify that the chosen deduplication policy handles repeated storage without corrupting existing information.

## TEST-MEM-010 — Correction

Verify that correcting an outdated record does not leave the obsolete information active as an equally authoritative current fact.

## TEST-MEM-011 — Index consistency

When derived indexes are implemented, verify that updates and deletions are reflected in retrieval.

## TEST-MEM-012 — Untrusted content

Verify that malicious instructions contained within a stored document or memory cannot grant additional permissions or override trusted application instructions.

## TEST-MEM-013 — Explicit storage failure

Verify that Mikasa does not claim to have remembered information when the actual storage operation fails.

---

# 56. Memory Development Sequence

Memory should be implemented incrementally.

Suggested milestones:

**MEM-0 — Research**

Evaluate the memory repositories and select an initial architectural approach.

**MEM-1 — Contracts**

Define memory records, identifiers, ownership, scopes, and storage interfaces.

**MEM-2 — Persistence**

Implement the approved canonical memory store.

**MEM-3 — Basic Operations**

Implement creation, retrieval, updating, and deletion.

**MEM-4 — Access Control**

Enforce memory ownership and access scope.

**MEM-5 — Runtime Integration**

Connect the Memory Service to the Context Manager and Agent Runtime.

**MEM-6 — Initial Retrieval**

Implement a simple, useful search strategy.

**MEM-7 — Lifecycle**

Add correction, deduplication, and appropriate retention controls.

**MEM-8 — Evaluation**

Run the initial memory acceptance scenario and automated tests.

Future milestones may introduce semantic retrieval, relationships, advanced consolidation, shared memory, and skill learning.

These capabilities must not be implemented merely because they appear in the target architecture.

---

# 57. Architecture Decisions Required

The following decisions must be resolved during research and implementation planning:

```text
MEMORY-001
Memory framework selection.

MEMORY-002
Canonical storage technology.

MEMORY-003
Memory record schema.

MEMORY-004
Memory classification and namespaces.

MEMORY-005
Initial retrieval strategy.

MEMORY-006
Memory ownership and access control.

MEMORY-007
Memory creation and retention policy.

MEMORY-008
Memory correction and deletion behavior.

MEMORY-009
Index consistency strategy.

MEMORY-010
Future shared-memory architecture.
```

These identifiers are planning references.

Approved decisions must be recorded in:

`docs/22_DECISION_LOG.md`

---

# 58. Definition of Done

The MVP Memory Service is complete when:

- A structured memory record can be created.
- Records use stable identifiers.
- Basic memory categories and scopes are supported.
- Records persist after application restart.
- Relevant information can be retrieved.
- Authorized records can be updated.
- Records can be deleted.
- Basic access controls are enforced.
- Relevant source metadata is retained.
- Memory operations return accurate results.
- The Agent Runtime can retrieve memory through the approved Memory Service.
- The system distinguishes persistent memory from conversation history and operational state.
- The integrated acceptance scenario succeeds.
- Relevant automated tests pass.
- No unsupported functionality is presented as complete.

---

# 59. Final Memory Principle

Mikasa should not remember everything.

She should remember what matters, know where that information belongs, retrieve it when useful, recognize when it may be outdated, and allow it to be corrected or forgotten.

The memory architecture must preserve four fundamental distinctions:

```text
KNOWLEDGE IS NOT AUTHORITY.

CONTEXT IS NOT PERMANENT MEMORY.

SIMILARITY IS NOT TRUTH.

SHARING IS NOT UNRESTRICTED ACCESS.
```

The goal is not to create the largest possible memory database.

The goal is to create an organized, reliable, privacy-conscious memory system that helps Mikasa perform useful work over time.

**Remember deliberately. Retrieve selectively. Verify appropriately. Update correctly. Forget when required.**
