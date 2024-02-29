# Welcome to the official HoloISO wiki!

HoloISO is a free (as in libre) reimplementation of SteamOS 3 (Holo), aimed at bringing the Steam Deck's SteamOS Holo redistribution into a generic, installable format. This project focuses on re-implementing proprietary components that the Steam client, operating system, gamescope, and user-created applications for Deck rely on, and it aims to provide a close-to-official SteamOS experience.

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
- NVIDIA GPUs (Beta).
- Intel GPUs (Random experience).

### Progress

#### Working Stuff

- Bootup
- SteamOS OOBE (Steam Deck UI First Boot Experience)
- Deck UI (separate session)
- Deck UI (-gamepadui)
- TDP/FPS limiting (*0)
- Global FSR
- Shader Pre-Caching
- Switch to Desktop from plasma/to plasma without user interference.
- Valve's exclusive Vapor appearance for KDE Plasma
- Cool-looking neofetch?
- A/B system updates with container sealing
- Native SteamOS addons for Deck devices, and core components such as: steamos-readonly and more

#### Working Stuff on Steam Deck Compared to Other Distributions

- Dock Firmware updater (additionally installable in desktop by running steamos-readonly disable && sudo pacman -S jupiter-dock-updater-bin)
- Steam Deck BIOS, Controller firmware, OS firmware updater, support for thumbstick and haptic motor calibration, native amplifier (CS35L41) support
- New fan curve control
- TDP/Clock control (*0) Disabled for ALL systems except for Steam Deck/Steam Deck OLED (Valve Jupiter/Valve Gallileo) due to VERY LOW hardcoded TDP/Clock values, especially for dGPUs.

## Installation Process

### Prerequisites

- 8GB flash drive
- More than 8 GB RAM if you plan to use "Copy-To-RAM" option to install
- AMD GPU that supports RADV Drivers instead of Radeon (Southern Islands and Sea Islands require additional kernel cmdline property), NVIDIA GPU equal or above to Maxwell generation
- UEFI-enabled device
- Disabled secure boot

### Installation

1. Flash the ISO from releases using BalenaEtcher, Rufus with DD mode, or by typing `sudo dd if=SteamOS.iso of=/dev/sd(your flash drive) bs=4M status=progress oflag=sync`, or by simply throwing ISO into Ventoy drive.
2. Boot into ISO.
3. Click on "Install HoloISO on this device".
4. Follow on-screen instructions.
5. Take your favorite hot beverage, and wait 'til it installs :3.
6. Upon booting, you'll be greeted with Steam Deck's OOBE screen, from where you'll connect to your network, and login to your Steam account. From there, you can exit to KDE Plasma seamlessly by choosing Switch to desktop in the power menu.

Click [here](https://t.me/HoloISO) to join the HoloISO Telegram update channel.

---

### Contribution

Contributions are welcome! If you encounter any issues or have suggestions for improvements, please open an [issue](https://github.com/holoiso-staging/issuetracker) or pull request on postcopy/buildroot parts of this organization.

---

_This is an unofficial implementation and is not affiliated with or endorsed by Valve Corporation._
