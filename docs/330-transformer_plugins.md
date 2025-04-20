# Transformer Plugins

Transformer plugins in Open Voice OS (OVOS) provide a flexible way to modify and enhance various types of data during processing. These plugins can transform audio data, text, metadata, and even dialog content.

## Audio Transformers

Audio transformers are designed to process and modify audio data. They can be used to detect languages from audio input or even decode data embedded within the audio.

### Available Plugins

| Plugin                                                                                                                                      | Description                                                                                     |
|---------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------|
| [ovos-audio-transformer-plugin-fasterwhisper](https://github.com/OpenVoiceOS/ovos-stt-plugin-fasterwhisper)                                 | Detects language from audio to inform Speech-to-Text (STT) processing.                          |
| [ovos-audio-transformer-plugin-speechbrain-langdetect](https://github.com/OpenVoiceOS/ovos-audio-transformer-plugin-speechbrain-langdetect) | Detects language from audio to inform STT.                                                     |
| [ovos-audio-transformer-plugin-ggwave](https://github.com/OpenVoiceOS/ovos-audio-transformer-plugin-ggwave)                                 | Decodes [data over audio](https://github.com/ggerganov/ggwave) and emits bus events in response. |

These plugins help automate language detection and data interpretation, which are especially useful for multilingual environments or when integrating specialized data streams into your voice assistant.

## Utterance Transformers

Utterance transformers modify the textual representation of speech, improving the quality of transcriptions and allowing for more advanced processing.

### Available Plugins

| Plugin                            | Description                           | Source                                                                                                                    |
|-----------------------------------|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| ovos-utterance-normalizer         | Normalizes text before it reaches the pipeline stage. | [OpenVoiceOS/ovos-utterance-normalizer](https://github.com/OpenVoiceOS/ovos-utterance-normalizer)                         |
| ovos-utterance-plugin-cancel      | Cancels an utterance mid-transcription. | [OpenVoiceOS/ovos-utterance-plugin-cancel](https://github.com/OpenVoiceOS/ovos-utterance-plugin-cancel)                   |
| ovos-utterance-corrections-plugin | Manually corrects bad transcriptions.  | [OpenVoiceOS/ovos-utterance-corrections-plugin](https://github.com/OpenVoiceOS/ovos-utterance-corrections-plugin)         |
| ovos-utterance-translation-plugin | Automatically translates unsupported languages. | [OpenVoiceOS/ovos-bidirectional-translation-plugin](https://github.com/OpenVoiceOS/ovos-bidirectional-translation-plugin) |

These plugins enhance the quality of speech recognition and allow real-time intervention for handling special cases, such as language translation or manual corrections.

## Metadata Transformers

Metadata transformers handle the transformation of metadata associated with audio or utterances. They help in structuring or enriching metadata for further use.

### Available Plugins

*Currently, no specific plugins are listed for metadata transformers.*

## Dialog Transformers

Dialog transformers modify conversational content, allowing you to rewrite speech or translate it into a different language before execution. These plugins are particularly useful for improving the interactivity and flexibility of voice-based dialogues.

### Available Plugins

| Plugin                                | Description                                    | Source                                                                                                                    |
|---------------------------------------|------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| ovos-dialog-transformer-openai-plugin | Rewrites speech with a large language model (LLM) before executing Text-to-Speech (TTS). | [OpenVoiceOS/ovos-solver-plugin-openai-persona](https://github.com/OpenVoiceOS/ovos-solver-plugin-openai-persona)         |
| ovos-dialog-translation-plugin        | Translates speech back into the user's language. | [OpenVoiceOS/ovos-bidirectional-translation-plugin](https://github.com/OpenVoiceOS/ovos-bidirectional-translation-plugin) |

Dialog transformers enable more dynamic interactions, such as generating personalized responses or translating dialogues into multiple languages.

## TTS Transformers

TTS (Text-to-Speech) transformers allow you to apply various effects or modifications to the speech output generated by the assistant, such as sound effects or audio filtering.

### Available Plugins

| Plugin                          | Description                   | Source                                                                                                        |
|---------------------------------|-------------------------------|---------------------------------------------------------------------------------------------------------------|
| ovos-tts-transformer-sox-plugin | Applies sound effects via `sox` (Sound eXchange). | [OpenVoiceOS/ovos-tts-transformer-sox-plugin](https://github.com/OpenVoiceOS/ovos-tts-transformer-sox-plugin) |

These plugins are helpful for modifying the final audio output, such as adding special effects, changing pitch, or applying filters.

## Standalone Usage

*Details on standalone usage are coming soon.*

## Plugin Templates

*Details on plugin templates are coming soon.*

## Conclusion

Transformer plugins in OVOS offer versatile tools for transforming data at various stages of processing. Whether you're working with audio, text, metadata, or dialog, these plugins allow for a high degree of customization and enhancement. OVOS's flexible plugin system empowers developers to create powerful, tailored experiences for users. Stay tuned for more updates and templates to help you create your own custom plugins.
