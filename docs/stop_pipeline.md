# Stop Pipeline

The **stop pipeline** is a core component of the Open Voice OS (OVOS) pipeline architecture. It defines the logic responsible for stopping ongoing interactions with active skills. This includes aborting responses, halting speech, and terminating background tasks that skills may be performing. 

Because stopping is a **fundamental feature of a voice assistant**, it is implemented as a **dedicated pipeline plugin**, not just a fallback or intent handler.

---

## Purpose

A voice assistant must always be capable of responding to a "stop" command. Whether the user says *“stop,” “cancel,”* or another localized phrase, OVOS must quickly:

* Determine if a skill is actively responding
* Allow skills to confirm whether they can be stopped
* Abort conversations, questions, or spoken responses

The `stop` pipeline guarantees this behavior through a flexible plugin system and localized vocab matching.

---

## How it works

The stop pipeline activates based on **high-confidence** or **medium-confidence** utterance matches.

### High-confidence (`stop_high`)

This is triggered when a user says an exact match for a stop command, e.g.,:

* “Stop”
* “Cancel”
* “Parar” (in Portuguese)
* “Stopp” (in German)

The plugin:

1. Checks if any **active skills** can be stopped.
2. Pings active skills
3. Waits briefly (0.5s) for replies.
4. Calls stop on relevant skills.
5. If no skills are active, emits a **global stop**: `mycroft.stop`.

### Medium-confidence (`stop_medium`)

Triggered for more complex phrases that include a stop command but are not exact matches, such as:

* “Can you stop now?”
* “I don’t want that anymore”
* “Stop playing music please”

This match falls back to fuzzy intent matching.

---

## Localization

The plugin supports stop commands in multiple languages using `.voc` files stored in:

```
locale/
  en-us/
    stop.voc
    global_stop.voc
  pt-pt/
    stop.voc
    global_stop.voc
```

You can help with language support via [GitLocalize]()

---

## Session Integration

The stop plugin interfaces with the OVOS session system:

* Skills that respond to `stop` will be removed from **active skill list**
* Session blacklists are respected, blacklisted skills will not be pinged
* Session state is updated after each successful stop

---

## Design Philosophy

* ⏱️ **Low latency**: Matches and stops skills within 0.5 seconds
* 🧩 **Extensible**: Other plugins can extend or override this pipeline
* 🗣️ **Localized**: All behavior is language-aware and configurable
* 🔄 **Resilient**: Falls back to global stop if skills are unresponsive

---

## Summary

The `stop` pipeline ensures that OVOS is always in control. Whether a user needs to quickly interrupt a skill, cancel a conversation, or shut down all interactions, the `StopService` plugin provides the robust, language-aware foundation to make that possible.

It is **not considered optional**, all OVOS installations should include this pipeline by default.
