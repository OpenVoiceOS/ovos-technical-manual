# Standalone Apps

Standalone applications can be made for OVOS, these applications are not skills, instead they are launched by the user

The main use case for these applications is in desktops, when you want to add a voice interface to a regular application

## OVOSAbstractApplication

`ovos-workshop` provides the `OVOSAbstractApplication` class, you can use all methods and decorators from regular Skills in applications built from this

```python
from ovos_workshop.app import OVOSAbstractApplication


class MyApplication(OVOSAbstractApplication):
    def __init__(self, skill_id: str = "my_app",
                 bus: Optional[MessageBusClient] = None,
                 resources_dir: Optional[str] = None,
                 gui: Optional[GUIInterface] = None,
                 **kwargs):
        """
        Create an Application. An application is essentially a skill, but
        designed such that it may be run without an intent service.
        @param skill_id: Unique ID for this application
        @param bus: MessageBusClient to bind to application
        @param resources_dir: optional root resource directory (else defaults to
            application `root_dir`
        @param gui: GUIInterface to bind (if `None`, one is created)
        """
        super().__init__(skill_id, bus, resources_dir, gui=gui, **kwargs)


    def do_app_stuff(self):
        pass

    @intent_handler("app_action.intent")
    def do_intent_stuff_in_app(self, message):
        pass

    
if __name__ == "__main__":
    # launch your application
    from ovos_utils import wait_for_exit_signal
    
    app = MyApplication()
    
    # wait for user to exit
    wait_for_exit_signal()
```

> **NOTE**: from OVOS perspective Voice Apps are just like skills and need to have a unique `skill_id`