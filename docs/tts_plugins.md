# TTS Plugins

TTS plugins are responsible for converting text into audio for playback

## List of TTS plugins

| Plugin                                                                                                                                                                                                   | Streaming | Offline | Type              |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------|---------|-------------------|
| [ovos-tts-plugin-mimic](https://github.com/OpenVoiceOS/ovos-tts-plugin-mimic)                                                                                                                            | ❌         | ✔️      | FOSS              |
| [ovos-tts-plugin-mimic2](https://github.com/OpenVoiceOS/ovos-tts-plugin-mimic2)                                                                                                                          | ❌         | ❌       | API (free)        |
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
