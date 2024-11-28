# OpenVoiceOS TTS Server

Turn any OVOS TTS plugin into a microservice!


## Companion plugin

Use with OpenVoiceOS [companion plugin](https://github.com/OpenVoiceOS/ovos-tts-server-plugin)

## Public Servers

| Member | Service | Plugin | URL |
|---|:---:|:---:|:---:|
| <div><a href="https://github.com/goldyfruit"><img src="https://avatars.githubusercontent.com/u/614115?v=4" alt="goldyfruit" width="25" height="25"></a></div> | TTS | [ovos-tts-server-plugin](https://github.com/OpenVoiceOS/ovos-tts-server-plugin) | <div>Mimic 1 - [https://tts.smartgic.io/mimic](https://tts.smartgic.io/mimic/status)</div><div>Mimic 3 - [https://tts.smartgic.io/mimic3](https://tts.smartgic.io/mimic3/status)</div><div>Piper - [https://tts.smartgic.io/piper](https://tts.smartgic.io/piper/status)</div><div>S.A.M. - [https://tts.smartgic.io/sam](https://tts.smartgic.io/sam/status)</div> |
| <div><a href="https://github.com/builderjer"><img src="https://avatars.githubusercontent.com/u/34875857?v=4" alt="builderjer" width="25" height="25"></a></div>| TTS | [ovos-tts-server-plugin](https://github.com/OpenVoiceOS/ovos-tts-server-plugin) | <div>Mimic 3 - [https://mimic3.ziggyai.online](https://mimic3.ziggyai.online/status)</div><div>Piper - [https://pipertts.ziggyai.online](https://pipertts.ziggyai.online/status)</div> |
| <div><a href="https://github.com/NeonGeckoCom"><img src="https://avatars.githubusercontent.com/u/32377662?s=200&v=4" alt="Neon" width="25" height="25"></a></div>| TTS | [ovos-tts-server-plugin](https://github.com/OpenVoiceOS/ovos-tts-server-plugin) | <div>Coqui - [https://coqui.neonaibeta.com](https://coqui.neonaibeta.com/status)</div><div>Coqui 2 - [https://coqui.neonaialpha.com](https://coqui.neonaialpha.com/status)</div> <div>Coqui 3 - [https://coqui.neonaiservices.com](https://coqui.neonaiservices.com/status)</div>|

## Install

`pip install ovos-tts-server`

## Usage

```bash
ovos-tts-server --help
usage: ovos-tts-server [-h] [--engine ENGINE] [--port PORT] [--host HOST] [--cache]

options:
  -h, --help       show this help message and exit
  --engine ENGINE  tts plugin to be used
  --port PORT      port number
  --host HOST      host
  --cache          save every synth to disk
```

eg, to use the [GladosTTS plugin](https://github.com/NeonGeckoCom/neon-tts-plugin-glados) `ovos-tts-server --engine neon-tts-plugin-glados --cache`

then do a get request `http://192.168.1.112:9666/synthesize/hello`

## Docker

Pre built containers can be found in the [ovos-docker-tts](https://github.com/OpenVoiceOS/ovos-docker-tts) repository

### Template

```dockerfile
FROM python:3.7

RUN pip3 install ovos-utils==0.0.15
RUN pip3 install ovos-plugin-manager==0.0.4
RUN pip3 install ovos-tts-server==0.0.1

RUN pip3 install {PLUGIN_HERE}

ENTRYPOINT ovos-tts-server --engine {PLUGIN_HERE} --cache
```

build it
```bash
docker build . -t my_ovos_tts_plugin
```

run it
```bash
docker run -p 8080:9666 my_ovos_tts_plugin
```

use it `http://localhost:8080/synthesize/hello`

Each plugin can provide its own Dockerfile in its repository using ovos-tts-server