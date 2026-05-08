# 🐧 Install Gentoo Linux Dual Boot (UEFI + Windows)

> Install Gentoo Linux dual boot dengan Windows menggunakan LiveGUI Gentoo.

# NOTE 
install gentoo jangan di remehkan gampang, resiko tanggung sendiri, di sini semuanya setup dari 0

---

# 📦 Persiapan

Yang dibutuhkan:

- Flashdisk minimal 8GB
- ISO Gentoo LiveGUI
- Ventoy
- Internet

---

# 🔽 Download Gentoo LiveGUI

Download ISO:

```txt
https://www.gentoo.org/downloads/
```

Pilih:

```txt
install-amd64-minimal
```

atau:

```txt
LiveGUI ISO
```

> Lebih gampang pake LiveGUI buat pemula.

---

# 🔥 Membuat Bootable USB (Ventoy)

## 1. Download Ventoy

```txt
https://www.ventoy.net/
```

## 2. Install Ventoy ke Flashdisk

- buka Ventoy
- pilih flashdisk
- klik:

```txt
Install
```

---

# 📂 Masukkan ISO Gentoo

Setelah Ventoy selesai:

- buka flashdisk
- copy ISO Gentoo ke flashdisk

Selesai ✅

---

# ⚠️ Persiapan Windows

## Disable Fast Startup

1. Control Panel
2. Power Options
3. Choose what the power buttons do
4. Disable:

```txt
Turn on fast startup
```

---

# 💽 Membuat Partisi Kosong

## Dari Windows

1. Tekan:

```txt
Win + X
```

2. Pilih:

```txt
Disk Management
```

3. Klik kanan drive Windows
4. Pilih:

```txt
Shrink Volume
```

5. Sisakan:
- minimal 40GB

Nanti jadi:

```txt
Unallocated
```

---

# 🚀 Boot ke Ventoy

Masuk boot menu:

| Brand | Tombol |
|---|---|
| ASUS | F8 |
| Acer | F12 |
| Lenovo | F12 |
| MSI | F11 |

Pilih:

```txt
UEFI: Ventoy
```

---

# ▶️ Boot ISO Gentoo

Di menu Ventoy:

- pilih ISO Gentoo
- enter

---

# 🌐 Connect WiFi

Kalau LAN skip.

Masuk WiFi setup:

```bash
nmtui
```

Pilih:
- Activate Connection
- pilih WiFi
- masukkan password

---

# 🌍 Test Internet

```bash
ping google.com
```

---

# 💽 Cek Disk

```bash
lsblk
```

Contoh:

```txt
nvme0n1
```

---

# 🧩 Membuat Partisi

Jalankan:

```bash
cfdisk /dev/nvme0n1
```

> Ganti sesuai disk kalian.

---

# 📁 EFI Partition

## Jangan Hapus EFI Windows

Biasanya:
- FAT32
- 100MB - 300MB

Contoh:

```txt
/dev/nvme0n1p1
```

Itu dipakai bersama Windows.

---

# ➕ Buat Root Partition Gentoo

Dari free space:

| Type | Size |
|---|---|
| Linux filesystem | 40GB+ |

Contoh:

```txt
/dev/nvme0n1p5
```

---

# 💾 Optional Swap

Bisa buat:
- 2GB - 8GB

Type:

```txt
Linux swap
```

---

# ✅ Write Partition

Pilih:

```txt
Write
```

Lalu:

```txt
yes
```

Kemudian:

```txt
Quit
```

---

# 🧱 Format Partition

## Format Root

```bash
mkfs.ext4 /dev/nvme0n1p5
```

## Format Swap

```bash
mkswap /dev/nvme0n1p6
swapon /dev/nvme0n1p6
```

---

# 📂 Mount Partition

## Mount Root

```bash
mount /dev/nvme0n1p5 /mnt/gentoo
```

## Mount EFI

```bash
mkdir -p /mnt/gentoo/boot/efi
mount /dev/nvme0n1p1 /mnt/gentoo/boot/efi
```

---

# 🌍 Masuk ke Folder Gentoo

```bash
cd /mnt/gentoo
```

---

# 📦 Download Stage3

```bash
links https://www.gentoo.org/downloads/
```

Cari:
- latest stage3 amd64

Copy link `.tar.xz`

---

# ⬇️ Download Stage3

```bash
wget LINK_STAGE3
```

---

# 📦 Extract Stage3

```bash
tar xpvf stage3-*.tar.xz --xattrs-include='*.*' --numeric-owner
```

---

# ⚙️ Copy DNS

```bash
cp --dereference /etc/resolv.conf /mnt/gentoo/etc/
```

---

# 📂 Mount System

```bash
mount --types proc /proc /mnt/gentoo/proc
mount --rbind /sys /mnt/gentoo/sys
mount --make-rslave /mnt/gentoo/sys
mount --rbind /dev /mnt/gentoo/dev
mount --make-rslave /mnt/gentoo/dev
```

---

# 🔐 Chroot ke Gentoo

```bash
chroot /mnt/gentoo /bin/bash
source /etc/profile
export PS1="(gentoo) $PS1"
```

---

# 🌍 Sync Repository

```bash
emerge-webrsync
```

---

# 🕒 Set Timezone

```bash
echo "Asia/Jakarta" > /etc/timezone
emerge --config sys-libs/timezone-data
```

---

# 🌐 Setup Locale

Edit locale:

```bash
nano /etc/locale.gen
```

Uncomment:

```txt
en_US.UTF-8 UTF-8
```

Generate locale:

```bash
locale-gen
```

---

# 🧠 Install Kernel

```bash
emerge sys-kernel/installkernel-gentoo sys-kernel/gentoo-kernel-bin
```

---

# 🌐 Install NetworkManager

```bash
emerge net-misc/networkmanager
```

Enable:

```bash
rc-update add NetworkManager default
```

---

# 🔑 Set Root Password

```bash
passwd
```

---

# 👤 Membuat User

```bash
useradd -m -G users,wheel,audio,video -s /bin/bash marcel
passwd marcel
```

---

# ⚡ Install GRUB

```bash
emerge sys-boot/grub
```

Install GRUB:

```bash
grub-install --efi-directory=/boot/efi
```

Generate config:

```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

---

# 🚪 Exit & Reboot

```bash
exit
umount -R /mnt/gentoo
reboot
```

Cabut flashdisk.

---

# ✅ Hasil Akhir

GRUB akan muncul:

```txt
Gentoo Linux
Windows Boot Manager
```

Dual boot berhasil 🎉
