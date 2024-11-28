# OpenVoiceOS STT HTTP Server

Turn any OVOS STT plugin into a microservice!


## Companion plugin

Use with OpenVoiceOS [companion plugin](https://github.com/OpenVoiceOS/ovos-stt-server-plugin)

## Public Servers

| Member | Service | Plugin | URL |
|---|:---:|:---:|:---:|
| <div><a href="https://github.com/builderjer"><img src="https://avatars.githubusercontent.com/u/34875857?v=4" alt="builderjer" width="25" height="25"></a></div> | STT | [ovos-stt-plugin-server](https://github.com/OpenVoiceOS/ovos-stt-plugin-server) | <div>Faster Whisper - [https://fasterwhisper.ziggyai.online/stt](https://fasterwhisper.ziggyai.online/status)</div> |
| <div><a href="https://github.com/goldyfruit"><img src="https://avatars.githubusercontent.com/u/614115?v=4" alt="goldyfruit" width="25" height="25"></a></div> | STT | [ovos-stt-plugin-server](https://github.com/OpenVoiceOS/ovos-stt-plugin-server) | <div>Faster Whisper - [https://stt.smartgic.io/fasterwhisper](https://stt.smartgic.io/fasterwhisper/status)</div><div>Chromium - [https://stt.smartgic.io/chromium](https://stt.smartgic.io/chromium/status)</div><div>Nemo - [https://stt.smartgic.io/nemo](https://stt.smartgic.io/nemo/status)</div>  |
| <div><a href="https://github.com/NeonGeckoCom"><img src="https://avatars.githubusercontent.com/u/32377662?s=200&v=4" alt="Neon" width="25" height="25"></a></div>| STT | [ovos-stt-server-plugin](https://github.com/OpenVoiceOS/ovos-Stt-server-plugin) | <div>Nemo - [https://nemo.neonaialpha.com](https://nemo.neonaialpha.com/status)</div> |

## Install

`pip install ovos-stt-http-server`

## Usage

```bash
ovos-stt-server --help
usage: ovos-stt-server [-h] [--engine ENGINE] [--port PORT] [--host HOST]

options:
  -h, --help       show this help message and exit
  --engine ENGINE  stt plugin to be used
  --port PORT      port number
  --host HOST      host
```

## Docker

Pre built containers can be found in the [ovos-docker-stt](https://github.com/OpenVoiceOS/ovos-docker-stt) repository

### Template

you can create easily create a docker file to serve any plugin

```dockerfile
FROM python:3.7

RUN pip3 install ovos-stt-http-server==0.0.1

RUN pip3 install {PLUGIN_HERE}

ENTRYPOINT ovos-stt-http-server --engine {PLUGIN_HERE}
```

build it
```bash
docker build . -t my_ovos_stt_plugin
```

run it
```bash
docker run -p 8080:9666 my_ovos_stt_plugin
```

Each plugin can provide its own Dockerfile in its repository using ovos-stt-http-server
