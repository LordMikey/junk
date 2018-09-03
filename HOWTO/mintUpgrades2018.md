
# 2018 Mint upgrades, Desktop & Laptop

Download ISO to a partition that is not the current system partition.

For convenience when using grub, mklink with short name to downloaded ISO.

## Preparation

* Backup copy of `/etc`
* Check userid of `username` in `/etc/passwd`
* Backup or move `/home/username`


## Grub Irritation Reminder

* hd(0,1) = /dev/sda1
* device numbers from 0 = a
* partition numbers from 1 = 1


## Mint 18.3

Target: desktop

```
ls (hd0,4)
set isofile="/share/lmde3.iso"
loopback loop (hd0,4)/share/lmde3.iso
linux (loop)/casper/vmlinuz file=/cdrom/preseed/linuxmint.seed boot=casper iso-scan/filename=$isofile
initrd (loop)/casper/initrd.lz
boot
```


* if installer hangs after starting: `sudo umount -l /isodevice`
* during install create temp user user2 = 1000, so `username` can remain 1001
* mv home dir, create new user, mv back


## LMDE 3

Target: clapped out former-Chromebook Acer C720P. System 'disk' is a 32MB USB stick with about four partitions
(the 128MB M2 SSD crapped out after four years; not wasting $99 for another).

Debian uses `boot=live`; default path is `/live` for vmlinuz, initrd & squashfs files. Device is passed to kernel
with `live-media` param using /dev/sdb style.

* temp mount iso: `mount -t iso9660 -o loop,ro somewhere.iso tmp`
* copied vmlinuz, initrd, filesystem.squashfs to directory /live at root of partition mounted to /home
* /live is default directory. If not, pass `live-media-path=/path/to/live` as kernel param
* see Debian manpages live-boot [doc](https://manpages.debian.org/jessie/live-boot-doc/live-boot.7.en.html)
* installer would not allow install to same USB stick, even though target partition not current system partition
* copied contents of /live to another USB stick. This was `hd(1,1)` = `sdb1` (single VFAT partition)
* files in /live: vmlinuz, initrd.ld, filesystem.squashfs, plus filesystem.* 
* installer removes packages used only by live at end of install

```
set root=(hd1,1)   # or (hd1,gpt1)
linux /live/vmlinuz debug=1 boot=live live-media=/dev/sdb1
initrd /live/initrd.lz
boot
```

## Lightdm problem

As usual after install, lightdm non-functional. Cryptic `SESSION != NULL` errors, flashing screen, cursor-only screen, mouse-only screen. Blow it away and start again:

```
sudo apt-get purge lightdm
sudo apt-get update
sudo apt-get install lightdm
sudo dpkg-reconfigure lightdm
```

Other problems after install: wifi connected but route table incomplete, no nameserver in resolve.conf, file systems
mounted read-only, etc. The usual $h1t. Got working after manual hacks and several more restarts.

