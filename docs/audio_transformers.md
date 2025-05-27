# Audio Transformers

**Audio Transformers** in OpenVoiceOS (OVOS) are plugins designed to process raw audio input before it reaches the Speech-to-Text (STT) engine. They enable functionalities such as noise reduction, language detection, and data transmission over sound, thereby enhancing the accuracy and versatility of voice interactions.

---

## Processing Flow

The typical audio processing pipeline in OVOS is as follows:

1. **Audio Capture**: Microphone captures raw audio input.
2. **Audio Transformation**: Audio Transformers preprocess the raw audio.
3. **Speech-to-Text (STT)**: Transformed audio is converted into text.
4. **Intent Recognition**: Text is analyzed to determine user intent.

Audio Transformers operate in step 2, allowing for enhancements and modifications to the audio signal before transcription.

---

## Configuration

To enable Audio Transformers, add them to your `mycroft.conf` under the `audio_transformers` section:

```json
"audio_transformers": {
  "plugin_name": {
    // plugin-specific configuration
  }
}
```

Replace `"plugin_name"` with the identifier of the desired plugin and provide any necessary configuration parameters.

---

## Available Audio Transformer Plugins

### **OVOS GGWave Audio Transformer**

* **Purpose**: Enables data transmission over sound using audio QR codes.
* **Features**:

    * Transmit data such as Wi-Fi credentials, URLs, or commands via sound.
    * Integrates with the `ovos-skill-ggwave` for voice-controlled activation.
  
* **Installation**:

```bash
  pip install ovos-audio-transformer-plugin-ggwave
```

* **Configuration Example**:

```json
  "audio_transformers": {
    "ovos-audio-transformer-plugin-ggwave": {
      "start_enabled": true
    }
  }
```

For more information, visit the [GitHub repository](https://github.com/OpenVoiceOS/ovos-audio-transformer-plugin-ggwave).

### **OVOS SpeechBrain Language Detection Transformer**

* **Purpose**: Automatically detects the language of spoken input to route it to the appropriate STT engine.
* **Features**:

    * Utilizes SpeechBrain models for language identification.
    * Enhances multilingual support by dynamically selecting the correct language model.
  
* **Installation**:

```bash
  pip install ovos-audio-transformer-plugin-speechbrain-langdetect
```

* **Configuration Example**:

```json
  "audio_transformers": {
    "ovos-audio-transformer-plugin-speechbrain-langdetect": {}
  }
```

For more information, visit the [GitHub repository](https://github.com/OpenVoiceOS/ovos-audio-transformer-plugin-speechbrain-langdetect).

---

## Creating Custom Audio Transformers

To develop your own Audio Transformer plugin for OVOS, implement a class that extends the base `AudioTransformer` template. 

This class allows you to process raw audio chunks at various stages before the Speech-to-Text (STT) engine processes the audio.

### Base Class Overview

Your custom transformer should subclass:

```python
from ovos_plugin_manager.templates.transformers import AudioTransformer

class MyCustomAudioTransformer(AudioTransformer):
    def __init__(self, config=None):
        super().__init__("my-custom-audio-transformer", priority=10, config=config)

    def on_audio(self, audio_data):
        # Process non-speech audio chunks (e.g., noise)
        return audio_data

    def on_hotword(self, audio_data):
        # Process full hotword/wakeword audio chunks
        return audio_data

    def on_speech(self, audio_data):
        # Process speech audio chunks during recording (not full utterance)
        return audio_data

    def on_speech_end(self, audio_data):
        # Process full speech utterance audio chunk
        return audio_data

    def transform(self, audio_data):
        # Optionally perform final transformation before STT stage
        # Return tuple (transformed_audio_data, optional_message_context)
        return audio_data, {}
```

### Lifecycle & Methods

* **Initialization**: Override `initialize()` for setup steps.
* **Audio Feed Handlers**:

      * `on_audio`: Handle background or non-speech chunks.
      * `on_hotword`: Handle wakeword/hotword chunks.
      * `on_speech`: Handle speech chunks during recording.
      * `on_speech_end`: Handle full utterance audio.
     
* **Final Transformation**:

      * `transform`: Return the final processed audio and optionally a dictionary of additional metadata/context that will be passed along with the `recognize_loop:utterance` message.

* **Reset**: The `reset()` method clears internal audio buffers, called after STT completes.

### Plugin Registration

In your `setup.py`, register the plugin entry point:

```python
entry_points={
    'ovos.plugin.audio_transformer': [
        'my-custom-audio-transformer = my_module:MyCustomAudioTransformer'
    ]
}
```

### Configuration Example

Add your transformer to `mycroft.conf`:

```json
"audio_transformers": {
  "my-custom-audio-transformer": {
    // plugin-specific config options here
  }
}
```

