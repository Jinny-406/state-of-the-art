# M I K A S A
## Voice System Architecture Specification

**File:** `docs/15_VOICE_SYSTEM.md`

**Version:** 0.1.0

**Status:** PROPOSED — Pending architecture research and approval

**Authority:** Voice input, speech recognition, text-to-speech, activation, interruption, live conversation, voice task control, and voice-specific UX

**Applies to:** Voice interface, Main Agent, Agent Runtime, Application Gateway, Task Manager, Permission Service, Model Router, UI, and future desktop/mobile clients.

---

# 1. Purpose

This document defines the architecture and operational behavior of Mikasa's Voice System.

The Voice System should allow users to interact naturally with Mikasa through speech while preserving the same task, permission, memory, and execution architecture used by text interfaces.

The Voice System may eventually support:

- Speech input.
- Speech-to-text.
- Spoken responses.
- Text-to-speech.
- Push-to-talk.
- Wake-word activation.
- Voice interruption.
- Live conversational turn-taking.
- Spoken task progress.
- Voice task cancellation.
- Voice approval flows where appropriate.
- Multiple audio backends.
- Local and cloud speech providers.

The initial voice milestone should remain deliberately small.

---

# 2. Core Voice Principle

Voice must remain an interface.

```text id="wdpjfq"
VOICE INPUT
    |
    v
SPEECH RECOGNITION
    |
    v
APPLICATION GATEWAY
    |
    v
M I K A S A CORE
    |
    v
RESPONSE / TASK STATE
    |
    v
VOICE RESPONSE
```

The Voice System must not create:

- A separate memory system.
- A separate agent runtime.
- A separate permission model.
- A separate task manager.
- A separate personality engine.

Text and voice should reach the same underlying Mikasa.

---

# 3. Voice Architecture Overview

```text id="wt0bpg"
                   USER
                     |
                     v
               AUDIO INPUT
                     |
                     v
             VOICE CONTROLLER
                     |
          +----------+----------+
          |                     |
          v                     v
   ACTIVATION SYSTEM      AUDIO PROCESSING
                                |
                                v
                      SPEECH-TO-TEXT PROVIDER
                                |
                                v
                         TRANSCRIPT
                                |
                                v
                      APPLICATION GATEWAY
                                |
                                v
                         MIKASA CORE
                                |
                                v
                           RESPONSE
                                |
                                v
                       SPEECH PLANNER
                                |
                                v
                     TEXT-TO-SPEECH PROVIDER
                                |
                                v
                         AUDIO OUTPUT
```

These are logical responsibilities.

They do not require separate processes.

---

# 4. Voice Controller

The Voice Controller coordinates voice interaction.

Suggested component:

`VoiceController`

Responsibilities:

- Start and stop listening.
- Manage activation state.
- Capture audio.
- Submit audio for transcription.
- Receive transcripts.
- Send transcripts to the Application Gateway.
- Receive Mikasa responses.
- Decide what should be spoken.
- Coordinate speech output.
- Handle interruptions.
- Handle cancellation.
- Maintain voice-session state.

The Voice Controller must not implement agent reasoning.

---

# 5. Voice Interaction Modes

Mikasa should eventually support several modes.

## Push-to-Talk

The user explicitly starts voice input.

Example:

```text id="j4clmj"
PRESS
  |
  v
SPEAK
  |
  v
RELEASE
  |
  v
PROCESS
```

This is the simplest and safest initial mode.

---

## Tap-to-Toggle

The user taps once to begin listening and again to stop.

Useful for longer voice input.

---

## Wake-Word Mode

Future versions may continuously listen for a local activation phrase.

Example:

```text id="g56npt"
"Hey Mikasa"
```

Wake-word mode must be optional.

It has additional privacy, resource, and false-activation concerns.

---

## Live Conversation Mode

Future versions may support continuous conversational turn-taking.

This requires:

- Voice activity detection.
- Fast transcription.
- Streaming response.
- Interruption handling.
- Audio queue management.
- Low latency.

Live mode is not required for the first voice implementation.

---

# 6. Initial Voice Milestone

The first voice milestone should support:

```text id="u0uhzf"
PUSH-TO-TALK

SPEECH-TO-TEXT

TEXT REQUEST SUBMISSION

TEXT RESPONSE

TEXT-TO-SPEECH

STOP SPEAKING
```

Wake-word activation and full duplex conversation should remain future features.

This gives Mikasa useful voice interaction without prematurely building a complex real-time audio system.

---

# 7. Audio Input

The Voice System must capture audio from an authorized input device.

Potential metadata:

```text id="4vq0hx"
AudioInput:
    input_id

    device_id

    sample_rate
    channels
    format

    started_at
    completed_at
```

Exact representation depends on the selected audio framework.

The interface should clearly indicate when the microphone is active.

---

# 8. Microphone Permissions

Microphone access must use explicit operating-system and application permissions.

Mikasa must not attempt to bypass system privacy controls.

The user should be able to disable microphone access entirely.

When access is unavailable, the UI should explain that voice input cannot start.

---

# 9. Listening State

The interface must clearly distinguish:

```text id="jfdqx2"
IDLE

LISTENING

PROCESSING

SPEAKING

INTERRUPTED

ERROR
```

A microphone indicator should reflect actual capture state.

Do not display:

```text id="0dl2ep"
Listening...
```

when audio capture is inactive.

---

# 10. Voice Activity Detection

Future continuous modes may use Voice Activity Detection (VAD).

VAD can help determine:

- When speech starts.
- When speech ends.
- Whether silence should end a turn.

VAD must not be confused with speech recognition.

Its job is detecting likely speech activity.

The first push-to-talk version does not require sophisticated VAD.

---

# 11. Speech-to-Text

Speech-to-text converts captured audio into text.

Suggested abstraction:

```text id="acyey2"
SpeechToTextProvider:
    transcribe(audio, options)
        -> TranscriptionResult
```

Future optional operation:

```text id="dqhb9f"
stream_transcription(audio_stream)
```

The Voice Controller should depend on this interface rather than a specific speech provider.

---

# 12. Transcription Result

Conceptual structure:

```text id="qlroge"
TranscriptionResult:
    transcript_id

    text

    language

    confidence_metadata

    started_at
    completed_at

    provider_id
```

Confidence values must be treated carefully.

Provider confidence is not always comparable between systems.

---

# 13. Transcript Validation

The transcript becomes user input.

Before acting on it, Mikasa should account for transcription uncertainty.

Example:

Spoken:

```text id="3gcoph"
Delete the test file.
```

Potential incorrect transcription:

```text id="1bq35g"
Delete the text file.
```

For high-impact actions, the permission and approval system should protect against serious consequences even if speech recognition is imperfect.

Voice transcription must not bypass normal approval requirements.

---

# 14. Transcript Display

Where a screen is available, the recognized text should be visible.

Example:

```text id="ortvm7"
You said:

"Run the tests for this project."
```

This helps users catch transcription errors.

The user may be allowed to edit the transcript before submission in certain modes.

---

# 15. Language Support

The Voice System should eventually support configurable recognition languages.

Possible behavior:

```text id="fk57ci"
AUTO DETECT

SELECTED LANGUAGE
```

Language support depends on the chosen speech provider.

The system must not claim support for languages that the configured provider cannot recognize reliably.

---

# 16. Mixed-Language Speech

Future versions may encounter users mixing languages in one utterance.

The architecture should allow providers that support multilingual transcription.

No complex multilingual routing is required for the first milestone.

---

# 17. Transcript Submission

Once a transcript is accepted, it should be submitted through the normal application gateway.

```text id="uc1um0"
TRANSCRIPT
    |
    v
UserRequest
    |
    v
APPLICATION GATEWAY
    |
    v
MAIN AGENT / TASK SYSTEM
```

Voice-originated requests should have metadata such as:

```text id="hlpd22"
input_mode: voice
```

The core request semantics remain the same.

---

# 18. Voice and Tasks

A spoken command may create an ordinary Mikasa task.

Example:

```text id="r2dtsa"
User:
"Mikasa, inspect my project and fix the failing tests."
```

The backend may create:

```text id="99plse"
Task:
Fix failing tests.
```

The task must remain visible in normal task views.

Voice tasks are not a separate task type.

---

# 19. Spoken Responses

Not every assistant response needs to be read aloud in full.

The Voice System should distinguish:

```text id="8435d1"
DISPLAY RESPONSE

SPOKEN RESPONSE
```

For example, a detailed coding report may be displayed while Mikasa says:

```text id="ht9msz"
"I fixed the failing test and verified it. I changed one file. The details are on screen."
```

This avoids long, exhausting spoken output.

---

# 20. Speech Planner

A future `SpeechPlanner` may decide how much information should be spoken.

Responsibilities:

- Create concise spoken summaries.
- Preserve critical warnings.
- Preserve approval details.
- Avoid reading large tables or code blocks aloud.
- Refer users to the visual interface for detailed output.

The Speech Planner must not change the actual task result.

It only adapts presentation for audio.

---

# 21. Text-to-Speech

TTS should use a provider abstraction.

Conceptual interface:

```text id="pm0c9l"
TextToSpeechProvider:
    synthesize(text, voice_config)
        -> AudioResult
```

Future operation:

```text id="46dxde"
stream_speech(text_stream)
```

The Voice Controller should not depend permanently on one TTS provider.

---

# 22. TTS Configuration

Possible configuration:

```text id="ju6ksm"
VoiceConfig:
    voice_id
    language

    rate
    pitch

    volume

    provider_options
```

Not all providers support all parameters.

Unsupported settings must not be silently represented as active.

---

# 23. Voice Identity

Mikasa may eventually have a recognizable default voice.

The voice should be treated as presentation configuration, not system identity.

Changing the voice must not change:

- Memory.
- Agent behavior.
- Permissions.
- Task history.
- Personality rules.

---

# 24. Voice Selection

The user should eventually be able to select from configured voices.

Possible settings:

```text id="ph3jbe"
Default voice

Speech speed

Auto-speak responses

Voice language
```

Provider-specific identifiers should remain behind the voice configuration layer.

---

# 25. Speech Output Queue

Mikasa may generate new responses while previous speech is still playing.

The Voice Controller should maintain an output queue.

Potential operations:

```text id="mvzlri"
enqueue

play

pause

cancel

clear
```

Important new information may supersede stale speech.

Example:

A task fails while Mikasa is still reading an old progress update.

The old speech should be interruptible.

---

# 26. Barge-In / Interruption

The user should eventually be able to interrupt Mikasa while she is speaking.

Example:

```text id="5unw3p"
MIKASA:
"I've inspected the project and—"

USER:
"Stop."
```

Expected:

- Stop TTS quickly.
- Do not automatically cancel the underlying task unless the user's intent is task cancellation.
- Return to listening or idle state.

---

# 27. Speech Interruption vs. Task Cancellation

These are different.

```text id="osoywi"
"Stop talking."
```

means:

```text id="0s77tn"
STOP AUDIO OUTPUT
```

while:

```text id="cpcb6g"
"Stop the task."
```

means:

```text id="qdgj37"
REQUEST TASK CANCELLATION
```

Intent must be interpreted carefully.

When ambiguity affects a high-impact running task, the system may need confirmation.

---

# 28. Voice Task Cancellation

The user should be able to say:

```text id="7dwyyu"
Cancel the current task.
```

The Voice System sends a normal cancellation request to the Task Manager.

The cancellation must use the same backend logic as clicking the cancel button.

The Voice Controller must not locally pretend a task was cancelled.

---

# 29. Voice Pause / Resume

When pause/resume exists, voice commands may include:

```text id="647744"
Pause this task.

Resume the project task.
```

These commands should call the real task APIs.

They must not simulate pause by merely muting output.

---

# 30. Voice Approvals

Sensitive actions require special care in voice.

Example:

```text id="cvy5pa"
Mikasa:
"I need permission to modify files outside the current project. Do you want to allow that once?"
```

The user may say:

```text id="981ox0"
Yes.
```

The approval must be associated with the exact pending action.

A generic "yes" must not authorize unrelated requests.

---

# 31. Approval Context

The Voice Controller must know which approval request is active.

Conceptual structure:

```text id="tlm6zw"
VoiceApprovalContext:
    approval_id

    task_id

    requested_action

    requested_scope

    expires_at
```

Only the active valid approval should accept a spoken decision.

---

# 32. Ambiguous Approval Responses

Responses such as:

```text id="hqhfvg"
Maybe.

Okay, whatever.

Sure, but only for that file.
```

must be interpreted according to the actual requested scope.

If the response changes the scope, the Permission Service must receive the correct restricted approval.

A vague or uncertain transcription must not silently become broad authorization.

---

# 33. Dangerous Misrecognition

High-impact actions should not depend on one uncertain transcription.

For example:

```text id="ur27pg"
Delete the repository.
```

If the transcription or intent is uncertain, the system should require a stronger confirmation path according to security policy.

Voice does not lower security requirements.

---

# 34. Wake-Word Architecture

Future wake-word support should be local where practical.

Conceptually:

```text id="23te37"
MICROPHONE
   |
   v
LOCAL WAKE-WORD DETECTOR
   |
   v
ACTIVATION
   |
   v
CAPTURE USER COMMAND
```

Continuous raw microphone audio should not need to be sent to a cloud service merely to detect the wake phrase if a suitable local solution is available.

The final design depends on chosen providers and platforms.

---

# 35. Wake-Word Privacy

Wake-word mode must be optional.

Users should be able to see whether it is enabled.

The interface should communicate:

```text id="oer3or"
Wake word: On

Microphone standby: Active
```

or equivalent.

No hidden always-listening mode should be introduced.

---

# 36. Wake-Word False Activations

Wake-word detection can produce false positives.

After activation, Mikasa should only create a task when meaningful speech is captured.

Random background audio should not create autonomous tasks.

---

# 37. Wake-Word Timeout

After wake activation, the system should return to standby if no meaningful command arrives within a reasonable configured window.

Exact timing should be determined during UX testing.

---

# 38. Live Conversation

Future live conversation should support fluid turns.

Conceptually:

```text id="zi31bm"
USER SPEAKS
    |
    v
STREAMING STT
    |
    v
MIKASA PROCESSES
    |
    v
STREAMING RESPONSE
    |
    v
STREAMING TTS
    |
    v
USER INTERRUPTS
```

This requires significantly more complexity than push-to-talk.

It should be implemented only after basic voice interaction is reliable.

---

# 39. Full Duplex

Full-duplex audio allows Mikasa and the user to speak with minimal turn barriers.

Potential problems:

- Mikasa hearing her own voice.
- Echo.
- Cross-talk.
- Partial transcription.
- Accidental interruption.
- High latency.
- Increased compute.

Echo cancellation and audio routing may be required.

Full duplex is a later capability.

---

# 40. Half Duplex

Half-duplex is simpler:

```text id="vp7hxv"
USER SPEAKS
    |
    v
MIKASA LISTENS
    |
    v
MIKASA SPEAKS
    |
    v
USER SPEAKS
```

The first voice implementation should prefer this model.

---

# 41. Streaming STT

Future streaming transcription may provide partial text while the user speaks.

The system must distinguish:

```text id="z09x4f"
PARTIAL TRANSCRIPT
```

from:

```text id="q0v2um"
FINAL TRANSCRIPT
```

Partial transcripts must not trigger high-impact actions.

The Application Gateway should normally receive finalized user input.

---

# 42. Streaming TTS

Streaming TTS may reduce perceived latency.

The system should start speaking only when enough response content is stable.

Tool-call arguments, internal structured data, or incomplete code must not accidentally be spoken as final results.

---

# 43. Latency

Voice interaction is sensitive to delay.

Potential latency stages:

```text id="2y9vyj"
AUDIO CAPTURE

STT

MODEL RESPONSE

TOOL EXECUTION

TTS

PLAYBACK
```

Mikasa should optimize the user experience without hiding actual task state.

For long-running work, she may say:

```text id="2u5t8g"
"I've started checking the project. I'll show the task progress on screen."
```

rather than holding the voice interaction open indefinitely.

---

# 44. Voice Progress

Voice progress should be concise.

Useful:

```text id="xoq1dz"
"I'm running the tests now."
```

```text id="1cxc8v"
"I found the failure and I'm checking the relevant module."
```

Avoid narrating every tool call.

---

# 45. Background Tasks and Voice

Voice should be able to start long-running tasks.

Once the task is created, it belongs to the normal task system.

The Voice Controller does not need to remain active for the task to continue.

This is especially important for future persistent execution.

---

# 46. Voice Notifications

Future desktop or mobile clients may speak selected notifications.

Examples:

```text id="u16jlg"
"Your task is complete."

"Mikasa needs your approval."

"The project build failed."
```

Spoken notifications should be configurable.

They must not reveal private information unexpectedly in shared environments.

---

# 47. Privacy Mode

A future privacy setting may disable spoken content or sensitive spoken details.

Possible options:

```text id="qncnyx"
Voice responses:
On / Off

Speak notifications:
On / Off

Speak sensitive details:
Never / Ask / Allow
```

The exact design requires UX testing.

---

# 48. Headphone Awareness

Future platform integrations may use output-device information.

For example, detailed spoken information may be more acceptable through headphones than a loudspeaker.

This is optional and platform-dependent.

No special behavior is required for MVP.

---

# 49. Speech History

The user may want a record of voice interactions.

The system should store the resulting transcript according to normal conversation-history rules.

Raw audio should not automatically be retained permanently.

---

# 50. Raw Audio Retention

Raw voice recordings should use a deliberate retention policy.

Possible modes:

```text id="5du5er"
DO NOT RETAIN

TEMPORARY UNTIL TRANSCRIPTION

USER-REQUESTED RETENTION
```

The default should minimize unnecessary audio storage.

Exact policy must be finalized before production voice deployment.

---

# 51. Transcript Retention

Final transcripts may be stored as normal conversation messages.

This must remain distinct from persistent memory.

A spoken sentence does not automatically become long-term user memory.

Memory creation still follows:

`docs/05_MEMORY_ARCHITECTURE.md`

---

# 52. Speech Errors

Possible voice errors include:

```text id="unij5w"
MICROPHONE_UNAVAILABLE

MICROPHONE_PERMISSION_DENIED

NO_SPEECH_DETECTED

TRANSCRIPTION_FAILED

TTS_FAILED

AUDIO_DEVICE_ERROR

PROVIDER_UNAVAILABLE

TIMEOUT
```

Errors should be normalized.

The user should receive understandable feedback.

---

# 53. No-Speech Detection

If the user activates voice input but says nothing, Mikasa should not create an empty task.

The interface may simply return to idle.

---

# 54. Low-Confidence Transcript

When transcription quality is poor, the system may:

- Show the transcript for confirmation.
- Ask the user to repeat.
- Proceed if the task is low impact and clear enough.

High-impact actions should use stronger confirmation.

---

# 55. Voice Provider Abstraction

Speech providers must be replaceable.

Possible abstractions:

```text id="3f6xw0"
SpeechToTextProvider

TextToSpeechProvider

WakeWordProvider

VoiceActivityDetector
```

These may come from different vendors or local libraries.

The Voice Controller should remain provider-independent.

---

# 56. Local Voice Providers

Mikasa should eventually support local STT and TTS where practical.

Benefits may include:

- Privacy.
- Offline operation.
- Reduced provider cost.
- Lower dependency on network connectivity.

Trade-offs may include:

- Hardware load.
- Installation complexity.
- Model size.
- Accuracy.
- Latency.

Local provider selection must be based on actual evaluation.

---

# 57. Cloud Voice Providers

Cloud providers may offer:

- Better speech recognition.
- More natural voices.
- Language coverage.
- Streaming features.

They also introduce:

- Network dependency.
- Credential requirements.
- Data-handling concerns.
- Usage cost.

The same provider abstraction should support both local and cloud services.

---

# 58. Voice and Model Router

The Voice System should not directly select the language model.

After transcription:

```text id="ydo1py"
VOICE
   |
   v
TEXT
   |
   v
APPLICATION GATEWAY
   |
   v
AGENT RUNTIME
   |
   v
MODEL ROUTER
```

Voice and text requests therefore share the same model-routing architecture.

---

# 59. Voice and Memory

Voice-originated requests should use the same Memory Service as text.

The system must not maintain:

```text id="ghpq51"
voice memory
```

and:

```text id="w41dbx"
text memory
```

as separate user histories unless there is an explicit product requirement.

---

# 60. Voice and Permissions

Voice commands must use the same permission system.

Saying:

```text id="5w8y6q"
"Delete that folder."
```

does not bypass file-delete permissions.

Saying:

```text id="9gjmda"
"Just do whatever you need."
```

does not grant unrestricted machine control unless an explicit trusted authorization flow defines that scope.

---

# 61. Voice and Specialists

The user should not need to address specialists by voice during ordinary use.

Example:

```text id="e99nd6"
"Mikasa, research this and fix the project."
```

Mikasa may internally delegate.

The spoken interaction remains with Mikasa.

---

# 62. Voice Personality

Voice presentation may eventually influence:

- Warmth.
- Speaking pace.
- Conciseness.
- Formality.
- Pauses.

These are presentation characteristics.

They must remain separate from permission and runtime architecture.

---

# 63. Spoken Content Style

Spoken responses should generally be shorter than written responses.

Voice should prioritize:

- Outcome.
- Important status.
- Required decisions.
- Critical warnings.

Detailed code, tables, and logs should remain primarily visual.

---

# 64. Code by Voice

Voice may be useful for coding commands:

```text id="bm1bj1"
"Mikasa, run the tests."

"Open the task result."

"Cancel the current build."
```

Long code dictation is possible but is not a priority feature.

The interface may show the transcribed command before execution.

---

# 65. Voice Corrections

The user should be able to correct misunderstood speech.

Example:

```text id="qyrc5d"
User:
"No, I said the auth file, not the app file."
```

Mikasa should treat the correction as new user input and update the task context appropriately.

---

# 66. Device Routing

Future versions may support selecting:

- Input microphone.
- Output speaker.
- Bluetooth headset.
- Default system device.

Device selection should remain an interface concern.

The core agent runtime should not care which microphone is being used.

---

# 67. Mobile Voice

A mobile client may eventually provide a strong voice-first interface.

Potential capabilities:

- Push-to-talk.
- Task creation.
- Notifications.
- Quick approvals.
- Spoken summaries.

Mobile-specific integrations must still use the same backend contracts.

---

# 68. Desktop Voice

Desktop voice may support:

- Keyboard shortcut activation.
- Push-to-talk.
- Wake word.
- Desktop overlay.
- Spoken task updates.

The desktop app should not require voice to function.

Voice must remain optional.

---

# 69. Voice Overlay

A lightweight future overlay may show:

```text id="r7kw5m"
M I K A S A

● Listening...

"Run the tests for Mikasa"
```

or:

```text id="6g300f"
M I K A S A

Speaking...
```

The overlay should be dismissible.

---

# 70. Visual Feedback

Even in voice-first use, visual feedback is useful.

The UI may display:

- Live microphone state.
- Transcript.
- Task status.
- Approval prompts.
- Spoken-response text.

Voice and visual feedback should stay synchronized.

---

# 71. Audio Feedback

Small audio cues may indicate:

- Activation.
- Listening stopped.
- Error.
- Approval required.

Cues should be subtle and configurable.

Avoid excessive UI sounds.

---

# 72. Voice Security Events

Potential operational events:

```text id="t9qgo7"
voice.listening_started

voice.listening_stopped

voice.transcription_started

voice.transcription_completed

voice.transcription_failed

voice.speech_started

voice.speech_interrupted

voice.speech_completed

voice.approval_received
```

These should not contain unnecessary raw audio.

---

# 73. Voice Observability

Developer mode may show:

```text id="ossgvj"
STT provider

STT latency

Detected language

Transcript

TTS provider

TTS latency

Voice state
```

Sensitive spoken content should follow the same logging privacy rules as text.

---

# 74. Voice Resource Budgets

Future voice sessions may use:

- Audio processing.
- STT model inference.
- TTS generation.
- Network bandwidth.

The system may eventually support limits on:

- Recording duration.
- Transcription duration.
- Audio output length.
- Provider usage.

MVP only needs reasonable practical limits and timeouts.

---

# 75. Voice Timeouts

Voice operations should support timeouts.

Examples:

```text id="0uk7q4"
maximum_recording_length

stt_timeout

tts_timeout
```

Failure to transcribe must not leave the system permanently stuck in processing state.

---

# 76. Offline Voice Mode

Future local voice support may allow:

```text id="5uxd5q"
LOCAL STT

LOCAL MIKASA MODEL

LOCAL TTS
```

This could provide fully local conversation for supported tasks.

Offline capability depends on model availability and hardware.

The architecture should support it without requiring it for MVP.

---

# 77. Voice Research Requirements

Before implementation, research appropriate voice technologies for:

- Speech recognition.
- Local STT.
- Cloud STT.
- Text-to-speech.
- Local TTS.
- Streaming audio.
- Wake-word detection.
- Voice activity detection.
- Cross-platform audio capture.

Selection should consider:

- Latency.
- Accuracy.
- Language support.
- Resource requirements.
- Privacy.
- Licensing.
- Platform compatibility.
- Maintenance.

No provider should be selected solely because it is popular.

---

# 78. MVP Voice Scope

The first voice milestone should support:

| Capability | First voice milestone |
|---|---|
| Microphone input | Required |
| Push-to-talk | Required |
| Speech-to-text | Required |
| Transcript display | Required |
| Submit transcript to Mikasa | Required |
| Text-to-speech | Required |
| Stop speech | Required |
| Voice errors | Required |
| Voice task creation | Required |
| Voice task cancellation | Required |
| Wake word | Deferred |
| Streaming STT | Deferred |
| Streaming TTS | Deferred |
| Full duplex | Deferred |
| Continuous conversation | Deferred |
| Multi-device voice | Deferred |
| Speaker identification | Deferred |
| Automatic language switching | Deferred |

---

# 79. MVP Acceptance Scenario

The first voice implementation must support a real interaction.

## Step 1

User activates push-to-talk.

UI shows:

```text id="lrzd26"
Listening...
```

## Step 2

User says:

```text id="33bhq2"
"Mikasa, run the tests for this project."
```

## Step 3

Speech is transcribed.

The transcript appears on screen.

## Step 4

The transcript is submitted through the normal Application Gateway.

## Step 5

Mikasa creates or continues the appropriate task.

## Step 6

The task executes through the normal Agent Runtime.

## Step 7

Mikasa returns a result.

## Step 8

A concise version of the result is spoken.

Example:

```text id="99802c"
"The tests finished. One test failed. I've shown the details on screen."
```

This must use real STT, backend task state, and TTS.

Hard-coded transcripts or prerecorded responses do not satisfy the acceptance scenario.

---

# 80. Cancellation Acceptance Scenario

While Mikasa is speaking:

User says or activates:

```text id="9grtsk"
"Stop talking."
```

Expected:

- Audio output stops.
- The task remains unchanged.

Then:

```text id="gqcc8x"
"Cancel the current task."
```

Expected:

- Real Task Manager cancellation is requested.
- Mikasa reports the actual cancellation result.

The two commands must not be treated as equivalent.

---

# 81. Approval Acceptance Scenario

A task reaches an approval-gated action.

Mikasa says:

```text id="xxfglw"
"I need permission to modify this file. Allow this once?"
```

The user responds:

```text id="jkn7ve"
"Yes."
```

Expected:

- The response is associated with the active approval.
- Only the requested scope is granted.
- The task continues.

A later unrelated action must not reuse this approval automatically.

---

# 82. Testing Requirements

## VOICE-TEST-001 — Microphone Permission

Verify voice input reports an understandable failure when microphone permission is denied.

## VOICE-TEST-002 — Audio Capture

Verify push-to-talk captures actual microphone audio.

## VOICE-TEST-003 — Transcription

Verify captured speech produces a transcript through the configured STT provider.

## VOICE-TEST-004 — Transcript Submission

Verify transcript requests use the normal Application Gateway.

## VOICE-TEST-005 — Task Creation

Verify a spoken task request creates a real backend task.

## VOICE-TEST-006 — TTS

Verify actual Mikasa responses can be synthesized and played.

## VOICE-TEST-007 — Stop Speech

Verify output playback can be interrupted.

## VOICE-TEST-008 — Task Cancellation

Verify spoken task cancellation affects the actual Task Manager state.

## VOICE-TEST-009 — Approval

Verify spoken approval applies only to the active approval request.

## VOICE-TEST-010 — STT Failure

Verify transcription failure does not create a fabricated transcript.

## VOICE-TEST-011 — TTS Failure

Verify task completion remains available as text if speech synthesis fails.

## VOICE-TEST-012 — Transcript Error Safety

Verify uncertain speech does not bypass high-impact action confirmation.

## VOICE-TEST-013 — Shared Core

Verify equivalent voice and text requests use the same runtime, memory, task, and permission systems.

---

# 83. Development Sequence

**VOICE-0 — Research**

Evaluate STT, TTS, audio, and wake-word options.

**VOICE-1 — Audio Input**

Implement microphone capture and push-to-talk.

**VOICE-2 — STT Contract**

Define the provider abstraction and normalized transcription result.

**VOICE-3 — STT Integration**

Integrate one functioning speech-to-text provider.

**VOICE-4 — Gateway Integration**

Submit transcripts as normal user requests.

**VOICE-5 — TTS Contract**

Define TTS provider abstraction.

**VOICE-6 — TTS Integration**

Speak concise Mikasa responses.

**VOICE-7 — Interruption**

Add speech cancellation.

**VOICE-8 — Task Controls**

Support voice cancellation and approval.

**VOICE-9 — Evaluation**

Run end-to-end voice scenarios.

Future phases may add wake word, streaming, full duplex, offline voice, and advanced multimodal interaction.

---

# 84. Architecture Decisions Required

The following decisions must be resolved before their implementation:

```text id="8ungkd"
VOICE-ADR-001
Cross-platform audio library.

VOICE-ADR-002
Initial STT provider.

VOICE-ADR-003
Initial TTS provider.

VOICE-ADR-004
Audio format and sample-rate strategy.

VOICE-ADR-005
Transcript retention policy.

VOICE-ADR-006
Raw audio retention policy.

VOICE-ADR-007
Spoken-response summarization strategy.

VOICE-ADR-008
Voice approval UX.

VOICE-ADR-009
Future wake-word technology.

VOICE-ADR-010
Future VAD architecture.

VOICE-ADR-011
Future streaming architecture.

VOICE-ADR-012
Future local/offline voice stack.
```

These are planning identifiers.

Approved decisions belong in:

`docs/22_DECISION_LOG.md`

---

# 85. Definition of Done

The first Voice System milestone is complete when:

- Microphone input works through an approved interface.
- Push-to-talk works reliably.
- Real audio can be transcribed.
- The recognized transcript is visible.
- Voice requests use the same Application Gateway as text requests.
- Spoken requests can create and control real tasks.
- Mikasa responses can be synthesized through an actual TTS provider.
- Speech output can be stopped.
- Voice task cancellation affects the real Task Manager.
- Voice approvals use the normal Permission Service.
- Voice failures fall back to understandable text UI.
- Raw credentials are never exposed to speech providers unnecessarily.
- Voice interaction does not create a separate memory or task architecture.
- The acceptance scenarios succeed.
- Relevant automated tests pass.

---

# 86. Final Voice Principle

Voice should make Mikasa easier to access, not architecturally different.

The system must preserve these distinctions:

```text id="ih89iv"
SPEECH
    !=
AUTHORITY

TRANSCRIPTION
    !=
PERFECT INTENT

STOP SPEAKING
    !=
CANCEL TASK

VOICE INTERFACE
    !=
SEPARATE ASSISTANT

WAKE WORD
    !=
PERMISSION TO ACT
```

The core flow remains:

```text id="i9nb7b"
LISTEN
   |
   v
TRANSCRIBE
   |
   v
UNDERSTAND
   |
   v
ACT THROUGH MIKASA CORE
   |
   v
VERIFY
   |
   v
RESPOND
   |
   v
SPEAK WHEN USEFUL
```

**One Mikasa. Multiple ways to communicate with her.**