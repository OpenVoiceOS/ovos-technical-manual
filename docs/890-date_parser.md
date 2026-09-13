# ovos-date-parser

`ovos-date-parser` is a comprehensive library for multilingual date and time parsing, extraction, and formatting,
designed to handle a range of human-readable date, time, and duration expressions.

## Features

- **Date and Time Extraction**: Extract specific dates and times from natural language phrases in various languages.
- **Duration Parsing**: Parse phrases that indicate a span of time, such as "two hours and fifteen minutes."
- **Friendly Time Formatting**: Format time for human-friendly output, supporting both 12-hour and 24-hour formats.
- **Relative Time Descriptions**: Generate relative descriptions (e.g., "tomorrow," "in three days") for given dates.
- **Multilingual Support**: Includes extraction and formatting methods for multiple languages, such as English, Spanish,
  French, German, and more.

## Installation

```bash
pip install ovos-date-parser
```

### Languages Supported

`ovos-date-parser` supports a wide array of languages, each with its own set of methods for handling natural language
time expressions.

- ✅ - supported
- ❌ - not supported
- 🚧 - imperfect placeholder, usually a language agnostic implementation or external library

**Parse**

| Language | `extract_duration` | `extract_datetime` |
|----------|--------------------|--------------------|
| az       | ✅                  | ✅                  |
| ca       | ✅                  | ✅                  |
| cs       | ✅                  | ✅                  |
| da       | ✅                  | ✅                  |
| de       | ✅                  | ✅                  |
| en       | ✅                  | ✅                  |
| es       | ✅                  | ✅                  |
| gl       | ✅                  | 🚧                 |
| eu       | ❌                  | ✅                  |
| fa       | ✅                  | ✅                  |
| fr       | ❌                  | ✅                  |
| hu       | ❌                  | 🚧                 |
| it       | ❌                  | ✅                  |
| nl       | ✅                  | ✅                  |
| pl       | ✅                  | ✅                  |
| pt       | ✅                  | ✅                  |
| ru       | ✅                  | ✅                  |
| sv       | ✅                  | ✅                  |
| uk       | ✅                  | ✅                  |


> 💡 If a language is not implemented for `extract_datetime` then [dateparser](https://dateparser.readthedocs.io/en/latest/) will be used as a fallback

**Format**

| Language | `nice_date`<br>`nice_date_time`<br>`nice_day` <br>`nice_weekday` <br>`nice_month` <br>`nice_year` <br>`get_date_strings` | `nice_time` | `nice_relative_time` | `nice_duration` |
|----------|--------------------------------------------------------------------------------------------------------------------------|-------------|----------------------|-----------------|
| az       | ✅                                                                                                                        | ✅           | 🚧                   | ✅               | 
| ca       | ✅                                                                                                                        | ✅           | 🚧                   | ✅               | 
| cs       | ✅                                                                                                                        | ✅           | 🚧                   | ✅               | 
| da       | ✅                                                                                                                        | ✅           | 🚧                   | ✅               | 
| de       | ✅                                                                                                                        | ✅           | 🚧                   | ✅               | 
| en       | ✅                                                                                                                        | ✅           | 🚧                   | ✅               | 
| es       | ✅                                                                                                                        | ✅           | 🚧                   | ✅               | 
| gl       | ✅                                                                                                                        | ✅           | 🚧                   | ✅               | 
| eu       | ✅                                                                                                                        | ✅           | ✅                    | ✅               | 
| fa       | ✅                                                                                                                        | ✅           | 🚧                   | ✅               | 
| fr       | ✅                                                                                                                        | ✅           | 🚧                   | ✅               | 
| hu       | ✅                                                                                                                        | ✅           | 🚧                   | ✅               | 
| it       | ✅                                                                                                                        | ✅           | 🚧                   | ✅               | 
| nl       | ✅                                                                                                                        | ✅           | 🚧                   | ✅               | 
| pl       | ✅                                                                                                                        | ✅           | 🚧                   | ✅               | 
| pt       | ✅                                                                                                                        | ✅           | 🚧                   | ✅               | 
| ru       | ✅                                                                                                                        | ✅           | 🚧                   | ✅               | 
| sv       | ✅                                                                                                                        | ✅           | 🚧                   | ✅               |
| sl       | ✅                                                                                                                        | ❌           | 🚧                   | ✅               |
| uk       | ✅                                                                                                                        | ✅           | 🚧                   | ✅               | 

## Usage

### Date and Time Extraction

Extract specific dates and times from a phrase. This function identifies date-related terms in natural language and
returns both the datetime object and any remaining text.

```python
from ovos_date_parser import extract_datetime

result = extract_datetime("Meet me next Friday at 3pm", lang="en")
print(result)  # (datetime object, "at 3pm")
```

### Duration Extraction

Identify duration phrases in text and convert them into a `timedelta` object. This can parse common human-friendly
duration expressions like "30 minutes" or "two and a half hours."

```python
from ovos_date_parser import extract_duration

duration, remainder = extract_duration("It will take about 2 hours and 30 minutes", lang="en")
print(duration)  # timedelta object
print(remainder)  # "about"
```

### Formatting Time

Generate a natural-sounding time format suitable for voice or display in different languages, allowing customization for
speech or written text.

```python
from ovos_date_parser import nice_time
from datetime import datetime

dt = datetime.now()
formatted_time = nice_time(dt, lang="en", speech=True, use_24hour=False)
print(formatted_time)  # "three o'clock"
```

### Relative Time Descriptions

Create relative phrases for describing dates and times in relation to the current moment or a reference datetime.

```python
from ovos_date_parser import nice_relative_time
from datetime import datetime, timedelta

relative_time = nice_relative_time(datetime.now() + timedelta(days=1), datetime.now(), lang="en")
print(relative_time)  # "tomorrow"
```

## Related Projects

- [ovos-number-parser](https://github.com/OpenVoiceOS/ovos-number-parser) - for handling numbers
- [ovos-lang-parser](https://github.com/OVOSHatchery/ovos-lang-parser) - for handling languages
- [ovos-color-parser](https://github.com/OVOSHatchery/ovos-color-parser) - for handling colors
