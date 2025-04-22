# AI Agents in OpenVoiceOS

OpenVoiceOS (OVOS) introduces a flexible and modular system for integrating AI agents into voice-first environments. This is made possible through a layered architecture built around **solvers**, **personas**, and **persona routing** components. This section explains how these parts work together to enable intelligent conversations with customizable behavior.

---

## Solver Plugins (Low-Level AI)

At the core of the AI agent system are [**solver plugins**](https://openvoiceos.github.io/ovos-technical-manual//360-solver_plugins/). These are simple black-box components responsible for handling a single task: receiving a text input (typically a question) and returning a text output (typically an answer).

![Untitled-2025-04-15-2340](https://github.com/user-attachments/assets/8a58417d-409e-4b87-94d0-0f2234064981)


### Key Features:
- **Input/Output**: Plain text in, plain text out.
- **Functionality**: Usually question-answering, though more specialized solvers exist (e.g., summarization, multiple choice).
- **Language Adaptation**: Solvers are automatically wrapped with a translation layer if they don't support the user's language. For instance, the Wolfram Alpha solver is English-only but can work with Portuguese through automatic bidirectional translation.
- **Fallback Behavior**: If a solver cannot produce a result (returns `None`), higher-level systems will attempt fallback options.

---

## Personas (Agent Definition Layer)

A **persona** represents a higher-level abstraction over solver plugins. It behaves like an AI agent with a defined personality and behavior, built by combining one or more solvers in a specific order

### Key Features:
- **Composition**: Each persona consists of a name, a list of solver plugins, and optional configuration for each.
- **Chained Execution**: When a user question is received, the persona tries solvers one by one. If the first solver fails (returns `None`), the next one is tried until a response is generated.
- **Customizable Behavior**: Different personas can emulate different personalities or knowledge domains by varying their solver stack.

![Untitled-2025-04-15-2340(7)](https://github.com/user-attachments/assets/453a906f-6d38-4878-ae7b-49b24270339f)

![Untitled-2025-04-15-2340(8)](https://github.com/user-attachments/assets/731835a3-44b1-463d-9fc6-085ca2658abc)


```
{
  "name": "OldSchoolBot",
  "solvers": [
    "ovos-solver-wikipedia-plugin",
    "ovos-solver-ddg-plugin",
    "ovos-solver-plugin-wolfram-alpha",
    "ovos-solver-wordnet-plugin",
    "ovos-solver-rivescript-plugin",
    "ovos-solver-failure-plugin"
  ],
  "ovos-solver-plugin-wolfram-alpha": {"appid": "Y7353-XXX"}
}
```


> 💡 personas don't need to use LLMs, you don't need a beefy GPU to use ovos-persona, any solver plugin can be used to define a persona

---

## Persona Pipeline (Runtime Routing in OVOS-Core)

Within `ovos-core`, the **[persona-pipeline](https://github.com/OpenVoiceOS/ovos-persona)** plugin handles all runtime logic for managing user interaction with AI agents.

### Key Features:
- **Persona Registry**: Supports multiple personas, defined by the user or discovered via installed plugins.
- **Session Control**: The user can say `"I want to talk with {persona_name}"` to route their dialog to a specific persona.
- **Session End**: The user can disable the current persona at any time to return to normal assistant behavior.
- **Fallback Handling**: If OpenVoiceOS can't answer, the system can ask the default persona instead of speaking an error.
- **Extensible**: Potential for future enhancements via messagebus to adjust system behavior based on persona (e.g., dynamic prompt rewriting).

in your `mycroft.conf`

```json
{
  "intents": {
      "persona": {
        "handle_fallback":  true,
        "default_persona": "Remote Llama"
      },
      "pipeline": [
          "stop_high",
          "converse",
          "ocp_high",
          "padatious_high",
          "adapt_high",
          "ovos-persona-pipeline-plugin-high",
          "ocp_medium",
          "...",
          "fallback_medium",
          "ovos-persona-pipeline-plugin-low",
          "fallback_low"
    ]
  }
}
```

---

## ReRankers / MultipleChoiceQuestionSolvers

A specialized kind of solver plugin that chooses the best answer out of several options

![Untitled-2025-04-15-2340(1)](https://github.com/user-attachments/assets/61c5034b-e54f-434a-8cbf-e967154af983)

These specialized solvers are used internally by [ovos-common-query-pipeline-plugin](https://github.com/OpenVoiceOS/ovos-common-query-pipeline-plugin), some skills and even by other question solver plugins!

Example configuration of [ovos-flashrank-reranker-plugin](https://github.com/TigreGotico/ovos-flashrank-reranker-plugin) for usage with `ovos-common-query-pipeline-plugin`

```json
"intents": {
    "common_query": {
        "min_self_confidence": 0.5,
        "min_reranker_score": 0.5,
        "reranker": "ovos-flashrank-reranker-plugin",
        "ovos-flashrank-reranker-plugin": {
          "model": "ms-marco-TinyBERT-L-2-v2"
        }
    }
}
```

---

## Collaborative Agents via MoS (Mixture of Solvers)

One of the most powerful features of the OVOS solver architecture is its ability to **orchestrate multiple agents collaboratively** through specialized **Mixture of Solvers (MoS)** plugins.

![image](https://gist.github.com/user-attachments/assets/a1ef9307-0680-4fb0-9616-0ecd8332ae73)

These [MoS solvers](https://github.com/TigreGotico/ovos-MoS) implement strategies that combine the strengths of various LLMs, rerankers, rule-based solvers, or even remote agents (like HiveMind nodes), allowing dynamic delegation and refinement of answers.

> 🤝 **Flexible Plugin Design**: MoS strategies are implemented as standard solver plugins. This means they can be composed, nested, or swapped just like any other solver—allowing advanced collaborative behavior with minimal integration effort.

### How It Works

Instead of relying on a single model or backend, a MoS solver delegates the query to several specialized solvers (workers) and uses strategies like voting, reranking, or even further generation to decide the best final response.

Examples include:

- **The King**: Uses a central "king" (reranker or LLM) to select or generate the best answer based on multiple solver outputs.
- **Democracy**: Implements a voting system among reranker solvers to choose the most agreed-upon response.
- **Duopoly**: A pair of collaborating LLMs discusses worker answers before passing them to a final decider (a president solver).

> 🌀 **Recursive Composition**: Any MoS strategy can recursively use another MoS as a sub-solver, allowing for arbitrarily deep collaboration trees.


Each strategy enables different dynamics between solvers—be it a single judge, a voting panel, or a back-and-forth discussion between agents.

---

## OVOS as a Solver Plugin

An advanced trick: **`ovos-core` itself can act as a solver plugin**. This allows you to expose OVOS itself as an agent to other applications in localhost

![Untitled-2025-04-15-2340(3)](https://github.com/user-attachments/assets/8022ff8a-5847-4bd7-93eb-316830ae7849)


- 🐳 Good for chaining OVOS instances in docker.
- 🦾 Use skills in a collaborative AI / MoS (mixture-of-solvers) setup.
- ❌ `ovos-bus-solver-plugin` makes **no sense inside a local persona** (infinite loop!), but is **great for standalone usage**.
- 🌐 Expose OVOS behind HTTP api via `ovos-persona-server` without exposing the messagebus directly 

```json
{
  "name": "Open Voice OS",
  "solvers": [
    "ovos-solver-bus-plugin",
    "ovos-solver-failure-plugin"
  ],
  "ovos-solver-bus-plugin": {
    "autoconnect": true,
    "host": "127.0.0.1",
    "port": 8181
  }
}
```

> 💡 if you are looking to access OVOS remotely or expose it as a service see [hivemind agents documentation](https://openvoiceos.github.io/ovos-technical-manual/152-hivemind-agents/) for a more secure alternative

---

## Summary Table

| Component            | Role                                                         |
|----------------------|--------------------------------------------------------------|
| **Solver Plugin**    | Stateless text-to-text inference (e.g., Q&A, summarization). |
| **Persona**          | Named agent composed of ordered solver plugins.              |
| **Persona Server**   | Expose personas to other Ollama/OpenAI compatible projects.  |
| **Persona Pipeline** | Handles persona activation and routing inside OVOS core.     |


By decoupling solvers, personas, and persona management, OVOS allows for powerful, customizable AI experiences, adaptable to both voice and text interactions across platforms.
