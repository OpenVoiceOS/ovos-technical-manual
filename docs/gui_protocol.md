## Protocol

The [gui service](https://github.com/OpenVoiceOS/ovos-gui) in ovos-core will expose a websocket to
the GUI clients following the protocol outlined in this page

The transport protocol works between gui service and the gui clients, OpenVoiceOS does not directly use the protocol but instead communicates with `ovos-gui` via the standard messagebus

The QT library which implements the protocol lives in the [mycroft-gui-qt5](https://github.com/OpenVoiceOS/mycroft-gui-qt5) repository. 

![imagem](https://github.com/OpenVoiceOS/ovos-technical-manual/assets/33701864/92e73af7-f7d2-4aa3-a294-77f87aa22390)

## Specification

This protocol defines how ovos-gui communicates with connected clients

- [CONNECTION - mycroft.gui.connected](#connection---mycroftguiconnected)
- [NAMESPACES](#namespaces)
  * [Active Skills - mycroft.system.active_skills](#active-skills---mycroftsystemactive-skills)
- [PAGES - mycroft.gui.list.xxx](#pages---mycroftguilistxxx)
  * [Insert new page at position](#insert-new-page-at-position)
  * [Move pages within the list](#move-pages-within-the-list)
  * [Remove pages from the list](#remove-pages-from-the-list)
- [EVENTS - mycroft.events.triggered](#events---mycrofteventstriggered)
  * [SPECIAL EVENT: page_gained_focus](#special-event--page-gained-focus)
- [SKILL DATA - mycroft.session.xxx](#skill-data---mycroftsessionxxx)
  * [Sets a new key/value in the sessionData dictionary](#sets-a-new-key-value-in-the-sessiondata-dictionary)
  * [Deletes a key/value pair from the sessionData dictionary](#deletes-a-key-value-pair-from-the-sessiondata-dictionary)
  * [Lists](#lists)
    + [Inserts new items at position](#inserts-new-items-at-position)
    + [Updates item values starting at the given position, as many items as there are in the array](#updates-item-values-starting-at-the-given-position--as-many-items-as-there-are-in-the-array)
    + [Move items within the list](#move-items-within-the-list)
    + [Remove items from the list](#remove-items-from-the-list)


## CONNECTION - mycroft.gui.connected

on connection gui clients announce themselves

This is an extension by OVOS to the [original mycroft protocol](https://github.com/MycroftAI/mycroft-gui/blob/master/transportProtocol.md)


```javascript
{
    "type": "mycroft.gui.connected",
    "gui_id": "unique_identifier_provided_by_client"
}
```

## NAMESPACES

ovos-gui maintains a list of namespaces with GUI data, namespaces usually correspond to a skill_id

Every message in the gui protocol specifies a namespace it belongs to

gui clients usualy display all namespaces, but can be requested to display a single one, 

eg, have a dedicated window to show a skill as a [traditional desktop app](https://github.com/OpenVoiceOS/ovos-ocp-audio-plugin/blob/dev/ovos_plugin_common_play/ocp/res/desktop/OCP.desktop)


### Active Skills - mycroft.system.active_skills

a reserved namespace is "mycroft.system.active_skills", the data contained in this namespace defines the namespace display priority

Recent skills are ordered from the last used to the oldest, so the first item of the list will always be the the one showing any GUI page, if available.

see the section about [lists](https://github.com/OpenVoiceOS/ovos-gui/blob/dev/protocol.md#lists) if you need to modify active skills


## PAGES - mycroft.gui.list.xxx

Each active skill is associated with a list of uris to the QML files of all gui items that are supposed to be visible.

Non QT GUIS get sent other file extensions such as .jsx or .html using the same message format

### Insert new page at position

```javascript
{
    "type": "mycroft.gui.list.insert",
    "namespace": "mycroft.weather"
    "position": 2
    "values": [{"url": "file://..../currentWeather.qml"}, ...] //values must always be in array form
}
```

### Move pages within the list
```javascript
{
    "type": "mycroft.gui.list.move",
    "namespace": "mycroft.weather"
    "from": 2
    "to": 5
    "items_number": 2 //optional in case we want to move a big chunk of list at once
}
```

### Remove pages from the list
```javascript
{
    "type": "mycroft.gui.list.remove",
    "namespace": "mycroft.weather"
    "position": 2
    "items_number": 5 //optional in case we want to get rid a big chunk of list at once
}
```


## EVENTS - mycroft.events.triggered

Events can either be emitted by a gui client (eg, some element clicked) or by the skill (eg, in response to a voice command)

```javascript
{
    "type": "mycroft.events.triggered"
    "namespace": "my_skill_id"
    "event_name": "my.gui.event",
    "parameters": {"item": 3}
}
```

### SPECIAL EVENT: page_gained_focus

This event is used when the ovos-gui wants a page of a particular skill to gain user attention focus and become the current active view and "focus of attention" of the user. 

when a GUI client receives it, it should render the requested GUI page

GUI clients can also emit this event, if a new page was rendered (eg, in response to a user swipping left)

NOTE: for responsiveness it is recommened this message is only emitted after the rendering has actually been done, skills may be waiting for this event to initiate some actons

```javascript
{
    "type": "mycroft.events.triggered",
    "namespace": "mycroft.weather",
    "event_name": "page_gained_focus",
    "data": {"number": 0}
}
```

The parameter "number" is the position (starting from zero) of the page

## SKILL DATA - mycroft.session.xxx

At the center of data sharing there is a key/value dictionary that is kept synchronized between ovos-gui and the GUI client.

Values can either be simple strings, numbers and booleans or be more complicated data types

this event can be sent from gui clients (eg, in response to a dropdown selection) or from skills (eg, change weather data)

NOTE: Once a new gui client connects to ovos-gui, all existing session data is sent to the client, 
after that the client gets live updates via these events

### Sets a new key/value in the sessionData dictionary

Either sets a new key/value pair or replace an existing old value.

```javascript
{
    "type": "mycroft.session.set",
    "namespace": "weather.mycroft"
    "data": {
        "temperature": "28",
        "icon": "cloudy",
        "forecast": [{...},...] //if it's a list see below for more message types
    }
}
```

### Deletes a key/value pair from the sessionData dictionary
```javascript
{
    "type": "mycroft.session.delete",
    "namespace": "weather.mycroft"
    "property": "temperature"
}
```

### Lists

#### Inserts new items at position
```javascript
{
    "type": "mycroft.session.list.insert",
    "namespace": "weather.mycroft"
    "property": "forecast" //the key of the main data map this list in contained into
    "position": 2
    "values": [{"date": "tomorrow", "temperature" : 13, ...}, ...] //values must always be in array form
}
```

#### Updates item values starting at the given position, as many items as there are in the array
```javascript
{
    "type": "mycroft.session.list.update",
    "namespace": "weather.mycroft"
    "property": "forecast"
    "position": 2
    "values": [{"date": "tomorrow", "temperature" : 13, ...}, ...] //values must always be in array form
}
```

#### Move items within the list
```javascript
{
    "type": "mycroft.session.list.move",
    "namespace": "weather.mycroft"
    "property": "forecast"
    "from": 2
    "to": 5
    "items_number": 2 //optional in case we want to move a big chunk of list at once
}
```

#### Remove items from the list
```javascript
{
    "type": "mycroft.session.list.remove",
    "namespace": "weather.mycroft"
    "property": "forecast"
    "position": 2
    "items_number": 5 //optional in case we want to get rid a big chunk of list at once
}
```


