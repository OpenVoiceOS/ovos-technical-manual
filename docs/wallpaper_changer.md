## Wallpaper Changer

This Python package, `wallpaper_changer`, allows you to easily change your desktop wallpaper programmatically across various Linux desktop environments. 

It supports popular environments like GNOME, Unity, Cinnamon, MATE, XFCE, KDE, LXDE, Fluxbox, Openbox, and more.

It is integrated with OVOS via [ovos-PHAL-plugin-wallpaper-manager](https://github.com/OpenVoiceOS/ovos-PHAL-plugin-wallpaper-manager)

### Installation

You can install `wallpaper_changer` via pip:

```bash
pip install wallpaper_changer
```

### Usage

Here's a basic example of how you can use `wallpaper_changer` to set your wallpaper:

```python
import wallpaper_changer

# Set wallpaper from a local file
wallpaper_changer.set_wallpaper('/path/to/your/image.jpg')

# Set wallpaper from a URL
wallpaper_changer.set_wallpaper('https://example.com/your/image.jpg')
```

### Supported Desktop Environments

- **GNOME**: `gnome`, `unity`, `cinnamon`
- **MATE**: `mate`
- **XFCE**: `xfce4`
- **KDE**: `kde`
- **LXDE**: `lxde`
- **Fluxbox**: `fluxbox`
- **Openbox**: `openbox`
- **IceWM**: `icewm`
- **JWM**: `jwm`
- **AfterStep**: `afterstep`
- **Blackbox**: `blackbox`
- **WindowMaker**: `windowmaker`

### Notes

- Make sure to install any required dependencies for your desktop environment, such as `pygobject` for GNOME-based environments or `dbus-python` for KDE.
- Some functionalities might require specific tools or libraries to be installed on your system.

### Compatibility

This package is designed for use on Linux systems and may not work on Windows or macOS due to differences in desktop management APIs. Contributions welcome!
