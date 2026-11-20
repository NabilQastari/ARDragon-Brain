---
title: "Setup Optimasi Token Laptop B"
type: session-memory
tags:
  - session-memory
  - laptop-b
  - token-optimization
  - rtk
  - tooling
date: 2026-11-21
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[laptop-a/planning_optimasi_token_laptop_b]]"
---

# Setup Optimasi Token Laptop B

## 1. Tujuan & Konteks Sesi
Mengimplementasikan dan mengonfigurasi tumpukan optimasi token (*The Optimal Lean Stack*) di lingkungan kerja **Laptop B** (`AR Dragon`) berdasarkan cetak biru arsitektur di [[laptop-a/planning_optimasi_token_laptop_b]]. Tujuannya adalah menekan konsumsi token input/output melalui pemblokiran indeks berkas sampah/biner, kompresi log terminal dengan `rtk`, dan penerapan aturan coding minimalis di `AGENTS.md`.

## 2. Keputusan Desain / Arsitektur / Teknis
- **Pemblokiran Konteks via `.antigravityignore`:** Menyalin penuh template Bagian 3A dari [[laptop-a/planning_optimasi_token_laptop_b]] ke root workspace agar direktori build/cache Unity (`/[Ll]ibrary/`, `/[Tt]emp/`, `/[Oo]bj/`, `/[Bb]uild/`, `*.meta`, dll.), cache Python (`**/__pycache__/`, `.pytest_cache/`, `.venv/`, `venv/`), aset 3D mentah (`*.blend`, `*.fbx`, `*.obj`), arsip (`*.zip`, `*.xlsx`, `*.docx`, `*.pdf`), media dump, dan isolasi `brain/laptop-a/memories/` + `brain/.obsidian/` tidak membebani pemindaian konteks.
- **Kompresi Output Terminal via `rtk` (Rust Token Killer):** Menginstal paket resmi `rtk-ai.rtk` menggunakan `winget` alih-alih menempatkan binary manual, dan menggunakannya untuk membungkus perintah Git, `pytest`, dan `pip` (dengan pengecualian untuk pipeline PowerShell `|` / `Select-Object` / cmdlet Windows).
- **Prinsip Coding Minimalis (*Ponytail-Lite / YAGNI*) dengan Penjaga Kontrak:** Aturan efisiensi kode di `AGENTS.md` tidak boleh melanggar kontrak integrasi di `kontrak_integrasi_ui_dan_progress.md`, struktur *Hybrid UI* (Live/Placeholder/Dormant), atribut `[SerializeField]`, maupun namespace `DragonWingAR.*`.

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **[`.antigravityignore`](file:///d:/Fauzih/Kuliah/SEMESTER%20V/Proyek%20Multimedia%20dan%20Game/AR%20Dragon/.antigravityignore) (root workspace):** Dibuat baru dengan seluruh aturan pengecualian Unity, IDE, Python CV, aset berat, arsip, media dump, dan isolasi Obsidian Laptop A.
- **Instalasi `rtk` (`1.2.0`):**
  - Pengecekan awal `rtk --version` tidak ditemukan; diinstal via `winget install rtk-ai.rtk --accept-source-agreements --accept-package-agreements`.
  - Binary terpasang di `C:\Users\ASUS\AppData\Local\Microsoft\WinGet\Packages\rtk-ai.rtk_Microsoft.Winget.Source_8wekyb3d8bbwe\rtk.exe` dan terdaftar ke User `PATH`.
- **[`AGENTS.md`](file:///d:/Fauzih/Kuliah/SEMESTER%20V/Proyek%20Multimedia%20dan%20Game/AR%20Dragon/AGENTS.md):** Ditambahkan section `## ⚡ Aturan Efisiensi Token & Eksekusi Minimalis (Laptop B)` berisi panduan `rtk`, prinsip *Ponytail-Lite / YAGNI*, toleransi kontrak arsitektur, dan *Session Hygiene* (`/compact`).
- **Verifikasi (Sanity Check):**
  - Awalnya diuji langsung lewat path lengkap `rtk.exe --version` (`rtk 1.2.0`) dan `rtk.exe git status` (`* master...origin/master`, exit code `0`); setelah server/sesi restart, panggilan bare `rtk --version` dan `rtk git -C brain status` terverifikasi aktif langsung dari `PATH`.
  - Diverifikasi berkas skrip inti `.cs` (`Assets/_Scripts/`), `.py` & `.json` (`ARDragonSkyGuardian_CV/`), dan `.md` (`AGENTS.md`, `brain/`) tetap terbaca normal.
- **Pembaruan Log & Push `brain/`:** Memperbarui `brain/laptop-b/Devlog.md` dan `brain/laptop-b/Tasks.md`, melakukan rebase terhadap update Laptop A (`2233476`), dan push ke `origin main` (commit `5fdf572`).

## 4. Detail Penting yang Harus Diingat
- **Lokasi Binary & `PATH` `rtk`:** `C:\Users\ASUS\AppData\Local\Microsoft\WinGet\Packages\rtk-ai.rtk_Microsoft.Winget.Source_8wekyb3d8bbwe\rtk.exe` (`rtk 1.2.0`). Perubahan User `PATH` oleh `winget` baru aktif pada shell/IDE setelah restart proses parent.
- **Catatan Hook `rtk`:** Output `rtk` menampilkan info `[rtk] /!\ No hook installed — run rtk init -g for automatic token savings` — saat ini pemakaian `rtk` di Laptop B dilakukan secara eksplisit via awalan perintah `rtk` sesuai `AGENTS.md`.
- **Pengecualian `.antigravityignore`:** Tetap izinkan `.cs`, `.py`, `.json`, dan `.md`; tidak ada file kode sumber yang terblokir.
- **Perilaku Git `brain/`:** `laptop-a/memories/Desain UI dan Denah Pulau 3 Biome.md` sempat muncul dengan peringatan line-ending (`LF will be replaced by CRLF`), namun tidak memiliki diff aktual — selalu stage file di `laptop-b/` secara eksplisit saat commit.

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] Uji Play Mode Webcam secara langsung (`Fly Up`, `Hover`, `Gentle Descent`, serta verifikasi visual Tahap 1 3-titik wajah) di Unity Editor.
- [ ] Lanjutkan ke **Revisi Titik Wajah — Tahap 2** (penyesuaian controller Unity: `DragonFlightController.cs`, `UDPPoseReceiver.cs`, `DragonHeadLookController`) lalu **Sesi 4** (`VillageZoneController` + VFX Effekseer) dan **Sesi 5** (`GameManager` & `AudioManager`) dengan menerapkan `rtk`, prinsip minimalis `AGENTS.md`, dan `/compact` antar-fokus besar.

---
**Navigasi Obsidian:** [[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[konteks]] · [[prd]] · [[laptop-a/planning_optimasi_token_laptop_b]]
