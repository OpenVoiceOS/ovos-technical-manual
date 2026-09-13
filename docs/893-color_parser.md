# OVOS Color Parser

> :warning: this package is a work in progress

## What does this have to do with voice?

- "change the lamp color to moss green"
- "make it darker"
- "more saturated"
- "a bit more yellowish"
- "perfect"

> NOTE: physicists are huge nerds, so they might say something like "change the lamp wave lenght to X nanometers", this
> is a terrible way to talk about color and innacurate but we also added basic support for this



## Extracting a color from text

The parser will do it's best to parse "color modifiers"

```python
from ovos_color_parser import color_from_description

names = [
    "Bright, vibrant green",
    "Pale pink",
    "Muted, warm gray",
    "Dark, cool blue",
  
]
for n in names:
    c = color_from_description(n)
    print(c.hex_str)
    print(c)
```

![image](https://github.com/user-attachments/assets/96601212-01d2-4eda-a7e2-120f2d7de5f5)


Color names are ambiguous, the same name sometimes refers to multiple colors. When a color is matched by the parser it "averages all matched colors"
```python
from ovos_color_parser import color_from_description

color = color_from_description("Red")
print(color.hex_str)  #D21B1B
print(color) 
# sRGBColor(r=210, g=27, b=27, name='Red', description='Red')
```
![image](https://github.com/user-attachments/assets/b54b5452-36a1-4dd8-8e3e-49dfacb57a97)



We can tell the parser to always return a known/named color with `cast_to_palette=True`, but this might not always return what you expect
```python
from ovos_color_parser import color_from_description

color = color_from_description("Red", cast_to_palette=True)
print(color.hex_str)  #CE202B
print(color)
# sRGBColor(r=206, g=32, b=43, name='Fire engine red', description='Red')
```
![image](https://github.com/user-attachments/assets/ac3cc89d-2949-4d8e-ae70-e9829402d7b7)


### Beware of impossible colors

Some colors are [impossible](https://en.wikipedia.org/wiki/Impossible_color), but that doesn't stop text from describing them

`"Reddish-green"` doesn’t make much sense as a description, unless you mean yellow or orange, which you don’t, because you would have said “yellow” or “orange”. The same applies to `"Yellowish–blue"`

> the Colour of Magic or the King Colour, was the eighth colour of the Discworld spectrum. 
Only visible to wizards and cats. It is described in "The Colour of Magic" as the colour of imagination and is a fluorescent greenish yellow-purple. 
The only time non-wizards can see it is when they close their eyes; the bursts of color are octarine.


Fluorescent greenish-yellow and purple are essentially opposite colors on the color wheel, with wavelengths that can’t coexist in a single light wave in the visible spectrum. Here’s why:

- Color Wavelengths and Light: Greenish-yellow light falls in a wavelength range of about 560–590 nanometers, while purple is not a pure spectral color but a combination of blue (around 450–495 nm) and red (around 620–750 nm). Human eyes perceive purple as a combination of these two ends of the spectrum.
- Color Opponency Theory: The human visual system relies on color opponency, where certain pairs of colors (like red-green and blue-yellow) are processed in opposing channels. Because of this, our brains can’t interpret colors that simultaneously activate both ends of an opponent channel. This is why we don’t perceive colors like reddish-green or yellowish-blue—our brains are simply wired to cancel out those combinations.
- Perceptual Limits: Fluorescent colors are especially intense because they emit light in a narrow, concentrated wavelength range, making them appear very saturated and bright. Attempting to mix fluorescent greenish-yellow with purple not only challenges the physiology of the eye but would also result in a muted brown or gray tone, as the colors cancel each other out.

In short, fluorescent greenish-yellow and purple light can’t coexist in a way our eyes can interpret as a single, stable color because of the biological limits of human color perception.


```python
from ovos_color_parser import color_from_description

# look! an impossible color
color = color_from_description("fluorescent greenish-yellow purple")
color.name = "Octarine"
print(color.hex_str) #76B11D
print(color)
# sRGBColor(r=118, g=177, b=29, name='Octarine', description='fluorescent greenish-yellow purple')
```
the parser will gladly output something... it just might not make sense

in this case the parser focused on `"greenish-yellow"`

![image](https://github.com/user-attachments/assets/82484998-3f19-4626-bcea-e6c570a90dc9)

but it could have focused on `"purple"`

![image](https://github.com/user-attachments/assets/4aee840a-ccb8-4a63-ad84-bf0d283a49c1)


## Comparing color objects

compare color distances (smaller is better)

```python
from ovos_color_parser import color_distance, color_from_description

color_a = color_from_description("green")
color_b = color_from_description("purple")
print(color_distance(color_a, color_b))
# 64.97192890677195

color_a = color_from_description("green")
color_b = color_from_description("yellow")
print(color_distance(color_a, color_b))
# 44.557493285361

color_a = color_from_description("yellow")
color_b = color_from_description("purple")
print(color_distance(color_a, color_b))
# 78.08287998809946
```

match a color object to a list of colors

```python
from ovos_color_parser import sRGBAColor, sRGBAColorPalette, closest_color

# https://en.wikipedia.org/wiki/Blue-green
BlueGreenPalette = sRGBAColorPalette(colors=[
  sRGBAColor(r=0, g=128, b=128, name="Blue-green"),
  sRGBAColor(r=0, g=255, b=255, name="Cyan (Aqua)", description="Brilliant bluish green"),
  sRGBAColor(r=64, g=224, b=208, name="Turquoise", description="Brilliant bluish green"),
  sRGBAColor(r=17, g=100, b=180, name="Green-blue", description="Strong blue"),
  sRGBAColor(r=57, g=55, b=223, name="Bondi blue"),
  sRGBAColor(r=0, g=165, b=156, name="Blue green (Munsell)", description="Brilliant bluish green"),
  sRGBAColor(r=0, g=123, b=167, name="Cerulean", description="Strong greenish blue"),
  sRGBAColor(r=0, g=63, b=255, name="Cerulean (RGB)", description="Vivid blue"),
  sRGBAColor(r=0, g=128, b=128, name="Teal", description="Moderate bluish green"),
])

print(closest_color(sRGBAColor(r=0, g=0, b=255, name="Blue"),
                    BlueGreenPalette.colors))
# sRGBColor(r=0, g=63, b=255, name='Cerulean (RGB)', description='Vivid blue')
print(closest_color(sRGBAColor(r=0, g=255, b=0, name="Green"),
                    BlueGreenPalette.colors))
# sRGBColor(r=64, g=224, b=208, name='Turquoise', description='Brilliant bluish green')
```

---

## Language support

When describing color in natural language to approximate it in RGB, there are several keywords that can convey
its properties effectively

```python
# Parse complex color descriptions
color = color_from_description("very bright, slightly warm muted blue")
```

- **Description**: Hue refers to the basic color family, such as red, blue, green, or yellow.
- **Translation to RGB**:
- The hue determines which of the primary RGB channels (red, green, or blue) will be most prominent. For example,
  “red” means a strong red channel with low green and blue, while “blue” means a high blue channel with low red and
  green.
- Hues like "yellow" indicate both red and green channels are high with blue low, while "purple" combines red and
  blue with little green.
  
---

- **Description**: Saturation, or chroma, is how pure or intense the color is. Terms like “vibrant,” “dull,” or “washed
  out” refer to saturation.
- **Translation to RGB**:
    - High saturation (vibrant): Increase the difference between the dominant channel(s) and others. For example, making
      the red channel much higher than green and blue for a vibrant red.
    - Low saturation (dull): Reduce the contrast between channels, creating a blend closer to grayscale. For instance,
      balancing red, green, and blue channels to similar values lowers saturation.

---


- **Description**: Brightness refers to how light or dark the color appears. Words like “bright,” “dim,” “dark,” or
  “pale” are often used.
- **Translation to RGB**:
    - High brightness (bright): Increase the values across all channels.
    - Low brightness (dark): Decrease values across channels while maintaining the hue's relative balance.

---

- **Description**: Color temperature reflects whether a color feels warm or cool. Terms like "warm red," "cool green,"
  or "cold blue" apply here.
- **Translation to RGB**:
    - Warm colors: Increase red or red and green channels.
    - Cool colors: Increase blue or decrease red.

---

- **Description**: Opacity doesn’t affect RGB but is relevant for color perception, especially in design. Terms like
  “translucent,” “opaque,” or “sheer” describe it.
- **Translation to RGB**:
    - Opacity affects the alpha channel (RGBA) rather than RGB values.



This approach, while interpretative, offers a structured way to translate natural language color descriptions into RGB
approximations.

#### Color Keywords 


To categorize adjectives and keywords that describe color in ways that translate into RGB or color space adjustments the
parser uses a `.json` file per language

Example JSON structure for English color keywords:
 
```json
{
  "saturation": {
    "high": ["vibrant", "rich", "bold", "deep"],
    "low": ["dull", "muted", "washed-out", "faded"]
  },
  "brightness": {
    "high": ["bright", "light", "pale", "glowing"],
    "low": ["dim", "dark", "shadowy", "faint"]
  }
}
```

Color name lists in each language are also used to determine the **hue**. 

> English has a word list of almost ~6000 color name mappings


Below are some examples of non-color-name keywords that define other qualities of a color


- **Very High Saturation**: For colors that are extremely intense or vivid.
    - Keywords: “neon,” “saturated,” “intense,” “brilliant,” “flamboyant”
- **High Saturation**: These adjectives indicate vibrant or intense colors where the hue is pronounced.
    - Keywords: “vibrant,” “rich,” “bold,” “deep,” “vivid,” “intense,” “pure,” “electric”
- **Low Saturation**: These adjectives imply a muted or washed-out appearance, often making the color appear closer to
  grayscale.
    - Keywords: “dull,” “muted,” “washed-out,” “faded,” “soft,” “pale,” “subdued,” “pastel”
- **Very Low Saturation**: For colors that are very desaturated, nearing grayscale.
    - Keywords: “drab,” “grayed,” “washed-out,” “faded,” “subdued”

---

- **Very High Brightness**: Extremely bright colors, often implying high lightness or near-whiteness.
    - Keywords: “blinding,” “radiant,” “glowing,” “white,” “light-filled”
- **High Brightness**: Bright colors, often indicating a lighter shade or close to white.
    - Keywords: “bright,” “light,” “pale,” “glowing,” “luminous,” “brilliant,” “clear,” “radiant”
- **Low Brightness**: These terms describe darker or dimmer shades, closer to black.
    - Keywords: “dim,” “dark,” “shadowy,” “faint,” “gloomy,” “subdued,” “deep,” “midnight”
- **Very Low Brightness**: Colors that are nearly black or very dark.
    - Keywords: “pitch-dark,” “black,” “shadowed,” “deep,” “ink-like”

---

- **Very High Temperature (Very Warm)**: Intense warm colors, strongly leaning toward red, orange, or intense yellow.
    - Keywords: “fiery,” “lava-like,” “burning,” “blazing”
- **High Temperature (Warm Colors)**: Warmer colors suggest a shift towards red or yellow tones, giving the color a
  warmer feel.
    - Keywords: “warm,” “hot,” “fiery,” “sunny,” “toasty,” “scorching,” “amber,” “reddish”
- **Low Temperature (Cool Colors)**: Cooler colors involve blue or green tones, giving the color a cooler or icy
  appearance.
    - Keywords: “cool,” “cold,” “chilly,” “icy,” “frosty,” “crisp,” “bluish,” “aqua”
- **Very Low Temperature (Very Cool)**: Extremely cool tones, verging on cold, icy blues or greens.
    - Keywords: “icy,” “arctic,” “frigid,” “wintry,” “glacial”

---

- **Very High Opacity**: Extremely solid or dense colors.
    - Keywords: “impenetrable,” “opaque,” “thick”
- **High Opacity**: Describes solid colors without transparency.
    - Keywords: “opaque,” “solid,” “dense,” “thick,” “cloudy,” “impenetrable,” “strong”
- **Low Opacity**: Indicates transparency or translucency, where the background may show through.
    - Keywords: “transparent,” “translucent,” “sheer,” “see-through,” “misty,” “delicate,” “airy”
- **Very Low Opacity**: Highly transparent or barely visible colors.
    - Keywords: “ethereal,” “ghostly,” “barely-there,” “translucent”


## Related Projects

- [ovos-number-parser](https://github.com/OpenVoiceOS/ovos-number-parser) - for handling numbers
- [ovos-date-parser](https://github.com/OpenVoiceOS/ovos-date-parser) - for handling dates and times
- [ovos-lang-parser](https://github.com/OVOSHatchery/ovos-lang-parser) - for handling languages
