# ovos-core

[OpenVoiceOS](https://openvoiceos.org) is an open source platform for smart speakers and other voice-centric devices.

OpenVoiceOS is fully modular. Furthermore, common components have been repackaged as plugins. That means it isn't just a
great assistant on its own, but also a pretty small library!

`ovos-core` contains "the brains" of OpenVoiceOS, all the NLP components and skills are managed here

## Skills Service

The skills service is responsible for loading skills and intent parsers

All user queries are handled by the skills service, you can think of it as OVOS's brain

All Mycroft Skills should work normally with `ovos-core` until version **0.1.0**, after that modernization is required!

Under OpenVoiceOS skills are regular python packages, any installed skills will be loaded automatically by `ovos-core`

Since `ovos-core` **0.0.8** it is also possible to launch a skill standalone via `ovos-workshop`, this enables individual skill containers in [ovos-docker](https://openvoiceos.github.io/ovos-docker)

This can be also be helpful during skill development for quick testing before the skill is packaged

```bash
ovos-skill-launcher {skill_id} [path/to/my/skill_id]
```

## Configuration

```javascript
"skills": {

    // blacklisted skills to not load
    // NB: This is the skill_id, usually the basename() of the directory where the skill lives, so if
     // the skill you want to blacklist is in /usr/share/mycroft/skills/mycroft-alarm.mycroftai/
    // then you should write `["mycroft-alarm.mycroftai"]` below.
    "blacklisted_skills": [],

    // fallback skill configuration (see below)
    "fallbacks": {...},

    // converse stage configuration (see below)
    "converse": {...}

},
```

## Utterance Transformers

**NEW** in `ovos-core` version **0.0.8**, originally developed for [Neon](https://github.com/NeonGeckoCom/neon-transformers)

when `ovos-core` receives a natural language query/ `utterance` from a user it is sent to a "preprocessing stage"

The `utterance` transformers framework consists of any number of plugins ordered by priority (developer defined), the `utterance` and
message.`context` are sent sequentially to all transformer plugins, and can be mutated by any of those plugins

![imagem](https://github.com/OpenVoiceOS/ovos-technical-manual/assets/33701864/75b67a91-dd8d-48cd-a146-91988bbbf374)

to enable a utterance transformer simply add it to `mycroft.conf` after installing it

```javascript
// To enable a utterance transformer plugin just add it's name with any relevant config
// these plugins can mutate the utterance between STT and the Intent stage
// they may also modify message.context with metadata
// plugins only load if they are installed and enabled in this section
"utterance_transformers": {

 "ovos-utterance-normalizer": {},

 // cancel utterances mid command
 "ovos-utterance-plugin-cancel": {},
  
 // define utterance fixes via fuzzy match ~/.local/share/mycroft/corrections.json
 // define unconditional replacements at word level ~/.local/share/mycroft/word_corrections.json
 "ovos-utterance-corrections-plugin": {},
 
 // translation plugin
 "ovos-utterance-translation-plugin": {
   "bidirectional": true,
   "verify_lang": false,
   "ignore_invalid": true,
   "translate_secondary_langs": false
 }
},
```

## Metadata Transformers

**NEW** in `ovos-core` version **0.0.8**

Similar to utterance transformers, these plugins only transform the `message.context`

```javascript
// To enable a metadata transformer plugin just add it's name with any relevant config
// these plugins can mutate the message.context between STT and the Intent stage
"metadata_transformers": {},
```

## Intent Pipelines

**NEW** in `ovos-core` version **0.0.8**

after the `utterance` has been transformed it is sent to various OVOS components by priority order until one can handle
the query

Pipelines include intent parsers, converse framework, common query framework and fallback skill framework

```javascript
  // Intent Pipeline / plugins config
  "intents" : {
    // the pipeline is a ordered set of frameworks to send an utterance too
    // if one of the frameworks fails the next one is used, until an answer is found
    // NOTE: if padatious is not installed, it will be replaced with padacioso (much slower)
    // in the future these will become plugins, and new pipeline stages can be added by end users
    "pipeline": [
        "ocp_high",
        "stop_high",
        "converse",
        "padatious_high",
        "adapt_high",
        "fallback_high",
        "stop_medium",
        "adapt_medium",
        "ovos-persona-pipeline-plugin-high",
        "adapt_low",
        "common_qa",
        "fallback_medium",
        "ovos-persona-pipeline-plugin-low",
        "fallback_low"
    ]
  },
```
