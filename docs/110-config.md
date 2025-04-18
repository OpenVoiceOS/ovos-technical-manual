## Configuration Management

### Summary
The OVOS configuration loader merges settings from multiple sources—default, system, remote, and user—so you can customize only what you need without touching shipped files.

---

### Usage Guide

1. **Locate or create your user config**

```bash
mkdir -p ~/.config/mycroft
nano ~/.config/mycroft/mycroft.conf
```  

Add only the keys you want to override; everything else falls back to defaults.

2. **Override via environment variables (optional)**

```bash
export OVOS_CONFIG_BASE_FOLDER="myfolder"
export OVOS_CONFIG_FILENAME="myconfig.yaml"
```  

This changes paths such as:

- `~/.config/mycroft/mycroft.conf` → `~/.config/myfolder/mycroft.conf`
- `~/.config/mycroft/mycroft.conf` → `~/.config/mycroft/myconfig.yaml`

3. **Use special flags (in system config)**

```json
{
 "disable_user_config": true,
 "disable_remote_config": true
}
```  
   
Place these in `/etc/mycroft/mycroft.conf` (or your default package file) to turn off loading of remote or user settings.

---

### Technical Explanation

**Load Order & Overrides**  

OVOS loads **all** existing files in this sequence, with later files overriding earlier ones:

- **Default** (`ovos-config` package)
- **System** (`/etc/mycroft/mycroft.conf`)
- **Remote** (`~/.config/<base>/web_cache.json`)
- **User** (`~/.config/<base>/<filename>`)  

> 💡 Keys repeated in multiple files are overridden by the last‑loaded file containing them.

**File Locations & Formats**

- **Base folder**: Controlled by `OVOS_CONFIG_BASE_FOLDER` (defaults to `mycroft`).
- **Filename**: Controlled by `OVOS_CONFIG_FILENAME` (defaults to `mycroft.conf`).
- **Formats**: JSON (`.json` or `.conf`) or YAML (`.yml` or `.yaml`).

**Protected Keys**  

Prevent certain settings from being overridden by remote or user configs.

```json
{
 "protected_keys": {
   "user": [
     "gui_websocket.host",
     "websocket.host"
   ]
 }
}
```

> 💡 this example block users from exposing the messagebus accidentally

**Disabling Layers**

- **disable_user_config**: If `true`, XDG user configs are ignored.
- **disable_remote_config**: If `true`, downloaded remote configs (`web_cache.json`) are ignored.

---


### Tips & Caveats

- **Always use your user file** (`~/.config/.../mycroft.conf`) to override defaults—never edit system or package‑shipped files.
- Ensure your JSON is valid; mixed file extensions may lead to load errors.
- Remember that setting `disable_user_config` or `disable_remote_config` will silently skip those layers—use with caution.
- Admin PHAL is a special service that runs as root, this means it can **only access `/etc/mycroft/mycroft.conf`**

---

### References

- [OVOS Config Loader (GitHub)](https://github.com/OpenVoiceOS/ovos-config)
- [XDG Base Directory Specification](https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html)
