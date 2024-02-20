# OCP Skills

OCP skills are built from the OVOSCommonPlaybackSkill class

These skills work as media providers, they return results for OCP to playback

The actual voice interaction is handled by OCP, skills only implement the returning of results

## OVOSCommonPlaybackSkill

subclass your skill from `OVOSCommonPlaybackSkill`

use `@ocp_search()` decorator in as many handlers as you want to return search results

use `@ocp_featured_media()` decorator to populate the [OCP skills menu]()

```python
from os.path import join, dirname

import radiosoma

from ovos_utils import classproperty
from ovos_utils.ocp import MediaType, PlaybackType
from ovos_utils.parse import fuzzy_match
from ovos_workshop.decorators.ocp import ocp_search, ocp_featured_media
from ovos_workshop.skills.common_play import OVOSCommonPlaybackSkill


class SomaFMSkill(OVOSCommonPlaybackSkill):

    def __init__(self, *args, **kwargs):
        # media type this skill can handle
        self.supported_media = [MediaType.MUSIC, MediaType.RADIO]
        self.skill_icon = join(dirname(__file__), "ui", "somafm.png")
        super().__init__(*args, **kwargs)

    @ocp_featured_media()
    def featured_media(self):
        # playlist when selected from OCP skills menu
        return [{
            "match_confidence": 90,
            "media_type": MediaType.RADIO,
            "uri": ch.direct_stream,
            "playback": PlaybackType.AUDIO,
            "image": ch.image,
            "bg_image": ch.image,
            "skill_icon": self.skill_icon,
            "title": ch.title,
            "author": "SomaFM",
            "length": 0
        } for ch in radiosoma.get_stations()]

    @ocp_search()
    def search_somafm(self, phrase, media_type):
        # check if user asked for a known radio station
        base_score = 0

        if media_type == MediaType.RADIO:
            base_score += 20
        else:
            base_score -= 30

        if self.voc_match(phrase, "radio"):
            base_score += 10
            phrase = self.remove_voc(phrase, "radio")

        if self.voc_match(phrase, "somafm"):
            base_score += 30  # explicit request
            phrase = self.remove_voc(phrase, "somafm")

        for ch in radiosoma.get_stations():
            score = round(base_score + fuzzy_match(ch.title.lower(),
                                                   phrase.lower()) * 100)
            if score < 50:
                continue
            yield {
                "match_confidence": min(100, score),
                "media_type": MediaType.RADIO,
                "uri": ch.direct_stream,
                "playback": PlaybackType.AUDIO,
                "image": ch.image,
                "bg_image": ch.image,
                "skill_icon": self.skill_icon,
                "title": ch.title,
                "author": "SomaFM",
                "length": 0
            }
```


## OCP Keywords

```python
def register_ocp_keyword(self, label: str, samples: List, langs: List[str] = None):
    """ register strings as native OCP keywords (eg, movie_name, artist_name ...)
    ocp keywords can be efficiently matched with self.ocp_match helper method
    that uses Aho–Corasick algorithm
    """

def load_ocp_keyword_from_csv(self, csv_path: str, lang: str):
    """ load entities from a .csv file for usage with self.ocp_voc_match
    see the ocp_entities.csv datatsets for example files built from wikidata SPARQL queries

    examples contents of csv file

        label,entity
        film_genre,swashbuckler film
        film_genre,neo-noir
        film_genre,actual play film
        film_genre,alternate history film
        film_genre,spy film
        ...
    """
```

## OCP Voc match

uses [Aho–Corasick algorithm](https://en.wikipedia.org/wiki/Aho%E2%80%93Corasick_algorithm) to match OCP keywords

this efficiently matches many keywords against an utterance

OCP keywords are registered via `self.register_ocp_keyword`

wordlists can also be loaded from a .csv file, see [the OCP dataset](https://github.com/OpenVoiceOS/ovos-classifiers/tree/dev/scripts/training/ocp/datasets) for a list of keywords gathered from wikidata with SPARQL queries


example usage
```python
import json

from ovos_utils.messagebus import FakeBus
from ovos_utils.ocp import MediaType
from ovos_workshop.skills.common_play import OVOSCommonPlaybackSkill


class HorrorBabbleSkill(OVOSCommonPlaybackSkill):

    def initialize(self):
        # get file from
        # https://github.com/JarbasSkills/skill-horrorbabble/blob/dev/bootstrap.json
        with open("hb.json") as f:
            db = json.load(f)
        book_names = []
        book_authors = []
        for url, data in db.items():
            t = data["title"].split("/")[0].strip()
            if " by " in t:
                title, author = t.split(" by ")
                title = title.replace('"', "").strip()
                author = author.split("(")[0].strip()
                book_names.append(title)
                book_authors.append(author)
                if " " in author:
                    book_authors += author.split(" ")
            elif t.startswith('"') and t.endswith('"'):
                book_names.append(t[1:-1])
            else:
                book_names.append(t)
        self.register_ocp_keyword(MediaType.AUDIOBOOK,
                                  "book_author",
                                  list(set(book_authors)))
        self.register_ocp_keyword(MediaType.AUDIOBOOK,
                                  "book_name",
                                  list(set(book_names)))
        self.register_ocp_keyword(MediaType.AUDIOBOOK,
                                  "audiobook_streaming_provider",
                                  ["HorrorBabble", "Horror Babble"])
```

```python
s = HorrorBabbleSkill(bus=FakeBus(), skill_id="demo.fake")

entities = s.ocp_voc_match("read The Call of Cthulhu by Lovecraft")
# {'book_author': 'Lovecraft', 'book_name': 'The Call of Cthulhu'}
print(entities)

entities = s.ocp_voc_match("play HorrorBabble")
# {'audiobook_streaming_provider': 'HorrorBabble'}
print(entities)
```