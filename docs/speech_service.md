# Listener Service

The listener service is responsible for handling audio input, it understands speech and converts it into `utterances` to
be handled by `ovos-core`

Different implementations of the listener service have been available during the years

- [mycroft-classic-listener](https://github.com/OpenVoiceOS/mycroft-classic-listener) the original listener from mycroft
  mark1 extracted into a standalone component - **archived**
- [ovos-listener](https://github.com/OpenVoiceOS/ovos-listener) - an updated version of the mycroft listener with VAD
  plugins and multiple hotwords support - **deprecated** in `ovos-core` version **0.0.8**
- [ovos-dinkum-listener](https://github.com/OpenVoiceOS/ovos-dinkum-listener) - a listener rewrite based
  on [mycroft-dinkum](https://github.com/MycroftAI/mycroft-dinkum) - **NEW** in `ovos-core `version **0.0.8**

## Listener

You can modify microphone settings and enable additional features under the listener section such as wake word /
utterance recording / uploading

```javascript
"listener": {
  // NOTE, multiple hotwords are supported, these fields define the main wake_word,
  // this is equivalent to setting "active": true in the "hotwords" section
  "wake_word": "hey_mycroft",
  "stand_up_word": "wake_up",
  
  "microphone": {...},
  
  "VAD": {...},
  
  // Seconds of speech before voice command has begun
  "speech_begin": 0.1,
  // Seconds of silence before a voice command has finished
  "silence_end": 0.5,
  // Settings used by microphone to set recording timeout with and without speech detected
  "recording_timeout": 10.0,
  // Settings used by microphone to set recording timeout without speech detected.
  "recording_timeout_with_silence": 3.0,
  
  // Setting to remove all silence/noise from start and end of recorded speech (only non-streaming)
  "remove_silence": true
}
```

## Microphone

**NEW** in `ovos-core` version **0.0.8**

Microphone plugins are responsible for feeding audio to the listener, different Operating Systems may require different
plugins or otherwise have performance benefits

```javascript
"listener": {
  "microphone": {
    "module": "ovos-microphone-plugin-alsa"
  }
}
```


## Hotwords

By default the listener is waiting for a hotword to do something in response

the most common usage of a hotword is as the assistant's name, instead of continuously transcribing audio the listener
waits for a wake word, and then listens to the user speaking

OVOS allows you to load any number of hot words in parallel and trigger different actions when they are detected

each hotword can do one or more of the following:

- trigger listening, also called a **wake_word**
- play a sound
- emit a bus event
- take ovos-core out of sleep mode, also called a **wakeup_word** or **standup_word**
- take ovos-core out of recording mode, also called a **stop_word**

To add a new hotword add its configuration under "hotwords" section.

By default, all hotwords are disabled unless you set `"active": true`.

Under the `"listener"` setting a main wake word and stand up word are defined, those will be automatically enabled
unless you set `"active": false`.

Users are expected to **only change** `listener.wake_word` if using a single wake word, setting `"active": true` is only
intended for **extra** hotwords

```javascript
"listener": {
    // Default wake_word and stand_up_word will be automatically set to active
    // unless explicitly disabled under "hotwords" section
    "wake_word": "hey mycroft",
    "stand_up_word": "wake up"
},
// Hotword configurations
"hotwords": {
    "hey_mycroft": {
        "module": "ovos-ww-plugin-precise-lite",
        "model": "https://github.com/OpenVoiceOS/precise-lite-models/raw/master/wakewords/en/hey_mycroft.tflite",
        "expected_duration": 3,
        "trigger_level": 3,
        "sensitivity": 0.5,
        "listen": true
    },
    // default wakeup word to take ovos out of SLEEPING mode,
    "wake_up": {
        "module": "ovos-ww-plugin-pocketsphinx",
        "phonemes": "W EY K . AH P",
        "threshold": 1e-20,
        "lang": "en-us",
        "wakeup": true,
        "fallback_ww": "wake_up_vosk"
    }
}
```

### Sound Classifiers

hotwords can be used as generic sound classifiers that emit bus events for other systems to detect

Let's consider a model trained to recognize coughing, and a companion plugin to track how often it happens, this can be used as an indicator of disease

```javascript
"hotwords": {
    "cough": {
        "module": "ovos-ww-plugin-precise",
        "version": "0.3",
        "model": "https://github.com/MycroftAI/precise-data/blob/models-dev/cough.tar.gz",
        "expected_duration": 3,
        "trigger_level": 3,
        "sensitivity": 0.5,
        "listen": false,
        "active": true,
        // on detection emit this msg_type
        "bus_event": "cough.detected"
    }
}
```

### Multilingualism

In multilingual homes a wake word can be configured for each language, by giving the assistant a different name in each we can assign a language to be used by STT

```javascript
"listener": {
    "wake_word": "hey mycroft"
},
"hotwords": {
  // default wake word, in global language
  "hey_mycroft": {...},   
  // extra wake word with lang assigned
  "android": {
      "module": "...",
      "model": "...",
      // set to active as extra wake word
      "active": true,
      "listen": true,
      // assign a language
      "stt_lang": "pt-pt"
  }
}
```

### Fallback Wake Words

**NEW** in `ovos-core` version **0.0.8**

hotword definitions can also include a `"fallback_ww"`, this indicates an alternative hotword config to load in case the
original failed to load for any reason

```javascript
"listener": {
    // Default wake_word and stand_up_word will be automatically set to active
    // unless explicitly disabled under "hotwords" section
    "wake_word": "hey mycroft",
    "stand_up_word": "wake up"
},
// Hotword configurations
"hotwords": {
    "hey_mycroft": {
        "module": "ovos-ww-plugin-precise-lite",
        "model": "https://github.com/OpenVoiceOS/precise-lite-models/raw/master/wakewords/en/hey_mycroft.tflite",
        "expected_duration": 3,
        "trigger_level": 3,
        "sensitivity": 0.5,
        "listen": true,
        "fallback_ww": "hey_mycroft_precise"
    },
    // in case precise-lite is not installed, attempt to use classic precise
    "hey_mycroft_precise": {
        "module": "ovos-ww-plugin-precise",
        "version": "0.3",
        "model": "https://github.com/MycroftAI/precise-data/raw/models-dev/hey-mycroft.tar.gz",
        "expected_duration": 3,
        "trigger_level": 3,
        "sensitivity": 0.5,
        "listen": true,
        "fallback_ww": "hey_mycroft_vosk"
    },
    // in case classic precise is not installed, attempt to use vosk
    "hey_mycroft_vosk": {
        "module": "ovos-ww-plugin-vosk",
        "samples": ["hey mycroft", "hey microsoft", "hey mike roft", "hey minecraft"],
        "rule": "fuzzy",
        "listen": true,
        "fallback_ww": "hey_mycroft_pocketsphinx"
    },
    // in case vosk is not installed, attempt to use pocketsphinx
    "hey_mycroft_pocketsphinx": {
        "module": "ovos-ww-plugin-pocketsphinx",
        "phonemes": "HH EY . M AY K R AO F T",
        "threshold": 1e-90,
        "lang": "en-us",
        "listen": true
    },
    // default wakeup word to take ovos out of SLEEPING mode,
    "wake_up": {
        "module": "ovos-ww-plugin-pocketsphinx",
        "phonemes": "W EY K . AH P",
        "threshold": 1e-20,
        "lang": "en-us",
        "wakeup": true,
        "fallback_ww": "wake_up_vosk"
    },
    // in case pocketsphinx plugin is not installed, attempt to use vosk
    "wake_up_vosk": {
        "module": "ovos-ww-plugin-vosk",
        "rule": "fuzzy",
        "samples": ["wake up"],
        "lang": "en-us",
        // makes this a wakeup word for usage in SLEEPING mode
        "wakeup": true
    }
}
```


## VAD

Voice Activity Detection plugins have several functions under the listener service

- detect when user finished speaking
- remove silence before sending audio to `STT` - **NEW** in `ovos-core` version **0.0.8**
- detect when user is speaking during `continuous mode` (read below)



```javascript
"listener": {  
  // Setting to remove all silence/noise from start and end of recorded speech (only non-streaming)
  "remove_silence": true,
  
  "VAD": {
     // recommended plugin: "ovos-vad-plugin-silero"
     "module": "ovos-vad-plugin-silero",
     "ovos-vad-plugin-silero": {"threshold": 0.2},
     "ovos-vad-plugin-webrtcvad": {"vad_mode": 3}
  }
}
```

## Audio Transformers

**NEW** in `ovos-core` version **0.0.8**, originally developed for [Neon](https://github.com/NeonGeckoCom/neon-transformers)

Similarly to utterance transformers in `ovos-core`, the listener exposes audio and message.`context` to a set of plugins
that can transform it before STT stage

Audio transformer plugins can either transform the audio binary data itself (eg, denoise) or the `context` (eg, speaker
recognition)

The audio is sent sequentially to all transformer plugins, ordered by priority (developer defined), until finally it is
sent to the STT stage

## STT

Two STT plugins may be loaded at once, if the primary plugin fails for some reason the second plugin will be used.

This allows you to have a lower accuracy offline model as fallback to account for internet outages, this ensures your
device never becomes fully unusable

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
}
```

### Continuous mode

**EXPERIMENTAL** - **NEW** in `ovos-core` version **0.0.8**

![imagem](https://github.com/OpenVoiceOS/ovos-dinkum-listener/assets/33701864/c8820161-9cb8-433f-9380-6d07965c7fa5)

```javascript
"listener": {
  // continuous listen is an experimental setting, it removes the need for
  // wake words and uses VAD only, a streaming STT is strongly recommended
  // NOTE: depending on hardware this may cause mycroft to hear its own TTS responses as questions
  "continuous_listen": false
}
```

### Hybrid mode

**EXPERIMENTAL** - **NEW** in `ovos-core` version **0.0.8**

![imagem](https://github.com/OpenVoiceOS/ovos-dinkum-listener/assets/33701864/b9012663-4f00-47a9-bac4-8b08392da12c)

```javascript
"listener": {
  // hybrid listen is an experimental setting,
  // it will not require a wake word for X seconds after a user interaction
  // this means you dont need to say "hey mycroft" for follow up questions
  "hybrid_listen": false,
  // number of seconds to wait for an interaction before requiring wake word again
  "listen_timeout": 45
}
```

### Recording mode

**EXPERIMENTAL** - **NEW** in `ovos-core` version **0.0.8**

Can be used via [Recording skill](https://github.com/OpenVoiceOS/skill-ovos-audio-recording)

![imagem](https://github.com/OpenVoiceOS/ovos-dinkum-listener/assets/33701864/0337b499-3175-4031-a83f-eda352d2197f)

