# VAD Plugins in OVOS

## Overview

Voice Activity Detection (VAD) helps determine when a user has *finished* speaking. In OVOS, VAD plugins are used **after** the wake word is detected to decide when to stop recording and send the audio to speech-to-text (STT) engines. This reduces latency and avoids sending unnecessary silence.

While wake word detection typically starts the recording, VAD ensures it ends cleanly — optimizing performance and responsiveness.

## Usage Guide

- Install the desired VAD plugin:
```bash
pip install ovos-vad-plugin-<name>
```

- Set your preferred plugin in the OVOS configuration file (`mycroft.conf`):
```javascript
{
 "listener": {
   "VAD": {
     "module": "ovos-vad-plugin-silero"
   },
  // Setting to remove all silence/noise from start and end of recorded speech before STT
  "remove_silence": true
 }
}
```

- Restart the OVOS service to apply changes.

> 💡 By default, VAD is only used **after** wake word activation. See below for optional continuous mode.

## Available VAD Plugins

| Plugin | Description |
|--------|-------------|
| [ovos-vad-plugin-silero](https://github.com/OpenVoiceOS/ovos-vad-plugin-silero) | Uses [Silero VAD](https://github.com/snakers4/silero-vad), a neural network–based VAD offering excellent real-time accuracy. **Recommended.** |
| [ovos-vad-plugin-webrtcvad](https://github.com/OpenVoiceOS/ovos-vad-plugin-webrtcvad) | Wraps Google’s [WebRTC VAD](https://webrtc.org/), lightweight and fast, suited for short audio frames. |
| [ovos-vad-plugin-noise](https://github.com/OpenVoiceOS/ovos-vad-plugin-noise) | Simple threshold-based VAD using volume levels. Useful for constrained devices, but less accurate. |
| [ovos-vad-plugin-precise](https://github.com/OpenVoiceOS/ovos-vad-plugin-precise) | Uses a custom-trained model with [Mycroft Precise](https://github.com/MycroftAI/mycroft-precise). Can be tailored for your environment. |

## Technical Explanation

In OVOS, VAD operates **after** the wake word engine triggers recording. Its main purpose is to detect **the end of the user's speech**. Without VAD, the system would use a fixed timeout (e.g., 3 seconds of silence), which can lead to premature cutoffs or excessive silence that slows down transcription.

VAD plugins continuously monitor the audio during recording and tell the listener when the user has stopped talking. Once silence is detected for a defined threshold, OVOS stops recording and forwards the result to the STT engine.

This flow looks like:

```text
[ Wake Word Detected ] → [ Start Recording ] → [ VAD detects end of speech ] → [ Stop Recording ] → [ Send to STT ]
```

### Experimental Continuous Mode

OVOS also supports an **experimental continuous listening mode** in `ovos-dinkum-listener`, where wake word detection is bypassed entirely. In this mode, the listener uses **VAD alone** to decide when someone is speaking and triggers STT automatically.

To enable this behavior:
```json
{
  "listener": {
    "continuous_listen": false,
    "VAD": {
      "module": "ovos-vad-plugin-silero"
    }
  }
}
```

> ⚠️ This mode is **experimental**, it is not the default and is **unstable or prone to false triggers**. Use with caution. This may also cause OVOS to hear its own TTS responses as questions

> 💡 [ovos-transcription-validator](https://openvoiceos.github.io/ovos-technical-manual/151-llm-transformers/#ovos-transcription-validator) is extremely recommend as a companion plugin for this mode

## Tips & Caveats

- **Silero** is the most accurate and works well across platforms.
- **Noise-based** VAD can be too sensitive in environments with background sound.
- VAD plugins may expose tunable settings like silence thresholds or sensitivity — refer to each plugin's documentation.
- Disabling the wake word and relying only on VAD is **experimental** and not recommended for production use (yet).


---

**Coming Soon**
- Standalone usage examples
- How to build a custom VAD plugin