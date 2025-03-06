# Skill Settings

Skill settings provide the ability for users to configure a Skill using the command line or a web-based interface. 

This is often used to:

* Change default behaviors - such as the sound used for users alarms.
* Authenticate with external services - such as Spotify
* Enter longer data as text rather than by voice - such as the IP address of the users Home Assistant server.

Skill settings are completely optional.

## Using settings in your Skill

Settings can be managed via command line, shipped by images or managed via a backend if ovos-core us configured to use one

When settings are fetched from the backend server, they are saved into a `settings.json` file. 
This file is automatically created when a Skill is loaded even if the Skill does not have any settings. 
Your Skill then accesses the settings from this file. 
Nowadays, the file is located in the Skill's XDG\_CONFIG\_DIR (usually `~/config/mycroft/skills/<skillname>`), however if a `settings.json` file already exists in the Skill's root directory (the deprecated location) that location is used for compatibility.

### Reading settings

Skill settings are available on the OVOSSkill class and inherit from a Python Dict. This means that you can use it just like you would any other Python dictionary.

To access the `show_time` variable from our example above we would use the `Dict.get` method:

```python
self.settings.get('show_time')
```

If the setting we are trying to access is not available, the `get` method will return `None`. Instead of assigning this to a variable and then testing for `None`, we can provide a default value as the second argument to the `get` method.

```python
self.settings.get('show_time', False)
```

In this example, if the settings have not been received, or the `show_time` setting has not been assigned, it will return the default value `False`.


**A few warnings**

We recommend using the `Dict.get` method above rather than accessing the setting directly with:

```python
self.settings['show_time']
```

Directly referencing the value may throw a KeyError if the setting has not yet been fetched from the server.

It is also important to note that the `settings` dictionary will not be available in your Skills `__init__` method as this is setting up your Skills Class. 
You should instead use an `initialize` method which is called after the Skill is fully constructed and registered with the system.


### Handling settings changes

OVOS will check for updates to a users settings regularly, both locally and on the configured backend. 

To perform some action when settings are updated, you can register a callback function in your Skill.

```python
def initialize(self):
  self.settings_change_callback = self.on_settings_changed
  self.on_settings_changed()

def on_settings_changed(self):
  show_time = self.settings.get('show_time', False)
  self.trigger_time_display(show_time)
```

In the example above, we have registered the `on_settings_changed` method to be our callback function. 
We have then immediately called the method to perform the relevant actions when the Skill is being initialized even though the Skills settings have not changed.

In the `on_settings_changed` method we have assigned the value of the `show_time` setting to a local variable. Then we have passed it as an argument to another method in our Skill that will trigger the display of the time based on its value.

### Writing to settings

Your Skill can reassign a setting locally. To do this we assign a value like you would with any other dictionary key.

```python
self.settings['show_time'] = True
```

The new value for the `show_time` setting will persist until a new setting is assigned locally by the Skill, or remotely if the user configured a backend

