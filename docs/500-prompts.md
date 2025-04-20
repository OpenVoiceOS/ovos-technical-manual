# Prompting the User for Responses in OVOS Skills

OVOS provides several built-in methods for engaging users in interactive conversations. These include asking open-ended questions, confirming yes/no responses, and offering multiple-choice selections — all handled in a natural, voice-first way.

Here we look at how to implement the most common types of prompts. For more information on conversation design see
the [Voice User Interface Design Guidelines](https://mycroft-ai.gitbook.io/docs/skill-development/voice-user-interface-design-guidelines/interactions-and-guidelines/statements-and-prompts).

---

## Usage Guide

Here’s how to use different types of prompts in your OVOS skills:

### 1. Open-Ended Questions

Let the user respond freely, either to trigger another skill or to handle the response with a custom intent.

```python
from ovos_workshop.skills import OVOSSkill
from ovos_workshop.decorators import intent_handler
import random

class AskMeSkill(OVOSSkill):
    @intent_handler('ask_me_something.intent')
    def handle_set_favorite(self):
        question = random.choice(self.question_list)
        self.speak(question, expect_response=True)
```

> `expect_response=True` keeps the mic open after speaking, so the response can be handled by OVOS's intent pipeline.

---

### 2. Request Extra Information with `get_response()`

Use this to ask a specific question and directly capture the user's reply.

```python
from ovos_workshop.skills import OVOSSkill
from ovos_workshop.decorators import intent_handler

class IceCreamSkill(OVOSSkill):
    @intent_handler('set.favorite.intent')
    def handle_set_favorite(self):
        favorite_flavor = self.get_response('what.is.your.favorite.flavor')
        self.speak_dialog('confirm.favorite.flavor', {'flavor': favorite_flavor})
```

**Optional `get_response()` arguments:**

- `data`: Dictionary to format the dialog file
- `validator`: A function to check if the user response is valid
- `on_fail`: A fallback string to say if validation fails
- `num_retries`: How many times to retry if the response isn’t valid

---

### 3. Yes/No Questions with `ask_yesno()`

Detects affirmations or negations from user responses.

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

**Behavior:**

- Returns `"yes"` or `"no"` for matching phrases.
- Returns the full utterance if unclear.
- Returns `None` if no valid response is detected.

> uses [ovos-solver-YesNo-plugin](https://github.com/OpenVoiceOS/ovos-solver-YesNo-plugin) to understand complex affirmations and denials — even double negations.

Example mappings:

| User Says                        | Detected As |
|----------------------------------|--------------|
| "yes"                            | yes          |
| "no"                             | no           |
| "don't think so"                 | no           |
| "that's affirmative"             | yes          |
| "no, but actually, yes"          | yes          |
| "yes, but actually, no"          | no           |
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
| "beans"                           | None            |

---

### 4. Multiple-Choice Prompts with `ask_selection()`

Let users choose from a list of options, by name or number.

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
        self.speak_dialog('coming.right_up', {'flavor': selection})
```

**Optional arguments:**

- `min_conf` (float): Minimum confidence threshold for fuzzy matching
- `numeric` (bool): If `True`, speak the list with numbered options

User responses like "chocolate", "the second one", or "option three" are all supported.

---

## Technical Notes

- All methods handle microphone activation and parsing behind the scenes.
- OVOS automatically integrates with the intent engine to resolve follow-up responses.
- These prompts are designed to support natural dialogue flows, validating and re-prompting as needed.

---

## Tips

- Always confirm user input when using `get_response()` or `ask_selection()` for clarity.
- Use `validator` with `get_response()` to catch unclear or unwanted input.
- Use `ask_yesno()` for quick binary decisions, but gracefully handle unexpected answers.

