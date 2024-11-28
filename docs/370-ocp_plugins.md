# OCP Plugins

handle extracting playable streams and metadata, skills might require specific plugins and will be ignored if plugins are missing

these plugins are used when a `sei//` is requested explicitly by a skill, or when a url pattern matches

| plugin  | descripton | Stream Extractor Ids | url pattern | 
|-------------------------------------------------------------------------------------|--------------------------|-------------------------------------------------|-----------------------------------------------------|
| [ovos-ocp-rss-plugin](https://github.com/OpenVoiceOS/ovos-ocp-rss-plugin)           | rss feeds                | `rss//`                                         |                                                     | 
| [ovos-ocp-bandcamp-plugin](https://github.com/OpenVoiceOS/ovos-ocp-bandcamp-plugin) | bandcamp urls            | `bandcamp//`                                    | `"bandcamp." in url`                                |
| [ovos-ocp-youtube-plugin](https://github.com/OpenVoiceOS/ovos-ocp-youtube-plugin)   | youtube urls             | `youtube//` , `ydl//`, `youtube.channel.live//` | `"youtube.com/" in url or "youtu.be/" in url`       |
| [ovos-ocp-m3u-plugin](https://github.com/OpenVoiceOS/ovos-ocp-m3u-plugin)           | .pls and .m3u formats    |`m3u//` , `pls//`                                | `".pls" in uri or ".m3u" in uri`                    |
| [ovos-ocp-news-plugin](https://github.com/OpenVoiceOS/ovos-ocp-news-plugin)         |  dedicated news websites |  `news//`                                       | `any([uri.startswith(url) for url in URL_MAPPINGS])`|

## Standalone Usage

TODO

## Plugin Template

TODO
