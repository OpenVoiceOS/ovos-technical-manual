# Dialog Transformers

**Dialog Transformers** in OpenVoiceOS (OVOS) are plugins that modify or enhance text responses just before they are sent to the Text-to-Speech (TTS) engine. This allows for dynamic adjustments to the assistant's speech, such as altering tone, simplifying language, or translating content, without requiring changes to individual skills.

---

## How They Work

1. **Intent Handling**: After a user's utterance is processed and an intent is matched, the corresponding skill generates a textual response.
2. **Transformation Phase**: Before this response is vocalized, it passes through any active dialog transformers.
3. **TTS Output**: The transformed text is then sent to the TTS engine for audio synthesis.

This pipeline ensures that all spoken responses can be uniformly modified according to the desired transformations.

---

## Configuration

To enable dialog transformers, add them to your `mycroft.conf` file under the `dialog_transformers` section:

```json
"dialog_transformers": {
  "plugin_name": {
    // plugin-specific configuration
  }
}
```

Replace `"plugin_name"` with the identifier of the desired plugin and provide any necessary configuration parameters.

---

## Available Dialog Transformer Plugins

### **OVOS Dialog Normalizer Plugin**

* **Purpose**: Prepares text for TTS by expanding contractions and converting digits to words, ensuring clearer pronunciation.
* **Example**:

    * Input: `"I'm 5 years old."`
    * Output: `"I am five years old."`
  
* **Installation**:

```bash
pip install ovos-dialog-normalizer-plugin
```
* **Configuration**:

```json
"dialog_transformers": {
  "ovos-dialog-normalizer-plugin": {}
}
```
* **Source**: [GitHub Repository](https://github.com/OpenVoiceOS/ovos-dialog-normalizer-plugin)

---

### **OVOS OpenAI Dialog Transformer Plugin**

* **Purpose**: Utilizes OpenAI's API to rewrite responses based on a specified persona or tone.
* **Example**:

    * Rewrite Prompt: `"Explain like I'm five"`
    * Input: `"Quantum mechanics is a branch of physics that describes the behavior of particles at the smallest scales."`
    * Output: `"Quantum mechanics helps us understand really tiny things."`

* **Installation**:

```bash
pip install ovos-openai-plugin
```

* **Configuration**:

```json
"dialog_transformers": {
    "ovos-dialog-transformer-openai-plugin": {
      "rewrite_prompt": "Explain like I'm five"
    }
}
```
* **Source**: [GitHub Repository](https://github.com/OpenVoiceOS/ovos-openai-plugin)

---

### **OVOS Bidirectional Translation Plugin**

* **Purpose**: Translates responses to match the user's language, enabling multilingual interactions.

* **Features**:

    * Detects the language of the user's input.
    * Works together with a companion utterance transformer plugin
    * Translates the assistant's response back into the user's language.
  
* **Installation**:

```bash
pip install ovos-bidirectional-translation-plugin
```
* **Configuration**:

```json
"dialog_transformers": {
    "ovos-bidirectional-dialog-transformer": {
      "bidirectional": true
    }
}
```
* **Source**: [GitHub Repository](https://github.com/OpenVoiceOS/ovos-bidirectional-translation-plugin)

---

## Creating Custom Dialog Transformers

To develop your own dialog transformer:

**Create a Python Class**:

```python
from ovos_plugin_manager.templates.transformers import DialogTransformer

class MyCustomTransformer(DialogTransformer):
   def __init__(self, config=None):
       super().__init__("my-custom-transformer", priority=10, config=config)

   def transform(self, dialog: str, context: dict = None) -> Tuple[str, dict]:
        """
        Optionally transform passed dialog and/or return additional context
        :param dialog: str utterance to mutate before TTS
        :returns: str mutated dialog
        """
       # Modify the dialog as needed
        return modified_dialog, context
```

**Register as a Plugin**:
In your `setup.py`, include:

```python
entry_points={
   'ovos.plugin.dialog_transformer': [
       'my-custom-transformer = my_module:MyCustomTransformer'
   ]
}
```

**Install and Configure**:
After installation, add your transformer to the `mycroft.conf`:

```json
"dialog_transformers": {
  "my-custom-transformer": {}
}
```

