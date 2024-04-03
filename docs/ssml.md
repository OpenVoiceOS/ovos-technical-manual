# SSMLBuilder Documentation

## Overview

The `SSMLBuilder` class provides a convenient way to generate Speech Synthesis Markup Language (SSML) strings for use with text-to-speech systems, such as Amazon Polly or Google Text-to-Speech. 
SSML allows developers to control various aspects of speech synthesis, such as voice, pitch, rate, and volume.

The `SSMLBuilder` class simplifies the creation of SSML strings by providing intuitive methods to control various aspects of speech synthesis.

## SSMLBuilder

```python
def __init__(self, ssml_tag=False, speak_tag=False)
```
- `ssml_tag`: If `True`, wraps the generated SSML with `<ssml>` tags. Default is `False`.
- `speak_tag`: If `True`, wraps the generated SSML with `<speak>` tags. Default is `False`.

#### Text Manipulation
1. `sub(alias, word)`
   - Replaces a word with a specified alias.
2. `emphasis(level, word)`
   - Emphasizes a word with a specified level.
3. `parts_of_speech(word, role)`
   - Specifies the usage or role of a word.
4. `pause_by_strength(strength)`
   - Inserts a pause with a specified strength.
5. `sentence(text)`
   - Wraps text with `<s>` tags to denote a sentence.
6. `say_emphasis(text)`
   - Emphasizes the text strongly.
7. `say_strong(text)`
   - Modifies the vocal-tract length to increase speech intensity.
8. `say_weak(text)`
   - Modifies the vocal-tract length to decrease speech intensity.
9. `say_softly(text)`
   - Modifies the phonation to produce a softer speech.
10. `say_auto_breaths(text)`
    - Adds automatic breaths to the speech.
11. `paragraph(text)`
    - Wraps text with `<p>` tags to denote a paragraph.
12. `audio(audio_file, text)`
    - Embeds audio with specified text.

#### Timing and Prosody
1. `pause(time, unit)`
   - Inserts a pause for a specified duration.
2. `prosody(attribute, text)`
   - Modifies prosodic attributes of the text such as pitch, rate, or volume.
3. `pitch(pitch, text)`
   - Changes the pitch of the text.
4. `volume(volume, text)`
   - Modifies the volume of the text.
5. `rate(rate, text)`
   - Adjusts the speaking rate of the text.

#### Voice and Phoneme
1. `say(text)`
   - Adds normal speed text to SSML.
2. `say_loud(text)`
   - Increases the volume of the text.
3. `say_slow(text)`
   - Slows down the speaking rate of the text.
4. `say_fast(text)`
   - Speeds up the speaking rate of the text.
5. `say_low_pitch(text)`
   - Lowers the pitch of the text.
6. `say_high_pitch(text)`
   - Raises the pitch of the text.
7. `say_whispered(text)`
   - Converts text into whispered speech.
8. `phoneme(ph, text)`
   - Specifies the phonetic pronunciation of the text.
9. `voice(voice, text)`
   - Specifies the voice to use for the text.
10. `whisper(text)`
    - Converts text into whispered speech.

#### Build and Utility
1. `build()`
   - Constructs the final SSML string.
2. `remove_ssml(text)`
   - Removes SSML tags from the given text.
3. `extract_ssml_tags(text)`
   - Extracts SSML tags from the given text.

### Example Usage
```python
# Initialize SSMLBuilder
ssml_builder = SSMLBuilder(ssml_tag=True, speak_tag=True)

# Generate SSML
ssml_builder.sentence("Hello, world!").pause(500, "ms").say_slow("How are you today?").build()

# Output:
# '<speak>\n<s>Hello, world!</s> <break time=500ms/><prosody rate='0.4'>How are you today?</prosody>\n</speak>'
```
