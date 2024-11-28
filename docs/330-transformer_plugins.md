# Transformer Plugins

**NEW** in `ovos-core` version **0.0.8**

Transformer plugins are able to *transform* data along the way, this can be audio data, text or metadata

## Audio Transformers

**NEW** in `ovos-core` version **0.0.8**, originally developed for [Neon](https://github.com/NeonGeckoCom/neon-transformers)


| plugin                                                                                                                                      | description                                                                                     | 
|---------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------| 
| [ovos-audio-transformer-plugin-fasterwhisper](https://github.com/OpenVoiceOS/ovos-stt-plugin-fasterwhisper)                                 | detect language from audio to inform STT                                                        | 
| [ovos-audio-transformer-plugin-speechbrain-langdetect](https://github.com/OpenVoiceOS/ovos-audio-transformer-plugin-speechbrain-langdetect) | detect language from audio to inform STT                                                        |  
| [ovos-audio-transformer-plugin-ggwave](https://github.com/OpenVoiceOS/ovos-audio-transformer-plugin-ggwave)                                 | decodes [data over audio](https://github.com/ggerganov/ggwave) and emits bus events in response |  


## Utterance Transformers

**NEW** in `ovos-core` version **0.0.8**, originally developed for [Neon](https://github.com/NeonGeckoCom/neon-transformers)


| plugin                            | description                           | source                                                                                                                          |
|-----------------------------------|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| ovos-utterance-normalizer         | normalizes text before pipeline stage | [OpenVoiceOS/ovos-classifiers](https://github.com/OpenVoiceOS/ovos-classifiers/blob/dev/ovos_classifiers/opm/heuristics.py#L41) |
| ovos-utterance-plugin-cancel      | cancel an utterance mid transcription | [OpenVoiceOS/ovos-utterance-plugin-cancel](https://github.com/OpenVoiceOS/ovos-utterance-plugin-cancel)                         |
| ovos-utterance-corrections-plugin | manually correct bad transcriptions   | [OpenVoiceOS/ovos-utterance-corrections-plugin](https://github.com/OpenVoiceOS/ovos-utterance-corrections-plugin)               |
| ovos-utterance-translation-plugin | auto translate unsupported languages  | [OpenVoiceOS/ovos-bidirectional-translation-plugin](https://github.com/OpenVoiceOS/ovos-bidirectional-translation-plugin)       |


## Metadata Transformers

**NEW** in `ovos-core` version **0.0.8**


## Dialog Transformers

**NEW** in `ovos-core` version **0.0.8**


| plugin                                | description                                    | source                                                                                                                    |
|---------------------------------------|------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| ovos-dialog-transformer-openai-plugin | rewrite speech with a LLM before executing TTS | [OpenVoiceOS/ovos-solver-plugin-openai-persona](https://github.com/OpenVoiceOS/ovos-solver-plugin-openai-persona)         |
| ovos-dialog-translation-plugin        | translate speech back into user language       | [OpenVoiceOS/ovos-bidirectional-translation-plugin](https://github.com/OpenVoiceOS/ovos-bidirectional-translation-plugin) |


## TTS Transformers

**NEW** in `ovos-core` version **0.0.8**


| plugin                          | description                   | source                                                                                                        |
|---------------------------------|-------------------------------|---------------------------------------------------------------------------------------------------------------|
| ovos-tts-transformer-sox-plugin | apply sound effects via `sox` | [OpenVoiceOS/ovos-tts-transformer-sox-plugin](https://github.com/OpenVoiceOS/ovos-tts-transformer-sox-plugin) |


## Standalone Usage

TODO

## Plugin Templates

TODO