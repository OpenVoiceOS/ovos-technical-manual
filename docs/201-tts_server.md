# OpenVoiceOS TTS Server

**Lightweight HTTP microservice for any OVOS text‑to‑speech plugin, with optional caching.**

Wrap your favorite OVOS TTS engine in a FastAPI service—ready to deploy locally, in Docker, or behind a load balancer.

The OpenVoiceOS TTS HTTP Server exposes any OVOS TTS plugin over a simple HTTP API. Send text, receive audio—no extra
glue code required.

---
## Usage Guide

**Install the server**

```bash
pip install ovos-tts-server
```

**Configure your TTS plugin**

In your `mycroft.conf` (or equivalent) under the `tts` section:
```json
{
 "tts": {
   "module": "ovos-tts-plugin-xxx",
   "ovos-tts-plugin-xxx": {
     "voice": "xxx"
   }
 }
}
```

**Launch the server**  

```bash
ovos-stt-server \
 --engine ovos-tts-plugin-xxx \
 --host 0.0.0.0 \
 --port 9666
```

**Verify it’s running**  

Visit http://localhost:9666/status in your browser or run:

```bash
curl http://localhost:9666/status
```

---

## Command‑Line Options

```bash
$ ovos-tts-server --help
usage: ovos-tts-server [-h] [--engine ENGINE] [--port PORT] [--host HOST] [--cache] [--lang LANG] [--gradio] [--title TITLE] [--description DESCRIPTION]
                       [--info INFO] [--badge BADGE]

options:
  -h, --help            show this help message and exit
  --engine ENGINE       tts plugin to be used
  --port PORT           port number
  --host HOST           host
  --cache               save every synth to disk
  --lang LANG           default language supported by plugin
  --gradio              Enable Gradio Web UI
  --title TITLE         Title for webUI
  --description DESCRIPTION
                        Text description to print in UI
  --info INFO           Text to display at end of UI
  --badge BADGE         URL of visitor badge

```

---

## Technical Explanation

- **FastAPI Core**  
  Spins up a FastAPI application exposing RESTful endpoints for synthesis and status checks.
- **Plugin Loading**  
  Dynamically loads any OVOS TTS plugin via Python entry points—no code changes needed when adding new voices.
- **Caching**  
  When `--cache` is enabled, every synthesis request is stored as a WAV file for debugging or reuse.
- **Scalability**  
  Stateless by design—run multiple instances behind NGINX, Traefik, or Kubernetes with round‑robin or load‑based
  routing.

---

## HTTP API Endpoints

| Endpoint                  | Method | Description                                       |
|---------------------------|--------|---------------------------------------------------|
| `/status`                 | GET    | Returns loaded plugin names and versions.         |
| `/synthesize/{utterance}` | GET    | URL‑encoded text → WAV audio bytes.               |
| `/v2/synthesize`          | GET    | JSON `{utterance: string, voice?: string}` → WAV. |
| `/docs`                   | GET    | Interactive OpenAPI (Swagger) docs.               |

> any query parameters passed to `/v2/synthesize` will be forwarded to the individual plugins `get_tts` method if they are defined as kwargs there. 
> 💡 This allows `"voice"` and `"lang"` to be defined at runtime and not by plugin config at load time (for plugins that support it)

---

## Companion Plugin

Point your OVOS instance at this TTS server:

```bash
pip install ovos-tts-server-plugin
```

**Configuration** `mycroft.conf`:

```json
{
  "tts": {
    "module": "ovos-tts-plugin-server",
    "ovos-tts-plugin-server": {
        "host": "http://localhost:9667",
        "voice": "xxx",
        "verify_ssl": false,
        "tts_timeout": 5
     }
 } 
}
```

---

## Docker Deployment

**Create a Dockerfile**
```dockerfile
FROM python:3.7-slim
RUN pip install ovos-tts-server
RUN pip install {YOUR_TTS_PLUGIN}
ENTRYPOINT ["ovos-tts-server", "--engine", "{YOUR_TTS_PLUGIN}"]
```

**Build & Run**
```bash
docker build -t my-ovos-tts .
docker run -p 8080:9666 my-ovos-tts
```

Pre-built containers are also available via the [ovos-docker-tts](https://github.com/OpenVoiceOS/ovos-docker-tts)
repository.

---

## Tips & Caveats

- **Audio Formats**: By default, outputs WAV (PCM). If you need MP3 or OGG, wrap with an external converter or check
  plugin support.
- **Disk Usage**: Caching every file can grow large; monitor `./cache/` or disable with `--no-cache`.
- **Security**: Consider adding API keys or putting a reverse proxy (NGINX, Traefik) in front for SSL termination and
  rate limiting.
- **Plugin Dependencies**: Some voices require native libraries (e.g., TensorFlow). Bake them into your Docker image to
  avoid runtime surprises.

---

## Links & References

- **TTS Server GitHub**: https://github.com/OpenVoiceOS/ovos-tts-server
- **Companion Plugin**: https://github.com/OpenVoiceOS/ovos-tts-server-plugin
- **Docker Images**: https://github.com/OpenVoiceOS/ovos-docker-tts
- **OVOS Plugin Manager**: https://github.com/OpenVoiceOS/ovos-plugin-manager