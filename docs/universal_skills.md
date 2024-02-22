# UniversalSkill

The `UniversalSkill` class is designed to facilitate automatic translation of input and output messages between different languages. 

This skill is particularly useful when native language support is not feasible, providing a convenient way to handle multilingual interactions.

> **NEW** - `ovos-core` version **0.0.8**

## Overview

This skill ensures that intent handlers receive utterances in the skill's internal language and are expected to produce responses in the same internal language. 

The `speak` method, used for generating spoken responses, automatically translates utterances from the internal language to the original query language.

> **NOTE:** The `self.lang` attribute reflects the original query language, while received utterances are always in `self.internal_language`.

## Language Plugins

To run `UniversalSkills` you need to configure [Translation plugins](https://openvoiceos.github.io/ovos-technical-manual/lang_plugins.md) in `mycroft.conf`

```javascript
  // Translation plugins
  "language": {
    // default plugin comes bundled with ovos-classifiers
    // TODO - better plugin + servers
    "detection_module": "ovos-lang-detect-ngram-lm",
    
    // default uses public servers for nllb
    // https://github.com/OpenVoiceOS/ovos-translate-server
    // https://github.com/OpenVoiceOS/ovos-translate-plugin-nllb
    "translation_module": "ovos-translate-plugin-server"
  },
```

## Usage

### Initialization

```python
# Example initialization
from ovos_workshop.skills.auto_translatable import UniversalSkill

class MyMultilingualSkill(UniversalSkill):
    """
    Skill that auto translates input/output from any language

    This skill is designed to automatically translate input and output messages
    between different languages. The intent handlers are ensured to receive
    utterances in the skill's internal language, and they are expected to produce
    utterances in the same internal language.

    The `speak` method will always translate utterances from the internal language
    to the original query language (`self.lang`).

    NOTE: `self.lang` reflects the original query language, but received utterances
          are always in `self.internal_language`.
    """
    def __init__(self, *args, **kwargs):
        """
        Initialize the UniversalSkill.

        Parameters for super():
        - internal_language (str): The language in which the skill internally operates.
        - translate_tags (bool): Whether to translate the private __tags__ value (adapt entities).
        - autodetect (bool): If True, the skill will detect the language of the utterance
                            and ignore self.lang / Session.lang.
        - translate_keys (list): default ["utterance", "utterances"] 
                                 Keys added here will have values translated in message.data.
        """
        # skill hardcoded in portuguese
        super().__init__(internal_language="pt-pt", translate_tags=translate_tags,
                         autodetect=autodetect, translate_keys=translate_keys, *args, **kwargs)
```

### Intents and Utterances

Use the `register_intent` and `register_intent_file` methods to register intents with universal intent handlers. The usual decorators also work

The `speak` method is used to generate spoken responses.
It automatically translates utterances if the output language is different from the skill's internal language or autodetection is enabled.

```python
# Example speaking utterance, hardcoded to self.internal_language
self.speak("Hello, how are you?")
```

### Universal Intent Handler

> **NOTE** Users should NOT use the `create_universal_handler` method manually in skill intents; it is automatically utilized by `self.register_intent`. 

The following example demonstrates its usage with `self.add_event`.

```python
# Example universal handler creation
def my_event_handler(message):
    # Your event handling logic here
    pass

# Manual usage with self.add_event
my_handler = self.create_universal_handler(my_event_handler)
self.add_event("my_event", my_handler)
```

## EnglishCatFacts Skill Example

Let's create a simple tutorial skill that interacts with an API to fetch cat facts in English. 

We'll use the `UniversalSkill` class to support translations for other languages.

```python
from ovos_workshop.skills.auto_translatable import UniversalSkill


class EnglishCatFactsSkill(UniversalSkill):
    def __init__(self, *args, **kwargs):
        """
        This skill is hardcoded in english, indicated by internal_language
        """
        super().__init__(internal_language="en-us", *args, **kwargs)
        
    def fetch_cat_fact(self):
        # Your logic to fetch a cat fact from an API
        cat_fact = "Cats have five toes on their front paws but only four on their back paws."
        return cat_fact

    @intent_handler("cat_fact.intent")
    def handle_cat_fact_request(self, message):
        # Fetch a cat fact in self.internal_language
        cat_fact = self.fetch_cat_fact()
        # Speak the cat fact, it will be translated to self.lang if needed
        self.speak(cat_fact)
```

In this example, the `CatFactsSkill` class extends `UniversalSkill`, allowing it to seamlessly translate cat facts into the user's preferred language.



## SpanishDatabase Skill Example

A more advanced example, let's consider a skill that listens to bus messages.

Our skill listens for messages containing a `"phrase"` payload in message.data that can be in any language, and it saves this phrase *in spanish* to a database. 
Then it speaks a hardcoded spanish utterance, and it gets translated into the language of the bus message Session

```python
from ovos_workshop.skills.auto_translatable import UniversalSkill

class SpanishDatabaseSkill(UniversalSkill):
    def __init__(self, *args, **kwargs):
        """
        This skill is hardcoded in spanish, indicated by internal_language
        """
        translate_keys=["phrase"] # translate "phrase" in message.data
        super().__init__(internal_language="es-es",
                         translate_keys=translate_keys,
                         *args, **kwargs)
    
    def initialize(self):
        # wrap the event into a auto translation layer
        handler = self.create_universal_handler(self.handle_entry)
        self.add_event("skill.database.add", handler)
        
    def handle_entry(self, message: Message):
        phrase = message.data["phrase"]  # assured to be in self.internal_language
        
        # Your logic to save phrase to a database
        
        self.speak("agregado a la base de datos") # will be spoken in self.lang
```
