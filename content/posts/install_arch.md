---
title: "Install arch linux"
date: 2018-09-26
draft: false
---

ping dikatrio.xyz

pacman -Sy terminus-font  
pacman -Ql terminux-font  
setfont ter-v22n

### check time
`timedatectl set-ntp true && timedatectl status`

`fdisk /dev/sda`  
n, two partitions  
first: start `+18G`  
second: for swap  
**after** creating partitions:  
a -> bootable first partition  
t -> type 82 swap partition  
w to write

### format the partitions  
`mkfs.ext4 /dev/sda1`

`mkswap /dev/sda2`  
`swapon /dev/sda2`

### mount the file systems  
`mount /dev/sda1 /mnt`

genfstab will later detect mounted file systems and swap space.  

### update mirrorlist  
`vim /etc/pacman.d/mirrorlist`

### install  
`pacstrap /mnt base base-devel`

### generate fstab  
`genfstab -U /mnt >> /mnt/etc/fstab`  
blkid to list UUIDs

### Change root into the new system  
`arch-chroot /mnt`

### set timezone  
`ln -sf /usr/share/zoneinfo/Europe/Athens /etc/localtime`

### set hardware clock  
`hwclock - -systohc`

(install vim)  
Uncomment en_US.UTF-8 UTF-8 and other needed locales in /etc/locale.gen, and generate them with:  
`locale-gen`

set LANG variable  
`echo "LANG=en_US.UTF-8" >> /etc/locale.conf`

`echo "theComputer" >> /etc/hostname`

in /etc/hosts  
```
127.0.0.1	localhost  
::1		localhost  
127.0.1.1	theComputer.localdomain	theComputer  
```
### install grub
```
pacman -S grub  
grub-install /dev/sda  
grub-mkconfig -o /boot/grub/grub.cfg
```

### set root password  & reboot
`passwd`  
`exit` (root enviroment)  
`reboot`

login as root

### create user
`useradd -m kon`  
`passwd kon`

still as root  
`visudo`  
add `USER_NAME ALL=(ALL) ALL`  
exit and save vi with ZZ


### setup network  
ip link  
start dhcpcd to bring up the wired interface  
`systemctl start dhcpcd`

**with systemd-networkd**   
`vim /etc/systemd/network/20-wired.network`  

```
[Match]  
name=en-wildcard  

[Network]  
DHCP=ipv4
```

```
systemctl restart systemd-networkd  
systemctl enable systemd-networkd
```

todo:   
`/etc/resolv.conf` for DNS and systemd-resolved  
wireless with iwd

**with NetworkManager**  
```
pacman -S networkmanager  
systemctl start NetworkManager
systemctl enable NetworkManager
```
 enable to create the symlinks so as to be enabled on reboot

todo:  
change DNS

### install desktop

`pacman -S xorg xorg-server`  

`pacman -S xfce4 xfce4-goodies`  
or  
`pacman -S gnome gnome-extra`

with gnome gdm is installed, so
```
systemctl enable gdm.service

```

for xfce  

* either with xinitrc  
`cp /etc/X11/xinit/xinitrc ~/.xinitrc`  
`exec startxfce4` at the end of xinitrc

* or a display manager
install gdm, works ok. lightdm not so much

### install lts kernel
check `uname -r`
```
sudo pacman -S linux-lts
sudo pacman -S linux-lts-headers
grub-mkconfig -o /boot/grub/grub.cfg
```
reboot, check again
