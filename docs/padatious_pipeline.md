# Padatious Pipeline

The **Padatious Pipeline Plugin** brings examples-based intent recognition to the **OpenVoiceOS (OVOS)** pipeline. It enables developers to define intents using example sentences, offering a simple and code-free way to create natural language interfaces for voice skills.

---

## Pipeline Stages

This plugin registers the following pipeline stages:

| Pipeline ID        | Description                              | Recommended Use                       |
| ------------------ | ---------------------------------------- | ------------------------------------- |
| `padatious_high`   | High-confidence Padatious intent matches | ✅ Primary stage for Padatious use     |
| `padatious_medium` | Medium-confidence Padatious matches      | ⚠️ Backup if confidence tuning allows |
| `padatious_low`    | Low-confidence Padatious matches         | 🚫 Not recommended (often inaccurate) |

Each stage is triggered based on the confidence level of the parsed intent, as configured in your system.

---

## Configuration

Configure Padatious thresholds in your `ovos.conf`:

```json
"intents": {
  "padatious": {
    "conf_high": 0.85,
    "conf_med": 0.65,
    "conf_low": 0.45
  }
}
```

These thresholds control which pipeline level receives a given intent result.

---

## Multilingual Support

Padatious is **excellent for multilingual environments** because intents are defined in plain text `.intent` files, not in code. This allows translators and non-developers to contribute new languages easily without touching Python.

To add another language, simply create a new `.intent` file in the relevant language folder, such as:

```
locale/pt-pt/weather.intent
locale/fr-fr/weather.intent
```

---

## Defining Intents

Intent examples are written line-by-line in `.intent` files:

```
what is the weather
tell me the weather
what's the weather like
```

In your skill:

```python
from ovos_workshop.decorators import intent_handler

@intent_handler("weather.intent")
def handle_weather(self, message):
    # Your code here
    pass
```

---

## Limitations

Padatious is reliable in terms of **not misclassifying** — it rarely picks the wrong intent. However, it has key limitations:

* **Weak paraphrase handling**: If the user speaks a sentence that doesn’t closely match an example, Padatious will often fail to match anything at all.

* **Rigid phrasing required**: You may end up in a “train the user to speak correctly” scenario, instead of training the system to understand variations.

* **Maintenance burden for sentence diversity**: Adding more phrasing requires adding more sentence examples per intent, increasing effort and clutter.

---

## When to Use

Padatious is a good choice in OVOS when:

* You want **easy localization/multilingual support**.
* You’re creating **simple, personal, or demo skills**.
* You can **control or guide user phrasing**, such as in kiosk or assistant environments.

Avoid Padatious for complex conversational use cases, skills with overlapping intents, or scenarios requiring broad paraphrasing support.

