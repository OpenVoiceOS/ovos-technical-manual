# OVOS Personal Backend

Personal mycroft backend alternative to mycroft.home, written in flask

This repo is an alternative to the backend meant for personal usage, this allows you to run without mycroft servers

**there are no user accounts**

This is NOT meant to provision third party devices, but rather to run on the mycroft devices directly or on a private
network

NOTE: There is no website for pairing, devices will just activate themselves and work

## Public Servers

| Member | Service | Needs Auth | URL |
|---|:---:|:---:|:---:|
| <div><a href="https://github.com/builderjer"><img src="https://avatars.githubusercontent.com/u/34875857?v=4" alt="builderjer" width="25" height="25"></a></div> | Personal Backend | No | <div>[https://ovospublicbackend.ziggyai.online](https://ovospublicbackend.ziggyai.online)</div> |

> **WARNING**: public servers are meant for demo purposes, you are strongly encouraged to self host a backend instead!

## Usage

from pip

```bash
pip install ovos-local-backend
```

start backend

```bash
$ ovos-local-backend -h
usage: ovos-local-backend [-h] [--flask-port FLASK_PORT] [--flask-host FLASK_HOST]

optional arguments:
  -h, --help            show this help message and exit
  --flask-port FLASK_PORT
                        Mock backend port number
  --flask-host FLASK_HOST
                        Mock backend host

```

## Classic Mycroft devices

With selene gone, personal backend is the only option to keep the lights on for classic mycroft devices

There are 2 main intended ways to run local backend with mycroft

- on same device as mycroft-core, tricking it to run without mycroft servers
- on a private network, to manage all your devices locally

NOTE: you can not fully run mycroft-core offline, it refuses to launch without internet connection, you can only replace
the calls to use this backend instead of mycroft.home

We recommend you use [ovos-core](https://github.com/OpenVoiceOS/ovos-core) instead

update your mycroft config to use this backend, delete `identity2.json` and restart mycroft

```json
{
  "server": {
    "url": "http://0.0.0.0:6712",
    "version": "v1",
    "update": true,
    "metrics": true
  },
  "listener": {
    "wake_word_upload": {
      "url": "http://0.0.0.0:6712/precise/upload"
    }
  }
}
```

## Backend Manager

a simple UI for [ovos-personal-backend](https://github.com/OpenVoiceOS/ovos-personal-backend), utility to manage all
your devices

![](https://github.com/OpenVoiceOS/ovos-backend-manager/raw/dev/screenshots/demo.gif)

`pip install ovos-backend-manager`

`ovos-backend-manager` will be available in the command line after installing

## Docker

There is also a docker container you can use

```bash
docker run -p 8086:6712 -d --restart always --name local_backend ghcr.io/openvoiceos/local-backend:dev
```

a `docker-compose.yml` could look like this

```yaml
version: '3.6'
services:
  # ...
  ovosbackend:
    container_name: ovos_backend
    image: ghcr.io/openvoiceos/local-backend:dev
    # or build from local source (relative to docker-compose.yml)
    # build: ../ovos/ovos-personal-backend/.
    restart: unless-stopped
    ports:
      - "6712:6712"                                              # default port backend API
      - "36535:36535"                                            # default port backend-manager
    volumes: # <host>:<guest>:<SELinux flag>
      - ./ovos/backend/config:/root/.config/json_database:z      # shared config directory
      - ./ovos/backend/data:/root/.local/share/ovos_backend:Z    # shared data directory
      # set `data_path` to `/root/.local/share/ovos_backend`
```

about [selinux flags](https://docs.docker.com/storage/bind-mounts/#configure-the-selinux-label) (omit if you don't deal
with selinux)

## How it works

### Configuration

configure backend by editing/creating ```~/.config/ovos_local_backend/ovos_backend.json```


```json
{
  "stt": {
    "module": "ovos-stt-plugin-server",
    "ovos-stt-plugin-server": {
      "url": "https://stt.openvoiceos.com/stt"
    }
  },
  "backend_port": 6712,
  "geolocate": true,
  "override_location": false,
  "api_version": "v1",
  "data_path": "~",
  "record_utterances": false,
  "record_wakewords": false,
  "wolfram_key": "$KEY",
  "owm_key": "$KEY",
  "lang": "en-us",
  "date_format": "DMY",
  "system_unit": "metric",
  "time_format": "full",
  "default_location": {
    "city": {
      "...": "..."
    },
    "coordinate": {
      "...": "..."
    },
    "timezone": {
      "...": "..."
    }
  }
}
```

- stt config follows the same format of mycroft.conf and
  uses [ovos-plugin-manager](https://github.com/OpenVoiceOS/OVOS-plugin-manager)
- set wolfram alpha key for wolfram alpha proxy expected by official mycroft skill
- set open weather map key for weather proxy expected by official mycroft skill
- if record_wakewords is set, recordings can be found at `DATA_PATH/wakewords`
- if record_utterances is set, recordings can be found at `DATA_PATH/utterances`

### Device Settings

Each paired device has a few settings that control behaviour backend side

- `name` - default `"Device-{uuid}"`, friendly device name for display
- `opt_in` - default `True`, flag to control if metrics and speech from this device will be saved
- `device_location` - default `"unknown"`, friendly name for indoor location
- `email` - default from backend config, email to send notifications to
- `isolated_skills` - default `False`, flag to control if skill settings are shared across devices (ovos only)

In selene this info would be populated during pairing process, in local backend it needs to be updated manually

- you can change these settings per device via the [admin api](./ovos_local_backend/backend/admin.py)
- you can also change these settings per device by manually editing paired devices database

### Location

Device location can be updated via the backend, mycroft-core will request this info on its own from time to time

default values comes from the local backend config file

```json
{
  "geolocate": true,
  "override_location": false,
  "default_location": {
    "city": {
      "...": "..."
    },
    "coordinate": {
      "...": "..."
    },
    "timezone": {
      "...": "..."
    }
  }
}
```

- if override location is True, then location will be set to configured default value
- if geolocate is True then location will be set from your ip address
- you can set a default location per device via the [admin api](./ovos_local_backend/backend/admin.py)
- you can also set a default location per device by manually editing paired devices database

### Device Preferences

Some settings can be updated via the backend, mycroft-core will request this info on its own from time to time

default values comes from the local backend config file

```json
{
  "lang": "en-us",
  "date_format": "DMY",
  "system_unit": "metric",
  "time_format": "full"
}
```

- these settings are also used for wolfram alpha / weather default values
- you can set these values per device via the [admin api](./ovos_local_backend/backend/admin.py)
- you can also set these values per device by manually editing paired devices database

### Skill settings

in selene all device share skill settings, with local backend you can control this per device via `isolated_skills` flag

"old selene" supported a single endpoint for both skill settings and settings meta, this allowed devices both to
download and upload settings

"new selene" split this into two endpoints, settingsMeta (upload only) and settings (download only), this disabled
two-way sync across devices

- you can set `isolated_skills` per device via the [admin api](./ovos_local_backend/backend/admin.py)
- you can also set `isolated_skills` per device by manually editing paired devices database
- both endpoints are available, but mycroft-core by default will use the new endpoints and does not support two-way sync
- you can edit settings by using the "old selene" endpoint
- you can also edit settings by manually editing settings database

### Email

Mycroft skills can request the backend to send an email to the account used for pairing the device

- Email will be sent to a pre-defined recipient email since there are no user accounts
- you can set a recipient email per device via the [admin api](./ovos_local_backend/backend/admin.py)
- you can set a recipient email per device by manually editing paired devices database

with the local backend you need to configure your own SMTP server and recipient email, add the following section to your
.conf

```json
{
  "email": {
    "smtp": {
      "username": "sender@gmail.com",
      "password": "123456",
      "host": "",
      "port": 465
    },
    "recipient": "receiver@gmail.com"
  }
}
```

If using gmail you will need to [enable less secure apps](https://hotter.io/docs/email-accounts/secure-app-gmail/)

## Project Timeline

- Jan
  2018 - [initial release](https://github.com/OpenVoiceOS/OVOS-mock-backend/tree/014389065d3e5c66b6cb85e6e77359b6705406fe)
  of reverse engineered mycroft backend - by JarbasAI
- July 2018 - Personal backend [added to Mycroft Roadmap](https://mycroft.ai/blog/many-roads-one-destination/)
- October 2018 - Community [involved in discussion](https://mycroft.ai/blog/mycroft-personal-server-conversation/)
- Jan 2019 - JarbasAI
  implementation [adopted by Mycroft](https://github.com/MycroftAI/personal-backend/tree/31ee96a8189d96f8102276bf4b9073811ee9a9b2)
    - NOTE: this should have been a fork or repository transferred, but was a bare clone
    - Original repository was archived
- October 2019 - Official mycroft
  backend [open sourced under a viral license](https://mycroft.ai/blog/open-sourcing-the-mycroft-backend/)
- Jun 2020 - original project [repurposed to be a mock backend](https://github.com/OpenJarbas/ZZZ-mock-backend) instead
  of a full alternative, [skill-mock-backend](https://github.com/JarbasSkills/skill-mock-backend) released
- Jan 2021 - mock-backend adopted by OpenVoiceOS, original repo unarchived and ownership transferred
