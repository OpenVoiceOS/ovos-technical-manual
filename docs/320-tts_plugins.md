# TTS Plugins

TTS plugins are responsible for converting text into audio for playback

## TTS

All Mycroft TTS plugins need to define a class based on the TTS base class from ovos_plugin_manager

```python
from ovos_plugin_manager.templates.tts import TTS

class myTTS(TTS):
    def __init__(self, lang, config):
        super().__init__(lang, config, validator, audio_ext='wav',
                         phonetic_spelling=False, ssml_tags=None)
        # Any specific init code goes here
```

The `super()` call does some setup adding specific options to how Mycroft will preprocess the sentence.

* `audio_ext`: filetype of output, possible options 'wav' and 'mp3'.
* `phonetec_spelling`, True if Mycroft should preprocess some difficult to pronounce words \(eg spotify\) or provide the raw text to the TTS.
* `ssml_tags`: list of valid SSML tags for the TTS if any, otherwise None.
* `validator`: a special class that verifies that the TTS is working in the current configuration.

The `get_tts()` method will be called by Mycroft to generate audio and \(optionally\) phonemes. This is the main method that the plugin creator needs to implement. It is called with:

* `sentence` \(str\): a piece of text to turn into audio.
* `wav_file` \(str\): where the plugin should store the generated audio data.

This method should generate audio data and return a Tuple `(wav_file, visemes)`:

* `wav_file` \(str\): path to written data \(generally the input argument\)
* `phonemes` \(list\): phoneme list for synthesized audio


## TTS Validator

To check if the TTS can be used, a validator class is needed. This should inherit from `mycroft.tts.TTSValidaor`. 

It will be called with the TTS class as argument and will store it in `self.tts`.

The following is the bare minimum implementation:

```python
class MyValidator(TTSValidator):
    def get_tts_class(self):
        # Should return a reference to the TTS class it's inteded to validate.

    def validate_lang(self):
        # Raise exception if `self.tts.lang` is not supported.

    def validate_connection(self):
        # Check that the software needed for the TTS is reachable,
        # be it a local executable, python module or remote server and
        # if not available raise an exception.
```

> NOTE: TTSValidator is optional

## Entry point

To make the class detectable as an TTS plugin, the package needs to provide an entry point under the `mycroft.plugin.tts` namespace.

```python
setup([...],
      entry_points = {'mycroft.plugin.tts': 'example_tts = my_tts:myTTS'}
      )
```

Where `example_tts` is is the TTS module name for the plugin, `my_tts` is the Python module and `myTTS` is the class in the module to return.

## List of TTS plugins

| Plugin                                                                                                                                                                                                   | Streaming | Offline | Type              |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------|---------|-------------------|
| [ovos-tts-plugin-mimic](https://github.com/OpenVoiceOS/ovos-tts-plugin-mimic)                                                                                                                            | ❌         | ✔️      | FOSS              |
| [ovos-tts-plugin-mimic3](https://github.com/OpenVoiceOS/ovos-tts-plugin-mimic3)                                                                                                                          | ❌         | ✔️      | FOSS              |
| [ovos-tts-plugin-piper](https://github.com/OpenVoiceOS/ovos-tts-plugin-piper)                                                                                                                            | ❌         | ✔️      | FOSS              |
| [ovos-tts-plugin-marytts](https://github.com/OpenVoiceOS/ovos-tts-plugin-marytts)                                                                                                                        | ❌         | ❌       | API (self hosted) |
| [ovos-tts-server-plugin](https://github.com/OpenVoiceOS/ovos-tts-server-plugin)                                                                                                                          | ❌         | ❌       | API (self hosted) |
| [ovos-tts-plugin-pico](https://github.com/OpenVoiceOS/ovos-tts-plugin-pico)                                                                                                                              | ❌         | ✔️      | FOSS              |
| [ovos-tts-plugin-edge-tts](https://github.com/OpenVoiceOS/ovos-tts-plugin-edge-tts)                                                                                                                      | ✔️        | ❌       | API (free)        |
| [ovos-tts-plugin-polly](https://github.com/OpenVoiceOS/ovos-tts-plugin-polly)                                                                                                                            | ❌         | ❌       | API (key)         |
| [ovos-tts-plugin-voicerss](https://github.com/OpenVoiceOS/ovos-tts-plugin-voicerss)                                                                                                                      | ❌         | ❌       | API (key)         |
| [ovos-tts-plugin-google-TX](https://github.com/OpenVoiceOS/ovos-tts-plugin-google-TX)                                                                                                                    | ❌         | ❌       | API (free)        |
| [ovos-tts-plugin-responsivevoice](https://github.com/OpenVoiceOS/ovos-tts-plugin-responsivevoice)                                                                                                        | ❌         | ❌       | API (free)        |
| [ovos-tts-plugin-espeakNG](https://github.com/OpenVoiceOS/ovos-tts-plugin-espeakNG)                                                                                                                      | ❌         | ✔️      | FOSS              |
| [ovos-tts-plugin-cotovia](https://github.com/OpenVoiceOS/ovos-tts-plugin-cotovia)                                                                                                                        | ❌         | ✔️      | FOSS              |
| [ovos-tts-plugin-SAM](https://github.com/OpenVoiceOS/ovos-tts-plugin-SAM)                                                                                                                                | ❌         | ✔️      | Abandonware       |
| [ovos-tts-plugin-beepspeak](https://github.com/OpenVoiceOS/ovos-tts-plugin-beepspeak)                                                                                                                    | ❌         | ✔️      | Fun               |
| ![imagem](https://github.com/OpenVoiceOS/ovos-media/assets/33701864/90f31b0a-dd56-457d-a3cf-7fc08b460038) [neon-tts-plugin-larynx_server](https://github.com/NeonGeckoCom/neon-tts-plugin-larynx_server) | ❌         | ❌       | API (self hosted) |
| ![imagem](https://github.com/OpenVoiceOS/ovos-media/assets/33701864/90f31b0a-dd56-457d-a3cf-7fc08b460038) [neon-tts-plugin-coqui](https://github.com/NeonGeckoCom/neon-tts-plugin-coqui)                 | ❌         | ✔️       | FOSS              |
| ![imagem](https://github.com/OpenVoiceOS/ovos-media/assets/33701864/90f31b0a-dd56-457d-a3cf-7fc08b460038) [neon-tts-plugin-coqui-remote](https://github.com/NeonGeckoCom/neon-tts-plugin-coqui-remote)   | ❌         | ❌       | API (self hosted) |
| ![imagem](https://github.com/OpenVoiceOS/ovos-media/assets/33701864/90f31b0a-dd56-457d-a3cf-7fc08b460038) [neon-tts-plugin-glados](https://github.com/NeonGeckoCom/neon-tts-plugin-glados)               | ❌         | ✔️      | FOSS              |



## Standalone Usage

TODO

## Plugin Template

```python
from ovos_plugin_manager.templates.tts import TTS


# base plugin class
class MyTTSPlugin(TTS):
    def __init__(self, *args, **kwargs):
        # in here you should specify if your plugin return wav or mp3 files
        # you should also specify any valid ssml tags
        ssml_tags = ["speak", "s", "w", "voice", "prosody",
                     "say-as", "break", "sub", "phoneme"]
        super().__init__(*args, **kwargs, audio_ext="wav", ssml_tags=ssml_tags)
        # read config settings for your plugin if any
        self.pitch = self.config.get("pitch", 0.5)

    def get_tts(self, sentence, wav_file):
        # TODO - create TTS audio @ wav_file (path)
        return wav_file, None

    @property
    def available_languages(self):
        """Return languages supported by this TTS implementation in this state
        This property should be overridden by the derived class to advertise
        what languages that engine supports.
        Returns:
            set: supported languages
        """
        # TODO - what langs can this TTS handle?
        return {"en-us", "es-es"}


# sample valid configurations per language
# "display_name" and "offline" provide metadata for UI
# "priority" is used to calculate position in selection dropdown 
#       0 - top, 100-bottom
# all other keys represent an example valid config for the plugin 
MyTTSConfig = {
    lang: [{"lang": lang,
            "display_name": f"MyTTS ({lang}",
            "priority": 70,
            "offline": True}]
    for lang in ["en-us", "es-es"]
}
```
