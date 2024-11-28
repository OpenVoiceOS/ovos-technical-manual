# Prompts

Here we look at how to implement the most common types of prompts. For more information on conversation design see
the [Voice User Interface Design Guidelines](https://mycroft-ai.gitbook.io/docs/skill-development/voice-user-interface-design-guidelines/interactions-and-guidelines/statements-and-prompts).

## Open Ended Questions

Sometimes after a Skill finishes speaking, we want to activate the microphone and let the utterance be handled as usual

It is possible to speak some dialog, and activate the listener, directing the response back to the standard intent parsing engine. We
may do this to let the user trigger another Skill, or because we want to make use of our own intents to handle the
response.

To do this, we use the `expect_response` parameter of `self.speak` and  `self.speak_dialog` methods expose a keyword argument to trigger listening once TTS has finished playback

```python
from ovos_workshop.skills import OVOSSkill
from ovos_workshop.decorators import intent_handler


class AskMeSkill(OVOSSkill):
    @intent_handler('ask_me_something.intent')
    def handle_set_favorite(self):
        question = random.choice(self.question_list)
        self.speak(question, expect_response=True)
```

## Request Extra Information

Any Skill can request extra information from the user before continuing - making a statement or asking a question before the microphone is
activated to record the User's response.

The base implementation of this is the `get_response()` method

To see it in action, let's create a simple Skill that asks the User what their favorite flavor of ice cream is.

```python
from ovos_workshop.skills import OVOSSkill
from ovos_workshop.decorators import intent_handler


class IceCreamSkill(OVOSSkill):
    @intent_handler('set.favorite.intent')
    def handle_set_favorite(self):
        favorite_flavor = self.get_response('what.is.your.favorite.flavor')
        self.speak_dialog('confirm.favorite.flavor', {'flavor': favorite_flavor})
```

In this Skill we have used `get_response()` and passed it the name of our dialog
file `'what.is.your.favorite.flavor.dialog'`. This is the simplest form of this method. It will speak dialog from the
given file, then activate the microphone for 3-10 seconds allowing the User to respond. The transcript of their response
will then be assigned to our variable `favorite_flavor`. To confirm that we have heard the User correctly we then speak
a confirmation dialog passing the value of `favorite_flavor` to be spoken as part of that dialog.

### Optional Arguments

The `get_response()` method also takes the following optional arguments:

* `data` (dict) - used to populate the dialog file, just like `speak_dialog()`
* `validator` (function) - returns a boolean to define whether the response meets some criteria for success
* `on_fail` (function) - returns a string that will be spoken if the validator returns False
* `num_retries` (int) - number of times the system should repeat the question to get a successful result

## Yes / No Questions

`ask_yesno()` checks if the response contains "yes" or "no" like phrases.

A longer phrase containing the required vocab is considered successful e.g. both "yes" and "yeah that would be great
thanks" would be considered a successful "yes".

If "yes" or "no" responses are detected, then the method will return the string "yes" or "no". If the response does not
contain "yes" or "no" vocabulary then the entire utterance will be returned. 

If no speech was detected indicating the User did not respond, or said something unexpected, then the method will return `None`.

`None` indicates an invalid answer, this tells the skill it should handle the error, such as by asking the question again

Let's add a new intent to our `IceCreamSkill` to see how this works.

```python
from ovos_workshop.skills import OVOSSkill
from ovos_workshop.decorators import intent_handler


class IceCreamSkill(OVOSSkill):
    @intent_handler('do.you.like.intent')
    def handle_do_you_like(self):
        likes_ice_cream = self.ask_yesno('do.you.like.ice.cream')
        if likes_ice_cream == 'yes':
            self.speak_dialog('does.like')
        elif likes_ice_cream == 'no':
            self.speak_dialog('does.not.like')
        else:
            self.speak_dialog('could.not.understand')
```

In this example we have asked the User if they like ice cream. We then speak different dialog whether they respond yes
or no. We also speak some error dialog if neither yes nor no are returned.

A dedicated parser for each language is provided by [ovos-lingua-franca](https://github.com/OpenVoiceOS/ovos-lingua-franca/blob/dev/lingua_franca/parse.py#L121),
ensuring we can understand nuances such as double negation

Some example sentences that would be considered either "yes" or "no". 

| Test Utterance                    | Expected Result |
|-----------------------------------|-----------------|
| "beans"                           | None            |
| "yes"                             | "yes"           |
| "no"                              | "no"            |
| "don't think so"                  | "no"            |
| "i think not"                     | "no"            |
| "that's affirmative"              | "yes"           |
| "no, but actually, yes"           | "yes"           |
| "yes, but actually, no"           | "no"            |
| "yes, yes, yes, but actually, no" | "no"            |
| "please"                          | "yes"           |
| "please don't"                    | "no"            |
| "no! please! I beg you"           | "no"            |
| "yes, i don't want it for sure"   | "no"            |
| "please! I beg you"               | "yes"           |
| "i want it for sure"              | "yes"           |
| "obviously"                       | "yes"           |
| "indeed"                          | "yes"           |
| "no, I obviously hate it"         | "no"            |
| "that's certainly undesirable"    | "no"            |
| "yes, it's a lie"                 | "yes"           |
| "no, it's a lie"                  | "no"            |
| "he is lying"                     | "no"            |
| "correct, he is lying"            | "yes"           |
| "it's a lie"                      | "no"            |
| "you are mistaken"                | "no"            |
| "that's a mistake"                | "no"            |
| "wrong answer"                    | "no"            |
| "it's not a lie"                  | "yes"           |
| "he is not lying"                 | "yes"           |
| "you are not mistaken"            | "yes"           |
| "tou are not wrong"               | "yes"           |

## Providing a list of options

`ask_selection()` provides a list of options to the User for them to select from. The User can respond with either the
name of one of these options or select with a numbered ordinal eg "the third".

This method automatically manages fuzzy matching the users response against the list of options provided. 

It also understands "numeric" responses, such as "the first option", "the third one", "option 4", "the last one"

Let's jump back into our `IceCreamSkill` to give the User a list of options to choose from.

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
        self.speak_dialog('coming.right.up', {'flavor': selection})
```

In this example we first speak some `welcome.dialog`. The list of flavors is then spoken, followed by
the `what.flavor.dialog`. Finally, we confirm the Users selection by speaking `coming.right.up.dialog`

### Optional arguments

There are two optional arguments for this method.

`min_conf` (float) defines the minimum confidence level for fuzzy matching the Users response against the list of
options. `numeric` (bool) if set to True will speak the options as a numbered list eg "One, vanilla. Two, chocolate. Or
three, mint"

