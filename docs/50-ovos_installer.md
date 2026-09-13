# How to Install Open Voice OS with the `ovos-installer`

Welcome to the quick-start guide for installing Open Voice OS (OVOS) using the official `ovos-installer`! This guide is suitable for **Raspberry Pi** and **desktop/server** Linux environments. Whether you’re running this on a headless Raspberry Pi or your everyday laptop, the steps are mostly the same—only the way you connect to the device differs.

> ⚠️ Note: Some “exotic” hardware (like ReSpeaker microphones or certain audio HATs) may require extra configuration. The installer aims for wide compatibility, but specialized setups might need some manual intervention.

Looking for a pre-built raspberry pi image instead? check out [raspOVOS](https://github.com/OpenVoiceOS/raspOVOS) and the companion [tutorial](https://openvoiceos.github.io/ovos-technical-manual/51-install_raspovos/)

---

## Step-by-step Installation

### ✅ 1. Connect to Your Device *(if remote)*

If you're installing on a headless device (like a Raspberry Pi), connect via SSH:

```bash
ssh -l your-username <your-device-ip>
```

---

### 🔄 2. Update Package Metadata

Make sure your package manager is up to date:

```bash
sudo apt update
```

---

### 📦 3. Install Prerequisites

Install `git` and `curl`—these are required to run the installer:

```bash
sudo apt install -y git curl
```

---

### 📥 4. Run the OVOS Installer

Now you're ready to kick off the installation process:

```bash
sudo sh -c "$(curl -fsSL https://raw.githubusercontent.com/OpenVoiceOS/ovos-installer/main/installer.sh)"
```

![image](https://gist.github.com/user-attachments/assets/8a87fd01-2570-419b-8154-159b2d5801cb)


---

## What Happens Next?

Once you run the script, the installer will:

- Perform system checks
- Install dependencies (Python, Ansible, etc.)
- Launch a **text-based user interface (TUI)** to guide you through the setup

This can take anywhere from **5 to 20 minutes**, depending on your hardware, internet speed, and storage performance. Now let’s walk through the installer screens!

---

## The Installer Wizard

Navigation:

- navigation is done via arrow keys
- pressing space selects options in the lists
    - eg. when selecting `virtualenv` or `containers`
- pressing tab will switch between the options and the `<next>`/`<back>` buttons
- pressing enter will execute the highligted `<next>`/`<back>` option

---

### 🌍 Language Selection

The first screen lets you select your preferred language. Just follow the on-screen instructions.

![image](https://gist.github.com/user-attachments/assets/61f9e089-1d54-49e9-8d4a-d5e1f6028ee2)

---

### 🧠 Environment Summary

You’ll be shown a summary of the detected environment—no action needed here. It’s just informative.

![image](https://gist.github.com/user-attachments/assets/1268a703-2007-4bc0-b153-36f33b782b20)

---

### 🧰 Choose Installation Method

You have two choices:

- **Virtualenv**: Recommended for most users. Easier to understand and manage.
- **Containers**: For advanced users familiar with Docker or Podman.

![image](https://gist.github.com/user-attachments/assets/e1b881fc-327d-4e1f-839b-396cffcd354c)

---

### 🌱 Choose Channel

Select the **“development”** channel. Once OVOS is production-ready, a “stable” channel will also be available.

![image](https://gist.github.com/user-attachments/assets/f782cebe-c86b-4474-93d7-894b712e8fe7)

---

### 🧪 Choose Profile

Pick the `ovos` profile. This is the classic, all-in-one Open Voice OS experience with all the necessary components running locally.

![image](https://gist.github.com/user-attachments/assets/0ff4279d-69fa-4ab8-b372-0fef263e6d7c)

---

### 🛠️ Feature Selection

Choose what features you’d like to install.

![image](https://gist.github.com/user-attachments/assets/bdb65ba6-18d6-42fd-aff6-22fab0826870)

> ⚠️ Note: Some features (like the GUI) may be unavailable on lower-end hardware like the Raspberry Pi 3B+.

---

### 🍓 Raspberry Pi Tuning *(if applicable)*

On Raspberry Pi boards, you’ll be offered system tweaks to improve performance. It's highly recommended to enable this!

![image](https://gist.github.com/user-attachments/assets/91bb5f18-9c5a-49ef-a0fe-5b0e52b44ee9)

---

### 🧾 Summary

Before the installation begins, you'll see a summary of your selected options. This is your last chance to cancel the process.

![image](https://gist.github.com/user-attachments/assets/62a565f3-6871-4dfe-a441-c482199feac0)

---

### 📊 Anonymous Telemetry

You'll be asked whether to share **anonymous usage data** to help improve Open Voice OS. Please consider opting in!

![image](https://gist.github.com/user-attachments/assets/b8015c41-370d-49d3-b783-996887cb421b)

The data collection only happens during the installation process, nothing else will be collected once the installation is over.

**The installer will ask you if you want to share or not the data.**

Below is a list of the collected data _(please have a look to the [Ansible tempalte](https://github.com/OpenVoiceOS/ovos-installer/blob/main/ansible/roles/ovos_installer/templates/telemetry.json.j2) used ti publish the data)_.

| Data                   | Description                                              |
| ---------------------- | -------------------------------------------------------- |
| `architecture`         | CPU architecture where OVOS was installed                |
| `channel`              | `stable` or `development` version of OVOS                |
| `container`            | OVOS installed into containers                           |
| `country`              | Country where OVOS has been installed                    |
| `cpu_capable`          | Is the CPU supports AVX2 or SIMD instructions            |
| `display_server`       | Is X or Wayland are used as display server               |
| `extra_skills_feature` | Extra OVOS's skills enabled during the installation      |
| `gui_feature`          | GUI enabled during the installation                      |
| `hardware`             | Is the device a Mark 1, Mark II or DevKit                |
| `installed_at`         | Date when OVOS has been installed                        |
| `os_kernel`            | Kernel version of the host where OVOS is running         |
| `os_name`              | OS name of the host where OVOS is running                |
| `os_type`              | OS type of the host where OVOS is running                |
| `os_version`           | OS version of the host where OVOS is running             |
| `profile`              | Which profile has been used during the OVOS installation |
| `python_version`       | What Python version was running on the host              |
| `raspberry_pi`         | Does OVOS has been installed on Raspberry Pi             |
| `skills_feature`       | Default OVOS's skills enabled during the installation    |
| `sound_server`         | What PulseAudio or PipeWire used                         |
| `tuning_enabled`       | Did the Rasperry Pi tuning feature wsas used             |
| `venv`                 | OVOS installed into a Python virtual environment         |

---

### 🧙‍♂️ Sit Back and Relax

The installation begins! This can take some time, so why not grab a coffee (or maybe a cupcake)? ☕🧁

Here is a demo of how the process should go if everything works as intended

[![asciicast](https://asciinema.org/a/710286.svg)](https://asciinema.org/a/710286)

---

## Installation Complete!

You’ve done it! OVOS is now installed and ready to serve you. Try saying things like:

- “What’s the weather?”
- “Tell me a joke.”
- “Set a timer for 5 minutes.”

![image](https://gist.github.com/user-attachments/assets/acbc71ed-46aa-4084-8f4c-82c6a2a19d49)

You’re officially part of the Open Voice OS community! 🎤✨

---

## Additional Configuration and Known Issues

Depending on your language you probably want to change the default plugins, the ovos-installer is not perfect and might not always select the best defaults

It is recommend that you run `ovos-config autoconfigure --help` after the initial install


[![asciicast](https://asciinema.org/a/710295.svg)](https://asciinema.org/a/710295)


---

## Troubleshooting

> Something went wrong?

Don’t panic! If the installer fails, it will generate a log file and upload it to [https://dpaste.com](https://dpaste.com). Please share that link with the community so we can help you out.

OVOS is a community-driven project, maintained by passionate volunteers. Your feedback, bug reports, and patience are truly appreciated.

