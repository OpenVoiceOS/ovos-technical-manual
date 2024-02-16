# Listener Service

The listener service is responsible for handling audio input, it understands speech and converts it into `utterances` to be handled by `ovos-core`

Different implementations of the listener service have been available during the years

- [mycroft-classic-listener](https://github.com/OpenVoiceOS/mycroft-classic-listener) the original listener from mycroft mark1 extracted into a standalone component - **archived**
- [ovos-listener](https://github.com/OpenVoiceOS/ovos-listener) - an updated version of the mycroft listener with VAD plugins and multiple hotwords support - **deprecated** in `ovos-core` version **0.0.8**
- [ovos-dinkum-listener](https://github.com/OpenVoiceOS/ovos-dinkum-listener) - a listener rewrite based on [mycroft-dinkum](https://github.com/MycroftAI/mycroft-dinkum) - **new** in `ovos-core `version **0.0.8**


## Listener

You can modify microphone settings and enable additional features under the listener section such as wake word / utterance recording / uploading

```javascript
{
  "listener": {
    // NOTE, multiple hotwords are supported, these fields define the main wake_word,
    // this is equivalent to setting "active": true in the "hotwords" section
    // see "hotwords" section at https://github.com/OpenVoiceOS/ovos-config/blob/dev/ovos_config/mycroft.conf
    "wake_word": "hey_mycroft",
    "stand_up_word": "wake_up",
    
    "microphone": {
      "module": "ovos-microphone-plugin-alsa"
    },
    
    VAD": {...},
    
    // Seconds of speech before voice command has begun
    "speech_begin": 0.1,
    // Seconds of silence before a voice command has finished
    "silence_end": 0.5,
    // Settings used by microphone to set recording timeout with and without speech detected
    "recording_timeout": 10.0,
    // Settings used by microphone to set recording timeout without speech detected.
    "recording_timeout_with_silence": 3.0,
    // Setting to remove all silence/noise from start and end of recorded speech (only non-streaming)
    "remove_silence": true,
    // continuous listen is an experimental setting, it removes the need for
    // wake words and uses VAD only, a streaming STT is strongly recommended
    // NOTE: depending on hardware this may cause mycroft to hear its own TTS responses as questions
    "continuous_listen": false,

    // hybrid listen is an experimental setting,
    // it will not require a wake word for X seconds after a user interaction
    // this means you dont need to say "hey mycroft" for follow up questions
    "hybrid_listen": false,
    // number of seconds to wait for an interaction before requiring wake word again
    "listen_timeout": 45
  }
}
```

## Hotwords

By default the listener is waiting for a hotword to do something in response

the most common usage of a hotword is as the assistant's name, instead of continuously transcribing audio the listener waits for a wake word, and then listens to the user speaking

OVOS allows you to load any number of hot words in parallel and trigger different actions when they are detected

each hotword can do one or more of the following:

- trigger listening, also called a **wake_word**
- play a sound
- emit a bus event
- take ovos-core out of sleep mode, also called a **wakeup_word** or **standup_word**
- take ovos-core out of recording mode, also called a **stop_word**

To add a new hotword add its configuration under "hotwords" section.

By default, all hotwords are disabled unless you set `"active": true`. 

Under the `"listener"` setting a main wake word and stand up word are defined, those will be automatically enabled unless you set `"active": false`. 

Users are expected to **only change** `listener.wake_word` if using a single wake word, setting `"active": true` is only intended for **extra** hotwords

```javascript
"listener": {
    // Default wake_word and stand_up_word will be automatically set to active
    // unless explicitly disabled under "hotwords" section
    "wake_word": "hey mycroft",
    "stand_up_word": "wake up"
},

"hotwords": {
    "hey mycroft": {
        "module": "ovos-ww-plugin-precise",
        "version": "0.3",
        "model": "https://github.com/MycroftAI/precise-data/raw/models-dev/hey-mycroft.tar.gz",
        "phonemes": "HH EY . M AY K R AO F T",
        "threshold": 1e-90,
        "lang": "en-us",
        "listen": true,
        "sound": "snd/start_listening.wav"
    },
    "wake up": {
        "module": "ovos-ww-plugin-pocketsphinx",
        "phonemes": "W EY K . AH P",
        "threshold": 1e-20,
        "lang": "en-us",
        "wakeup": true
    }
},
```


## VAD

Voice Activity Detection plugins have several functions under the listener service

- detect when user finished speaking  
- remove silence before sending audio to `STT`
- detect when user is speaking during `continuous mode` (read below)


```javascript
{
  "listener": {  
  
    // Setting to remove all silence/noise from start and end of recorded speech (only non-streaming)
    "remove_silence": true,
    
    VAD": {
         // recommended plugin: "ovos-vad-plugin-silero"
         "module": "ovos-vad-plugin-silero",
         "ovos-vad-plugin-silero": {"threshold": 0.2},
         "ovos-vad-plugin-webrtcvad": {"vad_mode": 3}
    }
  }
}
```

## STT

Two STT plugins may be loaded at once, if the primary plugin fails for some reason the second plugin will be used. 

This allows you to have a lower accuracy offline model as fallback to account for internet outages, this ensures your device never becomes fully unusable


```javascript
"stt": {
    "module": "ovos-stt-plugin-server",
    "fallback_module": "ovos-stt-plugin-vosk",
    "ovos-stt-plugin-server": {"url": "https://stt.openvoiceos.com/stt"}
},
```



## Modes of Operation

There are 3 modes to run dinkum, wakeword, hybrid, or continuous (VAD only)

Additionally, there are 2 temporary modes that can be triggered via bus events / companion skills

### Wake Word mode

![imagem](https://github.com/OpenVoiceOS/ovos-dinkum-listener/assets/33701864/c55388dc-a7fb-4857-9c35-f4a4223c4145)

### Continuous mode

**new** in `ovos-core` version **0.0.8**

![imagem](https://github.com/OpenVoiceOS/ovos-dinkum-listener/assets/33701864/c8820161-9cb8-433f-9380-6d07965c7fa5)

```javascript
{
  "listener": {
    // continuous listen is an experimental setting, it removes the need for
    // wake words and uses VAD only, a streaming STT is strongly recommended
    // NOTE: depending on hardware this may cause mycroft to hear its own TTS responses as questions
    "continuous_listen": false
  }
}
```


### Hybrid mode

**new** in `ovos-core` version **0.0.8**

![imagem](https://github.com/OpenVoiceOS/ovos-dinkum-listener/assets/33701864/b9012663-4f00-47a9-bac4-8b08392da12c)

```javascript
{
  "listener": {
    // hybrid listen is an experimental setting,
    // it will not require a wake word for X seconds after a user interaction
    // this means you dont need to say "hey mycroft" for follow up questions
    "hybrid_listen": false,
    // number of seconds to wait for an interaction before requiring wake word again
    "listen_timeout": 45
  }
}
```

### Sleep mode

Can be used via [Naptime skill](https://github.com/OpenVoiceOS/skill-ovos-naptime)

![imagem](https://github.com/OpenVoiceOS/ovos-dinkum-listener/assets/33701864/24835210-2116-4080-8c2b-fc18eecd923a)

Be sure to enable a wakeup word to get out of sleep!

```javascript
"listener": {
    "stand_up_word": "wake up"
},

"hotwords": {
    "wake up": {
        "module": "ovos-ww-plugin-pocketsphinx",
        "phonemes": "W EY K . AH P",
        "threshold": 1e-20,
        "lang": "en-us",
        "wakeup": true
    }
},
```

### Recording mode

Can be used via [Recording skill](https://github.com/OpenVoiceOS/skill-ovos-audio-recording)

![imagem](https://github.com/OpenVoiceOS/ovos-dinkum-listener/assets/33701864/0337b499-3175-4031-a83f-eda352d2197f)

