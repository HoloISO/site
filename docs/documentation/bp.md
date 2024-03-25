## Boot process (pre-graphical target)
The boot process is identical to how Arch Linux boots, for one slight exception.

Due to chaotic nature of immutable Linux distribuitons, and how `/etc` is being handled by systemd, HoloISO implements `holoiso` systemd hook, which mounts `/var` and respective overlay for `/etc` on `/var/overlays/etc/{upper,work}` before systemd starts in order to make sure that user-added drop-ins are completely functional without unsealing RO state.
If you're interested to know how it works, refer to here:

- [/usr/lib/initcpio/install/holoiso](https://github.com/HoloISO/postcopy/blob/beta/usr/lib/initcpio/install/holoiso)
- [/usr/lib/initcpio/hooks/holoiso](https://github.com/HoloISO/postcopy/blob/beta/usr/lib/initcpio/hooks/holoiso)

## Boot process (post-graphical target)
By default HoloISO does the same thing as Steam Deck does, Starts SDDM, loads in `gamescope-wayland.desktop` as set in `(var/overlays/etc/upper)/etc/sddm.conf.d/autologin.conf`. However, that can be altered by using `steamos-session-select` to boot into: `plasma, plasma-x11-persistent, gamescope` in current session/next reboot.