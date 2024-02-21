# Session Aware Skills

**NEW** `ovos-core` version **0.0.8**

If you want your skills to handle simultaneous users you need to make the [Session](https://openvoiceos.github.io/ovos-technical-manual/bus_service/#session) aware

Each remote client, usually a [voice satellite](https://jarbashivemind.github.io/HiveMind-community-docs/07_voicesat/), will send a `Session` with the `Message`

Your skill should keep track of any Session specific state separately, eg, a chat history

> **WARNING**: Skills need to be Session Aware to play well with [HiveMind](https://jarbashivemind.github.io/HiveMind-community-docs/)

## SessionManager

You can access the `Session` in a `Message` object via the `SessionManager` class

```python
from ovos_bus_client.session import SessionManager, Session

class MySkill(OVOSSkill):

    def on_something(self, message):
        sess = SessionManager.get(message)
        print(sess.session_id)
```

If the message originated in the device itself, the `session_id` is always equal to `"default"`, if it comes from an external client then it will be a unique uuid

## Per User Interactions

Let's consider a skill that keeps track of a chat history, how would such a skill keep track of `Sessions`?

```python
from ovos_bus_client.session import SessionManager, Session
from ovos_workshop.decorators import intent_handler
from ovos_workshop.skills import OVOSSkill


class UtteranceRepeaterSkill(OVOSSkill):

    def initialize(self):
        self.chat_sessions = {}
        self.add_event('recognizer_loop:utterance', self.on_utterance)

    # keep chat history per session
    def on_utterance(self, message):
        utt = message.data['utterances'][0]
        sess = SessionManager.get(message)
        if sess.session_id not in self.chat_sessions:
            self.chat_sessions[sess.session_id] = {"current_stt": ""}
        self.chat_sessions[sess.session_id]["prev_stt"] = self.chat_sessions[sess.session_id]["current_stt"]
        self.chat_sessions[sess.session_id]["current_stt"] = utt

    # retrieve previous STT per session
    @intent_handler('repeat.stt.intent')
    def handle_repeat_stt(self, message):
        sess = SessionManager.get(message)
        if sess.session_id not in self.chat_sessions:
            utt = self.translate('nothing')
        else:
            utt = self.chat_sessions[sess.session_id]["prev_stt"]
        self.speak_dialog('repeat.stt', {"stt": utt})
            
    # session specific stop event 
    # if this method returns True then self.stop will NOT be called
    def stop_session(self, session: Session):
        if session.session_id in self.chat_sessions:
            self.chat_sessions.pop(session.session_id)
            return True
        return False
```

A full example can be found in the [parrot skill](https://github.com/OpenVoiceOS/skill-ovos-parrot)
