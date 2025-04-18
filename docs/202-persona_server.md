# OVOS Persona Server


The OVOS Persona Server makes any defined persona available through an API compatible with OpenAI and Ollama, allowing you to use OVOS personas as drop-in replacements for traditional large language models (LLMs) in other tools and platforms.

---

## Usage Guide

To start the Persona Server with a specific persona file:

```bash
$ ovos-persona-server --persona my_persona.json
```

This will launch a local server (default: `http://localhost:8337`) that exposes the persona via OpenAI and Ollama-compatible endpoints.

---

## Technical Explanation

A **persona** in OVOS is a predefined character or assistant configuration that can respond to user inputs, leveraging OVOS’s conversational tools. The **Persona Server** acts as a gateway that translates external API requests (like those from OpenAI or Ollama clients) into interactions with this persona.

This enables seamless integration with a variety of existing tools that expect LLM-like behavior, including frameworks, bots, or smart home assistants.

---

## OpenAI-Compatible API Example

You can use the `openai` Python SDK to interact with the Persona Server:

```python
import openai

openai.api_key = ""  # No API key required for local use
openai.api_base = "http://localhost:8337"

response = openai.ChatCompletion.create(
    model="",  # Optional: some personas may define specific models
    messages=[{"role": "user", "content": "tell me a joke"}],
    stream=False,
)

if isinstance(response, dict):
    # Non-streaming response
    print(response.choices[0].message.content)
else:
    # Streaming response
    for token in response:
        content = token["choices"][0]["delta"].get("content")
        if content:
            print(content, end="", flush=True)
```

🛈 **Note:** Some persona solvers are **not LLMs** and do **not** maintain chat history. Only the last message in the `messages` list is processed in some cases.

---

## Ollama-Compatible API

The server is also fully compatible with tools expecting an Ollama API.

For example, the [Home Assistant Ollama integration](https://www.home-assistant.io/integrations/ollama/) can connect directly to an OVOS Persona Server, treating it as a local LLM backend.

---

## Tips

- Make sure your persona file (`.json`) includes all the configuration details required by the solver or conversational backend.
- If using in a production setting, consider securing your endpoint and defining rate limits.
- Since personas can be highly customized, capabilities may vary depending on the persona used.

---

## Related Links

- [OVOS Personas](https://openvoiceos.github.io/ovos-technical-manual/150-personas/)
- [OpenAI Python SDK](https://github.com/openai/openai-python)
- [Home Assistant Ollama Integration](https://www.home-assistant.io/integrations/ollama/)