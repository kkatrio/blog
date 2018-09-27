---
title: "Install arch"
date: 2018-09-26
draft: false
---

ping dikatrio.xyz

pacman -Sy terminus-font  
pacman -Ql terminux-font  
setfont ter-v22n

check time  
timedatectl set-ntp true && timedatectl status

fdisk /dev/sda, n, p/e, +18G  
fdisk list options with m  
after creating partitions,  
a -> bootable first partition  
t -> type 82 swap partition  
w to write

format the partitions  
mkfs.ext4 /dev/sda1

mkswap /dev/sda2  
swapon /dev/sda2

mount the file systems  
mount /dev/sda1 /mnt

genfstab will later detect mounted file systems and swap space.  

update mirrorlist  
vim /etc/pacman.d/mirrorlist

install  
pacstrap /mnt base base-devel

generate fstab  
genfstab -U /mnt >> /mnt/etc/fstab  
blkid to list UUIDs

Change root into the new system  
arch-chroot /mnt

timezone  
ln -sf /usr/share/zoneinfo/Europe/Athens /etc/localtime

set hardware clock  
hwclock --systohc

Uncomment en_US.UTF-8 UTF-8 and other needed locales in /etc/locale.gen, and generate them with:  
locale-gen

set LANG variable  
echo "LANG=en_US.UTF-8" >> /etc/locale.conf

echo "theComputer" >> /etc/hostname

/etc/hosts  
127.0.0.1	localhost  
::1		localhost  
127.0.1.1	theComputer.localdomain	theComputer  

pacman -S grub  
grub-install /dev/sda  
grub-mkconfig -o /boot/grub/grub.cfg

root password  
passwd

to reboot  
exit (root enviroment)  
reboot

login as root

----------------------------

useradd --create-home kon  
passwd kon

install sudo (gnome has it)  
visudo  
add USER_NAME ALL=(ALL) ALL

----------------------------

setup network  
ip link  
systemctl dhcpcd.service start  to bring up the wired interface or bring it up manually if it's enough to connect

with systemd-networkd   
vim /etc/systemd/network/20-wired.network  

[Match]  
name=en-wildcard  

[Network]  
DHCP=ipv4

systemctl restart systemd-networkd  
systemctl enable systemd-networkd

todo:   
/etc/resolv.conf for DNS and systemd-resolved  
wireless with iwd


with NetworkManager (for gnome)  
pacman -S networkmanager  
systemctl start NetworkManager.service  
systemctl enable NetworkManager.service creates the symlinks so as to be enabled on reboot

todo:  
resolve DNS

----------------------------
desktop

gnome  
pacman -S xorg xorg-server  
pacman -S gnome gnome-extra

systemctl start gdm.service  
systemctl enable gdm.service

i3  
pacman -S xorg xorg-server  
pacman -S i3wm

vim ~/.xinitrc  
#! /bin/bash  
exec i3

on ~/.bash_profile or ~/.zsh_profile  
 autostart systemd default session on tty1  
if [[ "$(tty)" == '/dev/tty1' ]]; then  
    exec startx  
fi


