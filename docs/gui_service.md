# GUI Service

OVOS devices with displays provide skill developers the opportunity to create skills that can be empowered by both voice
and screen interaction.

`ovos-gui`, aka, The GUI Service, is responsible for keeping track of what should be rendered, but does not perform the
rendering itself

The GUI service provides a websocket for gui clients to connect to, it is responsible for implementing the gui protocol
under `ovos-core`.

You can find indepth documentation in the dedicated GUI section of these docs

## Configuration

The gui service has a few sections in `mycroft.conf`

```javascript
"gui": {
    "idle_display_skill": "skill-ovos-homescreen.openvoiceos",
    "extension": "generic",
    "generic": {
        "homescreen_supported": false
    }
},
  
"gui_websocket": {
    "host": "0.0.0.0",
    "base_port": 18181,
    "route": "/gui",
    "ssl": false
},
```

## Architecture

The GUI state is defined by `namespaces`, usually corresponding to a `skill_id`, each with any number
of `pages`. `pages` are ordered, but only 1 page is rendered at a time.

OpenVoiceOS components interact with the GUI by defining session data and active pages, gui-clients may also send
back `events` to indicate interactions

The GUI clients may be implemented in any language, page templates are provided to skills via `self.gui.show_XXX` that
should be implemented by all clients.

## GUI Plugins

**NEW** in `ovos-core` version **0.0.8**

The active namespace is tracked by `ovos-gui` and manages
the [homescreen skill](https://github.com/OpenVoiceOS/skill-ovos-homescreen), desktop implementations like Plasma
Bigscreen do not have a homescreen, instead they manage each skill in their own window

A single GUI plugin can be loaded in `ovos-gui` to handle bus events and provide companion python code to GUI clients,
this is usually done by your OS and specific to a use case.

| plugin                                                                                            | description                                                                                                                                       | notes                                                                                                                                                                                                                 |
|---------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [ovos-gui-plugin-shell-companion](https://github.com/OpenVoiceOS/ovos-gui-plugin-shell-companion) | extra functionality for ovos-shell                                                                                                                | [message specs](https://openvoiceos.github.io/message_spec/shell)                                                                                                                                                     |
| [ovos-gui-plugin-bigscreen](https://github.com/OVOSHatchery/ovos-gui-plugin-bigscreen)            | window management for [Plasma Bigscreen](https://invent.kde.org/plasma/plasma-bigscreen) [Voice Apps](https://plasma-bigscreen.org/docs/develop/) | **UNMAINTED** <br> [message specs](https://openvoiceos.github.io/message_spec/gui_bigscreen/) <br> backup fork in life support [ovos-plasma-bigscreen-qt5](https://github.com/OVOSHatchery/ovos-plasma-bigscreen-qt5) |
| [ovos-gui-plugin-plasmoid](https://github.com/OVOSHatchery/ovos-gui-plugin-plasmoid)              | [Plasma integration of different Mycroft AI services](https://invent.kde.org/utilities/mycroft-plasmoid)                                          | **UNMAINTED** <br> never migrated to OVOS                                                                                                                                                                             |


![ovos-bigscreen](https://github.com/OpenVoiceOS/ovos-plasma-bigscreen/assets/33701864/afcc5e15-146b-4f38-be8d-0e5a56acaa55)

> OVOS on Smart TVs, powered by Plasma Bigscreen

## Desktop Applications

GUI clients are allowed to filter a `namespace`, essentially simulating a GUI skill in it's own dedicated window. this
is what powers [Plasma Bigscreen Voice Apps](https://plasma-bigscreen.org/docs/develop/) via .desktop files

Desktop files are also parsed to populate the skills in the [homescreen](https://github.com/OpenVoiceOS/skill-ovos-homescreen) app drawer

> **NOTE**: be sure to have [mycroft-gui-qt5](https://github.com/OpenVoiceOS/mycroft-gui-qt5) installed

```
[Desktop Entry]
X-DBUS-StartupType=None
X-KDE-StartupNotify=false
Version=1.0
Terminal=false
Type=Application
Name=OCP
Exec=ovos-gui-app --hideTextInput --skill=ovos.common_play
Icon=OCP
Categories=VoiceApp
StartupNotify=false
```

> Learn more about the [Desktop Entry Specification](https://specifications.freedesktop.org/desktop-entry-spec/desktop-entry-spec-latest.html)

## GUIInterface

Any component wanting to implement a GUI for OpenVoiceOS can do so via the `GUIInterface` class
from [ovos-bus-client](https://github.com/OpenVoiceOS/ovos-bus-client/blob/dev/ovos_bus_client/apis/gui.py)

Sending custom pages from skills requires skill to explicitly support a client platform

```python
class GUIInterface:
    """
    Interface to the Graphical User Interface, allows interaction with
    the mycroft-gui from anywhere

    Values set in this class are synced to the GUI, accessible within QML
    via the built-in sessionData mechanism.  For example, in Python you can
    write in a skill:
        self.gui['temp'] = 33
        self.gui.show_page('Weather')
        
    Then in the Weather.qml you'd access the temp via code such as:
        text: sessionData.time
    """
```

in OVOS Skills `self.gui` provides a `GUIInterface`  under `self.skill_id` namespace
