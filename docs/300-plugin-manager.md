# OVOS Plugin Manager (OPM)

![image](https://github.com/OpenVoiceOS/ovos-plugin-manager/assets/33701864/8c939267-42fc-4377-bcdb-f7df65e73252)

## Summary

The OVOS Plugin Manager (OPM) is a base package designed to provide arbitrary plugins to the OVOS ecosystem. It standardizes the interface for plugins, allowing them to be easily portable and configurable, whether integrated into OVOS projects or used in standalone applications.

---

## Usage Guide

To install a plugin using OPM, you can typically follow this process:

- **Install the plugin using pip:**
```bash
pip install ovos-plugin-name
```
- **Edit your configuration file (e.g., `mycroft.conf`) to enable and configure the plugin.**
- **Restart your OVOS service to apply the changes.**

> 💡 In some setups like `ovos-docker`, make sure you install plugins in the correct environment.

---

## Technical Explanation

OPM allows developers to create plugins that are decoupled from OVOS core functionality. By using OPM's standard interface, plugins can be easily integrated into a variety of OVOS services or other projects.

Each plugin can be classified according to its functionality, with its own entry point defined in `setup.py`. This approach ensures that plugins are portable and independent, allowing them to be reused in other projects.

---

## Plugin Types

OPM recognizes several plugin types, each serving a specific purpose within the OVOS ecosystem. These types help categorize plugins for easier integration and configuration:

```python
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

Each plugin type has its own category, with the most common being `skill`, `stt` (speech-to-text), `tts` (text-to-speech), and `wake_word`.

---

## Plugin Packaging

When creating a plugin, you need to define an entry point for the plugin type and class in your `setup.py`. Here’s a typical `setup.py` structure for packaging a plugin:

```python
from setuptools import setup

PLUGIN_TYPE = "mycroft.plugin.stt"  # Adjust based on the plugin type
PLUGIN_NAME = "ovos-stt-plugin-name"
PLUGIN_PKG = PLUGIN_NAME.replace("-", "_")
PLUGIN_CLAZZ = "MyPlugin"
PLUGIN_CONFIGS = "MyPluginConfig"

PLUGIN_ENTRY_POINT = f'{PLUGIN_NAME} = {PLUGIN_PKG}:{PLUGIN_CLAZZ}'
CONFIG_ENTRY_POINT = f'{PLUGIN_NAME}.config = {PLUGIN_PKG}:{PLUGIN_CONFIGS}'

setup(
    name=PLUGIN_NAME,
    version='0.1.0',
    packages=[PLUGIN_PKG],
    install_requires=["speechrecognition>=3.8.1", "ovos-plugin-manager>=0.0.1"],
    keywords='mycroft ovos plugin',
    entry_points={PLUGIN_TYPE: PLUGIN_ENTRY_POINT, f'{PLUGIN_TYPE}.config': CONFIG_ENTRY_POINT}
)
```

---

## 🛰️ Voice Satellites

HiveMind setups allow you to configure which plugins run server-side or satellite-side. Here are two examples:

- **Skills Server**: In this setup, the HiveMind server runs only core services and skills, while the satellites handle their own STT/TTS.

  ![Server Profile](https://github.com/OpenVoiceOS/ovos-technical-manual/assets/33701864/55694b82-69c9-4288-9a89-1d9716eb3c57)

- **Audio Server**: Here, the HiveMind server runs a full OVOS core, handling STT/TTS for the satellites.

  ![Listener Profile](https://github.com/OpenVoiceOS/ovos-technical-manual/assets/33701864/1455a488-af0f-44b4-a5e6-0418a7cd1f96)

These profiles help balance the workload between the server and satellites, improving performance based on the setup.

---

## Projects Using OPM

Several OVOS projects and tools support OPM plugins, either as dependencies or directly within their ecosystem:

- [ovos-core](https://github.com/OpenVoiceOS/ovos-core)
- [ovos-tts-server](https://github.com/OpenVoiceOS/ovos-tts-server)
- [ovos-stt-http-server](https://github.com/OpenVoiceOS/ovos-stt-http-server)
- [ovos-translate-server](https://github.com/OpenVoiceOS/ovos-translate-server)
- [neon-core](https://github.com/NeonGeckoCom/NeonCore)
- [HiveMind voice satellite](https://github.com/JarbasHiveMind/HiveMind-voice-sat)

Additionally, some plugins like AudioService, WakeWord, TTS, and STT are backwards compatible with Mycroft-Core, ensuring broad compatibility.

--- 

## Related Links

- [OVOS Plugin Manager Repository](https://github.com/OpenVoiceOS/OVOS-plugin-manager)
- [OVOS Installer](https://github.com/OpenVoiceOS/ovos-installer)