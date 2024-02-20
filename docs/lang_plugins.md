# Language Detection/Translation Plugins

These plugins can be used to detect the language of text and to translate it

They are not used internally by `ovos-core` but are integrated with external tools such as [solvers](https://openvoiceos.github.io/ovos-technical-manual/solvers/)

## List of Language plugins

| Plugin                                                                                                                                                                                                            | Detect | Tx | Offline | Type              |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|----|---------|-------------------|
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
