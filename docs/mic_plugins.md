## Microphone Plugins

**NEW** in `ovos-core` version **0.0.8**

Microphone plugins are responsible for feeding audio to the listener

| plugin                                                                                                  | notes                                                                              | operating systems           |
|---------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------|-----------------------------|
| [ovos-microphone-plugin-alsa](https://github.com/OpenVoiceOS/ovos-microphone-plugin-alsa)               | based on [pyalsaaudio](http://larsimmisch.github.io/pyalsaaudio), best performance | linux                       |
| [ovos-microphone-plugin-pyaudio](https://github.com/OpenVoiceOS/ovos-microphone-plugin-pyaudio)         | based on [PyAudio](https://people.csail.mit.edu/hubert/pyaudio/)                   | linux                       |
| [ovos-microphone-plugin-sounddevice](https://github.com/OpenVoiceOS/ovos-microphone-plugin-sounddevice) | based on [python-sounddevice](https://github.com/spatialaudio/python-sounddevice)  | linux <br> mac <br> windows |
| [ovos-microphone-plugin-files](https://github.com/OpenVoiceOS/ovos-microphone-plugin-files)             | audio files as input                                                               | linux <br> mac <br> windows |
| [ovos-microphone-plugin-arecord](https://github.com/OVOSHatchery/ovos-microphone-plugin-arecord)        | uses `arecord` via subprocess                                                      | linux                       |
| [ovos-microphone-plugin-socket](https://github.com/OVOSHatchery/ovos-microphone-plugin-socket)          | demo plugin for remote microphone                                                  | linux <br> mac <br> windows |


## Standalone Usage

TODO

## Plugin Template

TODO
