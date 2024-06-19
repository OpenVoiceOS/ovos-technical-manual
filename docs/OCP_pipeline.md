## OCP Pipeline

> **NEW** in ovos-core **version 0.0.8**

The OCP framework matches utterances and collects playback results from skills.

The new OCP Pipeline integrates media queries directly into ovos-core as a first class NLP pipeline component,
in ovos-core <= **version 0.0.7** Classic OCP registered intents with the regular intent system,

```javascript
{
  "intents" : {
    "OCP": {
      // legacy forces old audio service instead of OCP
      "legacy": false,
      // min confidence (0.0 - 1.0) to accept MediaType
      "classifier_threshold": 0.4,
      // min conf for each result (0 - 100)
      "min_score": 50,
      // filter results from "wrong" MediaType
      "filter_media": true,
      // filter results we lack plugins to play
      "filter_SEI": true,
      // playback mode
      // 0 - auto
      // 10 - audio results only
      // 20 - video results only
      "playback_mode": 0,
      // if MediaType query fails, try Generic query
      "search_fallback": true
    }
}
```

### Pipeline components

> **TIP**: read the [pipeline](https://openvoiceos.github.io/ovos-technical-manual/core/#pipelines) documentation first!

provide 4 new pipeline components:

- ocp_high

- ocp_medium

- ocp_low

- ocp_legacy

#### ocp_legacy - Legacy CommonPlay

If you can't migrate your old skills to the OCP framework, you can add `ocp_legacy` to your pipeline to check for legacy
common play skill matches

> **NOTE**: This is independent of the OCPSkills handling and needs to be explicitly added to your pipeline config

```
{
  "intents" : {
    // the pipeline is a ordered set of frameworks to send an utterance too
    // if one of the frameworks fails the next one is used, until an answer is found
    // NOTE: dont copy paste this, edit to your taste! "..." is not valid here
    "pipeline": [
        "converse",
        "...",
        "ocp_legacy",
        "...",
        "fallback_low"
    ]
  }
}
```

When comparing legacy common play skills to OCPSkills, it's essential to understand the differences in how they handle
playback within the OCP pipeline.

**Legacy CommonPlay Skills**:

- Originate from Mycroft.
- Used to depend on a companion skill under `mycroft-core`
- Supported via OCP Pipeline in `ovos-core` **version 0.0.8**.
- Deprecated since `ovos-core` **version 0.0.8**.
- Scheduled for full removal on `ovos-core **version 0.1.0**.
- Handle their own playback.
- Typically reliant on the legacy audio system.
- Requires manually enabling support in `mycroft.conf`

**OCPSkills**:

- Designed to work with the OCP Pipeline.
- Return results only, leaving playback management to the OCP system.
- Provide a more modular and maintainable system for media playback.
- Offer enhanced capabilities such as GUI, playlists, and video support.
- Represent the future direction of media playback within ovos-core.
- Natively supported by `ovos-core`

#### ocp_high - Unambiguous

Before regular intent stage, taking into account current OCP state  (media ready to play / playing)

```
{
  "intents" : {
    // NOTE: dont copy paste this, edit to your taste! "..." is not valid here
    "pipeline": [
        "converse",
        "ocp_high",
        "...",
        "fallback_low"
    ]
  }
}
```

Only matches if user unambiguously wants to trigger OCP

uses padacioso for exact matches

- play {query}
- previous  (media needs to be loaded)
- next  (media needs to be loaded)
- pause  (media needs to be loaded)
- play / resume (media needs to be loaded)
- stop (media needs to be loaded)

```python
ocp = OCPPipelineMatcher()
print(ocp.match_high("play metallica", "en-us"))
# IntentMatch(intent_service='OCP_intents',
#   intent_type='ocp:play',
#   intent_data={'media_type': <MediaType.MUSIC: 2>, 'query': 'metallica',
#                'entities': {'album_name': 'Metallica', 'artist_name': 'Metallica'},
#                'conf': 0.96, 'lang': 'en-us'},
#   skill_id='ovos.common_play', utterance='play metallica')

```

#### ocp_medium - Semi-Ambiguous

```
{
  "intents" : {
    // NOTE: dont copy paste this, edit to your taste! "..." is not valid here
    "pipeline": [
        "converse",
        "...",
        "ocp_medium",
        "...",
        "fallback_low"
    ]
  }
}
```

uses a binary classifier to detect if a query is about media playback

```python
ocp = OCPPipelineMatcher()

print(ocp.match_high("put on some metallica", "en-us"))
# None

print(ocp.match_medium("put on some metallica", "en-us"))
# IntentMatch(intent_service='OCP_media',
#   intent_type='ocp:play',
#   intent_data={'media_type': <MediaType.MUSIC: 2>,
#                'entities': {'album_name': 'Metallica', 'artist_name': 'Metallica', 'movie_name': 'Some'},
#                'query': 'put on some metallica',
#                'conf': 0.9578441098114333},
#   skill_id='ovos.common_play', utterance='put on some metallica')
```

#### ocp_low - Ambiguous

Uses keyword matching and requires at least 1 keyword

```
{
  "intents" : {
    // NOTE: dont copy paste this, edit to your taste! "..." is not valid here
    "pipeline": [
        "converse",
        "...",
        "ocp_low",
        "fallback_low"
    ]
  }
}
```

OCP skills can provide these keywords at runtime, additional keywords for things such as media_genre were collected via
SPARQL queries to wikidata

```python
ocp = OCPPipelineMatcher()

print(ocp.match_medium("i wanna hear metallica", "en-us"))
# None

print(ocp.match_fallback("i wanna hear metallica", "en-us"))
#  IntentMatch(intent_service='OCP_fallback',
#    intent_type='ocp:play',
#    intent_data={'media_type': <MediaType.MUSIC: 2>,
#                 'entities': {'album_name': 'Metallica', 'artist_name': 'Metallica'},
#                 'query': 'i wanna hear metallica',
#                 'conf': 0.5027561091821287},
#    skill_id='ovos.common_play', utterance='i wanna hear metallica')

```

### Playback Frameworks

After the OCP pipeline selects a skill, it proceeds as follows:

- For OCP Skills: The pipeline collects results from all skills, selects the best one, and handles playback accordingly.

- For CommonPlay Legacy Skills: The pipeline selects the best skill and instructs it to handle playback.

Here's a simple table comparing the three playback handling options for OCPSkills:

| Feature                 | Legacy Audio System | Classic OCP           | ovos-media       |
|-------------------------|---------------------|-----------------------|------------------|
| Music Playback          | Basic support       | Yes                   | Work in Progress |
| Video Playback          | No                  | Yes                   | Work in Progress |
| Web Playback            | No                  | Yes                   | Work in Progress |
| Legacy Audio Plugins    | Yes                 | Yes                   | No               |
| Media Plugins           | No                  | No                    | Yes              |
| GUI                     | No                  | Yes                   | Yes              |
| Shuffle/Repeat          | No                  | Yes                   | Yes              |
| Multiple Results        | No                  | Yes                   | Yes              |
| Featured Media          | No                  | Yes                   | Yes              |
| Playlists               | Very Limited        | Yes                   | Yes              |
| Search Results Playlist | No                  | Yes                   | Yes              |
| Now Playing Playlist    | No                  | Yes                   | Yes              |
| Deprecation Status      | Deprecated          | Scheduled for removal | N/A              |
| Development Status      | Bug fixes only      | Bug fixes only        | Work in Progress |

#### Legacy Audio Service

Integrating with
the [legacy audio service](https://openvoiceos.github.io/ovos-technical-manual/audio_service/#legacy-audio-service)
enables basic playback functionality. While limited it should work in more platform

Here's how to configure it **without OCP**:

```
{
    "enable_old_audioservice": true,
    "disable_ocp": true,
    "Audio": {
        "default-backend": "vlc",
        "backends": {
          "simple": {
            "type": "ovos_audio_simple",
            "active": true
          },
          "vlc": {
            "type": "ovos_vlc",
            "active": true
          }
        }
    }
  },
}
```

> **NOTE**: `"default-backend"` **must** be set when `"disable_ocp"` is set to true

#### Classic OCP

Employing Classic OCP expands on the legacy audio service with additional functionality. It is tightly integrated with
the legacy audio service

OCP was developed for mycroft-core under the legacy audio system and will pose as a legacy plugin, translating the
received bus events to the OCP API.

> **TIP**: OCP is **always** the default audio plugin unless `"disable_ocp"` is set to true in the
> configuration. `"default-backend"` has no effect here

OCP internally uses the legacy API to delegate playback when GUI is not available (or if configured to do so).

Here's how to configure it:

```
{
    "enable_old_audioservice": true,
    "disable_ocp": false,

    "Audio": {
        "backends": {
          "OCP": {
            "type": "ovos_common_play",
            "disable_mpris": true,
            "manage_external_players": false,
            "active": true
          },
          "simple": {
            "type": "ovos_audio_simple",
            "active": true
          },
          "vlc": {
            "type": "ovos_vlc",
            "active": true
          }
        }
    }
  },
}

```

#### ovos-media

Utilizing [ovos-media](https://openvoiceos.github.io/ovos-technical-manual/OCP/) introduces a more modern approach to
playback management. Here's how to configure it:

```
{
    "enable_old_audioservice": false,
    "disable_ocp": true
}
```

You also need to manually launch the `ovos-media` service, it is not yet integrated into the installer, docker or
pre-built images

> **WARNING** This feature is a work in progress and not ready for end users

### Classifiers

#### Architecture

Efficient entity matching is done
via [Aho–Corasick algorithm](https://en.wikipedia.org/wiki/Aho%E2%80%93Corasick_algorithm), keyword features are
essentially a keyword count.

The way the OCP dataset was collected ensures these keyword features were present during training and interpretable,
therefore during runtime any number of entities can be loaded, OVOS skills can also register their own keywords.

This approach together with classical text features is used to train classifiers used in the OCP Pipeline

![imagem](https://github.com/NeonJarbas/ocp-nlp/assets/59943014/2144c6a7-d32d-4b3f-89c3-0151f6257f60)

#### Media Type Classifier

internally used to tag utterances before OCP search process, this informs the result selection by giving priority to
certain skills and helps performance by skipping some skills completely during search

uses a scikit-learn classifier trained in a large synthetic dataset

![imagem](https://github.com/NeonJarbas/ocp-nlp/assets/59943014/4cdb440d-4673-4972-8691-4c3c489ff4e8)

```python
class MediaType:
    GENERIC = 0  # nothing else matches
    AUDIO = 1  # things like ambient noises
    MUSIC = 2
    VIDEO = 3  # eg, youtube videos
    AUDIOBOOK = 4
    GAME = 5  # because it shares the verb "play", mostly for disambguation
    PODCAST = 6
    RADIO = 7  # live radio
    NEWS = 8  # news reports
    TV = 9  # live tv stream
    MOVIE = 10
    TRAILER = 11
    AUDIO_DESCRIPTION = 12  # narrated movie for the blind
    VISUAL_STORY = 13  # things like animated comic books
    BEHIND_THE_SCENES = 14
    DOCUMENTARY = 15
    RADIO_THEATRE = 16
    SHORT_FILM = 17  # typically movies under 45 min
    SILENT_MOVIE = 18
    VIDEO_EPISODES = 19  # tv series etc
    BLACK_WHITE_MOVIE = 20
    CARTOON = 21
    ANIME = 22

    ADULT = 69  # for content filtering
    HENTAI = 70  # for content filtering
    ADULT_AUDIO = 71  # for content filtering
```

The features of this classifier have been engineered to allow influencing classifications at runtime based on available
skills

Classifier options:

- trained on text only features (count vectorizer baseline - english) ~= 85% accuracy

- trained on keyword features (lang agnostic - runtime keywords influence classification) ~= 88% accuracy

- trained on probabilities of text only classifier + keyword features (english only - runtime keywords influence
  classification) ~= 95% accuracy

NOTE: several classification algorithms have been tested, Perceptron and MLP are the most sensitive to the runtime bias
properly

#### Binary classifier

using the dataset collected for media type + ovos-datasets

![imagem](https://github.com/NeonJarbas/ocp-nlp/assets/59943014/bf9b796e-dc57-4320-a472-5b859b9dfcaf)

Classifier options:

- trained on text only features (count vectorizer baseline - english) ~= 95% accuracy

- trained on keyword features (lang agnostic - runtime keywords influence classification) ~= 90% accuracy

#### Standalone Usage

check if an utterance is playback related

```python
clf = BinaryPlaybackClassifier()
clf.load()
preds = clf.predict(["play a song", "play my morning jams",
                     "i want to watch the matrix",
                     "tell me a joke", "who are you", "you suck"])
print(preds)  # ['OCP' 'OCP' 'OCP' 'other' 'other' 'other']
```

get media type of a playback utterance

```python
# basic text only classifier
clf1 = MediaTypeClassifier()
clf1.load()

label, confidence = clf1.predict_prob(["play metallica"])[0]
print(label, confidence)  # [('music', 0.3438956411030462)]

# keyword biased classifier, uses the above internally for extra features
clf = BiasedMediaTypeClassifier(lang="en", preload=True)  # load entities database
clf.load()

# klownevilus is an unknown entity
label, confidence = clf.predict_prob(["play klownevilus"])[0]
print(label, confidence)  # music 0.3398020446925623

# probability increases for movie
clf.register_entity("movie_name", ["klownevilus"])  # movie correctly predicted now
label, confidence = clf.predict_prob(["play klownevilus"])[0]
print(label, confidence)  # movie 0.540225616798516
```
