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

when `ovos-core` receives a natural language query/ `utterance` from a user it is sent to a "preprocessing stage"

The `utterance` transformers framework consists of any number of plugins ordered by priority, the `utterance` and
message.`context` can the be mutated by any of those plugins

utterance transformers have been introduced in `ovos-core` **0.0.8**, originally developed for [Neon](https://github.com/NeonGeckoCom/neon-transformers)


| plugin                            | description                           | source                                                                                                                          |
|-----------------------------------|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| ovos-utterance-normalizer         | normalizes text before pipeline stage | [OpenVoiceOS/ovos-classifiers](https://github.com/OpenVoiceOS/ovos-classifiers/blob/dev/ovos_classifiers/opm/heuristics.py#L41) |
| ovos-utterance-plugin-cancel      | cancel an utterance mid transcription | [OpenVoiceOS/ovos-utterance-plugin-cancel](https://github.com/OpenVoiceOS/ovos-utterance-plugin-cancel)                         |
| ovos-utterance-corrections-plugin | manually correct bad transcriptions   | [OpenVoiceOS/ovos-utterance-corrections-plugin](https://github.com/OpenVoiceOS/ovos-utterance-corrections-plugin)               |
| ovos-utterance-translation-plugin | auto translate unsupported languages  | [OpenVoiceOS/ovos-bidirectional-translation-plugin](https://github.com/OpenVoiceOS/ovos-bidirectional-translation-plugin)       |


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
 "ovos-utterance-corrections-plugin": {}
},
```

## Metadata Transformers

Similar to utterance transformers, these plugins only transform the `message.context`

```javascript
// To enable a metadata transformer plugin just add it's name with any relevant config
// these plugins can mutate the message.context between STT and the Intent stage
"metadata_transformers": {},
```

## Pipelines

after the `utterance` has been transformed it is sent to various OVOS components by priority order until one can handle
the query

Pipelines include intent parsers, converse framework, common query framework and fallback skill framework

Most pipeline components will provide a high/medium/low variety with different confidence levels, but this is not
required

| pipeline         | description                                                 |
|------------------|-------------------------------------------------------------|
| stop_high        | stop command exact matches                                  |
| converse         | continuous conversation interception for skills             |
| padatious_high   | high confidence intent matches from padatious               |
| adapt_high       | high confidence intent matches from adapt                   |
| fallback_high    | high priority fallback skill matches                        |
| stop_medium      | medium confidence stop matches                              |
| padatious_medium | medium confidence intent matches from padatious             |
| adapt_medium     | medium confidence intent matches from adapt                 |
| adapt_low        | low confidence intent matches from adapt                    |
| common_query     | send utterance to common_query skills and select best match |
| fallback_medium  | medium priority fallback skill matches                      |
| stop_low         | low confidence stop matches                                 |
| fallback_low     | low priority fallback skill matches                         |

The concept of configurable pipelines has been introduced in `ovos-core` **0.0.8** and will be fully implemented as
plugins after version **0.1.0**

```javascript
  // Intent Pipeline / plugins config
  "intents" : {
    // the pipeline is a ordered set of frameworks to send an utterance too
    // if one of the frameworks fails the next one is used, until an answer is found
    // NOTE: if padatious is not installed, it will be replaced with padacioso (much slower)
    // in the future these will become plugins, and new pipeline stages can be added by end users
    "pipeline": [
        "stop_high",
        "converse",
        "padatious_high",
        "adapt_high",
        "fallback_high",
        "stop_medium",
        "padatious_medium",
        "adapt_medium",
        "adapt_low",
        "common_qa",
        "fallback_medium",
        "fallback_low"
    ]
  },
```
NOTE: the stop pipeline replaces [OpenVoiceOS/skill-ovos-stop](https://github.com/OpenVoiceOS/skill-ovos-stop)


### Converse

A malicious or badly designed skill using the converse method can potentially hijack the whole conversation loop and render the skills service unusable

Some settings are exposed to add some limitations to which skills can be activated and under what circumstances

The concept of "converse priority" is under active development

```javascript
"skills": {
    // converse stage configuration
    "converse": {
        // the default number of seconds a skill remains active,
        // if the user does not interact with the skill in this timespan it
        // will be deactivated, default 5 minutes (same as mycroft)
        "timeout": 300,
        
        // override of "skill_timeouts" per skill_id
        // you can configure specific skills to remain active longer
        "skill_timeouts": {},

        // conversational mode has 3 modes of operations:
        // - "accept_all"  # default mycroft-core behavior
        // - "whitelist"  # only call converse for skills in "converse_whitelist"
        // - "blacklist"  # only call converse for skills NOT in "converse_blacklist"
        "converse_mode": "accept_all",
        "converse_whitelist": [],
        "converse_blacklist": [],

        // converse activation has 4 modes of operations:
        // - "accept_all"  # default mycroft-core behavior, any skill can
        //                 # activate itself unconditionally
        // - "priority"  # skills can only activate themselves if no skill with
        //               # higher priority is active
        // - "whitelist"  # only skills in "converse_whitelist" can activate themselves
        // - "blacklist"  # only skills NOT in converse "converse_blacklist" can activate themselves
        // NOTE: this does not apply for regular skill activation, only to skill
        //       initiated activation requests, eg, self.make_active()
        "converse_activation": "accept_all",

        // number of consecutive times a skill is allowed to activate itself
        // per minute, -1 for no limit (default), 0 to disable self-activation
        "max_activations": -1,
        
        // override of "max_activations" per skill_id
        // you can configure specific skills to activate more/less often
        "skill_activations": {},

        // if false only skills can activate themselves
        // if true any skill can activate any other skill
        "cross_activation": true,

        // if false only skills can deactivate themselves
        // if true any skill can deactivate any other skill
        // NOTE: skill deactivation is not yet implemented
        "cross_deactivation": true,

        
        // you can add skill_id: priority to override the developer defined
        // priority of those skills, 
        
        // converse priority is work in progress and not yet exposed to skills
        // priority is assumed to be 50
        // the only current source for converse priorities is this setting
        "converse_priorities": {
           // "skill_id": 10
        }
    }

},
```

### Fallback Skills

Just like with converse a badly designed or malicious skill can hijack the fallback skill loop, while this is not as serious as with converse some protections are also provided

You can configure what skills are allowed to use the fallback mechanism, you can also modify the fallback priority to ensure skills behave well together. 

Since priority is defined by developers sometimes the default value is not appropriate and does not fit well with the installed skills collection

```javascript
"skills": {
    // fallback skill configuration
    "fallbacks": {
        // you can add skill_id: priority to override the developer defined
        // priority of those skills, this allows customization
        // of unknown intent handling for default_skills + user preferences
        "fallback_priorities": {
           // "skill_id": 10
        },
        // fallback skill handling has 3 modes of operations:
        // - "accept_all"  # default mycroft-core behavior
        // - "whitelist"  # only call fallback for skills in "fallback_whitelist"
        // - "blacklist"  # only call fallback for skills NOT in "fallback_blacklist"
        "fallback_mode": "accept_all",
        "fallback_whitelist": [],
        "fallback_blacklist": []
    }
},
```
