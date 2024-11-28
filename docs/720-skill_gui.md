# GUIInterface

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

## Page Templates

To have a unified look and feel, and to allow simple UIs to be integrated into skills without UI framework knowledge, the GUIInterface provides page templates

A page template is a ui file, like QML or html, that is used by gui clients to render the info provided by `ovos-gui`.

Skills may provide their own pages, for example for [QT Voice Apps](https://openvoiceos.github.io/ovos-technical-manual/qt_apps/), but is their responsibility to explicitly support individual gui client apps if not using a provided template


#### Text

Display simple strings of text.

```python
self.gui.show_text(self, text, title=None, override_idle=None, override_animations=False)
```

Arguments:

* text \(str\): Main text content.  It will auto-paginate
* title \(str\): A title to display above the text content.
* override\_idle \(boolean, int\):
  * True: Takes over the resting page indefinitely
  * \(int\): Delays resting page for the specified number of seconds.
* override\_animations \(boolean\):
  * True: Disables showing all platform skill animations.
  * False: 'Default' always show animations.

#### Static Image

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

#### Animated Image

Display an animated image such as a gif.

```python
self.gui.show_animated_image(self, url, caption=None, title=None, fill=None, override_idle=None, override_animations=False)
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

#### HTML Page

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

#### Remote URL

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

