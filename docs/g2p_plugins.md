# Grapheme to Phoneme Plugins

Grapheme to Phoneme is the process of converting text into a set of "sound units" called phonemes

In `ovos-audio` these plugins are used to auto generate mouth movements / visemes in the TTS stage.

They can also be used to help configuring wake words or to facilitate training of TTS systems

These plugins can provide phonemes either in ARPA or IPA alphabets, an automatic conversion will happen behind the scenes when needed

## Visemes

Visemes are representations of the shape of a human mouth when speaking.

![visemes](http://www.web3.lu/wp-content/uploads/2014/09/visemes.jpg)

Mouth movements are generated via a mapping of ARPA to VISEMES, TTS plugins may provide this natively, or a G2P plugin may be used to estimate it directly from text

Visemes are predefined mouth positions, timing per phonemes is crucial for a natural mouth movement

The Mycroft Mark 1 uses this to make his "lips" match his speech. 

OpenVoiceOS uses six basic visemes.

| viseme | mouth position   |
|--------|------------------|
| 0      | wide open        |
| 1      | pursed           |
| 2      | open             |
| 3      | narrow lips      |
| 4      | closed lips      |
| 5      | parted lips      |
| 6      | barely open lips |

Mapping based on [Jeffers phoneme to viseme map, seen in table 1](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.221.6377&rep=rep1&type=pdf), partially based on the "12 mouth shapes visuals seen [here](https://wolfpaulus.com/journal/software/lipsynchronization/)

## List of G2P plugins

| Plugin                                                                                        | Type |
|-----------------------------------------------------------------------------------------------|------|
| [neon-g2p-cmudict-plugin](https://github.com/NeonGeckoCom/g2p-cmudict-plugin)                 | ARPA |
| [neon-g2p-phoneme-guesser-plugin](https://github.com/NeonGeckoCom/g2p-phoneme-guesser-plugin) | ARPA |
| [neon-g2p-mimic-plugin](https://github.com/NeonJarbas/g2p-mimic-plugin)                       | ARPA |
| [neon-g2p-mimic2-plugin](https://github.com/NeonJarbas/g2p-mimic2-plugin)                     | ARPA |
| [neon-g2p-espeak-plugin](https://github.com/NeonJarbas/g2p-espeak-plugin)                     | IPA  |
| [neon-g2p-gruut-plugin](https://github.com/NeonGeckoCom/g2p-gruut-plugin)                     | IPA  |

## Standalone Usage

All G2P plugins can be used as follows

```python

utterance = "hello world"
word = "hello"
lang="en-us"

plug = G2pPlugin()

# convert a word into a list of phonemes
phones = plug.get_ipa(word, lang)
assert phones == ['h', 'ʌ', 'l', 'oʊ']

phones = plug.get_arpa(word, lang)
assert phones == ['HH', 'AH', 'L', 'OW']

# convert a utterance into a list of phonemes
phones = plug.utterance2arpa(utterance, lang)
assert phones == ['HH', 'AH', 'L', 'OW', '.', 'W', 'ER', 'L', 'D']

phones = plug.utterance2ipa(utterance, lang)
assert phones == ['h', 'ʌ', 'l', 'oʊ', '.', 'w', 'ɝ', 'l', 'd']

# convert a utterance into a list of viseme, duration pairs
visemes = plug.utterance2visemes(utterance, lang)
assert visemes == [('0', 0.0775), ('0', 0.155), ('3', 0.2325), ('2', 0.31), ('2', 0.434), ('2', 0.558), ('3', 0.682), ('3', 0.806)]
```

## Plugin Template

```python
from ovos_plugin_manager.templates.g2p import Grapheme2PhonemePlugin
from ovos_utils.lang.visimes import VISIMES

# base plugin class
class MyARPAG2PPlugin(Grapheme2PhonemePlugin):
    def __init__(self, config=None):
        self.config = config or {}

    def get_arpa(self, word, lang, ignore_oov=False):
        phones = []  # TODO implement
        return phones
    
    def get_durations(self, utterance, lang="en", default_dur=0.4):
        words = utterance.split()
        phones = [self.get_arpa(w, lang) for w in utterance.split()]
        dur = default_dur  # TODO this is plugin specific
        return [(pho, dur) for pho in phones]
    
    def utterance2visemes(self, utterance, lang="en", default_dur=0.4):
        phonemes = self.get_durations(utterance, lang, default_dur)
        return [(VISIMES.get(pho[0].lower(), '4'), float(pho[1]))
                for pho in phonemes]

```

If your plugin uses IPA instead of ARPA simply replace `get_arpa` with `get_ipa`

```python
from ovos_plugin_manager.templates.g2p import Grapheme2PhonemePlugin
from ovos_utils.lang.visimes import VISIMES

# base plugin class
class MyIPAG2PPlugin(Grapheme2PhonemePlugin):
    def __init__(self, config=None):
        self.config = config or {}

    def get_ipa(self, word, lang, ignore_oov=False):
        phones = []  # TODO implement
        return phones
    
    def get_durations(self, utterance, lang="en", default_dur=0.4):
        # auto converted to arpa if ipa is implemented
        phones = [self.get_arpa(w, lang) for w in utterance.split()]
        dur = default_dur  # TODO this is plugin specific
        return [(pho, dur) for pho in phones]
    
    def utterance2visemes(self, utterance, lang="en", default_dur=0.4):
        phonemes = self.get_durations(utterance, lang, default_dur)
        return [(VISIMES.get(pho[0].lower(), '4'), float(pho[1]))
                for pho in phonemes]

```

