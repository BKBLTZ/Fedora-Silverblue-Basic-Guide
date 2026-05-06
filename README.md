🪨 Panduan Fedora Linux & Silverblue

**Dokumentasi lengkap untuk pengguna Linux menengah — dari instalasi hingga workflow modern.**

[![Fedora 44](https://img.shields.io/badge/Fedora-44-51A2DA?style=flat-square&logo=fedora&logoColor=white)](https://fedoraproject.org)
[![Silverblue](https://img.shields.io/badge/Silverblue-Atomic-3c6eb4?style=flat-square)](https://fedoraproject.org/atomic-desktops/silverblue/)
[![GNOME](https://img.shields.io/badge/GNOME-50-4A86CF?style=flat-square&logo=gnome&logoColor=white)](https://gnome.org)
[![License](https://img.shields.io/badge/Lisensi-CC%20BY--SA%204.0-lightgrey?style=flat-square)](LICENSE)

</div>

---

## 📖 Tentang Panduan Ini

Panduan ini ditulis untuk pengguna yang sudah familiar dengan Linux dan ingin memahami ekosistem **Fedora** secara mendalam — terutama **Fedora Silverblue**, distribusi berbasis *immutable/atomic desktop* yang menjadi masa depan Fedora untuk pengguna desktop.

Topik yang dibahas mencakup arsitektur sistem, manajemen paket multi-layer, containerisasi, hingga kustomisasi tampilan GNOME.

> **Edisi:** Fedora 44 · April 2026

---

## 📚 Daftar Isi

| # | Bab | Topik Utama |
|---|-----|-------------|
| 1 | [Fedora Linux: Keluarga & Ekosistem](panduan-fedora-silverblue.md#bab-1--fedora-linux-keluarga--ekosistem) | Official Editions, Atomic Desktops, Spins, Labs |
| 2 | [Silverblue: Arsitektur Mendalam](panduan-fedora-silverblue.md#bab-2--fedora-silverblue-arsitektur-mendalam) | rpm-ostree, Flatpak, Toolbx, SELinux |
| 3 | [Instalasi & Konfigurasi Awal](panduan-fedora-silverblue.md#bab-3--instalasi--konfigurasi-awal) | Instalasi, pasca-install, rebase versi |
| 4 | [Manajemen Aplikasi & Paket](panduan-fedora-silverblue.md#bab-4--manajemen-aplikasi--paket) | Flatpak, Toolbx, Distrobox, rpm-ostree |
| 5 | [Kustomisasi Tampilan GNOME](panduan-fedora-silverblue.md#bab-5--kustomisasi-tampilan-gnome) | Tweaks, Extensions, Tema, gsettings |
| — | [Glosarium](panduan-fedora-silverblue.md#glosarium) | Definisi istilah teknis |

---

## ⚡ Quick Reference

Ringkasan perintah yang paling sering dipakai:

```bash
# Cek status sistem Silverblue
rpm-ostree status

# Update OS (staging — aktif setelah reboot)
rpm-ostree upgrade && systemctl reboot

# Rollback ke versi sebelumnya
rpm-ostree rollback

# Aktifkan Flathub (wajib setelah instalasi baru)
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo

# Install aplikasi GUI (selalu via Flathub)
flatpak install flathub com.visualstudio.code

# Buat container development (Toolbx)
toolbox create mydev && toolbox enter mydev

# Instal paket di dalam Toolbx (seperti dnf biasa)
sudo dnf install nodejs npm python3-pip
```

---

## 🗺️ Alur Instalasi Silverblue

```
Download ISO ──► Buat USB Bootable ──► Boot & Install
                                             │
                                             ▼
                                    Aktifkan Flathub
                                             │
                                             ▼
                                    Aktifkan RPM Fusion
                                             │
                                             ▼
                                    Install Aplikasi (Flatpak)
                                             │
                                             ▼
                                    Konfigurasi GNOME
```

---

## 📦 Panduan Memilih Metode Instalasi

| Jenis Software | Metode | Perlu Reboot? |
|----------------|--------|:---:|
| Aplikasi GUI (browser, editor, media) | Flatpak (Flathub) | ❌ |
| Tools CLI development (gcc, npm, pip) | Toolbx / Distrobox | ❌ |
| Driver GPU / codec sistem | rpm-ostree | ✅ |
| Font sistem | rpm-ostree | ✅ |
| Snap packages | ❌ Tidak kompatibel | — |

> **Prioritas:** `Flatpak → Toolbx → rpm-ostree`

---

## 🔗 Referensi Resmi

- [Dokumentasi Fedora Silverblue](https://docs.fedoraproject.org/en-US/fedora-silverblue/)
- [Flathub](https://flathub.org)
- [RPM Fusion](https://rpmfusion.org)
- [Toolbx](https://containertoolbx.org)
- [Distrobox](https://distrobox.it)
- [Flatpak Docs](https://docs.flatpak.org)
- [GNOME Extensions](https://extensions.gnome.org)
- [GNOME Look](https://gnome-look.org)

---

<div align="center">

*Dibuat berdasarkan kondisi Fedora 44 · April 2026*  
*Informasi mungkin berubah pada versi Fedora berikutnya*

</div>
