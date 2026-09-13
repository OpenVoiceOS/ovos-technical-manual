# Media Playback Plugins

OVOS Media Plugins handle media playback, enabling OVOS to interact with popular streaming services and media players for audio, video, and remote control.

## Available Plugins

Here are the key media plugins available in OVOS:

| Plugin | Audio | Video | Web | Remote | Notes |
|--------|-------|-------|-----|--------|-------|
| **[ovos-media-plugin-chromecast](https://github.com/OpenVoiceOS/ovos-media-plugin-chromecast)** | ✔️ | ✔️ | ❌ | ✔️ | Extra: [cast_control](https://github.com/alexdelorenzo/cast_control) for MPRIS interface |
| **[ovos-media-plugin-spotify](https://github.com/OpenVoiceOS/ovos-media-plugin-spotify)** | ✔️ | ❌ | ❌ | ✔️ | Requires premium account<br>Extra: [spotifyd](https://github.com/Spotifyd/spotifyd) for native Spotify player |

Each plugin is designed for specific media platforms and devices, allowing OVOS to interact with popular streaming services and media players.


> ⚠️ `ovos-media` is a work in progress and has not yet been released, plugins support both `ovos-audio` and `ovos-media`

---

## ovos-media-plugin-spotify

The **ovos-media-plugin-spotify** allows OVOS to initiate playback on Spotify, enabling integration with OVOS systems.

> ⚠️ The [companion skill](https://github.com/OpenVoiceOS/skill-ovos-spotify) is needed for voice search integration.

### Installation

To install the plugin, use the following command:

```bash
pip install ovos-media-plugin-spotify
```
> 💡 If you want to make the OVOS device itself a Spotify player, we recommend using [spotifyd](https://github.com/Spotifyd/spotifyd).

### OAuth Setup

Currently, OAuth needs to be performed manually. After installing the plugin, run the following command:

```bash
$ ovos-spotify-oauth
```

This will prompt you to enter your Spotify developer credentials after you have created an application on [Spotify Developer Dashboard](https://developer.spotify.com). Follow the instructions and enter the provided information.

Example output:

```bash
$ ovos-spotify-oauth
This script creates the token information needed for running spotify
        with a set of personal developer credentials.

        It requires the user to go to developer.spotify.com and set up a
        developer account, create an "Application" and make sure to whitelist
        "https://localhost:8888".

        After you have done that enter the information when prompted and follow
        the instructions given.
        
YOUR CLIENT ID: xxxxx
YOUR CLIENT SECRET: xxxxx
Go to the following URL: https://accounts.spotify.com/authorize?client_id=xxx&response_type=code&redirect_uri=https%3A%2F%2Flocalhost%3A8888&scope=user-library-read+streaming+playlist-read-private+user-top-read+user-read-playback-state
Enter the URL you were redirected to: https://localhost:8888/?code=.....
ocp_spotify oauth token saved
```

### Configuration

After OAuth setup, edit your `mycroft.conf` to expose your Spotify players. 

Use the provided `ovos-spotify-autoconfigure` script to automatically configure all Spotify devices under your `mycroft.conf`:

```bash
$ ovos-spotify-autoconfigure
This script will auto configure ALL spotify devices under your mycroft.conf
        
        SPOTIFY PREMIUM is required!
        
        If you have not yet authenticated your spotify account, run 'ovos-spotify-oauth' first!
        
Found device: OpenVoiceOS-TV

mycroft.conf updated!

# Legacy Audio Service:
{'backends': {'spotify-OpenVoiceOS-TV': {'active': True,
                                         'identifier': 'OpenVoiceOS-TV',
                                         'type': 'ovos_spotify'}}}

# ovos-media Service:
{'audio_players': {'spotify-OpenVoiceOS-TV': {'active': True,
                                              'aliases': ['OpenVoiceOS-TV'],
                                              'identifier': 'OpenVoiceOS-TV',
                                              'module': 'ovos-media-audio-plugin-spotify'}}}
```

---

## ovos-media-plugin-chromecast

The **ovos-media-plugin-chromecast** allows OVOS to initiate playback on Chromecast devices, enabling integration with OVOS systems.

### Installation

To install the plugin, use the following command:

```bash
pip install ovos-media-plugin-chromecast
```

![Chromecast Integration](https://github.com/OpenVoiceOS/ovos-media-plugin-chromecast/assets/33701864/b1c7de47-750c-478a-9ebe-15d4076eb71c)

> 💡 If you want to control Chromecast playback externally, you can install [cast_control](https://github.com/alexdelorenzo/cast_control) to enable MPRIS interface integration.

### Configuration

Use the `ovos-chromecast-autoconfigure` script to automatically configure Chromecast devices under your `mycroft.conf`:

```bash
$ ovos-chromecast-autoconfigure
```

This script will discover Chromecast devices on your network and update `mycroft.conf` with the necessary configuration.

Example output:

```bash
$ ovos-chromecast-autoconfigure
Scanning...
    - Found Chromecast: Bedroom TV - 192.168.1.17:8009

Found devices: ['Bedroom TV']

mycroft.conf updated!

# Legacy Audio Service:
{'backends': {'chromecast-bedroom-tv': {'active': True,
                                        'identifier': 'Bedroom TV',
                                        'type': 'ovos_chromecast'}}}

# ovos-media Service:
{'audio_players': {'chromecast-bedroom-tv': {'active': True,
                                             'aliases': ['Bedroom TV'],
                                             'identifier': 'Bedroom TV',
                                             'module': 'ovos-media-audio-plugin-chromecast'}}},
 'video_players': {'chromecast-bedroom-tv': {'active': True,
                                             'aliases': ['Bedroom TV'],
                                             'identifier': 'Bedroom TV',
                                             'module': 'ovos-media-video-plugin-chromecast'}}}
```

---

### Summary

OVOS Media Plugins, like **ovos-media-plugin-spotify** and **ovos-media-plugin-chromecast**, provide seamless integration with popular media platforms and devices, allowing you to control playback directly through OVOS. Whether it's streaming from Spotify, controlling Chromecast devices, or casting media, these plugins enhance the flexibility of the OVOS ecosystem for media playback.