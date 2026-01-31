# GUI Service

OVOS devices with displays provide skill developers the opportunity to create skills that can be empowered by both voice
and screen interaction.

`ovos-gui`, aka, The GUI Service, is responsible for keeping track of what should be rendered, but does not perform the
rendering itself

The GUI service provides a websocket for gui clients to connect to, it is responsible for implementing the gui protocol
under `ovos-core`.

You can find indepth documentation of [the GUI protocol](https://openvoiceos.github.io/ovos-technical-manual/gui_protocol) in the dedicated GUI section of these docs


## Architecture

The GUI state is defined by `namespaces`, usually corresponding to a `skill_id`, each with any number
of `pages`. 

users are expected to be able to "swipe left" and "swipe right" to switch between `pages` within a `namespace`

OpenVoiceOS components interact with the GUI by defining session data and active pages, gui-clients may also send back `events` to indicate interactions.

![imagem](https://github.com/OpenVoiceOS/ovos-technical-manual/assets/33701864/69c653dc-9bad-4a3a-bd43-efefb938f650)

`pages` are ordered and, usually, only 1 `page` is rendered at a time. 

If the screen size allows it platform specific gui client applications are free to render all `pages` into view.

The GUI clients may be implemented in any programming language, the default page templates provided to skills via [GUIInterface](https://openvoiceos.github.io/ovos-technical-manual/skill_gui) should be implemented and provided by all alternative clients.

## Active Namespaces

In the context of a smartspeaker, when the GUI is idle a `homescreen` may be displayed, eg. an animated face or clock

![imagem](https://github.com/OpenVoiceOS/ovos-technical-manual/assets/33701864/25a2725a-271b-469d-822a-148b4fdfa30e)

Whenever a `page` is displayed by a skill, `ovos-gui` tracks it and sets it's `namespace` to active, then tells the gui clients to render it.

The active `namespace` and how long a page stays up are managed by `ovos-gui`, usually via platform specific plugins. 

`ovos-gui` will decide when a `namespace` is no longer active, and then the next `namespace` will be rendered, 

Skills using the [GUIInterface](https://openvoiceos.github.io/ovos-technical-manual/skill_gui/) can indicate how long they want a page to remain active

Example: 

- OVOS is idle - homescreen is the active `namespace`

- you ask OVOS to play music and the music page shows up - music player page is the active `namespace`

- you ask OVOS a question and wolfram alpha page shows up - wolfram page is the active `namespace`

- wolfram alpha times out - music player page is the active `namespace`

- music ends and page times out - homescreen is the active `namespace`

> **NOTE**: GUI does not yet support Session, in the future namespaces will be tracked per Session allowing remote clients to each have their own GUI state


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
