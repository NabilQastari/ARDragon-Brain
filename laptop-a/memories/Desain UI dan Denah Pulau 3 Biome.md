---
title: "Perancangan UI Lobby, Kalibrasi, In-Game Flight HUD, Wireframe FigCraft, dan Denah Pulau 3 Biome"
type: session-memory
tags:
  - session-memory
  - laptop-a
  - ui-ux
  - wireframe
  - figcraft
  - island-design
  - prd
date: 2026-11-13
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[konteks]]"
  - "[[prd]]"
  - "[[rencana_desain_ui]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
---

# Perancangan UI Lobby, Kalibrasi, In-Game Flight HUD, Wireframe FigCraft, dan Denah Pulau 3 Biome

## 1. Tujuan & Konteks Sesi
Sesi ini berfokus pada empat agenda utama di sisi desain & arsitektur (Laptop A):
1. Menggabungkan dokumen teknis & PRD terbaru (v4.0 — *Production Ready*) dengan dokumen v3.0 yang ada di workspace (`konteks.md` dan `prd.md`).
2. Menunda sementara **Sesi 4** (Zona 3 Desa & VFX) karena model pulau lama ($800 \times 800\text{ m}$) perlu diremodel, lalu mengalihkan fokus aktif ke **perancangan lengkap antarmuka (GUI)** game *AR Dragon: Sky Guardian*.
3. Merancang dan mengunci konsep **peta denah kepulauan baru** yang lebih luas dengan 3 biome + gunung tengah sebagai tempat *spawn* naga.
4. Menyusun **prompt wireframe FigCraft** untuk seluruh sisa layar UI (di luar lobby utama yang sudah dibuat) tanpa ukuran piksel kaku agar siap ditinjau layoutnya sebelum masuk ke tahap desain visual.

---

## 2. Keputusan Desain / Arsitektur / Teknis

### A. Penggabungan Konteks & PRD (v4.0)
- **Single Source of Truth:** Menyatukan aturan kolaborasi semi-autonomous, prinsip *anti-bug* (1 scene utama, tidak ada aset 3D ganda desa), sistem level & bintang (Level 1: 90s, Level 2: 150s, Level 3: 210s; 3★ = 100%, 2★ ≥ 67%, 1★ ≥ 34%) dengan spesifikasi terbaru Unity 6 (`6000.3.7f1`) URP.
- **Navigasi Terbang 3D (Y-T-A) & Dual-Port UDP:** Mengunci spesifikasi Pose Y (naik $+10\text{ m/s}$), Pose T (mendatar $12\text{ m/s}$), Pose A (menukik $-12\text{ m/s}$ + *Dive Boost* hingga $16.2\text{ m/s}$), Yaw $65^\circ/\text{s}$, Roll $\pm 35^\circ$, proteksi raycast tanah (`safeMinAltitude = Mathf.Max(5f, terrainHeight + 2.5f)`, `maxAltitude = 140f`), UDP `5052` (13-float CSV termasuk `MOUTH_OPEN`), UDP `5053` (JPEG PIP $320 \times 180$ quality 48), dan kontrol keyboard *zero-conflict*.

### B. Arah Visual & Tata Letak UI (*Design Approved*)
- **Gaya Visual Utama:** *Clean Minimalist 2D Vector Cartoon Fantasy* ala Nintendo (*Zelda: Link's Awakening*), menggunakan **panel kertas krem solid (`#FBF6EB`) 100% opaque (tanpa transparansi/glassmorphism)**, bingkai kayu halus, pita *teal* & oranye, tombol *coral-red* (`#E74C3C`) & beige (`#E0D5BE`), serta latar putih polos (`#FFFFFF`) pada lembar aset agar mudah di-*slice* untuk Unity dan terbaca jelas dari jarak webcam $1.5 - 2\text{ m}$.
- **Pemisahan Halaman Menu Utama:**
  - **Tab Calibration** dipindah ke tab terpisah di sebelah kiri navbar agar halaman **Missions** di tengah bersih dari kotak webcam dan fokus penuh pada 3 kartu level (`01 SMOG VALLEY`, `02 WILDFIRE`, `03 DROUGHT`). Tab **Settings** berada di kanan navbar.
  - **Tab Calibration** menampilkan *Dual Preview* berdampingan: kiri (*Player Tracking / Camera Mirror*) dengan status `TRACKING: READY (1.8m)` dan kanan (*Dragon Reaction / Live Dragon Preview*) dengan status `STATUS: CRUISE FLIGHT`, ditambah 3 indikator gestur di bawah (`POSE Y · CLIMB UP`, `POSE T · CRUISE FORWARD`, `POSE A · DIVE BOOST`).
- **In-Game Flight HUD (Compact Space-Saving):**
  - Lebih dari 85% area tengah dan kiri layar dibiarkan lapang.
  - Tengah atas: pill ramping `02:15 · VILLAGES: 1/3` **tanpa aksen pita**.
  - Kanan atas: tombol `ESC · PAUSE`.
  - Sisi kiri: *Global Vitality Progress Bar* vertikal (`33%` dengan ikon daun).
  - Kanan bawah: klaster bertumpuk Telemetri Naga (`42m`, `12.0m/s`, `POSE T`) tepat di atas monitor PIP Webcam (`● CAM READY`).
  - Tengah layar: penunjuk arah halus `▲ WILDFIRE · 140m`.
- **Pendekatan Wireframe untuk Layar Lainnya & FigCraft:**
  - Untuk *Finish Modal*, *Village Action Overlay*, dan *Settings*, disepakati mengunci **wireframe monokrom (tanpa penjelasan ukuran piksel)** terlebih dahulu untuk memvalidasi hierarki layout di FigCraft sebelum diisi desain visual.

### C. Arsitektur Kepulauan Baru (3 Biome + Gunung Pusat)
- Pulau direvisi dari 1 balok persegi $800 \times 800\text{ m}$ menjadi **Kepulauan Kompak 4 Wilayah (*Tight-Knit Caldera Archipelago*)** skala $\pm 1200 \times 1200\text{ m}$ (rasio 1:1) bergaya peta *Genshin Impact* (lautan biru teal dengan garis pantai toska, dominan hijau subur, hanya 4 daratan utama tanpa pulau kecil atau danau kecil pengganggu, dipisahkan selat air sempit):
  - `CENTER`: Gunung Naga Suci berwarna hijau (titik *spawn* naga).
  - `BIOME 1`: Lembah hijau dengan danau-danau besar dan semburat kabut abu-abu (Desa 1 — Polusi Udara / *Smog Valley*).
  - `BIOME 2`: Daratan hijau bertransisi ke semburat bara oranye (Desa 2 — Kebakaran Hutan / *Wildfire Woods*).
  - `BIOME 3`: Daratan hijau bertransisi ke tanah kering kekuningan (Desa 3 — Kekeringan Tanah / *Drought Plateau*).
- **Urutan Roadmap Disepakati:** Selesaikan implementasi/desain UI terlebih dahulu, baru masuk **Sesi Pembaruan Model Pulau** (dimulai dengan gambar referensi per biome).

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset

### Aset Gambar yang Disimpan
1. **Folder baru `Referensi UI/`** (8 file):
   - `Referensi UI/BG_Island_Aerial_View.jpg` (background pulau pov atas awan 16:9 tanpa UI)
   - `Referensi UI/UI_Missions_Screen.jpg` (menu utama / level select 01–03)
   - `Referensi UI/UI_Calibration_Screen.jpg` (halaman kalibrasi + live dragon preview)
   - `Referensi UI/UI_InGame_Flight_HUD.jpg` (HUD penerbangan kompak kanan bawah + bar vertikal kiri)
   - `Referensi UI/UI_Composite_Mockup.jpg` (pratinjau komposit UI di atas pulau)
   - `Referensi UI/UI_Style_Inspiration_Kit.jpg` (lembar inspirasi gaya kertas, kayu, & pita)
   - `Referensi UI/Wireframe_Finish_Modal.png` (wireframe layar kemenangan 3 bintang)
   - `Referensi UI/Wireframe_Village_Action.jpg` (wireframe tantangan gestur auto-hover desa)
2. **Folder baru `assets/upgrade model pulau/`** (1 file):
   - `assets/upgrade model pulau/Peta_Denah_Resmi_Genshin_Style.jpg` (peta denah 1:1 resmi 4 wilayah)

### Dokumen Markdown yang Dibuat / Diperbarui (di Root Workspace)
- **`rencana_desain_ui.md`** (baru dibuat & diperbarui): Katalog seluruh aset di `Referensi UI/`, spesifikasi layout Lobby (`Missions`, `Calibration`, `Settings`), In-Game Flight HUD, Mode Auto-Hover Desa, Finish Modal, serta rencana teknis Unity (*9-sliced sprites*, `RenderTexture` untuk preview naga, `TextMeshPro`).
- **`konteks.md`**: Digabungkan ke v4.0 dan diperbarui pada Bagian 2.3 dengan spesifikasi *Tight-Knit Caldera Archipelago* ($1200 \times 1200\text{ m}$).
- **`prd.md`**: Digabungkan ke v4.0, menambahkan **Sesi GUI** sebagai fokus aktif, menjadwalkan **Sesi Model Pulau** setelah GUI selesai, dan menunda **Sesi 4** hingga model pulau baru selesai.
- **`gdd_peta_pulau.md`** *(catatan: di root workspace, diperbarui ke v2.0 merujuk `Peta_Denah_Resmi_Genshin_Style.jpg`; file duplikat di `brain/laptop-a/` sebelumnya telah dibersihkan)*.

### Pengecekan FigCraft MCP
- Terverifikasi koneksi ke aplikasi FigCraft (`127.0.0.1:9877`) melalui `inspect_document`: dokumen aktif `"Untitled"` di `Page 1` telah memiliki `Frame 16:9 (1920x1080)`, `Victory Modal - Wireframe (1920x1080)`, dan `Title Screen - Wireframe (1920x1080)`.
- Disusun **1 Prompt Tunggal Wireframe FigCraft** (mengecualikan halaman lobby utama yang sudah jadi dan tanpa ukuran piksel) untuk 5 layar: `Calibration & Motion Tutorial`, `In-Game Flight HUD`, `Village Gesture Challenge Overlay`, `Finish UI (Victory & Results Modal)`, dan `Settings & Audio Modal`.

---

## 4. Detail Penting yang Harus Diingat
- **Preferensi UI User:** Hindari efek transparan/glassmorphism, hindari tanda kurung siku literal pada tombol hasil akhir UI, jaga kontras tinggi solid, dan pertahankan >85% ruang terbuka pada *In-Game Flight HUD*.
- **Preferensi Wireframe:** Saat membuat prompt wireframe untuk FigCraft, jangan mencantumkan angka ukuran piksel (seperti `500px`, `680px`) dan jangan memasukkan ulang halaman lobby utama yang sudah selesai.
- **Aturan Peta Pulau Baru:** Rasio peta **1:1**, dominan hijau alami (bukan warna kartun mencolok terpisah), gunung tengah berwarna hijau, hanya 4 daratan utama (*ZERO* pulau kecil satelit & *ZERO* pulau di dalam danau), serta hanya 4 label (`CENTER`, `BIOME 1`, `BIOME 2`, `BIOME 3`).
- **Prompt Gemini Carbon:** Gunakan bahasa deskriptif alami tanpa flag Midjourney (`--ar`, `--v`, `--iw`), dan lampirkan gambar referensi langsung saat ingin mengkloning gaya.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] **Laptop A (UI Visual di FigCraft):** Menjalankan prompt wireframe tunggal untuk sisa 5 layar di FigCraft, lalu menyusun prompt desain visual/warna berdasarkan gambar referensi yang akan dikirim user.
- [ ] **Laptop A (Setelah UI Selesai — Sesi Model Pulau):** Membuat prompt dan mengumpulkan gambar referensi 3D per biome (`CENTER` Dragon Peak, `BIOME 1` Smog Valley, `BIOME 2` Wildfire Woods, `BIOME 3` Drought Plateau) mengacu pada `Peta_Denah_Resmi_Genshin_Style.jpg`.
- [ ] **Laptop A (GDD):** Menyusun ulang GDD peta pulau versi baru ($1200 \times 1200\text{ m}$) beserta koordinat pad desa terbaru di `brain/laptop-a/` untuk kebutuhan Sesi 4.
- [ ] **Laptop B:** Mengimplementasikan Canvas UI di Unity (`Panel_Missions`, `Panel_Calibration` + `RenderTexture` naga, `Panel_Settings`, `InGameFlightHUD`, `VillageActionOverlay`, `Victory/Defeat Modal`) sesuai [[rencana_desain_ui]].

**Navigasi Obsidian:** [[00 - Overview]] · [[konteks]] · [[prd]] · [[rencana_desain_ui]] · [[Tasks]] · [[Devlog]]
