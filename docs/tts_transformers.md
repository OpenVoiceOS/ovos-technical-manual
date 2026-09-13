# TTS Transformers

**TTS Transformers** in OpenVoiceOS (OVOS) are plugins that process synthesized speech audio after the Text-to-Speech (TTS) engine generates it but before it's played back to the user. 

They enable post-processing of audio to apply effects, enhance clarity, voice clone or tailor the output to specific needs.

---

## How They Work

The typical flow for speech output in OVOS is:

1. **Dialog Generation**: The assistant formulates a textual response.
2. **Dialog Transformation**: Optional plugins modify the text to adjust tone or style.
3. **Text-to-Speech (TTS)**: The text is converted into speech audio.
4. **TTS Transformation**: Plugins apply audio effects or modifications to the speech.
5. **Playback**: The final audio is played back to the user.

TTS Transformers operate in step 4, allowing for dynamic audio enhancements without altering the original TTS output.

---

## Configuration

To enable TTS Transformers, add them to your `mycroft.conf` under the `tts_transformers` section:

```json
"tts_transformers": {
  "plugin_name": {
    // plugin-specific configuration
  }
}
```

Replace `"plugin_name"` with the identifier of the desired plugin and provide any necessary configuration parameters.

---

## Available TTS Transformer Plugins

### **OVOS SoX TTS Transformer**

* **Purpose**: Applies various audio effects using SoX (Sound eXchange) to the TTS output.
* **Features**:

    * Pitch shifting
    * Reverb
    * Tempo adjustment
    * Equalization
    * Noise reduction
    * And many more
  
* **Installation**:

```bash
  pip install ovos-tts-transformer-sox-plugin
```

* **Configuration Example**:

```json
  "tts_transformers": {
    "ovos-tts-transformer-sox-plugin": {
      "effects": ["pitch 300", "reverb"]
    }
  }
```

* **Requirements**: Ensure SoX is installed and available in your system's PATH.
* **Source**: [GitHub Repository](https://github.com/OpenVoiceOS/ovos-tts-transformer-sox-plugin)

---

## Creating Custom TTS Transformers

To develop your own TTS Transformer:

**Create a Python Class**:

```python
from ovos_plugin_manager.templates.transformers import TTSTransformer

class MyCustomTTSTransformer(TTSTransformer):
   def __init__(self, config=None):
       super().__init__("my-custom-tts-transformer", priority=10, config=config)

   def transform(self, wav_file: str, context: dict = None) -> Tuple[str, dict]:
       """Transform passed wav_file and return path to transformed file"""
       # Apply custom audio processing to wav_file
       return modified_wav_file, context

```



**Register as a Plugin**:
In your `setup.py`, include:

```python
entry_points={
   'ovos.plugin.tts_transformer': [
       'my-custom-tts-transformer = my_module:MyCustomTTSTransformer'
   ]
}
```


**Install and Configure**:
After installation, add your transformer to the `mycroft.conf`:

```json
"tts_transformers": {
 "my-custom-tts-transformer": {}
}
```

---

By leveraging TTS Transformers, you can enhance the auditory experience of your OVOS assistant, tailoring speech output to better suit your preferences or application requirements.([ovoshatchery.github.io][4])

