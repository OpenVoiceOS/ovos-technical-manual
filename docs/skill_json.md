# skill.json

Skills can **optionally** include a `skill.json` file in their `locale` folder

> **WARNING** skills in the wild might include an earlier and **deprecated** version of `skill.json` in the root of the skill directory

This file contains metadata about the skill and all info about how to install and display the skill entry

```javascript
{
  "skill_id": "skill-XXX.exampleauthor",
  "source": "https://github.com/ExampleAuthor/skill-XXX",
  "package_name": "ovos-skill-XXX",
  "pip_spec": "git+https://github.com/ExampleAuthor/skill-XXX@BRANCH",
  "license": "Apache-2.0",
  "author": "ExampleAuthor",
  "extra_plugins": {
    "core": ["ovos-utterance-transformer-XXX"],
    "PHAL": ["ovos-PHAL-XXX"],
    "listener": ["ovos-audio-transformer-XXX", "ovos-ww-plugin-XXX", "ovos-vad-plugin-XXX", "ovos-stt-plugin-XXX"],
    "audio": ["ovos-dialog-transformer-XXX","ovos-tts-transformer-XXX", "ovos-tts-plugin-XXX"],
    "media": ["ovos-ocp-XXX", "ovos-media-XXX"],
    "gui": ["ovos-gui-extension-XXX"]
  },
  "icon": "http://example_icon.svg",
  "images": ["http://example_logo.png", "http://example_screenshot.png"],
  "name": "My Skill",
  "description": "Does awesome skill stuff!",
  "examples": [
    "do the thing",
    "say this to use the skill"
  ],
  "tags": ["productivity", "entertainment", "aliens"]
}
```

> **EXPERIMENTAL** this specification might change, it is still at the proposal stage and open for feedback

# Spec

On the JSON file:

* `skill_id` Required unique skill identifier, this is defined in `setup.py` and by convention is usually `repo.author` (lower-cased)
* `source` Optional git url to download the source code, a skill can be installed from source with `pip install git+{source}`
* `package_name` Required package name of the skill, if the skill is on pypi it can be installed with `pip install {package_name}`
* `pip_spec` Optional [PEP 508](https://peps.python.org/pep-0508/#specification) install spec to install the skill. `pip install {pip_spec}`
* `license` Optional [SPDX License ID](https://spdx.org/licenses/)
* `author` Optional string author name. Overrides any name extracted from `skill_id` or `source` URL
* `extra_plugins` Optional list of python requirements that are not direct dependencies of the skill but should be installed in a companion OVOS service
* `name` Optional human-readable name for the skill to be used in store listings, UI, etc. Overrides any name extracted from `skill_id` or `source` URL
* `description` Optional human-readable short description of the skill. This should be limited to one or two sentences max
* `examples` Optional list of example utterances that this skill should handle
* `tags` Optional list of arbitrary labels and categories, helps search results to find this skill
* `icon` Optional skill icon to display
* `images` Optional list of images to showcase the skill

> **LANG SUPPORT**: include `skill.json` in each lang subfolder of your `locale` skill directory, this signals language support and allows translation of `name`, `description`, `examples` and `tags`

# Installation
Installation of a skill should generally follow the priority of `<pip_spec>` > `<package_name>` > `git+<source>`. At least one of these *MUST* be a valid installation option. A minimal exmple of
skill installation in Python is included *for reference only*:

```python
import pip
import json

with open("skill.json") as f:
    skill_spec = json.load(f)

specs = [skill_spec.get('pip_spec')] if skill_spec.get('pip_spec') else []
specs.append(skill_spec['package_name']
if skill_spec.get('source'):
    specs.append(f"git+{skill_spec['source']}")

for spec in specs:
    if pip.main(['install', spec) == 0:
        # Successful installation
        break
```

Note that this example does not account for existing packages and using it on its own could result in broken dependencies.
