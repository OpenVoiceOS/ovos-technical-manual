# Utterance Transformers

**Utterance Transformers** in OpenVoiceOS (OVOS) are plugins that process and modify user utterances immediately after speech-to-text (STT) conversion but before intent recognition. They serve to enhance the accuracy and flexibility of the assistant by correcting errors, normalizing input, and handling multilingual scenarios.

---

## How They Work

1. **Speech Recognition**: The user's spoken input is transcribed into text by the STT engine.
2. **Transformation Phase**: The transcribed text passes through any active utterance transformers.
3. **Intent Recognition**: The transformed text is then processed by the intent recognition system to determine the appropriate response.

This sequence ensures that any necessary preprocessing is applied to the user's input, improving the reliability of intent matching.

---

## Configuration

To enable utterance transformers, add them to your `mycroft.conf` file under the `utterance_transformers` section:

```json
"utterance_transformers": {
  "plugin_name": {
    // plugin-specific configuration
  }
}
```

Replace `"plugin_name"` with the identifier of the desired plugin and provide any necessary configuration parameters.

---

## Available Utterance Transformer Plugins

### **OVOS Utterance Normalizer Plugin**

* **Purpose**: Standardizes user input by expanding contractions, converting numbers to words, and removing unnecessary punctuation.
* **Example**:

    * Input: `"I'm 5 years old."`
    * Output: `"I am five years old"`
  
* **Installation**:

```bash
pip install ovos-utterance-normalizer
```
* **Configuration**:

```json
"utterance_transformers": {
  "ovos-utterance-normalizer": {}
}
```
* **Source**: [GitHub Repository](https://github.com/OpenVoiceOS/ovos-utterance-normalizer)

---

### **OVOS Utterance Corrections Plugin**

* **Purpose**: Applies predefined corrections to common misrecognitions or user-defined replacements to improve intent matching.
* **Features**:

    * Full utterance replacements via `corrections.json`
    * Word-level replacements via `word_corrections.json`
    * Regex-based pattern replacements via `regex_corrections.json`
  
* **Example**:

    * Input: `"shalter is a switch"`
    * Output: `"schalter is a switch"`
  
* **Installation**:

```bash
pip install ovos-utterance-corrections-plugin
```
* **Configuration**:

```json
"utterance_transformers": {
  "ovos-utterance-corrections-plugin": {}
}
```
* **Source**: [GitHub Repository](https://github.com/OpenVoiceOS/ovos-utterance-corrections-plugin)

---

### **OVOS Utterance Cancel Plugin**

* **Purpose**: Detects phrases indicating the user wishes to cancel or ignore the current command and prevents further processing.
* **Example**:

    * Input: `"Hey Mycroft, can you tell me the... umm... oh, nevermind that"`
    * Output: *Utterance is discarded; no action taken*
  
* **Installation**:

```bash
pip install ovos-utterance-plugin-cancel
```
* **Configuration**:

```json
"utterance_transformers": {
  "ovos-utterance-plugin-cancel": {}
}
```
* **Source**: [GitHub Repository](https://github.com/OpenVoiceOS/ovos-utterance-plugin-cancel)

---

### **OVOS Bidirectional Translation Plugin**

* **Purpose**: Detects the language of the user's input and translates it to the assistant's primary language if necessary, enabling multilingual interactions.
* **Features**:

    * Language detection and translation to primary language
    * Optional translation of responses back to the user's language
  
* **Example**:

    * Input: `"¿Cuál es el clima hoy?"` (Spanish)
    * Output: `"What is the weather today?"` (translated to English for processing)
  
* **Installation**:

```bash
pip install ovos-bidirectional-translation-plugin
```
* **Configuration**:

```json
"utterance_transformers": {
    "ovos-bidirectional-utterance-transformer": {
      "verify_lang": true,
      "ignore_invalid_langs": true
    }
}
```
* **Source**: [GitHub Repository](https://github.com/OpenVoiceOS/ovos-bidirectional-translation-plugin)

---

## Creating Custom Utterance Transformers

To develop your own utterance transformer:

**Create a Python Class**:

```python
from ovos_plugin_manager.templates.transformers import UtteranceTransformer

class MyCustomTransformer(UtteranceTransformer):
   def __init__(self, config=None):
       super().__init__("my-custom-transformer", priority=10, config=config)

   def transform(self, utterances, context):
       # Modify the utterances as needed
       return modified_utterances, context
```

**Register as a Plugin**:
In your `setup.py`, include:

```python
entry_points={
   'ovos.plugin.utterance_transformer': [
       'my-custom-transformer = my_module:MyCustomTransformer'
   ]
}
```

**Install and Configure**:
After installation, add your transformer to the `mycroft.conf`:

```json
"utterance_transformers": {
 "my-custom-transformer": {}
}
```

