# Microphone Plugins in OVOS

Microphone plugins in Open Voice OS (OVOS) are responsible for capturing audio input and feeding it to the listener. Introduced in `ovos-core` version **0.0.8**, these plugins allow for flexible integration with different audio backends and platforms.

## Usage Guide

To use a microphone plugin in OVOS:

- Install the desired plugin with `pip`:
```bash
pip install ovos-microphone-plugin-<name>
```

- Update your `mycroft.conf` (or `ovos.conf`) to specify the plugin:
```json
{
 "listener": {
   "microphone": {
     "module": "ovos-microphone-plugin-alsa"  // or another plugin
   }
 }
}
```

- Restart OVOS to apply the new microphone plugin configuration.

## Supported Microphone Plugins

| Plugin | Description | OS Compatibility |
|--------|-------------|------------------|
| [ovos-microphone-plugin-alsa](https://github.com/OpenVoiceOS/ovos-microphone-plugin-alsa) | Based on [pyalsaaudio](http://larsimmisch.github.io/pyalsaaudio). Offers low-latency and high performance on ALSA-compatible devices. | Linux |
| [ovos-microphone-plugin-pyaudio](https://github.com/OpenVoiceOS/ovos-microphone-plugin-pyaudio) | Uses [PyAudio](https://people.csail.mit.edu/hubert/pyaudio/). Good general-purpose plugin for Linux. | Linux |
| [ovos-microphone-plugin-sounddevice](https://github.com/OpenVoiceOS/ovos-microphone-plugin-sounddevice) | Built on [python-sounddevice](https://github.com/spatialaudio/python-sounddevice). Offers cross-platform support. | Linux, macOS, Windows |
| [ovos-microphone-plugin-files](https://github.com/OpenVoiceOS/ovos-microphone-plugin-files) | Uses audio files as input instead of a live microphone—ideal for testing and debugging. | Linux, macOS, Windows |
| [ovos-microphone-plugin-arecord](https://github.com/OVOSHatchery/ovos-microphone-plugin-arecord) | Wraps `arecord` using subprocess calls. Simple and effective on systems with ALSA. | Linux |
| [ovos-microphone-plugin-socket](https://github.com/OVOSHatchery/ovos-microphone-plugin-socket) | Receives audio over a socket connection. Useful for remote microphone setups. | Linux, macOS, Windows |

## Technical Explanation

OVOS uses a plugin architecture to decouple the audio input system from the rest of the voice stack. Microphone plugins implement a common interface, making it easy to swap between different audio sources or backends without changing application code.

Each plugin provides a stream of audio data that OVOS uses to detect wake words and perform speech-to-text (STT) processing. The `microphone` module is configured in the main listener settings, and the selected plugin is loaded dynamically at runtime.

## Tips & Caveats

- **Performance**: For best results on Linux, the ALSA plugin typically provides the lowest latency.
- **Cross-platform development**: Use the `sounddevice` or `files` plugin when developing on non-Linux systems.
- **Testing**: The `files` plugin is ideal for automated testing environments where live input isn’t available.
- **Remote audio**: The `socket` plugin is a proof-of-concept for networked microphones and is not recommended for production use without customization.


---

**Coming Soon**
- Standalone usage examples
- How to create your own microphone plugin (plugin template)