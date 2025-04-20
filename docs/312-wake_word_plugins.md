# Wake Word Plugins

Wake Word plugins allow Open Voice OS to detect specific words or sounds, typically the assistant’s name (e.g., "Hey Mycroft"), but can be customized for various use cases. These plugins enable the system to listen for and react to activation commands or phrases.

## Available Plugins

OVOS supports different wake word detection plugins, each with its own strengths and use cases

The default OVOS plugins are:

- **[ovos-ww-plugin-precise-lite](https://github.com/OpenVoiceOS/ovos-ww-plugin-precise-lite)**: A model-based plugin that uses a trained machine learning model to detect wake words.
- **[ovos-ww-plugin-vosk](https://github.com/OpenVoiceOS/ovos-ww-plugin-vosk)**: A text-based plugin leveraging Vosk, which allows you to define a wake word without requiring a trained model. This is useful during the initial stages of data collection.

Each plugin has its pros and cons, with Vosk offering a faster setup for simple wakeword recognition without model training.

## Wakeword Configuration

The `hotwords` section in your `mycroft.conf` allows you to configure the wakeword detection parameters for each plugin. For instance:

```json
"hotwords": {
  "hey_mycroft": {
    "module": "ovos-ww-plugin-precise-lite",
    "model": "https://github.com/OpenVoiceOS/precise-lite-models/raw/master/wakewords/en/hey_mycroft.tflite",
    "expected_duration": 3,
    "trigger_level": 3,
    "sensitivity": 0.5,
    "listen": true
  }
}
```

> 💡 see the full docs for the [listener service](https://openvoiceos.github.io/ovos-technical-manual/101-speech_service/#hotwords)


## Tips and Caveats

- **Vosk Plugin**: The Vosk plugin is useful when you need a simple setup that doesn’t require training a wake word model. It’s great for quickly gathering data during the development stage.
- **Precision and Sensitivity**: Adjust the `sensitivity` and `trigger_level` settings carefully. Too high a sensitivity can lead to false positives, while too low may miss detection.

## Plugin Development

### Key Methods

When developing a custom wake word plugin, the following methods are essential:

- **`found_wake_word(frame_data)`**: This method must be defined. It checks whether a wake word is found in the provided audio data.

- **`update(chunk)`**: An optional method for processing live audio chunks and making streaming predictions.

- **`stop()`**: An optional method to shut down the plugin, like unloading data or halting external processes.

> ⚠️ `found_wake_word(frame_data)` should ignore `frame_data`, this has been deprecated and is only provided for backwards-compatibility. Plugins are now expected to handle real time audio via `update` method

### Registering Your Plugin

To integrate your custom plugin, add it to OVOS via the following entry point:

```python
setup([...], entry_points={'mycroft.plugin.wake_word': 'example_wake_word_plugin = my_example_ww:MyWakeWordEngine'})
```

### Example Plugin

Here’s a simple implementation of a wake word plugin:

```python
from ovos_plugin_manager.templates.hotwords import HotWordEngine
from threading import Event

class MyWWPlugin(HotWordEngine):
    def __init__(self, key_phrase="hey mycroft", config=None, lang="en-us"):
        super().__init__(key_phrase, config, lang)
        self.detection = Event()
        self.engine = MyWW(key_phrase)

    def found_wake_word(self, frame_data):
        # NOTE: frame_data should be ignored, it is deprecated
        # inference happens via the self.update_method
        detected = self.detection.is_set()
        if detected:
            self.detection.clear()
        return detected

    def update(self, chunk):
        if self.engine.found_it(chunk):
            self.detection.set()

    def stop(self):
        self.engine.bye()
```
