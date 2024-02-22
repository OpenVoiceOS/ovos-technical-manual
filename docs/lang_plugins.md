# Language Detection/Translation Plugins

These plugins can be used to detect the language of text and to translate it

They are used internally by [Universal Skills](https://openvoiceos.github.io/ovos-technical-manual/universal_skills) and integrated with external tools such
as [solvers](https://openvoiceos.github.io/ovos-technical-manual/solvers/)

## List of Language plugins

| Plugin                                                                                                                                                                                                            | Detect | Tx | Offline | Type              |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|----|---------|-------------------|
| [ovos-translate-plugin-nllb](https://github.com/OpenVoiceOS/ovos-translate-plugin-nllb)                                                                                                                           | ❌️     | ✔  | ✔️      | FOSS              |
| [ovos-translate-plugin-server](https://github.com/OpenVoiceOS/ovos-translate-server-plugin)                                                                                                                       | ❌      | ✔  | ❌       | API (self hosted) |
| [ovos-lang-detector-plugin-lingua-podre](https://github.com/OpenVoiceOS/lingua-podre)                                                                                                                             | ✔️     | ❌  | ✔️      | FOSS              |
| [ovos-lang-detect-ngram-lm](https://github.com/OpenVoiceOS/ovos-classifiers)                                                                                                                                      | ✔️     | ❌  | ✔️      | FOSS              |
| ![imagem](https://github.com/OpenVoiceOS/ovos-media/assets/33701864/90f31b0a-dd56-457d-a3cf-7fc08b460038)  [neon-lang-plugin-cld2](https://github.com/NeonGeckoCom/neon-lang-plugin-cld2)                         | ✔️     | ❌  | ✔️      | FOSS              |
| ![imagem](https://github.com/OpenVoiceOS/ovos-media/assets/33701864/90f31b0a-dd56-457d-a3cf-7fc08b460038)  [neon-lang-plugin-cld3](https://github.com/NeonGeckoCom/neon-lang-plugin-cld3)                         | ✔️     | ❌  | ✔️      | FOSS              |
| ![imagem](https://github.com/OpenVoiceOS/ovos-media/assets/33701864/90f31b0a-dd56-457d-a3cf-7fc08b460038)  [neon-lang-plugin-langdetect](https://github.com/NeonGeckoCom/neon-lang-plugin-langdetect)             | ✔️     | ❌  | ✔️      | FOSS              |
| ![imagem](https://github.com/OpenVoiceOS/ovos-media/assets/33701864/90f31b0a-dd56-457d-a3cf-7fc08b460038)  [neon-lang-plugin-fastlang](https://github.com/NeonGeckoCom/neon-lang-plugin-fastlang)                 | ✔️     | ❌  | ✔️      | FOSS              |
| ![imagem](https://github.com/OpenVoiceOS/ovos-media/assets/33701864/90f31b0a-dd56-457d-a3cf-7fc08b460038)  [neon-lang-plugin-libretranslate](https://github.com/NeonGeckoCom/neon-lang-plugin-libretranslate)     | ✔️     | ✔️ | ❌       | API (self hosted) |
| ![imagem](https://github.com/OpenVoiceOS/ovos-media/assets/33701864/90f31b0a-dd56-457d-a3cf-7fc08b460038)  [neon-lang-plugin-apertium](https://github.com/NeonGeckoCom/neon-lang-plugin-apertium)                 | ❌      | ✔️ | ❌       | API (self hosted) |
| ![imagem](https://github.com/OpenVoiceOS/ovos-media/assets/33701864/90f31b0a-dd56-457d-a3cf-7fc08b460038)  [neon-lang-plugin-amazon_translate](https://github.com/NeonGeckoCom/neon-lang-plugin-amazon_translate) | ✔️     | ✔️ | ❌       | API (key)         |
| ![imagem](https://github.com/OpenVoiceOS/ovos-media/assets/33701864/90f31b0a-dd56-457d-a3cf-7fc08b460038)  [neon-lang-plugin-google_translate](https://github.com/NeonGeckoCom/neon-lang-plugin-google_translate) | ✔️     | ✔️ | ❌       | API (key)         |

## Standalone Usage

TODO

## Plugin Template

TODO
