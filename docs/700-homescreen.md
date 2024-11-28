# OpenVoiceOS Home Screen

The home screen is the central place for all your tasks. It is what your device displays when it is idle

## Configuration

Select a homescreen in `mycroft.conf` or via `ovos-shell`

```javascript
"gui": {
    "idle_display_skill": "skill-ovos-homescreen.openvoiceos"
}
```

## Resting Faces

The resting face API provides skill authors the ability to extend their skills to supply their own customized IDLE screens that will be displayed when there is no activity on the screen.

```
import requests
from ovos_workshop.skills import OVOSSkill
from ovos_workshop.descorators import intent_handler, resting_screen_handler


class CatSkill(OVOSSkill):
    def update_cat(self):
        r = requests.get('https://api.thecatapi.com/v1/images/search')
        return r.json()[0]['url']

    @resting_screen_handler("Cat Image")
    def idle(self, message):
        img = self.update_cat()
        self.gui.show_image(img)

    @intent_handler('show_cat.intent')
    def cat_handler(self, message):
        img = self.update_cat()
        self.gui.show_image(img)
        self.speak_dialog('mjau')
```

A more advanced example, refreshing a webpage on a timer

```python
from ovos_workshop.skills import OVOSSkill
from ovos_workshop.descorators import intent_handler, resting_screen_handler

class WebpageHomescreen(OVOSSkill):

    def initialize(self):
        """Perform final setup of Skill."""
        # Disable manual refresh until this Homepage is made active.
        self.is_active = False
        self.disable_intent("refresh-homepage.intent")
        self.settings_change_callback = self.refresh_homescreen

    def get_intro_message(self):
        """Provide instructions on first install."""
        self.speak_dialog("setting-url")
        self.speak_dialog("selecting-homescreen")

    @resting_screen_handler("Webpage Homescreen")
    def handle_request_to_use_homescreen(self, message: Message):
        """Handler for requests from GUI to use this Homescreen."""
        self.is_active = True
        self.display_homescreen()
        self.refresh_homescreen(message)
        self.enable_intent("refresh-homepage.intent")

    def display_homescreen(self):
        """Display the selected webpage as the Homescreen."""
        default_url = "https://openvoiceos.github.io/status"
        url = self.settings.get("homepage_url", default_url)
        self.gui.show_url(url)

    @intent_handler("refresh-homepage.intent")
    def refresh_homescreen(self, message: Message):
        """Update refresh rate of homescreen and refresh screen.

        Defaults to 600 seconds / 10 minutes.
        """
        self.cancel_scheduled_event("refresh-webpage-homescreen")
        if self.is_active:
            self.schedule_repeating_event(
                self.display_homescreen,
                0,
                self.settings.get("refresh_frequency", 600),
                name="refresh-webpage-homescreen",
            )

    def shutdown(self):
        """Actions to perform when Skill is shutting down."""
        self.is_active = False
        self.cancel_all_repeating_events()
```