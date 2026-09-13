# Language Support in OpenVoiceOS

OpenVoiceOS (OVOS) aims to support multiple languages across its components, including intent recognition, speech-to-text (STT), text-to-speech (TTS), and skill dialogs. However, full language support requires more than translation of interface text. This document outlines the current state of language support, known limitations, and how contributors can help improve multilingual performance in OVOS.

---

While the OVOS installer allows users to select a preferred language, **selecting a language does not guarantee full support across all subsystems**. True multilingual support requires dedicated:

- ✅ Translations (intents, dialogs, settings, etc.)
- ✅ STT (Speech-to-Text) plugins trained on the target language
- ✅ TTS (Text-to-Speech) plugins capable of generating speech in the selected language
- ✅ Language-specific intent adaptation and fallback logic

Without these, many core features (e.g., voice commands, speech output, skill interactions) may not function as expected.

---

## Adding a New Language

Adding support for a new language in OVOS is a multi-step process requiring:

- Translations of assistant dialog and intent files
- A compatible STT plugin with reliable speech recognition
- A natural-sounding TTS voice
- Validation using real-world user data

We welcome and encourage community participation to improve language support. Every contribution helps make OVOS more accessible to speakers around the world.

---

## STT and TTS Requirements

For a language to function correctly in a voice assistant environment, it must have **dedicated STT and TTS plugins** that support the language reliably.

### STT (Speech-to-Text)

- STT plugins must be able to recognize speech in the target language with high accuracy.
- Some plugins are multilingual (e.g., Whisper, MMS), but accuracy varies across languages.
- For production use, **language-specific tuning or models are recommended**.

### TTS (Text-to-Speech)

- The TTS engine must generate clear, natural-sounding speech in the selected language.
- Not all TTS plugins support all languages.
- Quality varies significantly by model and backend.

A list of early TTS and STT plugins test with per-language accuracy benchmarks is available at:

- 🔍 [STT Bench](https://stt-bench.tigregotico.pt)
- 🔊 [TTS Bench](https://tts-bench.tigregotico.pt)

---

## Translation Coverage

OVOS uses [GitLocalize](https://gitlocalize.com/users/OpenVoiceOS) for managing translation files across its repositories. This includes:

- Skill dialog files
- Intent files (used by Padatious/Adapt)
- Configuration metadata

### 📊 Translation Progress

Translation progress is tracked at:  
👉 [https://openvoiceos.github.io/lang-support-tracker](https://openvoiceos.github.io/lang-support-tracker)

The tracker provides daily updates and displays all languages that have reached at least 25% translation coverage.

> ❗ If your language is missing from GitLocalize, [open an issue](https://github.com/OpenVoiceOS/lang-support-tracker/issues) to request it. Currently, languages must be added manually.

---

## Known Limitations

- Selecting a language during installation only automatically configures a compatible STT/TTS plugin for **some languages**. Manual action might be required for full support
- Many skills contain only partial translations or outdated strings.
- Skills may be partially translated, with only a subset of intents available for your language
- Skills may have translated intents but missing dialog translations. The assistant typically speaks the dialog filename if it is not translated

---

## How to Improve Language Support

### 1. **Contribute Translations**

Use GitLocalize to translate dialog and intent files:

- [GitLocalize for OVOS](https://gitlocalize.com/users/OpenVoiceOS)
- [Translation Tutorial](https://www.openvoiceos.org/translation)

Translation stats for each language are also available in:

- [Markdown summaries (e.g., `translate_status_pt.md`)](https://openvoiceos.github.io/lang-support-tracker/tx_info/translate_status_pt-PT.md)
- [JSON format (e.g., `pt-PT.json`)](https://openvoiceos.github.io/lang-support-tracker/tx_info/pt-PT.json)

---

### 2. **Test in Real-World Usage**

Translation coverage alone does not ensure accuracy. Native speakers are encouraged to test OVOS with real speech input and report issues with:

- Intent matching failures
- Mispronunciations or robotic speech
- Incorrect or unnatural translations

You can help by **enabling open data collection** in your OVOS instance:

```json
"open_data": {
  "intent_urls": [
    "https://metrics.tigregotico.pt/intents"
  ]
}
```

> 💡 Alternatively, you may self-host the reporting server: [ovos-opendata-server on GitHub](https://github.com/OpenVoiceOS/ovos-opendata-server)

### Monitoring Tools

- 📈 Live Data Dashboard: [https://opendata.tigregotico.pt](https://opendata.tigregotico.pt)
- ✅ Server Status: [https://metrics.tigregotico.pt/status](https://metrics.tigregotico.pt/status)


---

## Benchmark Projects (Open Data)

Explore public benchmark tools for evaluating model performance:

| Project                                                         | Description |
|-----------------------------------------------------------------|-------------|
| [GitLocalize Bench](https://gitlocalize-bench.tigregotico.pt)   | Evaluate intent translation coverage and performance |
| [STT Bench](https://stt-bench.tigregotico.pt)                   | Test STT plugin accuracy across datasets and languages |
| [TTS Bench](https://tts-bench.tigregotico.pt)                   | Compare TTS output quality across plugins |
| [Meteocat](https://meteocat.bench.tigregotico.pt)               | Catalan weather query benchmark |


---

##  Tips for Contributors

- Translators: Use GitLocalize’s side-by-side editor to keep intent logic intact.
- Developers: Review user-submitted errors on the dashboard to improve skill performance.
- Curious users: Explore benchmark results to see how well OVOS handles your language.

