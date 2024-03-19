## Anatomy of a Skill

### `vocab`, `dialog`, and `locale` directories

The `dialog`, `vocab`, and `locale` directories contain subdirectories for each spoken language the skill supports.
The subdirectories are named using the [IETF language tag](https://en.wikipedia.org/wiki/IETF\_language\_tag) for the
language.
For example, Brazilian Portuguese is 'pt-br', German is 'de-de', and Australian English is 'en-au'.

`dialog` and `vocab` have been **deprecated**, they are still supported, but we strongly recommend you use `locale` for new
skills

inside the `locale` folder you will find subfolders for each language (e.g. `en-us`), often all you need to do in order
to translate a skill is adding a new folder for your language here

each language folder can have the structure it wants, you may see files grouped by type in subfolder or all in the base
folder

You will find several unfamiliar file extensions in this folder, but these are simple text files

* `.dialog` files used for defining speech responses
* `.intent` files used for defining Padatious Intents
* `.voc` files define keywords primarily used in Adapt Intents
* `.entity` files define a named entity primarily used in Padatious Intents

### __init__.py

The `__init__.py` file is where most of the Skill is defined using Python code. 

#### Importing libraries

```python
from ovos_workshop.intents import IntentBuilder
from ovos_workshop.decorators import intent_handler
from ovos_workshop.skills import OVOSSkill
```

This section of code imports the required _libraries_. Some libraries will be required on every Skill, and your skill
may need to import additional libraries.

#### Class definition

The `class` definition extends the `OVOSSkill` class:

```python
class HelloWorldSkill(OVOSSkill):
```

The class should be named logically, for example "TimeSkill", "WeatherSkill", "NewsSkill", "IPaddressSkill". If you
would like guidance on what to call your Skill, please join
the [skills Channel on OVOS Chat](https://matrix.to/#/#openvoiceos-skills:matrix.org).

Inside the class, methods are then defined.

#### __init__()

This method is the _constructor_. It is called when the Skill is first constructed. It is often used to declare state
variables or perform setup actions, however it cannot fully utilise OVOSSkill methods as the skill is not fully initialized yet at this point. 

**You usually don't have to include the constructor.**

An example `__init__` method might be:

```python
def __init__(self, *args, **kwargs):
    super().__init__(*args, **kwargs)
    self.already_said_hello = False
    self.be_friendly = True


```

`__init__` method must accept at least `skill_id` and `bus` kwargs and pass them to `super()`, we recommend passing `*args, **kwargs` like in example above instead

**NOTE**: `self.skill_id`, `self.filesystem`, `self.settings`, `self.bus` are only available after the call to `super()`, if you need them consider using `initialize` instead


#### initialize()

This method is called during `__init__`, if you implemented `__init__` in your skill it will be called during `super()`

Perform any final setup needed for the skill here. This function is invoked after the skill is fully constructed and
registered with the system. Intents will be registered and Skill settings will be available.

If you need to access `self.skill_id`, `self.bus`, `self.settings` or `self.filesystem` you must do it here instead of `__init__`

```python
def initialize(self):
    my_setting = self.settings.get('my_setting')
```

#### @intent_handler

We can use the `initialize` function to manually register intents, however the `@intent_handler` decorator is a
cleaner way to achieve this. We will learn all about the different [Intents](../intents.md) shortly. 

In skills we can see two different intent styles.

1. An Adapt handler, triggered by a keyword defined in a `ThankYouKeyword.voc` file.

```python
   @intent_handler(IntentBuilder('ThankYouIntent').require('ThankYouKeyword'))
   def handle_thank_you_intent(self, message):
       self.speak_dialog("welcome")
```
2. A Padatious intent handler, triggered using a list of sample phrases.

```python
   @intent_handler('HowAreYou.intent')
   def handle_how_are_you_intent(self, message):
       self.speak_dialog("how.are.you")
```

In both cases, the function receives two _parameters_:

* `self` - a reference to the HelloWorldSkill object itself
* `message` - an incoming message from the `messagebus`.

Both intents call the `self.speak_dialog()` method, passing the name of a dialog file to it. In this
case `welcome.dialog` and `how.are.you.dialog`.

#### stop()

You will usually also have a `stop()` method.

The `stop` method is called anytime a User says "Stop" or a similar command. It is useful for stopping any output or process that a User might want to end without needing to issue a Skill specific utterance such as media playback or an expired alarm notification.

In the following example, we call a method `stop_beeping` to end a notification that our Skill has created.

If the skill "consumed" the stop signal it should return True, else return False. 

```python
    def stop(self):
        if self.beeping:
            self.stop_beeping()
            return True
        return False
```

If a Skill has any active functionality, the stop() method should terminate the functionality, leaving the Skill in a known good state.

When the skill returns True no other skill will be stopped, when it returns False the next active skill will attempt to stop and so on until something consumes the stop signal

#### shutdown()

The `shutdown` method is called during the Skill process termination. 
It is used to perform any final actions to ensure all processes and operations in execution are stopped safely. 
This might be particularly useful for Skills that have scheduled future events, may be writing to a file or database, or that have initiated new processes.

In the following example we cancel a scheduled event and call a method in our Skill to stop a subprocess we initiated.

```python
    def shutdown(self):
        self.cancel_scheduled_event('my_event')
        self.stop_my_subprocess()
```

### settingsmeta.yaml

This file defines the settings UI that will be available to a User through a backend or companion app

Jump to [Skill Settings](skill-settings.md) for more information on this file and handling of Skill settings.


### setup.py

This file allows a skill to be installed just like any other python package. This means you can publish your skill on pypi or favorite package manager and use it as a dependency

A typical setup.py file looks like this
```python
#!/usr/bin/env python3
from setuptools import setup
import os
from os import walk, path


# TODO update this info!
# Define package information
SKILL_CLAZZ = "MySkill"  # Make sure it matches __init__.py class name
VERSION = "0.0.1"
URL = "https://github.com/authorName/ovos-skill-name"
AUTHOR = "authorName"
EMAIL = ""
LICENSE = "Apache2.0"
DESCRIPTION = "a skill for OVOS"

PYPI_NAME = URL.split("/")[-1]  # pip install PYPI_NAME

# Construct entry point for plugin
SKILL_ID = f"{PYPI_NAME.lower()}.{AUTHOR.lower()}"
SKILL_PKG = PYPI_NAME.lower().replace('-', '_')
PLUGIN_ENTRY_POINT = f"{SKILL_ID}={SKILL_PKG}:{SKILL_CLAZZ}"


def get_requirements(requirements_filename: str):
    """
    Parse requirements from a file.

    Args:
        requirements_filename (str, optional): The filename of the requirements file.
            Defaults to "requirements.txt".

    Returns:
        List[str]: A list of parsed requirements.

    Notes:
        If the environment variable MYCROFT_LOOSE_REQUIREMENTS is set, this function
        will modify the parsed requirements to use loose version requirements,
        replacing '==' with '>=' and '~=' with '>='.

    """
    requirements_file = path.join(path.abspath(path.dirname(__file__)),
                                  requirements_filename)
    with open(requirements_file, 'r', encoding='utf-8') as r:
        requirements = r.readlines()
    requirements = [r.strip() for r in requirements if r.strip()
                    and not r.strip().startswith("#")]
    if 'MYCROFT_LOOSE_REQUIREMENTS' in os.environ:
        print('USING LOOSE REQUIREMENTS!')
        requirements = [r.replace('==', '>=').replace('~=', '>=') for r in requirements]
    return requirements


def find_resource_files():
    """ensure all non-code resource files are included in the package"""
    # add any folder with files your skill uses here! 
    resource_base_dirs = ("locale", "ui", "vocab", "dialog", "regex")
    base_dir = path.dirname(__file__)
    package_data = ["*.json"]
    for res in resource_base_dirs:
        if path.isdir(path.join(base_dir, res)):
            for (directory, _, files) in walk(path.join(base_dir, res)):
                if files:
                    package_data.append(
                        path.join(directory.replace(base_dir, "").lstrip('/'),
                                  '*'))
    return package_data


# Setup configuration
setup(
    name=PYPI_NAME,
    version=VERSION,
    description=DESCRIPTION,
    url=URL,
    author=AUTHOR,
    author_email=EMAIL,
    license=LICENSE,
    package_dir={SKILL_PKG: ""},
    package_data={SKILL_PKG: find_resource_files()},
    packages=[SKILL_PKG],
    include_package_data=True,
    install_requires=get_requirements("requirements.txt"),
    keywords='ovos skill plugin',
    entry_points={'ovos.plugin.skill': PLUGIN_ENTRY_POINT}
)
```