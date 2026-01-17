# 🐧 Arch Linux: From Bootable USB to Fully Installed System

This guide is **to-the-point**, **command-focused**, and **GitHub-ready**. It covers:

* Creating a bootable USB
* Installing Arch Linux (UEFI)
* Essential post-install steps
* Tools used + alternatives

> Target: Beginners who want **full control** and **real understanding** (not Archinstall magic)

---

## 0️⃣ What You Need

* A PC / Laptop (UEFI system assumed)
* Internet connection
* USB drive (≥ 4 GB)
* Another Linux / Windows system

---

## 1️⃣ Download Arch Linux ISO

### Tool Used: `wget`

```bash
wget https://geo.mirror.pkgbuild.com/iso/latest/archlinux-x86_64.iso
```

### Alternatives

* Browser download
* `curl -O <url>`
* Torrent (recommended for stability)

---

## 2️⃣ Create Bootable USB

### 🔹 On Linux (Recommended)

### Tool Used: `dd` (raw disk writer)

```bash
lsblk          # identify USB (example: /dev/sdb)
sudo dd if=archlinux-x86_64.iso of=/dev/sdb bs=4M status=progress oflag=sync
```

⚠️ **Use disk (`/dev/sdb`), NOT partition (`/dev/sdb1`)**

### Alternatives

| Tool            | Notes                     |
| --------------- | ------------------------- |
| Balena Etcher   | GUI, safest for beginners |
| Ventoy          | Multi-ISO USB             |
| Rufus (Windows) | Best for Windows users    |

---

## 3️⃣ Boot Into Arch ISO

* Enter BIOS/Boot menu (F2 / F12 / ESC)
* Select USB → **Arch Linux install medium**

You will land directly in **root shell**.

---

## 4️⃣ Verify Boot Mode (IMPORTANT)

```bash
ls /sys/firmware/efi
```

* Exists → ✅ UEFI
* Not found → ❌ Legacy (this guide assumes UEFI)

---

## 5️⃣ Internet Connection

### Wired (Automatic)

```bash
ping -c 3 google.com
```

### Wi-Fi

### Tool Used: `iwctl`

```bash
iwctl
device list
station wlan0 scan
station wlan0 get-networks
station wlan0 connect WIFI_NAME
exit
```

Verify:

```bash
ping google.com
```

---

## 6️⃣ Update System Clock

```bash
timedatectl set-ntp true
```

---

## 7️⃣ Disk Partitioning (UEFI)

### Tool Used: `cfdisk`

```bash
cfdisk /dev/nvme0n1   # or /dev/sda
```

### Create:

| Partition | Size | Type             |
| --------- | ---- | ---------------- |
| EFI       | 512M | EFI System       |
| Root      | Rest | Linux filesystem |

### Alternatives

* `fdisk`
* `parted`

---

## 8️⃣ Format Partitions

```bash
mkfs.fat -F32 /dev/nvme0n1p1
mkfs.ext4 /dev/nvme0n1p2
```

---

## 9️⃣ Mount Partitions

```bash
mount /dev/nvme0n1p2 /mnt
mkdir /mnt/boot
mount /dev/nvme0n1p1 /mnt/boot
```

---

## 🔟 Install Base System

### Tool Used: `pacstrap`

```bash
pacstrap /mnt base linux linux-firmware vim networkmanager
```

---

## 1️⃣1️⃣ Generate fstab

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

---

## 1️⃣2️⃣ Chroot Into System

```bash
arch-chroot /mnt
```

---

## 1️⃣3️⃣ Timezone & Locale

```bash
ln -sf /usr/share/zoneinfo/Asia/Kolkata /etc/localtime
hwclock --systohc
```

Edit locale:

```bash
vim /etc/locale.gen
# uncomment
# en_US.UTF-8 UTF-8
```

```bash
locale-gen
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

---

## 1️⃣4️⃣ Hostname & Hosts

```bash
echo "arch" > /etc/hostname
```

```bash
vim /etc/hosts
```

```text
127.0.0.1   localhost
::1         localhost
127.0.1.1   arch.localdomain arch
```

---

## 1️⃣5️⃣ Root Password

```bash
passwd
```

---

## 1️⃣6️⃣ Bootloader (GRUB)

### Tool Used: `grub`

```bash
pacman -S grub efibootmgr
```

```bash
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
grub-mkconfig -o /boot/grub/grub.cfg
```

### Alternatives

* systemd-boot (simpler)
* rEFInd (GUI)

---

## 1️⃣7️⃣ Enable Network

```bash
systemctl enable NetworkManager
```

---

## 1️⃣8️⃣ Create Normal User (IMPORTANT)

```bash
useradd -m vp
passwd vp
```

```bash
pacman -S sudo
EDITOR=vim visudo
```

Uncomment:

```text
%wheel ALL=(ALL) ALL
```

```bash
usermod -aG wheel vp
```

---

## 1️⃣9️⃣ Exit & Reboot

```bash
exit
umount -R /mnt
reboot
```

Remove USB 🚀

---

## 2️⃣0️⃣ Post Install (After Login)

```bash
sudo pacman -S xorg plasma sddm konsole dolphin
sudo systemctl enable sddm
reboot
```

### Desktop Alternatives

| DE    | Command                   |
| ----- | ------------------------- |
| GNOME | `pacman -S gnome gdm`     |
| XFCE  | `pacman -S xfce4 lightdm` |
| i3    | `pacman -S i3`            |

---

## 🔧 Common Tools Summary

| Purpose        | Tool   | Alternatives          |
| -------------- | ------ | --------------------- |
| Bootable USB   | dd     | Etcher, Rufus, Ventoy |
| Disk Partition | cfdisk | fdisk, parted         |
| Network        | iwctl  | nmcli                 |
| Bootloader     | GRUB   | systemd-boot          |

---

