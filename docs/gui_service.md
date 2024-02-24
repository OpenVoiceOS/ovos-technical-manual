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

The GUI clients may be implemented in any programming language, the page templates provided to skills via `self.gui.show_XXX` should be implemented and provided by all alternative clients.

![imagem](https://github.com/OpenVoiceOS/ovos-technical-manual/assets/33701864/69c653dc-9bad-4a3a-bd43-efefb938f650)

> **NOTE**: GUI does not yet support Session, in the future namespaces will be tracked per Session allowing remote clients to each have their own GUI state


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

