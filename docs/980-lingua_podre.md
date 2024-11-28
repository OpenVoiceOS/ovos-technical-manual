# lingua podre

**lingua podre** is a simple Python language detection library that utilizes word lists for language identification. It provides a straightforward way to predict the language of a given text based on the frequency of language-specific stopwords.

## How It Works

**lingua podre** utilizes pre-defined word lists (stopwords) associated with each supported language. By analyzing the frequency of these language-specific stopwords in a given text, the library predicts the most likely language(s) of the text.

**Note**: The accuracy of language detection can vary based on the quality and coverage of the word lists. For longer texts or specialized content, consider additional text processing and language modeling techniques for improved accuracy.

## Install

You can install **lingua podre** using pip:

```bash
pip install lingua_podre
```

## Usage

```python
from lingua_podre import predict_lang, get_lang_scores

# Example text in English and Portuguese
utterance = "hello my name is Bob"
utterance_pt = "olá o meu nome é João"

# Predict the language of the English text
print(predict_lang(utterance))
# Output: ['en']

# Get language scores for the English text
print(get_lang_scores(utterance))
# Output: {'en': 0.2727272727272727, 'pl': 0.09090909090909091, 'cs': 0.09090909090909091, 'sk': 0.09090909090909091, 'hu': 0.09090909090909091, 'sv': 0.09090909090909091, 'nb': 0.09090909090909091, 'da': 0.09090909090909091, 'nl': 0.09090909090909091}

# Predict the language of the Portuguese text
print(predict_lang(utterance_pt))
# Output: ['pt']

# Get language scores for the Portuguese text
print(get_lang_scores(utterance_pt))
# Output: {'pt': 0.2857142857142857, 'ca': 0.07142857142857142, 'pl': 0.07142857142857142, 'cs': 0.07142857142857142, 'ro': 0.14285714285714285, 'it': 0.14285714285714285, 'tr': 0.07142857142857142, 'sk': 0.07142857142857142, 'es': 0.07142857142857142}
```

## OVOS Plugin Integration

**lingua podre** includes an OVOS plugin that integrates with the Open Voice Operating System (OVOS) framework. 
This integration allows you to use **lingua podre** for language detection, to use it configure `ovos-lang-detector-plugin-lingua-podre` in your `mycroft.conf`.


## Available Languages

The **lingua podre** library supports the following languages for language detection:

- Arabic
- Bulgarian
- Catalan
- Czech
- Danish
- Dutch
- English
- Finnish
- French
- German
- Gujarati
- Hindi
- Hebrew
- Hungarian
- Indonesian
- Malaysian
- Italian
- Norwegian
- Polish
- Portuguese
- Romanian
- Russian
- Slovak
- Spanish
- Swedish
- Turkish
- Ukrainian
- Vietnamese

