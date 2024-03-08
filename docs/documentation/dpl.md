## steamos-update
This chapter will describe how `steamos-update` is being used to deploy builds on target HoloISO instance.

`steamos-update` is a bash script that does following things:

If `steamos-update check` is being ran:

1. Downloads latest update metadata from `https://get.holoiso.ru.eu.org/holoiso-images` in following fashion:
    - Checks if there's update branch override (i.o.w subbranch) set by `${UPDATE_BRANCH_OVERRIDE}` either by default in `/etc/holoiso-release` or in `/etc/manual-enroll-override` set by `holoiso-enroll-build`, in which case, the endpoint link turns into `https://get.holoiso.ru.eu.org/holoiso-images/${UPDATE_BRANCH_OVERRIDE}/latest_${UPD_BRANCH}.releasemeta`
    - Else? Set `https://get.holoiso.ru.eu.org/holoiso-images/latest_${UPD_BRANCH}.releasemeta` and download the metadata from there.
    !!! info "Wonder if something is going wrong? By default, Steam client checks for those exit codes:"
        - Exitcode `7` means that no update is available
        - Exitcode `0` means that update is available/finished updating/successful return
        - Anything else means that update failed and the PID should be terminated immediately

2. If an update exists (`"${STAGING_OS_TAG}" > "${OS_TAG}"`):
    - Arms "ready-to-update" condition (`/tmp/steamos-ready-to-deploy`)

    Else? Report to the user that either:
    - `System is up to date`
    - `"Your version (${OS_TAG}) is newer than currently available (${STAGING_OS_TAG}). Please wait for next update to appear available in branch \"${UPD_BRANCH}\" for installation."`

If everything is correct, next "Update" press in Steam client will run `steamos-update` without any arguments, and here's what it will do now:

1. It will download the latest release subvolume according to the update metadata to `/home/.steamos/updatecontainer/${STAGING_OS_TAG}`
2. After downloading the subvolume file, it will download the sha256 sum to verify that everything went smoothly while downloading
    - This will engage verification process where:
    
    If currently downloaded file sha matches the endpoint sha:
    
    - Good enough to hand-off!

    Else?

    - Delete the entire update container, and abort any hand-offs

3. If everything is correct, it will hand-off the arguments file to `steamos-update-os ${argdata}`
## steamos-update-os
`steamos-update-os` applies the images, and does any post-update hooks it needs to do

1. If hand-off is being requested, check that BOTH argument data exists and sentinel is armed.
2. If everything looks good, proceed to run `steamos-update-os` normally

### Process
1. Decompress `.zst` of the update container in the same directory it is location in
2. Remove any unused subvolumes (except the currently booted one)
3. Install subvolume via `btrfs receive /holo_root/rootfs/ < ${installfile}`
4. Apply postupdate hooks (mounts subvolume in `/tmp/mounts/${IMAGEFILE}` and uses `arch-chroot` to do anything inside it):
    - Unseal the subvolume first (`steamos-readonly disable`)
    - Install device-specific postupdate hooks (`holoiso-postupdate-mgmt`)
    - Update GRUB configuration (`holoiso-grub-update`)
    - Seal the subvolume (`steamos-readonly enable`)
5. Clean up (Unmount the subvolume and remove `/home/.steamos/updatecontainer` contents)
6. Done

This sums up the normal update process via steamos-update.

## Manual installation
If you would like to manually install customly-built/or official, but downloaded from the server image build, follow these instructions:

1. Make sure that your image is decompressed (run `file` on the file, and see if it says `BTRFS stream`)
2. Make sure that your current installation is set to RW (`steamos-readonly disable`)

Let's assume that our file is called INSTALL.img,

1. Apply subvolume against `holo_root` by `btrfs receive /holo_root/rootfs/ < INSTALL.img`
2. Mount the subvolume in any folder you wish by `mount -t btrfs -o subvol=rootfs/INSTALL -L holo_root path`
3. Apply postupdate hooks (use `arch-chroot path` to perform these tasks):
    - Unseal the subvolume first (`steamos-readonly disable`)
    - Install device-specific postupdate hooks (`holoiso-postupdate-mgmt`)
    - Update GRUB configuration (`holoiso-grub-update`)
    - Seal the subvolume (`steamos-readonly enable`)
4. Reboot the device, and see if GRUB shows your new boot option (`SteamOS, subvolume /rootfs/INSTALL` or `SteamOS, subvolume /rootfs/INSTALL with linux-kernel`)

That is all you need to install subvolume images on your own, in future, the post-update machinations will be slimmed down to one command.