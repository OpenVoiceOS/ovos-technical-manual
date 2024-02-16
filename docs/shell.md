# OVOS Shell

[OVOS-shell](https://github.com/OpenVoiceOS/ovos-shell) is the OpenVoiceOS client implementation of the [mycroft-gui-qt5](https://github.com/OpenVoiceOS/mycroft-gui-qt5) library used in our embedded device images

## Companion Plugins

To unlock full functionality you also need to configure [ovos-gui-plugin-shell-companion](https://github.com/OpenVoiceOS/ovos-gui-plugin-shell-companion) in mycroft.conf

This plugin integrates with `ovos-gui` to provide:

- color scheme manager
- notifications widgets
- configuration provider  (settings UI)
- brightness control  (night mode etc)

```javascript
{
  "gui": {
    // Extensions provide additional GUI platform support for specific devices
    "extension": "ovos-gui-plugin-shell-companion",
    
    // homescreen skill to use
    "idle_display_skill": "skill-ovos-homescreen.openvoiceos"
  }
}
```

OVOS-shell is tightly coupled to [PHAL](#what-is-phal), the following companion plugins should also be installed

- [ovos-PHAL-plugin-network-manager](https://github.com/OpenVoiceOS/ovos-PHAL-plugin-network-manager)
- [ovos-PHAL-plugin-gui-network-client](https://github.com/OpenVoiceOS/ovos-PHAL-plugin-gui-network-client)
- [ovos-PHAL-plugin-wifi-setup](https://github.com/OpenVoiceOS/ovos-PHAL-plugin-wifi-setup)
- [ovos-PHAL-plugin-alsa](https://github.com/OpenVoiceOS/ovos-PHAL-plugin-alsa)
- [ovos-PHAL-plugin-system](https://github.com/OpenVoiceOS/ovos-PHAL-plugin-system)


## Configuration

The Shell can be configured in a few ways.

### GUI

Display settings

![](https://github.com/OpenVoiceOS/ovos_assets/raw/master/Images/shell_settings.gif)

Color Theme editor

![](https://github.com/OpenVoiceOS/ovos_assets/raw/master/Images/shell_theme.gif)


### Shell Options
`~/.config/OpenvoiceOS/OvosShell.conf` can be edited to change shell options that
may also be changed via UI. An example config would look like:
```
[General]
fakeBrightness=1
menuLabels=true
```

### Themes
Shell themes can be included in `/usr/share/OVOS/ColorSchemes/` or 
`~/.local/share/OVOS/ColorSchemes/` in json format. Note that colors should include
an alpha value (usually `FF`).

```json
{
  "name": "Neon Green",
  "primaryColor": "#FF072103",
  "secondaryColor": "#FF2C7909",
  "textColor": "#FFF1F1F1"
}
```