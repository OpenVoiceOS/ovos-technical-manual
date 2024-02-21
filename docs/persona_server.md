# Persona Server

**EXPERIMENTAL** - **NEW** `ovos-core` version **0.0.8**

The Persona initiative is a work in progress, it can be integrated via [persona-server](https://github.com/OpenVoiceOS/ovos-persona-server/) and [ChatGPT skill](https://github.com/OpenVoiceOS/skill-ovos-fallback-chatgpt/) as a developer preview

## Running

`$ ovos-persona-server --persona rivescript_bot.json`

## Personas

A Persona is defined by a combination of [question solver plugins](https://openvoiceos.github.io/ovos-technical-manual/solvers/), the solvers are tried sequentially until one succeeds in answering the question similarly to fallback skills

personas don't need to use LLMs, you don't need a beefy GPU to use ovos-persona

some repos and skills also provide solvers, such as ovos-classifiers (wordnet), skill-ddg, skill-wikipedia and skill-wolfie

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
  "ovos-solver-plugin-wolfram-alpha": {"appid": "Y7353-9HQAAL8KKA"}
}
```

this persona would search ddg api / wikipedia for "what is"/"tell me about" questions,
falling back to wordnet when offline for dictionary look up,
and finally rivescript for general chitchat,
we also add the failure solver to be sure the persona always says something

wolfram alpha illustrates how to pass solver configs, it has a requirement for an API key

search/knowledge base solvers can be used together with LLM solvers to ensure factual answers and act as a tool/internet access layer,
in the example above you would typically replace rivescript with a LLM.

Some solvers may also use other solvers internally, such as a tool implementation for other LLMs

## Client side usage

OpenAI compatible API, for usage with OVOS see [ovos-solver-plugin-openai-persona](https://github.com/OpenVoiceOS/ovos-solver-plugin-openai-persona)

```python
import openai

openai.api_key = ""
openai.api_base = "http://localhost:8337"

# NOTE - most solvers don't support a chat history,
#  only last message in messages list is considered
chat_completion = openai.ChatCompletion.create(
    model="",  # individual personas might support this, passed under context
    messages=[{"role": "user", "content": "tell me a joke"}],
    stream=False,
)

if isinstance(chat_completion, dict):
    # not stream
    print(chat_completion.choices[0].message.content)
else:
    # stream
    for token in chat_completion:
        content = token["choices"][0]["delta"].get("content")
        if content != None:
            print(content, end="", flush=True)

```