### Prerequisites

- 8GB flash drive
- Around 60GB free space anywhere on the drive (You could put /home partition on any drive you wish.)
- More than 8 GB RAM if you plan to use "Copy-To-RAM" option to install
- For normal builds (`rel/beta`): AMD GPU that supports RADV Drivers instead of Radeon (Southern Islands and Sea Islands require additional kernel cmdline property)
- UEFI-enabled device
- Disabled secure boot

### Installation

1. Flash the ISO from the [downloads](downloads.md) page using BalenaEtcher, Rufus with DD mode, or by typing `sudo dd if=SteamOS.iso of=(your flash drive) bs=4M status=progress oflag=sync`, or by simply throwing ISO into Ventoy drive
2. Boot into ISO
3. Create following partitions (using gparted/parted/cfdisk, whichever suits you the most):
    - `holo_efi` with 256MB size, vfat filesystem
    - `holo_root` with minimum of 15GB size, btrfs filesystem
    - `holo_var` with minimum of 512MB size, ext4 filesystem
    - `holo_home` with no fixated size, with possibility to extended to max free space, ext4
4. Format those partitions:
    - `holo_efi`: mkfs -t vfat ${corresponding partition}
    - `holo_root`: mkfs -t btrfs -f ${corresponding partition}
    - `holo_var`: mkfs.ext4 -F ${corresponding partition}
    - `holo_home`: mkfs.ext4 -F ${corresponding partition}
5. Run `holoiso_bootstrap --username "username" --password "user password" --root_password "root password"`
6. Wait till it finishes, and reboot. 

### Migrating older dual-boot installations
To migrate pre-immutable dual-boot installations onto a new base, do following things:

1. Delete both `HOLOEFI` and `holo-root` partitions
2. On the available free space, create `holo_efi`, `holo_root`, `holo_var` partitions as described above
3. Change `holo-home` to `holo_home` label by `e2label /dev/disk/by-label/holo-home holo_home`
4. Run `holoiso_bootstrap --username "your old username" --password "your old user password" --root_password "root password"`
5. Wait till it finishes, and reboot.