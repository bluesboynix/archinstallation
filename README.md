# Arch Linux Installation

My Arch Linux installation quick step.

---

## Overview

The installation is split into three phases:

1. **Pre-Installation** (manual partitioning recommended)
2. **After arch-chroot Setup**
3. **Post-Installation** (desktop environment and apps)

---

## 1. Pre-Installation (Manual)

Using [`cfdisk`](https://man.archlinux.org/man/cfdisk.8) for partitioning the target disk safely. fdisk also available.

**Partition layout example UEFI:**
- /dev/sdX1 - 1 GB, EFI System
- /dev/sdX2 - 2 GB, Linux Swap
- /dev/sdX3 - Rest, Linux filesystem
- Note: for MBR, skip efi and follow /dev/sdX1 as / and /dev/sdX2 as swap

**Formatting and mounting:**

```bash
mkfs.fat -F32 /dev/sdX1
mkswap /dev/sdX2
swapon /dev/sdX2
mkfs.ext4 /dev/sdX3

mount /dev/sdX3 /mnt
mkdir -p /mnt/boot
mount /dev/sdX1 /mnt/boot
```

**Install base packages:**
```bash
pacstrap /mnt base base-devel linux linux-headers linux-firmware sudo grub nano git amd-ucode
```

**Generate fstab:**
```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

**Chroot into new system:**
```bash
arch-chroot /mnt
```

## 2. After arch-chroot Setup
**Set the Timezone**
```bash
ln -sf /usr/share/zoneinfo/Asia/Kolkata /etc/localtime
```

**Generate the hardware clock:**
```bash
hwclock --systohc
```

**Localization**
nvim /etc/locale.gen
    en_US.UTF-8 UTF-8

**Generate locales:**
```bash
locale-gen
```

**Create /etc/locale.conf and set your LANG:**
```bash
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

**Set Hostname**
```bash
echo "myhostname" > /etc/hostname
```

**Add matching entries in /etc/hosts:**
```bash
echo "127.0.0.1   localhost" >> /etc/hosts
echo "::1         localhost" >> /etc/hosts
echo "127.0.1.1   myhostname.localdomain myhostname" >> /etc/hosts
```

**Set Root Password**
```bash
passwd
```

**Install and Configure Bootloader (GRUB)**
```bash
pacman -S grub efibootmgr mtools dosfstools
```

**Mount EFI partition (if not already mounted), e.g., if EFI is /dev/sda1:**
```bash
mkdir /boot/efi
mount /dev/sda1 /boot/efi
```

**Install GRUB:**
```bash
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB
```

**If MBR**
```bash
grub-install --target=i386-pc /dev/sda
```

**Generate GRUB config file:**
```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

**Enable Network**
```bash
systemctl enable NetworkManager
```
## 3. Post Installation
**Create admin user**
```bash
pacman -S zsh
useradd -m -G wheel,users,audio,video -s /bin/zsh username
passwd username
```

**Setup yay (AUR helper)**
```bash
sudo pacman -S --needed git base-devel
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

**Install Core Hyprland Ecosystem**
```bash
pacman -S hyprland hyprpicker hypridle hyprlock hyprpaper xdg-desktop-portal-hyprland hyprshot foot
```

**Install Tools and Utilities**
```bash
pacman -S rofi waybar
yay -S wlogout 
```
**Setup PipeWire Audio**
```bash
pacman -S pipewire pipewire-pulse wireplumber

### After user creation
systemctl --user enable --now pipewire pipewire-pulse wireplumber
```
**Display Manager**
```bash
pacman -S ly
sysytemctl enable ly
```

**Some useful fonts Fonts**
- ttf-cantarell
- ttf-liberation
- ttf-hack-nerd
- ttf-fira-code
- ttf-nerd-fonts-symbols
- ttf-nerd-fonts-symbols-common
- ttf-nerd-fonts-symbols-mono

Optionally clear font cache
```bash
fc-cache -fv
```
**Useful tools**
- clipboard manager - cliphist
- file manager - thunar (gui), yazi (tui)
- notification - swaync
- Application launcher - rofi
- file transfer - curl, wget
- browsers - google-chrome (from AUR), firefox, nyxt
- editor - emacs, neovim
- pdf - zathura zathura-pdf-poppler
- terminal - foot, kitty, alacritty, ghostty
- media player - mpv, smplayer
- gtk settings - nwg-look




















