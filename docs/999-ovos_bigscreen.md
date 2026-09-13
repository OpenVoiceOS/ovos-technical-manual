# Plasma Bigscreen - OVOS Edition

**EXPERIMENTAL** - experimental repository

> **WARNING** - Not actively maintained, this fork is essentially a snapshot in time since Plasma Bigscreen dropped support for OVOS and moved to QT6

## Introduction

A big launcher giving you easy access to any installed apps and skills.
Controllable via voice or TV remote.

This project is using various open-source components like Plasma Bigscreen, OpenVoiceOS and libcec.

![ovos-bigscreen](https://github.com/OpenVoiceOS/ovos-plasma-bigscreen/assets/33701864/afcc5e15-146b-4f38-be8d-0e5a56acaa55)

This is a fork from https://invent.kde.org/plasma/plasma-bigscreen/

Changes:

- moves from Mycroft to OVOS 

- "mycroft" is no longer optional and it's integration is enabled by default

- Remove MycroftSkillInstaller (not OVOS compliant)

- Remove "Recent" section

- Remove generic "Applications" section

- Add "Media" section

- Add "Network" section

- Add "Graphics" section

## Voice Control

Bigscreen supports OpenVoiceOS, a free and open-source voice assistant that can be run completely decentralized on your own server.

Download new apps (aka skills) for your Bigscreen or add your own ones for others to enjoy.

## Remote control your TV via CEC

CEC (Consumer Electronics Control) is a standard to control devices over HDMI.
Use your normal TV remote control, or a RC with built-in microphone for voice control and optional mouse simulation.

## Application Launcher

Bigscreen replaces your DE, to stop an application from showing up you can edit `/etc/xdg/applications-blacklistrc`

Adding new applications only requires a [.desktop file](https://specifications.freedesktop.org/desktop-entry-spec/desktop-entry-spec-latest.html)

see [plasma-bigscreen/bigscreen-image-settings](https://invent.kde.org/plasma-bigscreen/bigscreen-image-settings) for more settings you might want to tweak in a Bigscreen image

## Installing from source

```bash
mkdir build
cd build
cmake .. -DCMAKE_INSTALL_PREFIX=/usr -DCMAKE_BUILD_TYPE=Release   -DKDE_INSTALL_LIBDIR=lib -DKDE_INSTALL_USE_QT_SYS_PATHS=ON -DCMAKE_CXX_COMPILER=clazy
make
sudo make install
```

## Running

To start the Bigscreen homescreen in a window, run:

```
QT_QPA_PLATFORM=wayland dbus-run-session kwin_wayland "plasmashell -p org.kde.plasma.mycroft.bigscreen"
```

you can also select [plasma-bigscreen-x11](bin/plasma-bigscreen-x11) on your login screen as DE

## Related repositories

- Image Settings for Bigscreen https://invent.kde.org/plasma-bigscreen/bigscreen-image-settings
- Plasma Remote Controllers https://invent.kde.org/plasma-bigscreen/plasma-remotecontrollers
- ovos-gui-app - https://github.com/OpenVoiceOS/mycroft-gui-qt5
- bigscreen gui extension https://github.com/OpenVoiceOS/ovos-gui-plugin-bigscreen