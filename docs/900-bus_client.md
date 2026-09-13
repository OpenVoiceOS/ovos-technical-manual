# MessageBus Client

The [OVOS MessageBus Client](https://github.com/OpenVoiceOS/ovos-bus-client) is a Python module providing a simple interface for the OVOS MessageBus. It can be used to connect to OVOS, send messages, and react to messages sent by the OVOS system.

The module is available through [PyPI.org](https://pypi.org/project/ovos-bus-client) or directly [on Github](https://github.com/OpenVoiceOS/ovos-bus-client).

#### MessageBusClient\(\)

The `MessageBusClient()` object can be setup to connect to any host and port as well as any endpoint on that host. this makes it quite versatile and will work on the main bus as well as on a gui bus. 

If no arguments are provided it will try to connect to a local instance of OVOS on the default endpoint and port.

> 💡 in skills and plugins `self.bus` provides a MessageBusClient connections out of the box, you don't usually need to initialize this yourself

#### Message\(\)

The `Message` object is a representation of the messagebus message, this will always contain a message type but can also contain data and context. Data is usually real information while the context typically contain information on where the message originated or who the intended recipient is.

```python
Message('MESSAGE_TYPE', data={'meaning': 42}, context={'origin': 'A.Dent'})
```

### Sending a Message

In the following example we setup an instance of the MessageBusClient then emit a `speak` Message with a data payload. OVOS would consume this Message and speak "Hello World".

```python
from ovos_bus_client import MessageBusClient, Message

print('Setting up client to connect to a local mycroft instance')
client = MessageBusClient()
client.run_in_thread()

print('Sending speak message...')
client.emit(Message('speak', data={'utterance': 'Hello World'}))
```

### Listening for a Message

In the following example we setup an instance of the MessageBusClient. We then define a function `print_utterance` that prints the `utterance` from a Message. This is registered as a handler for the `speak` Message. Finally we call the `run_forever()` method to keep the `client` running.

If this code had run before the example above, it would catch the `speak` Message we emitted and print: `OVOS said "Hello World"`

```python
from ovos_bus_client import MessageBusClient, Message

print('Setting up client to connect to a local ovos instance')
client = MessageBusClient()

def print_utterance(message):
    print('OVOS said "{}"'.format(message.data.get('utterance')))


print('Registering handler for speak message...')
client.on('speak', print_utterance)

client.run_forever()
```
