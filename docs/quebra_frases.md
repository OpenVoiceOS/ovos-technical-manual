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

Here is an example demonstrating the usage of `quebra_frases` functionalities:

```python
import quebra_frases

# Tokenization
sentence = "This is an example sentence."
words = quebra_frases.word_tokenize(sentence)
print(words)
# Output: ['This', 'is', 'an', 'example', 'sentence.']

sentences = quebra_frases.sentence_tokenize("This is the first sentence. This is the second sentence.")
print(sentences)
# Output: ['This is the first sentence.', 'This is the second sentence.']

# Chunking
delimiters = ["."]
chunks = quebra_frases.chunk(sentence, delimiters)
print(chunks)
# Output: ['This is an example sentence']

samples = ["The quick brown fox", "The lazy dog"]
common_chunks = quebra_frases.get_common_chunks(samples)
print(common_chunks)
# Output: {'The'}

# Token Analysis
sample_texts = ["apple banana orange", "banana mango kiwi"]
common_tokens = quebra_frases.get_common_tokens(sample_texts)
print(common_tokens)
# Output: {'banana'}

uncommon_tokens = quebra_frases.get_uncommon_tokens(sample_texts)
print(uncommon_tokens)
# Output: {'apple', 'orange', 'mango', 'kiwi'}
```

