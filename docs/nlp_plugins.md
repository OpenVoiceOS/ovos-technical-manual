# NLP plugins

Several NLP tasks are exposed as plugins, this allows to configure how to solve these tasks centrally

**NEW** in `ovos-core` version **0.0.8**

## Keyword Extraction

Extract keywords from utterances

| Plugin                                                                              | Description                     |
|-------------------------------------------------------------------------------------|---------------------------------|
| [ovos-keyword-extractor-heuristic](https://github.com/OpenVoiceOS/ovos-classifiers) |                                 |
| [ovos-keyword-extractor-rake](https://github.com/OpenVoiceOS/ovos-classifiers)      | nltk data dependent (stopwords) |

## Tokenization

Split utterances into tokens

| Plugin                                                                                      | Description               |
|---------------------------------------------------------------------------------------------|---------------------------|
| [ovos-tokenization-plugin-quebrafrases](https://github.com/OpenVoiceOS/ovos-plugin-manager) | heuristic based tokenizer |

## Sentence Segmentation

Split utterances into sub-commands

| Plugin                                                                                      | Description                           |
|---------------------------------------------------------------------------------------------|---------------------------------------|
| [ovos-segmentation-plugin-quebrafrases](https://github.com/OpenVoiceOS/ovos-plugin-manager) | heuristic based sentence segmentation |

## Coreference Resolution

Replace coreferences (pronouns) with their entities

| Plugin                                                                           | Description                          |
|----------------------------------------------------------------------------------|--------------------------------------|
| [ovos-coref-solver-heuristic](https://github.com/OpenVoiceOS/ovos-classifiers)   | heuristic based coref solver         |
| [ovos-classifiers-coref-solver](https://github.com/OpenVoiceOS/ovos-classifiers) | models trained with ovos-classifiers |

## Postag

| Plugin                                                                               | Description                          |
|--------------------------------------------------------------------------------------|--------------------------------------|
| [ovos-postag-plugin-nltk](https://github.com/OpenVoiceOS/ovos-plugin-manager)        | using nltk default postag            |
| [ovos-classifiers-postag-plugin](https://github.com/OpenVoiceOS/ovos-plugin-manager) | models trained with ovos-classifiers |
