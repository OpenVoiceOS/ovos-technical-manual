# OCP Pipeline

The **OCP (OVOS Common Playback)** Pipeline Plugin integrates seamlessly into the OVOS intent processing framework,
enabling intelligent handling of media-related voice commands. By leveraging classifiers, skill-registered catalogs, and
playback-specific filters, OCP facilitates accurate recognition and execution of user requests such as "play music," "
pause video," or "next song."

---

## Pipeline Components

The OCP Pipeline Plugin registers three components within the OVOS intent pipeline, each corresponding to different
confidence levels in interpreting media-related intents:

| Pipeline ID  | Description                            | Recommended Use                                                                    |   
|--------------|----------------------------------------|------------------------------------------------------------------------------------|
| `ocp_high`   | High-confidence media intent matches   | ✅ Primary media commands                                                           |   
| `ocp_medium` | Medium-confidence media intent matches | ⚠️ Ambiguous media queries                                                         |   
| `ocp_low`    | Low-confidence media intent matches    | 🚫 Broad keyword matches. Only use if the device is exclusively for media playback |   

These components should be ordered in the pipeline to prioritize higher-confidence matches

---

## Intent Classification

OCP employs a combination of techniques to classify and handle media-related intents:

* **Keyword-Based Matching**: Identifies explicit media-related terms in user utterances.

* **Skill-Registered Keywords**: Utilizes media keywords registered by OCP-aware skills (e.g., artist names, show
  titles) to enhance intent recognition.

* **Media Type Classification**: Assigns a media type (e.g., music, podcast, movie) to the query based on keywords or an
  optional experimental classifier.

> ⚠️ The `ocp_low` component relies on skill-registered keywords and may trigger on queries that include known media
> terms, even if the user's intent is not to initiate playback.

---

## Media Type Handling

OCP supports various media types, including:

* `music`
* `podcast`
* `movie`
* `radio`
* `audiobook`
* `news`

Media type classification is primarily based on keywords within the user's query. For example, a query containing "play
the latest news" would be classified under the `news` media type. An experimental classifier can also be enabled to
predict media types based on the full query context.

---

## Result Filtering

After gathering potential media results from OCP-enabled skills, the plugin applies several filters to ensure relevance
and playability:

* **Confidence Threshold**: Results with a `match_confidence` below the configured `min_score` are discarded.

* **Media Type Consistency**: If a media type has been classified, results of differing types are removed.

* **Plugin Availability**: Results requiring unavailable playback plugins (e.g., `spotify://` URIs without the Spotify
  plugin) are excluded.

* **Playback Mode Preference**: Respects user or system preferences for audio-only or video-only playback, filtering out
  incompatible results.

---

## Playback Management

OCP delegates the actual media playback to the appropriate plugin managed by `ovos-audio`. Skills act solely as media
catalogs, providing search results without handling playback directly. This separation ensures a consistent and
centralized playback experience across different media types and sources.

the OCP Pipeline keeps track of media player status across `Session`s, this is taken into account during the intent
matching process

eg. if no media player is active, then "next song" will not trigger

---

## Configuration Options

OCP behavior can be customized via the `mycroft.conf` file under the `intents` section:

```json
{
  "intents": {
    "OCP": {
      "experimental_media_classifier": false,
      "experimental_binary_classifier": false,
      "legacy": false,
      "classifier_threshold": 0.4,
      "min_score": 40,
      "filter_media": true,
      "filter_SEI": true,
      "playback_mode": 0,
      "search_fallback": true
    }
  }
}
```

| Key                              | Type  | Default | Description                                                            |                                                       
|----------------------------------|-------|---------|------------------------------------------------------------------------| 
| `experimental_media_classifier`  | bool  | false   | Enable ML-based media type classification (English only).              |                                                       
| `experimental_binary_classifier` | bool  | false   | Enable ML-based media detection for `ocp_medium`.  (English only).     |                                                       
| `legacy`                         | bool  | false   | Use legacy audio service API instead of OCP (not recommended).         |                                                       
| `classifier_threshold`           | float | 0.4     | Minimum confidence for trusting classifier results (0.0–1.0).          |                                                       
| `min_score`                      | int   | 40      | Minimum match confidence to retain a skill result (0–100).             |                                                       
| `filter_media`                   | bool  | true    | Enable media type-based result filtering.                              |                                                       
| `filter_SEI`                     | bool  | true    | Filter out results requiring unavailable plugins (Stream Extractors).  |                                                       
| `playback_mode`                  | int   | 0       | Playback preference: `0` = auto, `10` = audio-only, `20` = video-only. |                                                       
| `search_fallback`                | bool  | true    | Perform a generic media search if no type-specific results are found.  |

---
