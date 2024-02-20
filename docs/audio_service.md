# Audio Service

The audio service is responsible for handling TTS and simple sounds playback


## Skill Methods

skills can use `self.play_audio`, `self.acknowledge`, `self.speak` and `self.speak_dialog` methods to interact with `ovos-audio`

```python
def play_audio(self, filename: str, instant: bool = False):
    """
    Queue and audio file for playback
    @param filename: File to play
    @param instant: if True audio will be played instantly 
                    instead of queued with TTS
    """
```
```python
def acknowledge(self):
    """
    Acknowledge a successful request.

    This method plays a sound to acknowledge a request that does not
    require a verbal response. This is intended to provide simple feedback
    to the user that their request was handled successfully.
    """
```
```python
def speak(self, utterance: str, expect_response: bool = False, wait: Union[bool, int] = False):
    """Speak a sentence.

    Args:
        utterance (str):        sentence mycroft should speak
        expect_response (bool): set to True if Mycroft should listen
                                for a response immediately after
                                speaking the utterance.
        wait (Union[bool, int]): set to True to block while the text
                                 is being spoken for 15 seconds. Alternatively, set
                                 to an integer to specify a timeout in seconds.
    """
```
```python
def speak_dialog(self, key: str, data: Optional[dict] = None,
                 expect_response: bool = False, wait: Union[bool, int] = False):
    """
    Speak a random sentence from a dialog file.

    Args:
        key (str): dialog file key (e.g. "hello" to speak from the file
                                    "locale/en-us/hello.dialog")
        data (dict): information used to populate sentence
        expect_response (bool): set to True if Mycroft should listen
                                for a response immediately after
                                speaking the utterance.
        wait (Union[bool, int]): set to True to block while the text
                                 is being spoken for 15 seconds. Alternatively, set
                                 to an integer to specify a timeout in seconds.
    """
```
to play sounds via bus messages emit `"mycroft.audio.play_sound"` or `"mycroft.audio.queue"` with data `{"uri": "path/sound.mp3"}`

## PlaybackThread

`ovos-audio` implements a queue for sounds, any OVOS component can queue a sound for playback.

Usually only TTS speech is queue for playback, but sounds effects may also be queued for richer experiences, for example in a story telling skill

The PlaybackThread ensures sounds don't play over each other but instead sequentially, listening might be triggered after TTS finishes playing if requested in the `"speak"` message

shorts sounds can be played outside the PlaybackThread, usually when instant feedback is required, such as in the listening sound or on error sounds

You can configure default sounds and the playback commands under `mycroft.conf`

```javascript
  // File locations of sounds to play for default events
  "sounds": {
    "start_listening": "snd/start_listening.wav",
    "end_listening": "snd/end_listening.wav",
    "acknowledge": "snd/acknowledge.mp3",
    "error": "snd/error.mp3"
  },

  // Mechanism used to play WAV audio files
  // by default ovos-utils will try to detect best player
  "play_wav_cmdline": "paplay %1 --stream-name=mycroft-voice",

  // Mechanism used to play MP3 audio files
  // by default ovos-utils will try to detect best player
  "play_mp3_cmdline": "mpg123 %1",

  // Mechanism used to play OGG audio files
  // by default ovos-utils will try to detect best player
  "play_ogg_cmdline": "ogg123 -q %1",

```

> NOTE: by default the playback commands are not set and OVOS will try to determine the best way to play a sound automatically


## Native playback

Usually playback is triggered in response to originating bus message, eg `"recognizer_loop:utterance"`, this message contains
metadata that is used to determine if playback should happen.

`message.context` may contain a source and destination, playback is only triggered if a message destination is a
`native_source` or if missing (considered a broadcast).

This separation of native sources allows remote clients such as an android app to interact with OVOS without the actual
device where ovos-core is running repeating all TTS and music playback out loud

You can learn more about message targeting [here](https://openvoiceos.github.io/ovos-technical-manual/bus_service/#message-targeting)

By default, only utterances originating from the speech client are considered native

```javascript
"Audio": {
    "native_sources": ["debug_cli", "audio"]
}
```

## Dialog Transformers

**NEW** in `ovos-core` version **0.0.8**

Similarly to utterance transformers in core,  `ovos-audio` exposes `utterance` and message.`context` to a set
of plugins that can transform it before TTS stage

The `utterance` to be spoken is sent sequentially to all transformer plugins, ordered by priority (developer defined),
until finally it is sent to the TTS stage

| plugin                                | description                                    | source                                                                                                                    |
|---------------------------------------|------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| ovos-dialog-transformer-openai-plugin | rewrite speech with a LLM before executing TTS | [OpenVoiceOS/ovos-solver-plugin-openai-persona](https://github.com/OpenVoiceOS/ovos-solver-plugin-openai-persona)         |
| ovos-dialog-translation-plugin        | translate speech back into user language       | [OpenVoiceOS/ovos-bidirectional-translation-plugin](https://github.com/OpenVoiceOS/ovos-bidirectional-translation-plugin) |

To enable a transformer add it to `mycroft.conf`

```javascript
// To enable a dialog transformer plugin just add it's name with any relevant config
// these plugins can mutate utterances before TTS
"dialog_transformers": {
    "ovos-dialog-translation-plugin": {},
    "ovos-dialog-transformer-openai-plugin": {
        "rewrite_prompt": "rewrite the text as if you were explaining it to a 5 year old"
    }
}
```


## TTS

Two TTS plugins may be loaded at once, if the primary plugin fails for some reason the second plugin will be used.

This allows you to have a lower quality offline voice as fallback to account for internet outages, this ensures your
device can always give you feedback

```javascript
"tts": {
    "pulse_duck": false,
    
    // plugins to load
    "module": "ovos-tts-plugin-server",
    "fallback_module": "ovos-tts-plugin-mimic",
    
    // individual plugin configs
    "ovos-tts-plugin-server": {
        "host": "https://tts.smartgic.io/piper",
        "v2": true,
        "verify_ssl": true,
        "tts_timeout": 5,
    }
}
```

## TTS Transformers

**NEW** in `ovos-core` version **0.0.8**

Similarly to audio transformers in `ovos-dinkum-listener`, the audio data generated by TTS is exposed to a set
of plugins that can transform it before playback

The audio to be spoken is sent sequentially to all transformer plugins, ordered by priority (developer defined),
until finally it played back to the user

> **NOTE**: Does not work with StreamingTTS

| plugin                          | description                   | source                                                                                                        |
|---------------------------------|-------------------------------|---------------------------------------------------------------------------------------------------------------|
| ovos-tts-transformer-sox-plugin | apply sound effects via `sox` | [OpenVoiceOS/ovos-tts-transformer-sox-plugin](https://github.com/OpenVoiceOS/ovos-tts-transformer-sox-plugin) |

To enable a transformer add it to `mycroft.conf`

```javascript
// To enable a tts transformer plugin just add it's name with any relevant config
// these plugins can mutate audio after TTS
"tts_transformers": {
    "ovos-tts-transformer-sox-plugin": {
        "default_effects": {
            "speed": {"factor": 1.1}
        }
    }
}
```

## Classic Audio Service

**DEPRECATION WARNING** - `ovos-core` version `0.0.8` is moving long-form media playback to `ovos-media` service, this is a work in progress and
will be a default in version **0.1.0** when `ovos-media` is introduced

`ovos-media` is a work in progress, it does not yet ship with OVOS by default, but it can be manually enabled

In order to use `ovos-media` you need to enable the OCP pipeline in `ovos-core` and to disable the old audio service 

disabling old audio service
```json
{
  "enable_old_audioservice": false
}
```

> **DEPRECATION WARNING** - `"enable_old_audioservice": true` will use the [old OCP](https://github.com/OpenVoiceOS/ovos-ocp-audio-plugin) that shipped as an audio plugin in order to work in classic mycroft

You can enable additional Audio plugins under the `"Audio"` section of `mycroft.conf`

OCP will decide when to call the Audio service and what plugin to use

```javascript
"Audio": {
    "backends": {
      "OCP": {
        "type": "ovos_common_play",
        "active": true
      },
      "simple": {
        "type": "ovos_audio_simple",
        "active": true
      },
      "vlc": {
        "type": "ovos_vlc",
        "active": true
      }
    }
},
```


