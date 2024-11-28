# Mark1 Utils

small library to interact with a [Mycroft Mark1 faceplate via the messagebus](https://openvoiceos.github.io/message_spec/phal_mk1/)

gives you full control of the faceplate and eyes pixel by pixel

![](./demo/86788719_10157996735085818_3139665318654246912_n.jpg)

## Animate the eyes

```python
from ovos_mark1.eyes import Eyes
from ovos_bus_client.utils import get_mycroft_bus

bus = get_mycroft_bus("0.0.0.0")

eyes = Eyes(bus)
eyes.hue_spin()
```

## Faceplate Icons

```python
from ovos_mark1.faceplate import BlackScreen

class MusicIcon(BlackScreen):
    str_grid = """
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
XXXXXXXXXXXXXX     XXXXXXXXXXXXX
XXXXXXXXXXXXXX     XXXXXXXXXXXXX
XXXXXXXXXXXXXX XXX XXXXXXXXXXXXX
XXXXXXXXXXXXXX XXX XXXXXXXXXXXXX
XXXXXXXXXXXXX  XX  XXXXXXXXXXXXX
XXXXXXXXXXXX   X   XXXXXXXXXXXXX
XXXXXXXXXXXXX XXX XXXXXXXXXXXXXX
"""
    
icon = MusicIcon()
icon.print()  # show in terminal
icon.display()  # show in mark1
```

## Faceplate Animations

```python

# it's snowing !
class FallingDots(FacePlateAnimation):
    def __init__(self, n=10, bus=None):
        super().__init__(bus=bus)
        self._create = True
        assert 0 < n < 32
        self.n = n

    @property
    def n_dots(self):
        n = 0
        for y in range(self.height):
            for x in range(self.width):
                if self.grid[y][x]:
                    n += 1
        return n

    def animate(self):
        self.move_down()
        if self._create:
            if random.choice([True, False]):
                self._create = False
                x = random.randint(0, self.width - 1)
                self.grid[0][x] = 1
        if self.n_dots < self.n:
            self._create = True
```

Pre made animations

```python
from ovos_mark1.faceplate.animations import ParticleBox
from ovos_bus_client.utils import get_mycroft_bus
from time import sleep

bus = get_mycroft_bus("0.0.0.0")

for faceplate in ParticleBox(bus=bus):
    faceplate.display(invert=False)
    sleep(0.5)

    
from ovos_mark1.faceplate.cellular_automaton import Rule110

a = Rule110(bus=bus)

for grid in a:
    grid.print()  # animate in terminal
    grid.display(invert=False)
    sleep(0.5)
```