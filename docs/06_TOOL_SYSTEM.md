# M I K A S A
## Tool System Architecture Specification

**File:** `docs/06_TOOL_SYSTEM.md`

**Version:** 0.1.0

**Status:** PROPOSED — Pending architecture research and approval

**Authority:** Tool architecture, registration, discovery, execution, permissions, MCP, plugins, and capability management

**Applies to:** Main agent, specialist agents, tool providers, execution environments, external integrations, and extension systems.

---

# 1. Purpose

This document defines the architecture of Mikasa's tool system.

The tool system provides a standardized mechanism through which Mikasa can interact with files, development environments, browsers, external services, applications, and other supported environments.

The system must be designed so that new capabilities can be introduced without modifying the core Agent Runtime for every integration.

The tool system must support:

- Tool registration.
- Capability discovery.
- Structured input and output.
- Tool execution.
- Permission enforcement.
- Execution isolation.
- Error handling.
- Timeouts.
- Cancellation.
- Result verification.
- Operational logging.
- Native tools.
- MCP integrations.
- Future plugin-based extensions.

The initial implementation must prioritize a small, functional tool system rather than attempting to provide every conceivable capability.

---

# 2. Core Philosophy

Mikasa should interact with external environments through explicitly defined capabilities.

A capability is an action or service that Mikasa can use to complete an authorized task.

Examples:

```text
READ A FILE

SEARCH A DIRECTORY

MODIFY A PROJECT FILE

EXECUTE A TEST COMMAND

SEARCH PUBLIC INFORMATION

NAVIGATE A WEBPAGE

QUERY AN EXTERNAL SERVICE
```

The agent must not need to know the internal implementation details of every capability.

Instead, the tool system provides a consistent interface for discovering, selecting, authorizing, and executing tools.

**The model proposes an action. The tool system validates and executes it within the permissions granted to the task.**

---

# 3. High-Level Architecture

The proposed tool architecture:

```text
                 M I K A S A
                      |
                      v
                 AGENT RUNTIME
                      |
                      v
                 TOOL REGISTRY
                      |
                      v
                TOOL EXECUTOR
                      |
             +--------+--------+
             |                 |
             v                 v
       INPUT VALIDATOR    PERMISSION SERVICE
             |                 |
             +--------+--------+
                      |
                      v
              EXECUTION CONTROLLER
                      |
           +----------+----------+
           |          |          |
           v          v          v
         NATIVE      MCP       PLUGIN
         TOOLS       TOOLS     TOOLS
           |          |          |
           +----------+----------+
                      |
                      v
              STRUCTURED RESULT
                      |
                      v
                 AGENT RUNTIME
```

This is a logical architecture.

It does not require each component to run as a separate server or process.

---

# 4. Fundamental Components

The tool system consists of several distinct responsibilities.

| Component | Responsibility |
|---|---|
| Tool Registry | Maintains available tool definitions |
| Tool Discovery | Identifies capabilities available to an agent |
| Tool Selector | Helps select tools relevant to a task |
| Tool Executor | Coordinates and controls tool execution |
| Input Validator | Validates structured tool arguments |
| Permission Service | Authorizes requested operations |
| Execution Controller | Enforces execution environment and resource limits |
| Tool Adapters | Connect native, MCP, and plugin tools |
| Result Normalizer | Converts results into a common format |
| Tool Observability | Records execution events and outcomes |

These responsibilities may initially be implemented using a small number of focused modules.

Do not create unnecessary services or abstractions merely to mirror this table.

---

# 5. Tool Categories

Mikasa should eventually support three primary categories of tools.

## 5.1 Native Tools

Native tools are capabilities implemented directly within Mikasa's codebase.

Examples:

- File reading.
- Directory listing.
- File searching.
- Authorized file modification.
- Controlled terminal execution.
- Task inspection.
- Memory operations through approved interfaces.

Native tools should use the same standardized tool contracts as external integrations.

They must not receive unrestricted access merely because they are part of Mikasa's source code.

---

## 5.2 MCP Tools

Mikasa should support compatible tools exposed through the Model Context Protocol (MCP).

MCP integrations may eventually provide access to:

- External applications.
- Development services.
- Databases.
- Document systems.
- Search providers.
- Other authorized capabilities.

MCP should be treated as an integration protocol, not a replacement for Mikasa's internal runtime architecture.

MCP tools must pass through the same authorization and execution controls as native tools.

An MCP server's declaration that an operation is available does not mean the operation is authorized.

---

## 5.3 Plugin Tools

A future plugin system may provide packaged extensions containing tools, integrations, or other capabilities.

Plugins may contain multiple related tools.

Examples:

```text
PLUGIN: DEVELOPMENT

    repository inspection
    test execution
    project diagnostics
```

```text
PLUGIN: DOCUMENTS

    document search
    document reading
    document creation
```

Plugins must not automatically receive unrestricted filesystem, network, credential, or host access.

The complete plugin lifecycle is a future implementation responsibility.

---

# 6. Universal Tool Contract

Every registered tool must expose a standardized definition.

Proposed conceptual schema:

```text
ToolDefinition:
    tool_id
    name
    version

    description
    capability_category

    input_schema
    output_schema

    required_permissions
    execution_environment

    timeout
    side_effect_class

    provider_id
    metadata
```

This schema is conceptual.

The final implementation must define exact types, validation rules, and optional fields.

## Required properties

Every tool must provide:

- A unique registered identifier.
- A clear description of its purpose.
- A structured input contract.
- A structured result contract.
- Explicit permission requirements.
- Defined error behavior.

Tools with external side effects must also declare the relevant execution and approval requirements.

---

# 7. Tool Identity and Naming

Tool identifiers must be stable and unambiguous.

Suggested naming:

```text
filesystem.read_file

filesystem.list_directory

filesystem.search_files

filesystem.write_file

terminal.execute

browser.navigate

research.search
```

A tool identifier must not silently change its meaning after registration.

When multiple providers expose equivalent capabilities, the registry must distinguish them.

Example:

```text
native.filesystem.read_file

mcp.provider_a.read_file
```

The exact naming convention must be finalized during implementation.

Descriptions may change without changing tool identity when the underlying capability remains compatible.

---

# 8. Tool Registry

The Tool Registry is the authoritative directory of available tools.

Its responsibilities include:

- Registering tools.
- Validating definitions.
- Maintaining tool identifiers.
- Tracking tool availability.
- Exposing tool schemas.
- Associating tools with providers.
- Identifying permission requirements.
- Supporting tool removal or disabling.

Conceptual interface:

```text
ToolRegistry:
    register(tool_definition)

    unregister(tool_id)

    get(tool_id)

    list_available(context)

    get_capabilities(context)
```

These signatures are illustrative.

## Registration rule

A tool must be registered before the Agent Runtime can invoke it.

The agent must not execute arbitrary, unregistered functions merely because a model generated their names.

---

# 9. Capability Registry

Mikasa should distinguish between a tool and a capability.

A tool is an implementation.

A capability describes what the system can accomplish.

For example:

```text
CAPABILITY:
    Read project files

IMPLEMENTATION:
    native.filesystem.read_file
```

A future capability registry could map requirements to available implementations.

Conceptually:

```text
USER GOAL
    |
    v
REQUIRED CAPABILITIES
    |
    v
AVAILABLE TOOL IMPLEMENTATIONS
    |
    v
AUTHORIZED TOOL SELECTION
```

This allows Mikasa to eventually replace one tool provider with another without fundamentally changing the main agent's task-planning logic.

The MVP does not require an advanced capability-matching engine.

---

# 10. Tool Discovery

Mikasa should discover relevant tools rather than sending every registered tool to the model for every request.

Tool discovery should consider:

- Current task.
- Required capabilities.
- Available providers.
- Tool availability.
- Task permissions.
- Execution environment.
- Compatibility with the current model.

For example:

A coding task may need:

```text
filesystem.read_file

filesystem.search_files

filesystem.write_file

terminal.execute
```

It does not automatically need every messaging, calendar, browser, and database integration.

## Discovery rule

A tool that is unavailable or unauthorized must not be presented as an executable capability.

The system must distinguish between:

- Installed.
- Configured.
- Available.
- Authorized.
- Temporarily unavailable.
- Unsupported.

These states are not interchangeable.

---

# 11. Tool Selection

The Agent Runtime may use the model to propose appropriate tools.

However, tool selection is constrained by the registry and the current execution context.

A selected tool must:

1. Exist in the registry.
2. Be available.
3. Support the requested operation.
4. Accept the proposed arguments.
5. Be permitted for the current task.

The model must not invent a tool or capability and assume it exists.

If a required capability is unavailable, Mikasa should report the limitation or identify an approved alternative.

---

# 12. Tool Invocation Contract

Every tool invocation must have a traceable identity.

Proposed structure:

```text
ToolInvocation:
    invocation_id

    task_id
    execution_id

    tool_id
    arguments

    permission_context
    execution_context

    requested_at
```

The permission context must be derived from trusted application state.

Model-generated arguments must not be accepted as authoritative permission grants.

Every invocation should be associated with the task or execution that requested it.

---

# 13. Input Validation

Tool arguments must be validated before execution.

Validation should cover:

- Required fields.
- Data types.
- Allowed values.
- Input size limits.
- Supported operations.
- Resource identifiers.
- Applicable path or domain restrictions.

Example:

```text
TOOL:
    filesystem.read_file

INPUT:
    path: string
```

Providing a string is not sufficient authorization to read that path.

The filesystem tool must also enforce the configured workspace and access policy.

Malformed arguments must produce a structured validation error.

The tool must not execute an invalid request and attempt to repair the damage afterward.

---

# 14. Tool Execution Lifecycle

Every tool invocation should follow a consistent execution process.

```text
TOOL REQUEST
     |
     v
RESOLVE REGISTERED TOOL
     |
     v
VALIDATE INPUT
     |
     v
CHECK AVAILABILITY
     |
     v
CHECK PERMISSIONS
     |
     +---- DENIED ------> RETURN DENIAL
     |
     v
SELECT EXECUTION ENVIRONMENT
     |
     v
EXECUTE TOOL
     |
     v
CAPTURE RESULT
     |
     v
NORMALIZE OUTPUT
     |
     v
RECORD EXECUTION
     |
     v
RETURN TO AGENT RUNTIME
```

Actual permission enforcement must also occur at the resource-access boundary.

Passing an earlier permission check does not authorize a tool to access resources outside its granted scope.

---

# 15. Execution Environments

Not every tool should execute with the same privileges.

Possible execution environments include:

```text
READ_ONLY

SANDBOXED

PROJECT_WRITE

APPROVED_NETWORK

CONTROLLED_HOST
```

These labels represent capability boundaries, not interchangeable permission levels.

A tool may require a combination of specific permissions.

For example, a sandboxed test command may require project reading and limited write access but no unrestricted host control.

The final isolation technology must be selected during architecture research.

---

# 16. Permission Enforcement

Every tool must declare its required permissions.

Possible permission categories include:

```text
filesystem.read

filesystem.write

terminal.execute

network.access

browser.navigate

external_service.read

external_service.write

credentials.use

desktop.control
```

Actual permissions must be more precise when necessary.

For example:

```text
filesystem.read:
    allowed_workspace: project_alpha
```

is different from unrestricted filesystem access.

## Authorization rule

The model cannot grant itself additional permissions.

A plugin cannot grant itself access by declaring a broader permission requirement.

An MCP server cannot authorize its own access to unrelated user resources.

Permission decisions must be enforced by trusted application code.

Detailed policy belongs in:

`docs/10_SECURITY_PERMISSIONS.md`

---

# 17. Approval Requests

Some tool operations may require explicit user approval.

Examples include:

- Destructive changes.
- Sensitive external actions.
- Elevated computer access.
- Changes outside an approved project workspace.
- Installing or enabling an extension with additional privileges.

When approval is required:

```text
TOOL REQUEST
     |
     v
PERMISSION SERVICE
     |
     v
APPROVAL REQUIRED
     |
     v
PAUSE REQUESTED ACTION
     |
     v
PRESENT ACTION DETAILS
     |
     v
USER DECISION
     |
     +---- APPROVED ---> REVALIDATE AND EXECUTE
     |
     +---- DENIED -----> REJECT ACTION
```

Approval must be specific to the requested action or explicitly defined scope.

A previous approval must not silently authorize unrelated future operations.

A denied action must not be retried through another tool to bypass the decision.

---

# 18. Structured Tool Results

Every tool must return a normalized result.

Proposed schema:

```text
ToolResult:
    invocation_id
    tool_id

    status

    output
    error

    started_at
    completed_at

    duration
    metadata
```

Suggested statuses:

```text
SUCCESS

FAILURE

TIMEOUT

DENIED

CANCELLED

UNKNOWN_OUTCOME
```

The `UNKNOWN_OUTCOME` state is important for operations where execution may have occurred but the result cannot be confirmed.

For example, an external service might complete an operation before the connection fails.

The runtime must not blindly repeat such an operation.

---

# 19. Error Handling

Tools must return meaningful errors.

Possible categories:

```text
INVALID_ARGUMENTS

TOOL_NOT_FOUND

TOOL_UNAVAILABLE

PERMISSION_DENIED

EXECUTION_FAILED

TIMEOUT

CANCELLED

PROVIDER_ERROR

RESOURCE_NOT_FOUND

UNKNOWN_ERROR
```

Error messages should provide enough information for diagnosis without exposing credentials or unnecessary sensitive data.

The Tool Executor reports execution errors.

The Agent Runtime determines whether the task can continue or requires another strategy.

---

# 20. Timeouts and Resource Limits

Tool execution must support configurable limits.

Possible limits include:

- Maximum execution duration.
- Maximum output size.
- Maximum concurrent operations.
- Filesystem access scope.
- Network access scope.
- Memory usage.
- Process resource limits.

The initial implementation must enforce tool timeouts and relevant workspace restrictions.

Additional resource controls should be introduced according to the selected execution environment.

Timeouts must not be treated as proof that an external action did not occur.

---

# 21. Cancellation

Tool execution must support cancellation where practical.

When a task is cancelled:

1. Stop scheduling new tool invocations.
2. Request cancellation of active operations where supported.
3. Collect available execution outcomes.
4. Perform appropriate cleanup.
5. Report the actual cancellation state.

Some operations may not support immediate cancellation.

The Tool Executor must communicate this limitation accurately to the Agent Runtime.

---

# 22. Idempotency and Safe Retries

The tool system must distinguish between operations that can safely be repeated and operations that may produce additional side effects.

Examples:

Reading a file can usually be repeated.

Creating a new external record may not be safe to repeat without checking whether the first operation succeeded.

Tool metadata may include:

```text
side_effect_class

retry_policy

supports_idempotency_key

supports_result_verification
```

Retries must follow the tool's approved policy.

The runtime must not automatically retry every failed or timed-out operation.

For operations with uncertain outcomes, verification or user intervention may be required before continuing.

---

# 23. Output Handling

Tool outputs may be large, incomplete, malformed, or untrusted.

The Tool Executor should enforce output limits and return structured metadata.

Large outputs may be:

- Truncated.
- Paginated.
- Summarized through an approved process.
- Stored as referenced task artifacts.

Truncation must be indicated clearly.

The runtime must not present truncated output as though it contains the complete result.

Tool outputs must not automatically become permanent memories.

---

# 24. Tool Result Verification

Successful execution does not necessarily mean the user's objective was achieved.

For example:

```text
FILE WRITE SUCCEEDED
```

does not prove:

```text
APPLICATION BUG FIXED
```

Tool results should expose enough information for the runtime to verify task outcomes.

Verification may require an additional tool action.

Examples:

- Read the modified file.
- Run an available test.
- Check the resulting application state.
- Confirm the expected external record exists.

Verification requirements must follow the current task and its acceptance criteria.

---

# 25. Filesystem Tools

The MVP must provide basic filesystem capabilities within an approved workspace.

Initial capabilities:

```text
filesystem.list_directory

filesystem.read_file

filesystem.search_files

filesystem.write_file
```

Additional operations may include file creation, modification, and authorized deletion when required by an approved task.

## Filesystem requirements

- Enforce workspace boundaries.
- Validate requested paths.
- Prevent unauthorized access outside the workspace.
- Distinguish files from directories.
- Handle missing resources.
- Report actual read and write results.
- Respect configured size limits.

Filesystem restrictions must account for symbolic links, path resolution, and other ways a requested path could refer to a resource outside the approved workspace.

The tool must not trust a model-generated path merely because it appears to be inside the project directory.

---

# 26. Terminal Tools

The MVP must support controlled terminal execution.

Proposed capability:

```text
terminal.execute
```

The terminal tool should support approved development tasks such as:

- Running project tests.
- Checking application versions.
- Inspecting project state.
- Running authorized development commands.

## Requirements

- Use the approved execution environment.
- Enforce the assigned permission context.
- Support timeouts.
- Capture standard output.
- Capture standard error.
- Return exit status.
- Support cancellation where practical.
- Respect workspace boundaries.
- Apply output limits.

Terminal access must not automatically imply unrestricted host access.

The final command execution, isolation, and permission strategy must be defined before implementation.

---

# 27. Browser Tools

Browser interaction is a future capability.

Potential operations include:

```text
browser.navigate

browser.inspect_page

browser.extract_content

browser.interact

browser.capture_screenshot
```

The browser subsystem should eventually support:

- Navigation.
- Page inspection.
- Content extraction.
- Controlled interaction.
- Result verification.
- Session management.

Browser actions with external consequences must use appropriate permissions.

For example, reading a public webpage and submitting an external transaction are different kinds of operations.

The browser subsystem must treat webpage content as untrusted task data.

Detailed browser capabilities belong in:

`docs/12_RESEARCH_ENGINE.md`

---

# 28. Research Tools

The research subsystem may eventually expose:

```text
research.search

research.fetch_source

research.extract_content

research.compare_sources
```

Research tools should preserve sufficient source information for verification.

Potential result metadata:

```text
source_url

source_title

retrieval_timestamp

content_reference

extraction_status
```

The system must distinguish retrieved information from model-generated inference.

Research output must not automatically be stored as verified persistent knowledge.

---

# 29. Coding Tools

Coding capabilities should use approved tool interfaces.

Potential operations:

```text
code.inspect_project

code.search_repository

code.apply_patch

code.run_tests

code.inspect_diff
```

Some of these capabilities may be implemented through combinations of filesystem, terminal, and version-control tools rather than separate specialized tools.

Do not duplicate existing capabilities without an architectural reason.

Coding workflows must respect:

- Project boundaries.
- Approved task scope.
- File permissions.
- Execution isolation.
- Verification requirements.

Detailed coding architecture belongs in:

`docs/13_CODING_ENGINE.md`

---

# 30. MCP Integration Architecture

MCP integrations must be separated from the core Agent Runtime through an adapter.

Conceptually:

```text
AGENT RUNTIME
      |
      v
TOOL REGISTRY
      |
      v
TOOL EXECUTOR
      |
      v
MCP ADAPTER
      |
      v
AUTHORIZED MCP SERVER
      |
      v
EXTERNAL CAPABILITY
```

The MCP adapter should translate between Mikasa's internal tool contracts and the compatible external protocol.

## Responsibilities

- Manage configured MCP connections.
- Discover available tools.
- Validate external tool definitions.
- Map tool identifiers.
- Translate invocation arguments.
- Normalize results.
- Handle connection errors.
- Enforce applicable permissions.

The adapter must not grant permissions based solely on metadata supplied by an MCP server.

---

# 31. MCP Server Configuration

Mikasa should eventually support explicit MCP server configuration.

Conceptual configuration:

```text
MCPServerConfig:
    server_id
    display_name
    connection_type
    connection_config
    enabled
    permission_policy
```

Exact configuration fields depend on the approved MCP implementation and transport.

Credentials must be managed through an approved secrets system.

They must not be stored as ordinary agent memories.

The initial implementation may use a manually configured list of trusted MCP servers.

Automatic discovery and installation are later capabilities.

---

# 32. MCP Tool Discovery

When an MCP server is connected, Mikasa may discover its advertised tools.

The discovery process should:

1. Identify the configured server.
2. Establish a connection through the approved adapter.
3. Retrieve available tool definitions.
4. Validate supported schemas.
5. Associate tools with their provider.
6. Determine applicable permissions.
7. Register approved tools.
8. Expose available capabilities to the runtime.

Discovery does not automatically authorize execution.

An MCP server may advertise capabilities that are unavailable or prohibited under the current permission policy.

---

# 33. MCP Failure Handling

MCP integrations may fail independently of Mikasa's core runtime.

Possible failures:

- Server unavailable.
- Connection interrupted.
- Invalid tool schema.
- Authentication failure.
- Unsupported protocol behavior.
- Tool execution timeout.
- Malformed response.

The adapter must normalize these failures.

An unavailable MCP server must not unnecessarily crash the entire assistant.

The registry should accurately reflect the availability of affected tools.

---

# 34. Plugin Architecture

A future plugin system should allow related capabilities to be packaged and managed together.

A plugin may provide:

- Tools.
- Model adapters.
- Memory adapters.
- Interface integrations.
- Skill definitions.
- Event handlers.

Plugins must use explicitly defined extension interfaces.

They must not be allowed to directly modify arbitrary internal runtime state.

---

# 35. Plugin Manifest

Every future plugin should include a structured manifest.

Conceptual schema:

```text
PluginManifest:
    plugin_id
    name
    version

    description
    publisher

    provided_capabilities
    required_permissions

    dependencies
    compatibility

    entrypoint
    integrity_metadata
```

A plugin manifest must not be treated as proof that the plugin is trustworthy.

Plugin provenance, compatibility, dependencies, and permission requirements must be evaluated before activation.

---

# 36. Plugin Lifecycle

The future plugin system should distinguish:

```text
DISCOVERED

EVALUATED

INSTALLED

CONFIGURED

ENABLED

DISABLED

REMOVED
```

These lifecycle states are different.

A downloaded plugin is not automatically installed.

An installed plugin is not automatically enabled.

An enabled plugin is not automatically authorized to perform every action it exposes.

---

# 37. Learning New Capabilities

One of Mikasa's long-term goals is extensibility.

When Mikasa encounters a task requiring an unavailable capability, she should eventually be able to identify the missing capability and propose a suitable extension.

Conceptually:

```text
USER GOAL
     |
     v
IDENTIFY REQUIRED CAPABILITY
     |
     v
CHECK EXISTING TOOLS
     |
     +---- AVAILABLE ---> USE AUTHORIZED TOOL
     |
     v
CAPABILITY UNAVAILABLE
     |
     v
IDENTIFY POSSIBLE EXTENSION
     |
     v
EVALUATE COMPATIBILITY AND TRUST
     |
     v
PRESENT EXTENSION PROPOSAL
     |
     v
OBTAIN REQUIRED APPROVAL
     |
     v
INSTALL / CONFIGURE THROUGH APPROVED PROCESS
     |
     v
VERIFY CAPABILITY
```

Mikasa must not automatically install arbitrary external code simply because a webpage or model response recommends it.

New capabilities must respect the project's dependency, permission, and security policies.

Automated extension management is outside the MVP scope.

---

# 38. Tool Versioning

Tools may evolve over time.

The registry should eventually support identifying tool versions.

Changes that break existing input or output contracts must be handled explicitly.

The runtime must not assume that a cached tool definition remains valid after a provider or plugin update.

Tool compatibility must be verified when required.

---

# 39. Tool Dependencies

Some tools may depend on:

- External executables.
- Installed packages.
- Network services.
- Model capabilities.
- Credentials.
- Operating-system features.
- Other tools.

The tool registry should eventually distinguish a registered tool from an operational tool.

Example:

A browser tool may be registered but unavailable because its browser backend is not configured.

The agent must not present that capability as functional.

Missing dependencies must produce understandable diagnostics.

---

# 40. Tool Availability and Health

The future tool system should support basic availability checks.

Potential states:

```text
AVAILABLE

UNAVAILABLE

DEGRADED

DISABLED

UNCONFIGURED
```

Health checks should be lightweight and appropriate to the provider.

An external connection failure should not require restarting the entire agent when recovery is possible.

Tool health information should be accessible through the runtime's operational interface.

---

# 41. Tool Observability

Tool operations must produce structured execution records.

Potential events:

```text
tool.registered

tool.unregistered

tool.requested

tool.started

tool.completed

tool.failed

tool.denied

tool.cancelled

tool.timeout
```

Records should include:

- Invocation ID.
- Task ID.
- Execution ID.
- Tool ID.
- Provider ID.
- Execution status.
- Duration.
- Error category where applicable.

Logs must not unnecessarily expose credentials, private file contents, or other sensitive information.

---

# 42. Tool Evaluation

New tools must be evaluated before being considered functional.

Evaluation should cover:

- Contract compliance.
- Input validation.
- Permission enforcement.
- Successful execution.
- Error handling.
- Timeout behavior.
- Cancellation.
- Result normalization.
- Resource restrictions.
- Compatibility with the Agent Runtime.

External integrations should also be tested for unavailable servers, invalid responses, and authentication failures.

A tool must not be marked functional solely because it can be registered.

---

# 43. Initial Implementation Scope

The first working version of Mikasa requires a focused tool system.

| Capability | MVP status |
|---|---|
| Tool Registry | Required |
| Tool definition schema | Required |
| Tool invocation contract | Required |
| Input validation | Required |
| Structured results | Required |
| Permission enforcement | Required |
| Tool timeouts | Required |
| Basic cancellation | Required |
| Filesystem tools | Required |
| Controlled terminal tool | Required |
| Tool execution logging | Required |
| MCP integration | Future phase |
| Plugin framework | Future phase |
| Browser interaction | Future phase |
| Advanced capability selection | Future phase |
| Automatic extension discovery | Future phase |
| Automatic extension installation | Future phase |
| Full desktop control | Future phase |

Do not implement future capabilities merely because their architecture is described here.

---

# 44. MVP Acceptance Scenario

The first tool-system implementation must support a functional integration scenario.

**User request:**

"Mikasa, inspect the files in this project, open its configuration file, identify the relevant setting, and make the requested change."

Expected behavior:

```text
RECEIVE USER REQUEST
        |
        v
IDENTIFY REQUIRED TOOLS
        |
        v
DISCOVER AVAILABLE TOOLS
        |
        v
VALIDATE TASK PERMISSIONS
        |
        v
LIST PROJECT FILES
        |
        v
READ CONFIGURATION FILE
        |
        v
IDENTIFY REQUIRED CHANGE
        |
        v
EXECUTE AUTHORIZED FILE MODIFICATION
        |
        v
READ FILE AGAIN
        |
        v
VERIFY EXPECTED CONTENT
        |
        v
REPORT RESULT
```

The scenario must use actual registered tools.

Hard-coded tool outputs do not satisfy the acceptance criteria.

The same implementation must reject an unauthorized attempt to access a resource outside the configured workspace.

---

# 45. Tool Testing Requirements

The following tests must be implemented for the relevant MVP capabilities.

## TOOL-TEST-001 — Registration

Verify that a valid tool can be registered.

## TOOL-TEST-002 — Duplicate Registration

Verify that duplicate identifiers cannot silently overwrite an existing tool.

## TOOL-TEST-003 — Discovery

Verify that available tools can be retrieved through the registry.

## TOOL-TEST-004 — Unknown Tool

Verify that requesting an unregistered tool returns a structured error.

## TOOL-TEST-005 — Input Validation

Verify that invalid arguments are rejected before execution.

## TOOL-TEST-006 — Permission Denial

Verify that unauthorized tool requests are rejected.

## TOOL-TEST-007 — Filesystem Boundaries

Verify that file operations cannot escape the configured workspace.

## TOOL-TEST-008 — Successful Execution

Verify that a valid tool invocation returns the expected structured result.

## TOOL-TEST-009 — Tool Failure

Verify that execution failures produce structured errors.

## TOOL-TEST-010 — Timeout

Verify that tool timeouts are enforced and reported.

## TOOL-TEST-011 — Cancellation

Verify that cancellation prevents additional actions and terminates supported active operations.

## TOOL-TEST-012 — Unknown Outcome

Verify that uncertain external outcomes are not automatically retried as though no action occurred.

## TOOL-TEST-013 — Tool Output Injection

Verify that instructions embedded in untrusted tool results cannot override runtime permissions.

## TOOL-TEST-014 — Integration

Verify that the Agent Runtime can discover, invoke, and consume results from real registered tools.

---

# 46. Development Sequence

The tool system should be implemented incrementally.

**TOOLS-0 — Research**

Study the tool architectures of Gemini CLI, OpenHands, DeerFlow, OpenClaw, and other relevant repositories.

**TOOLS-1 — Contracts**

Define tool definitions, invocation requests, results, and error contracts.

**TOOLS-2 — Registry**

Implement tool registration and discovery.

**TOOLS-3 — Executor**

Implement validation, execution coordination, timeouts, and structured results.

**TOOLS-4 — Permissions**

Integrate trusted permission enforcement.

**TOOLS-5 — Native Tools**

Implement the initial filesystem and controlled terminal tools.

**TOOLS-6 — Runtime Integration**

Connect tool discovery and invocation to the Agent Runtime.

**TOOLS-7 — Verification**

Complete functional, integration, and permission tests.

Future milestones may introduce MCP, browser tools, plugins, dynamic capability selection, and extension management.

---

# 47. Architecture Decisions Required

The following decisions must be resolved before the relevant implementation begins:

```text
TOOLS-ADR-001
Internal tool definition format.

TOOLS-ADR-002
Tool execution contract.

TOOLS-ADR-003
Tool input and output schema validation.

TOOLS-ADR-004
Initial native tool set.

TOOLS-ADR-005
Filesystem boundary enforcement.

TOOLS-ADR-006
Terminal execution isolation.

TOOLS-ADR-007
Permission integration.

TOOLS-ADR-008
Tool timeout and cancellation strategy.

TOOLS-ADR-009
MCP integration approach.

TOOLS-ADR-010
Future plugin architecture.
```

These identifiers are planning references.

Approved decisions must be recorded in:

`docs/22_DECISION_LOG.md`

---

# 48. Definition of Done

The MVP Tool System is complete when:

- Tools can be registered through a common interface.
- Registered tools can be discovered.
- Tool arguments are validated.
- Unknown tools are rejected.
- Permissions are enforced.
- Filesystem operations respect workspace boundaries.
- Controlled terminal execution works.
- Tool execution returns structured results.
- Errors and timeouts are handled.
- Cancellation is supported where required.
- Tool operations are observable.
- The Agent Runtime can invoke registered tools.
- Tool results can influence subsequent agent actions.
- The integrated acceptance scenario succeeds.
- Relevant automated tests pass.

The implementation must not depend on hard-coded responses or simulated tool execution to claim completion.

---

# 49. Final Tool System Principle

Mikasa should be able to gain new capabilities without requiring every new integration to change the core Agent Runtime.

Tools must remain discoverable, structured, replaceable, and controlled.

The architecture must preserve the distinction between:

```text
AVAILABLE CAPABILITY

AUTHORIZED CAPABILITY

EXECUTED ACTION

VERIFIED RESULT
```

These are four different things.

**A tool gives Mikasa the ability to act. The runtime decides when to act. The permission system determines what is allowed. Verification determines whether the action achieved its intended result.**
