# Intent Transformers

**Intent Transformers** are a pluggable mechanism in OVOS that allow you to enrich or transform intent data **after** an intent is matched by an engine (Padatious, Adapt, etc.), but **before** it is passed to the skill handler.

This is useful for:

* Named Entity Recognition (NER)
* Keyword extraction
* Slot filling
* Contextual enrichment

Transformers operate on `IntentHandlerMatch` or `PipelineMatch` objects and are executed **in order of priority**. They enable complex processing pipelines without requiring every skill to reimplement entity logic.

---

## Default Transformers

In a standard OVOS installation, the following plugins are **installed and enabled by default**:

| Plugin                          | Description                                                                                        | Priority |
|---------------------------------| -------------------------------------------------------------------------------------------------- | -------- |
| `ovos-keyword-template-matcher` | Extracts values from `{placeholder}`-style intent templates                                        | 1        |
| `ovos-ahocorasick-ner-plugin`   | Performs NER using Aho-Corasick keyword matching based on registered entities from skill templates | 5        |

These are **not built into core**, but are bundled in standard OVOS setups and configured via `intent_transformers` in your configuration file.

---

## Configuration

To enable or disable specific transformers, modify your `mycroft.conf`:

```json
"intent_transformers": {
  "ovos-keyword-template-matcher": {
    "active": true
  },
  "ovos-ahocorasick-ner-plugin": {
    "active": false
  }
}
```


---

## How It Works

### Example Workflow

1. An utterance matches an intent via Padatious, Adapt, or another engine.
2. The matched intent is passed to the `IntentTransformersService`.
3. Each registered transformer plugin runs its `transform()` method.
4. Extracted entities are injected into the intent’s `match_data`.
5. The updated `match_data` is passed to the skill via the `Message` object.

### Skill Access

Entities extracted by transformers are made available to your skill in the `message.data` dictionary:

```python
location = message.data.get("location")
person = message.data.get("person")
```

---

## Default Plugins

### `ovos-ahocorasick-ner-plugin`

This plugin builds a per-skill Aho-Corasick automaton using keywords explicitly provided by the developer via registered entities.

> ❗ It will **only match keywords that the skill developer has accounted for**

It does **not** use external data or extract entities generically.

---

### `ovos-keyword-template-matcher`

This plugin parses registered intent templates like:

```
what's the weather in {location}
```

It uses the template structure to extract `{location}` directly from the utterance.

If the user says "what's the weather in Tokyo", the plugin will populate:

```python
match_data = {
  "location": "Tokyo"
}
```

---

## Writing Your Own Intent Transformer

To create a custom transformer:

```python
from ovos_plugin_manager.templates.transformers import IntentTransformer

class MyCustomTransformer(IntentTransformer):
    def __init__(self, config=None):
        super().__init__("my-transformer", priority=10, config=config)

    def transform(self, intent):
        # Modify intent.match_data here
        return intent
```

