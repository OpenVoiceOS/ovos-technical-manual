# Quebra Frases

The `quebra_frases` package provides essential text processing tools for tokenization, chunking, and token analysis. 

**No External Dependencies**: quebra_frases is designed to be lightweight and does not rely on external libraries other than regex for efficient text processing.

## Installation

You can install the `quebra_frases` package using pip:

```bash
pip install quebra_frases
```

## Overview

The `quebra_frases` package includes several modules and functionalities:

- **Tokenization**: Text tokenization is the process of splitting text into meaningful units such as words, sentences, or paragraphs.
- **Chunking**: Text chunking involves dividing text into smaller chunks based on specified delimiters or patterns.
- **Token Analysis**: This package also provides methods to analyze tokens across multiple text samples, extracting common, uncommon, and exclusive tokens.

## Usage

### Tokenization

The `quebra_frases` package offers various tokenization methods:

- `word_tokenize(input_string)`: Tokenizes an input string into words.
- `sentence_tokenize(input_string)`: Splits an input string into sentences.
- `paragraph_tokenize(input_string)`: Divides an input string into paragraphs.

### Chunking

Chunking is performed using the following functions:

- `chunk(text, delimiters)`: Splits text into chunks based on specified delimiters.
- `get_common_chunks(samples)`: Extracts common chunks from a list of text samples.
- `get_uncommon_chunks(samples)`: Extracts uncommon chunks from text samples.
- `get_exclusive_chunks(samples)`: Extracts exclusive chunks that are unique to each text sample.

### Token Analysis

Token analysis functions are available for text sample comparison:

- `get_common_tokens(samples)`: Extracts tokens that are common across multiple text samples.
- `get_uncommon_tokens(samples)`: Extracts tokens that are uncommon across multiple text samples.
- `get_exclusive_tokens(samples)`: Extracts tokens that are exclusive to each individual text sample.

## Example Usage

Tokenization

```python
import quebra_frases

sentence = "sometimes i develop stuff for mycroft, mycroft is FOSS!"
print(quebra_frases.word_tokenize(sentence))
# ['sometimes', 'i', 'develop', 'stuff', 'for', 'mycroft', ',', 
# 'mycroft', 'is', 'FOSS', '!']

print(quebra_frases.span_indexed_word_tokenize(sentence))
# [(0, 9, 'sometimes'), (10, 11, 'i'), (12, 19, 'develop'), 
# (20, 25, 'stuff'), (26, 29, 'for'), (30, 37, 'mycroft'), 
# (37, 38, ','), (39, 46, 'mycroft'), (47, 49, 'is'), 
# (50, 54, 'FOSS'), (54, 55, '!')]

print(quebra_frases.sentence_tokenize(
    "Mr. Smith bought cheapsite.com for 1.5 million dollars, i.e. he paid a lot for it. Did he mind? Adam Jones Jr. thinks he didn't. In any case, this isn't true... Well, with a probability of .9 it isn't."))
#['Mr. Smith bought cheapsite.com for 1.5 million dollars, i.e. he paid a lot for it.',
#'Did he mind?',
#"Adam Jones Jr. thinks he didn't.",
#"In any case, this isn't true...",
#"Well, with a probability of .9 it isn't."]

print(quebra_frases.span_indexed_sentence_tokenize(
    "Mr. Smith bought cheapsite.com for 1.5 million dollars, i.e. he paid a lot for it. Did he mind? Adam Jones Jr. thinks he didn't. In any case, this isn't true... Well, with a probability of .9 it isn't."))
#[(0, 82, 'Mr. Smith bought cheapsite.com for 1.5 million dollars, i.e. he paid a lot for it.'),
#(83, 95, 'Did he mind?'),
#(96, 128, "Adam Jones Jr. thinks he didn't."),
#(129, 160, "In any case, this isn't true..."),
#(161, 201, "Well, with a probability of .9 it isn't.")]

print(quebra_frases.paragraph_tokenize('This is a paragraph!\n\t\nThis is another '
                                       'one.\t\n\tUsing multiple lines\t   \n     '
                                       '\n\tparagraph 3 says goodbye'))
#['This is a paragraph!\n\t\n',
#'This is another one.\t\n\tUsing multiple lines\t   \n     \n',
#'\tparagraph 3 says goodbye']

print(quebra_frases.span_indexed_paragraph_tokenize('This is a paragraph!\n\t\nThis is another '
                                                    'one.\t\n\tUsing multiple lines\t   \n     '
                                                    '\n\tparagraph 3 says goodbye'))
#[(0, 23, 'This is a paragraph!\n\t\n'),
#(23, 77, 'This is another one.\t\n\tUsing multiple lines\t   \n     \n'),
#(77, 102, '\tparagraph 3 says goodbye')]
```

chunking

```python
import quebra_frases

delimiters = ["OpenVoiceOS"]
sentence = "sometimes i develop stuff for OpenVoiceOS, OpenVoiceOS is FOSS!"
print(quebra_frases.chunk(sentence, delimiters))
# ['sometimes i develop stuff for', 'OpenVoiceOS', ',', 'OpenVoiceOS', 'is FOSS!']
```

token analysis

```python
import quebra_frases

samples = ["tell me what do you dream about",
           "tell me what did you dream about",
           "tell me what are your dreams about",
           "tell me what were your dreams about"]
print(quebra_frases.get_common_chunks(samples))
# {'tell me what', 'about'}
print(quebra_frases.get_uncommon_chunks(samples))
# {'do you dream', 'did you dream', 'are your dreams', 'were your dreams'}
print(quebra_frases.get_exclusive_chunks(samples))
# {'do', 'did', 'are', 'were'}

samples = ["what is the speed of light",
           "what is the maximum speed of a firetruck",
           "why are fire trucks red"]
print(quebra_frases.get_exclusive_chunks(samples))
# {'light', 'maximum', 'a firetruck', 'why are fire trucks red'})
print(quebra_frases.get_exclusive_chunks(samples, squash=False))
#[['light'],
#['maximum', 'a firetruck'],
#['why are fire trucks red']])
```
