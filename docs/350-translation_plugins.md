# Language Detection and Translation Plugins

Language detection and translation plugins in Open Voice OS (OVOS) enable the system to identify the language of text and translate it between different languages. These plugins are particularly useful in the context of [Universal Skills](https://openvoiceos.github.io/ovos-technical-manual/universal_skills) and can be integrated with external tools like [solvers](https://openvoiceos.github.io/ovos-technical-manual/solvers/).

## Available Language Plugins

OVOS supports a variety of language detection and translation plugins, each with different capabilities, such as language detection, text translation, offline functionality, and support for external APIs.

| Plugin                                                                                                                                                                                                            | Detect | Translate | Offline | Type              |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|-----------|---------|-------------------|
| [ovos-translate-plugin-server](https://github.com/OpenVoiceOS/ovos-translate-server-plugin)                                                                                                                       | ✔️     | ✔️        | ❌       | API (self hosted) |
| [ovos-translate-plugin-nllb](https://github.com/OpenVoiceOS/ovos-translate-plugin-nllb)                                                                                                                           | ❌     | ✔️        | ✔️      | FOSS              |
| [ovos-lang-detector-fasttext-plugin](https://github.com/OpenVoiceOS/ovos-lang-detector-fasttext-plugin)                                                                                                           | ✔️     | ❌        | ✔️      | FOSS              |
| [ovos-lang-detect-ngram-lm](https://github.com/OpenVoiceOS/ovos-classifiers)                                                                                                                                      | ✔️     | ❌        | ✔️      | FOSS              |
| [ovos-lang-detector-plugin-lingua-podre](https://github.com/OpenVoiceOS/lingua-podre)                                                                                                                             | ✔️     | ❌        | ✔️      | FOSS              |
| [ovos-lang-detector-plugin-voter](https://github.com/OpenVoiceOS/ovos-lang-detector-classics-plugin)                                                                                                              | ✔️     | ❌        | ✔️      | FOSS              |
| [ovos-lang-detector-plugin-cld2](https://github.com/OpenVoiceOS/ovos-lang-detector-classics-plugin)                                                                                                               | ✔️     | ❌        | ✔️      | FOSS              |
| [ovos-lang-detector-plugin-cld3](https://github.com/OpenVoiceOS/ovos-lang-detector-classics-plugin)                                                                                                               | ✔️     | ❌        | ✔️      | FOSS              |
| [ovos-lang-detector-plugin-fastlang](https://github.com/OpenVoiceOS/ovos-lang-detector-classics-plugin)                                                                                                           | ✔️     | ❌        | ✔️      | FOSS              |
| [ovos-lang-detector-plugin-langdetect](https://github.com/OpenVoiceOS/ovos-lang-detector-classics-plugin)                                                                                                         | ✔️     | ❌        | ✔️      | FOSS              |
| [ovos-google-translate-plugin](https://github.com/OpenVoiceOS/ovos-google-translate-plugin)                                                                                                                       | ✔️     | ✔️        | ❌       | API (free)        |
| [neon-lang-plugin-libretranslate](https://github.com/NeonGeckoCom/neon-lang-plugin-libretranslate)     | ✔️     | ✔️        | ❌       | API (self hosted) |
| [neon-lang-plugin-amazon_translate](https://github.com/NeonGeckoCom/neon-lang-plugin-amazon_translate) | ✔️     | ✔️        | ❌       | API (key)         |

### Key Features of Language Plugins:

- **Language Detection**: Plugins like `ovos-lang-detector-fasttext-plugin` automatically detect the language of the input text, which is crucial for multi-language support in voice assistants.
- **Translation**: Plugins like `ovos-translate-plugin-nllb` and `ovos-google-translate-plugin` can translate text from one language to another, enabling multilingual capabilities for OVOS.
- **Offline Support**: Some plugins, such as `ovos-lang-detector-fasttext-plugin`, offer offline functionality, which is essential in environments where an internet connection may not be available.
- **API-based Plugins**: Plugins like `ovos-translate-plugin-server` and `neon-lang-plugin-libretranslate` use external APIs and can be self-hosted or accessed with an API key for translation services.

## Standalone Usage

*TODO: Add standalone usage instructions for each plugin once ready.*

## Plugin Template

*TODO: Provide a template for developing custom language detection and translation plugins.*

By using these plugins, developers can easily integrate language detection and translation features into OVOS-based voice assistants, making it more versatile and capable of handling multiple languages.
