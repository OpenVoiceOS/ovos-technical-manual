# Language Detection/Translation Plugins

These plugins can be used to detect the language of text and to translate it

They are used internally by [Universal Skills](https://openvoiceos.github.io/ovos-technical-manual/universal_skills) and
integrated with external tools such
as [solvers](https://openvoiceos.github.io/ovos-technical-manual/solvers/)

## List of Language plugins

| Plugin                                                                                                                                                                                                            | Detect | Tx | Offline | Type              |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|----|---------|-------------------|
| [ovos-translate-plugin-server](https://github.com/OpenVoiceOS/ovos-translate-server-plugin)                                                                                                                       | ✔️     |  ✔️   | ❌       | API (self hosted) |
| [ovos-translate-plugin-nllb](https://github.com/OpenVoiceOS/ovos-translate-plugin-nllb)                                                                                                                           | ❌️     |  ✔️    | ✔️      | FOSS              |
| [ovos-lang-detector-fasttext-plugin](https://github.com/OpenVoiceOS/ovos-lang-detector-fasttext-plugin)                                                                                                           | ✔️     | ❌  | ✔️      | FOSS              |
| [ovos-lang-detect-ngram-lm](https://github.com/OpenVoiceOS/ovos-classifiers)                                                                                                                                      | ✔️     | ❌  | ✔️      | FOSS              |
| [ovos-lang-detector-plugin-lingua-podre](https://github.com/OpenVoiceOS/lingua-podre)                                                                                                                             | ✔️     | ❌  | ✔️      | FOSS              |
| [ovos-lang-detector-plugin-voter](https://github.com/OpenVoiceOS/ovos-lang-detector-classics-plugin)                                                                                                              | ✔️     | ❌  | ✔️      | FOSS              |
| [ovos-lang-detector-plugin-cld2](https://github.com/OpenVoiceOS/ovos-lang-detector-classics-plugin)                                                                                                               | ✔️     | ❌  | ✔️      | FOSS              |
| [ovos-lang-detector-plugin-cld3](https://github.com/OpenVoiceOS/ovos-lang-detector-classics-plugin)                                                                                                               | ✔️     | ❌  | ✔️      | FOSS              |
| [ovos-lang-detector-plugin-fastlang](https://github.com/OpenVoiceOS/ovos-lang-detector-classics-plugin)                                                                                                           | ✔️     | ❌  | ✔️      | FOSS              |
| [ovos-lang-detector-plugin-langdetect](https://github.com/OpenVoiceOS/ovos-lang-detector-classics-plugin)                                                                                                         | ✔️     | ❌  | ✔️      | FOSS              |
| [ovos-google-translate-plugin](https://github.com/OpenVoiceOS/ovos-google-translate-plugin)                                                                                                                       | ✔️     | ✔️ | ❌       | API (free)        |
| ![imagem](https://github.com/OpenVoiceOS/ovos-media/assets/33701864/90f31b0a-dd56-457d-a3cf-7fc08b460038)  [neon-lang-plugin-libretranslate](https://github.com/NeonGeckoCom/neon-lang-plugin-libretranslate)     | ✔️     | ✔️ | ❌       | API (self hosted) |
| ![imagem](https://github.com/OpenVoiceOS/ovos-media/assets/33701864/90f31b0a-dd56-457d-a3cf-7fc08b460038)  [neon-lang-plugin-amazon_translate](https://github.com/NeonGeckoCom/neon-lang-plugin-amazon_translate) | ✔️     | ✔️ | ❌       | API (key)         |

## Standalone Usage

TODO

## Plugin Template

TODO
