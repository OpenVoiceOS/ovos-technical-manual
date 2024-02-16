# GUI Service

OVOS devices with displays provide skill developers the opportunity to create skills that can be empowered by both voice and screen interaction. 

`ovos-gui`, aka, The GUI Service, is responsible for keeping track of what should be rendered, but does not perform the rendering itself

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

The GUI state is defined by `namespaces`, usually corresponding to a `skill_id`, each with any number of `pages`. `pages` are ordered, but only 1 page is rendered at a time.

OpenVoiceOS components interact with the GUI by defining session data and active pages, gui-clients may also send back `events` to indicate interactions

The GUI clients may be implemented in any language, page templates are provided to skills via `self.gui.show_XXX` that should be implemented by all clients. 

sending custom pages from skills requires skill to explicitly support a client platform

## GUI Plugins

The active namespace is tracked by `ovos-gui` and manages the [homescreen skill](https://github.com/OpenVoiceOS/skill-ovos-homescreen), desktop implementations like Plasma Bigscreen do not have a homescreen, instead they manage each skill in their own window

GUI clients are allowed to filter a `namespace`, essentially simulating a GUI skill in it's own dedicated window. this is what powers plasma bigscreen Voice Apps via .desktop files

A single GUI plugin can be loaded in `ovos-gui` to handle bus events and provide companion python code to GUI clients, this is usually done by your OS and specific to a use case. 

Currently the only existing plugin is for [ovos-shell](https://openvoiceos.github.io/ovos-technical-manual/shell/)

## Introduction to QML

Currently only a [QT5 gui-client](https://github.com/OpenVoiceOS/mycroft-gui-qt5) is available, help wanted to [migrate to QT6](https://github.com/OVOSHatchery/mycroft-gui-qt6)!

The reference GUI client implementation is based on the QML user interface markup language that gives you complete freedom to create in-depth innovative interactions without boundaries or provide you with simple templates within the GUI framework that allow minimalistic display of text and images based on your skill development specifics and preferences.

QML user interface markup language is a declarative language built on top of Qt's existing strengths designed to describe the user interface of a program: both what it looks like, and how it behaves. QML provides modules that consist of sophisticated set of graphical and behavioral building elements.

A collection of resources to familiarize you with QML and Kirigami Framework.

* [Introduction to QML ](http://doc.qt.io/qt-5/qml-tutorial.html)
* [Introduction to Kirigami](https://www.kde.org/products/kirigami/)
* [Original Mycroft GUI Docs](https://mycroft-ai.gitbook.io/docs/skill-development/displaying-information/mycroft-gui)
