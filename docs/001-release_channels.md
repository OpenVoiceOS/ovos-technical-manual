# OVOS Release Channels & Installation Options

Open Voice OS (OVOS) is a **modular voice assistant platform** that lets you install only the components you need. Whether you're building a lightweight voice interface or a full-featured smart assistant, OVOS gives you flexibility through modular packages and optional feature sets called **extras**.

To manage updates and ensure system stability, OVOS uses **release channels** and **constraints files**, allowing users to pin versions based on their desired stability level.

---

## Choosing a Release Channel

OVOS follows [**semantic versioning**](https://semver.org/) (SemVer) with a **rolling release model** and supports three release channels — **stable**, **testing**, and **alpha** — so you can pick the right balance between cutting-edge features and system reliability.

These channels are managed via the [constraints files](https://pip.pypa.io/en/stable/user_guide/#constraints-files) hosted in the [ovos-releases](https://github.com/OpenVoiceOS/ovos-releases) repository

### 1. Stable Channel (Production-Ready)
- ✅ Bug fixes only
- 🚫 No new features or breaking changes
- ✅ Recommended for production or everyday use

```bash
pip install ovos-core[mycroft] -c https://raw.githubusercontent.com/OpenVoiceOS/ovos-releases/refs/heads/main/constraints-stable.txt
```

### 2. Testing Channel (Feature Updates)
- ✅ Bug fixes and new features
- ⚠️ Not as thoroughly tested as stable
- 🧪 Best for early adopters or development environments

```bash
pip install ovos-core[mycroft] -c https://raw.githubusercontent.com/OpenVoiceOS/ovos-releases/refs/heads/main/constraints-testing.txt
```

### 3. Alpha Channel (Bleeding Edge)
- 🔬 Experimental features
- ⚠️ May include breaking changes
- 🧪 Not suitable for production use

```bash
pip install ovos-core[mycroft] --pre -c https://raw.githubusercontent.com/OpenVoiceOS/ovos-releases/refs/heads/main/constraints-alpha.txt
```

> 💡 `constraints.txt` files act like version "filters". They don’t install packages directly, but ensure only approved versions get installed.

---

## 🔧 OVOS From Scratch: Custom Installation

Rather than using a full distro, you can manually pick which components to install:

- [`ovos-messagebus`](https://github.com/OpenVoiceOS/ovos-messagebus) – internal messaging between services
- [`ovos-core`](https://github.com/OpenVoiceOS/ovos-core) – skill handling
- [`ovos-audio`](https://github.com/OpenVoiceOS/ovos-audio) – text-to-speech (TTS), audio playback
- [`ovos-dinkum-listener`](https://github.com/OpenVoiceOS/ovos-dinkum-listener) – wake word, voice activation
- [`ovos-gui`](https://github.com/OpenVoiceOS/ovos-gui) – GUI integration
- [`ovos-PHAL`](https://github.com/OpenVoiceOS/ovos-PHAL) – hardware abstraction layer

This is useful if you’re building something like a **Hivemind node** or **headless device**, where you might not need audio output or a GUI.

---

## What Are OVOS Extras?

OVOS uses Python extras (e.g., `[mycroft]`) to let you install predefined groups of components based on your use case.

| Extra Name           | Purpose                                                                 |
|----------------------|-------------------------------------------------------------------------|
| `mycroft`            | Core services for full voice assistant experience                      |
| `lgpl`               | Adds optional LGPL-licensed tools like Padatious                       |
| `plugins`            | Includes various plugin interfaces                                     |
| `skills-essential`   | Must-have skills (like system control, clock, weather)                 |
| `skills-audio`       | Audio I/O-based skills                                                  |
| `skills-gui`         | GUI-dependent skills                                                    |
| `skills-internet`    | Skills that require an internet connection                             |
| `skills-media`       | OCP (OpenVoiceOS Common Play) media playback skills                    |
| `skills-desktop`     | Desktop environment integrations                                       |

### Full Installation Example

```bash
pip install ovos-core[mycroft,lgpl,plugins,skills-essential,skills-audio,skills-gui,skills-internet,skills-media,skills-desktop]
```

### Minimal Installation Example

```bash
pip install ovos-core[mycroft,plugins,skills-essential]
```


---

## Technical Notes

- OVOS originally began as a fork of `mycroft-core`. Since version **0.0.8**, it has been **fully modularized**, with each major service in its own repository.
- All packages follow [Semantic Versioning (SemVer)](https://semver.org/), so you can rely on versioning to understand stability and compatibility.
- Constraints files are a **work in progress** and won’t be finalized until the first official [codename release](https://github.com/OpenVoiceOS/ovos-releases/issues/5).

---

## ⚠️ Tips & Caveats

- Using `--pre` installs pre-releases across all dependencies, not just OVOS-specific ones — so use with caution.
- You can mix and match extras based on your hardware or use case, e.g., omit GUI skills on a headless server.
- When using constraints files, make sure all packages are pinned — it avoids installing incompatible versions.

---

## See Also

- [OVOS Releases repo](https://github.com/OpenVoiceOS/ovos-releases)
- [Constraints files explanation (pip docs)](https://pip.pypa.io/en/stable/user_guide/#constraints-files)
- [Semantic Versioning](https://semver.org/)
- [OVOS Component Repos](https://github.com/OpenVoiceOS)

