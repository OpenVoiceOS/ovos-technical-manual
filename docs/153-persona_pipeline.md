# Persona Pipeline

The **`ovos-persona-pipeline-plugin`** provides a dynamic way to integrate persona-based conversational behavior into the OVOS pipeline system. It allows you to route user utterances to AI personas instead of skill matchers, depending on context and configuration.

---

## Overview

The `persona-pipeline` is a plugin for the OVOS pipeline architecture. It dynamically delegates user utterances to a configured **Persona**, which attempts to resolve the intent using a sequence of **Solver Plugins** (e.g., LLMs, search tools, knowledge bases).

You can configure it to:

- 🔀 Intercept all utterances and give full control to the persona.
- 🧠 Fall back to the persona only if skills don't match.
- ⚙️ Operate based on confidence tiers (high/medium/low).

---

## Plugin Structure

The plugin is composed of two components:

| Plugin Name                            | Usage                            |
|----------------------------------------|----------------------------------|
| `ovos-persona-pipeline-plugin-high`    | For active persona interactions  |
| `ovos-persona-pipeline-plugin-low`     | Fallback persona handling        |

You must **insert these plugin IDs** in your `mycroft.conf` under the `intents.pipeline` key to activate persona handling at the appropriate tier.

---

## Configuration

```jsonc
{
  "intents": {
    "persona": {
      "handle_fallback": true,
      "default_persona": "Remote Llama",
      "short-term-memory": true
    },
    "pipeline": [
      // Depending on strategy, insert plugin here
    ]
  }
}
```

### `persona` section options:

| Key                  | Description                                                                 |
|----------------------|-----------------------------------------------------------------------------|
| `handle_fallback`    | Enables fallback routing when no persona is active                          |
| `default_persona`    | Sets a persona to use by default (e.g., after boot or reset)                |
| `short-term-memory`  | Maintains conversation state within a session (boolean)                     |

---

## Pipeline Strategies

### 1. **Full Control (Persona-First)**

In this mode, **personas override** all skills. The persona handles every utterance unless explicitly deactivated.

```jsonc
"pipeline": [
  "ovos-persona-pipeline-plugin-high",
  "stop_high",
  "converse",
  "padatious_high",
  "adapt_high",
  ...
]
```

- ✅ Best for immersive chatbot experiences
- ⚠️ Skills like music, alarms, and weather will not trigger unless persona is disabled

---

### 2. **Hybrid Mode (Skills First)**

Only unmatched or low-confidence utterances are routed to the persona.

```jsonc
"pipeline": [
  "stop_high",
  "converse",
  "padatious_high",
  "adapt_high",
  "ovos-persona-pipeline-plugin-high",
  "fallback_medium",
  ...
]
```

- ✅ Preserves traditional voice assistant behavior
- 💬 Persona fills in where skills fall short

---

### 3. **Fallback Mode Only**

Even when no persona is active, this mode allows the pipeline to fall back to a **default persona** for unmatched utterances.

```jsonc
"pipeline": [
  ...
  "fallback_medium",
  "ovos-persona-pipeline-plugin-low",
  "fallback_low"
]
```

- ✅ Replaces `skill-ovos-fallback-chatgpt`
- 🔄 Fallbacks to a default persona response for a consistent assistant feel

---

## Persona Resolution Flow

1. **Utterance Received**
2. Pipeline matchers are checked in order.
3. If `persona-pipeline` is reached:
    - If a persona is **active**, send utterance to that persona.
    - If no persona is active and `handle_fallback` is enabled, use the **default_persona**.
4. The persona delegates to its configured **solvers** until one returns a response.
5. The pipeline returns the matched response back to the user.

---

## Persona Configuration

Personas are defined in:

```
~/.config/ovos_persona/*.json
```

### Example:

```json
{
  "name": "Remote Llama",
  "solvers": [
    "ovos-solver-openai-plugin",
    "ovos-solver-failure-plugin"
  ],
  "ovos-solver-openai-plugin": {
    "api_url": "https://llama.smartgic.io/v1",
    "key": "sk-xxx",
    "persona": "friendly and concise assistant"
  }
}
```

Each persona defines a `solvers` list.

- Solvers are attempted **in order**.
- The first solver to return a valid result ends the search.
- Include a `"ovos-solver-failure-plugin"` as a final fallback for graceful error handling.

---

## Persona Intents

`"ovos-persona-pipeline-plugin-high"` supports a set of core voice intents to manage persona interactions seamlessly. 

These intents provide **out-of-the-box functionality** for controlling the Persona Service, ensuring smooth integration with the conversational pipeline and enhancing user experience.

### **List Personas**

**Example Utterances**:

- "What personas are available?"
- "Can you list the personas?"
- "What personas can I use?"

### **Check Active Persona**

**Example Utterances**:

- "Who am I talking to right now?"
- "Is there an active persona?"
- "Which persona is in use?"

### **Activate a Persona**

**Example Utterances**:

- "Connect me to {persona}"
- "Enable {persona}"
- "Awaken the {persona} assistant"
- "Start a conversation with {persona}"
- "Let me chat with {persona}"


### **Single-Shot Persona Questions**

Enables users to query a persona directly without entering an interactive session.

**Example Utterances**:

- "Ask {persona} what they think about {utterance}"
- "What does {persona} say about {utterance}?"
- "Query {persona} for insights on {utterance}"
- "Ask {persona} for their perspective on {utterance}"


### **Stop Conversation**

**Example Utterances**:

- "Stop the interaction"
- "Terminate persona"
- "Deactivate the chatbot"
- "Go dormant"
- "Enough talking"
- "Shut up"

---

