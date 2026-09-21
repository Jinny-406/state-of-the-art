# M I K A S A
## Model Router Architecture Specification

**File:** `docs/09_MODEL_ROUTER.md`

**Version:** 0.1.0

**Status:** PROPOSED — Pending architecture research and approval

**Authority:** Model integration, provider abstraction, model selection, routing, failover, and usage management

**Applies to:** Main Agent, specialist agents, Agent Runtime, Model Router, model providers, configuration, and model-related infrastructure.

---

# 1. Purpose

This document defines the model architecture of M I K A S A.

The Model Router provides a standardized interface between Mikasa's Agent Runtime and the AI models used to perform tasks.

Its purpose is to prevent the application from becoming permanently dependent on one provider, API format, or model.

The Model Router should eventually support:

- Multiple model providers.
- Cloud models.
- Local models.
- OpenAI-compatible endpoints.
- Model capability discovery.
- Task-based model selection.
- Specialist-specific model configurations.
- Provider availability monitoring.
- Model fallback.
- Request and response normalization.
- Streaming.
- Token and usage tracking.
- Resource and cost limits.
- Provider error handling.
- Configurable model preferences.

The initial implementation must remain focused on establishing a reliable abstraction with at least one functioning provider.

Advanced routing and provider orchestration are future capabilities.

---

# 2. Core Principle

The Agent Runtime must not depend directly on a specific AI provider.

All model requests must pass through a common interface.

```text
                M I K A S A
                     |
                     v
                AGENT RUNTIME
                     |
                     v
                 MODEL ROUTER
                     |
                     v
              PROVIDER INTERFACE
                     |
          +----------+----------+
          |          |          |
          v          v          v
       CLOUD A    CLOUD B     LOCAL
       PROVIDER   PROVIDER    MODEL
          |          |          |
          +----------+----------+
                     |
                     v
             NORMALIZED RESPONSE
                     |
                     v
                AGENT RUNTIME
```

The Agent Runtime should not need to know whether a response came from a cloud provider or a local inference engine.

Provider-specific implementation details belong inside provider adapters.

---

# 3. Architectural Responsibilities

The Model Router is responsible for:

- Resolving the configured model.
- Checking model availability.
- Validating required capabilities.
- Selecting the appropriate provider adapter.
- Applying supported request configuration.
- Coordinating provider requests.
- Normalizing provider responses.
- Handling provider-level errors.
- Recording relevant usage information.
- Supporting future routing and fallback policies.

The Model Router is not responsible for:

- Planning tasks.
- Executing tools.
- Modifying persistent memories.
- Granting permissions.
- Controlling task lifecycle.
- Deciding whether a user's objective has been achieved.
- Creating unrelated specialist agents.

Those responsibilities belong to other Mikasa components.

---

# 4. Internal Architecture

The proposed logical structure is:

```text
                 AGENT RUNTIME
                       |
                       v
                  MODEL ROUTER
                       |
          +------------+------------+
          |            |            |
          v            v            v
        MODEL       CAPABILITY     ROUTING
       REGISTRY     VALIDATOR      POLICY
          |            |            |
          +------------+------------+
                       |
                       v
                PROVIDER ADAPTER
                       |
                       v
                MODEL EXECUTION
                       |
                       v
               RESPONSE NORMALIZER
                       |
                       v
                 USAGE TRACKER
                       |
                       v
                 AGENT RUNTIME
```

These are logical responsibilities.

They do not require separate servers, processes, or classes.

The MVP may implement them using a small number of focused modules.

---

# 5. Model Provider Abstraction

A model provider is an integration that allows Mikasa to communicate with one or more AI models.

Examples of provider categories:

```text
CLOUD API PROVIDER

LOCAL INFERENCE PROVIDER

OPENAI-COMPATIBLE PROVIDER

SPECIALIZED MODEL PROVIDER
```

A provider may expose multiple models.

A model may have different capabilities depending on its provider, version, configuration, and supported API.

Provider and model identity must remain separate.

---

# 6. Provider Adapter

Every supported provider must implement an approved provider interface.

Conceptual interface:

```text
ModelProvider:
    generate(request)
        -> ModelResponse

    get_capabilities(model_id)
        -> ModelCapabilities

    check_availability()
        -> ProviderStatus
```

Future capabilities may include:

```text
stream(request)
    -> ModelEventStream

count_tokens(request)
    -> TokenEstimate

list_models()
    -> ModelList
```

These signatures are illustrative.

The final implementation must define asynchronous behavior, exceptions, cancellation, streaming, and return types.

Not every provider supports every optional operation.

Unsupported capabilities must be represented explicitly.

---

# 7. Provider Independence

Provider-specific details must remain inside provider adapters.

For example:

```text
AGENT RUNTIME
      |
      v
NORMALIZED MODEL REQUEST
      |
      v
MODEL PROVIDER ADAPTER
      |
      v
PROVIDER-SPECIFIC API REQUEST
```

The provider adapter translates the internal request into the format required by the selected provider.

The response is then converted back into Mikasa's internal response contract.

The Agent Runtime must not contain separate execution branches for every provider's API.

---

# 8. Model Registry

The Model Registry maintains information about configured and available models.

Proposed conceptual structure:

```text
ModelDefinition:
    model_id
    provider_id

    display_name
    model_identifier

    capabilities
    context_limit

    supported_input_types
    supported_output_types

    configuration
    availability

    metadata
```

The registry must distinguish between:

- A model definition.
- A configured model.
- An available model.
- A model authorized for the current task.

A model being listed in a configuration file does not prove that the corresponding provider is reachable or that its credentials are valid.

---

# 9. Model Identity

Every configured model must have a stable internal identifier.

Example:

```text
model_id: general_default
```

A separate provider-specific identifier may contain the actual model name required by the API.

Example:

```text
provider_id: provider_a

model_identifier: provider-specific-model-name
```

The Agent Runtime should use the internal identifier or approved routing profile.

Provider-specific model names should remain within the model configuration and provider adapter layers.

---

# 10. Model Capability Representation

Different models support different capabilities.

The Model Registry should describe relevant capabilities explicitly.

Potential capabilities:

```text
TEXT_INPUT

TEXT_OUTPUT

TOOL_CALLING

STRUCTURED_OUTPUT

STREAMING

VISION_INPUT

AUDIO_INPUT

AUDIO_OUTPUT

LONG_CONTEXT
```

Future capabilities may be added when needed.

Capability support must be verified through provider documentation or appropriate integration tests.

Do not assume every OpenAI-compatible endpoint supports every OpenAI-style feature.

---

# 11. Capability Requirements

A model request may specify required capabilities.

Example:

```text
ModelRequirements:
    text_input: true
    text_output: true
    tool_calling: true
```

The Model Router must not select a model that lacks a required capability merely because it is cheaper or faster.

If no compatible model is available, the router must return an appropriate error.

The runtime may then report the limitation or select an approved alternative execution strategy.

An alternative strategy must still satisfy the task's actual requirements.

---

# 12. Model Profiles

Mikasa may eventually support reusable model profiles.

Profiles describe the kind of model capability requested by a task.

Possible profiles:

```text
DEFAULT

FAST

REASONING

CODING

RESEARCH

VISION

LONG_CONTEXT

LOCAL
```

These profiles are configuration and routing categories.

They do not guarantee that a selected model is actually capable of completing every task associated with the profile.

Model profiles must be mapped to real, configured models.

---

# 13. Model Selection

The initial implementation may use one configured default model.

Future selection strategies may consider:

- Required capabilities.
- Task type.
- Provider availability.
- User preferences.
- Configured model profiles.
- Context requirements.
- Resource constraints.
- Usage limits.
- Cost.
- Performance measured through evaluations.

Selection must remain deterministic and inspectable where practical.

The router should record which model was selected.

---

# 14. Routing Policies

The Model Router should eventually support configurable routing policies.

Potential policies include:

**Fixed routing**

Use one explicitly configured model.

**Profile routing**

Use the model assigned to the requested profile.

**Capability routing**

Select from models that satisfy required capabilities.

**Availability routing**

Use an approved alternative when the preferred provider is unavailable.

**Resource-aware routing**

Choose an eligible model according to configured resource or cost constraints.

Advanced routing must not be implemented before the initial provider abstraction works reliably.

---

# 15. Main Agent Model

The Main Agent must obtain model access through the Model Router.

Its configuration may specify:

```text
MainAgentModelConfig:
    preferred_model
    required_capabilities
    generation_options
```

The Main Agent must not directly instantiate provider-specific clients.

Changing the primary model should not require modifying the Main Agent's execution logic.

---

# 16. Specialist-Agent Models

Future specialist agents may use different model configurations.

Example:

```text
MAIN AGENT
    |
    +---- RESEARCH SPECIALIST
    |         |
    |         v
    |     RESEARCH MODEL
    |
    +---- CODING SPECIALIST
    |         |
    |         v
    |      CODING MODEL
    |
    +---- TESTING SPECIALIST
              |
              v
          GENERAL MODEL
```

Specialist model selection must use the shared Model Router.

Specialists must not independently manage unrestricted provider credentials.

The selected model must satisfy the specialist's actual capability requirements.

---

# 17. Model Request Contract

Every model request must use a normalized internal contract.

Proposed structure:

```text
ModelRequest:
    request_id
    execution_id

    model_reference

    messages
    available_tools

    generation_options
    required_capabilities

    timeout
    cancellation_context

    metadata
```

The exact schema must be defined during implementation.

The request must distinguish trusted instructions from lower-trust content such as retrieved documents and tool outputs.

The router must preserve the appropriate instruction structure supported by the selected provider.

It must not silently discard essential runtime constraints when converting request formats.

---

# 18. Message Representation

Different providers may use different message formats.

Mikasa should maintain a normalized internal message representation.

Conceptual structure:

```text
ModelMessage:
    message_id
    role
    content
    tool_call_reference
    metadata
```

Possible content types:

```text
TEXT

IMAGE

AUDIO

TOOL_REQUEST

TOOL_RESULT
```

Not every content type is required for the MVP.

The initial implementation may support text and the structured tool messages necessary for its selected provider.

Future multimodal support must extend the internal contracts without breaking existing text-based workflows.

---

# 19. Tool Calling

Tool calling is essential to Mikasa's agent runtime.

The model may propose tool invocations.

However, model-generated tool calls must pass through the Agent Runtime and Tool System.

```text
MODEL RESPONSE
      |
      v
NORMALIZE TOOL CALL
      |
      v
AGENT RUNTIME
      |
      v
TOOL REGISTRY
      |
      v
VALIDATION AND AUTHORIZATION
      |
      v
TOOL EXECUTOR
```

The Model Router must not directly execute tool calls returned by a provider.

It should normalize the requested tool name, arguments, and relevant call identifiers.

The Tool System remains responsible for actual execution.

---

# 20. Tool-Calling Compatibility

Different models may have different tool-calling capabilities.

The Model Router must account for:

- Native structured tool calling.
- Supported argument formats.
- Multiple tool calls in one response.
- Tool call identifiers.
- Tool result message requirements.
- Provider-specific limitations.

A provider that accepts an OpenAI-compatible HTTP request does not necessarily support identical tool-call semantics.

Tool-calling behavior must be verified through integration tests.

The system must not present a provider as fully compatible based only on successful basic text generation.

---

# 21. Structured Output

Some Mikasa workflows may require structured model responses.

Examples:

- Task plans.
- Tool arguments.
- Classification results.
- Specialist delegation requests.
- Evaluation results.

The router should eventually support a normalized structured-output request contract.

Where a provider supports constrained structured output, the adapter may use it.

Where the provider does not support it, the runtime may use an approved alternative followed by schema validation.

Invalid structured output must produce an appropriate error.

A model response must not bypass validation because it appears syntactically correct.

---

# 22. Model Response Contract

Model responses must be normalized.

Proposed structure:

```text
ModelResponse:
    response_id
    request_id

    provider_id
    model_id

    content
    tool_calls

    finish_reason
    usage

    status
    metadata
```

The exact contract must distinguish successful responses from provider failures.

Provider-specific information may be preserved in optional metadata without forcing unrelated runtime components to understand it.

---

# 23. Finish Reasons

Different providers may report different reasons for ending a generation.

Mikasa should normalize relevant outcomes.

Possible categories:

```text
COMPLETE

TOOL_CALL

LENGTH_LIMIT

CANCELLED

PROVIDER_REJECTED

ERROR

UNKNOWN
```

The final mapping must be based on the actual supported provider APIs.

A response ending because of a length limit must not automatically be treated as a complete final answer.

A response containing tool calls must be passed to the Agent Runtime for validation and execution.

---

# 24. Streaming

Mikasa should eventually support streaming model responses.

Streaming may improve:

- Interactive conversations.
- Perceived responsiveness.
- Voice integration.
- Long responses.
- Progress display.

The architecture must distinguish between:

- A partial output fragment.
- A complete response.
- A complete tool-call request.
- A provider error.
- A cancelled stream.

Incomplete tool-call arguments must not be executed.

The router must assemble and validate the complete supported tool request before passing it to the runtime.

Streaming is optional for the initial MVP unless required by the selected interface.

---

# 25. Streaming Contract

A future normalized stream may emit:

```text
RESPONSE_STARTED

TEXT_DELTA

TOOL_CALL_DELTA

TOOL_CALL_COMPLETED

USAGE_UPDATE

RESPONSE_COMPLETED

RESPONSE_FAILED
```

These events are conceptual.

The final schema must be compatible with the application's event and runtime systems.

Streaming must not bypass cancellation, resource limits, or permission enforcement.

---

# 26. Generation Configuration

The Model Router should support provider-independent generation options where practical.

Possible configuration:

```text
GenerationOptions:
    max_output_tokens
    temperature
    top_p
    stop_sequences
```

Not every model supports every option.

The provider adapter must map supported values correctly.

Unsupported options must be rejected, explicitly omitted according to an approved policy, or reported as unavailable.

The router must not silently claim that a generation setting was applied when the provider ignored it.

---

# 27. Context Limits

Different models have different context capacities.

The Model Registry should store verified context limits where available.

The Context Manager remains responsible for preparing relevant context.

The Model Router must validate applicable provider limits before sending requests when those limits are known.

The router must distinguish between:

- Input context capacity.
- Output token limits.
- Combined input/output constraints.
- Provider-specific restrictions.

Unknown limits must not be represented as unlimited capacity.

---

# 28. Context Overflow

When a request exceeds the selected model's supported context capacity, the router must return an appropriate error or request an approved context-reduction strategy.

The router must not silently remove important system instructions, task constraints, permissions, or critical tool results.

Context reduction belongs primarily to the Context Manager.

The Model Router enforces the selected provider's request constraints.

---

# 29. Provider Configuration

Provider configuration must be structured.

Proposed schema:

```text
ProviderConfig:
    provider_id
    provider_type

    endpoint
    authentication_reference

    enabled
    request_timeout

    model_definitions

    metadata
```

The exact fields depend on the provider.

Credentials must not be stored directly in ordinary configuration files when a secure credential mechanism is available.

Provider configuration must be validated before use.

---

# 30. API Credentials

Mikasa must use an approved secrets-management mechanism for provider credentials.

The system must not:

- Commit API keys to source control.
- Store API keys as ordinary agent memories.
- Include raw credentials in model prompts.
- Expose credentials in normal execution logs.
- Share all provider credentials with every specialist.
- Return credentials through model or tool responses.

A provider adapter may receive access to the credential required for its authorized operation.

The Model Router must not expose unrelated secrets to the selected model.

---

# 31. OpenAI-Compatible Endpoints

Mikasa should eventually support configurable OpenAI-compatible endpoints.

A compatible provider configuration may include:

```text
provider_type:
    openai_compatible

endpoint:
    configured_endpoint

model:
    configured_model

authentication:
    credential_reference
```

The actual endpoint and model identifiers must be provided through configuration.

Compatibility must be verified.

The system must not assume that every endpoint supports:

- Structured tool calling.
- Streaming.
- Vision.
- Audio.
- Identical token usage reporting.
- Identical generation parameters.
- The same response format across all features.

Provider-specific differences must be handled inside the adapter.

---

# 32. Local Models

Mikasa should eventually support local model execution through a compatible local inference provider.

The architecture should allow local models to be configured without modifying the Agent Runtime.

A local provider may expose:

- Model identifier.
- Endpoint or runtime reference.
- Supported capabilities.
- Context limits.
- Availability.
- Resource requirements.

The Model Router should distinguish a model being installed from the model being operational.

Local inference must not be represented as available if the configured runtime is not running or cannot load the selected model.

---

# 33. Local Model Constraints

Local models may have different hardware requirements and capabilities.

The routing system should eventually consider:

- Available memory.
- Model size.
- Supported context.
- Inference speed.
- Tool-calling reliability.
- Runtime compatibility.
- Multimodal support.

A model being small enough to load does not necessarily mean it can reliably complete autonomous coding or research tasks.

Model suitability must be evaluated against representative Mikasa tasks.

The initial architecture must not hard-code assumptions about the user's hardware.

---

# 34. Cloud Models

Cloud providers may offer capabilities unavailable or impractical in a particular local environment.

Mikasa should support cloud providers through approved adapters.

Cloud model access may require:

- Authentication.
- Network connectivity.
- Provider-specific request formatting.
- Rate-limit handling.
- Usage tracking.
- Billing controls.

The system must distinguish model subscription access from API access.

It must not assume that access to a provider's consumer application automatically includes free or unlimited API usage.

---

# 35. Model Availability

The Model Router should distinguish between configured and operational models.

Potential availability states:

```text
AVAILABLE

UNAVAILABLE

UNCONFIGURED

DEGRADED

DISABLED

RATE_LIMITED
```

The initial implementation may use a simpler status model.

Availability checks should not introduce excessive provider requests or unnecessary cost.

A successful configuration validation does not guarantee that every future request will succeed.

---

# 36. Provider Health

Future versions may monitor provider health.

Possible signals:

- Connection failures.
- Authentication failures.
- Request latency.
- Rate limits.
- Timeout frequency.
- Invalid responses.
- Model availability.

Health information may inform routing decisions.

However, temporary provider failure must not automatically cause Mikasa to modify its permanent model configuration.

---

# 37. Model Fallback

Mikasa should eventually support approved fallback models.

Example:

```text
PREFERRED MODEL
       |
       v
REQUEST FAILED
       |
       v
CLASSIFY FAILURE
       |
       v
CHECK FALLBACK POLICY
       |
       v
SELECT COMPATIBLE ALTERNATIVE
       |
       v
VALIDATE CAPABILITIES
       |
       v
RETRY REQUEST
```

Fallback must remain within the current task's resource and privacy constraints.

The router must not automatically switch from a local model to a cloud provider when the task's information is restricted to local processing.

---

# 38. Fallback Restrictions

A fallback model must satisfy:

- Required capabilities.
- Authorized provider policy.
- Data-handling restrictions.
- Resource limits.
- Cost limits.
- Context requirements.

The system must not switch providers simply because an alternative is available.

If no acceptable alternative exists, return an appropriate error.

The Agent Runtime may then decide whether the task can continue.

---

# 39. Safe Request Retries

Model request retries must be bounded.

Potential retryable errors:

- Temporary network interruptions.
- Transient provider errors.
- Selected rate-limit responses.
- Temporary provider unavailability.

Normally non-retryable errors:

- Invalid credentials.
- Unsupported model.
- Invalid request schema.
- Explicit provider rejection.
- Exhausted resource budget.

The router must not endlessly retry failed requests.

It must record sufficient information to distinguish retries from new task actions.

---

# 40. Retry and Tool Execution Separation

A model request retry is different from repeating a tool action.

If a provider connection fails after the model has produced an incomplete tool-call response, the runtime must not execute partial tool-call arguments.

If a tool has already executed, retrying the model request must not automatically repeat that tool.

The Agent Runtime remains responsible for tracking action identity and execution state.

Model fallback must not bypass the existing task or tool-execution contracts.

---

# 41. Provider Error Normalization

Provider-specific errors should be converted into common internal error categories.

Possible categories:

```text
AUTHENTICATION_ERROR

RATE_LIMIT_ERROR

CONNECTION_ERROR

TIMEOUT_ERROR

MODEL_UNAVAILABLE

UNSUPPORTED_CAPABILITY

INVALID_REQUEST

CONTEXT_LIMIT_EXCEEDED

PROVIDER_REJECTION

PROVIDER_INTERNAL_ERROR

UNKNOWN_PROVIDER_ERROR
```

Errors must preserve sufficient diagnostic information.

Sensitive credentials and unnecessary private request content must not be exposed through logs.

---

# 42. Usage Tracking

The Model Router should track relevant model usage.

Possible usage information:

```text
ModelUsage:
    request_id

    provider_id
    model_id

    input_tokens
    output_tokens

    total_tokens

    duration
    reported_cost
    metadata
```

Actual usage fields depend on the provider.

The system must distinguish:

- Provider-reported usage.
- Locally estimated usage.
- Unknown usage.

Do not fabricate token counts or financial costs when the provider does not supply them and no reliable estimation method exists.

---

# 43. Cost Management

Future versions should support configurable usage budgets.

Possible controls:

```text
MAXIMUM_TASK_COST

MAXIMUM_SESSION_COST

MAXIMUM_MODEL_REQUESTS

MAXIMUM_OUTPUT_TOKENS

MAXIMUM_TOTAL_TOKENS
```

The Model Router may reject requests that would violate an enforced budget.

When cost estimates are uncertain, the system must communicate that limitation.

Cost management must not silently select a model lacking required capabilities.

The initial MVP does not require a sophisticated pricing engine.

---

# 44. Resource-Aware Routing

Future model selection may consider resource usage.

Examples:

- Prefer an available local model for an appropriate lightweight task.
- Use a configured coding-capable model for a development workflow.
- Avoid unnecessary use of expensive models for simple classification.
- Select a compatible model when a task requires vision.

These are potential strategies.

They must be validated against actual task performance.

A cheaper or faster model must not automatically be considered suitable for every task.

---

# 45. Model Evaluation

Model selection should eventually be informed by repeatable evaluations.

Evaluation categories may include:

- General instruction following.
- Tool-call correctness.
- Structured-output validity.
- Coding task success.
- Research task success.
- Context handling.
- Response latency.
- Resource usage.
- Reliability.

Evaluations must specify:

- Model version.
- Provider configuration.
- Test environment.
- Task set.
- Scoring methodology.
- Date of evaluation.

The routing system must not treat a model's reputation or marketing description as equivalent to verified performance within Mikasa.

---

# 46. Model Configuration Profiles

Mikasa may eventually support user-defined model configurations.

Example:

```text
profiles:
    default:
        model: configured_general_model

    coding:
        model: configured_coding_model

    research:
        model: configured_research_model

    local:
        model: configured_local_model
```

This is an illustrative configuration format.

The actual syntax must be selected according to the project's approved configuration system.

A profile should describe a configured selection.

It should not require users to modify the Agent Runtime source code whenever they want to change models.

---

# 47. Runtime Model Switching

The user may eventually change Mikasa's active model through an approved interface.

Model switching should:

1. Validate the requested model.
2. Check availability.
3. Verify required capabilities.
4. Apply the appropriate configuration.
5. Report success or failure.

Changing the model for a new task is different from changing the model during an active execution.

Active execution switching must preserve task state, tool-call consistency, context compatibility, and resource limits.

The initial MVP may require model changes to occur between executions.

---

# 48. Model Routing and Memory

The Model Router must not independently retrieve unrestricted memory.

The Context Manager and Memory Service determine which information is relevant and authorized for a model request.

The Model Router receives the prepared request.

Different providers may have different data-handling requirements.

The routing policy must respect applicable restrictions on which data may be sent to which provider.

A provider fallback must not silently disclose information to an external service when the task was configured for local-only processing.

---

# 49. Model Routing and Permissions

Model access does not grant tool permissions.

A model may request an operation.

The Agent Runtime and Tool System determine whether that operation is supported and authorized.

The Model Router must not execute external actions merely because a provider response contains a tool request.

Provider-specific tool descriptions must not override the trusted Tool Registry.

---

# 50. Model Routing and Specialist Agents

Specialist agents may request models through assigned profiles.

The Agent Manager determines which model profiles or selections are permitted for a specialist.

The Model Router resolves those requests to available, compatible providers.

A specialist must not automatically receive access to every configured provider or credential.

The model configuration assigned to a specialist must remain within the parent task's resource and data-access constraints.

---

# 51. Observability

Model requests must produce useful operational records.

Potential events:

```text
model.requested

model.selected

model.started

model.completed

model.failed

model.timeout

model.fallback_selected

model.usage_recorded
```

Records may include:

- Request ID.
- Task ID.
- Execution ID.
- Provider ID.
- Model ID.
- Duration.
- Status.
- Error category.
- Usage information.

Logs must not unnecessarily contain full prompts, private memory contents, credentials, or sensitive tool results.

---

# 52. Configuration Security

The Model Router must respect the project's security architecture.

Provider configuration must not allow a model response, retrieved document, or external tool result to silently:

- Replace trusted provider endpoints.
- Retrieve API credentials.
- Change authorized model access.
- Disable usage limits.
- Modify privacy restrictions.
- Enable unrestricted network access.

Configuration changes must occur through trusted application mechanisms.

---

# 53. Research Requirements

Before finalizing model integration, research the relevant open-source systems.

## Gemini CLI

https://github.com/google-gemini/gemini-cli

Study:

- Model request handling.
- Provider integration.
- Streaming.
- Tool-call representation.
- Context management.
- Error handling.

## OpenClaw

https://github.com/openclaw/openclaw

Study:

- Model configuration.
- Provider abstraction.
- Model selection.
- Fallback behavior.
- Agent-specific model settings.

## AgenticSeek

https://github.com/Fosowl/agenticSeek

Study:

- Local model integration.
- Model configuration.
- Agent-specific model usage.
- Runtime requirements.

## DeerFlow

https://github.com/bytedance/deer-flow

Study:

- Model configuration.
- Model interaction in agent workflows.
- Specialist model assignment.
- Provider abstraction.

## ZCode

https://github.com/zai-org/ZCode

Study:

- Model interaction within coding workflows.
- Tool-calling compatibility.
- Context management.
- Provider integration.

Research findings must distinguish verified source-code behavior from documentation claims and architectural assumptions.

The final approach must be documented in the project's decision log.

---

# 54. MVP Model Router Scope

The first working version must support a small but functional model abstraction.

| Capability | MVP |
|---|---|
| Provider interface | Required |
| One functional provider adapter | Required |
| Configurable default model | Required |
| Normalized model requests | Required |
| Normalized model responses | Required |
| Tool-call compatibility with selected provider | Required |
| Provider error handling | Required |
| Request timeout | Required |
| Model availability validation | Required |
| Secure credential configuration | Required |
| Basic usage reporting where available | Required |
| Multiple provider adapters | Deferred |
| Automatic model selection | Deferred |
| Advanced fallback | Deferred |
| Cost-aware routing | Deferred |
| Local model auto-discovery | Deferred |
| Specialist-specific model routing | Deferred |
| Advanced multimodal routing | Deferred |

The MVP must not implement advanced routing merely because the architecture anticipates it.

---

# 55. MVP Acceptance Scenario

The initial Model Router must demonstrate a functional integration with the Agent Runtime.

**Scenario:**

A user asks Mikasa to inspect a small project and identify a problem.

Expected behavior:

```text
USER REQUEST
      |
      v
AGENT RUNTIME
      |
      v
NORMALIZED MODEL REQUEST
      |
      v
MODEL ROUTER
      |
      v
CONFIGURED PROVIDER
      |
      v
MODEL RESPONSE
      |
      v
NORMALIZED TOOL REQUEST
      |
      v
AGENT RUNTIME
      |
      v
TOOL EXECUTION
      |
      v
TOOL RESULT
      |
      v
FOLLOW-UP MODEL REQUEST
      |
      v
FINAL RESPONSE
```

The scenario must use an actual configured model provider and real registered tools.

Hard-coded model responses do not satisfy the integrated acceptance test.

The test must also verify that provider failures produce understandable errors rather than silently terminating the application.

---

# 56. Testing Requirements

The following tests must cover the relevant implemented capabilities.

## MODEL-TEST-001 — Provider Registration

Verify that a valid provider adapter can be configured and resolved.

## MODEL-TEST-002 — Default Model

Verify that the configured default model is selected.

## MODEL-TEST-003 — Request Normalization

Verify that internal model requests are correctly translated by the provider adapter.

## MODEL-TEST-004 — Response Normalization

Verify that provider responses are converted into the approved internal format.

## MODEL-TEST-005 — Tool Calling

Verify that a supported model can generate a valid tool request through the normalized interface.

## MODEL-TEST-006 — Unknown Model

Verify that requesting an unconfigured model produces an appropriate error.

## MODEL-TEST-007 — Unsupported Capability

Verify that a model lacking a required capability is rejected.

## MODEL-TEST-008 — Authentication Failure

Verify that invalid credentials produce a structured authentication error.

## MODEL-TEST-009 — Timeout

Verify that a provider timeout is handled according to the configured execution policy.

## MODEL-TEST-010 — Invalid Provider Response

Verify that malformed or unsupported responses do not trigger unsafe execution.

## MODEL-TEST-011 — Usage Reporting

Verify that provider-reported usage is preserved accurately and missing usage is not fabricated.

## MODEL-TEST-012 — Credential Protection

Verify that credentials are not exposed through ordinary logs, model requests, or tool results.

## MODEL-TEST-013 — Runtime Integration

Verify that the Agent Runtime can complete a representative task through the Model Router.

## MODEL-TEST-014 — Fallback

When fallback is implemented, verify that alternatives respect capability, privacy, permission, and resource constraints.

## MODEL-TEST-015 — Streaming

When streaming is implemented, verify that partial tool-call arguments are not executed before the complete request is validated.

---

# 57. Development Sequence

The Model Router should be implemented incrementally.

**MODEL-0 — Research**

Evaluate relevant model-provider implementations and select the initial approach.

**MODEL-1 — Contracts**

Define normalized request, response, provider, capability, and error contracts.

**MODEL-2 — Provider Adapter**

Implement one functional provider adapter.

**MODEL-3 — Configuration**

Add structured model and provider configuration.

**MODEL-4 — Runtime Integration**

Connect the Model Router to the Agent Runtime.

**MODEL-5 — Tool Calling**

Verify that model-generated tool calls are normalized and processed correctly by the runtime.

**MODEL-6 — Error Handling**

Implement provider error normalization, request timeouts, and availability checks.

**MODEL-7 — Evaluation**

Run integration tests using an actual configured provider.

Future milestones may introduce:

- Additional providers.
- Local models.
- Model profiles.
- Automatic selection.
- Fallback.
- Usage budgets.
- Specialist-specific routing.
- Multimodal support.

Each future capability requires an approved implementation task.

---

# 58. Architecture Decisions Required

The following decisions must be resolved before their respective implementation work begins:

```text
MODEL-ADR-001
Initial provider implementation.

MODEL-ADR-002
Normalized request and response contracts.

MODEL-ADR-003
Provider configuration format.

MODEL-ADR-004
Model capability schema.

MODEL-ADR-005
Tool-call normalization strategy.

MODEL-ADR-006
Credential storage and access.

MODEL-ADR-007
Request timeout and retry behavior.

MODEL-ADR-008
Usage accounting.

MODEL-ADR-009
Future model-selection policy.

MODEL-ADR-010
Provider fallback policy.

MODEL-ADR-011
Local inference integration.

MODEL-ADR-012
Streaming and multimodal contracts.
```

These identifiers are planning references rather than approved Architecture Decision Record numbers.

Approved decisions must be recorded in:

`docs/22_DECISION_LOG.md`

---

# 59. Definition of Done

The MVP Model Router is complete when:

- One real model provider is successfully integrated.
- Model access occurs through a common provider interface.
- The default model is configurable.
- Requests and responses use normalized internal contracts.
- The selected provider supports the tool-calling behavior required by the MVP.
- Tool calls are returned to the Agent Runtime rather than executed directly by the router.
- Invalid model configurations produce understandable errors.
- Provider timeouts are handled correctly.
- Credentials are protected.
- Relevant usage information is reported accurately.
- The Agent Runtime can execute a real multi-step task through the Model Router.
- The integrated acceptance scenario succeeds.
- Relevant automated tests pass.

Future routing features must not be represented as implemented until their corresponding requirements and tests are satisfied.

---

# 60. Final Model Router Principle

Mikasa's intelligence must not be permanently tied to one model.

Models are replaceable computational capabilities within the larger agent architecture.

```text
M I K A S A
     |
     v
UNDERSTAND TASK REQUIREMENTS
     |
     v
IDENTIFY REQUIRED MODEL CAPABILITIES
     |
     v
SELECT AUTHORIZED MODEL
     |
     v
EXECUTE MODEL REQUEST
     |
     v
NORMALIZE RESPONSE
     |
     v
RETURN TO AGENT RUNTIME
```

The Model Router must preserve four important distinctions:

```text
CONFIGURED IS NOT AVAILABLE.

AVAILABLE IS NOT COMPATIBLE.

COMPATIBLE IS NOT AUTHORIZED.

A MODEL RESPONSE IS NOT A VERIFIED TASK RESULT.
```

**Mikasa owns the agent architecture. Models provide intelligence within it. Replacing a model must not require rebuilding the assistant.**
