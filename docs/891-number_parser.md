# OVOS Number Parser

OVOS Number Parser is a tool for extracting, pronouncing, and detecting numbers from text across multiple languages. It
supports functionalities like converting numbers to their spoken forms, extracting numbers from text, identifying
fractional and ordinal numbers, and more.

## Features

- **Pronounce Numbers:** Converts numerical values to their spoken forms.
- **Pronounce Ordinals:** Converts numbers to their ordinal forms.
- **Extract Numbers:** Extracts numbers from textual inputs.
- **Detect Fractions:** Identifies fractional expressions.
- **Detect Ordinals:** Checks if a text input contains an ordinal number.

## Supported Languages

- ✅ - supported
- ❌ - not supported
- 🚧 - imperfect placeholder, usually a language agnostic implementation or external library


| Language Code           | Pronounce Number | Pronounce Ordinal | Extract Number | numbers_to_digits |
|-------------------------|------------------|-------------------|----------------|-------------------|
| `en` (English)          | ✅               | 🚧                | ✅             | ✅                |
| `az` (Azerbaijani)      | ✅               | 🚧                | ✅             | ✅                |
| `ca` (Catalan)          | ✅                | 🚧                 | ✅              | 🚧                 |
| `gl` (Galician)         | ✅                | ❌                | ✅              |  🚧                  |
| `cs` (Czech)            | ✅                | 🚧                 | ✅              | ✅                 |
| `da` (Danish)           | ✅                | ✅                 | ✅              | 🚧                 |
| `de` (German)           | ✅                | ✅                 | ✅              | ✅                 |
| `es` (Spanish)          | ✅                | 🚧                 | ✅              | 🚧                 |
| `eu` (Euskara / Basque) | ✅                | ❌                 | ✅              | ❌                 |
| `fa` (Farsi / Persian)  | ✅                | 🚧                 | ✅              | ❌                 |
| `fr` (French)           | ✅                | 🚧                 | ✅              | ❌                 |
| `hu` (Hungarian)        | ✅                | ✅                 | ❌              | ❌                 |
| `it` (Italian)          | ✅                | 🚧                | ✅              | ❌                 |
| `nl` (Dutch)            | ✅                | ✅                 | ✅              | ✅                 |
| `pl` (Polish)           | ✅                | 🚧                 | ✅              | ✅                 |
| `pt` (Portuguese)       | ✅                | 🚧                 | ✅              | 🚧                 |
| `ru` (Russian)          | ✅                | 🚧                 | ✅              | ✅                 |
| `sv` (Swedish)          | ✅                | ✅                 | ✅              | ❌                 |
| `sl` (Slovenian)        | ✅                | 🚧                 | ❌              | ❌                 |
| `uk` (Ukrainian)        | ✅                | 🚧                 | ✅              | ✅                 |


> 💡 If a language is not implemented for `pronounce_number` or `pronounce_ordinal` then [unicode-rbnf](https://github.com/rhasspy/unicode-rbnf) will be used as a fallback

## Installation

To install OVOS Number Parser, use:

```bash
pip install ovos-number-parser
```

## Usage

### Pronounce a Number

Convert a number to its spoken equivalent.

```python
def pronounce_number(number: Union[int, float], lang: str, places: int = 2, short_scale: bool = True,
                     scientific: bool = False, ordinals: bool = False) -> str:
    """
    Convert a number to its spoken equivalent.

    Args:
        number: The number to pronounce.
        lang (str): A BCP-47 language code.
        places (int): Number of decimal places to express. Default is 2.
        short_scale (bool): Use short (True) or long scale (False) for large numbers.
        scientific (bool): Pronounce in scientific notation if True.
        ordinals (bool): Pronounce as an ordinal if True.

    Returns:
        str: The pronounced number.
    """
```

**Example Usage:**

```python
from ovos_number_parser import pronounce_number

# Example
result = pronounce_number(123, "en")
print(result)  # "one hundred and twenty-three"
```

### Pronounce an Ordinal

Convert a number to its ordinal spoken equivalent.

```python
def pronounce_ordinal(number: Union[int, float], lang: str, short_scale: bool = True) -> str:
    """
    Convert an ordinal number to its spoken equivalent.

    Args:
        number: The number to pronounce.
        lang (str): A BCP-47 language code.
        short_scale (bool): Use short (True) or long scale (False) for large numbers.

    Returns:
        str: The pronounced ordinal number.
    """
```

**Example Usage:**

```python
from ovos_number_parser import pronounce_ordinal

# Example
result = pronounce_ordinal(5, "en")
print(result)  # "fifth"
```

### Extract a Number

Extract a number from a given text string.

```python
def extract_number(text: str, lang: str, short_scale: bool = True, ordinals: bool = False) -> Union[int, float, bool]:
    """
    Extract a number from text.

    Args:
        text (str): The string to extract a number from.
        lang (str): A BCP-47 language code.
        short_scale (bool): Use short scale if True, long scale if False.
        ordinals (bool): Consider ordinal numbers.

    Returns:
        int, float, or False: The extracted number, or False if no number found.
    """
```

**Example Usage:**

```python
from ovos_number_parser import extract_number

# Example
result = extract_number("I have twenty apples", "en")
print(result)  # 20
```

### Check for Fractional Numbers

Identify if the text contains a fractional number.

```python
def is_fractional(input_str: str, lang: str, short_scale: bool = True) -> Union[bool, float]:
    """
    Check if the text is a fraction.

    Args:
        input_str (str): The string to check if fractional.
        lang (str): A BCP-47 language code.
        short_scale (bool): Use short scale if True, long scale if False.

    Returns:
        bool or float: False if not a fraction, otherwise the fraction as a float.
    """
```

**Example Usage:**

```python
from ovos_number_parser import is_fractional

# Example
result = is_fractional("half", "en")
print(result)  # 0.5
```

### Check for Ordinals

Determine if the text contains an ordinal number.

```python
def is_ordinal(input_str: str, lang: str) -> Union[bool, float]:
    """
    Check if the text is an ordinal number.

    Args:
        input_str (str): The string to check if ordinal.
        lang (str): A BCP-47 language code.

    Returns:
        bool or float: False if not an ordinal, otherwise the ordinal as a float.
    """
```

**Example Usage:**

```python
from ovos_number_parser import is_ordinal

# Example
result = is_ordinal("third", "en")
print(result)  # 3
```

## Related Projects

- [ovos-date-parser](https://github.com/OpenVoiceOS/ovos-date-parser) - for handling dates and times
- [ovos-lang-parser](https://github.com/OVOSHatchery/ovos-lang-parser) - for handling languages
- [ovos-color-parser](https://github.com/OVOSHatchery/ovos-color-parser) - for handling colors

## License

This project is licensed under the Apache License 2.0.
