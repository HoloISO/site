### Prerequisites

- 8GB flash drive
- More than 8 GB RAM if you plan to use "Copy-To-RAM" option to install
- For normal builds (`rel/beta`): AMD GPU that supports RADV Drivers instead of Radeon (Southern Islands and Sea Islands require additional kernel cmdline property)
- For `dev_nv` builds: NVIDIA GPU above or equal to Maxwell generation
- UEFI-enabled device
- Disabled secure boot

### Installation

1. Flash the ISO from the [downloads](downloads.md) page using BalenaEtcher, Rufus with DD mode, or by typing `sudo dd if=SteamOS.iso of=(your flash drive) bs=4M status=progress oflag=sync`, or by simply throwing ISO into Ventoy drive.
2. Boot into ISO.
3. Click on "Install HoloISO on this device".
4. Follow on-screen instructions.
5. Take your favorite hot beverage, and wait 'til it installs :3.
6. Upon booting, you'll be greeted with Steam Deck's OOBE screen, from where you'll connect to your network, and login to your Steam account. From there, you can exit to KDE Plasma seamlessly by choosing Switch to desktop in the power menu.