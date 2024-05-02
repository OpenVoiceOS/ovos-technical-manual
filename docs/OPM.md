# OPM

![](https://raw.githubusercontent.com/OpenVoiceOS/ovos_assets/72edbcca11ac25b09d164afb42bf04cee23b801d/Logo/Raw/opm-logo.svg)

OPM is the [OVOS Plugin Manager](https://github.com/OpenVoiceOS/OVOS-plugin-manager), this base package provides
arbitrary plugins to the ovos ecosystem

OPM plugins import their base classes from OPM making them portable and independent of core, plugins can be used in
your standalone projects

By using OPM you can ensure a standard interface to plugins and easily make them configurable in your project, plugin
code and example configurations are mapped to a string via python entrypoints in setup.py

## Where to install plugins

Each kind of plugin should go into it's respective ovos service, most of the time ovos is sharing the base environment but in some setups such as ovos-docker you should keep in mind the correct locations to install each plugin

![image](https://github.com/OpenVoiceOS/ovos-technical-manual/assets/33701864/861cadfb-8be7-4c7f-b701-86fe49ac5a63)

Each type of plugin has it's own dedicated documentation page, most of the time you only need to `pip install ovos-plugin-name` and edit `mycroft.conf` to configure and enable the plugin

### HiveMind

HiveMind setups allow you to decide which plugins run server side or satellite side

In this example the hivemind server runs only core + skills, the satellites handle their own STT/TTS, this corresponds to the `server profile` in [ovos-installer](https://github.com/OpenVoiceOS/ovos-installer)

![image](https://github.com/OpenVoiceOS/ovos-technical-manual/assets/33701864/55694b82-69c9-4288-9a89-1d9716eb3c57)

In this example the hivemind server runs a full ovos-core, it handles STT/TTS for the satellites, this corresponds to the `listener profile` in [ovos-installer](https://github.com/OpenVoiceOS/ovos-installer)

![image](https://github.com/OpenVoiceOS/ovos-technical-manual/assets/33701864/1455a488-af0f-44b4-a5e6-0418a7cd1f96)

## Plugin Packaging

Plugins need to define one entrypoint with their plugin type and plugin class

```python
# OPM recognized plugin types
class PluginTypes(str, Enum):
    PHAL = "ovos.plugin.phal"
    ADMIN = "ovos.plugin.phal.admin"
    SKILL = "ovos.plugin.skill"
    VAD = "ovos.plugin.VAD"
    PHONEME = "ovos.plugin.g2p"
    AUDIO = 'mycroft.plugin.audioservice'
    STT = 'mycroft.plugin.stt'
    TTS = 'mycroft.plugin.tts'
    WAKEWORD = 'mycroft.plugin.wake_word'
    TRANSLATE = "neon.plugin.lang.translate"
    LANG_DETECT = "neon.plugin.lang.detect"
    UTTERANCE_TRANSFORMER = "neon.plugin.text"
    METADATA_TRANSFORMER = "neon.plugin.metadata"
    AUDIO_TRANSFORMER = "neon.plugin.audio"
    QUESTION_SOLVER = "neon.plugin.solver"
    COREFERENCE_SOLVER = "intentbox.coreference"
    KEYWORD_EXTRACTION = "intentbox.keywords"
    UTTERANCE_SEGMENTATION = "intentbox.segmentation"
    TOKENIZATION = "intentbox.tokenization"
    POSTAG = "intentbox.postag"
```
plugins can also optionally provide metadata about language support and sample configs via the `{plugin_type}.config` entrypoint

A typical `setup.py` for a plugin looks like this
```python
from setuptools import setup

### replace this data with your plugin specific info
PLUGIN_TYPE = "mycroft.plugin.stt"  # see Enum above
PLUGIN_NAME = "ovos-stt-plugin-name"
PLUGIN_PKG = PLUGIN_NAME.replace("-", "_")
PLUGIN_CLAZZ = "MyPlugin"
PLUGIN_CONFIGS = "MyPluginConfig"
###

PLUGIN_ENTRY_POINT = f'{PLUGIN_NAME} = {PLUGIN_PKG}:{PLUGIN_CLAZZ}'
CONFIG_ENTRY_POINT = f'{PLUGIN_NAME}.config = {PLUGIN_PKG}:{PLUGIN_CONFIGS}'

# add version, author, license, description....
setup(
    name=PLUGIN_NAME,
    version='0.1.0',
    packages=[PLUGIN_PKG],
    install_requires=["speechrecognition>=3.8.1",
                      "ovos-plugin-manager>=0.0.1"],
    keywords='mycroft ovos plugin',
    entry_points={PLUGIN_TYPE: PLUGIN_ENTRY_POINT,
                  f'{PLUGIN_TYPE}.config': CONFIG_ENTRY_POINT}
)
```


## Projects using OPM

OPM plugins are know to be natively supported by the following projects (non-exhaustive list)

- [ovos-core](https://github.com/OpenVoiceOS/ovos-core)
- [ovos-local-backend](https://github.com/OpenVoiceOS/ovos-local-backend)
- [ovos-tts-server](https://github.com/OpenVoiceOS/ovos-tts-server)
- [ovos-stt-http-server](https://github.com/OpenVoiceOS/ovos-stt-http-server)
- [ovos-translate-server](https://github.com/OpenVoiceOS/ovos-translate-server)
- [neon-core](https://github.com/NeonGeckoCom/NeonCore)
- [HiveMind voice satellite](https://github.com/JarbasHiveMind/HiveMind-voice-sat)

Additionally, some plugins (AudioService, WakeWord, TTS and STT) are also backwards compatible with mycroft-core
