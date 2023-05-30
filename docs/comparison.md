# Images compared

OpenVoiceOS ready to use images come in several flavours; The buildroot version, being the minimal consumer type of
image and the Manjaro version, being the full distribution easy / easier for developing. a headless raspbian image is also maintained by the community

Some friend projects also publish images that include OpenVoiceOS in some form, those are also listed here

Mycroft images are largely unmaintained and are also listed for comparison purposes, usage is not recommended


- [Operating System](#operating-system)
      * [OpenVoiceOS](#openvoiceos)
      * [Friends](#friends)
- [Plugins](#plugins)
      * [OpenVoiceOS](#openvoiceos-1)
      * [Friends](#friends-1)
- [Functionality](#functionality)
      * [OpenVoiceOS](#openvoiceos-2)
      * [Friends](#friends-2)
- [Configuration](#configuration)
      * [OpenVoiceOS](#openvoiceos-3)
      * [Friends](#friends-3)
- [Hardware](#hardware)
      * [OpenVoiceOS](#openvoiceos-4)
      * [Friends](#friends-4)
    

## Operating System


### OpenVoiceOS

|                                                | ovos-buildroot                     | ovos-raspbian                   |
|------------------------------------------------|------------------------------------|---------------------------------|
| **Operating System**                           |                                    |                                 |
| Base OS                                        | buildroot                          | raspbian                        |
| Last Updated - YYYY/MM/DD                      | 2023-0?-??                         | 2023-0?-??                      |
| **Customization**                              |                                    |                                 |  
| Open Build System                              | Yes                                | Yes<br>(pi-gen github workflow) |
| Package manager                                | N/A<br>(*No buildtools available*) | apt                             |
| **Software - Architecture**                    |                                    |                                 | 
| Core                                           | ovos-core                          | ovos-core                       |
| GUI                                            | ovos-shell                         | N/A                             |
| Launcher                                       | systemd<br>user session            | systemd<br>user session         |
| **Updating**                                   |                                    |                                 |
| Update mechanism                               | pip<br>(bash scripts)              | pip<br>(bash scripts)           |
| **Screen - GUI**                               |                                    |                                 |
| GUI supported<br>*Show GUI if screen attached* | eglfs                              | N/A                             |

### Friends

|                                                | NeonAI                    | Bigscreen                              |
|------------------------------------------------|---------------------------|----------------------------------------|
| **Operating System**                           |                           |                                        |
| Base OS                                        | debian                    | manjaro                                |
| Last Updated - YYYY/MM/DD                      | 2023-03 (regular updates) | [Rolling Release](https://github.com/manjaro-arm/bigscreen/releases)          |
| **Customization**                              |                           |                                        |
| Open Build System                              | Yes                       | [Manjaro ARM Tools](https://gitlab.manjaro.org/manjaro-arm/applications/manjaro-arm-tools)                               |
| Package manager                                | apt                       | pacman                                 |
| **Software - Architecture**                    |                           |                                        |
| Core                                           | neon-core                 | ovos-core                              |
| GUI                                            | ovos-shell                | Plasma Shell                           |
| Launcher                                       | systemd<br>system session | systemd<br>user session                |
| **Updating**                                   |                           |                                        |
| Update mechanism                               | PHAL plugin<br>(pip/git)  | package manager<br>(mycroft-bigscreen) |
| **Screen - GUI**                               |                           |                                        |
| GUI supported<br>*Show GUI if screen attached* | eglfs                     | X11/Wayland                            |


## Plugins

### OpenVoiceOS

|                         | ovos-buildroot                                    | ovos-raspbian                                     |
|-------------------------|---------------------------------------------------|---------------------------------------------------|
| **Default Plugins**     |                                                   |                                                   |
| VAD                     | ovos-vad-plugin-webrtcvad                         | ovos-vad-plugin-webrtcvad                         |
| stt                     | ovos-stt-plugin-server<br>(ovos proxy)            | ovos-stt-plugin-server<br>(ovos proxy)            |
| fallback_stt            | N/A                                               | N/A                                               |
| tts                     | ovos-tts-plugin-mimic3-server<br>(public servers) | ovos-tts-plugin-mimic3-server<br>(public servers) |
| fallback_tts            | ovos-tts-plugin-mimic                             | ovos-tts-plugin-mimic                             |
| **Recommended Plugins** |                                                   |                                                   | 
| STT - On device         | ovos-stt-plugin-vosk                              | ovos-stt-plugin-vosk                              |
| STT - On premises       | ovos-stt-plugin-server<br>(any plugin)            | ovos-stt-plugin-server<br>(any plugin)            |
| STT - Cloud             | ovos-stt-plugin-server<br>(ovos proxy)            | ovos-stt-plugin-server<br>(ovos proxy)            | 
| TTS - On device         | ovos-tts-plugin-mimic3                            | ovos-tts-plugin-mimic3                            | 
| TTS - On premises       | ovos-tts-plugin-server<br>(any plugin)            | ovos-tts-plugin-server<br>(any plugin)            |
| TTS - Cloud             | ovos-tts-plugin-mimic3-server<br>(public servers) | ovos-tts-plugin-mimic3-server<br>(public servers) | 
| VAD                     | ovos-vad-plugin-webrtcvad                         | ovos-vad-plugin-silero                            | 


### Friends

|                         | NeonAI                                         | Bigscreen                                         |
|-------------------------|------------------------------------------------|---------------------------------------------------|
| **Default Plugins**     |                           	                   |                                                    |
| VAD                     | ovos-vad-plugin-webrtcvad                      | ovos-vad-plugin-webrtcvad                         | 
| stt                     | neon-stt-plugin-google-cloud-streaming         | ovos-stt-plugin-server<br>(ovos proxy)            |
| fallback_stt            | ovos-stt-plugin-vosk                           | N/A                                               |
| tts                     | neon-tts-plugin-coqui-remote                   | ovos-tts-plugin-mimic2                            |
| fallback_tts            | neon-tts-plugin-coqui                          | ovos-tts-plugin-mimic                             |
| **Recommended Plugins** |                           	                    |                                                    |
| STT - On device         | ovos-stt-plugin-vosk                           | ovos-stt-plugin-vosk                              |
| STT - On premises       | neon-stt-plugin-nemo                           | ovos-stt-plugin-server<br>(any plugin)            |
| STT - Cloud             | neon-stt-plugin-google-cloud-streaming         | ovos-stt-plugin-server<br>(ovos proxy)            |
| TTS - On device         | neon-tts-plugin-coqui                          | ovos-tts-plugin-mimic3                            |
| TTS - On premises       | ovos-stt-plugin-server + neon-tts-plugin-coqui | ovos-tts-plugin-server<br>(any plugin)            |
| TTS - Cloud             | neon-tts-plugin-coqui-remote                   | ovos-tts-plugin-mimic3-server<br>(public servers) |
| VAD                     | ovos-vad-plugin-webrtcvad                      | ovos-vad-plugin-webrtcvad                         |


## Functionality

MNOs - Mycroft/Neon/OVOS

### OpenVoiceOS

|                                    | ovos-buildroot                      | ovos-raspbian                       |
|------------------------------------|-------------------------------------|-------------------------------------|
| **Skill Frameworks**               |                                     |                                     |
| Mycroft Audio Service<br>(bus api) | Yes                                 | Yes                                 |
| Adapt Intents                      | Yes                                 | Yes                                 | 
| Padatious Intents                  | Yes                                 | Yes                                 | 
| OCP Skills                         | Yes                                 | Partial<br>(audio only)             |
| MNOS Common Query Skills           | Yes                                 | Yes                                 | 
| MNOs Fallback Skills               | Yes                                 | Yes                                 |
| MNOs Skills                        | Yes                                 | Yes                                 |
| Mycroft Common Play Skills         | Partial<br>(imperfect compat layer) | Partial<br>(imperfect compat layer) |
| **Media Frameworks**               |                                     |                                     | 
| OCP                                | Yes                                 | Yes                                 | 
| MPRIS                              | Yes                                 | Yes (?)                             |
| KDEConnect                         | Yes                                 | No (?)                              |
| Spotify Daemon                     | Yes                                 | No                                  |
| Airplay                            | Yes                                 | No                                  |
| Bluetooth Speaker                  | WIP                                 | No                                  |
| **IOT Frameworks**                 |                                     |                                     |     
| HomeAssistant integration          | **WIP**<br>*HomeAssistant<br>PHAL Plugin* | **WIP**<br>*HomeAssistant<br>PHAL Plugin*<br>(manual install) |    

### Friends

|                                 	 | NeonAI                              | Bigscreen                           |
|------------------------------------|-------------------------------------|-------------------------------------| 
| **Skill Frameworks**               |                                     |                                     |  
| Mycroft Audio Service<br>(bus api) | Yes                                 | Yes                                 |
| Adapt Intents                      | Yes                                 | Yes                                 | 
| Padatious Intents                  | Yes                                 | Yes                                 | 
| OCP Skills                         | Yes                                 | Yes                                 | 
| MNOS Common Query Skills           | Yes                                 | Yes                                 |
| MNOs Fallback Skills               | Yes                                 | Yes                                 | 
| MNOs Skills                        | Yes                                 | Yes                                 | 
| Mycroft Common Play Skills         | Partial<br>(imperfect compat layer) | Partial<br>(imperfect compat layer) |
| **Media Frameworks**               |                                     |                                     |
| OCP                                | Yes                                 | Yes                                 | 
| MPRIS                              | Yes (?)                             | Yes                                 | 
| KDEConnect                         | No (?)                              | Yes (?)                             |
| Spotify Daemon                     | No                                  | No (?)                              | 
| Airplay                            | No                                  | No (?)                              | 
| Bluetooth Speaker                  | No                                  | No (?)                              | 
| **IOT Frameworks**                 |                                           |                                           |                            
| HomeAssistant integration          | **WIP**<br>*HomeAssistant<br>PHAL Plugin* | **WIP**<br>*HomeAssistant<br>PHAL Plugin* |


## Configuration

### OpenVoiceOS

|                             | ovos-buildroot           | ovos-raspbian                               |
|-----------------------------|--------------------------|---------------------------------------------|
| **Configuration - Option**  |                          |                                             |
| Data privacy                |  Yes                     | Yes                                         |                       
| Offline mode                | Yes  <br> (setup skill)  | yes  <br> (need to change default plugins)  |                      
| Color theming               | Yes                      | No                                          |                      
| Non-Pairing mode            | Yes  <br> (setup skill)  | Yes<br> (preconfigured default)             |                      
| API Access w/o pairing      | Yes                      | Yes                                         |                      
| On-Screen configuration     | Yes                      | No                                          |                     
| Online configuration        | personal-backend         | personal-backend                            |                     
| **Network Setup - Options** |                                           |                       |                          | 
| Mobile WiFi Setup<br>*Easy device "hotspot"<br>to connect from phone*   | Yes                   | No                       |                       
| On device WiFi Setup<br>*Configure the connection<br>directly on screen*| Yes                   | No                       |                     
| On screen keyboard                                                      | Yes                   | No                       |                     
| Reconfigure network<br>*Easy way to change the<br>network settings*     | Yes <br> (on screen)  | Yes  <br> (raspi-config) |                      


### Friends

|         	| NeonAI | Bigscreen |
|-----------|--------|-----------| 
| **Network Setup - Options**                                                                                |
| Mobile WiFi Setup<br>*Easy device "hotspot"<br>to connect from phone*                                      |                        No                        |                       No                       |
| On device WiFi Setup<br>*Configure the connection<br>directly on device*                                |                                                                     Yes                        |                      Yes                       |
| On screen keyboard                                                                                         |                                                                               Yes                        |                       Yes                       |
| Reconfigure network<br>*Easy way to change the<br>network settings*                                            |                                                                            Yes                        |                      Yes                       |
| **Configuration - Option**                                                                                  |
| Data privacy                                                                                               |                                                                           Yes                        |                     Yes                     |
| Offline mode                                                                                               |                                                                       Yes                        |                       Yes                        | 
| Color theming                                                                                              |                                                                           Yes                        |                       No                        | 
| Non-Pairing mode                                                                                           |                                                                     Yes                        |                       Yes                        |
| API Access w/o pairing                                                                                     |                                                                              Yes                        |                       Yes                        |
| On-Device configuration                                                                                     |                                                                             Yes                        |                       ?                        | 
| Online configuration                                                                                        |                                                   *WIP*                       |                       ?                       | 


## Hardware

### OpenVoiceOS

|                              | ovos-buildroot                | ovos-raspbian                        |
|------------------------------|-------------------------------|--------------------------------------|
| **Hardware - Compatibility** |
| Raspberry Pi 3B              | **WIP**                       | Yes                                  |   
| Raspberry Pi 3B+             | **WIP**                       | Yes                                  | 
| Raspberry Pi 3A+             | **WIP**                       | Yes                                  |   
| Raspberry Pi 4               | Yes                           | Yes                                  | 
| X86_64                       | *planned*                     | No                                   |                     
| Mark-1                       | **WIP**                       | No<br>*possibly in future*           |                        
| Mark-2                       | **WIP**<br>(no leds)          | partial<br>(no gui + manual install) |           
| Mark-2  (dev kit)            | Yes                           | partial<br>(no gui + manual install) |           
| **Hardware - Peripherals**   |
| ReSpeaker  2-mic             | Yes (?)                       | manual install (?)                   |        
| ReSpeaker  4-mic squared     | Yes (?)                       | manual install (?)                   |        
| ReSpeaker  4-mic linear      | Yes (?)                       | manual install (?)                   |  
| ReSpeaker  6-mic             | Yes (?)                       | manual install (?)                   |                
| USB                          | Yes                           | Yes                                  |                    
| SJ-201                       | Yes                           | manual install (?)                   |                   
| Google AIY v1                | manual install (?)            | manual install (?)                   |                     
| Google AIY v2                | No<br>*perhaps in the future* | manual install (?)                   |                   


### Friends

|         	| NeonAI | Bigscreen |
|-----------|--------|-----------|
| **Hardware - Compatibility** |
| Raspberry Pi 3B              | No                 | No                   | 
| Raspberry Pi 3B+             | No                 | No                   | 
| Raspberry Pi 3A+             | No                 | No                   | 
| Raspberry Pi 4               | Yes                | Yes                  | 
| X86_64                       | No                 | No                   |                   
| Mark-1                       | No                 | No                   |                      
| Mark-2                       | Yes                | No                   |         
| Mark-2  (dev kit)            | Yes                | No                   | 
| **Hardware - Peripherals**   |
| ReSpeaker  2-mic             | manual install (?) | manual install (?)   | 
| ReSpeaker  4-mic squared     | manual install (?) | manual install (?)   |   
| ReSpeaker  4-mic linear      | manual install (?) | manual install (?)   |
| ReSpeaker  6-mic             | manual install (?) | manual install (?)   |          
| USB                          | Yes                | Yes                  |                 
| SJ-201                       | Yes                | No                   |              
| Google AIY v1                | manual install (?) | manual install (?)   |                 
| Google AIY v2                | manual install (?) | manual install (?)   |              
                
