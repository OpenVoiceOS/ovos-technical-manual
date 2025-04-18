# Skill Settings


Skill settings in OVOS allow users to configure and personalize the behavior of Skills—either through the command line, configuration files, or a web-based interface. This enables advanced customization and support for external integrations, while remaining completely optional for basic usage.

## Common Use Cases

- Changing default behaviors (e.g. alarm sounds, display preferences)
- Authenticating with third-party services (e.g. Spotify)
- Entering longer or complex data (e.g. IP addresses, API keys)

---

## Using Skill Settings in Your Skill

Settings are managed through a dictionary-like object available on the `OVOSSkill` base class. They are persisted to disk and can be updated locally by the Skill or remotely by the user via a frontend.

Settings are stored in your Skill's configuration directory, usually:

```
~/.config/mycroft/skills/<your_skill_id>/settings.json
```

### Accessing Settings

You can read settings like a standard Python dictionary, but it's recommended to use `.get()` to avoid `KeyError` exceptions:

```python
# Safely read the 'show_time' setting with a default fallback
show_time = self.settings.get("show_time", False)
```

> ⚠️ Avoid using `self.settings['show_time']` directly, as it will raise a `KeyError` if the setting is not defined.

Also, do not access `self.settings` in your `__init__()` method—wait until the `initialize()` method to ensure the settings are fully loaded.

---

## Handling Settings Updates

OVOS automatically checks for setting changes, either locally or from a remote backend. You can register a callback to react when settings change:

```python
def initialize(self):
    self.settings_change_callback = self.on_settings_changed
    self.on_settings_changed()  # Also run immediately on start

def on_settings_changed(self):
    show_time = self.settings.get('show_time', False)
    self.trigger_time_display(show_time)
```

This ensures your Skill responds to user configuration changes dynamically.

---

## Writing to Settings

You can update and persist values to the settings file simply by assigning them:

```python
self.settings['show_time'] = True
```

Changes will persist across restarts unless overridden remotely via a backend or web interface.

---

## Web-Based Skill Settings (Optional UI)

A **community-built** web interface, [OVOS Skill Config Tool](https://github.com/OscillateLabsLLC/ovos-skill-config-tool), provides a modern, user-friendly way to configure OVOS skills.

### Features

- Clean UI for managing skill-specific settings
- Grouping and organization of Skills
- Dark mode support
- Built-in Basic Authentication for security

![Skill Config Interface](https://github.com/OscillateLabsLLC/ovos-skill-config-tool/raw/main/skills-interface.webp)

### Installation

Install via pip:

```bash
pip install ovos-skill-config-tool
```

Run the tool:

```bash
ovos-skill-config-tool
```

Access it in your browser at [http://0.0.0.0:8000](http://0.0.0.0:8000)

#### Default Login

- **Username**: `ovos`
- **Password**: `ovos`

To customize credentials:

```bash
export OVOS_CONFIG_USERNAME=myuser
export OVOS_CONFIG_PASSWORD=mypassword
ovos-skill-config-tool
```

---

## Tips

- Always use `.get(key, default)` for safe reads.
- Use `initialize()` instead of `__init__()` for anything that depends on settings.
- Use settings callbacks to keep your Skill reactive to user changes.

---

## See Also

- [OVOS Skill Config Tool on GitHub](https://github.com/OscillateLabsLLC/ovos-skill-config-tool)