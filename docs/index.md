# Welcome to the official HoloISO wiki!

HoloISO is a free (as in libre) reimplementation of SteamOS 3 (Holo), aimed at bringing the Steam Deck's SteamOS Holo redistribution into a generic, installable format. This project focuses on re-implementing proprietary components that the Steam client, operating system, gamescope, and user-created applications for Deck rely on, and it aims to provide a close-to-official SteamOS experience.

Click [here](https://t.me/HoloISO) to join the HoloISO Telegram update channel.

## Common Questions

### Is This Official?

No, but it is close to 99% of the way there. Most of the code and packages are straight from Valve, with zero possible edits. The ISO is being built from the same rootfs bootstrap as all HoloISO installations run.

### Hardware Support

#### CPU:

Most CPUs work fine, but people have reported an inoperable experience on 7xxx series CPUs. Work for these CPUs is undergoing.

#### WLAN/PCIe Additional Cards:

- Any pre-2021 WLAN Card works fine on Valve's 5.13 Neptune kernel.
- linux-zen provides support for ALL current cards.

#### Sound:

- Everything mostly works fine™.

#### GPU:

- AMD GPUs with RADV support are guaranteed to work fully stable.
- Intel GPUs (Random experience).

### Progress

#### Working Stuff

- Bootup
- SteamOS OOBE (Steam Deck UI First Boot Experience)
- Deck UI (separate session)
- Deck UI (-gamepadui)
- TDP/Clock control¹
- Global FSR
- Shader Pre-Caching
- Switch to Desktop from plasma/to plasma without user interference.
- Valve's exclusive Vapor appearance for KDE Plasma
- Cool-looking neofetch?
- A/B system updates with container sealing
- Native SteamOS addons for Deck devices, and core components such as: steamos-readonly and more

¹ - *Disabled for ALL systems except for Steam Deck/Steam Deck OLED (Valve Jupiter/Valve Gallileo) due to VERY LOW hardcoded TDP/Clock values, especially for dGPUs.*

#### Working Stuff on Steam Deck Compared to Other Distributions

- Dock Firmware updater (additionally installable in desktop by running steamos-readonly disable && sudo pacman -S jupiter-dock-updater-bin)
- Steam Deck BIOS, Controller firmware, OS firmware updater, support for thumbstick and haptic motor calibration, native amplifier (CS35L41) support
- New fan curve control
- TDP/Clock control

---

### Contribution

Contributions are welcome! If you encounter any issues or have suggestions for improvements, please open an [issue](https://github.com/HoloISO/issuetracker) or pull request on postcopy/buildroot parts of this organization.

---

_This is an unofficial reimplementation and is not affiliated with or endorsed by Valve Corporation._
