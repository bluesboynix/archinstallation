# Arch Linux Installation Scripts

My scripts and instructions to automate and guide the installation of Arch Linux.

---

## Overview

The installation is split into three phases:

1. **Pre-Installation** (manual partitioning recommended)
2. **Chroot Configuration**
3. **Post-Installation** (desktop environment and apps)

---

## Pre-Installation (Manual)

We recommend using [`cfdisk`](https://man.archlinux.org/man/cfdisk.8) for partitioning the target disk safely.

**Partition layout example:**

| Partition | Size   | Type             | Flags       |
| --------- | ------ | ---------------- | ----------- |
| `/dev/sdX1` | 1 GB   | EFI System       | boot, esp   |
| `/dev/sdX2` | 2 GB   | Linux Swap       | swap        |
| `/dev/sdX3` | Rest   | Linux filesystem |             |

**Commands for formatting and mounting:**

```bash
mkfs.fat -F32 /dev/sdX1
mkswap /dev/sdX2
swapon /dev/sdX2
mkfs.ext4 /dev/sdX3

mount /dev/sdX3 /mnt
mkdir -p /mnt/boot
mount /dev/sdX1 /mnt/boot

