# OpenVoiceOS Translate Server

Turn any OVOS Language plugin into a microservice!

## Install

`pip install ovos-translate-server`

## Companion Plugin

Use with OpenVoiceOS [companion plugin](https://github.com/OpenVoiceOS/ovos-translate-server-plugin)

## Public Servers

| Member                                                                                                                                                          |  Service  |                                           Plugin                                            |                                                                          URL                                                                           |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------:|:-------------------------------------------------------------------------------------------:|:------------------------------------------------------------------------------------------------------------------------------------------------------:|
| <div><a href="https://github.com/OpenVoiceOS"><img src="https://avatars.githubusercontent.com/u/72275918?&v=4" alt="OVOS" width="25" height="25"></a></div>     | Translate | [ovos-translate-plugin-server](https://github.com/OpenVoiceOS/ovos-translate-server-plugin) |        [NLLB](https://github.com/OpenVoiceOS/ovos-translate-plugin-nllb) - [https://nllb.openvoiceos.org](https://nllb.openvoiceos.org/status)         |
| <div><a href="https://github.com/builderjer"><img src="https://avatars.githubusercontent.com/u/34875857?v=4" alt="builderjer" width="25" height="25"></a></div> | Translate | [ovos-translate-plugin-server](https://github.com/OpenVoiceOS/ovos-translate-server-plugin) |     [NLLB](https://github.com/OpenVoiceOS/ovos-translate-plugin-nllb) - [https://ovosnllb.ziggyai.online](https://ovosnllb.ziggyai.online/status)      |
| <div><a href="https://github.com/goldyfruit"><img src="https://avatars.githubusercontent.com/u/614115?v=4" alt="goldyfruit" width="25" height="25"></a></div>   | Translate | [ovos-translate-plugin-server](https://github.com/OpenVoiceOS/ovos-translate-server-plugin) | [NLLB](https://github.com/OpenVoiceOS/ovos-translate-plugin-nllb) - [https://translator.smartgic.io/nllb](https://translator.smartgic.io/nllb/status)  |
| <div><a href="https://github.com/OpenVoiceOS"><img src="https://avatars.githubusercontent.com/u/72275918?&v=4" alt="OVOS" width="25" height="25"></a></div>     |  Detect   | [ovos-translate-plugin-server](https://github.com/OpenVoiceOS/ovos-translate-server-plugin) |        [lingua-podre](https://github.com/OpenVoiceOS/lingua-podre) - [https://nllb.openvoiceos.org/detect](https://nllb.openvoiceos.org/status)        |
| <div><a href="https://github.com/builderjer"><img src="https://avatars.githubusercontent.com/u/34875857?v=4" alt="builderjer" width="25" height="25"></a></div> |  Detect   | [ovos-translate-plugin-server](https://github.com/OpenVoiceOS/ovos-translate-server-plugin) |     [lingua-podre](https://github.com/OpenVoiceOS/lingua-podre) - [https://ovosnllb.ziggyai.online/detect](https://ovosnllb.ziggyai.online/status)     |
| <div><a href="https://github.com/goldyfruit"><img src="https://avatars.githubusercontent.com/u/614115?v=4" alt="goldyfruit" width="25" height="25"></a></div>   |  Detect   | [ovos-translate-plugin-server](https://github.com/OpenVoiceOS/ovos-translate-server-plugin) | [lingua-podre](https://github.com/OpenVoiceOS/lingua-podre) - [https://translator.smartgic.io/nllb/detect](https://translator.smartgic.io/nllb/status) |

## Usage

```bash
ovos-translate-server --help
usage: ovos-translate-server [-h] [--tx-engine TX_ENGINE]
                   [--detect-engine DETECT_ENGINE] [--port PORT] [--host HOST]

optional arguments:
  -h, --help            show this help message and exit
  --tx-engine TX_ENGINE
                        translate plugin to be used
  --detect-engine DETECT_ENGINE
                        lang detection plugin to be used
  --port PORT           port number
  --host HOST           host

```

eg, to use the [NLLB plugin](https://github.com/OpenVoiceOS/ovos-translate-plugin-nllb) for translation,
and [Lang Classifier Classics](https://github.com/OpenVoiceOS/ovos-lang-detector-classics-plugin) for detection

`ovos-translate-server --tx-engine ovos-translate-plugin-nllb --detect-engine ovos-lang-detector-classics-plugin`

then you can do get requests

- `http://0.0.0.0:9686/translate/en/o meu nome é Casimiro` (auto detect source lang)
- `http://0.0.0.0:9686/translate/pt/en/o meu nome é Casimiro`  (specify source lang)
- `http://0.0.0.0:9686/detect/o meu nome é Casimiro`

## Docker

Pre built containers can be found in the [ovos-docker-tx](https://github.com/OpenVoiceOS/ovos-docker-tx) repository

### Template

you can create easily crete a docker file to serve any plugin

```dockerfile
FROM python:3.7

RUN pip3 install ovos-utils==0.0.15
RUN pip3 install ovos-plugin-manager==0.0.4
RUN pip3 install ovos-translate-server==0.0.1

RUN pip3 install {PLUGIN_HERE}

ENTRYPOINT ovos-translate-server --tx-engine {PLUGIN_HERE} --detect-engine {PLUGIN_HERE}
```

build it

```bash
docker build . -t my_ovos_translate_plugin
```

run it

```bash
docker run -p 8080:9686 my_ovos_translate_plugin
```

Each plugin can provide its own Dockerfile in its repository using ovos-translate-server