# Bus Service

The **Message Bus** is the internal communication layer used by OVOS to allow independent components to interact using structured messages. It acts as a central nervous system, coordinating everything from speech recognition to skill execution.

---

## Overview

In the OVOS ecosystem, the messagebus is implemented as a **WebSocket** interface. Messages follow a structured format containing:

- A `type`: identifying the intent or action
- An optional `data` payload (JSON)
- An optional `context` dictionary for session or routing metadata

Some messages trigger actions; others act as notifications or state broadcasts. Both core OVOS components and external systems (e.g., [HiveMind](https://jarbashivemind.github.io/HiveMind-community-docs)) can interact with the bus.

---

## Configuration

The messagebus is configured in `mycroft.conf` under the `websocket` section:

```json
"websocket": {
    "host": "127.0.0.1",
    "port": 8181,
    "route": "/core",
    "shared_connection": true
}
```

> It is **strongly recommended** to keep the `host` set to `127.0.0.1` to prevent unauthorized remote access.

---

## Security

By default, all skills share a single bus connection. This can be exploited by malicious or poorly designed skills to interfere with others.

- To improve isolation, set `"shared_connection": false`. This ensures each skill uses a separate WebSocket connection.
- For a demonstration of potential vulnerabilities, see [BusBrickerSkill](https://github.com/EvilJarbas/BusBrickerSkill).

Security concerns are further documented in [Nhoya/MycroftAI-RCE](https://github.com/Nhoya/MycroftAI-RCE).

> ⚠️ Never expose the messagebus to the public internet. It provides full control over the OVOS instance and the host system.

> 💡 For remote interaction, use [HiveMind](https://openvoiceos.github.io/community-docs/friends/#hivemind), which offers secure proxy access to the bus.

---

## Message Structure

Each message sent on the bus consists of:

```json
{
    "type": "message.type",
    "data": { /* arbitrary JSON payload */ },
    "context": { /* optional metadata */ }
}
```

- `type`: Identifies the message (e.g., `"recognizer_loop:utterance"`)
- `data`: Carries command-specific information
- `context`: Session and routing information, used internally

For a complete index of known OVOS messages, refer to the [Message Spec documentation](https://openvoiceos.github.io/message_spec/).

---

## Sessions

Messages can carry a `"session"` key inside their `context` to preserve request-specific state and user preferences. Sessions help enable:

- Multi-user support
- Conversational context
- Remote device handling

Example session fields include:

- Language and location
- TTS/STT preferences
- Active skills and follow-up intents
- Pipeline settings
- Site or device ID

Sessions are typically auto-managed by `ovos-core` for local interactions using the default session ID (`"default"`). External clients (e.g., HiveMind voice satellites) are expected to manage their own sessions.

See the [Session-Aware Skills](https://openvoiceos.github.io/ovos-technical-manual/session_skills/) documentation for implementation guidelines.

---

## Message Targeting and Routing

OVOS uses `context["source"]` and `context["destination"]` to enable smart message routing across components and external devices.

The `Message` object includes utility methods:

- `.forward()`: Sends the message onward while preserving the current context
- `.reply()`: Sends a response back to the original source (swapping source/destination)

Example:

```python
bus.emit(Message('recognizer_loop:utterance', data, 
    context={
        'destination': ['audio', 'kde'],
        'source': 'remote_service'
    }))
```

OVOS itself does not implement any actual routing, everything connected to the ovos messagebus receives every message, however this metadata enables 3rd party applications to fully manage these messages and decide if and where to send them

> 💡 [HiveMind](https://jarbashivemind.github.io/HiveMind-community-docs/) uses these fields extensively to direct replies to the correct satellite.

---

## Internal Routing Overview

![OVOS Message Flow Diagram](https://github.com/OpenVoiceOS/ovos-technical-manual/assets/33701864/df9aa669-ce7f-430e-b4db-f57200e75332)

- The Intent Service replies directly to utterance messages.
- Skill and intent interactions use `.forward()` to retain context.
- Skills sending their own events should manually manage routing.

> ⚠️ Skills that are not session-aware may behave unexpectedly when used with external voice clients.
