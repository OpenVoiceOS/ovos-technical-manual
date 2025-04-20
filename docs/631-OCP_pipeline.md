# **OCP Pipeline**

The **OCP (OVOS Common Playback)** framework integrates seamlessly into **ovos-core** to enhance media playback through an intent-aware, classifier-assisted pipeline. It enables automatic recognition and handling of media-related queries, such as "play music", "pause video", or "next song", using trained classifiers, skill catalogs, and playback-specific filters.

---

## **Pipeline Components**

The OCP Pipeline includes multiple components, designed to detect and handle media-related intents with varying levels of certainty. These components are placed in order of confidence in the intent pipeline configuration:

### 1. **ocp_high - Unambiguous**
- **Purpose**: Catches queries that are *clearly* and *explicitly* about media playback.
- **Behavior**: Triggers only when the user's request unambiguously indicates a media action, e.g., "play Metallica".
- **Usage**: Should be placed before other components in the pipeline.

```json
{
  "intents": {
    "pipeline": [
      "converse",
      "ocp_high",
      "fallback_low"
    ]
  }
}
```

### 2. **ocp_medium - Semi-Ambiguous**
- **Purpose**: Handles likely media queries where intent is probable but not definite.
- **Behavior**: Uses a binary classifier to determine if the utterance is media-related.
- **Usage**: Placed after `ocp_high`, to catch softer matches.

```json
{
  "intents": {
    "pipeline": [
      "converse",
      "ocp_medium",
      "fallback_low"
    ]
  }
}
```

### 3. **ocp_low - Ambiguous**
- **Purpose**: Captures uncertain queries using keyword-based heuristics.
- **Behavior**: Triggers whenever the query includes a known media-related keyword (e.g., artist, movie name) registered by a skill.
- **Usage**: Last media filter, before true fallback layers.

```json
{
  "intents": {
    "pipeline": [
      "converse",
      "ocp_low",
      "fallback_low"
    ]
  }
}
```

---

## **Skill Media Keywords**

OCP-aware skills can **register domain-specific media keywords** — such as artist names, albums, show titles, etc. — that enhance intent recognition and classification.

These keywords serve multiple purposes:

- Improve query classification (e.g., "play Enter Sandman" → MUSIC).
- Influence classifier bias when media type is uncertain (e.g., "Matrix" might bias toward MUSIC if it's registered only as a song).
- Expand the available media catalog dynamically as new skills are added.

> ⚠️ The `ocp_low` component relies on these keywords and will initiate a media search whenever known media is present on the text, even on questions unrelated to media playback

---

## **MediaType Matching**

To improve accuracy and reduce search latency, OCP classifies user queries into **media types**:

Examples of supported media types:

- `music`
- `movie`
- `radio`
- `podcast`
- `audiobook`
- `news`

Media type is inferred in two ways:

- **Keyword-Based Classification** (default): If the query includes words like "movie", "news", or "podcast", OCP assigns the relevant media type.
- **Classifier-Based Matching** (optional/experimental): An **experimental** classifier (currently English-only) predicts the media type based on the full query and context. This classifier adapts its bias dynamically using registered skills as a catalog — for instance, if a skill registers "Matrix" as a *song*, the classifier is more likely to infer MUSIC rather than MOVIE.

The default implementation is currently quite limited, each keyword triggers its corresponding `MediaType`. For example:

- A query containing the keyword **"movie"** will be categorized as **MediaType.MOVIE**.
- A query with **"news"** will be classified as **MediaType.NEWS**.
- A query with **"radio"** will be categorized under **MediaType.RADIO**.

> 💡 A query is initially classified as **MediaType.GENERIC** unless it explicitly contains a keyword

---

## **Result Filtering**

After potential media results are gathered from OCP-enabled skills, the pipeline **filters** them based on a series of rules to ensure only valid and playable entries are passed to the playback engine.

- Confidence Score Threshold: Each result includes a `match_confidence` score (0–100). If it falls below the configured `min_score` threshold, it is **discarded**.
- Media Type Filtering: If the query was classified as a specific media type (e.g., MUSIC), results with a different media type (e.g., PODCAST) are **removed**.
- Plugin Availability (Stream Extractors): Each result has a URI scheme (e.g., `http://`, `spotify://`). If the required plugin is not installed (like `ovos-media-spotify`), the result is **discarded** to prevent playback errors.
- Audio/Video Preference Filtering: If the user (or system) requests audio-only or video-only playback
   - Audio mode skips video-only results.
   - Video mode skips pure audio unless a video is attached.
   - The pipeline dynamically adapts if the query includes hints like "watch" or "just audio".

---

## **Playback Frameworks**

Once the best match is selected, **playback is delegated** to the appropriate media plugin loaded via `ovos-audio`. 

Skills do **not** handle media playback directly — they return media candidates, and **OCP takes care of launching and controlling the stream**.

> 💡 Skills act as media catalogs. Playback is handled by `ovos-audio`, and only skills with compatible stream URIs are selected.

---

## OCP Configuration Options

All of the above behavior is customizable using config settings under `"OCP"` in the `"intents"` subsection of your `mycroft.conf`.

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
| Key                             | Type    | Default | Description                                                                      |
|----------------------------------|---------|---------|----------------------------------------------------------------------------------|
| `experimental_media_classifier`  | bool    | false   | Enable ML-based media type classification (English only).                        |
| `experimental_binary_classifier` | bool    | false   | Enable ML-based media detection for `ocp_medium` (is this query media-related?). |
| `legacy`                         | bool    | false   | Use legacy audio service bus api instead of OCP bus api (not recommended).       |
| `classifier_threshold`           | float   | 0.4     | Minimum confidence for trusting classifier result (0.0–1.0).                     |
| `min_score`                      | int     | 40      | Minimum match confidence for keeping a skill result (0–100).                     |
| `filter_media`                   | bool    | true    | Enable media type-based result filtering.                                        |
| `filter_SEI`                     | bool    | true    | Filter out results that require unavailable plugins (Stream Extractors).         |
| `playback_mode`                  | int     | 0       | Playback preference: `0` = auto, `10` = audio-only, `20` = video-only.           |
| `search_fallback`                | bool    | true    | perform a second GENERIC media seach if no type-specific results are found.      |

---

## Summary

- OCP uses classifiers, keyword matching, and skill-registered catalogs to intelligently route and filter media playback requests.
- It prioritizes clarity of intent through configurable components (`ocp_high`, `ocp_medium`, `ocp_low`).
- Post-matching filters guarantee high confidence, media type compatibility, plugin support, and preferred output mode.
- Playback is entirely managed by `ovos-audio` using the selected plugin backend — skills never handle actual playback.

---

## Media Query Embeddings (upcoming)

![image](https://github.com/user-attachments/assets/5420d3bf-928c-41ff-9eba-d1f57d10a65e)

![image](https://github.com/user-attachments/assets/1841e0da-e976-49d0-8d7d-1ced6df4c45c)