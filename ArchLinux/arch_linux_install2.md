Archi Linux 설치

환경: bios only

__Connet to the internet__
```
# ip link
# ip a
# ping archlinux.org
```

__Partition the disks__
```
# fdisk -l
* Example layouts: BIOS with MBR
------------------------------------------------------------------------
Mount point			Partition	Partition type	Suggested size
-------------------------------------------------------------------------
/mnt				/dev/sdX1	Linux			Remainder of the device
[SWAP]				/dev/sdX2	Linux swap		More than 512 MiB

# cfdisk /dev/sdX
Select label type : dos (BIOS with MBR)

Format the partitions
# mkfs.xfs /dev/vda2
# mkswap /dev/vda1
# swapon /dev/vda1
```

__Mount the file systems__   
ount the file system on the root partition to /mnt, for example:
```
# mount /dev/vda2 /mnt
```

__Installation__

Selet mirrors
# vi /etc/pacman.d/mirrorlist
## South Korea
Server = http://ftp.lanet.kr/pub/archlinux/$repo/os/$arch
Server = https://ftp.lanet.kr/pub/archlinux/$repo/os/$arch

Install essential packages
# pacstrap /mnt base linux linux-firmware(가상화에서 제외)
* You could omit the installation of the firmware package when installing in a virtual machine or container.


Configure the system

Fstab
# genfstab -U /mnt >> /mnt/etc/fstab

Chroot
# arch-chroot /mnt

Packages install
# pacman -S vi grub openssh dhcpcd(client)

Time zone
# ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime
Run hwclock(8) to generate /etc/adjtime:
# hwclock --systohc
This command assumes the hardware clock is set to UTC. See System time#Time standard for details.

Localization
Edit /etc/locale.gen and uncomment en_US.UTF-8 UTF-8 and other needed locales.
# locale-gen
Create the locale.conf file, and set the LANG variable accordingly:
# echo "LANG=en_US.UTF-8" > /etc/locale.conf

Network configuration
Create the hostname file:
# echo "arch01" > /etc/hostname
 Add matching entries to hosts:
# echo "127.0.0.1	localhost	arch01" >> /etc/hosts

Start the daemon start/enable dhcpcd.service
# systemctl enable dhcpcd
# systemctl enable sshd


Root password
# passwd

Boot loader
# grub-install --target=i386-pc /dev/vda
Generate the main configuration file
# grub-mkconfig -o /boot/grub/grub.cfg


Reboot
exit or ctrl+d 

