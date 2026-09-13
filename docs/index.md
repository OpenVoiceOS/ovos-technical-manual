# The OpenVoiceOS Technical Manual

![](https://github.com/OpenVoiceOS/ovos_assets/blob/master/Logo/ovos-logo-512.png?raw=true)

Welcome to the **Open Voice OS (OVOS)** developer documentation. This guide is your starting point for exploring, building, and contributing to OVOS — an open and modular platform for voice-enabled applications.

---

## What is OVOS?

**Open Voice OS (OVOS)** is a flexible voice platform that goes beyond traditional voice assistants. It provides the foundational tools and frameworks for integrating voice interaction into a wide range of projects.

While OVOS can power a “Hey Mycroft…”-style assistant, it is not limited to that use case. As a voice operating system, OVOS is highly customizable and has been used in:

- Robots and automation systems  
- Smart furniture and mirrors  
- Cloud-based voice services  
- Embedded devices and smart TVs  

OVOS is designed to work wherever voice interfaces are needed — whether that’s on a local device or in the cloud.

---

## OVOS Distros

If you’d rather not install and configure components manually, OVOS has several prebuilt distributions:

- [**raspOVOS**](https://github.com/OpenVoiceOS/raspOVOS): A Raspberry Pi image with pre-installed OVOS services.
- [**ovos-installer**](https://github.com/OpenVoiceOS/ovos-installer): Installs OVOS on top of supported Linux systems.
- [**ovos-buildroot**](https://github.com/OpenVoiceOS/ovos-buildroot): A highly customizable buildroot-based image (in development).

These distros offer a faster setup experience with curated default skills and settings.


---

## What You’ll Find in This Manual

This documentation includes:

- **Architecture Overview** – A breakdown of how OVOS components work together  
- **Plugin System** – Details on STT, TTS, wake word engines, GUI backends, and more  
- **Application Development** – How to create and deploy custom voice-enabled applications  
- **Testing and Debugging** – Tools and practices for developing with OVOS  

---

## Related Projects and External Resources

- **Docker Setup**: [OVOS Docker Documentation](https://openvoiceos.github.io/ovos-docker/)
- **Message Bus Reference**: [OVOS Message Spec](https://openvoiceos.github.io/message_spec/)
- **Remote Client Framework**: [HiveMind Documentation](https://jarbashivemind.github.io/HiveMind-community-docs/)

---

## Contributing to This Documentation

This manual is maintained by the OVOS community — developers, users, and contributors who help shape the platform.

Whether you’re spotting errors, clarifying instructions, or adding new information, contributions are always welcome.

To contribute, please [open an issue or pull request](https://github.com/OpenVoiceOS/ovos-technical-manual) on GitHub.

---

## Getting Started Tips

- OVOS is modular — you can run just one or two components to start.
- Try a pre-built distro for a plug-and-play experience.
- Check the message bus reference to see how OVOS components communicate.
- Explore real-world examples from the community to see OVOS in action.
