# STT Plugins

STT plugins are responsible for converting spoken audio into text

## `STT`

The base STT, this handles the audio in "batch mode" taking a complete audio file, and returning the complete transcription.

Each STT plugin class needs to define the `execute()` method taking two arguments:

* `audio` \([AudioData](https://github.com/Uberi/speech_recognition/blob/master/reference/library-reference.rst#audiodataframe_data-bytes-sample_rate-int-sample_width-int---audiodata) object\) - the audio data to be transcribed.  
* `lang` \(str\) - _optional_ - the BCP-47 language code

The bare minimum STT class will look something like

```python
from ovos_plugin_manager.templates.stt import STT

class MySTT(STT):
    def execute(audio, language=None):
        # Handle audio data and return transcribed text
        [...]
        return text
```

## `StreamingSTT`

A more advanced STT class for streaming data to the STT. This will receive chunks of audio data as they become available and they are streamed to an STT engine.

The plugin author needs to implement the `create_streaming_thread()` method creating a thread for handling data sent through `self.queue`. 

The thread this method creates should be based on the [StreamThread class](). `handle_audio_data()` method also needs to be implemented.

## Entry point

To make the class detectable as an STT plugin, the package needs to provide an entry point under the `mycroft.plugin.stt` namespace.

```python
setup([...],
      entry_points = {'mycroft.plugin.stt': 'example_stt = my_stt:mySTT'}
      )
```

Where `example_stt` is is the STT module name for the plugin, my\_stt is the Python module and mySTT is the class in the module to return.


## List of STT plugins

| Plugin                                                                                                                                                                                                                       | Offline | Streaming | Type              | 
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------|-----------|-------------------|
| [ovos-stt-plugin-fasterwhisper](https://github.com/OpenVoiceOS/ovos-stt-plugin-fasterwhisper)                                                                                                                                | ✔️      | ❌         | FOSS              |
| [ovos-stt-plugin-whispercpp](https://github.com/OpenVoiceOS/ovos-stt-plugin-whispercpp)                                                                                                                                      | ✔️      | ❌         | FOSS              |
| [ovos-stt-plugin-vosk](https://github.com/OpenVoiceOS/ovos-stt-plugin-vosk)                                                                                                                                                  | ✔️      | ❌         | FOSS              |
| [ovos-stt-plugin-chromium](https://github.com/OpenVoiceOS/ovos-stt-plugin-chromium)                                                                                                                                          | ❌       | ❌         | API (free)        |
| [ovos-stt-plugin-http-server](https://github.com/OpenVoiceOS/ovos-stt-plugin-http-server)                                                                                                                                    | ❌       | ❌         | API (self hosted) |
| [ovos-stt-plugin-pocketsphinx](https://github.com/OpenVoiceOS/ovos-stt-plugin-pocketsphinx)                                                                                                                                  | ✔️      | ❌         | FOSS              |
| [ovos-stt-azure-plugin](https://github.com/OpenVoiceOS/ovos-stt-azure-plugin)                                                                                                                                                | ❌       | ❌         | API (key)         |
| ![imagem](https://github.com/OpenVoiceOS/ovos-media/assets/33701864/90f31b0a-dd56-457d-a3cf-7fc08b460038) [neon-stt-plugin-google_cloud_streaming](https://github.com/NeonGeckoCom/neon-stt-plugin-google_cloud_streaming)   | ❌       | ✔         | API (key)         |
| ![imagem](https://github.com/OpenVoiceOS/ovos-media/assets/33701864/90f31b0a-dd56-457d-a3cf-7fc08b460038) [neon-stt-plugin-nemo](https://github.com/NeonGeckoCom/neon-stt-plugin-nemo)                                       | ✔️      | ✔️        | FOSS              | 
| ![imagem](https://github.com/OpenVoiceOS/ovos-media/assets/33701864/90f31b0a-dd56-457d-a3cf-7fc08b460038) [neon-stt-plugin-nemo-remote](https://github.com/NeonGeckoCom/neon-stt-plugin-nemo-remote)                         | ❌️      | ❌         | API (self hosted) | 

## Standalone Usage

STT plugins can be used in your owm projects as follows

```python
from speech_recognition import Recognizer, AudioFile

plug = STTPlug()

# verify lang is supported
lang = "en-us"
assert lang in plug.available_languages

# read file
with AudioFile("test.wav") as source:
    audio = Recognizer().record(source)

# transcribe AudioData object
transcript = plug.execute(audio, lang)
```

## Plugin Template

```python
from ovos_plugin_manager.templates.stt import STT


# base plugin class
class MySTTPlugin(STT):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        # read config settings for your plugin
        lm = self.config.get("language-model")
        hmm = self.config.get("acoustic-model")

    def execute(self, audio, language=None):
        # TODO - convert audio into text and return string
        transcript = "You said this"
        return transcript

    @property
    def available_languages(self):
        """Return languages supported by this STT implementation in this state
        This property should be overridden by the derived class to advertise
        what languages that engine supports.
        Returns:
            set: supported languages
        """
        # TODO - what langs can this STT handle?
        return {"en-us", "es-es"}


# sample valid configurations per language
# "display_name" and "offline" provide metadata for UI
# "priority" is used to calculate position in selection dropdown 
#       0 - top, 100-bottom
# all other keys represent an example valid config for the plugin 
MySTTConfig = {
    lang: [{"lang": lang,
            "display_name": f"MySTT ({lang}",
            "priority": 70,
            "offline": True}]
    for lang in ["en-us", "es-es"]
}
```
