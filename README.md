# bootstrap-install-dgl
Install a pre-customized Debian GNU/Linux with debootstrap from scratch

## About

* Author: Frank Guthausen
* License: GNU General Public License v2 (if no other license is mentioned)
* this is a tool collection to create chroot environments, containers, bootable USB devices, virtual machines (e.g. KVM or Xen) and normal bootable installations for servers, desktops and laptops

## Usage: quick install amd64 (64bit) Debian jessie

* choose a device which will become the root device of your new system
* note UUID for fstab
* mount it to /mnt
* for each separate device/partition, e.g. as in /home /tmp /var etc.pp., mkdir /mnt/${foo} and mount
* note UUID(s) for fstab
* debootstrap
```
    date ; time debootstrap --arch amd64 jessie /mnt http://ftp.debian.org/debian ; date
```
* mount further devices, e.g. for /boot
* note UUID(s) for fstab
* clone this git repository
```
    cd /mnt/opt
    git clone https://github.com/fmgborg/bootstrap-install-dgl.git
    date ; time rsync -a /mnt/opt/bootstrap-install-dgl/bootstrap /mnt/opt/ ; date
```
* mount system devices (chrootenv on)
```
    mount --bind /dev /mnt/dev
    mount --bind /dev/pts /mnt/dev/pts
    mount --bind /proc /mnt/prov
    mount --bind /sys /mnt/sys
```
* choose an identifier for customization, e.g. acme (ACME)
```
    vi /mnt/opt/bootstrap/jessie/meta/etc/customizationname
```
* run the customization script
```
    chroot /mnt/ /opt/bootstrap/jessie/meta/sbin/customization.sh
```
* edit configuration template files, to boot the new system at least
```
    vi /mnt/opt/bootstrap/jessie/system/customized/etc/network/interfaces
    vi /mnt/opt/bootstrap/jessie/system/customized/etc/fstab
    vi /mnt/opt/bootstrap/jessie/system/customized/etc/hostname
    vi /mnt/opt/bootstrap/jessie/system/customized/etc/hosts
```
* edit installation script (noninteractive or interactive)
```
    vi /mnt/opt/bootstrap/jessie/system/customized/sbin/run-once-stage.00.01-pre-user.sh
```
* run the installation script
```
    time chroot /mnt/ sh /opt/bootstrap/jessie/system/customized/sbin/run-once-stage.00.01-pre-user.sh
```
or to read the logfile later
```
    { date ; time chroot /mnt/ sh /opt/bootstrap/jessie/system/customized/sbin/run-once-stage.00.01-pre-user.sh ; date ; } 2>&1 | tee -a install.log
```
* chroot into the new installation
```
    chroot /mnt
```
* you should see a coloured prompt, beginning with the word ``chroot''
* for non German keyboard layouts there is no preconfiguration yet
```
    dpkg-reconfigure -plow keyboard-configuration
    dpkg-reconfigure -plow console-setup
```
* install bootloader
```
    aptitude install grub-pc # grub2
```
* leave chroot with CTRL+D or `exit`
* unmount devices in reverse order
```
    umount /mnt/sys
    umount /mnt/proc
    umount /mnt/dev/pts
    umount /mnt/dev
    umount /mnt/boot
    umount /mnt/${foo}
    umount /mnt
```
* the new system should boot now

## More
Another short howto can be found in the bootstrap/README file.
