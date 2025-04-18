# OpenVoiceOS Translate Server

**Expose OVOS language detection and translation plugins over HTTP.**

The OVOS Translate Server allows any OpenVoiceOS-compatible translation or language detection plugin to run as a lightweight web service. This makes it easy to integrate translation features into any application or device using simple HTTP requests.

Great for local or cloud deployments, and ideal for use with the OVOS companion plugin to provide translation capabilities to your voice assistant.

---

## Usage Guide

### Install the Server

```bash
pip install ovos-translate-server
```

### Run the Server

```bash
ovos-translate-server \
  --tx-engine ovos-translate-plugin-nllb \
  --detect-engine ovos-lang-detector-classics-plugin
```

### Make Requests

Once the server is running (default on `http://0.0.0.0:9686`), you can access endpoints like:

- Auto-detect source language:
  ```
  GET /translate/en/o meu nome é Casimiro
  → "My name is Casimiro"
  ```

- Specify source and target language:
  ```
  GET /translate/pt/en/o meu nome é Casimiro
  → "My name is Casimiro"
  ```

- Language detection:
  ```
  GET /detect/o meu nome é Casimiro
  → "pt"
  ```

---

## Command-Line Options

```bash
$ ovos-translate-server --help
usage: ovos-translate-server [-h] [--tx-engine TX_ENGINE] [--detect-engine DETECT_ENGINE] [--port PORT] [--host HOST]

options:
  -h, --help            show this help message and exit
  --tx-engine TX_ENGINE
                        translate plugin to be used
  --detect-engine DETECT_ENGINE
                        lang detection plugin to be used
  --port PORT           port number
  --host HOST           host

```

---

## Technical Overview

- **Plugin-based**: Uses the OVOS Plugin Manager to dynamically load any compatible plugin by name.
- **RESTful API**: Simple HTTP endpoints allow you to send and receive translations from any app.
- **Language Detection Support**: Works with any OVOS lang-detection plugin.
- **Easy Deployment**: Perfect for running locally, inside Docker, or on a small server.

---

## Docker Deployment

### Use Prebuilt Images

Check out [ovos-docker-tx](https://github.com/OpenVoiceOS/ovos-docker-tx) for prebuilt containers.

### Build Your Own

Create a `Dockerfile`:

```dockerfile
FROM python:3.7

RUN pip install ovos-utils==0.0.15
RUN pip install ovos-plugin-manager==0.0.4
RUN pip install ovos-translate-server==0.0.1

# Install your plugins
RUN pip install {PLUGIN_HERE}

ENTRYPOINT ovos-translate-server --tx-engine {PLUGIN_HERE} --detect-engine {PLUGIN_HERE}
```

Build the image:

```bash
docker build . -t my_ovos_translate_plugin
```

Run the container:

```bash
docker run -p 8080:9686 my_ovos_translate_plugin
```

Each plugin can provide its own Dockerfile using `ovos-translate-server` as the entrypoint.

---

## Companion Plugin Integration

To use this server with an OVOS voice assistant instance, install:

```bash
pip install ovos-translate-server-plugin
```

Then configure your `mycroft.conf`:

```json
{
  "language": {
    "detection_module": "ovos-lang-detector-plugin-server",
    "translation_module": "ovos-translate-plugin-server",
    "ovos-translate-plugin-server": {
      "host": "http://localhost:9686",
      "verify_ssl": false
    },
    "ovos-lang-detector-plugin-server": {
      "host": "http://localhost:9686",
      "verify_ssl": false
    }
  }
}
```

---

## Tips & Caveats

- Some translation plugins auto-detect language; others require you to specify `source_lang`.
- Network errors or unresponsive servers will trigger fallback plugins, if configured.
- For production, consider placing the service behind a reverse proxy with HTTPS enabled.

---

## Related Projects

- **Translate Server Plugin**:  
  [ovos-translate-server-plugin](https://github.com/OpenVoiceOS/ovos-translate-server-plugin)

- **Translate Server Source**:  
  [ovos-translate-server](https://github.com/OpenVoiceOS/ovos-translate-server)

- **Docker Templates**:  
  [ovos-docker-tx](https://github.com/OpenVoiceOS/ovos-docker-tx)

- **Plugin Manager**:  
  [ovos-plugin-manager](https://github.com/OpenVoiceOS/ovos-plugin-manager)

---

With the OVOS Translate Server, adding multi-language support to your voice assistant is just an HTTP request away.