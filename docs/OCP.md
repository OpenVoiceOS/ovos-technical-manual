# ovos-media  


> **EXPERIMENTAL** - **NEW** `ovos-core` version **0.0.8**

ovos-media is a work in progress, it does not yet ship with OVOS by default, but it can be manually enabled

In order to use ovos-media you need to enable the OCP pipeline in ovos-core and to disable the old audio service 

disabling old audio service
```json
{
  "enable_old_audioservice": false
}
```
Enabling OCP pipeline

```javascript
{
  // Intent Pipeline / plugins config
  "intents" : {
    // the pipeline is a ordered set of frameworks to send an utterance too
    // if one of the frameworks fails the next one is used, until an answer is found
    "pipeline": [
        "converse",
        "ocp_high",
        "...",
        "common_qa",
        "ocp_medium",
        "...",
        "ocp_fallback",
        "fallback_low"
    ]
  }
}
```


## OCP

![](https://github.com/OpenVoiceOS/ovos_assets/blob/master/Logo/ocp.png?raw=true)

[OCP](https://github.com/OpenVoiceOS/ovos-ocp-audio-plugin) stands for OpenVoiceOS Common Play, it is a full-fledged
media player service that can handle audio and video

> **DEPRECATION WARNING** OCP is in the process of migrating from a audio plugin to ovos-media service, 
this documentation is not valid for `ovos-core` version **0.0.7**

OCP provides a pipeline component specialized in matching media queries.

The pipeline classifies the media type (movie, music, podcast...) and queries OCP skills for results, you can read more about the [OCP Pipeline docs]()


## Architecture

![imagem](https://github.com/NeonJarbas/ovos-media/assets/59943014/7dc1d635-4340-43db-a38d-294cfedab70f)


## Media Intents

Before regular intent stage, taking into account current OCP state  (media ready to play / playing)

- `"play {query}"`
- `"previous"`  (media needs to be loaded)
- `"next"`  (media needs to be loaded)
- `"pause"`  (media needs to be loaded)
- `"play"` / `"resume"` (media needs to be loaded)
- `"stop"` (media needs to be loaded)
- `"I like that song"`  (music needs to be playing)

## Plugins

### OCP Skills

Skills provide search results, think about them as media providers/catalogs for OCP

You can find OCP skills in the [awesome-ocp-skills](https://github.com/OpenVoiceOS/awesome-ocp-skills) list 

### Media Plugins

these plugins handle the actual track playback. OCP virtual player delegates media playback to these plugins

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


### OCP Plugins

handle extracting playable streams and metadata, skills might require specific plugins and will be ignored if plugins are missing

these plugins are used when a `sei//` is requested explicitly by a skill, or when a url pattern matches

| plugin  | descripton | Stream Extractor Ids | url pattern | 
|-------------------------------------------------------------------------------------|--------------------------|-------------------------------------------------|-----------------------------------------------------|
| [ovos-ocp-rss-plugin](https://github.com/OpenVoiceOS/ovos-ocp-rss-plugin)           | rss feeds                | `rss//`                                         |                                                     | 
| [ovos-ocp-bandcamp-plugin](https://github.com/OpenVoiceOS/ovos-ocp-bandcamp-plugin) | bandcamp urls            | `bandcamp//`                                    | `"bandcamp." in url`                                |
| [ovos-ocp-youtube-plugin](https://github.com/OpenVoiceOS/ovos-ocp-youtube-plugin)   | youtube urls             | `youtube//` , `ydl//`, `youtube.channel.live//` | `"youtube.com/" in url or "youtu.be/" in url`       |
| [ovos-ocp-m3u-plugin](https://github.com/OpenVoiceOS/ovos-ocp-m3u-plugin)           | .pls and .m3u formats    |`m3u//` , `pls//`                                | `".pls" in uri or ".m3u" in uri`                    |
| [ovos-ocp-news-plugin](https://github.com/OpenVoiceOS/ovos-ocp-news-plugin)         |  dedicated news websites |  `news//`                                       | `any([uri.startswith(url) for url in URL_MAPPINGS])`|


## MPRIS integration

OCP Integrates with MPRIS allows OCP to control external players

![imagem](https://github.com/NeonJarbas/ovos-media/assets/33701864/856c0228-8fc5-4ee6-a19d-4290f2e07258)

### Sync with external players

Via MPRIS OCP can control and display data from external players, 
if using KDEConnect this includes playback in connected devices

See a demo here

[![demo video](https://img.youtube.com/vi/1KMFV0UVYEM/default.jpg)](https://www.youtube.com/watch?v=1KMFV0UVYEM)

This also includes voice intents, allowing you for example to voice control spotify

### Manage multiple players

If OCP is set to manage external players it will ensure only one of them is playing media at once, 
if using KDEConnect this includes playback in connected devices

See a demo here (**warning**: contains black metal)

[![demo video](https://img.youtube.com/vi/YzC7oFYCcRE/default.jpg)](https://www.youtube.com/watch?v=YzC7oFYCcRE)


## Skills Menu

Some skills provide featured_media, you can access these from the OCP menu

![](https://github.com/OpenVoiceOS/ovos_assets/raw/master/Images/ocp/ocp_skills.gif)

## Homescreen widget

The homescreen skill that comes pre-installed with OpenVoiceOS also comes with a widget for the OCP framework.

![](https://raw.githubusercontent.com/OpenVoiceOS/ovos_assets/master/Images/homescreen-mediawidget.gif)

## File Browser integration

selected files will be played in OCP

![](https://github.com/OpenVoiceOS/ovos_assets/raw/master/Images/ocp/ocp_file_browser.gif)

folders are considered playlists

![](https://github.com/OpenVoiceOS/ovos_assets/raw/master/Images/ocp/folder_playlist.gif)


## Favorite Songs

You can like a song that is currently playing via GUI and intent "I like that song"

![like](https://github.com/OpenVoiceOS/ovos-media/assets/33701864/27aee29a-ca3b-4c73-992e-9fd5ef513f4d)

Liked songs can be played via intent "play my favorite songs" or GUI

![favs](https://github.com/OpenVoiceOS/ovos-media/assets/33701864/cdf7a682-c417-43f7-a4ae-589b07de55cf)


## Configuration

under mycroft.conf

```javascript
{
  // Configure ovos-media service
  // similarly to wakewords, configure any number of playback handlers
  // playback handlers might be local applications or even remote devices
  "media": {

    // order of preference to try playback handlers
    // if unavailable or unable to handle a uri, the next in list is used
    // NB: users may request specific handlers in the utterance

    // keys are the strings defined in "audio_players"
    "preferred_audio_services": ["gui", "vlc", "mplayer", "cli"],

    // keys are the strings defined in "web_players"
    "preferred_web_services": ["gui", "browser"],

    // keys are the strings defined in "video_players"
    "preferred_video_services": ["gui", "vlc"],

    // PlaybackType.AUDIO handlers
    "audio_players": {
        // vlc player uses a headless vlc instance to handle uris
        "vlc": {
            // the plugin name
            "module": "ovos-media-audio-plugin-vlc",

            // friendly names a user may use to refer to this playback handler
            // those will be parsed by OCP and used to initiate
            // playback in the request playback handler
            "aliases": ["VLC"],

            // deactivate a plugin by setting to false
            "active": true
        },
        // command line player uses configurable shell commands with file uris as arguments
        "cli": {
            // the plugin name
            "module": "ovos-media-audio-plugin-cli",

            // friendly names a user may use to refer to this playback handler
            // those will be parsed by OCP and used to initiate
            // playback in the request playback handler
            "aliases": ["Command Line"],

            // deactivate a plugin by setting to false
            "active": true
        },
        // gui uses mycroft-gui natively to handle uris
        "gui": {
            // the plugin name
            "module": "ovos-media-audio-plugin-gui",

            // friendly names a user may use to refer to this playback handler
            // those will be parsed by OCP and used to initiate
            // playback in the request playback handler
            "aliases": ["GUI", "Graphical User Interface"],

            // deactivate a plugin by setting to false
            "active": true
        }
    },

    // PlaybackType.VIDEO handlers
    "video_players": {
        // vlc player uses a headless vlc instance to handle uris
        "vlc": {
            // the plugin name
            "module": "ovos-media-video-plugin-vlc",

            // friendly names a user may use to refer to this playback handler
            // those will be parsed by OCP and used to initiate
            // playback in the request playback handler
            "aliases": ["VLC"],

            // deactivate a plugin by setting to false
            "active": true
        },
        // gui uses mycroft-gui natively to handle uris
        "gui": {
            // the plugin name
            "module": "ovos-media-video-plugin-gui",

            // friendly names a user may use to refer to this playback handler
            // those will be parsed by OCP and used to initiate
            // playback in the request playback handler
            "aliases": ["GUI", "Graphical User Interface"],

            // deactivate a plugin by setting to false
            "active": true
        }
    },

    // PlaybackType.WEBVIEW handlers
    "web_players": {
        // open url in the native browser
        "browser": {
            // the plugin name
            "module": "ovos-media-web-plugin-browser",

            // friendly names a user may use to refer to this playback handler
            // those will be parsed by OCP and used to initiate
            // playback in the request playback handler
            "aliases": ["Browser", "Local Browser", "Default Browser"],

            // deactivate a plugin by setting to false
            "active": true
        },
        // gui uses mycroft-gui natively to handle uris
        "gui": {
            // the plugin name
            "module": "ovos-media-web-plugin-gui",

            // friendly names a user may use to refer to this playback handler
            // those will be parsed by OCP and used to initiate
            // playback in the request playback handler
            "aliases": ["GUI", "Graphical User Interface"],

            // deactivate a plugin by setting to false
            "active": true
        }
    }
  }
}
```

## Troubleshooting

Having trouble getting OCP to run properly and be exposed as an MPRIS media player? Check the following:

- The `DBUS_SESSION_BUS_ADDRESS` environment variable is what OCP uses to try to connect to [`dbus`](https://www.freedesktop.org/wiki/Software/dbus/). On an OVOS system it will look something like `unix:path=/run/user/1000/bus`. To get the right user ID, run `id -u`.
  - If `DBUS_SESSION_BUS_ADDRESS` is not set, the next place OCP checks is the `DISPLAY` environment variable. If this is set and looks similar to the value above, then you can probably exclude `DBUS_SESSION_BUS_ADDRESS`, but if neither are set then use `DBUS_SESSION_BUS_ADDRESS`.
- Make sure your OCP settings in your config file like something like the following, taking note of the `dbus_type` value:
```json
"media": {
  "dbus_type": "session"
}
```
  - If your `dbus_type` is set to `system` then OCP will still work, but since it requires root privileges to read from the system dbus, external systems or programs without root privileges cannot read the MPRIS data there.

You can confirm if the OCP player is registered with dbus using the following command: `dbus-send --session --dest=org.freedesktop.DBus --type=method_call --print-reply /org/freedesktop/DBus org.freedesktop.DBus.ListNames`

The output should look something like the following, if it is working:

```bash
method return time=1691467760.293397 sender=org.freedesktop.DBus -> destination=:1.10 serial=3 reply_serial=2
   array [
      string "org.freedesktop.DBus"
      string "org.freedesktop.systemd1"
      string ":1.10"
      string "org.mpris.MediaPlayer2.OCP"
      string ":1.9"
      string ":1.1"
   ]
```

The important part is the `org.mpris.MediaPlayer2.OCP` value.

If the above steps do not work, please reach out to the OVOS team on Matrix for assistance.
