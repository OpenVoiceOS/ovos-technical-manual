# 🔧 Transformer Plugins (Runtime Modifiers)

**Transformer plugins** operate independently of personas and provide fine-grained control over OVOS’s internal processing pipeline. They are not part of the persona framework but can synergize with it.

### Key Details:
- **Scope**: Transformers apply within the OVOS core pipeline—not inside personas (though solver plugins can use them internally if desired).
- **Independence**: Transformers and personas are separate systems. However, future enhancements may allow dynamic interaction between the two (e.g., a persona adjusting transformer settings).

### Key Integration Points:
- **[Utterance Transformers](https://openvoiceos.github.io/ovos-technical-manual//102-core/#utterance-transformers)**: Operate between **STT (Speech-to-Text)** and **NLP (Natural Language Processing)**.
- **[Dialog Transformers](https://openvoiceos.github.io/ovos-technical-manual//103-audio_service/#dialog-transformers)**: Operate between **NLP** and **TTS (Text-to-Speech)**.

---

## Examples of Transformer Plugins Using AI Agents


#### ✅ OVOS Transcription Validator

This plugin validates the output of STT engines using a language model to filter out incorrect or incoherent transcriptions *before* they are passed to NLP.

**How It Works:**

1. Receives an STT transcript and its language code.
2. Sends both to an LLM prompt (local or via Ollama).
3. Gets a `True` or `False` response based on utterance validity.

**Configuration Snippet (mycroft.conf):**
```json
"utterance_transformers": {
  "ovos-transcription-validator-plugin": {
    "model": "gemma3:1b",
    "ollama_url": "http://192.168.1.200:11434",
    "prompt_template": "/path/to/template.txt",
    "error_sound": true,
    "mode": "reprompt"
  }
}
```

**Use Case**: Prevent skills from being triggered by invalid STT output like `"Potato stop green light now yes."`

---
#### 🗣️ Dialog Transformer


This plugin rewrites assistant responses based on a persona-style prompt, enabling tone or complexity adjustments.

**Example Prompt Use Cases:**

- `"Rewrite the text as if you were explaining it to a 5-year-old"`
- `"Explain it like you're teaching a child"`
- `"Make it sound like an angry old man"`
- `"Add more 'dude'ness to it"`

**Configuration Snippet (mycroft.conf):**
```json
"dialog_transformers": {
  "ovos-dialog-transformer-openai-plugin": {
    "rewrite_prompt": "rewrite the text as if you were explaining it to a 5-year-old"
  }
}
```

This plugin often leverages LLMs through solver plugins but operates *after* the main dialog logic, adjusting the final output.

