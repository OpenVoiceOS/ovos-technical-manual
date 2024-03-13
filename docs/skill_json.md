# skill.json

Skills can **optionally** include a `skill.json` file in their `locale` folder

> **WARNING** skills in the wild might include an earlier and **deprecated** version of `skill.json` in the root of the skill directory

This file contains metadata about the skill and all info about how to install and display the skill entry

```javascript
{
  "skill_id": "skill-XXX.exampleauthor",
  "source": "https://github.com/ExampleAuthor/skill-XXX",
  "package_name": "ovos-skill-XXX",
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

* `skill_id` refers to the unique skill identifier, this is defined in `setup.py` and by convention is usually `repo.author` (lower-cased)
* `source` git url to download the source code, a skill can be installed from source with `pip install git+{source}`
* `package_name` the package name of the skill, if the skill is on pypi it can be installed with `pip install {package_name}`
* `extra_plugins` list of python requirements that are not direct dependencies of the skill but should be installed in a companion OVOS service
* `name` is a human-readable name for the skill to be used in store listings, UI, etc.
* `description` is a human-readable short description of the skill. This should be limited to one or two sentences max
* `examples` is a list of example utterances that this skill should handle
* `tags` is a list of arbitrary labels and categories, helps search results to find this skill
* `icon` optional skill icon to display
* `images` optional list of images to showcase the skill

> **LANG SUPPORT**: include `skill.json` in each lang subfolder of your `locale` skill directory, this signals language support and allows translation of `name`, `description`, `examples` and `tags`