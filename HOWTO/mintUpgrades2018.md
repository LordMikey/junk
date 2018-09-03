
# 2018 Mint upgrades, Desktop & Laptop

download iso to partition that is not the system partition
for convenience when using grub, mklink to downloaded iso

## Preparation

Backup copy of /etc
Check userid of user in /etc/passwd
Backup or move /home/user 


## Grub Irritation Note

hd(0,1) = /dev/sda1
device numbers from 0 = a
partition numbers from 1 = 1


## Mint 18.3

```
ls (hd0,4)
set isofile="/share/lmde3.iso"
loopback loop (hd0,4)/share/lmde3.iso
linux (loop)/casper/vmlinuz file=/cdrom/preseed/linuxmint.seed boot=casper iso-scan/filename=$isofile
initrd (loop)/casper/initrd.lz
boot
```


if hang after starting install:
sudo umount -l /isodevice

cp /etc 
during install create temp user user2 = 1000, so user can remain 1001
mv home dir, create new user, mv back


## LMDE3 squashfs

Debian uses `boot=live`; default path is /live for vmlinuz, initrd & squashfs. Device is passed to kernel
with `live-media` param using /dev/sdb style

* temp mount iso: mount -t iso9660 -o loop,ro where.iso tmp
* copied vmlinuz, initrd, filesystem.squashfs to dir /live at root of /home partition
* /live is default dir. If not, pass live-media-path=/path/to/live as kernel param
* see Debian manpages live-boot doc
* installer will not let you install to same USB device, even though target partitions not system partition
* copied contents of /live to another USB stick. This was hd(1,1) = sdb1 (single VFAT partition)
* files in /live: vmlinuz, initrd.ld, filesystem.squashfs, plus filesystem.* 
* installer removes packages used only by live at end of install

```
set root=(hd1,1)   # or (hd1,gpt1)
linux /live/vmlinuz debug=1 boot=live live-media=/dev/sdb1
initrd /live/initrd.lz
boot
```
