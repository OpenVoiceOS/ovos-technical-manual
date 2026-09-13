# OVOS Intent Pipeline

The OpenVoiceOS (OVOS) Intent Pipeline is a modular and extensible system designed to interpret user utterances and map
them to appropriate actions or responses.

It orchestrates various intent parsers and fallback mechanisms to ensure accurate and contextually relevant responses.

---

## What is an Intent Pipeline?

An intent pipeline in OVOS is a sequence of processing stages that analyze user input to determine the user's intent.
Each stage employs different strategies, ranging from high-confidence intent parsers to fallback mechanisms, to
interpret the input.

This layered approach ensures that OVOS can handle a wide range of user queries with varying degrees of specificity and
complexity.

---

## Pipeline Structure

OVOS pipelines are structured to prioritize intent matching based on confidence levels:

* **High Confidence**: Primary intent parsers that provide precise matches.
* **Medium Confidence**: Secondary parsers that handle less specific queries.
* **Low Confidence**: Fallback mechanisms for ambiguous or unrecognized inputs.

Each component in the pipeline is a plugin that can be enabled, disabled, or reordered according to user preferences.
This flexibility allows for customization based on specific use cases or device capabilities.

---

## Available Pipeline Components

Below is a list of available pipeline components, categorized by their confidence levels and functionalities:

### High Confidence Components

| Pipeline                            | Description                                                     | Notes                                                                      |   
|-------------------------------------|-----------------------------------------------------------------|----------------------------------------------------------------------------| 
| `stop_high`                         | Exact match for stop commands                                   | Replaces [skill-ovos-stop](https://github.com/OpenVoiceOS/skill-ovos-stop) |                                                                                                
| `converse`                          | Continuous conversation interception for skills                 |                                                                            |                                                                                                
| `padatious_high`                    | High-confidence matches using Padatious                         |                                                                            |                                                                                                
| `adapt_high`                        | High-confidence matches using Adapt                             |                                                                            |                                                                                                
| `fallback_high`                     | High-priority fallback skill matches                            |                                                                            |                                                                                                
| `ocp_high`                          | High-confidence media-related queries                           |                                                                            |                                                                                                
| `ovos-persona-pipeline-plugin-high` | Active persona conversation (e.g., LLM integration)             |                                                                            |                                                                                       
| `ovos-m2v-pipeline-high`            | Multilingual intent classifier capable of handling paraphrasing | Only supports **default skills**, not dynamic                              |

### Medium Confidence Components

| Pipeline                   | Description                                                     | Notes                                                                      |                                         
|----------------------------|-----------------------------------------------------------------|----------------------------------------------------------------------------| 
| `stop_medium`              | Medium-confidence stop command matches                          | Replaces [skill-ovos-stop](https://github.com/OpenVoiceOS/skill-ovos-stop) |                                         
| `padatious_medium`         | Medium-confidence matches using Padatious                       |                                                                            |                                         
| `adapt_medium`             | Medium-confidence matches using Adapt                           |                                                                            |                                         
| `ocp_medium`               | Medium-confidence media-related queries                         |                                                                            |                                         
| `fallback_medium`          | Medium-priority fallback skill matches                          |                                                                            |                                                                                  
| `ovos-m2v-pipeline-medium` | Multilingual intent classifier capable of handling paraphrasing | Only supports **default skills**, not dynamic                              |

### Low Confidence Components

| Pipeline                           | Description                                                     | Notes                                         |  
|------------------------------------|-----------------------------------------------------------------|-----------------------------------------------| 
| `stop_low`                         | Low-confidence stop command matches                             | Disabled by default                           |  
| `padatious_low`                    | Low-confidence matches using Padatious                          | Often inaccurate; disabled by default         |   
| `adapt_low`                        | Low-confidence matches using Adapt                              |                                               |   
| `ocp_low`                          | Low-confidence media-related queries                            |                                               |   
| `fallback_low`                     | Low-priority fallback skill matches                             |                                               |   
| `common_query`                     | Sends utterance to common_query skills                          | Selects the best match among available skills |   
| `ovos-persona-pipeline-plugin-low` | Persona catch-all fallback (e.g., LLM integration)              |                                               |                                                                                  
| `ovos-m2v-pipeline-low`            | Multilingual intent classifier capable of handling paraphrasing | Only supports **default skills**, not dynamic | 

---

## Customizing the Pipeline

OVOS allows users to customize the intent pipeline through configuration files. Users can enable or disable specific
components, adjust their order, and set confidence thresholds to tailor the system's behavior to their needs. This
customization ensures that OVOS can be optimized for various applications, from simple command recognition to complex
conversational agents.

```json
  "intents": {
    "adapt": {
      "conf_high": 0.5,
      "conf_med": 0.3,
      "conf_low": 0.2
    },
    "persona": {
      "handle_fallback": true,
      "default_persona": "Remote Llama"
    },
    "pipeline": [
      "ovos-m2v-pipeline-high",
      "ocp_high",
      "stop_high",
      "converse",
      "padatious_high",
      "adapt_high",
      "stop_medium",
      "adapt_medium",
      "common_qa",
      "fallback_medium",
      "fallback_low"
    ]
  },
```

---

By understanding and configuring the OVOS Intent Pipeline, developers and users can enhance the accuracy and
responsiveness of their voice assistant applications.
