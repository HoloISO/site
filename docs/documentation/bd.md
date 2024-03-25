Unlike Steam Deck, which utilizes rauc and desync as build patching/delivering method, HoloISO utilizes compressed BTRFS subvolumes that are built by [buildroot/build.sh](https://github.com/HoloISO/buildroot/blob/master/build.sh) with it's own preset as update stream, and is being downloaded and applied by `steamos-update` served by [postcopy](https://github.com/HoloISO/postcopy).

### Build process
In order to make a default HoloISO system image build, you would need following things:

- Installed Arch Linux/HoloISO with `btrfs-progs archiso git arch-install-scripts` preinstalled
- At least 20GB of free space in drive, where working and output directories are located, and at least 10GB of free space in drive, where `/var/cache/pacman/pkg` is located.
#### Getting started
1. Clone [https://github.com/HoloISO/buildroot](https://github.com/HoloISO/buildroot) by using git like this: `git clone https://github.com/HoloISO/buildroot`
2. If you would like to do make an identical build of HoloISO, clone `postcopy_{BRANCH}` to `buildroot` by using git like this for eg.: `git clone https://github.com/HoloISO/postcopy -b beta buildroot/postcopy_beta`
#### Building
1. Start building by `sudo bash buildroot/build.sh --flavor beta --deployment_rel beta --snapshot_ver "anything you like" --workdir "path to workdir" --output-dir "path to output dir"`
2. If you wish to maintain your own fork, with working updates, add `--add-release` to the build command which will generate SHA sum and update metadata to be used by update client with your host.
!!! warning "About updates..."

    In order to properly serve updates on your own, make *SURE* that you change the update endpoint in `/postcopy_{BRANCH}/etc/steamos-atomupd/mirror`
3. After several minutes or so, you will find your compressed BTRFS subvolume snapshot in the output directory, which you can deploy to your working instance if you wish.
!!! failure "Additional notes"

    Any subbranch designation **MUST** be declared with `-dev` after the actual branch name, for example: `beta-dev_nv`, `beta-dev_igd`, else the update client **WILL BE AND IS** going to be rendered unusable.