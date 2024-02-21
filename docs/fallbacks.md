# Fallback Skill

## Order of precedence

The Fallback **Skills** all have a priority and will be checked in order from low priority value to high priority value. 
If a Fallback **Skill** can handle the **Utterance** it will create a response and return `True`. 

After this no other Fallback **Skills** are tried. This means the priority for Fallbacks that can handle a _broad_ range of queries should be _high_ \(80-100\) and Fallbacks that only responds to a very specific range of queries should be higher \(20-80\). The more specific, the lower the priority value.

##  Fallback Handlers

Import the `FallbackSkill` base class:

```python
from ovos_workshop.skills.fallback import FallbackSkill
```

Create a derived class and register the handler with the fallback system


```python
class MeaningFallback(FallbackSkill):
    """
        A Fallback skill to answer the question about the
        meaning of life, the universe and everything.
    """
    def initialize(self):
         """
             Registers the fallback handler
         """
         self.register_fallback(self.handle_fallback, 10)
         # Any other initialize code you like can be placed here
```

> **NOTE**: a `FallbackSkill` can register any number of fallback handlers

Implement the fallback handler \(the method that will be called to potentially handle the **Utterance**\). The method implements logic to determine if the **Utterance** can be handled and shall output speech if itcan handle the query. It shall return Boolean `True` if the **Utterance** was handled and Boolean `False` if not.

```python
    def handle_fallback(self, message):
        """
            Answers question about the meaning of life, the universe
            and everything.
        """
        utterance = message.data.get("utterance")
        if 'what' in utterance
            and 'meaning' in utterance
            and ('life' in utterance
                or 'universe' in utterance
                or 'everything' in utterance):
            self.speak('42')
            return True
        else:
            return False
```

The above example can be found [here](https://github.com/forslund/fallback-meaning).


## Check utterances

**NEW** - `ovos-core` version **0.0.8** 

Fallback skills should report if they are able to answer a question, without actually executing any action.

Besides providing performance improvements this allows other OVOS components to check how a utterance will be handled without side effects

```python
    def can_answer(self, utterances: List[str], lang: str) -> bool:
        """
        Check if the skill can answer the particular question. Override this
        method to validate whether a query can possibly be handled. By default,
        assumes a skill can answer if it has any registered handlers
        @param utterances: list of possible transcriptions to parse
        @param lang: BCP-47 language code associated with utterances
        @return: True if skill can handle the query
        """
        return len(self._fallback_handlers) > 0
```

## Security

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
