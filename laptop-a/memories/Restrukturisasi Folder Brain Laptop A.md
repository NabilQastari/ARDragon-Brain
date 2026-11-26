---
title: Restrukturisasi Folder Brain Laptop A
type: session-memory
tags:
  - session-memory
  - laptop-a
  - obsidian-vault
  - brain-organization
  - architecture
date: 2026-11-26
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
---

# 🗂️ Restrukturisasi & Penataan Folder Kategori Brain Laptop A

> **Ringkasan Eksekutif:** Penataan menyeluruh terhadap 19 berkas spesifikasi, perencanaan teknis, dan prompt handoff yang menumpuk di root `brain/laptop-a/` ke dalam 5 subfolder kategori fungsional berbasis domain game development (`ui-ux/`, `gameplay-mechanics/`, `cv-control/`, `visual-vfx/`, `handoff-laptop-b/`). Dokumen inti pelacakan (`Tasks.md`, `Devlog.md`, `konteks.md`, `prd.md`) dan `memories/` dipertahankan di root agar simetris dengan Laptop B dan mematuhi aturan anti-collision `AGENTS.md`.

---

## 1. Tujuan & Konteks Sesi

- **Pemicu:** Pengguna menginstruksikan *"rapikan brain laptop a / buat folder baru berdasarkan kategori brain"* untuk menertibkan file markdown yang bertambah banyak pasca-desain Sesi 4, 4B, dan 5.
- **Kondisi Awal:** Di direktori `brain/laptop-a/` terdapat 23 file markdown di tingkat root dan 1 folder `memories/`. Dari 23 file tersebut, hanya 4 yang merupakan dokumen pelacakan/arsitektur utama, sedangkan 19 lainnya adalah dokumen spesifikasi teknis dan prompt eksekusi yang belum terkelompokkan.
- **Target:** Membangun struktur folder modular yang rapi, menjaga integritas resolusi wikilinks di Obsidian, mematuhi batasan write-permission (hanya memodifikasi `laptop-a/` dan `00 - Overview.md`), serta memperbarui peta navigasi vault.

---

## 2. Keputusan Desain / Arsitektur / Teknis

1. **Pemilihan 5 Folder Kategori Modular:**
   - `ui-ux/`: Wadah untuk desain antarmuka, aset Unity UI export kit, dan kontrak integrasi progress.
   - `gameplay-mechanics/`: Wadah untuk alur quest 3 desa, fisika momentum penerbangan naga, arsitektur game loop, sistem penilaian 3 bintang, dan kamera dinamis.
   - `cv-control/`: Wadah untuk panduan 17 pose kontrol, audit preview CV (FPS only), revisi 3 titik wajah, dan generator prompt visual siluet/kerangka pose CV.
   - `visual-vfx/`: Wadah untuk rencana implementasi skill Effekseer, dynamic camera FOV ($60^\circ–85^\circ$), skybox adaptif 3 biome, dan master prompt visual.
   - `handoff-laptop-b/`: Wadah untuk petunjuk operasional dan handoff umum Laptop B (patch planning, checklist pra-sesi 4, panduan optimasi token, dan SOP screenshot mandiri).
2. **Prinsip Simetri & Anti-Collision (`AGENTS.md`):**
   - Dokumen inti pelacakan (`Tasks.md`, `Devlog.md`, `konteks.md`, `prd.md`) tetap berada di root `brain/laptop-a/`, identik dengan hierarki di `brain/laptop-b/` (`Devlog.md`, `Tasks.md`, `konteks.md`, `prd.md`, `progress.md`).
   - Folder catatan memori sesi historis tetap berada di `brain/laptop-a/memories/` untuk menjamin kepatuhan 100% terhadap protokol `"jadikan sesi ini memori"`.
   - Tidak ada modifikasi apa pun pada `brain/laptop-b/` (*strict zero write*).
3. **Penyelarasan Wikilinks & Peta Pengetahuan:**
   - Wikilinks pendek bawaan Obsidian (`[[filename]]`) tetap berfungsi otomatis karena nama file bersifat unik di seluruh vault.
   - Peta pengetahuan di `brain/00 - Overview.md` diperbarui dengan heading kategori terstruktur dan link eksplisit format `[[laptop-a/<kategori>/<file>|<label>]]` untuk kemudahan navigasi pengguna.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset

1. **Pembuatan Direktori:**
   - Dibuat 5 subdirektori baru di bawah `brain/laptop-a/`: `ui-ux`, `gameplay-mechanics`, `cv-control`, `visual-vfx`, dan `handoff-laptop-b`.
2. **Migrasi File Menggunakan `git mv` (19 File):**
   - **`ui-ux/` (3 file):**
     - `rencana_desain_ui.md`
     - `panduan_aset_ui_unity.md`
     - `kontrak_integrasi_ui_dan_progress.md`
   - **`gameplay-mechanics/` (5 file):**
     - `mekanisme_tantangan_desa.md`
     - `planning_momentum_dan_revisi_kontrol.md`
     - `arsitektur_sesi_5_gameloop_dan_scoring.md`
     - `prompt_eksekusi_sesi_5_gameloop_dan_scoring_laptop_b.md`
     - `planning_kamera_dinamis_dan_navigasi_pause.md`
   - **`cv-control/` (4 file):**
     - `panduan_detail_pose_dan_sync_laptop_b.md`
     - `planning_audit_pose_dan_clean_cv_preview.md`
     - `planning_revisi_titik_wajah_dan_pov.md`
     - `prompt_visual_pose_cv_kerangka.md`
   - **`visual-vfx/` (3 file):**
     - `rencana_skill_vfx_effekseer_laptop_b.md`
     - `planning_vfx_fov_skybox_dan_animasi.md`
     - `prompt_eksekusi_vfx_skybox_dan_fov_laptop_b.md`
   - **`handoff-laptop-b/` (4 file):**
     - `laptop B Patch 1 planning.md`
     - `checklist_finalisasi_pra_sesi_4.md`
     - `planning_optimasi_token_laptop_b.md`
     - `panduan_screenshot_mandiri_dan_laporan.md`
3. **Pembaruan Navigasi Vault:**
   - Memperbarui `brain/00 - Overview.md` bagian *Dokumen Desain & Arsitektur Laptop A* menjadi 5 sub-kategori rapi dengan tautan file aktif.
   - Mencatat aktivitas restrukturisasi pada `brain/laptop-a/Devlog.md` dan checklist `brain/laptop-a/Tasks.md`.
4. **Sinkronisasi Git:**
   - Perubahan di-commit dengan pesan `docs(laptop-a): restrukturisasi folder kategori brain laptop-a dan update overview`, di-rebase terhadap commit baru Laptop B, dan di-push ke remote `origin main`.

---

## 4. Detail Penting yang Harus Diingat

- **Konvensi Penulisan Baru:** Dokumen spesifikasi baru yang dibuat di Laptop A harus langsung ditempatkan di dalam folder kategori yang sesuai (`ui-ux/`, `gameplay-mechanics/`, `cv-control/`, `visual-vfx/`, atau `handoff-laptop-b/`), tidak lagi diletakkan di root `brain/laptop-a/`.
- **Lokasi File Inti yang Tidak Boleh Pindah:**
  - `brain/laptop-a/Tasks.md`
  - `brain/laptop-a/Devlog.md`
  - `brain/laptop-a/konteks.md`
  - `brain/laptop-a/prd.md`
  - `brain/laptop-a/memories/`
- **Aturan Kolaborasi Dual-Device:** Selalu jalankan `git -C brain pull --rebase origin main` sebelum melakukan push untuk menghindari reject akibat commit paralel dari Laptop B.

---

## 5. Tugas Tersisa & Langkah Selanjutnya

- [ ] Pastikan Laptop B merujuk path kategori baru ketika membaca file spesifikasi/prompt dari Laptop A.
- [ ] Lanjutkan penulisan dokumen spesifikasi atau handoff baru langsung di dalam kategori folder yang tepat.
- [ ] Jaga kebersihan root `brain/laptop-a/` pada sesi-sesi pengembangan berikutnya.

---

**Navigasi Obsidian:** [[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[konteks]] · [[prd]]
