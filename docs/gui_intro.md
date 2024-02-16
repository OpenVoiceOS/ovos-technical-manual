# Introduction

OVOS devices with displays provide skill developers the opportunity to create skills that can be empowered by both voice and screen interaction. 

`ovos-gui`, aka, The GUI Service, is responsible for keeping track of what should be rendered, but does not perform the rendering itself

## Architecture

The GUI state is defined by `namespaces`, usually corresponding to a skill, each with any number of `pages`. `pages` are ordered, but only 1 page is rendered at a time.

GUI clients are allowed to filter a `namespace`, essentially simulating a GUI skill in it's own dedicated window. this is what powers plasma bigscreen Voice Apps via .desktop files

OpenVoiceOS components interact with the GUI by defining session data and active pages, gui-clients may also send back `events` to indicate interactions

The GUI clients may be implemented in any language, page templates are provided to skills via `self.gui.show_XXX` that should be implemented by all clients. 

sending custom pages from skills requires skill to explicitly support a client platform

## Introduction to QML

Currently only a [QT5 gui-client](https://github.com/OpenVoiceOS/mycroft-gui-qt5) is available, help wanted to [migrate to QT6](https://github.com/OVOSHatchery/mycroft-gui-qt6)!

The reference GUI client implementation is based on the QML user interface markup language that gives you complete freedom to create in-depth innovative interactions without boundaries or provide you with simple templates within the GUI framework that allow minimalistic display of text and images based on your skill development specifics and preferences.

QML user interface markup language is a declarative language built on top of Qt's existing strengths designed to describe the user interface of a program: both what it looks like, and how it behaves. QML provides modules that consist of sophisticated set of graphical and behavioral building elements.

A collection of resources to familiarize you with QML and Kirigami Framework.

* [Introduction to QML ](http://doc.qt.io/qt-5/qml-tutorial.html)
* [Introduction to Kirigami](https://www.kde.org/products/kirigami/)
