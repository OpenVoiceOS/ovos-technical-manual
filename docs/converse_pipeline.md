# Converse Pipeline

The **Converse Pipeline** in **OpenVoiceOS (OVOS)** manages active conversational contexts between the assistant and skills. It allows skills to keep handling user input across multiple turns, enabling more natural, stateful conversations.

---

## Purpose

The **Converse pipeline** enables **multi-turn conversations** by prioritizing which skills are given the opportunity to handle an utterance through their `converse()` method before normal intent parsing occurs.

Key purposes include:

* **Preserve conversational context** across multiple turns.
* **Prioritize recently used skills** for more natural interactions.
* **Enable stateful behavior**, such as follow-up questions or corrections.
* **Prevent unnecessary intent parsing** when a skill is already engaged.
* **Support skill-defined session control** via manual activation/deactivation.

This allows OVOS to act more like a true conversational assistant rather than a single-turn command system.

---

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


---

## Pipeline Stages

This plugin registers a single pipeline:

| Pipeline ID | Description                  | Recommended Use                                                            |
|-------------|------------------------------|----------------------------------------------------------------------------|
| `converse`  | Continuous dialog for skills | Should always be present, do not remove unless you know what you are doing |


---

## How It Works

* When a user speaks, the pipeline checks if any skill is actively conversing.
* Active skills implement a `converse()` method that determines if they want to handle the utterance.
* If no active skill accepts the input, the regular intent matching process continues.
* Skills can automatically deactivate after a timeout or based on custom logic.
* Only a limited number of skills can be active at any given time (defaults configurable).


---

## Skill Integration

Skills integrate with the converse pipeline by:

* Implementing a `converse()` method that checks if the skill wants to handle an utterance.
* Returning `True` if the utterance was handled, `False` otherwise.
* Managing internal state to determine when to exit conversation mode.

This enables modular, stateful conversations without hardcoding turn-taking logic into the core assistant.

---

## Configuration

Customize the pipeline via `mycroft.conf`:

```json
"skills": {
  "converse": {
    "cross_activation": true,
    "converse_activation": "accept_all",
    "converse_mode": "accept_all",
    "converse_blacklist": [],
    "converse_whitelist": [],
    "max_activations": 3,
    "skill_activations": {
      "skill-example": 5
    },
    "timeout": 300,
    "skill_timeouts": {
      "skill-example": 600
    },
    "max_skill_runtime": 10
  }
}
```

**Key Options**

| Config Key           | Description                                                              |
| -------------------- | ------------------------------------------------------------------------ |
| `cross_activation`   | Allow skills to activate/deactivate other skills during a conversation.  |
| `converse_mode`      | Global mode for allowing/disallowing skills from converse participation. |
| `converse_blacklist` | Skills that are not allowed to enter converse mode.                      |
| `converse_whitelist` | Skills explicitly allowed to converse.                                   |
| `max_activations`    | Default number of times a skill can consecutively handle turns.          |
| `skill_activations`  | Per-skill override of `max_activations`.                                 |
| `timeout`            | Time (in seconds) before an idle skill is removed from converse mode.    |
| `skill_timeouts`     | Per-skill override of `timeout`.                                         |
| `max_skill_runtime`  | Maximum time (in seconds) to wait for a skill’s `converse()` response.   |

---

## Converse Modes

| Mode         | Description                                                                  |
| ------------ | ---------------------------------------------------------------------------- |
| `accept_all` | All skills are allowed to use converse mode (default).                       |
| `whitelist`  | Only skills explicitly listed in `converse_whitelist` can use converse mode. |
| `blacklist`  | All skills can use converse mode except those in `converse_blacklist`.       |


---

## Security & Performance

A malicious or badly designed skill using the converse method can potentially hijack the whole conversation loop and render the skills service unusable

Because skills can "hijack" the conversation loop indefinitely, misbehaving or malicious skills can degrade UX. Protections include:

* Timeouts for inactivity and maximum runtime.
* `max_activations` limits per skill.
* Blacklist/whitelist enforcement to restrict which skills can enter converse mode.
* `cross_activation` can be disabled to prevent skill-to-skill manipulation.


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

---

## Notes

* The plugin **does not enforce a fallback behavior** if no skill accepts the input.
* If no skill handles the utterance via converse, the pipeline falls back to normal intent matching or fallback skills.
* This mechanism is ideal for multi-turn conversations like dialogs, games, or assistant flows that require memory of previous input.
