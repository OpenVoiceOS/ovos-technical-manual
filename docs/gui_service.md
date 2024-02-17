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

The active namespace is tracked by `ovos-gui` and manages
the [homescreen skill](https://github.com/OpenVoiceOS/skill-ovos-homescreen), desktop implementations like Plasma
Bigscreen do not have a homescreen, instead they manage each skill in their own window

A single GUI plugin can be loaded in `ovos-gui` to handle bus events and provide companion python code to GUI clients,
this is usually done by your OS and specific to a use case.

| plugin                                                                                            | description                                                                                              | notes                                                                                                                                 |
|---------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|
| [ovos-gui-plugin-shell-companion](https://github.com/OpenVoiceOS/ovos-gui-plugin-shell-companion) | extra functionality for ovos-shell                                                                       |                                                                                                                                       |
| [ovos-gui-plugin-bigscreen](https://github.com/OVOSHatchery/ovos-gui-plugin-bigscreen)            | window management for [Plasma Bigscreen](https://invent.kde.org/plasma/plasma-bigscreen) [Voice Apps](https://plasma-bigscreen.org/docs/develop/)  | **UNMAINTED** <br> backup fork in life support [ovos-plasma-bigscreen-qt5](https://github.com/OVOSHatchery/ovos-plasma-bigscreen-qt5) |
| [ovos-gui-plugin-plasmoid](https://github.com/OVOSHatchery/ovos-gui-plugin-plasmoid)              | [Plasma integration of different Mycroft AI services](https://invent.kde.org/utilities/mycroft-plasmoid) | **UNMAINTED** <br> never migrated to OVOS                                                                                             |

GUI clients are allowed to filter a `namespace`, essentially simulating a GUI skill in it's own dedicated window. this
is what powers [Plasma Bigscreen Voice Apps](https://plasma-bigscreen.org/docs/develop/) via .desktop files

![ovos-bigscreen](https://github.com/OpenVoiceOS/ovos-plasma-bigscreen/assets/33701864/afcc5e15-146b-4f38-be8d-0e5a56acaa55)


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

### Text

Display simple strings of text.

```python
self.gui.show_text(self, text, title=None, override_idle=None, override_animations=False)
```

Arguments:

* text \(str\): Main text content. It will auto-paginate
* title \(str\): A title to display above the text content.
* override\_idle \(boolean, int\):
    * True: Takes over the resting page indefinitely
    * \(int\): Delays resting page for the specified number of seconds.
* override\_animations \(boolean\):
    * True: Disables showing all platform skill animations.
    * False: 'Default' always show animations.

### Static Image

Display a static image such as a jpeg or png.

```python
self.gui.show_image(self, url, caption=None, title=None, fill=None, override_idle=None, override_animations=False)
```

Arguments:

* url \(str\): Pointer to the image
* caption \(str\): A caption to show under the image
* title \(str\): A title to display above the image content
* fill \(str\): Fill type - supports:
    * 'PreserveAspectFit',
    * 'PreserveAspectCrop',
    * 'Stretch'
* override\_idle \(boolean, int\):
    * True: Takes over the resting page indefinitely
    * \(int\): Delays resting page for the specified number of seconds.
* override\_animations \(boolean\):
    * True: Disables showing all platform skill animations.
    * False: 'Default' always show animations.

### Animated Image

Display an animated image such as a gif.

```python
self.gui.show_animated_image(self, url, caption=None, title=None, fill=None, override_idle=None,
                             override_animations=False)
```

Arguments:

* url \(str\): Pointer to the .gif image
* caption \(str\): A caption to show under the image
* title \(str\): A title to display above the image content
* fill \(str\): Fill type - supports:
    * 'PreserveAspectFit',
    * 'PreserveAspectCrop',
    * 'Stretch'
* override\_idle \(boolean, int\):
    * True: Takes over the resting page indefinitely
    * \(int\): Delays resting page for the specified number of seconds.
* override\_animations \(boolean\):
    * True: Disables showing all platform skill animations.
    * False: 'Default' always show animations.

### HTML Page

Display a local HTML page.

```python
self.gui.show_html(self, html, resource_url=None, override_idle=None, override_animations=False)
```

Arguments:

* html \(str\): HTML text to display
* resource\_url \(str\): Pointer to HTML resources
* override\_idle \(boolean, int\):
    * True: Takes over the resting page indefinitely
    * \(int\): Delays resting page for the specified number of seconds.
* override\_animations \(boolean\):
    * True: Disables showing all platform skill animations.
    * False: 'Default' always show animations.

### Remote URL

Display a webpage.

```python
self.gui.show_url(self, url, override_idle=None, override_animations=False)
```

Arguments:

* url \(str\): URL to render
* override\_idle \(boolean, int\):
    * True: Takes over the resting page indefinitely
    * \(int\): Delays resting page for the specified number of seconds.
* override\_animations \(boolean\):
    * True: Disables showing all platform skill animations.
    * False: 'Default' always show animations.
