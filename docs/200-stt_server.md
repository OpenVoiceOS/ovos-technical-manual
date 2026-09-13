# OpenVoiceOS STT HTTP Server

**Lightweight HTTP microservice for any OVOS speech‑to‑text plugin, with optional Gradio UI.**

The OpenVoiceOS STT HTTP Server wraps your chosen OVOS STT plugin inside a FastAPI service (complete with automatic language detection), making it easy to deploy on your local machine, in Docker, or behind a load balancer.

---

## Usage Guide

**Install the server** 

```bash
pip install ovos-stt-http-server
```

**Configure your STT plugin**  

In your `mycroft.conf` (or equivalent) under the `stt` section:  
```json
{
 "stt": {
   "module": "ovos-stt-plugin-xxx",
   "ovos-stt-plugin-xxx": {
     "model": "xxx"
   }
 }
}
```

**Launch the server**  

```bash
ovos-stt-server \
 --engine ovos-stt-plugin-xxx \
 --host 0.0.0.0 \
 --port 9666
```

**Verify it’s running**  

Visit [http://localhost:9666/status](http://localhost:9666/status) in your browser or run:  

```bash
curl http://localhost:9666/status
```

---

## Command‑Line Options

```bash
$ ovos-stt-server --help
usage: ovos-stt-server [-h] --engine ENGINE [--lang-engine LANG_ENGINE] [--port PORT] [--host HOST] [--lang LANG] [--multi] [--gradio] [--cache] [--title TITLE]
                       [--description DESCRIPTION] [--info INFO] [--badge BADGE]

options:
  -h, --help            show this help message and exit
  --engine ENGINE       stt plugin to be used
  --lang-engine LANG_ENGINE
                        audio language detection plugin to be used
  --port PORT           port number
  --host HOST           host
  --lang LANG           default language supported by plugin
  --multi               Load a plugin instance per language (force lang support)
  --gradio              Enable Gradio Web UI
  --cache               Cache models for Gradio demo
  --title TITLE         Title for webUI
  --description DESCRIPTION
                        Text description to print in UI
  --info INFO           Text to display at end of UI
  --badge BADGE         URL of visitor badge

```
---

## Technical Explanation

- **FastAPI core**  
  The server spins up a FastAPI app exposing REST endpoints.
- **Plugin wrapping**  
  Any OVOS STT plugin (Deepgram, Whisper, etc.) is loaded dynamically via entry points.
- **Language detection**  
  If you enable `--lang-engine`, incoming audio is passed through the detector, falling back to `--lang` or plugin defaults.
- **Scalability**  
  Stateless design lets you run multiple instances behind a load balancer or in Kubernetes.
- **Optional Gradio UI**  
  Launches a simple web demo for testing without writing any front‑end code.

---

## HTTP API Endpoints

| Endpoint       | Method | Description                                                |
| -------------- | ------ | ---------------------------------------------------------- |
| `/status`      | GET    | Returns plugin names, versions, and Gradio status.        |
| `/stt`         | POST   | Transcribe audio → plain‑text transcript.                 |
| `/lang_detect` | POST   | Detect language → JSON `{ "lang": "en", "conf": 0.83 }`.  |
| `/docs`        | GET    | Interactive FastAPI OpenAPI docs.                         |

---

## Companion Plugin

To point a OpenVoiceOS (or compatible project) to a STT server you can use the companion plugin

**Install**  
```bash
pip install ovos-stt-plugin-server
```

**Configure**  
```json
  "stt": {
    "module": "ovos-stt-plugin-server",
    "ovos-stt-plugin-server": {
      "urls": ["https://0.0.0.0:8080/stt"],
      "verify_ssl": true
    },
 }
```

for audio language detection

```json
  "listener": {
    "audio_transformers": {
        "ovos-audio-lang-server-plugin": {
          "urls": ["https://0.0.0.0:8080/lang_detect"],
          "verify_ssl": true
        }
    }
  }
```

---

## Docker Deployment

**Create a Dockerfile**
```dockerfile
FROM python:3.7-slim
RUN pip install ovos-stt-http-server==0.0.1
RUN pip install {YOUR_STT_PLUGIN}
ENTRYPOINT ["ovos-stt-http-server", "--engine", "{YOUR_STT_PLUGIN}"]
```

**Build & Run**
```bash
docker build -t my-ovos-stt .
docker run -p 8080:9666 my-ovos-stt
```

Pre-built containers are also available via the [ovos-docker-stt](https://github.com/OpenVoiceOS/ovos-docker-stt) repository.

---

## Tips & Caveats

- **Audio Formats**: Ensure client sends PCM‑compatible formats (`.wav`, `.mp3` recommended).
- **Securing Endpoints**: Consider putting a reverse proxy (NGINX, Traefik) in front for SSL or API keys.
- **Plugin Dependencies**: Some STT engines require heavy native libraries—bake them into your Docker image.

---

## Links & References

- OVOS STT HTTP Server GitHub: https://github.com/OpenVoiceOS/ovos-stt-http-server
- Companion Plugin: https://github.com/OpenVoiceOS/ovos-stt-server-plugin
- Docker Images: https://github.com/OpenVoiceOS/ovos-docker-stt
- OVOS Plugin Manager: https://github.com/OpenVoiceOS/ovos-plugin-manager