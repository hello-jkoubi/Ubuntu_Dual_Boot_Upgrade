# Ubuntu 22.04 → 24.04 Upgrade (Dual Boot with Windows)

This guide documents how to safely replace Ubuntu 22.04 with Ubuntu 24.04 on a dual-boot system while preserving Windows.

---

## System Setup

Single internal SSD (`nvme0n1`) with:

- `nvme0n1p1` → EFI partition (Windows Boot Manager)
- `nvme0n1p3` → Windows (NTFS)
- `nvme0n1p4` → Windows Recovery
- `nvme0n1p5` → Ubuntu 22.04 (to be replaced)

This layout can be verified by running:

```bash
lsblk -o NAME,SIZE,TYPE,MOUNTPOINT
```
---

## Goal

- Replace Ubuntu 22.04 → Ubuntu 24.04
- Keep Windows untouched
- Avoid triple boot setup
- Preserve EFI partition

---

## Step 1 — Create Bootable USB

[Download Ubuntu 24.04 ISO](https://ubuntu.com/download/desktop) and create a bootable USB using Balena Etcher or similar.

---

## Step 2 — Boot from USB (MSI)

1. Insert USB
2. Reboot system
3. Press `F11` to open boot menu
   > Note: The key to access the boot menu depends on your motherboard or laptop manufacturer.  
   > Common keys include: `F11` (MSI), `F12`, `ESC`, or `F9`.
4. Select `UEFI: USB`

---

## Step 3 — Start Installer

Once you boot from the USB, you will see the first screen:

Select **`Try or Install Ubuntu`**.

You will then go through a series of initial setup screens:

- Choose your language  
- Accessibility 
- Keyboard layout  
- Connect to internet 
- Updates available → skip  
- Select `Install Ubuntu`  
- Choose `Interactive Installation`  
- Select `Default selection`

The following video shows the flow of these steps:

test 1
[Watch video](docs/ubuntu_installer.mp4)

test 2
<div align="center">
  <a href="docs/ubuntu_installer.mp4">
    <img src="docs/IMG_3945.png" width="600">
  </a>
</div>

---

#### Recommended Software Options

<div align="center">
  <img src="docs/IMG_3953.png" alt="base" width="600"/>
</div>

On the next screen, make sure to select both:

- `Install third-party software for graphics and Wi-Fi hardware`
- `Download and install support for additional media formats`

This ensures:
- Proper GPU drivers  
- Wi-Fi compatibility  
- Media playback support  

---

## Step 4 — Choose Installation Type

<div align="center">
  <img src="docs/IMG_3954.png" alt="base" width="600"/>
</div>

Select:

- **`Manual installation`**

Do NOT select:
- `Install alongside`
- `Erase disk`

---

## Step 5 — Partition Setup

> Important: This is the most critical step of the entire process.  
> Make sure you select the correct disk and partition. Choosing the wrong one can lead to data loss.
>
> If unsure, stop and verify your partitions first:
>
> ```bash
> lsblk -o NAME,SIZE,TYPE,MOUNTPOINT
> ```
>
> or:
>
> ```bash
> sudo fdisk -l
> ```
>
> In this case:
>
> - Disk: `nvme0n1` (internal SSD)  
> - Partition: `nvme0n1p5` → Ubuntu 22.04  
>
> **This partition will be formatted and replaced with Ubuntu 24.04**.

Identify the correct disk:

- `nvme0n1` → internal SSD (target)

---

### Configure Ubuntu Partition

<div align="center">
  <img src="docs/IMG_3923.jpg" alt="base" width="600"/>
</div>

Select:

- `nvme0n1p5`

Set:

- Filesystem → `Ext4`
- Mount point → `/`
- Format → **enabled**

---

### Configure EFI Partition

Select:

- `nvme0n1p1`

Set:

- Mount point → `/boot/efi`
- Format → **disabled**

---

### Bootloader Location

Set:

- Device for bootloader installation → `nvme0n1`

---

## Step 6 — Final Check

<div align="center">
  <img src="docs/IMG_3927.jpg" alt="base" width="600"/>
</div>

Ensure:

- Only `nvme0n1p5` is formatted
- Windows partition (`nvme0n1p3`) is untouched
- EFI partition is NOT formatted
- Bootloader is installed on `nvme0n1`

---


## Step 7 — Review and Install

<div align="center">
  <img src="docs/IMG_3928.jpg" alt="base" width="600"/>
</div>

Before starting the installation, carefully review the summary:

- Installation disk: `nvme0n1`
- EFI partition: `nvme0n1p1` → used for `/boot/efi` (not formatted)
- Root partition: `nvme0n1p5` → formatted as `ext4` and used for `/`

Make sure:

- Only `nvme0n1p5` is being formatted
- The Windows partition (`nvme0n1p3`) is not listed for formatting
- The EFI partition is used but not formatted

If everything looks correct, click **`Install`** to begin the installation.

This will:
- Replace Ubuntu 22.04 with Ubuntu 24.04
- Keep Windows intact
- Configure the system to use GRUB for dual boot

---

## Step 8 — Reboot

After installation:

- Remove USB
- Reboot system

---

## Expected Result

GRUB boot menu appears with:

- `Ubuntu 24.04`
- `Windows Boot Manager`

