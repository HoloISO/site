# Documentation
HoloISO is a work-in-progress distribution that does not guarantee total compatibility with systems due to it being a total reimplementation of what Valve ships on Steam Deck.

## The filesystem structure
The partition layout and boot scheme is pre-defined and cannot be changed neither by us, or by the end user, and looks exactly like this:
```bash
- holo_efi
- holo_root
    - /rootfs
- holo_var
    - /overlays/etc/upper
    - /overlays/etc/work
- holo_home
    - /.steamos/offload/*
```
- `holo_efi` houses the GRUB EFI exectuable that your computer boots upon POSTing
- `holo_root` is the root filesystem container that contains HoloISO subvolumes, and has following abilities:
    - Each existing subvolume can be sealed (set to be read-only), thus protecting the alien world from touching it/them/those;
    - This container is set to be compressed by default, no matter how fstab tells kernel to act `(compress-force=zstd:1)`
!!! warning "Read-only notes"

    Default write permission behavior can be altered by using `steamos-readonly {enable,disable}`, which sets currently active/chrooted subvolume as read{only/write}. Please note that any changes made to currently active subvolume, won't be carried over to new one.
- `holo_var` houses `/var`, which contains usual `/var` contents and bindmount receivers for following things:
    - `/var/lib/pacman` - Gets overmounted by a physical location in `/holo_root/rootfs/${SUBVOLUME}/var/lib/pacman`
    - `/var/overlays/etc/*` - Overlay setup for /etc to be read-written by services that require it
- `holo_home` houses `/home` directory that includes:
    - User directory, eg: `/home/jack`
    - `/home/.steamos/offload/*` - Offload directories that are meant to be R/W by default, which include by default `/var/lib/pacman /opt /root /srv /usr/lib/debug /usr/local /var/cache/pacman /var/lib/docker /var/lib/flatpak /var/lib/systemd/coredump /var/log /var/tmp`
### Default mountpoints
`holo_root`:
```
holo_root on / type btrfs (ro,noatime,nodiratime,compress-force=zstd:1,ssd,discard,space_cache=v2,subvolid=256,subvol=/rootfs/holoiso_beta_snapshot20240125.1712.26)
holo_root on /holo_root type btrfs (rw,noatime,nodiratime,compress-force=zstd:1,ssd,discard,space_cache=v2,subvolid=5,subvol=/)
holo_root on /var/lib/pacman type btrfs (ro,noatime,nodiratime,compress-force=zstd:1,ssd,discard,space_cache=v2,subvolid=256,subvol=/rootfs/holoiso_beta_snapshot20240125.1712.26)
```
`holo_var`:
```
holo_var on /var type ext4 (rw,relatime)
/new_root/etc on /etc type overlay (rw,relatime,lowerdir=/new_root/etc,upperdir=/new_root/var/overlays/etc/upper,workdir=/new_root/var/overlays/etc/work,index=off,metacopy=off)
```
`holo_home`:
```
holo_home on /home type ext4 (rw,relatime)
holo_home on /opt type ext4 (rw,relatime)
holo_home on /root type ext4 (rw,relatime)
holo_home on /srv type ext4 (rw,relatime)
holo_home on /usr/lib/debug type ext4 (rw,relatime)
holo_home on /usr/local type ext4 (rw,relatime)
holo_home on /var/cache/pacman type ext4 (rw,relatime)
holo_home on /var/lib/docker type ext4 (rw,relatime)
holo_home on /var/lib/flatpak type ext4 (rw,relatime)
holo_home on /var/lib/systemd/coredump type ext4 (rw,relatime)
holo_home on /var/log type ext4 (rw,relatime)
holo_home on /var/tmp type ext4 (rw,relatime)
```
### Default boot option
```
menuentry 'SteamOS, subvolume rootfs/holoiso_beta_snapshot20240125.1712.26' --class steamos --class gnu-linux --class gnu --class os $menuentry_id_option 'gnulinux-simple-LABEL=holo_root' {
        load_video
        set gfxpayload=keep
        insmod gzio
        insmod part_gpt
        insmod btrfs
        set root='hd0,gpt2'
        if [ x$feature_platform_search_hint = xy ]; then
          search --no-floppy --fs-uuid --set=root --hint-bios=hd0,gpt2 --hint-efi=hd0,gpt2 --hint-baremetal=ahci0,gpt2  5e207989-5c67-4459-9e16-645f9c3e7210
        else
          search --no-floppy --fs-uuid --set=root 5e207989-5c67-4459-9e16-645f9c3e7210
        fi
        linux   /rootfs/holoiso_beta_snapshot20240125.1712.26/boot/vmlinuz-linux-zen root=LABEL=holo_root rw rootflags=subvol=rootfs/holoiso_beta_snapshot20240125.1712.26  loglevel=3 quiet splash plymouth.nolog
        initrd  /rootfs/holoiso_beta_snapshot20240125.1712.26/boot/intel-ucode.img /rootfs/holoiso_beta_snapshot20240125.1712.26/boot/amd-ucode.img /rootfs/holoiso_beta_snapshot20240125.1712.26/boot/initramfs-linux-zen.img
}
```
## Boot process (pre-graphical target)
The boot process is identical to how Arch Linux boots, for one slight exception.

Due to chaotic nature of immutable Linux distribuitons, and how `/etc` is being handled by systemd, HoloISO implements `holoiso` systemd hook, which mounts `/var` and respective overlay for `/etc` on `/var/overlays/etc/{upper,work}` before systemd starts in order to make sure that user-added drop-ins are completely functional without unsealing RO state.
If you're interested to know how it works, refer to here:

- [/usr/lib/initcpio/install/holoiso](https://github.com/holoiso-staging/postcopy/blob/beta/usr/lib/initcpio/install/holoiso)
- [/usr/lib/initcpio/hooks/holoiso](https://github.com/holoiso-staging/postcopy/blob/beta/usr/lib/initcpio/hooks/holoiso)

## Boot process (post-graphical target)
By default HoloISO does the same thing as Steam Deck does, Starts SDDM, loads in `gamescope-wayland.desktop` as set in `(var/overlays/etc/upper)/etc/sddm.conf.d/autologin.conf`. However, that can be altered by using `steamos-session-select` to boot into: `plasma, plasma-x11-persistent, gamescope` in current session/next reboot.

## Build process/deployment
Unlike Steam Deck, which utilizes rauc and desync as build patching/delivering method, HoloISO utilizes compressed BTRFS subvolumes that are built by [buildroot/build.sh](https://github.com/holoiso-staging/buildroot/blob/master/build.sh) with it's own preset as update stream, and is being downloaded and applied by `steamos-update` served by [postcopy](https://github.com/holoiso-staging/postcopy).

### Build process
In order to make a default HoloISO system image build, you would need following things:

- Installed Arch Linux/HoloISO with `btrfs-progs archiso git arch-install-scripts` preinstalled
- At least 20GB of free space in drive, where working and output directories are located, and at least 10GB of free space in drive, where `/var/cache/pacman/pkg` is located.
#### Getting started
1. Clone [https://github.com/holoiso-staging/buildroot](https://github.com/holoiso-staging/buildroot) by using git like this: `git clone https://github.com/holoiso-staging/buildroot`
2. If you would like to do make an identical build of HoloISO, clone `postcopy_{BRANCH}` to `buildroot` by using git like this for eg.: `git clone https://github.com/holoiso-staging/postcopy -b beta buildroot/postcopy_beta`
#### Building
1. Start building by `sudo bash buildroot/build.sh --flavor beta --deployment_rel beta --snapshot_ver "anything you like" --workdir "path to workdir" --output-dir "path to output dir"`
2. If you wish to maintain your own fork, with working updates, add `--add-release` to the build command which will generate SHA sum and update metadata to be used by update client with your host.
!!! warning "About updates..."

    In order to properly serve updates on your own, make *SURE* that you change the update endpoint in `/postcopy_{BRANCH}/usr/bin/steamos-update`
3. After several minutes or so, you will find your compressed BTRFS subvolume snapshot in the output directory, which you can deploy to your working instance if you wish.
!!! failure "Additional notes"

    Any subbranch designation **MUST** be declared with `-dev` after the actual branch name, for example: `beta-dev_nv`, `beta-dev_igd`, else the update client **WILL BE AND IS** going to be rendered unusable.

### Deployment
This chapter will describe how `steamos-update` is being used to deploy builds on target HoloISO instance.
