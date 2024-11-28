# Media Plugins

these plugins handle media playback. OCP virtual player delegates media playback to these plugins

| plugin  | audio | video | web | remote | notes |
|---------------------------------------------------------------------------------------------|----|----|---|----|-------------------------------------------|
| [ovos-media-plugin-simple](https://github.com/OpenVoiceOS/ovos-media-plugin-simple)         | ✔️ | ❌ | ❌ | ❌ | default for audio                         |
| [ovos-media-plugin-qt5](https://github.com/OpenVoiceOS/ovos-media-plugin-qt5)               | ✔️ | ✔️ | ✔️ | ❌ | WIP - recommended for embedded ovos-shell |
| [ovos-media-plugin-mplayer](https://github.com/OpenVoiceOS/ovos-media-plugin-mplayer)       | ✔️ | ✔️ | ❌ | ❌ | recommended for video                     |
| [ovos-media-plugin-vlc](https://github.com/OpenVoiceOS/ovos-media-plugin-vlc)               | ✔️ | ✔️ | ❌ | ❌ |                                           |
| [ovos-media-plugin-chromecast](https://github.com/OpenVoiceOS/ovos-media-plugin-chromecast) | ✔️ | ✔️ | ❌ | ✔️ | extra: [cast_control](https://github.com/alexdelorenzo/cast_control) for MPRIS interface   |
| [ovos-media-plugin-spotify](https://github.com/OpenVoiceOS/ovos-media-plugin-spotify) | ✔️ | ❌ | ❌ | ✔️ | needs premium account<br>extra: [spotifyd](https://github.com/Spotifyd/spotifyd) for native spotify player  |
| ![imagem](https://github.com/OpenVoiceOS/ovos-media/assets/33701864/90f31b0a-dd56-457d-a3cf-7fc08b460038) [ovos-media-plugin-xdg](https://github.com/NeonGeckoCom/ovos-media-plugin-xdg) | ✔️ | ✔️ | ✔️ | ❌ | [xdg-open](https://man.archlinux.org/man/xdg-open.1) is for use inside a desktop session only |
| ![imagem](https://github.com/OpenVoiceOS/ovos-media/assets/33701864/90f31b0a-dd56-457d-a3cf-7fc08b460038) [ovos-media-plugin-webbrowser](https://github.com/NeonGeckoCom/ovos-media-plugin-webbrowser) | ❌ | ❌ | ✔️ | ❌ | [webbrowser](https://docs.python.org/3/library/webbrowser.html) is for use inside a desktop session only |


## Standalone Usage

TODO

## Plugin Template

TODO
