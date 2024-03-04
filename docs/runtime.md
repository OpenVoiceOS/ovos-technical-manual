# Runtime Requirements



## ready_settings

The `ready_settings` configuration is part of the `SkillManager` and determines when the `'mycroft.ready'` message is
emitted, indicating that OVOS is ready to operate.

- **'skills':**
    - Waits for the all offline skills to be loaded

- **'network_skills':**
    - Waits for the `'mycroft.network.connected'` message  

- **'internet_skills':**
    - Waits for the `'mycroft.internet.connected'` message  

- **'setup':**
    - Waits for an external setup skill to handle actions such as pairing and configuration. 

- **'audio':**
    - Waits for the audio stack to be initialized 

- **'speech':**
    - Waits for the speech engine (STT) to be initialized  

```json
{
  "ready_settings": [
    "skills",
    "network_skills",
    "internet_skills",
    "audio",
    "speech"
  ]
}
```

> **WARNING** The default behavior is to wait only for offline skills at the start. This is a departure from mycroft-core, which did not have dynamic skills. It may impact skills listening for the `mycroft.ready` event. 

In this example, the `ready_settings` are configured to wait for network and internet connectivity before emitting
the `'mycroft.ready'` message. Each setup can costumize these settings based on their needs, a offline install won't
want internet skills, a server wont want a audio stack etc.

## Dynamic Loading and Unloading

**NEW** in `ovos-core` version **0.0.8**

**Optimizing Resource Usage**

OVOS introduces dynamic loading and unloading of skills, streamlining resource management. Skill developers no longer need to perform connectivity checks during the skill loading process.

Skills are loaded only when their specific requirements are met. This optimization prevents unnecessary loading, conserving system resources and ensuring a more efficient skill environment.

**Reducing Unintended Activations**

Dynamic unloading of skills based on specific conditions significantly reduces the chance of unintentional activations. In scenarios where required conditions are not met, skills are gracefully unloaded, enhancing the user experience by avoiding unintended skill triggers.

This approach aligns with resource-conscious design, providing a more responsive and reliable voice assistant environment. Developers can focus on skill functionality, knowing that OVOS efficiently manages skill loading and unloading based on runtime requirements.

## The RuntimeRequirements @classproperty

**NEW** in `ovos-core` version **0.0.8**

> **WARNING** this functionality deprecates `"priority_skills"` config option

The `RuntimeRequirements` property is a class property that skill developers can utilize to define the conditions and
resource requirements of their skill during initialization and runtime. This property is a part of the `OVOSSkill` class
and is used to guide the loading and unloading of skills based on specified criteria.

To use `RuntimeRequirements`, skill developers need to override the property within their skill class.

It's important to note that `RuntimeRequirements` is optional. If not defined, OVOS will assume internet is required but GUI is optional. This default behavior reflects the original behavior of `mycroft-core` and **might change in the future**.

> **NOTE:** The `@classproperty` decorator allows `SkillManager` to read this without instantiating the skill.

These examples showcase how `RuntimeRequirements` can be used to tailor the loading and runtime conditions of a skill
based on specific requirements and dependencies.

### Fully Offline Skill

In this example, a fully offline skill is defined. The skill does not require internet or network connectivity during
loading or runtime. If the network or internet is unavailable, the skill can still operate.

Defining this will ensure your skill loads as soon as possible; otherwise, the `SkillManager` will wait for internet before loading the skill.

```python
from ovos_utils import classproperty
from ovos_workshop.skills import OVOSSkill


class MyOfflineSkill(OVOSSkill):

    @classproperty
    def runtime_requirements(self):
        return RuntimeRequirements(internet_before_load=False,
                                   network_before_load=False,
                                   requires_internet=False,
                                   requires_network=False,
                                   no_internet_fallback=True,
                                   no_network_fallback=True)
```

### Internet-Dependent Skill

In this example, an online search skill with a local cache is defined. The skill requires internet connectivity during
both loading and runtime. If the internet is not available, the skill won't load. Once loaded, the skill continues to
require internet connectivity.

However, our skill keeps a cache of previous results; therefore, it declares it can handle internet outages and will not
be unloaded when the internet goes down.

```python
from ovos_utils import classproperty
from ovos_workshop.skills import OVOSSkill


class MyInternetSkill(OVOSSkill):

    @classproperty
    def runtime_requirements(self):
        # our skill can answer cached results when the internet goes down
        return RuntimeRequirements(
            internet_before_load=True,  # only load once we have internet
            requires_internet=True,  # indicate we need internet to work
            no_internet_fallback=True  # do NOT unload if internet goes down
        )

    def initialize(self):
        ...  # do something that requires internet connectivity
```

### IOT Skill Controlling Devices via LAN

Consider a skill that should only load once we have a network connection.
By specifying that requirement, we can ensure that the skill is only loaded once the requirements are met, and it is
safe to utilize network resources on initialization.

In this example, an IOT skill controlling devices via LAN is defined. The skill requires network connectivity during
loading, and if the network is not available, it won't load.

Once loaded, the skill continues to require network connectivity and will unload if the network is lost.

```python
from ovos_utils import classproperty
from ovos_workshop.skills import OVOSSkill


class MyIOTSkill(OVOSSkill):

    @classproperty
    def runtime_requirements(self):
        return RuntimeRequirements(
            network_before_load=True,  # only load once network available
            requires_network=True,  # we need network to work
            no_network_fallback=False  # unload if network goes down
        )

    def initialize(self):
        ...  # do something that needs LAN connectivity
```

### GUI and Internet-Dependent Skill with Fallback

Consider a skill with both graphical user interface (GUI) and internet dependencies is defined.

The skill requires both GUI availability and internet connectivity during loading.

If either the GUI or the internet is not available, the skill won't load. Once loaded, the skill continues to require
both GUI availability, but internet connectivity is optional.

If the user asks "show me the picture of the day" and we have both internet and a GUI, our skill will match the intent.

If we do not have internet but have a GUI, the skill will still operate, using a cached picture. If no GUI is available then the skill will unload regardless of internet status

```python
from ovos_utils import classproperty
from ovos_workshop.skills import OVOSSkill


class MyGUIAndInternetSkill(OVOSSkill):

    @classproperty
    def runtime_requirements(self) -> RuntimeRequirements:
        return RuntimeRequirements(
            gui_before_load=True,  # only load if GUI is available
            requires_gui=True,  # continue requiring GUI once loaded
            internet_before_load=True,  # only load if internet is available
            requires_internet=True,  # continue requiring internet once loaded
            no_gui_fallback=False,  # unload if GUI becomes unavailable
            no_internet_fallback=True  # do NOT unload if internet becomes unavailable, use cached picture
        )

    def initialize(self):
        ...  # do something that requires both GUI and internet connectivity
```
