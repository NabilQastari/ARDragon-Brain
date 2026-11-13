---
title: "Product Requirements Document (PRD v4.0)"
tags:
  - prd
  - roadmap
  - laptop-a
  - design
status: active
owner: Laptop A
updated: 2026-11-13
related:
  - "[[00 - Overview]]"
  - "[[konteks]]"
  - "[[rencana_desain_ui]]"
  - "[[gdd_peta_pulau]]"
---

# 📋 PRODUCT REQUIREMENTS DOCUMENT (PRD) & DEVELOPMENT PLAYBOOK: AR DRAGON SKY GUARDIAN (v4.0 — PRODUCTION READY)

> **Catatan Penamaan:** Project Unity = `AR Dragon Sky Guardian`, folder Python = `ARDragonSkyGuardian_CV`. Namespace C# = `DragonWingAR.*`.
> **Mod Kerja:** Kolaborasi Manusia (User/Developer) + AI Agent (Hermes / Antigravity Agent) — Semi-Autonomous Human-in-the-Loop.
> **Rujukan Utama:** Dokumen teknis, ukuran fisik, dan rumus matematika merujuk pada [[konteks]].

---

## 🚨 PERATURAN WAJIB KOLABORASI SEMI-AUTONOMOUS

Sebagai AI Agent yang bekerja sama dengan Pengguna (Manusia), AI Agent **WAJIB** mematuhi 4 pilar aturan:

1. **SATU SESI SETIAP GILIRAN (STRICT ITERATIVE PROGRESSION):**
   Proyek dibagi menjadi Tahap 0 dan 5 Sesi Pembangunan. Agent **DILARANG** melompati sesi atau mengerjakan semua sesi sekaligus. Saat ini Sesi 1–3 telah **SELESAI**, fokus aktif berada pada **SESI 4**.
2. **PEMISAHAN TUGAS LOGIKA vs VISUAL / INSPECTOR:**
   - **Tugas AI Agent:** Menulis semua kode Python CV, C# Unity, algoritma matematika gestur, optimasi performa 0 compiler errors, serta script automasi Unity Editor `[MenuItem("DragonWingAR/...")]`.
   - **Tugas Pengguna (Manusia):** Menjalankan pengujian kamera fisik langsung, validasi rasa kendali (game feel), dan menyetujui transisi antar sesi.
3. **PANDUAN LANGKAH DEMI LANGKAH LENGKAP:**
   Setiap instruksi eksekusi wajib disertai tutorial klik demi klik yang jelas (nama menu, posisi hierarchy, parameter inspector, perintah terminal).
4. **BERHENTI & VALIDASI DI SETIAP AKHIR SESI:**
   Di akhir setiap sesi, Agent wajib menyajikan ringkasan hasil kerja dan mengajukan pertanyaan verifikasi dari bagian 🛑 **[PERTANYAAN WAJIB AKHIR SESI]** sebelum melangkah ke sesi berikutnya.

---

## 🎯 1. VISI & TUJUAN PRODUK

AR Dragon Sky Guardian adalah game *PC exergaming* 3D berbasis input Computer Vision (kamera webcam standar) yang memadukan kebebasan simulasi penerbangan naga di atas pulau semi-terbuka (*semi-open-world*) $800 \times 800\text{ m}$ dengan misi penyelamatan lingkungan hidup.

### Sasaran Utama:
1. **Zero-Equipment Barrier:** Pemain hanya membutuhkan laptop/PC berkamera standar tanpa sensor mahal (Kinect/VR).
2. **Kontrol Tubuh Alami (Intuitive Body Flight):**
   - Mengemudikan arah naga melalui kemiringan bahu dan tangan (Yaw Turn & Roll Banking).
   - Mengendalikan elevasi 3D penuh dengan pose alfabetik: **Pose Y (Naik $\uparrow$)**, **Pose T (Lurus Mendatar $\rightarrow$)**, dan **Pose A (Menukik Turun $\downarrow$ + Dive Boost)**.
   - Melakukan aksi pemulihan desa (mengibas kabut asap, menyemburkan air purba, memanggil hujan embun) melalui gestur tubuh interaktif dan bukaan mulut.
3. **Performa Tinggi & Bebas Lag:**
   - Inferensi AI YOLOv8n-pose $\le 15\text{ ms}$ pada CUDA GPU.
   - Komunikasi Dual-Port UDP lokal (5052 Telemetri, 5053 PIP Video) tanpa jeda input.
   - Visual pulau dan naga stabil 60 FPS di Unity 6 URP.

---

## 🗺️ 2. STATUS ROADMAP PENGEMBANGAN

| Tahap / Sesi | Fokus Utama | Status | Hasil Utama & Verifikasi |
| :--- | :--- | :---: | :--- |
| **Tahap 0** | Setup Environment Unity 6 & Python CUDA | ✅ **SELESAI** | Unity 6 URP, venv Python 3.10+, PyTorch CUDA, OpenCV 4.x. |
| **Sesi 1** | Model CV & Streaming Dual UDP | ✅ **SELESAI** | YOLOv8n-pose terpilih, One-Euro filter, head-crop mouth tracker, Dual UDP Sender (5052 & 5053). |
| **Sesi 2** | Integrasi UDP & Navigasi 3D (Y-T-A) | ✅ **SELESAI** | `UDPPoseReceiver.cs`, `DragonFlightController.cs`, Pose Y-T-A, dive boost 16.2 m/s, ground raycast protection, keyboard zero-conflict fallback. |
| **Sesi 3** | Integrasi Aset 3D Pulau, Naga, & HUD | ✅ **SELESAI** | `Dragon_Model.blend` (Skala 5m, URP Lit double-sided), `pulau.blend` (800x800m), InGameFlightHUD (telemetri + PIP webcam 5053). |
| **Sesi GUI** | Sistem GUI & Menu Lobby (Missions, Kalibrasi, Finish) | ⏳ **FOKUS AKTIF** | Desain terkunci di `Referensi UI/` & [[rencana_desain_ui]]. Implementasi Menu Lobby (Missions 1-3), Tab Kalibrasi (Live Dragon Preview), Flight HUD, dan Finish Modal. |
| **Sesi Model Pulau** | Pembaruan Model Kepulauan 3 Biome (Blender) | ⏳ **SELANJUTNYA** | Dimulai setelah GUI selesai. Rujukan resmi: `assets/upgrade model pulau/Peta_Denah_Resmi_Genshin_Style.jpg`. Tahap 1: Konsep visual per biome, Tahap 2: Modeling 3D Blender. |
| **Sesi 4** | Zona 3 Desa, Mekanik Misi, & VFX | ⏳ **MENYUSUL** | Memasang trigger pad, auto-hover, sistem partikel (asap, api, embun) pada kepulauan 3 biome yang baru. |
| **Sesi 5** | Game Manager, Sistem Bintang, & Build PC | ⏳ **TERAKHIR** | Alur Level 1–3, timer mundur, evaluasi bintang, audio/SFX, dan standalone build Windows executable (`.exe`). |

---

## 🛠️ 3. SPESIFIKASI SESI IMPLEMENTASI

---

### 🔬 SESI 1: KOMPUTASI VISION & UDP SENDER (SELESAI ✅)
- **Modul Python:** `ARDragonSkyGuardian_CV/active_cv_sender.py`
  - YOLOv8n-pose dengan primary target locking (bounding box terbesar).
  - Dynamic arm length clamping & One-Euro filter untuk mencegah jitter tangan.
  - Head-cropped mouth openness detection untuk mendeteksi semburan air purba.
  - Dual UDP streaming: port `5052` (13-float CSV pose telemetri) dan port `5053` (JPEG frame 320x180 quality 48 untuk preview PIP Unity).

---

### 🕹️ SESI 2: NAVIGASI PENERBANGAN 3D & SISTEM POSE Y-T-A (SELESAI ✅)
- **Komponen Unity:**
  - `UDPPoseReceiver.cs`: Mendengarkan UDP `5052`, mem-parsing data CSV secara thread-safe, menghitung pose Y-T-A, serta menyediakan fallback keyboard zero-conflict yang tidak menimpa data kamera.
  - `DragonFlightController.cs`:
    - **Auto-Forward:** Kecepatan jelajah $12\text{ m/s}$ dengan akselerasi menukik (*Dive Boost*) hingga $16.2\text{ m/s}$.
    - **Sistem Elevasi Y-T-A:**
      - **Pose Y:** Naik menanjak ($+10\text{ m/s}$, visual pitch mendongak $-20^\circ$).
      - **Pose T:** Meluncur mendatar ($0\text{ m/s}$ vertikal, mempertahankan ketinggian saat ini).
      - **Pose A:** Menukik turun ($-12\text{ m/s}$, visual pitch menunduk tajam $+20^\circ$).
    - **Kemudi Belok:** Putaran Yaw ($65^\circ/\text{s}$) dan Roll banking visual ($-35^\circ$ s/d $+35^\circ$).
    - **Ground Raycast Clearance:** Sensor raycast vertikal menjaga naga minimal $2.5\text{ m}$ di atas permukaan tanah bukit pulau (bebas tembus tanah, batas atas plafon 140m).

---

### 🐉 SESI 3: INTEGRASI ASET 3D PULAU, NAGA, & HUD (SELESAI ✅)
- **Aset 3D:**
  - **Pulau (`pulau.blend` / `pulau.fbx`):** Ukuran $813\text{ m} \times 800\text{ m}$ berkarakter *grounded cutaway slab* dengan terrain, bebatuan bukit (puncak tertinggi 118.9m), dan 3 danau organik.
  - **Naga (`Dragon_Model.blend` / `Dragon_Model.fbx`):** Dimensi bentang sayap $5.06\text{ m}$, panjang $3.60\text{ m}$, tinggi $1.59\text{ m}$. Shading menggunakan 12 material URP Lit double-sided rendering (`_Cull = 0`).
  - **Kamera Follow:** Posisi `(0, 2.5, -6.5)`, sudut pitch $14^\circ$, FOV $60^\circ$.
- **In-Game Flight HUD (`InGameFlightHUD.cs`):**
  - **Kiri Atas:** Telemetri penerbangan naga (Kecepatan, Ketinggian, Status Pose Y/T/A, Slider Kemudi, Indikator Aksi Gestur).
  - **Kanan Atas:** Kotak Picture-in-Picture (PIP) menampilkan stream kamera langsung dari port 5053, dengan fallback radar kerangka tubuh interaktif.
  - **Bawah Layar:** Panduan kontrol singkat yang jelas.

---

### 🎨 SESI GUI: SISTEM ANTARMUKA PENGGUNA & MENU LOBBY (FOKUS AKTIF ⏳)

> **Dokumen Rujukan Lengkap:** [[rencana_desain_ui]]  
> **Folder Aset Visual:** [`Referensi UI/`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/)

**Tujuan Sesi:** Mengimplementasikan antarmuka grafis (GUI) lengkap sesuai konsep *Clean Minimalist 2D Vector Cartoon Fantasy* yang telah disetujui:
1. **Lobby & Level Selection Screen (`UI_Missions_Screen.jpg`):**
   - Background aerial island panorama (`BG_Island_Aerial_View.jpg`).
   - Header terpusat dengan 3 segmented capsule tabs: `[ CALIBRATION ]`, `[ MISSIONS (Active) ]`, `[ SETTINGS ]`.
   - 3 Kartu Level (Smog Valley 90s, Wildfire 150s, Drought Plateau Locked).
2. **Tab Kalibrasi & Live Dragon Preview (`UI_Calibration_Screen.jpg`):**
   - Dual-Panel Preview: Panel kiri untuk webcam mirror & pose skeleton, panel kanan untuk live response model naga 3D (`Dragon_Model.blend`).
   - Indikator deteksi pose real-time: `POSE Y (Climb Up)`, `POSE T (Cruise Forward)`, `POSE A (Dive Boost)`.
3. **Finish & In-Game UI Modules:**
   - Victory Modal (3 Bintang ⭐⭐⭐ & ringkasan misi).
   - Defeat Modal (Waktu habis / coba lagi).
   - Penyesuaian HUD dan navigasi kompas desa.

---

### 🏝️ SESI MODEL PULAU: PEMBARUAN KEPULAUAN 3 BIOME (LANGKAH SELANJUTNYA ⏳)

> **Status Alur Kerja:** Dilaksanakan segera setelah Sesi GUI selesai.  
> **Cetak Biru Resmi:** [`assets/upgrade model pulau/Peta_Denah_Resmi_Genshin_Style.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/Peta_Denah_Resmi_Genshin_Style.jpg)

**Tahapan Eksekusi:**
1. **Tahap 1 (Visual Reference Per Biome):**
   - Merender gambar referensi lanskap 3D untuk masing-masing biome:
     - `CENTER`: Gunung Naga Hijau (Dragon Spawn Peak).
     - `BIOME 1`: Lembah Danau & Perbukitan (Smog Valley).
     - `BIOME 2`: Hutan Pinus Musim Gugur (Wildfire Woods).
     - `BIOME 3`: Dataran Tinggi Batu Kering (Drought Plateau).
2. **Tahap 2 (3D Modeling & Sculpting di Blender):**
   - Membuat medan 3D kepulauan skala $1200\text{ m} \times 1200\text{ m}$ mengikuti kontur denah resmi.
   - Pematangan selat air laut sempit antar-pulau.
   - Penempatan pad desa dan collider terrain.
3. **Tahap 3 (Ekspor & Setup Unity URP):**
   - Ekspor `.fbx` ke Unity 6 dengan material URP Lit dan MeshCollider.

---

### 🏡 SESI 4: INTERAKSI ZONA 3 DESA, MEKANIK TANTANGAN, & VFX (MENYUSUL ⏳)

**Tujuan Sesi:** Memasang *gameplay loop* pemulihan 3 desa di lokasi pad pulau resmi menggunakan gestur interaktif dan sistem efek visual (VFX).

#### A. Penempatan Zona Desa di Pad Marker Pulau:
1. **Desa 1 (Polusi Asap):**
   - Posisi Pad: `Pad_Desa1_Polusi` di `(-200.00, 0.13, 80.00)`
   - Trigger: `SphereCollider` (Radius $28\text{ m}$, `isTrigger = true`).
   - Masalah: Asap hitam pekat menyelimuti desa.
   - Partikel: `VFX_BlackSmoke` (*Particle System* laju emisi 50 $\rightarrow$ berkurang linier ke 0 seiring progres).
   - Gestur Penyelesaian: **Mengepakkan Sayap** (gerakan tangan naik-turun melewati bahu secara berulang, `IsFlapping`).
2. **Desa 2 (Kebakaran Hutan):**
   - Posisi Pad: `Pad_Desa2_HutanKebakaran` di `(10.00, 0.23, -133.00)`
   - Trigger: `SphereCollider` (Radius $32\text{ m}$, `isTrigger = true`).
   - Masalah: Pepohonan dan lingkungan desa terbakar kobaran api.
   - Partikel: `VFX_Fire` (Skala transform api menyusut dari 1.0 ke 0.0) + `VFX_WaterBreath` (Semburan partikel air purba dari mulut naga).
   - Gestur Penyelesaian: **Membuka Mulut Lebar** (`MouthOpen > 0.45`) atau menangkupkan kedua tangan di depan dada (`Distance(LW, RW) < 0.14f`).
3. **Desa 3 (Kekeringan Tanah):**
   - Posisi Pad: `Pad_Desa3_Kekeringan` di `(260.00, 0.82, 13.00)`
   - Trigger: `SphereCollider` (Radius $30\text{ m}$, `isTrigger = true`).
   - Masalah: Tanah retak tandus dan pepohonan kering kecoklatan.
   - Partikel: `VFX_DewRain` (Hujan embun berkilau dari langit) + perubahan warna material tanah/pohon dari coklat (`#8B5A2B`) ke hijau subur (`#2E8B57`) via `Color.Lerp`.
   - Gestur Penyelesaian: **Mengangkat Kedua Tangan Tinggi** di atas kepala (`IsHandsHighUp`: `LW.y < LS.y - 0.14f && RW.y < RS.y - 0.14f`).

#### B. Mekanik Auto-Hovering:
- Saat naga masuk ke zona desa yang belum pulih (`localProgress < 100%`), naga secara otomatis berhenti maju dan mengambang halus (*hover bobbing* $\pm 0.4\text{ m}$) sambil memutar badan menghadap ke pusat desa.
- Kecepatan pemulihan: $+20\%$ per detik aksi sah (5 detik aksi konsisten = 100% pulih).
- Begitu mencapai 100%, status Auto-Hover dilepaskan dan naga kembali dapat terbang bebas menuju desa berikutnya.

#### C. Skrip Pembantu Editor:
- Sediakan script `Assets/Editor/Session4_VillageBuilder.cs` dengan menu `[MenuItem("DragonWingAR/Sesi 4 - Setup Zona 3 Desa & VFX")]` untuk otomatis menyusun Trigger Collider, komponen `VillageZoneController.cs`, dan partikel pada ketiga Pad desa.

#### 🛑 [PERTANYAAN WAJIB AKHIR SESI 4]
> "Komponen zona 3 desa, logika auto-hover, sistem gestur pemulihan, dan penyesuaian VFX telah terpasang.
> 1. Apakah naga otomatis berhenti (Auto-Hover) saat memasuki zona pad desa?
> 2. Apakah aksi gestur (kepak sayap, buka mulut/tangan di dada, dan tangan tinggi) berhasil menaikkan progres desa hingga 100%?
> 3. Apakah efek visual (asap reda, api padam, dan hujan embun hijau) berubah sesuai progres?
> 4. Apakah Sesi 4 sudah selesai dan kita siap masuk ke Sesi 5 (Game Manager, Level, Sound, & Final Build)?"

---

### 🏆 SESI 5: GAME MANAGER, SISTEM LEVEL, & FINAL POLISH (TAHAP TERAKHIR ⏳)

**Tujuan Sesi:** Menyelesaikan alur permainan menyeluruh dari menu lobby hingga evaluasi kemenangan dan ekspor game mandiri (*standalone executable*).

1. **Struktur Level & Waktu:**
   - **Level 1 (Pemula):** Hanya Desa 1 aktif. Batas waktu: 90 detik.
   - **Level 2 (Menengah):** Desa 1 + Desa 2 aktif. Batas waktu: 150 detik.
   - **Level 3 (Ahli):** Ketiga Desa aktif. Batas waktu: 210 detik.
2. **Sistem Penilaian Bintang:**
   - Selesai sebelum batas waktu & `GlobalProgress >= 100%` $\rightarrow$ **3 Bintang** ⭐⭐⭐
   - Waktu habis & `GlobalProgress >= 67%` $\rightarrow$ **2 Bintang** ⭐⭐
   - Waktu habis & `GlobalProgress >= 34%` $\rightarrow$ **1 Bintang** ⭐
   - Progres $< 34\%$ $\rightarrow$ **Gagal / Mengulang**
3. **Audio & Sound Effects:**
   - BGM penerbangan orkestra fantasi bertempo dinamis.
   - SFX kepakan sayap naga saat pose Y menanjak.
   - SFX angin mendesing tajam saat pose A menukik (*dive boost*).
   - SFX semburan air purba dan lonceng kemenangan desa pulih.
4. **Final Standalone Build:**
   - Ekspor standalone Windows executable (`AR_Dragon_Sky_Guardian.exe`).
   - Skrip *one-click batch launcher* (`start_game.bat`) yang otomatis menyalakan background Python sender dan game Unity secara bersamaan.

#### 🛑 [PERTANYAAN WAJIB AKHIR SESI 5]
> "Seluruh sistem Game Manager, alur Level 1–3, audio, dan standalone executable telah selesai dibangun.
> 1. Apakah timer hitung mundur dan akumulasi bintang berfungsi dengan benar di ketiga level?
> 2. Apakah file start_game.bat dapat menyalakan Python sender dan Unity game secara instan dengan satu klik?
> 3. Apakah seluruh build game siap untuk presentasi dan pengujian akhir?"

---

## 🎮 4. PANDUAN PENGUJIAN CEPAT (TESTING PLAYBOOK)

### Cara Menjalankan Sesi Pengujian:

1. **Menjalankan Game di Unity:**
   - Buka project di Unity 6 (`6000.3.7f1`).
   - Buka scene: `Assets/_Scenes/Test_Dragon_Flight.unity`.
   - Tekan tombol **Play (▶️)**.
2. **Menjalankan Pengirim Pose Python:**
   - Buka terminal PowerShell di folder `ARDragonSkyGuardian_CV`:
     ```powershell
     cd ARDragonSkyGuardian_CV
     venv\Scripts\activate
     python active_cv_sender.py
     ```
   - Berdiri atau duduk di depan webcam dengan pencahayaan yang cukup.
3. **Uji Coba Kontrol Penerbangan 3D:**
   - Angkat kedua tangan ke atas membentuk **Y** $\rightarrow$ Naga menanjak naik $\uparrow$ ($+10\text{ m/s}$).
   - Rentangkan kedua tangan mendatar membentuk **T** $\rightarrow$ Naga meluncur lurus mendatar $\rightarrow$ ($12\text{ m/s}$, ketinggian stabil).
   - Arahkan kedua tangan serong ke bawah membentuk **A** $\rightarrow$ Naga menukik tajam $\downarrow$ dengan kecepatan meningkat (*Dive Boost* $16.2\text{ m/s}$).
   - Miringkan salah satu tangan lebih tinggi dari yang lain $\rightarrow$ Naga berbelok kiri/kanan dengan kemiringan tubuh visual (*banking roll* $-35^\circ$ s/d $+35^\circ$).
4. **Uji Coba Keyboard (Tanpa Kamera — Zero-Conflict):**
   - Tekan `W` atau `Y` atau `Spasi` $\rightarrow$ Naik (Pose Y).
   - Tekan `S` atau `A` atau `Panah Bawah` $\rightarrow$ Turun (Pose A).
   - Tekan `T` atau lepas tombol $\rightarrow$ Lurus mendatar (Pose T).
   - Tekan `Panah Kiri / Kanan` atau `D` $\rightarrow$ Kemudi Belok.
   - Tekan `1` $\rightarrow$ Simulasi kepakan sayap (Desa 1).
   - Tekan `2` atau `F` $\rightarrow$ Simulasi semburan air (Desa 2).
   - Tekan `3` atau `R` $\rightarrow$ Simulasi panggil hujan embun (Desa 3).


---
**Navigasi Obsidian:** [[00 - Overview]] · [[konteks]] · [[rencana_desain_ui]] · [[gdd_peta_pulau]] · [[Tasks]] · [[Devlog]]
