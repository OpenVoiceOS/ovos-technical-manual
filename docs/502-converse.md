# Converse

Each Skill may define a `converse()` method. This method will be called anytime the Skill has been recently active and a new utterance is processed.&#x20;

The converse method expects a single argument which is a standard Mycroft Message object. This is the same object an intent handler receives.

Converse methods must return a Boolean value. True if an utterance was handled, otherwise False.

## Basic usage

Let's use a version of the Ice Cream Skill we've been building up and add a converse method to catch any brief statements of thanks that might directly follow an order.

```python
from ovos_workshop.skills import OVOSSkill
from ovos_workshop.decorators import intent_handler


class IceCreamSkill(OVOSSkill):
    def initialize(self):
        self.flavors = ['vanilla', 'chocolate', 'mint']

    @intent_handler('request.icecream.intent')
    def handle_request_icecream(self):
        self.speak_dialog('welcome')
        selection = self.ask_selection(self.flavors, 'what.flavor')
        self.speak_dialog('coming-right-up', {'flavor': selection})

    def converse(self, message):
        if self.voc_match(message.data['utterances'][0], 'Thankyou'):
            self.speak_dialog("you-are-welcome")
            return True
```

In this example:

1. A User might request an ice cream which is handled by `handle_request_icecream()`&#x20;
2. The Skill would be added to the system Active Skill list for up to 5 minutes.
3. Any utterance received by OVOS would trigger this Skills converse system whilst it is considered active.
4. If the User followed up with a pleasantry such as "Hey Mycroft, thanks" - the converse method would match this vocab against the `Thankyou.voc` file in the Skill and speak the contents of the `you-are-welcome.dialog` file. The method would return `True` and the utterance would be consumed meaning the intent parsing service would never be triggered.
5. Any utterance that did not match would be silently ignored and allowed to continue on to other converse methods and finally to the intent parsing service.


> **WARNING** skills that are not [Session](https://openvoiceos.github.io/ovos-technical-manual/bus_service/#session) aware may behave weirdly with voice satellites, see the [parrot skill](https://github.com/OpenVoiceOS/skill-ovos-parrot/) for an example.


## Active Skill List

A Skill is considered active if it has been called in the last 5 minutes.

Skills are called in order of when they were last active. For example, if a user spoke the following commands:

> Hey Mycroft, set a timer for 10 minutes
>
> Hey Mycroft, what's the weather

Then the utterance "what's the weather" would first be sent to the Timer Skill's `converse()` method, then to the intent service for normal handling where the Weather Skill would be called.

As the Weather Skill was called it has now been added to the front of the Active Skills List. Hence, the next utterance received will be directed to:

1. `WeatherSkill.converse()`
2. `TimerSkill.converse()`
3. Normal intent parsing service

When does a skill become active?

1. **before** an intent is called the skill is **activated**
2. if a fallback **returns True** (to consume the utterance) the skill is **activated** right **after** the fallback
3. if converse **returns True** (to consume the utterance) the skill is **reactivated** right **after** converse
4. a skill can activate/deactivate itself at any time

## Making a Skill Active

There are occasions where a Skill has not been triggered by the User, but it should still be considered "Active".

In the case of our Ice Cream Skill - we might have a function that will execute when the customers order is ready. 
At this point, we also want to be responsive to the customers thanks, so we call `self.activate()` to manually add our Skill to the front of the Active Skills List.

```python
from ovos_workshop.skills import OVOSSkill
from ovos_workshop.decorators import intent_handler


class IceCreamSkill(OVOSSkill):
    def on_order_ready(self, message):
        self.activate()
        
    def handle_activate(self, message: Message):
        """
        Called when this skill is considered active by the intent service;
        converse method will be called with every utterance.
        Override this method to do any optional preparation.
        @param message: `{self.skill_id}.activate` Message
        """
        LOG.info("Skill has been activated")
```

## Deactivating a Skill

The active skill list will be pruned by `ovos-core`, any skills that have not been interacted with for longer than 5 minutes will be deactivated

Individual Skills may react to this event, to clean up state or, in some rare cases, to reactivate themselves

```python
from ovos_workshop.skills import OVOSSkill


class AlwaysActiveSkill(OVOSSkill):

    def handle_deactivate(self, message: Message):
        """
        Called when this skill is no longer considered active by the intent
        service; converse method will not be called until skill is active again.
        Override this method to do any optional cleanup.
        @param message: `{self.skill_id}.deactivate` Message
        """
        self.activate()
```

A skill can also deactivate itself at any time

```python
from ovos_workshop.skills import OVOSSkill


class LazySkill(OVOSSkill):

    def handle_intent(self, message: Message):
        self.speak("leave me alone")
        self.deactivate()
```

## Conversational Intents

**NEW** in `ovos-core` version **0.0.8**

Skills can have extra intents valid while they are active, those are internal and not part of the main intent system, instead each skill checks them BEFORE calling `converse`

the `@conversational_intent` decorator can be used to define converse intent handlers

these intents only trigger after an initial interaction, essentially they are only follow up questions

```python
class DogFactsSkill(OVOSSkill):

    @intent_handler("dog_facts.intent")
    def handle_intent(self, message):
        fact = "Dogs sense of smell is estimated to be 100,000 times more sensitive than humans"
        self.speak(fact)

    @conversational_intent("another_one.intent")
    def handle_followup_question(self, message):
        fact2 = "Dogs have a unique nose print,  making each one distinct and identifiable."
        self.speak(fact2)
```
> **NOTE**: Only works with `.intent` files, Adapt/Keyword intents are NOT supported

A more complex example, a game skill that allows saving/exiting the game only during playback

```python
class MyGameSkill(OVOSSkill):

    @intent_handler("play.intent")
    def handle_play(self, message):
        self.start_game(load_save=True)

    @conversational_intent("exit.intent")
    def handle_exit(self, message):
        self.exit_game()

    @conversational_intent("save.intent")
    def handle_save(self, message):
        self.save_game()
        
    def handle_deactivate(self, message):
        self.game_over() # user abandoned interaction
        
    def converse(self, message):
        if self.playing:
            # do some game stuff with the utterance
            return True
        return False
```

> **NOTE**:  if these intents trigger, they are called **INSTEAD** of `converse`


## Security

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
