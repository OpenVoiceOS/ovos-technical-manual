# OVOS Common Playback - Stream Extractor Plugins

OVOS Common Playback (OCP) Stream Extractor Plugins are designed to handle the extraction of playable streams and their associated metadata just before playback. This delegation allows skills to focus on their core functionality without having to worry about stream extraction, thus preventing additional latency during search or other operations. The relevant plugin is automatically invoked based on the **Stream Extractor Identifier (SEI)** or a matching URL pattern.

A SEI typically precedes the URI, which is used to access the stream. If the required plugin is missing, the corresponding request will be ignored.

## Available Plugins

Here are the key stream extractor plugins available in OVOS:

| Plugin | Description | Stream Extractor IDs (SEIs) | URL Pattern |
|--------|-------------|-----------------------------|-------------|
| **[ovos-ocp-rss-plugin](https://github.com/OpenVoiceOS/ovos-ocp-rss-plugin)** | Handles RSS feed URLs | `rss//` | N/A |
| **[ovos-ocp-bandcamp-plugin](https://github.com/OpenVoiceOS/ovos-ocp-bandcamp-plugin)** | Handles Bandcamp URLs | `bandcamp//` | `"bandcamp." in url` |
| **[ovos-ocp-youtube-plugin](https://github.com/OpenVoiceOS/ovos-ocp-youtube-plugin)** | Handles YouTube URLs | `youtube//`, `ydl//`, `youtube.channel.live//` | `"youtube.com/" in url` or `"youtu.be/" in url` |
| **[ovos-ocp-m3u-plugin](https://github.com/OpenVoiceOS/ovos-ocp-m3u-plugin)** | Handles `.pls` and `.m3u` file formats | `m3u//`, `pls//` | `".pls" in uri or ".m3u" in uri` |
| **[ovos-ocp-news-plugin](https://github.com/OpenVoiceOS/ovos-ocp-news-plugin)** | Handles dedicated news websites | `news//` | `any([uri.startswith(url) for url in URL_MAPPINGS])` |

Each plugin is designed to extract and process streams from specific types of content sources, ensuring seamless integration of services like YouTube, Bandcamp, RSS feeds, and more without introducing delays in skill interactions.

## Standalone Usage

*TODO: Instructions for using the plugins in a standalone setup*

## Plugin Template

*TODO: Template for creating a new stream extractor plugin*

### Summary

These plugins delegate the task of stream extraction to just before playback, relieving skills from the burden of handling it themselves and preventing latency during search or other tasks. They ensure OVOS can integrate various streaming services efficiently by using SEIs to identify the stream and process the corresponding URI automatically.