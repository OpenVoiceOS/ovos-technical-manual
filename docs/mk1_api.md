# Enclosure Api

The EnclosureApi is an abstraction over an hypothetical "body" housing OVOS

eg, The Mark 1 **Device** is housed in an **Enclosure**. The Enclosure is the shell that houses a **Device** that runs OVOS.

```python
from ovos_bus_client.apis.enclosure import EnclosureApi

api = EnclosureApi(bus)
```
## The Mark 1 Enclosure capabilities

The Mark 1 _mouth_ and _eyes_ can be controlled by **Skills** using the `self.enclosure` object inherited from the `OVOSSkill` base class. 
This object acts as an interface to the **Enclosure** and allows the Skill creator to draw to the _mouth_ display.

This is how the _mouth_ and _eyes_ are made to change during operations such as audio playback.

Dedicated utils for fine grained control over the mark 1 can be found at [ovos-mark1-utils](https://github.com/OpenVoiceOS/ovos-mark1-utils)

## Drawing to the _mouth_ display

### Drawing text to the _mouth_ display

Text can be sent to the display using the `mouth_text()` method of the `enclosure` object.

```python
self.enclosure.mouth_text('The meaning of life, the universe and everything is 42')
```

If the text is too long to fit on the display, the text will scroll.

@TODO how many characters will fit on the display before it will scroll?

### Drawing images to the _mouth_ display

**Clearing an existing image from the mouth display**

Before writing an image to the _mouth_ display, you should clear any previous image.

```python
self.enclosure.mouth_display(img_code="HIAAAAAAAAAAAAAA", refresh=False)
self.enclosure.mouth_display(img_code="HIAAAAAAAAAAAAAA", x=24, refresh=False)
```

### How images are drawn on the _mouth_ display

The _mouth_ display is a grid, 32 pixels wide and 8 pixels high.

There are two ways to draw an image on the _mouth_ display.

**Addressing each pixel using a string encoding**

You can draw an image to the _mouth_ display by binary encoding pixel information in a string.

The binary encoding is straightforward value substitution.

| Letter Value | Pixel value |
| :--- | :--- |
| A | 0 |
| B | 1 |
| C | 2 |
| D | 3 |
| E | 4 |
| F | 5 |
| G | 6 |
| H | 7 |
| I | 8 |

and so on.

```python
self.enclosure.mouth_display(img_code="HIAAAAAAAAAAAAAA", refresh=False)
self.enclosure.mouth_display(img_code="HIAAAAAAAAAAAAAA", x=24, refresh=False)
```

The code above clears the image by sending a string consisting of `HI` which stands for a Width of 7 and a height of 8 and each A stands for a segment of 4 pixels in the off state.

@TODO we really need a grid image here to show how it works - to make it easier to understand.

**Sending a PNG image to the mouth display**

Another way to draw an image on the _mouth_ display is to create a PNG-formatted image with a width of 32 pixels and a height of 8 pixels, then use the `mouth_display_png()` method of the `enclosure` object.

The image should be black and white, with white meaning a dark pixel, and black indicating an illuminated pixel.

`mouth_display_png()` expects the first argument to be the image absolute path. Optional arguments are

* `threshold`: The value at which a pixel should be considered 'dark' or 'illuminated'
* `invert`: Treat white in the image as illuminated pixels, and black as dark pixels
* `x`: The x position \(horizontal\) at which the image should be displaye, in pixels
* `y`: The y position \(vertical\) at which the image should be displayed, in pixels
* `refresh`: clear the display before writing to it

@TODO all the above needs to be validated - the information is educated guesswork

```python
self.mouth_display_png('/path/to/image.png', threshold=70, invert=False, x=0, y=0, refresh=True)
```

Example image: ![A note symbol](https://akeiexil.files.wordpress.com/2017/08/note1.png)

**Tools for converting PNG image representations to string representations**

If you don't want to convert PNG files at runtime \(for example when creating simple animations\) [this short python script](https://gist.github.com/forslund/9d8805fd7adb9e74ec3ea321e1676a21) will convert PNG files to strings compatible with the `img_code` of `self.enclosure.mouth_display()`.

### Resetting the display to the default state

When the **Skill** is finished, you should reset the **Enclosure** to the default state using

```python
self.enclosure.reset()
```

This will clear the screen and blink the Mark 1's _eyes_ once.
