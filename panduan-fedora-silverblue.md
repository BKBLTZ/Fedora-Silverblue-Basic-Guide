# 🪨 Panduan Lengkap Fedora Linux & Silverblue

> **Edisi Fedora 44 · April 2026** — Untuk Pengguna Linux Menengah  
> *Atomic Desktop · Manajemen Paket · Containerisasi · Workflow Modern*

---

## 📋 Daftar Isi

- [Bab 1 — Fedora Linux: Keluarga & Ekosistem](#bab-1--fedora-linux-keluarga--ekosistem)
- [Bab 2 — Fedora Silverblue: Arsitektur Mendalam](#bab-2--fedora-silverblue-arsitektur-mendalam)
- [Bab 3 — Instalasi & Konfigurasi Awal](#bab-3--instalasi--konfigurasi-awal)
- [Bab 4 — Manajemen Aplikasi & Paket](#bab-4--manajemen-aplikasi--paket)
- [Bab 5 — Kustomisasi Tampilan GNOME](#bab-5--kustomisasi-tampilan-gnome)
- [Glosarium](#glosarium)
- [Referensi Resmi](#referensi-resmi)

---

## Bab 1 — Fedora Linux: Keluarga & Ekosistem

### 1.1 Apa itu Fedora Linux?

Fedora Linux adalah distribusi Linux **cutting-edge** yang dikembangkan oleh komunitas Fedora Project dan disponsori oleh **Red Hat** (anak perusahaan IBM). Fedora berfungsi sebagai **"upstream"** bagi Red Hat Enterprise Linux (RHEL) dan CentOS Stream — teknologi yang diuji coba di Fedora akan "mengalir turun" ke RHEL setelah dianggap cukup stabil.

```
Fedora → CentOS Stream → RHEL
```

**Siklus Rilis:**

| Info | Detail |
|------|--------|
| Frekuensi rilis | Setiap ~6 bulan (April & Oktober) |
| Masa dukungan | ~13 bulan (2 rilis penuh) |
| Versi terkini | Fedora 44 (dirilis 28 April 2026) |
| Komponen utama F44 | GNOME 50, Kernel 6.14, Python 3.14 |

---

### 1.2 Empat Kategori Besar Fedora

#### Kategori 1 — Official Editions

Edisi resmi didukung penuh oleh Fedora Engineering Steering Committee (FESCo) dengan proses QA paling ketat.

| Edisi | Desktop | Catatan |
|-------|---------|---------|
| **Fedora Workstation** | GNOME 50 | Flagship, target: developer & power user |
| **Fedora KDE** | KDE Plasma 6.6 | Naik status jadi edisi resmi sejak F42 |
| **Fedora Server** | Headless | Untuk datacenter dan server produksi |
| **Fedora Cloud** | Cloud image | AWS, GCP, Azure — optimasi ukuran minimal |
| **Fedora IoT** | Embedded / SBC | Raspberry Pi, x86-64 & Arm64 |
| **Fedora CoreOS** | Container-focused | Immutable, self-updating, untuk Kubernetes |

#### Kategori 2 — Fedora Atomic Desktops

Varian Fedora dengan arsitektur **immutable** (sistem inti read-only). Pembaruan diterapkan secara atomik — seluruhnya atau tidak sama sekali.

| Varian Atomic | Desktop | Keterangan |
|---------------|---------|------------|
| **Silverblue** | GNOME 50 | Flagship atomic — paling mature & terdokumentasi |
| **Kinoite** | KDE Plasma 6.6 | Alternatif atomic untuk pengguna KDE |
| **Sway Atomic** | Sway (Wayland) | Tiling window manager berbasis Wayland |
| **Budgie Atomic** | Budgie Desktop | Desktop ringan bergaya modern |
| **COSMIC Atomic** | COSMIC (System76) | DE baru berbasis Rust, tersedia sejak F42 |

> 💡 **Tip:** Semua varian Atomic menggunakan teknologi inti yang sama (rpm-ostree + OSTree). Perbedaannya hanya pada paket desktop yang disertakan.

#### Kategori 3 — Fedora Spins

Fedora konvensional (**mutable**) dengan desktop environment berbeda. Contoh aktif:

- **Xfce Spin** — ringan, cocok untuk hardware lama
- **LXQt Spin** — sangat ringan, berbasis Qt
- **MATE Spin** — fork GNOME 2, antarmuka klasik
- **Cinnamon Spin** — dari ekosistem Linux Mint, familiar
- **i3 Spin** — tiling window manager berbasis X11

> ⚠️ **Peringatan:** Spins tidak melalui proses QA seketat Official Editions.

#### Kategori 4 — Fedora Labs

Fedora Workstation yang dikurasi dengan paket untuk kebutuhan profesi tertentu:

- **Security Lab** — Wireshark, nmap, Metasploit, Aircrack-ng, tool forensik
- **Scientific Lab** — Python scientific stack, Jupyter, LaTeX, GNU Octave
- **Design Suite** — GIMP, Inkscape, Blender, Krita, Kdenlive
- **Games Lab** — Wine, Steam (via Flatpak), emulator retro
- **Robotics Suite** — ROS, Gazebo simulator
- **Python Classroom** — lingkungan belajar Python siap pakai

---

### 1.3 Perbedaan Mutable vs Atomic (Immutable)

> ❌ **Miskonsepsi umum:** "Atomic berarti aplikasi tidak bisa diinstal atau diubah."  
> ✅ **Yang benar:** Hanya **sistem inti OS** (`/usr`, `/lib`, `/bin`) yang read-only. Aplikasi tetap bisa diinstal via Flatpak, Toolbx, atau rpm-ostree. `/home` sepenuhnya bisa ditulis.

| Aspek | Mutable (Workstation/Spins) | Atomic (Silverblue/Kinoite) |
|-------|-----------------------------|-----------------------------|
| **Sistem inti** | Bisa diubah langsung (dnf) | Read-only, berubah saat reboot |
| **Update OS** | In-place (file diganti langsung) | Atomik: aktif setelah reboot |
| **Rollback** | Tidak ada (perlu snapshot manual) | Otomatis via GRUB |
| **Risiko update rusak** | Lebih tinggi (partial update) | Sangat rendah (transaksional) |
| **Instal paket sistem** | `sudo dnf install paket` | `rpm-ostree install paket` + reboot |
| **Konsistensi antar mesin** | Bisa berbeda (config drift) | Identik pada versi yang sama |
| **Target pengguna** | Pengguna umum, sysadmin | Developer, prioritaskan stabilitas |

---

## Bab 2 — Fedora Silverblue: Arsitektur Mendalam

### 2.1 Filosofi Desain Silverblue

Proyek ini lahir pada 2018 sebagai "Fedora Atomic Workstation". Pada 2019 berganti nama menjadi **Fedora Silverblue** dan menjadi edisi resmi.

Silverblue dibangun di atas tiga prinsip fundamental:

1. **Immutability** — Sistem inti tidak dapat dimodifikasi saat runtime. Tidak ada yang bisa menjalankan `rm -rf /usr` secara tidak sengaja.
2. **Reproducibility** — Dua mesin dengan versi Silverblue yang sama memiliki sistem inti identik bit-per-bit.
3. **Container-First** — Workflow pengembangan dirancang di sekitar container (Toolbx, Distrobox, Podman).

---

### 2.2 Tiga Pilar Silverblue

#### Pilar 1 — rpm-ostree: Sistem Image Berbasis Commit

rpm-ostree menggabungkan **OSTree** (version control untuk filesystem, mirip Git) dengan **RPM** (sistem paket Red Hat).

**Cara kerja konseptual:**
- Setiap versi OS disimpan sebagai "commit" (analog: `git commit`)
- Update = mengunduh commit baru, bukan menimpa file yang ada
- GRUB menyimpan 2 "deployment" terakhir (aktif + sebelumnya)
- Rollback = memilih deployment lama di GRUB saat boot

**Perintah penting:**

```bash
# Cek status sistem (deployment aktif dan pending)
rpm-ostree status

# Unduh dan staging update
rpm-ostree upgrade

# Kembali ke versi sebelumnya
rpm-ostree rollback

# Instal paket di atas base image (butuh reboot)
rpm-ostree install nama-paket

# Hapus paket yang di-layer
rpm-ostree uninstall nama-paket

# Pin deployment agar tidak terhapus saat update
sudo ostree admin pin 0

# Lihat daftar paket yang di-layer
rpm-ostree status -v
```

> ⚠️ **Peringatan:** Setiap `rpm-ostree install` membutuhkan **REBOOT** untuk aktif.

---

#### Pilar 2 — Flatpak: Ekosistem Aplikasi GUI yang Aman

Flatpak adalah sistem distribusi aplikasi cross-distro yang menyandbox setiap aplikasi dari sistem host. Di Silverblue, Flatpak adalah **metode instalasi aplikasi GUI yang diutamakan**.

> ⚠️ **Peringatan:** Remote "fedora" bawaan sangat terbatas. **Selalu aktifkan Flathub secara manual** setelah instalasi baru!

**Perintah esensial:**

```bash
# Aktifkan Flathub (WAJIB dilakukan pertama kali)
flatpak remote-add --if-not-exists flathub \
  https://flathub.org/repo/flathub.flatpakrepo

# Cari aplikasi
flatpak search nama-aplikasi

# Instal dari Flathub
flatpak install flathub com.spotify.Client

# Update semua aplikasi
flatpak update

# Hapus aplikasi
flatpak uninstall com.spotify.Client

# Bersihkan runtime yang tidak terpakai
flatpak uninstall --unused

# List semua aplikasi terinstal
flatpak list --app

# Lihat dan override izin
flatpak info --show-permissions com.spotify.Client
flatpak override --user --filesystem=home com.spotify.Client
```

> 💡 **Tip:** Install **Flatseal** (`com.github.tchx84.Flatseal`) untuk mengelola izin Flatpak via GUI.

---

#### Pilar 3 — Toolbx & Distrobox: Lingkungan Development Fleksibel

**Toolbx — Solusi Resmi Fedora:**

Container berbasis Fedora yang bisa dimodifikasi penuh. `/home` di-share antara host dan container.

```bash
# Buat container Toolbx
toolbox create nama-container
toolbox create --image registry.fedoraproject.org/fedora:44 mydev

# Masuk ke container
toolbox enter nama-container

# Di dalam container: pakai dnf seperti biasa!
sudo dnf install nodejs npm golang rustc

# Jalankan perintah tanpa masuk container
toolbox run -c nama-container python3 script.py

# List semua container
toolbox list

# Hapus container
toolbox rm nama-container
```

**Distrobox — Alternatif yang Lebih Powerful:**

Bisa membuat container dari distro LAIN (Ubuntu, Arch, openSUSE, Alpine, dll.).

```bash
# Instal Distrobox
rpm-ostree install distrobox
# (reboot dulu)

# Buat container Ubuntu 24.04
distrobox create --name ubuntu --image ubuntu:24.04

# Buat container Arch Linux
distrobox create --name arch --image archlinux:latest

# Masuk ke container
distrobox enter ubuntu

# Di dalam Ubuntu: pakai apt!
sudo apt install build-essential

# Export aplikasi dari container ke desktop host
distrobox-export --app firefox
```

**Perbandingan Toolbx vs Distrobox:**

| Aspek | Toolbx | Distrobox |
|-------|--------|-----------|
| Distro container | Fedora saja | Banyak distro |
| Integrasi Fedora | Native, resmi | Baik, tapi pihak ketiga |
| Kemudahan setup | Sangat mudah | Lebih banyak opsi |
| Export GUI apps | Terbatas | Penuh (distrobox-export) |
| Use case ideal | Dev Fedora, CLI umum | Tools yang hanya ada di distro lain |

---

### 2.3 SELinux di Silverblue

SELinux aktif dalam mode **enforcing** secara bawaan. Setiap proses dan file diberi "label" keamanan — akses tanpa izin eksplisit akan **ditolak, bahkan untuk root**.

> ⚠️ **Peringatan:** JANGAN nonaktifkan SELinux (`setenforce 0`). Ini melemahkan keamanan sistem secara signifikan.

```bash
# SALAH: Mount volume tanpa label SELinux
podman run -v /home/user/data:/data myimage

# BENAR: Tambahkan suffix :Z untuk labeling otomatis
podman run -v /home/user/data:/data:Z myimage

# Cek status SELinux
getenforce

# Lihat log penolakan
sudo ausearch -m AVC -ts recent

# Troubleshoot
sudo audit2why < /var/log/audit/audit.log
```

---

### 2.4 Perbandingan Silverblue vs Distro Lain

| Fitur | Silverblue | Workstation | Ubuntu 24.04 | Arch Linux |
|-------|------------|-------------|--------------|------------|
| **Sistem inti** | Read-only (OSTree) | Mutable (RPM) | Mutable (APT) | Mutable (Pacman) |
| **Rollback OS** | ✅ Otomatis | ❌ Tidak ada | ❌ Tidak ada | ⚠️ Manual (Btrfs) |
| **Install app GUI** | Flatpak utama | RPM / Flatpak | APT / Snap / Flatpak | Pacman / AUR |
| **Update atomik** | ✅ Ya | ❌ Tidak | ❌ Tidak | ❌ Tidak |
| **Container-first** | ✅ Desain utama | ⚠️ Bisa | ⚠️ Bisa | ⚠️ Bisa |
| **Kurva belajar** | Menengah-Tinggi | Rendah | Rendah | Sangat Tinggi |

---

## Bab 3 — Instalasi & Konfigurasi Awal

### 3.1 Persyaratan Sistem

| Komponen | Minimum | Disarankan |
|----------|---------|------------|
| **RAM** | 4 GB | 8 GB+ |
| **Penyimpanan** | 40 GB HDD | 80 GB+ SSD |
| **Prosesor** | 64-bit (x86-64 / aarch64) | Multi-core modern |
| **GPU** | Integrated graphics | GPU diskret (NVIDIA/AMD) |
| **Internet** | Opsional saat instalasi | Wajib untuk Flatpak & update |

---

### 3.2 Instalasi Step-by-Step

#### Langkah 1: Download ISO & Buat USB Bootable

```bash
# Download ISO dari:
# https://fedoraproject.org/atomic-desktops/silverblue/

# Verifikasi integritas ISO
sha256sum Fedora-Silverblue-ostree-x86_64-44-1.4.iso
# Bandingkan dengan checksum di halaman download

# Buat USB bootable via terminal
sudo dd if=Fedora-Silverblue-*.iso of=/dev/sdX bs=4M status=progress conv=fsync
```

> 💡 **Tip:** Gunakan **Fedora Media Writer** (GUI) untuk membuat USB bootable dengan lebih aman — tersedia untuk Windows, macOS, dan Linux.

#### Langkah 2: Proses Instalasi

1. Boot dari USB via BIOS/UEFI (F2, F12, DEL, atau ESC)
2. Pilih **"Start Fedora-Silverblue-44 Live"** di menu GRUB
3. Klik **"Install to Hard Drive"** di Live Environment
4. Di **Anaconda Installer**, konfigurasi:
   - **Bahasa:** Indonesia atau English
   - **Keyboard:** Indonesian
   - **Zona Waktu:** Asia/Jakarta (WIB) / Asia/Makassar (WITA) / Asia/Jayapura (WIT)
   - **Tujuan Instalasi:** Pilih disk target
5. **Partisi:**
   - **Automatic** *(Rekomendasi)*: Btrfs otomatis (EFI + `/boot` + `/`)
   - **Custom**: Untuk dual-boot atau kebutuhan khusus
6. Klik **"Begin Installation"** → tunggu 10-20 menit → restart

> ⚠️ **Peringatan Dual-Boot:** Selalu install **Windows DULU**, baru Fedora. Pilih partisi kosong yang tersedia dan **jangan sentuh** partisi Windows/EFI yang sudah ada!

---

### 3.3 Konfigurasi Wajib Pasca-Instalasi

#### Langkah 1: Aktifkan Flathub (WAJIB)

```bash
flatpak remote-add --if-not-exists flathub \
  https://flathub.org/repo/flathub.flatpakrepo

# Verifikasi
flatpak remotes
# Output harus menampilkan "flathub" dengan URL flathub.org
```

#### Langkah 2: Aktifkan RPM Fusion

```bash
rpm-ostree install \
  https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm \
  https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm

# REBOOT dulu!
systemctl reboot

# Setelah reboot: aktifkan codec multimedia
rpm-ostree install ffmpeg gstreamer1-plugin-openh264
```

#### Langkah 3: Instal Aplikasi Esensial via Flatpak

```bash
# Browser
flatpak install flathub org.mozilla.firefox
flatpak install flathub com.google.Chrome

# Manajemen Flatpak & Extensions
flatpak install flathub com.github.tchx84.Flatseal
flatpak install flathub com.mattjakeman.ExtensionManager

# Produktivitas
flatpak install flathub org.libreoffice.LibreOffice
flatpak install flathub com.discordapp.Discord
flatpak install flathub com.spotify.Client

# Video player
flatpak install flathub io.github.celluloid_player.Celluloid
```

#### Langkah 4: Aktifkan Auto-Update Staging

```bash
sudo bash -c 'echo "AutomaticUpdatePolicy=stage" >> /etc/rpm-ostreed.conf'
sudo systemctl enable --now rpm-ostreed-automatic.timer

# Verifikasi
cat /etc/rpm-ostreed.conf | grep AutomaticUpdate
```

#### Langkah 5: Install GNOME Tweaks

```bash
rpm-ostree install gnome-tweaks
# Reboot untuk mengaktifkan
```

---

### 3.4 Upgrade ke Versi Fedora Berikutnya (Rebase)

Di Silverblue, upgrade versi disebut **"rebase"** — sistem secara atomik berpindah ke commit OSTree versi baru.

```bash
# Cek versi saat ini
rpm-ostree status

# Rebase ke Silverblue 44 (contoh dari F43)
rpm-ostree rebase fedora:fedora/44/x86_64/silverblue

# Reboot untuk mengaktifkan
systemctl reboot

# Jika ada masalah: rollback
rpm-ostree rollback
```

> ⚠️ **Peringatan:** JANGAN skip versi! Proses rebase harus dilakukan satu versi per satu: `42 → 43 → 44`.

---

## Bab 4 — Manajemen Aplikasi & Paket

### 4.1 Arsitektur Layer Instalasi

| Layer | Teknologi | Contoh Software | Keterangan |
|-------|-----------|-----------------|------------|
| **1 — Base OS** | OSTree / rpm-ostree | Kernel, systemd, GNOME | Read-only, dari Fedora upstream |
| **2 — RPM Layer** | rpm-ostree install | Driver NVIDIA, codec, font | Di-overlay di atas base, butuh reboot |
| **3 — Containers** | Toolbx / Distrobox | gcc, nodejs, python-dev | Mutable, terisolasi per container |
| **4 — Flatpak** | flatpak install | Firefox, Spotify, GIMP | Sandboxed, no reboot |
| **5 — AppImage** | ./NamaApp.AppImage | App tanpa repo | Portable, tanpa instalasi |

> 💡 **Prioritas yang disarankan:** `Flatpak → Toolbx → rpm-ostree layer`

---

### 4.2 Flatpak via Terminal (Direkomendasikan)

```bash
# ─── PENCARIAN ───────────────────────────────────────
flatpak search code

# ─── INSTALASI ───────────────────────────────────────
flatpak install flathub com.visualstudio.code
flatpak install flathub org.gimp.GIMP
flatpak install -y flathub com.spotify.Client   # tanpa konfirmasi

# ─── UPDATE ──────────────────────────────────────────
flatpak update                              # semua aplikasi
flatpak update com.visualstudio.code        # satu aplikasi

# ─── UNINSTALL ───────────────────────────────────────
flatpak uninstall com.spotify.Client
flatpak uninstall --unused                  # hapus runtime tidak terpakai

# ─── INFORMASI & LIST ────────────────────────────────
flatpak list --app
flatpak info com.visualstudio.code
flatpak info --show-permissions com.visualstudio.code

# ─── MANAJEMEN IZIN ──────────────────────────────────
flatpak override --user --filesystem=home com.visualstudio.code
flatpak override --user --nodevice=all com.example.App
flatpak override --user --reset com.example.App
```

---

### 4.3 Toolbx untuk Development

```bash
# ─── WORKFLOW PYTHON ─────────────────────────────────
toolbox create python-project
toolbox enter python-project
sudo dnf install python3-pip python3-devel gcc
pip install flask sqlalchemy pytest
cd ~/Projects/my-flask-app && python app.py

# ─── WORKFLOW NODE.JS ────────────────────────────────
toolbox create nodejs-project
toolbox enter nodejs-project
sudo dnf install nodejs npm
cd ~/Projects/my-react-app && npm install && npm start

# ─── JALANKAN TANPA ENTER CONTAINER ──────────────────
toolbox run -c python-project python3 ~/script.py
toolbox run -c nodejs-project npm test
```

> 💡 **Best Practice:** Buat **satu container per proyek** atau teknologi: `toolbox-python`, `toolbox-nodejs`, `toolbox-go`, `toolbox-rust`.

---

### 4.4 rpm-ostree Layer (Pilihan Terakhir)

Gunakan **hanya** untuk: driver hardware, codec sistem, font sistem, dan tools yang butuh integrasi mendalam dengan OS.

```bash
# ✅ Driver NVIDIA
rpm-ostree install akmod-nvidia xorg-x11-drv-nvidia

# ✅ Codec multimedia sistem
rpm-ostree install ffmpeg gstreamer1-plugins-bad-free-extras

# ✅ Font sistem
rpm-ostree install google-noto-fonts-common

# ✅ Shell pengganti (contoh: ZSH)
rpm-ostree install zsh
chsh -s /usr/bin/zsh

# ─── MANAJEMEN LAYER ──────────────────────────────────
rpm-ostree status                    # status + daftar paket di-layer
rpm-ostree uninstall nama-paket      # hapus dari layer
rpm-ostree override reset --all      # reset semua override
```

---

### 4.5 Tabel Keputusan Cepat

| Jenis Software | Metode | Perlu Reboot? |
|----------------|--------|---------------|
| Aplikasi GUI umum | Flatpak (Flathub) | ❌ Tidak |
| Tools CLI development | Toolbx / Distrobox | ❌ Tidak |
| Driver GPU & hardware | rpm-ostree | ✅ Ya |
| Codec multimedia | rpm-ostree | ✅ Ya |
| Font sistem | rpm-ostree | ✅ Ya |
| App tanpa repo resmi | AppImage | ❌ Tidak |
| **Snap packages** | **❌ TIDAK KOMPATIBEL** | — |

> ⚠️ **Snap tidak kompatibel** dengan Silverblue. Gunakan Flatpak sebagai alternatif — hampir semua app di Snap juga tersedia di Flathub.

---

## Bab 5 — Kustomisasi Tampilan GNOME

### 5.1 GNOME Tweaks

```bash
rpm-ostree install gnome-tweaks
systemctl reboot

# Buka setelah reboot
gnome-tweaks
```

**Fitur utama:** Appearance (tema GTK, ikon, kursor), Fonts, Keyboard & Mouse, Startup Applications, Window Titlebars.

---

### 5.2 GNOME Extensions

**Extension Manager (Direkomendasikan):**

```bash
flatpak install flathub com.mattjakeman.ExtensionManager
```

**Extensions populer untuk Silverblue + GNOME 50:**

| Extension | Fungsi | Cara Install |
|-----------|--------|--------------|
| **Dash to Dock** | Dock permanen di bawah/samping | Extension Manager / RPM |
| **Blur my Shell** | Efek blur di panel & overview | Extension Manager |
| **Vitals** | Monitor CPU, RAM, temp di panel | Extension Manager |
| **User Themes** | Aktifkan tema shell kustom | Extension Manager / RPM |
| **AppIndicator Support** | Tampilkan tray icon sistem lama | Extension Manager |
| **GSConnect** | Integrasi Android (KDE Connect) | Extension Manager / RPM |

> ⚠️ **Peringatan:** Extensions sangat terikat dengan versi GNOME Shell. Selalu cek kompatibilitas sebelum install.

---

### 5.3 Tema & Ikon

Di Silverblue, `/usr/share/themes` dan `/usr/share/icons` adalah **read-only**. Simpan tema di home directory:

```bash
mkdir -p ~/.themes ~/.icons ~/.local/share/fonts

# Install tema GTK
cp -r WhiteSur-gtk-theme/WhiteSur-Light ~/.themes/

# Install ikon
cp -r Papirus ~/.icons/

# Aktifkan via terminal
gsettings set org.gnome.desktop.interface gtk-theme "WhiteSur-Light"
gsettings set org.gnome.desktop.interface icon-theme "Papirus"
```

> 💡 **Sumber tema:** [gnome-look.org](https://www.gnome-look.org). Untuk GNOME 50, filter menggunakan **"GTK4"**.

---

### 5.4 Kustomisasi via gsettings

```bash
# Dark mode
gsettings set org.gnome.desktop.interface color-scheme "prefer-dark"

# Light mode
gsettings set org.gnome.desktop.interface color-scheme "default"

# Accent color (GNOME 50 native)
# Opsi: blue, teal, green, yellow, orange, red, pink, purple, slate
gsettings set org.gnome.desktop.interface accent-color "blue"

# Scaling font (HiDPI)
gsettings set org.gnome.desktop.interface text-scaling-factor 1.25

# Tampilkan tombol minimize & maximize
gsettings set org.gnome.desktop.wm.preferences button-layout 'appmenu:minimize,maximize,close'

# Night Light
gsettings set org.gnome.settings-daemon.plugins.color night-light-enabled true
gsettings set org.gnome.settings-daemon.plugins.color night-light-temperature 4000

# Nonaktifkan animasi (mempercepat di hardware lemah)
gsettings set org.gnome.desktop.interface enable-animations false

# Font kustom
gsettings set org.gnome.desktop.interface font-name "Inter 11"
gsettings set org.gnome.desktop.interface monospace-font-name "JetBrains Mono 10"

# Lihat semua key yang tersedia
gsettings list-recursively | grep gnome.desktop.interface
```

---

## Glosarium

| Istilah | Definisi |
|---------|----------|
| **AppImage** | Format distribusi app Linux yang bisa dijalankan langsung tanpa instalasi. |
| **Atomic Update** | Pembaruan yang diterapkan seluruhnya atau tidak sama sekali. |
| **Commit (OSTree)** | Snapshot filesystem pada titik waktu tertentu, mirip "commit" di Git. |
| **Container** | Lingkungan terisolasi yang berbagi kernel dengan host. |
| **Deployment** | Satu versi OS yang tersimpan di disk. Silverblue menyimpan 2: aktif & sebelumnya. |
| **Distrobox** | Tools untuk menjalankan container dari distro manapun di atas Fedora. |
| **Flatpak** | Sistem distribusi aplikasi cross-distro dengan sandboxing. |
| **Flathub** | Repositori Flatpak terbesar dan paling populer. |
| **Flatseal** | GUI untuk mengelola izin sandbox Flatpak. |
| **Immutable OS** | OS di mana direktori inti sistem bersifat read-only saat runtime. |
| **OSTree** | Sistem versioning filesystem Linux — seperti Git untuk seluruh isi OS. |
| **Podman** | Container engine daemonless dan rootless bawaan Fedora. |
| **Rebase** | Proses upgrade Silverblue ke versi Fedora berikutnya secara atomik. |
| **RHEL** | Red Hat Enterprise Linux — distribusi enterprise berbayar. Fedora adalah upstream-nya. |
| **Rollback** | Kembali ke deployment sebelumnya via terminal atau menu GRUB. |
| **rpm-ostree** | Tools hybrid OSTree + RPM yang mengelola siklus OS Silverblue. |
| **SELinux** | Modul keamanan kernel berbasis label — akses tanpa izin ditolak, bahkan untuk root. |
| **Silverblue** | Fedora Atomic Desktop dengan GNOME — edisi flagship immutable Fedora. |
| **Staging (Update)** | Update diunduh di background, aktif setelah reboot berikutnya. |
| **Toolbx** | Tools resmi Fedora untuk container mutable di atas Silverblue. |
| **Wayland** | Protokol windowing system modern pengganti X11. Default di GNOME 50. |

---

## Referensi Resmi

| Sumber | URL |
|--------|-----|
| Dokumentasi Fedora Silverblue | https://docs.fedoraproject.org/en-US/fedora-silverblue/ |
| Fedora Project | https://fedoraproject.org |
| Flathub | https://flathub.org |
| RPM Fusion | https://rpmfusion.org |
| OSTree Docs | https://ostreedev.github.io/ostree/ |
| Toolbx | https://containertoolbx.org |
| Distrobox | https://distrobox.it |
| GNOME Extensions | https://extensions.gnome.org |
| GNOME Look (Tema & Ikon) | https://gnome-look.org |
| Flatpak Docs | https://docs.flatpak.org |

---

> *Panduan ini dibuat berdasarkan kondisi Fedora 44 (April 2026). Informasi mungkin berubah pada versi Fedora berikutnya.*
