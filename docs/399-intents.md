# Skills and Intents

At the heart of OVOS lies a powerful yet flexible **intent handling system** that enables voice-driven interaction. The system connects **user utterances** to **developer-defined behavior** through *intents*.

## Key Concept

**Skills register intent handlers.**
In practice, this means that:

* A **bus message** representing a user **intent** is **mapped to a specific piece of code**—the **intent handler**—within a skill.
* When the system detects that an utterance matches a registered intent, it emits the relevant bus message, and the corresponding handler is invoked.

## How Intents Are Defined

Skill developers have **two main ways** to define intents:

### 1. Example Utterances

* Developers write full example phrases that a user might say.
* The engine learns patterns from these to match similar user utterances.
* Example:

```text
["what's the weather", "tell me the weather", "how's the forecast"]
```

### 2. Keyword Rules

* Developers define combinations of required and optional **keywords**.
* Rules are defined in a more structured way.
* Example:

```python
IntentBuilder("WeatherIntent")
.require("weather_keyword")
.optionally("location")
```

## How OVOS Handles This

The **OVOS Core** is responsible for interpreting user utterances and deciding **which (if any) intent** they match. This is done by comparing the input against the limited training data (example phrases or keyword rules) provided by skill developers.

### Modern Intent Pipelines

* Historically:

    * **Adapt** was used for keyword-based matching.
    * **Padatious** was used for example-based matching.
    * These were inherited from Mycroft.

* Now:

    * OVOS has evolved into a **highly configurable intent pipeline framework**.
    * Multiple intent engines can be used in **parallel or sequence**.
    * Skill developers and system integrators can choose or define:

        * Which engines to use
        * How to prioritize them
        * When to fall back or skip certain engines

### Example Flow:

1. User says: *"What's the weather like tomorrow in Lisbon?"*
2. OVOS pipelines the utterance through configured engines.
3. If an intent matches, a bus message like `intent:WeatherIntent` is emitted.
4. The matching skill’s handler for `WeatherIntent` is called with the parsed data.

## Summary

* **Intent = Message + Handler**
* Skills declare what they can handle; OVOS decides *when* to trigger them.
* Intents are defined either via:
    * Full utterance examples
    * Structured keyword rules
* Modern OVOS pipelines go beyond Padatious and Adapt, allowing advanced, modular configurations for intent parsing.
