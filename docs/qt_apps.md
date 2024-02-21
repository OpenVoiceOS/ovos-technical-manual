# QT Applications

> **NOTE**: Only QT5 is supported, help wanted to migrate to QT6! 

You can build full standalone QT Voice Applications using [QML](https://openvoiceos.github.io/ovos-technical-manual/qt5/) with `ovos-gui` either via skills or [OVOSAbstractApp]()

## Desktop Files

GUI clients are allowed to filter a `namespace`, providing a GUI skill in it's own dedicated window. 

This is what powers [Plasma Bigscreen Voice Apps](https://plasma-bigscreen.org/docs/develop/) via .desktop files

Desktop files are also parsed to populate the skills in the [homescreen](https://github.com/OpenVoiceOS/skill-ovos-homescreen) app drawer

> **NOTE**: be sure to have [mycroft-gui-qt5](https://github.com/OpenVoiceOS/mycroft-gui-qt5) installed

```
[Desktop Entry]
X-DBUS-StartupType=None
X-KDE-StartupNotify=false
Version=1.0
Terminal=false
Type=Application
Name=OCP
Exec=ovos-gui-app --hideTextInput --skill=ovos.common_play
Icon=OCP
Categories=VoiceApp
StartupNotify=false
```

> Learn more about the [Desktop Entry Specification](https://specifications.freedesktop.org/desktop-entry-spec/desktop-entry-spec-latest.html)
