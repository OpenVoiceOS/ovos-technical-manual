# OCP Skills

OCP skills are built from the OVOSCommonPlaybackSkill class

These skills work as media providers, they return results for OCP to playback

The actual voice interaction is handled by OCP, skills only implement the returning of results


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

uses Aho–Corasick algorithm to match OCP keywords

this efficiently matches many keywords against an utterance

OCP keywords are registered via self.register_ocp_keyword

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