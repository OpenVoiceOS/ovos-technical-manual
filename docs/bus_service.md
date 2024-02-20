# Bus Service

The bus service provides a websocket where all internal events travel

You can think of the bus service as OVOS's nervous system

The messagebus is considered an internal and private websocket, external clients should not connect directly to it.
## Configuration

The messagebus has a dedicated section in `mycroft.conf`

```javascript
"websocket": {
    "host": "127.0.0.1",
    "port": 8181,
    "route": "/core",
    "shared_connection": true
}
```

## Security

in mycroft-core all skills share a bus connection, this allows malicious skills to manipulate it and affect other skills

you can see a demonstration of this problem with [BusBrickerSkill](https://github.com/EvilJarbas/BusBrickerSkill)

`"shared_connection": false` ensures each skill gets its own websocket connection and avoids this problem

It is recommended you **DO NOT** change `"host": "127.0.0.1"`, this will ensure no outside world connections are allowed


### Exposing the bus

Please do not expose the messagebus to the outside world!

Anything connected to the bus can fully control OVOS, and OVOS usually has full control over the whole system!

You can read more about the security issues over at [Nhoya/MycroftAI-RCE](https://github.com/Nhoya/MycroftAI-RCE)

If you need to connect to the bus from the outside world please check the companion project [HiveMind](https://openvoiceos.github.io/community-docs/friends/#hivemind)

Lots of guides for mycroft tell you to expose the websocket, please (re)read the info and links above, think 10 times before following steps blindly

## Messages

A `Message` consists of a json payload, it contains a `type` , some `data` and a `context`. 

The `context` is considered to be metadata and might be changed at any time in transit, the `context` can contain anything depending on where the message came from, and often is completely empty. 

You can think of the message `context` as a sort of session data for a individual interaction, in general messages down the chain keep the `context` from the original message, most listeners (eg, skills) will only care about `type` and `data`. 

An index of all bus messages emitted or listened too by all the OVOS repositories can be found at [https://openvoiceos.github.io/message_spec](https://openvoiceos.github.io/message_spec/)

## Session

**NEW** in `ovos-core` version **0.0.8**

The `message.context` may contain anything, but a special key is the `"session"`

A Session contains per-request preferences and state data, external clients such as [voice satellites](https://jarbashivemind.github.io/HiveMind-community-docs/07_voicesat/) have their own session

the Session can be used to select user preferences or resume previous conversations by restoring skill state

`ovos-core` always uses the reserved `"session_id": "default"`, and will fully manage the state of the default Session, individual clients are supposed to manage their own session and might ignore updates coming from `ovos-core`

Things included in a Session

- list of active skills (able to converse)
- list of intent contexts (follow up questions)
- utterance state (get_response vs regular utterance)
- language
- tts / stt preferences (eg, voice)
- intent pipeline to be used
- site_id

By default these values are populated from `mycroft.conf`, skills have some "magic variables" that also reflect the session, such as `self.lang`

It is up to individual skills to manage session, see the [parrot skill](https://github.com/OpenVoiceOS/skill-ovos-parrot/) for an example.

> **WARNING** skills that are not session aware may keep a shared state behave weirdly with voice satellites

[Transformers](https://openvoiceos.github.io/ovos-technical-manual/core/#utterance-transformers) may modify the Session, for example to inject intent context 

## Message Targeting

ovos-core uses the message `context` to add metadata about the messages themselves, where do they come from and what are they intended for.

the `Message` object provides the following methods:

- `message.forward` method, keeps previous context.
	- message continues going to same `destination`
- `message.reply` method swaps `"source"` with `"destination"`
	- message goes back to `source`

The context `destination` parameter in the original message can be set to a list with any number of intended targets:

```python
bus.emit(Message('recognizer_loop:utterance', data, 
				 context={'destination': ['audio', 'kde'],
						  'source': "remote_service"))
```

### Sources

OVOS injects the context when it emits an utterance, usually when spoken via STT service

STT will identify itself as `audio`

`mycroft.conf` contains a `native_sources` section you can configure to change how the audio service processes external requests

### Destinations

TTS checks the message context if it's the intended target for the message and will only speak in the following conditions:

- Explicitly targeted i.e. the `destination` is `"audio"`
- `destination` is set to `None`
- `destination` is missing completely

The idea is that for example when the android app is used to access OpenVoiceOS the device at home shouldn't start to speak.

TTS will be executed when `"audio"` is the `destination` (configurable in `"native_sources"`)

A missing `destination` or if the `destination` is set to `None` is interpreted as a multicast and should trigger all output capable processes (be it the audio service, a web-interface, the KDE plasmoid or maybe the android app)

### Internal routing

- intent service will `.reply` to the original utterance message
- all skill/intent service messages are `.forward` (from previous intent service `.reply`)
- skills sending their own messages might not respect this **warning**
