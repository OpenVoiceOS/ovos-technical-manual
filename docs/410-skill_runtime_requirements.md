# Runtime Requirements in OVOS

OVOS (Open Voice OS) introduces advanced runtime management to ensure skills are only loaded and active when the system is ready. This improves performance, avoids premature skill activation, and enables greater flexibility across different system setups (offline, headless, GUI-enabled, etc.).

This guide covers how to control when OVOS declares readiness, how dynamic skill loading works, and how developers can use `RuntimeRequirements` to specify resource dependencies for their skills.

---

## Usage Guide

### Step 1: Customize `ready_settings` (in your configuration)

You can specify what the system must wait for before emitting the `mycroft.ready` message:

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

In this example, the `ready_settings` are configured to wait for network and internet connectivity before emitting
the `'mycroft.ready'` message. Each setup can customize these settings based on their needs, a offline install won't
want internet skills, a server wont want a audio stack etc.

### Step 2: Define `RuntimeRequirements` in your skill

Use the `runtime_requirements` class property to control when and how your skill should load based on system resources like internet, network, or GUI.

Example:
```python
from ovos_utils import classproperty
from ovos_workshop.skills import OVOSSkill
from ovos_utils.process_utils import RuntimeRequirements

class MySkill(OVOSSkill):

    @classproperty
    def runtime_requirements(self):
        return RuntimeRequirements(
            requires_internet=True
        )
```

---

## Technical Explanation

### `ready_settings`

The `ready_settings` config controls when OVOS emits `mycroft.ready`, which signals that the system is ready for use. Each entry in this list waits for a different component:

- **"skills"** – Waits for offline skills to load.
- **"network_skills"** – Waits for the system to detect a network connection (`mycroft.network.connected`).
- **"internet_skills"** – Waits for an internet connection (`mycroft.internet.connected`).
- **"setup"** – Waits for an external setup process (e.g., pairing or configuration).
- **"audio"** – Waits for the audio playback and capture systems to be initialized.
- **"speech"** – Waits for the STT (speech-to-text) engine to be ready.
- **{skill_id}** - Waits for a specific skill to be available

> ⚠️ **Note**: By default, OVOS only waits for offline skills. Unlike Mycroft-core, OVOS supports dynamic loading, so timing can impact skills that depend on the `mycroft.ready` message.

---

## Dynamic Loading and Unloading

Introduced in `ovos-core 0.0.8`, dynamic skill management improves system performance and reliability by:

- **Only loading skills when their requirements are met.**
- **Unloading skills when they become unusable due to lost resources.**

### Benefits:
- Reduces memory and CPU usage.
- Avoids unnecessary skill activations.
- Simplifies skill logic (e.g., no need to check for connectivity manually).

Skills are loaded only when their specific requirements are met. This optimization prevents unnecessary loading, conserving system resources and ensuring a more efficient skill environment.

**Reducing Unintended Activations**

Dynamic unloading of skills based on specific conditions significantly reduces the chance of unintentional activations. In scenarios where required conditions are not met, skills are gracefully unloaded, enhancing the user experience by avoiding unintended skill triggers.

This approach aligns with resource-conscious design, providing a more responsive and reliable voice assistant environment. Developers can focus on skill functionality, knowing that OVOS efficiently manages skill loading and unloading based on runtime requirements.

---

## RuntimeRequirements (`@classproperty`)

Also introduced in `ovos-core 0.0.8`, the `RuntimeRequirements` class property allows skill developers to declare when a skill should be loaded or unloaded based on runtime conditions.

> ⚠️ Replaces the deprecated `"priority_skills"` config.

### Key fields:

| Field                 | Description |
|----------------------|-------------|
| `internet_before_load` | Wait for internet before loading |
| `requires_internet`     | Unload if internet is lost (unless fallback enabled) |
| `no_internet_fallback` | If true, do **not** unload when internet is lost |
| `network_before_load`  | Wait for network before loading |
| `requires_network`     | Unload if network is lost (unless fallback enabled) |
| `gui_before_load`      | Wait for GUI before loading |
| `requires_gui`         | Unload if GUI is lost (unless fallback enabled) |
| `no_gui_fallback`      | If true, do **not** unload when GUI is lost |

> 🧠 Uses `@classproperty` so the system can evaluate the requirements without loading the skill.

---

## Examples

### 1. Fully Offline Skill

In this example, a fully offline skill is defined. The skill does not require internet or network connectivity during
loading or runtime. If the network or internet is unavailable, the skill can still operate.

Defining this will ensure your skill loads as soon as possible; otherwise, the `SkillManager` will wait for internet before loading the skill.

```python
from ovos_utils import classproperty
from ovos_workshop.skills import OVOSSkill
from ovos_utils.process_utils import RuntimeRequirements


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
Loads immediately, runs without internet or network.

---

### 2. Internet-Dependent Skill (with fallback)

In this example, an online search skill with a local cache is defined. The skill requires internet connectivity during
both loading and runtime. If the internet is not available, the skill won't load. Once loaded, the skill continues to
require internet connectivity.

However, our skill keeps a cache of previous results; therefore, it declares it can handle internet outages and will not
be unloaded when the internet goes down.

```python
from ovos_utils import classproperty
from ovos_workshop.skills import OVOSSkill
from ovos_utils.process_utils import RuntimeRequirements


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
Loads only when internet is available. Stays loaded even if internet is lost, using a cached fallback.

---

### 3. LAN-Controlled IOT Skill

Consider a skill that should only load once we have a network connection.
By specifying that requirement, we can ensure that the skill is only loaded once the requirements are met, and it is
safe to utilize network resources on initialization.

In this example, an IOT skill controlling devices via LAN is defined. The skill requires network connectivity during
loading, and if the network is not available, it won't load.

Once loaded, the skill continues to require network connectivity and will unload if the network is lost.

```python
from ovos_utils import classproperty
from ovos_workshop.skills import OVOSSkill
from ovos_utils.process_utils import RuntimeRequirements


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
Loads when the local network is connected. Unloads if the network is lost.

---

### 4. GUI + Internet Skill (Unloads without GUI)

Consider a skill with both graphical user interface (GUI) and internet dependencies is defined.

The skill requires both GUI availability and internet connectivity during loading.

If either the GUI or the internet is not available, the skill won't load. Once loaded, the skill continues to require
both GUI availability, but internet connectivity is optional.

If the user asks "show me the picture of the day" and we have both internet and a GUI, our skill will match the intent.

If we do not have internet but have a GUI, the skill will still operate, using a cached picture. If no GUI is available then the skill will unload regardless of internet status

```python
from ovos_utils import classproperty
from ovos_workshop.skills import OVOSSkill
from ovos_utils.process_utils import RuntimeRequirements


class MyGUIAndInternetSkill(OVOSSkill):

    @classproperty
    def runtime_requirements(self):
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
Requires GUI and internet to load. Will stay loaded if internet is lost (e.g., to show a cached picture), but unloads if GUI becomes unavailable.

---

## Tips and Caveats

- If `runtime_requirements` is not defined, OVOS assumes **internet is required** but **GUI is optional**.
- You can combine different requirements to handle a wide range of usage patterns (e.g., headless servers, embedded devices, smart displays).
- Consider defining graceful fallbacks to avoid unnecessary unloading.
