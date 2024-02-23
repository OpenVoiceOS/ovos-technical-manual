# Persona Server

**EXPERIMENTAL** - **NEW** `ovos-core` version **0.0.8**

The Persona initiative is a work in progress, it can be integrated
via [persona-server](https://github.com/OpenVoiceOS/ovos-persona-server/)
and [ChatGPT skill](https://github.com/OpenVoiceOS/skill-ovos-fallback-chatgpt/) as a developer preview

Personas give personality to OVOS, they can be anything from classic chatbots to state of the art language models!

Persona server follows the standard OpenAI API, also allowing you to connect personas to existing ChatGPT applications

## Public Servers

| Member                                                                                                                                                        |                                     Solvers / LLM model                                     | Native Language |                                URL                                |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------:|:---------------:|:-----------------------------------------------------------------:|
| <div><a href="https://github.com/goldyfruit"><img src="https://avatars.githubusercontent.com/u/614115?v=4" alt="goldyfruit" width="25" height="25"></a></div> | [mistral-7b-openorca.Q4_K_M.gguf](https://huggingface.co/TheBloke/Mistral-7B-OpenOrca-GGUF) |     english     | <div>[https://llama.smartgic.io](https://llama.smartgic.io)</div> |


## Companion plugin

Use with [ovos-solver-plugin-openai-persona](https://github.com/OpenVoiceOS/ovos-solver-plugin-openai-persona)

## Personas

A Persona is defined by a combination
of [question solver plugins](https://openvoiceos.github.io/ovos-technical-manual/solvers/), the solvers are tried
sequentially until one succeeds in answering the question similarly to fallback skills

some repos and skills also provide solvers, such as ovos-classifiers (wordnet), skill-ddg, skill-wikipedia and
skill-wolfie

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

search/knowledge base solvers can be used together with chatbot solvers to ensure factual answers and act as a tool/internet
access layer, for classic chatbots the example above serves as a rudimentary implementation of tool use

Chatbots are a nice toy and for chit-chat work well as a fallback if internet is not available, but you most likely want to connect a LLM to persona, be it remote or local

## LLMs

A basic example connecting to ChatGPT or compatible LLM (or a remote persona!)

```
"name": "ChatGPT",
"solvers": [
    "ovos-solver-openai-persona-plugin"
],
"ovos-solver-openai-persona-plugin": {
    "api_url": "<OpenAI_or_compatible_server_url>",
    "key": "<your_OpenAI_key>",
    "persona": "helpful, creative, clever, and very friendly."
}
```

> **NOTE**: In the config above "persona" sets the system prompt, this is specific to the solver plugin

Wrapping your LLM in a solver plugin provides you [Bidirectional translation](https://openvoiceos.github.io/ovos-technical-manual/lang_plugins), even if your LLM is language specific you are able to use it in any language

RAG and tool use for LLMs is better implemented as part of a dedicated solver plugin, LLM solvers may also use other solvers internally as agents/tools for LLMs, 

## Running

`$ ovos-persona-server --persona rivescript_bot.json`

## OpenAI API compatible

OpenAI compatible API, can be used with hundreds of projects that support it

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

## Compatible Projects

The OpenAI API ended up becoming a sort of standard, [ovos-solver-plugin-openai-persona](https://github.com/OpenVoiceOS/ovos-solver-plugin-openai-persona) can not only connect to ChatGPT, but is also compatible with public servers for several projects

- [oobabooga/text-generation-webui](https://github.com/oobabooga/text-generation-webui/wiki/12-%E2%80%90-OpenAI-API)
- [llama.cpp](https://github.com/ggerganov/llama.cpp/blob/master/examples/server/README.md)
- [LocalAI](https://github.com/mudler/LocalAI)
- [Xinference](https://github.com/xorbitsai/inference)
- [RayLLM](https://github.com/ray-project/ray-llm)
- [OpenLLM](https://github.com/bentoml/OpenLLM)
- [FastChat](https://github.com/lm-sys/FastChat)
- [gatogpt](https://github.com/elgatopanzon/gatogpt)
- [litellm](https://github.com/BerriAI/litellm)
- [nitro](https://github.com/janhq/nitro)
- [modelz-llm](https://github.com/tensorchord/modelz-llm)
- [llama-api](https://github.com/c0sogi/llama-api)
- [tabbyAPI](https://github.com/theroyallab/tabbyAPI)
- [basaran](https://github.com/hyperonym/basaran)
- ... and many more
