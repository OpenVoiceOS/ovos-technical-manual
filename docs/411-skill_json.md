# Skill Metadata File

The `skill.json` file is an optional but powerful way to describe your Open Voice OS (OVOS) skill. It provides metadata used for installation, discovery, and display in GUIs or app stores.

## Purpose

- Helps OVOS identify and install your skill.
- Enhances GUI experiences with visuals and usage examples.
- Lays the foundation for future help dialogs and skill documentation features.

---

## Usage Guide

1. Create a `skill.json` file inside your skill's `locale/<language-code>` folder.
2. Fill in the metadata fields as needed (see below).
3. If your skill supports multiple languages, include a separate `skill.json` in each corresponding `locale` subfolder.

> ⚠️ **Avoid using old `skill.json` formats** found in some legacy skills where the file exists at the root level. These are deprecated.

---

## Example `skill.json`

```json
{
  "skill_id": "skill-xxx.exampleauthor",
  "source": "https://github.com/ExampleAuthor/skill-xxx",
  "package_name": "ovos-skill-xxx",
  "pip_spec": "git+https://github.com/ExampleAuthor/skill-xxx@main",
  "license": "Apache-2.0",
  "author": "ExampleAuthor",
  "extra_plugins": {
    "core": ["ovos-utterance-transformer-xxx"],
    "PHAL": ["ovos-PHAL-xxx"],
    "listener": ["ovos-audio-transformer-xxx", "ovos-ww-plugin-xxx", "ovos-vad-plugin-xxx", "ovos-stt-plugin-xxx"],
    "audio": ["ovos-dialog-transformer-xxx", "ovos-tts-transformer-xxx", "ovos-tts-plugin-xxx"],
    "media": ["ovos-ocp-xxx", "ovos-media-xxx"],
    "gui": ["ovos-gui-extension-xxx"]
  },
  "icon": "http://example.com/icon.svg",
  "images": ["http://example.com/logo.png", "http://example.com/screenshot.png"],
  "name": "My Skill",
  "description": "Does awesome skill stuff!",
  "examples": [
    "do the thing",
    "say this to use the skill"
  ],
  "tags": ["productivity", "entertainment", "aliens"]
}
```

---

## Field Reference

| Field            | Type     | Required | Description |
|------------------|----------|----------|-------------|
| `skill_id`       | string   | ✅ Yes    | Unique ID, typically `repo.author` style (lowercase). |
| `source`         | string   | ❌ Optional | Git URL to install from source. |
| `package_name`   | string   | ✅ Yes    | Python package name (e.g., for PyPI installs). |
| `pip_spec`       | string   | ❌ Optional | [PEP 508](https://peps.python.org/pep-0508/) install spec. |
| `license`        | string   | ❌ Optional | License ID (see [SPDX list](https://spdx.org/licenses/)). |
| `author`         | string   | ❌ Optional | Display name of the skill author. |
| `extra_plugins`  | object   | ❌ Optional | Dependencies to be installed in other OVOS services (not this skill). |
| `icon`           | string   | ❌ Optional | URL to a skill icon (SVG recommended). |
| `images`         | list     | ❌ Optional | Screenshots or promotional images. |
| `name`           | string   | ❌ Optional | User-facing skill name. |
| `description`    | string   | ❌ Optional | Short, one-line summary of the skill. |
| `examples`       | list     | ❌ Optional | Example utterances your skill handles. |
| `tags`           | list     | ❌ Optional | Keywords for searchability. |

---

## Language Support

To support multiple languages, place a `skill.json` file in each corresponding `locale/<lang>` folder. Fields like `name`, `description`, `examples`, and `tags` can be translated for that locale.

---

## Installation Behavior

When installing a skill, OVOS will try the following methods in order:

1. `pip_spec` (if present)
2. `package_name` (from PyPI)
3. `source` (from Git)

At least **one valid installation path is required**.

---

## Tips & Caveats

- This metadata format is **experimental** and may evolve—check for updates regularly.
- `extra_plugins` allows for declaring companion plugins your skill may require, but that aren't direct Python dependencies.
- The [Skill store](https://store.openvoiceos.org) and GUI tools like `ovos-shell` use `icon`, `images`, `examples`, and `description` to present the skill visually.

---

## See Also

- [PEP 508 – Dependency specification](https://peps.python.org/pep-0508/)
- [SPDX License List](https://spdx.org/licenses/)
