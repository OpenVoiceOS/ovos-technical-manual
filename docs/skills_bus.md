# OVOSSkill Bus Interaction

The base [OVOSSkill API](http://mycroft-core.readthedocs.io/en/stable/) handles most of the Messagebus usage automatically. 

For example, the `mycroft.stop` message is caught by the skill framework, invoking an overridden `OVOSSkills.stop()` method within a **Skill**. 

Similarly, the `OVOSSkill.speak()` and `OVOSSkill.speak_dialog()` methods generate `speak` messages to be conveyed to the text-to-speech \(TTS\) and audio systems.

You will really only need to know about the Mycroft Messagebus if you are developing advanced **Skills**. 

The `OVOSSkill.add_event()` method allows you to attach a handler which will be triggered when the message is seen on the Messagebus.

## Connecting Message handlers

```python
class ListenForMessageSkill(OVOSSkill):
  def initialize(self):  
      self.add_event('recognizer_loop:record_begin',  
                    self.handle_listener_started)  
      self.add_event('recognizer_loop:record_end',  
                    self.handle_listener_ended)

  def handle_listener_started(self, message):  
      # code to excecute when active listening begins...

  def handle_listener_ended(self, message):  
      # code to excecute when active listening begins...
```

## Generating Messages

```python
from ovos_bus_client import Message

class GenerateMessageSkill(OVOSSkill):
  def some_method(self):  
    self.bus.emit(Message("recognizer_loop:utterance",  
                          {'utterances': ["the injected utterance"],  
                            'lang': 'en-us'}))  

```
