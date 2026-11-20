---
title: Penataan Struktur Folder Proyek
type: session-memory
tags:
  - session-memory
  - laptop-b
  - folder-organization
  - maintenance
date: 2026-11-14
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
---

# Penataan Struktur Folder Proyek

## 1. Tujuan & Konteks Sesi
- Merapikan tata letak file di root repository `AR Dragon` dan root project Unity `AR Dragon Sky Guardian/` yang sebelumnya menumpuk (aset `.blend`, skrip Blender, paket VFX beserta arsip `.zip`, screenshot preview, dokumen Office/Excel, launcher `.bat`, serta file JSON sementara).
- Memastikan seluruh pemindahan file tetap menjaga integrasi antar-komponen (`PythonProcessManager.cs`, impor modul `ARDragonSkyGuardian_CV/`, serta vault `brain/`).

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Mempertahankan Direktori Inti di Root:**
   - `AR Dragon Sky Guardian/` (Unity 6) dan `ARDragonSkyGuardian_CV/` (Python CV) tetap dipertahankan di root dengan struktur internalnya agar deteksi otomatis path di `PythonProcessManager.cs` (`../ARDragonSkyGuardian_CV/active_cv_sender.py` dan `venv/Scripts/python.exe`) serta impor langsung antar-skrip di `ARDragonSkyGuardian_CV/` (`from motion_recognizer import ...`, `from player_calibrator import ...`, dll.) tidak rusak.
   - `brain/`, `.git/`, `.gitignore`, `.obsidian/`, dan `AGENTS.md` tetap di root.
2. **Pengelompokan Aset ke `assets/`:**
   - Seluruh aset non-Unity-imported (`.blend`/`.blend1`, skrip Blender Python, VFX mentah, arsip `.zip`, dan screenshot preview) dipusatkan di `assets/` dengan subfolder fungsional (`blender/`, `blender/scripts/`, `vfx/`, `archives/`, `screenshots/`).
   - Struktur ganda `vfx/vfx/` diratakan menjadi `assets/vfx/`.
3. **Pemisahan Dokumen & Launcher ke `docs/` dan `tools/`:**
   - Dokumen proyek (`.docx`, `.xlsx`) dipindahkan ke `docs/`.
   - Skrip peluncur (`.bat`) dipindahkan ke `tools/`.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **`assets/`:**
  - `Assets AR Dragon Sky Guardian/*.blend*` $\rightarrow$ `assets/blender/` (`Dragon_Model.blend`, `Nagcu.blend`, `Nagcu.blend1`, `pohon.blend`, `pohon.blend1`, `pohon_backup.blend`, `pulau.blend`, `rumah.blend`, `rumah.blend1`, `rumah_backup.blend`, `rumah_backup.blend1`).
  - `Assets AR Dragon Sky Guardian/*.py` $\rightarrow$ `assets/blender/scripts/` (`apply_textures.py`, `apply_textures_pohon.py`, `apply_textures_pohon_final.py`, `inspect_slots.py`, `list_pohon.py`).
  - Menghapus folder kosong `Assets AR Dragon Sky Guardian/`.
  - `vfx/vfx/*` $\rightarrow$ `assets/vfx/` (`PNG/`, `Unity samples/`, `Kenney.url`, `License.txt`, `Patreon.url`, `Preview.png`) dan menghapus folder kosong `vfx/`.
  - `vfx.zip` $\rightarrow$ `assets/archives/vfx.zip`.
  - `AR Dragon Sky Guardian/*.png` (8 file: `game_hud_tpose.png`, `game_hud_yta.png`, `game_preview_dragon.png`, `game_preview_live.png`, `game_view_playmode.png`, `island_game_view.png`, `island_scene_view.png`, `scene_preview.png`) $\rightarrow$ `assets/screenshots/`.
- **`docs/`:**
  - `Dokumentasi Projek.docx` $\rightarrow$ `docs/Dokumentasi Projek.docx`.
  - `TS & List Asset.xlsx` $\rightarrow$ `docs/TS & List Asset.xlsx`.
- **`tools/`:**
  - `jalankan_dragon.bat` $\rightarrow$ `tools/jalankan_dragon.bat`.
  - `Run Hermes Desktop.bat` $\rightarrow$ `tools/Run Hermes Desktop.bat`.
- **Pembersihan Root:**
  - Menghapus `test_profile_tmp.json` (sisa output sementara tes kalibrasi) dari root.
- **Dokumentasi Brain:**
  - Memperbarui `brain/laptop-b/Tasks.md` dan `brain/laptop-b/Devlog.md`, lalu commit (`88da95e`) & push ke `origin main`.

---

## 4. Detail Penting yang Harus Diingat
- `tools/jalankan_dragon.bat` memiliki path absolut `PROJECT_DIR=D:\Fauzih\Kuliah\SEMESTER V\Proyek Multimedia dan Game\AR Dragon` yang tetap valid walaupun file `.bat` kini berada di `tools/`.
- `ARDragonSkyGuardian_CV/test_phase4_validation.py` menulis `test_profile_tmp.json` secara relatif terhadap working directory saat dijalankan; jika dijalankan dari root, file sementara dapat muncul kembali di root.
- Seluruh referensi internal `PythonProcessManager.cs` ke `ARDragonSkyGuardian_CV/active_cv_sender.py` tetap tidak terpengaruh oleh penataan ini.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] Pastikan saat melakukan commit pada repo utama `AR Dragon` (jika diperlukan), perubahan pemindahan folder aset/dokumen ikut dirapikan atau disesuaikan dengan `.gitignore`.
- [ ] Lanjutkan antrean aktif di [[Tasks]] (**Uji Play Mode Webcam**, **Texturing & Ekspor `rumah.blend`**, **Sesi GUI**, **Sesi Model Pulau**, **Sesi 4**, **Sesi 5**).

---
**Navigasi Obsidian:** [[00 - Overview]] · [[Tasks]] · [[Devlog]]
