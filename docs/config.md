# Configuration

## Config Files

When the configuration loader starts, it looks in several locations byorder, and loads ALL configurations. 

Keys that exist in multiple configuration files will be overridden by the last file to contain the value. This process results in
a minimal amount being written for a specific device and user, without modifying default distribution files.

The main configuration files are commonly referred too as:

- **default** - the file shipped by the `ovos-config` package, containing default values
- **system** - located at `/etc/mycroft/mycroft.conf`, intended to be shipped by specific platforms such as pre built raspberry pi images for dedicated hardware or by your OS package manager
- **remote** - locate at `~/.config/mycroft/web_cache.json`, if using a backend to manage multiple devices this is where any downloaded config would be stored
- **user** - located at `~/.config/mycroft/mycroft.conf`, intended for users to modify, usually does not exist until created by a user

> users should **ALWAYS** change the file located at `~/.config/mycroft/mycroft.conf`, if it does not exist, create it!

## OS ENV vars

all XDG paths across OVOS take into account envvar `OVOS_CONFIG_BASE_FOLDER` when building the final path, this defaults to `"mycroft"`

> eg. if envvar`OVOS_CONFIG_BASE_FOLDER` is set to "myfolder" `~/.config/mycroft/mycroft.conf` becomes `~/.config/myfolder/mycroft.conf`

similiarly the config file name is determined  envvar `OVOS_CONFIG_FILENAME`, this defaults to `"mycroft.conf"` 

> eg. if `OVOS_CONFIG_FILENAME` is set to "myconfig.yaml" `~/.config/mycroft/mycroft.conf` becomes `~/.config/mycroft/myconfig.yaml`

Some individual configuration files **full path** can also be modified by environment variables, instead of dynamically determined via the previous settings. 

For backwards compatibility with mycroft-core previously existing Mycroft envvars are also respected

- `'OVOS_DEFAULT_CONFIG'` to replace the default mycroft.conf that ships with ovos-config package
- `'MYCROFT_SYSTEM_CONFIG'` to replace `/etc/mycroft/mycroft.conf`
- `'MYCROFT_WEB_CACHE'` to replace `$XDG_CONFIG_PATH/mycroft/web_cache.json`

> **NOTE:** Configuration files can be in either json or yaml format. json files must have `.json` or `.conf` file extensions, yaml files must have `.yml` or `.yaml` file extensions. This applies to full paths and to `OVOS_CONFIG_FILENAME`

## Configuring Configuration

There are a couple of special configuration keys that change the way the configuration stack loads.

### protected_keys

A `"protected_keys"` configuration section may be added to a `Default` or `System` Config file
(default `/etc/mycroft/mycroft.conf`). This configuration section specifies
other configuration keys that may not be specified in `remote` or `user` configurations.
Keys may specify nested parameters with `.` to exclude specific keys within nested dictionaries.
An example config could be:

```json
{
  "protected_keys": {
    "remote": [
      "gui_websocket.host",
      "websocket.host"
    ],
    "user": [
      "gui_websocket.host"
    ]
  }
}
```

This example specifies that `config['gui_websocket']['host']` may be specified in user configuration, but not remote.
`config['websocket']['host']` may not be specified in user or remote config, so it will only consider default
and system configurations.

### disable_user_config

If this config parameter is set to True in `Default` or `System` configuration,
no user configurations will be loaded (no XDG configuration paths).

### disable_remote_config

If this config parameter is set to True in `Default` or `System` configuration,
the remote configuration (`web_cache.json`) will not be loaded.

