# M I K A S A
## Real-Time Voice Runtime Architecture

**File:** `docs/15_VOICE_SYSTEM.md`

**Version:** 0.2.0

**Status:** PROPOSED — redesigned for local-first, real-time conversational voice

**Authority:** Audio input/output, streaming speech recognition, conversational turn-taking, interruption, expressive speech generation, local TTS routing, voice identity, and voice-specific interaction behavior

**Applies to:** Application Gateway, Main Agent, Task Manager, Model Router, Permission Service, desktop/mobile interfaces, local inference providers, and future multimodal systems.

---

# 1. Purpose

Mikasa's Voice System must not behave like a traditional:

```text
record
→ transcribe
→ wait
→ generate
→ synthesize entire WAV
→ play
```

voice assistant.

The target is a real-time conversational system where Mikasa can:

- Hear the user naturally.
- Detect when the user starts and stops speaking.
- Transcribe speech continuously.
- Understand partial and completed turns.
- Begin preparing responses with low latency.
- Speak before an entire response has been synthesized.
- Stream audio progressively.
- Continue listening while speaking.
- Be interrupted immediately.
- Distinguish interruption of speech from cancellation of a task.
- Change speaking style based on conversational state.
- Preserve one consistent Mikasa voice across different TTS engines.
- Operate locally wherever practical.
- Adapt to available hardware.
- Fall back gracefully when advanced voice capabilities are unavailable.

Voice remains an interface to the same Mikasa core.

It is not a second assistant.

---

# 2. Core Voice Principle

The architecture must preserve:

```text
VOICE INPUT
    !=
SEPARATE AGENT
```

Both text and voice reach:

```text
APPLICATION GATEWAY

↓

MAIN AGENT

↓

TASK MANAGER

↓

AGENT RUNTIME

↓

TOOLS / MEMORY / MODELS
```

Voice adds:

```text
REAL-TIME AUDIO INTERACTION
```

around the existing core.

---

# 3. Target Experience

The intended user experience is closer to a natural human conversation.

Example:

```text
MIKASA:
"I checked the runtime and I think the issue is—"

USER:
"Wait. Did you check the config first?"

MIKASA:
[stops speaking immediately]

"Yeah. Let me check that before I continue."
```

The previous task may continue running.

Stopping Mikasa's speech must not automatically cancel the task.

---

# 4. Voice Design Goals

The system should optimize for:

```text
LOW LATENCY

NATURAL TURN-TAKING

INTERRUPTIBILITY

LOCAL EXECUTION

EXPRESSIVE HUMAN SPEECH

CONSISTENT VOICE IDENTITY

PRIVACY

MODULAR PROVIDERS

GRACEFUL DEGRADATION

RESOURCE AWARENESS
```

No single provider should be required to implement every capability.

---

# 5. Non-Goals

The first implementation does not require:

```text
perfect human indistinguishability

always-on wake word

speaker identity authentication

full emotional simulation

3D avatar lip synchronization

perfect full-duplex conversation

support for every language

automatic cloud fallback
```

The architecture must allow these later without depending on them now.

---

# 6. High-Level Architecture

```text
                         M I K A S A

                            CORE
                             ▲
                             │
                    APPLICATION GATEWAY
                             ▲
                             │
                      TURN MANAGER
                    ┌────────┴────────┐
                    │                 │
              USER SPEECH        MIKASA SPEECH
                    │                 │
                    │                 ▼
                    │          SPEECH DIRECTOR
                    │                 │
                    │                 ▼
                    │           LOCAL TTS ROUTER
                    │                 │
                    │                 ▼
                    │        STREAMING AUDIO PLAYER
                    │                 │
                    │                 ▼
                    │              SPEAKER
                    │
                    ▼
              AUDIO FRONT-END
                    │
          ┌─────────┼──────────┐
          │         │          │
          ▼         ▼          ▼
         VAD       AEC      DENOISING
          │
          ▼
      STREAMING STT
          │
          ▼
    PARTIAL / FINAL TEXT
```

A separate interruption path operates continuously:

```text
MICROPHONE
   │
   ▼
VAD
   │
   ├── HUMAN SPEECH DETECTED
   │
   ▼
BARGE-IN CONTROLLER
   │
   ├── duck current TTS
   ├── confirm speech
   ├── cancel synthesis
   ├── flush queued audio
   └── return control to user turn
```

---

# 7. Major Components

The Voice Runtime consists of these logical systems:

```text
AudioFrontEnd

VoiceActivityDetector

EchoCancellationLayer

StreamingSpeechRecognizer

TurnManager

BargeInController

SpeechDirector

LocalTTSRouter

VoiceIdentityManager

StreamingAudioPlayer

VoiceSessionManager
```

These do not necessarily need separate processes.

They represent clear responsibilities.

---

# 8. Audio Front-End

`AudioFrontEnd` owns raw microphone processing.

Responsibilities:

- Capture microphone audio.
- Normalize sample format.
- Resample when necessary.
- Apply noise suppression.
- Feed VAD.
- Feed acoustic echo cancellation.
- Feed STT.
- Maintain timing information.
- Expose microphone state.

Conceptually:

```text
Microphone
   ↓
Audio Capture
   ↓
Resample
   ↓
Noise Suppression
   ↓
Echo Cancellation
   ↓
VAD
   ↓
STT
```

---

# 9. Audio Format

The internal audio format should be normalized.

A likely representation may use:

```text
PCM

mono

16-bit or float PCM

fixed internal sample rate
```

The final format is an ADR.

Providers may internally require different formats.

Adapters should convert at the provider boundary.

---

# 10. Voice Activity Detection

VAD detects probable human speech.

It does not perform transcription.

Responsibilities:

```text
speech_started

speech_continues

speech_ended

silence

possible_interruption
```

VAD is critical for:

- End-of-turn detection.
- Barge-in.
- Hands-free sessions.
- Avoiding unnecessary STT work.

---

# 11. Acoustic Echo Cancellation

Mikasa must eventually be able to listen while she speaks.

Without acoustic echo cancellation:

```text
MIKASA SPEAKS
      ↓
SPEAKER
      ↓
MICROPHONE HEARS MIKASA
      ↓
STT THINKS MIKASA IS USER
```

This is unacceptable.

The audio player should expose a reference stream:

```text
TTS OUTPUT
   │
   ├────→ SPEAKER
   │
   └────→ AEC REFERENCE
```

AEC uses this signal to suppress Mikasa's own speech from microphone input.

---

# 12. Self-Speech Suppression

Echo cancellation may not be perfect.

The system should also use:

- Known playback timing.
- Outgoing speech transcript.
- Audio correlation where useful.
- VAD confidence.
- Microphone/speaker state.

This provides layered protection against Mikasa responding to herself.

---

# 13. Speech Recognition Architecture

Speech recognition should be streaming-first.

Conceptual interface:

```text
StreamingSTTProvider:
    start_session()

    push_audio(chunk)

    receive_partial()

    receive_final()

    cancel()

    close()
```

Traditional one-shot transcription may remain as a fallback.

---

# 14. Partial Transcripts

Streaming STT may produce:

```text
"can you che—"

"can you check the—"

"can you check the config"

"can you check the config first?"
```

These are:

```text
PARTIAL
```

until finalized.

Partial transcripts are useful for:

- UI feedback.
- Turn prediction.
- Latency reduction.

They are not automatically executable commands.

---

# 15. Final Transcripts

A final transcript represents a completed user turn.

Only finalized text should normally become a canonical `UserRequest`.

Exception:

Future advanced conversational modes may begin speculative processing from partial text.

Speculative work must not create irreversible actions before finalization.

---

# 16. Endpointing

The system must determine when the user has finished speaking.

Signals may include:

- VAD silence.
- STT endpoint prediction.
- Punctuation/prosody.
- Maximum silence timer.
- Explicit push-to-talk release.

Good endpointing is essential.

Too early:

```text
user gets cut off
```

Too late:

```text
conversation feels sluggish
```

Endpoint behavior should eventually adapt to speaking style.

---

# 17. Turn Manager

`TurnManager` is the central coordinator of conversational timing.

It does not perform language reasoning.

It coordinates:

```text
WHO CURRENTLY HAS THE FLOOR?
```

Possible states:

```text
IDLE

USER_STARTING

USER_SPEAKING

USER_ENDING

TRANSCRIBING

MIKASA_PROCESSING

MIKASA_STARTING

MIKASA_SPEAKING

INTERRUPTING

RECOVERING

MUTED

ERROR
```

These states belong to voice interaction.

They must not replace task states.

---

# 18. Turn Ownership

At any moment, the system should know:

```text
USER OWNS TURN

MIKASA OWNS TURN

TURN TRANSITION

NO ACTIVE TURN
```

This allows correct behavior during interruption.

---

# 19. Full Duplex Target

The eventual architecture should support:

```text
MICROPHONE ACTIVE
+
MIKASA AUDIO PLAYING
```

simultaneously.

This is full-duplex interaction.

However, the system may initially operate in:

```text
SMART HALF-DUPLEX
```

while retaining the same architecture.

---

# 20. Smart Half-Duplex

Initial implementation may use:

```text
USER SPEAKS

↓

MIKASA PROCESSES

↓

MIKASA SPEAKS

↓

USER INTERRUPTS IF NEEDED
```

The microphone remains available for barge-in detection.

This gives much of the natural experience without requiring perfect full-duplex audio immediately.

---

# 21. Barge-In

Barge-in means the user talks while Mikasa is speaking.

The system should react rapidly.

Expected flow:

```text
USER STARTS SPEAKING
      |
      v
VAD TRIGGER
      |
      v
DUCK AUDIO
      |
      v
CONFIRM HUMAN SPEECH
      |
      v
STOP CURRENT PLAYBACK
      |
      v
CANCEL TTS GENERATION
      |
      v
CLEAR STALE SPEECH QUEUE
      |
      v
LISTEN TO USER
```

---

# 22. Audio Ducking

The player should reduce Mikasa's volume immediately when potential user speech begins.

This gives the user conversational control before final interruption confirmation.

Example:

```text
100% volume

↓

potential interruption

↓

15–30% volume

↓

confirmed human speech

↓

0%
```

Exact values require UX testing.

---

# 23. False Barge-In Protection

Random noise should not constantly interrupt Mikasa.

Potential confirmation signals:

```text
VAD confidence

speech duration

AEC-cleaned signal

STT activity

minimum speech window
```

The goal is fast but not overly sensitive interruption.

---

# 24. Interruption Semantics

The system must distinguish at least:

```text
STOP_SPEAKING

INTERRUPT_RESPONSE

CANCEL_TASK
```

Example:

```text
"stop talking"
```

means:

```text
STOP_SPEAKING
```

not:

```text
CANCEL_TASK
```

Example:

```text
"cancel the build task"
```

means a real task cancellation request.

---

# 25. Cancellation Tokens

Streaming generation systems should support cancellation.

Potential chain:

```text
BargeInController
      |
      ├── cancel AudioPlayer
      ├── cancel TTS generation
      └── optionally cancel response generation
```

Whether the LLM generation is cancelled depends on interaction policy.

The underlying long-running task normally remains active.

---

# 26. Speech Queue

The `StreamingAudioPlayer` maintains a speech queue.

Possible operations:

```text
enqueue

play

duck

pause

resume

flush

cancel_current
```

Old progress messages must be discardable.

Example:

If task state changes from:

```text
RUNNING
```

to:

```text
FAILED
```

before an old progress message plays, the old progress message may be dropped.

---

# 27. Streaming TTS

TTS should produce audio incrementally.

Preferred:

```text
TEXT SEGMENT
   ↓
TTS
   ↓
AUDIO CHUNK 1 → PLAY
AUDIO CHUNK 2 → PLAY
AUDIO CHUNK 3 → PLAY
...
```

Avoid waiting for a complete response WAV.

This improves:

- First-audio latency.
- Interruptibility.
- Memory usage.
- Conversational feel.

---

# 28. Local TTS Router

Mikasa must not hard-code a single TTS engine.

Suggested component:

```text
LocalTTSRouter
```

Responsibilities:

- Register local TTS engines.
- Query capabilities.
- Check hardware requirements.
- Select appropriate engine.
- Create streaming synthesis session.
- Normalize output.
- Apply fallback policy.

---

# 29. TTS Provider Contract

Conceptual contract:

```text
TTSProvider:
    provider_id

    get_capabilities()

    check_availability()

    load_voice(identity)

    start_stream(request)

    cancel(stream_id)

    unload()
```

Streaming API may yield:

```text
AudioChunk
```

objects.

---

# 30. TTS Capability Metadata

Providers should advertise capabilities.

Examples:

```text
STREAMING

VOICE_CLONING

VOICE_DESIGN

EMOTION_CONTROL

STYLE_CONTROL

SPEED_CONTROL

PITCH_CONTROL

VOCAL_EVENTS

MULTILINGUAL

CPU_SUPPORTED

GPU_SUPPORTED

LOW_LATENCY

OFFLINE
```

Routing depends on real supported features.

---

# 31. Candidate TTS Providers

Current research candidates include:

```text
Pocket TTS

Breeze TTS 2

Whisper-Chan adapter

future local engines
```

These are candidates.

They are not yet architecture commitments.

---

# 32. Pocket TTS Role

Pocket TTS is a strong candidate for:

```text
REALTIME_LOCAL
LOW_RESOURCE
CPU_FIRST
```

profiles.

Reasons to investigate further:

- Small model.
- Streaming.
- Local execution.
- Voice cloning.
- Low first-audio latency.
- CPU-oriented design.

It should be evaluated on the actual target machines.

---

# 33. Breeze TTS 2 Role

Breeze TTS 2 is a candidate for:

```text
EXPRESSIVE_HIGH_QUALITY
```

profiles.

Interesting capabilities include:

- Voice design.
- Voice cloning.
- Delivery direction.
- Emotional control.
- Vocal events.
- Streaming.

Its larger hardware requirements make it unsuitable as the only voice backend.

Licensing must also be evaluated before production use.

---

# 34. Whisper-Chan Role

`Jinny-406/whisper-chan` should be researched as a separate candidate or source of reusable voice-runtime ideas.

Do not assume its capabilities until the repository is inspected.

Possible outcomes:

```text
TTS provider

STT provider

full voice runtime

audio utility layer

research-only reference
```

---

# 35. TTS Profiles

The router should eventually support profiles.

Example:

```text
REALTIME

EXPRESSIVE

LOW_RESOURCE

OFFLINE

BATTERY_SAVER

HIGH_QUALITY
```

Profiles represent intent.

They do not directly name providers.

---

# 36. Example Provider Routing

Conceptually:

```text
REALTIME
    ↓
Pocket TTS if available

EXPRESSIVE
    ↓
Breeze TTS if compatible

LOW_RESOURCE
    ↓
lowest-cost local engine

OFFLINE
    ↓
local engines only
```

Routing logic must remain evidence-based.

---

# 37. No Silent Cloud Fallback

If voice mode is configured:

```text
LOCAL_ONLY
```

Mikasa must never silently send:

```text
audio

transcript

response text
```

to a remote speech provider.

Any cloud fallback must require explicit configuration.

---

# 38. Hardware Awareness

TTS providers may have very different requirements.

The router should inspect:

```text
CPU

RAM

GPU

VRAM

OS

available provider runtime
```

through approved hardware APIs.

No user-specific hardware model should be hard-coded.

---

# 39. Provider Availability

A provider may be:

```text
NOT_INSTALLED

INSTALLED

LOADING

AVAILABLE

DEGRADED

INCOMPATIBLE

FAILED
```

Voice UI should represent actual state.

---

# 40. Voice Identity

Mikasa should have a provider-independent voice identity.

Suggested concept:

```text
VoiceIdentity:
    identity_id
    name

    persona_description

    reference_voice

    voice_embedding_refs

    supported_languages

    delivery_defaults

    provider_profiles
```

Example identity:

```text
identity_id:
mikasa_default

presentation:
young feminine voice

character:
warm
confident
intelligent
natural
calm

delivery:
conversational
subtle
not robotic
not announcer-like
```

---

# 41. Original Voice Requirement

Mikasa's voice should be based on:

- An original voice.
- Properly licensed voice material.
- Authorized voice cloning input.

The architecture should not depend on impersonating a specific real person.

---

# 42. Voice Portability

The same Mikasa identity should be reproducible across providers where possible.

Example:

```text
Pocket TTS
    → Mikasa voice embedding A

Breeze TTS
    → Mikasa voice profile B
```

The resulting voices need not be acoustically identical.

They should preserve recognizable style and identity.

---

# 43. Speech Director

`SpeechDirector` converts Mikasa's canonical response into a spoken-performance request.

The LLM response should not be sent blindly to TTS.

Conceptually:

```text
Canonical Response
      |
      v
Speech Director
      |
      v
Spoken Representation
      |
      v
TTS Router
```

---

# 44. Spoken Representation

Conceptual structure:

```text
SpeechRequest:
    request_id

    spoken_text

    delivery:
        mood
        energy
        pace
        emphasis
        pause_style
        urgency

    vocal_events

    voice_identity

    priority

    interruptible

    task_id
```

The exact schema requires an ADR.

---

# 45. Canonical vs. Spoken Response

The canonical answer may be:

```text
The test suite completed with 132 passing tests.
Two integration tests failed because the database
fixture is missing...
```

Mikasa may speak:

```text
"The tests finished. Two integration tests failed.
I put the details on screen."
```

The spoken version is presentation.

The canonical result remains unchanged.

---

# 46. Natural Speech

Speech output should avoid sounding like:

```text
I. HAVE. COMPLETED. THE. TASK.
```

The system should support:

- Natural phrasing.
- Prosody.
- Pauses.
- Sentence rhythm.
- Emphasis.
- Appropriate speed variation.

The TTS provider determines how much control is available.

---

# 47. Emotion and State

Speech delivery may respond to conversational state.

Examples:

```text
normal explanation
→ calm / conversational

important warning
→ focused / slightly urgent

successful task
→ warm / positive

user interruption
→ brief / responsive

error
→ calm / clear
```

Do not create exaggerated artificial emotion for every response.

---

# 48. State-Aware Speech

Speech may take context from:

```text
task state

conversation state

response purpose

urgency

approval state

error state
```

It should not use private hidden reasoning.

---

# 49. Vocal Events

Advanced engines may support subtle events such as:

```text
small laugh

breath

sigh

hesitation
```

These should be used sparingly.

They should never make critical information harder to understand.

---

# 50. Speech Segmentation

Long responses should be segmented.

Example:

```text
Sentence group 1
→ synthesize
→ play

Sentence group 2
→ synthesize
→ play
```

This reduces first-audio latency.

---

# 51. Semantic Chunking

Do not split speech arbitrarily by character count.

Prefer boundaries such as:

- Sentence.
- Clause.
- Short paragraph.
- Complete idea.

This improves naturalness.

---

# 52. Response Streaming

Future integration may allow:

```text
LLM TOKEN STREAM
      |
      v
STABLE TEXT SEGMENTER
      |
      v
SPEECH DIRECTOR
      |
      v
STREAMING TTS
```

This allows Mikasa to begin speaking before the full LLM response is complete.

---

# 53. Stable Text Barrier

Raw partial model tokens must not immediately reach TTS.

Bad:

```text
"I thi—"
```

because later generation may change direction.

The segmenter should wait for stable semantic chunks.

---

# 54. Speculative Speech

Very advanced implementations may speak speculative content early.

This should be avoided initially.

Incorrect spoken information is harder to retract than uncommitted text.

---

# 55. Conversational Acknowledgments

For long-running tasks, Mikasa may respond quickly with:

```text
"Got it. I'm checking the project now."
```

Then the actual task proceeds independently.

This reduces perceived latency.

The acknowledgment must reflect real task creation.

---

# 56. Progress Speech Policy

Mikasa should not narrate every internal action.

Useful spoken updates:

```text
"I found the failing test."

"I need your permission before I modify that folder."

"The task is finished."
```

Avoid:

```text
"I'm calling tool number three."

"I'm parsing JSON now."

"I'm making model request six."
```

---

# 57. Progress Modes

Possible user configuration:

```text
SILENT

IMPORTANT_ONLY

NORMAL

VERBOSE
```

Default should likely be:

```text
IMPORTANT_ONLY
```

during background work.

---

# 58. Speech Priority

Speech queue priorities may include:

```text
CRITICAL

APPROVAL

DIRECT_REPLY

TASK_RESULT

IMPORTANT_PROGRESS

LOW_PRIORITY_PROGRESS
```

Higher-priority messages may replace stale low-priority speech.

---

# 59. User Interruption Priority

Human speech always has priority over ordinary Mikasa speech.

When reliable interruption is available:

```text
USER SPEECH
>
TTS OUTPUT
```

---

# 60. Voice Sessions

Conceptual structure:

```text
VoiceSession:
    voice_session_id

    session_id

    mode

    state

    microphone_device
    output_device

    stt_provider
    tts_profile

    voice_identity

    started_at
    last_activity_at

    muted
```

Voice session state is separate from agent task state.

---

# 61. Voice Modes

Potential modes:

```text
PUSH_TO_TALK

TAP_TO_TALK

CONVERSATION

HANDS_FREE

WAKE_WORD
```

Initial target:

```text
PUSH_TO_TALK
+
INTERRUPTIBLE PLAYBACK
```

---

# 62. Conversation Mode

Later:

```text
CONVERSATION MODE
```

keeps a voice session open.

The user can:

- Speak.
- Wait.
- Interrupt.
- Continue.

No repeated microphone button is required.

---

# 63. Wake Word

Wake word should be added only after conversation mode is reliable.

Preferred architecture:

```text
LOCAL WAKE DETECTOR

↓

VOICE SESSION ACTIVATION
```

The wake word grants attention.

It does not grant permission for privileged actions.

---

# 64. Always-Listening Privacy

Always-listening mode must clearly indicate:

```text
MICROPHONE STANDBY ACTIVE
```

Local wake-word detection should be preferred when practical.

Raw standby audio should not become conversation history or memory.

---

# 65. Speech-to-Text Provider Router

STT should also use provider abstraction.

Possible future providers:

```text
LOCAL FAST STT

LOCAL HIGH ACCURACY STT

REMOTE OPTIONAL STT
```

Routing may consider:

- Language.
- Hardware.
- Latency.
- Privacy.
- Accuracy.

---

# 66. STT Confidence

Low-confidence recognition should affect behavior.

Example:

Recognized:

```text
"delete project cache"
```

with poor confidence.

For potentially destructive action:

```text
REQUEST CONFIRMATION
```

STT confidence is not identity authentication.

---

# 67. Command Corrections

User:

```text
"No, I said config, not cache."
```

This should:

- Correct the conversational turn.
- Update current task context if relevant.
- Not create unrelated memory automatically.

---

# 68. Voice Approval

Voice approvals must use the normal Permission Service.

Example:

```text
MIKASA:
"I need permission to write outside the current project.
Allow it once?"
```

User:

```text
"Yes, only this folder."
```

The resulting permission scope must reflect:

```text
THIS FOLDER
```

not a broad approval.

---

# 69. Sensitive Voice Approvals

High-impact actions may require stronger confirmation.

Potential behavior:

```text
voice confirmation

+

visible approval card
```

for certain permissions.

Exact policy belongs to Security ADRs.

---

# 70. Voice Is Not Authentication

Speaker recognition may eventually help personalize interaction.

It must not become the sole security mechanism for:

- Credentials.
- Destructive actions.
- Sensitive data.
- Permission elevation.

---

# 71. Audio Retention

Raw microphone audio should use minimal retention.

Preferred default:

```text
PROCESS

↓

TRANSCRIBE

↓

DISCARD
```

unless:

- User explicitly saves it.
- Debug mode explicitly captures it.
- A task requires it.

---

# 72. Transcript Retention

Final transcripts may enter conversation history.

They do not automatically become persistent memory.

Conversation history:

```text
!=
MEMORY
```

---

# 73. TTS Cache

The system may cache safe reusable voice assets such as:

```text
voice embeddings

provider states

model weights
```

Generated conversational speech should not be permanently cached by default.

---

# 74. Voice Model Loading

Some local models may take time to initialize.

The runtime should support:

```text
COLD

LOADING

READY

UNLOADING
```

states.

Frequently used voice models may remain loaded when resources permit.

---

# 75. Resource Management

The Voice Runtime should coordinate memory use with:

- Local LLM.
- Browser.
- Coding sandbox.
- Other local models.

Running everything simultaneously may exceed hardware capacity.

---

# 76. Adaptive Model Loading

Possible policy:

```text
HIGH RESOURCE SYSTEM
→ keep STT + TTS loaded

LOW RESOURCE SYSTEM
→ lazy load / unload

VOICE SESSION START
→ prewarm required model
```

This must be based on actual hardware.

---

# 77. Latency Budget

Important latency stages:

```text
speech detection

endpointing

STT

agent response

speech segmentation

TTS first chunk

audio playback
```

Each should be measured separately.

Do not only measure total round-trip time.

---

# 78. First-Audio Latency

A key voice metric is:

```text
time from end of user turn
to first Mikasa audio
```

Another useful metric:

```text
time from generated speech segment
to first playback sample
```

No numerical target should be committed until baseline measurements exist.

---

# 79. Interruption Latency

Measure:

```text
user starts speaking
↓
Mikasa audio stops
```

This is one of the most important perceived-quality metrics.

---

# 80. Naturalness Evaluation

Voice evaluation should consider:

- Naturalness.
- Intelligibility.
- Voice consistency.
- Latency.
- Prosody.
- Interruption behavior.
- Long-form stability.

Naturalness should be evaluated with blind listening where practical.

---

# 81. Voice Identity Evaluation

Test whether listeners perceive the same Mikasa identity across:

- Neutral speech.
- Fast speech.
- Serious warnings.
- Longer explanations.
- Different TTS providers.

Provider switching should not make Mikasa feel like a completely different assistant when avoidable.

---

# 82. Provider Fallback

If preferred expressive TTS is unavailable:

```text
EXPRESSIVE
    ↓
REALTIME
```

may be allowed.

Fallback should never silently violate:

```text
LOCAL_ONLY
```

or other privacy settings.

---

# 83. Graceful Degradation

Possible degradation:

```text
FULL DUPLEX
    ↓
INTERRUPTIBLE HALF DUPLEX
    ↓
PUSH TO TALK
    ↓
TEXT ONLY
```

Mikasa should remain usable.

---

# 84. Failure Behavior

Possible errors:

```text
MICROPHONE_UNAVAILABLE

AUDIO_DEVICE_LOST

STT_UNAVAILABLE

STT_TIMEOUT

TTS_UNAVAILABLE

TTS_STREAM_FAILED

AEC_FAILED

VOICE_MODEL_INCOMPATIBLE

RESOURCE_EXHAUSTED
```

Voice failure should not crash the core assistant.

---

# 85. TTS Failure

If TTS fails:

```text
canonical text response remains available
```

The task result must not be lost.

---

# 86. STT Failure

If STT fails:

Do not invent a user request.

Show:

```text
I couldn't reliably transcribe that.
```

or equivalent.

---

# 87. Device Changes

Future desktop/mobile support should handle:

```text
headphones connected

Bluetooth device changed

microphone disconnected

default output changed
```

without restarting Mikasa when practical.

---

# 88. Headphones

Headphones simplify:

- Echo cancellation.
- Full duplex.
- Privacy.

But they must not be required.

---

# 89. Desktop Integration

Desktop voice may support:

```text
global push-to-talk hotkey

conversation overlay

microphone state

current transcript

interrupt button

mute
```

The interface remains separate from the Voice Runtime.

---

# 90. Mobile Integration

Mobile may eventually support:

- Push-to-talk.
- Hands-free conversation.
- Bluetooth headset integration.
- Lock-screen task status.
- Voice notifications.

Mobile OS restrictions must be respected.

---

# 91. Spoken Notifications

Mikasa may speak important task events.

Examples:

```text
"The build finished."

"I need your approval."

"The research task is blocked."
```

Notifications must be configurable.

---

# 92. Quiet Mode

Voice configuration should eventually allow:

```text
SILENT

HEADPHONES_ONLY

NORMAL

DO_NOT_DISTURB
```

A task completing at an inconvenient time should not unexpectedly speak aloud.

---

# 93. Observability

Voice events should include:

```text
voice.session_started

voice.user_speech_started

voice.user_speech_ended

voice.partial_transcript

voice.final_transcript

voice.response_started

voice.tts_started

voice.tts_first_chunk

voice.playback_started

voice.barge_in_detected

voice.playback_interrupted

voice.session_ended
```

Sensitive raw audio must not appear in normal logs.

---

# 94. Voice Traces

One voice turn may trace:

```text
turn_24

├─ VAD start
├─ STT partial
├─ STT final
├─ UserRequest created
├─ Agent response
├─ SpeechDirector
├─ TTS stream
├─ Playback
└─ completion
```

This is extremely useful for latency tuning.

---

# 95. Voice Metrics

Potential metrics:

```text
speech_to_final_transcript_latency

turn_endpoint_latency

response_first_token_latency

tts_first_chunk_latency

first_audio_latency

barge_in_stop_latency

stt_error_rate

tts_error_rate

false_barge_in_rate
```

No invented benchmark numbers.

---

# 96. Testing — Audio Front-End

Verify:

```text
microphone starts/stops

correct device used

audio format normalized

noise input does not create task

device loss handled
```

---

# 97. Testing — Streaming STT

Verify:

```text
partial transcripts arrive

final transcript arrives once

duplicate final transcript does not create duplicate task

cancel stops transcription

low-confidence sensitive command is handled safely
```

---

# 98. Testing — TTS Streaming

Verify:

```text
first audio chunk plays before complete synthesis

chunks play in order

cancel stops generation

flush removes stale queued speech
```

---

# 99. Testing — Barge-In

Critical scenario:

```text
MIKASA SPEAKING

↓

USER STARTS TALKING

↓

MIKASA AUDIO DUCKS

↓

HUMAN SPEECH CONFIRMED

↓

TTS CANCELLED

↓

PLAYBACK STOPS

↓

USER TRANSCRIPT CONTINUES
```

Task continues unless separately cancelled.

---

# 100. Testing — Echo Prevention

While Mikasa speaks through speakers:

```text
her own voice must not create a new user turn
```

This requires a dedicated integration test.

---

# 101. Testing — Task Cancellation

Scenario:

User says:

```text
"Stop talking."
```

Expected:

```text
speech stops

task remains RUNNING
```

Then:

```text
"Cancel the task."
```

Expected:

```text
TaskManager cancellation requested
```

---

# 102. Testing — Provider Fallback

Preferred provider unavailable.

Expected:

- Router evaluates permitted fallback.
- Privacy policy respected.
- Voice identity preserved as much as possible.
- UI reflects degraded provider.

---

# 103. Testing — Local-Only

Set:

```text
LOCAL_ONLY = true
```

Disable local providers.

Expected:

```text
VOICE UNAVAILABLE / DEGRADED
```

Never remote transmission.

---

# 104. Testing — Long Speech

Test:

- Long paragraph.
- Several minutes of output.
- Memory stability.
- Audio continuity.
- Cancellation halfway through.

The system must not require complete audio generation in memory first.

---

# 105. Testing — Rapid Interruption

Test repeated interaction:

```text
Mikasa speaks
user interrupts
Mikasa responds
user interrupts again
```

No deadlock.

No overlapping audio streams.

No duplicate transcripts.

---

# 106. Testing — Emotional Delivery

For providers supporting expressive control:

Verify that changes in:

```text
calm

urgent

positive

serious
```

produce meaningful but consistent differences.

This test may require human evaluation.

---

# 107. Initial Implementation Milestone

The first real Voice Runtime milestone should contain:

```text
AudioFrontEnd

Push-to-talk

one local STT provider

TurnManager

SpeechDirector basic implementation

LocalTTSRouter

Pocket TTS candidate adapter or selected equivalent

streaming playback

barge-in

stop-speaking semantics

same Application Gateway

voice observability
```

---

# 108. Initial Milestone Exclusions

Do not require:

```text
Breeze TTS integration

wake word

full hands-free mode

speaker authentication

3D avatar

advanced emotional events

mobile voice

multiple simultaneous TTS models
```

The architecture must support them later.

---

# 109. Second Voice Milestone

Possible additions:

```text
conversation mode

AEC

continuous microphone

improved endpointing

multiple TTS providers

VoiceIdentity

expressive SpeechDirector

hardware-aware routing
```

---

# 110. Third Voice Milestone

Possible additions:

```text
full duplex

wake word

local wake detector

advanced emotion

mobile integration

spoken task notifications

adaptive voice behavior
```

---

# 111. Candidate Voice Research Tasks

Before implementation, evaluate:

```text
Pocket TTS
- actual latency
- CPU usage
- voice quality
- cloning quality
- streaming cancellation

Breeze TTS 2
- expressive quality
- latency
- VRAM
- voice consistency
- streaming
- license implications

Whisper-Chan
- actual architecture
- reusable components
- provider suitability
```

Testing must occur on real supported hardware.

---

# 112. STT Research Tasks

Research should compare local STT options for:

```text
streaming quality

partial transcript quality

endpointing

CPU/GPU requirements

language support

latency

cancellation

Windows support
```

No STT provider is selected by this document.

---

# 113. Audio Library Research

Evaluate libraries for:

```text
cross-platform microphone capture

low-latency playback

AEC integration

device switching

buffer control

Windows/macOS/Linux support
```

The chosen layer must support interruption.

---

# 114. Architecture Decisions Required

```text
VOICE-ADR-001
Audio I/O framework.

VOICE-ADR-002
Initial local STT provider.

VOICE-ADR-003
Initial real-time TTS provider.

VOICE-ADR-004
Expressive TTS provider.

VOICE-ADR-005
Internal audio format.

VOICE-ADR-006
VAD implementation.

VOICE-ADR-007
AEC implementation.

VOICE-ADR-008
TurnManager state machine.

VOICE-ADR-009
Barge-in thresholds and semantics.

VOICE-ADR-010
SpeechRequest schema.

VOICE-ADR-011
VoiceIdentity representation.

VOICE-ADR-012
TTS provider capability schema.

VOICE-ADR-013
Hardware-aware routing.

VOICE-ADR-014
Raw audio retention.

VOICE-ADR-015
Streaming STT integration.

VOICE-ADR-016
Streaming LLM-to-TTS segmentation.

VOICE-ADR-017
Local-only voice policy.

VOICE-ADR-018
Future wake-word system.
```

Approved decisions belong in:

`docs/22_DECISION_LOG.md`

---

# 115. Acceptance Scenario — Natural Conversation

User enables voice.

```text
USER:
"Mikasa, check the project tests."
```

Expected:

1. Speech is detected.
2. Partial transcript appears.
3. Final transcript appears.
4. One canonical request is created.
5. Real task begins.
6. Mikasa starts speaking a short acknowledgment.
7. Speech is streamed.
8. Task continues.

Then while Mikasa speaks:

```text
USER:
"Actually, check the config first."
```

Expected:

1. Human speech detected.
2. Mikasa's volume ducks.
3. Barge-in is confirmed.
4. Current TTS stream is cancelled.
5. Queued speech is flushed.
6. User speech continues transcribing.
7. New turn reaches the same agent/task context.
8. Mikasa adapts task plan if appropriate.
9. Task is not automatically cancelled.

This is the primary voice acceptance scenario.

---

# 116. Acceptance Scenario — Human-Like Delivery

Mikasa produces:

```text
"I found the issue. The config points to the old path."
```

Expected:

- Consistent Mikasa voice identity.
- Natural pacing.
- Correct emphasis.
- No robotic long pause before playback.
- Audio begins before entire response is synthesized.

---

# 117. Acceptance Scenario — Local Privacy

Configuration:

```text
Voice Mode:
LOCAL_ONLY
```

Expected:

- Audio remains local.
- STT remains local.
- TTS remains local.
- No automatic cloud fallback.
- Missing local provider produces honest degraded state.

---

# 118. Acceptance Scenario — Provider Switching

Realtime provider:

```text
Pocket TTS
```

becomes unavailable.

Expressive compatible provider exists locally.

Expected:

- LocalTTSRouter selects permitted provider.
- VoiceSession remains functional.
- Provider change is logged.
- Core task remains unaffected.

---

# 119. Definition of Done — Voice Runtime V1

Voice Runtime V1 is complete when:

- Real microphone input works.
- Audio is normalized.
- Local streaming STT works.
- Partial and final transcripts are distinct.
- Only final turns create canonical requests.
- Voice requests use the same Application Gateway as text.
- TurnManager coordinates interaction state.
- Local TTS routing exists.
- At least one real local TTS backend works.
- Audio plays progressively.
- First audio begins before complete synthesis finishes.
- Speech can be interrupted.
- Barge-in does not cancel the task.
- Task cancellation is separately supported.
- Provider failures degrade gracefully.
- Voice events are observable.
- Raw secrets/audio are not leaked to logs.
- Local-only policy is enforceable.
- End-to-end acceptance tests pass.

---

# 120. Final Voice Architecture Principle

Mikasa's voice should feel like a real conversational interface, not a sound effect added to a chatbot.

The system must preserve:

```text
HEARING
    !=
UNDERSTANDING

PARTIAL TRANSCRIPT
    !=
FINAL USER REQUEST

SPEAKING
    !=
TASK EXECUTION

STOP SPEAKING
    !=
CANCEL TASK

VOICE STYLE
    !=
CORE REASONING

VOICE PROVIDER
    !=
MIKASA'S IDENTITY

LOCAL-FIRST
    !=
ONE HARD-CODED MODEL

FULL DUPLEX
    !=
UNCONTROLLED ALWAYS-LISTENING
```

The target loop is:

```text
LISTEN CONTINUOUSLY

↓

DETECT HUMAN SPEECH

↓

TRANSCRIBE STREAMING

↓

UNDERSTAND THE TURN

↓

ACT THROUGH THE SAME MIKASA CORE

↓

PREPARE NATURAL SPOKEN OUTPUT

↓

STREAM LOCAL SPEECH

↓

KEEP LISTENING

↓

ALLOW INTERRUPTION

↓

ADAPT

↓

CONTINUE
```

**Mikasa should not merely have a voice. She should have a real-time conversational voice runtime.**