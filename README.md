# Arch Linux Installation Guide (UEFI + BIOS + GRUB + Dual Boot)

![Arch Linux](https://img.shields.io/badge/Arch_Linux-1793D1?logo=arch-linux&logoColor=white)
![GRUB](https://img.shields.io/badge/GRUB-BB00FF?logo=gnu-grub&logoColor=white)
![UEFI](https://img.shields.io/badge/UEFI-000000?logo=ubuntu&logoColor=white)
![Dual Boot](https://img.shields.io/badge/Dual_Boot-blue)

---

## Table of Contents
- [1. Boot Into Arch ISO](#1-boot-into-arch-iso)
- [2. Connect to Internet](#2-connect-to-internet)
- [3. Update System Clock](#3-update-system-clock)
- [4. Partitioning](#4-partitioning)
  - [UEFI Partition Scheme](#uefi-partition-scheme)
  - [BIOS-MBR Partition Scheme](#bioslegacy-mbr-partition-scheme)
- [5. Mount Partitions](#5-mount-partitions)
- [6. Install Base System](#6-install-base-system)
- [7. Generate fstab](#7-generate-fstab)
- [8. Chroot](#8-chroot)
- [9. System Configuration](#9-time-locale-hostname)
- [10. Set Root Password](#10-set-root-password)
- [11. Enable Network](#11-enablenetworkmanager)
- [12. Install GRUB](#12-install-grub)
- [13. Dual Boot Setup](#13-dual-boot-with-windows)
- [14. Unmount & Reboot](#14-unmount--reboot)
- [Post Installation Setup](#post-installation-setup)
  - [Create User](#create-new-user)
  - [Utilities](#install-useful-utilities)
  - [AUR Helper yay](#install-aur-helper-yay)
  - [PipeWire + WirePlumber](#pipewire--wireplumber)
  - [Ly Display Manager](#display-manager-ly)
  - [Fonts](#fonts)
  - [Bluetooth Setup](#Bluetooth-Setup)
  - [Hyprland Setup](#hyprland-setup)
  
---

# 1. Boot Into Arch ISO
```bash
ls /sys/firmware/efi/efivars
```
If present → UEFI mode.

If absent → BIOS mode.

# 2. Connect to Internet
Wi-Fi
```
iwctl
station wlan0 connect "SSID"
```
LAN

Works automatically.

---
# 3. Update System Clock
```
timedatectl set-ntp true
```

---
# 4. Partitioning

## UEFI Partition Scheme
| Partition | Size      | FS    | Purpose              |
| --------- | --------- | ----- | -------------------- |
| /dev/sda1 | 300–500MB | FAT32 | EFI System Partition |
| /dev/sda2 | 20–50GB   | ext4  | Root `/`             |
| /dev/sda3 | Remaining | ext4  | Home                 |
| /dev/sdaX | Optional  | swap  | Swap                 |

Format:
```
mkfs.fat -F32 /dev/sda1
mkfs.ext4 /dev/sda2
mkfs.ext4 /dev/sda3
mkswap /dev/sda4
```

## BIOS/Legacy (MBR) Partition Scheme
| Partition | Size      | FS        |
| --------- | --------- | --------- |
| /dev/sda1 | 1MB       | BIOS boot |
| /dev/sda2 | 20–50GB   | ext4      |
| /dev/sda3 | Remaining | ext4      |
| /dev/sdaX | Optional  | swap      |

Format:
```
mkfs.ext4 /dev/sda2
mkfs.ext4 /dev/sda3
mkswap /dev/sda4
```
---
# 5. Mount Partitions

UEFI:
```
mount /dev/sda2 /mnt
mkdir -p /mnt/boot/efi
mount /dev/sda1 /mnt/boot/efi
```

BIOS:
```
mount /dev/sda2 /mnt
```

Optional home:
```
mkdir /mnt/home
mount /dev/sda3 /mnt/home
```

---
# 6. Install Base System

```
pacstrap /mnt base linux linux-firmware networkmanager grub efibootmgr os-prober
```

---
# 7. Generate fstab
```
genfstab -U /mnt >> /mnt/etc/fstab
```

---
# 8. Chroot
```
arch-chroot /mnt
```

---
# 9. Time, Locale, Hostname
```
ln -sf /usr/share/zoneinfo/Asia/Kolkata /etc/localtime
hwclock --systohc
```

Locale:
```
nano /etc/locale.gen
```

Uncomment:
```
en_US.UTF-8 UTF-8
```

Generate:
```
locale-gen
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

Hostname:
```
echo "archlinux" > /etc/hostname
```

---
# 10. Set Root Password
```
passwd
```

---
# 11. EnableNetworkManager
```
systemctl enable NetworkManager
```

---
# 12. Install GRUB

UEFI:
```
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=Arch
grub-mkconfig -o /boot/grub/grub.cfg
```

BIOS:
```
grub-install --target=i386-pc /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```

---
# 13. Dual Boot with Windows

Install:
```
pacman -S os-prober
```

Enable detection:
```
nano /etc/default/grub
```

Set:
```
GRUB_DISABLE_OS_PROBER=false
```

Update:
```
grub-mkconfig -o /boot/grub/grub.cfg
```

---
# 14. Unmount & Reboot
```
exit
umount -R /mnt
reboot
```

---

# Post Installation Setup

  - Create User
  - Utilities
  - AUR Helper yay
  - PipeWire + WirePlumber
  - Ly Display Manager
  - Fonts
  - Bluetooth
  - Hyprland

---

# Create New User
```
useradd -m -G wheel -s /bin/bash username
passwd username
EDITOR=nano visudo
```

Uncomment:
```
%wheel ALL=(ALL) ALL
```

# Install Useful Utilities
```
pacman -S git base-devel neovim unzip p7zip
```

# Install AUR Helper (yay)
```
cd /opt
git clone https://aur.archlinux.org/yay.git
chown -R username:username yay
cd yay
sudo -u username makepkg -si
```
# PipeWire + WirePlumber
```
pacman -S pipewire pipewire-alsa pipewire-pulse pipewire-jack wireplumber
systemctl --user enable --now pipewire wireplumber
```

# Display Manager (ly)
```
pacman -S ly
systemctl enable ly
```

# Fonts
```
pacman -S ttf-hack-nerd ttf-nerd-fonts-symbols ttf-dejavu ttf-font-awesome
```

# Bluetooth Setup

Install Bluetooth packages:
```
pacman -S bluez bluez-utils
```

Enable and start Bluetooth service:
```
systemctl enable bluetooth
systemctl start bluetooth
```

To pair devices using bluetoothctl:
```
bluetoothctl
power on
agent on
default-agent
scan on
# wait for device MAC
pair XX:XX:XX:XX:XX:XX
trust XX:XX:XX:XX:XX:XX
connect XX:XX:XX:XX:XX:XX
```

Or Just using blueTui
```
pacman -S bluetui
```

Then run bluetui.

# Hyprland Setup

Install Hyprland:
```
pacman -S hyprland hyprpaper hypridle hyprlock waybar wofi
```

Recommended Extras:
```
pacman -S kitty thunar polkit-gnome brightnessctl bluez bluez-utils
systemctl enable bluetooth
```

Create Config:
```
mkdir -p ~/.config/hypr
cp /usr/share/hyprland/examples/hyprland.conf ~/.config/hypr/
```
