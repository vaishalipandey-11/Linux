# 🧹 Ubuntu Cleanup & Performance Optimization Guide

> A complete step-by-step guide to clean, free up space, and check your system's performance after cleanup.

---

## ✅ Section 1: System Cleanup

### 1. Remove Unused Packages

```bash
sudo apt autoremove
sudo apt autoclean
sudo apt clean
```

### 2. Clean System Logs

```bash
sudo journalctl --vacuum-time=7d
```

### 3. Purge Old Kernels

```bash
sudo apt --purge autoremove
```

### 4. Delete Thumbnail Cache

```bash
rm -rf ~/.cache/thumbnails/*
```

### 5. Empty Trash and Recently Used Files

```bash
rm -rf ~/.local/share/Trash/*
rm -rf ~/.local/share/recently-used.xbel
```

### 6. Find Large Files

```bash
sudo du -ah / | sort -rh | head -n 20
```

### 7. Clean Snap Old Revisions

```bash
snap list --all
```

Then:

```bash
snap list --all | awk '/disabled/{print $1, $3}' | \
while read snapname revision; do
    sudo snap remove "$snapname" --revision="$revision"
done
```

### 8. Clean `/var`, `/opt`, and Other Large Folders

```bash
sudo ncdu /
sudo ncdu /var
sudo ncdu /opt
```

### 9. Optional: Clean Flatpak Unused Files

```bash
flatpak uninstall --unused
```

### 10. GUI Tool: BleachBit (Optional)

```bash
sudo apt install bleachbit
```

### 11. Enable Preload for Faster App Launch

```bash
sudo apt install preload
```

---

## ⚙️ Section 2: Performance & Disk Usage Check

### 1. Check Disk Usage Summary

```bash
df -h
```

### 2. Directory-Wise Disk Usage

```bash
sudo ncdu /
```

### 3. RAM, CPU, Swap Usage

```bash
htop
```

Install:

```bash
sudo apt install htop
```

### 4. Disk I/O Usage

```bash
sudo apt install iotop
sudo iotop
```

### 5. SMART Disk Health

```bash
sudo apt install smartmontools
sudo smartctl -a /dev/sda
```

### 6. Disk Read/Write Speed Test

```bash
sudo hdparm -Tt /dev/sda
```

### 7. Monitor System Activity in Real-Time

```bash
vmstat 1
```

### 8. Analyze Boot Performance

```bash
systemd-analyze
systemd-analyze blame
```

### 9. Internet Speed Test (Optional)

```bash
sudo apt install speedtest-cli
speedtest
```

---

## 🧠 Quick Summary

| Task        | Command                 |
| ----------- | ----------------------- |
| Disk space  | `df -h`                 |
| Folder size | `ncdu /`                |
| RAM & CPU   | `htop`                  |
| Disk I/O    | `iotop`                 |
| Disk health | `smartctl -a`           |
| Disk speed  | `hdparm -Tt`            |
| Boot time   | `systemd-analyze`       |
| Boot delay  | `systemd-analyze blame` |

---

> Keep this as your go-to reference whenever you feel your system is getting slow or cluttered!
