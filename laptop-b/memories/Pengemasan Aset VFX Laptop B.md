---
title: "Pengemasan Aset VFX Laptop B"
type: session-memory
tags:
  - session-memory
  - laptop-b
  - vfx
  - effekseer
  - export
  - threejs
date: 2026-11-26
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[laptop-a/visual-vfx/studio_animasi_threejs_fanamir]]"
  - "[[laptop-a/visual-vfx/planning_vfx_fov_skybox_dan_animasi]]"
  - "[[Implementasi Sesi 4B VFX dan Atmosfer]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
---

# Pengemasan Aset VFX Laptop B

## 1. Tujuan & Konteks Sesi
- Mengumpulkan, menyusun, dan mengemas seluruh aset Efek Visual (VFX) aktif dari proyek Unity *Fanamir: The Sky Guardian* ke dalam satu arsip ZIP lokal di `assets/Export_VFX_LaptopB.zip`.
- Berkas arsip ini disiapkan agar pengguna dapat langsung menyalinnya ke **Laptop A** untuk pengujian, evaluasi visual, dan integrasi di **Three.js Studio** (`dragon_anim_studio/`).
- Sesuai instruksi, berkas ZIP **tidak ditambahkan ke git (`git add` / `git push`)** agar ukuran repositori tetap ramping dan efisien.

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Struktur Isi Paket Arsip yang Rapi:**
   - `Effekseer/`: Berisi 7 berkas partikel aktif `.efkefc` dari `Assets/VFX/Effekseer/` beserta subfolder `resources/` berisi 14 berkas tekstur PNG aktif (`T_Flare01`, `T_Glow01`, `T_GradientV01`, `T_NoiseStreak01/02`, `T_NoiseTile01`, `T_Smoke02`, `T_SmokeSheet01`, `T_Spark01`, `T_SpeedStreak01`, `T_Star01`, `T_Trail01/02`, `T_WispTrail01`).
   - `Effekseer_Runtime_efk/`: Berisi 7 berkas partikel biner runtime `.efk` hasil kompilasi dari `vfx/exports/` yang siap digunakan pada runtime web / Three.js.
   - `Python_Generators/`: Berisi 7 skrip resep Python generator (`src/*.py`), resep terkompilasi (`recipes/*.json`), brief spesifikasi desain (`briefs/*.json`), definisi material (`materials/*.vfxmat.json`), manifest VFX (`vfx_manifest.json`), serta modul helper pustaka `vfxkit/`.
   - `Unity_References/`: Berisi skrip pengendali emitter & zona (`DragonVfxController.cs`, `VillageZoneController.cs`, `EffekseerNormalTextureGuard.cs`, `BiomeAtmosphereController.cs`, `Session4_VillageBuilder.cs`), aset serialized Effekseer (`7` `.asset` + `EffekseerSettings.asset`), serta material dan shader terkait (`Mat_ZoneRing_S4.mat`, `Mat_DynamicBiomeSkybox.mat`, `DynamicBiomeSkybox.shader`).
   - `README_VFX.txt`: Berkas dokumentasi tekstual komprehensif yang memuat ringkasan sistem koordinat, hierarki model naga, daftar 7 efek visual, soket/tulang jangkar di Unity, offset runtime, skala emitter, dan pemicu gameplay.
2. **Standardisasi Sistem Koordinat & Parameter Emitter:**
   - Sistem koordinat Unity: Left-Handed, +Y Up, +Z Forward.
   - Pengaturan Effekseer di `EffekseerSettings.asset`: `isRightEffekseerHandledCoordinateSystem = 0` (Left-Handed / Unity), `MaintainGammaColorInLinearSpace = 1`, `enableDistortion = 0`, `enableDepth = 0`.
   - Seluruh emitter menggunakan mode `EmitterScale = Local (0)` dan `playOnStart = false`.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Pembuatan Staging & Kompresi:**
  - Membuat direktori staging sementara `assets/Export_VFX_LaptopB/` dengan subdirektori terorganisir.
  - Menyalin 7 berkas `.efkefc`, 14 tekstur partikel, 7 berkas `.efk`, 7 skrip generator Python, resep/briefs/materials, pustaka `vfxkit`, skrip C#, dan aset serialized Unity.
  - Menyusun berkas panduan `assets/Export_VFX_LaptopB/README_VFX.txt`.
  - Mengompresi seluruh isi staging menjadi berkas arsip `assets/Export_VFX_LaptopB.zip` dengan algoritma kompresi optimal.
  - Menghapus folder staging sementara setelah ZIP berhasil diverifikasi (`TEMP_STAGE_EXISTS: False`).
- **Verifikasi Hasil ZIP:**
  - **Lokasi Persis:** `assets/Export_VFX_LaptopB.zip`
  - **Ukuran Berkas:** **869.790 byte** (**849,4 KB** / **0,829 MB**)
  - **Jumlah Entri:** **97 berkas** (total tidak terkompresi: `1.423.513 byte`)

---

## 4. Detail Penting yang Harus Diingat
- **Hierarki Transform Naga (`/Dragon_Player` di `MainIsland_Gameplay.unity`):**
  - `/Dragon_Player` (`localScale: 1, 1, 1`)
    - `Visual_Dragon` (`localScale: 1, 1, 1`)
      - `Dragon_Model` (`ARM_Dragon`, 47 tulang): `localPosition = (0, -0.7937, 0.6985)`, `localScale = (1.25, 1.25, 1.25)`.
      - `VFX_Sockets` (`localScale: 1, 1, 1`)
- **Daftar 7 Partikel, Soket/Tulang Jangkar, & Skala Emitter:**
  1. `VFX_Flight_WingtipTrails.efkefc` (Wind Status, 0.80s, Looping):
     - Tulang: `leftWingTipBone` = `Wing_Finger_Outer.L`, `rightWingTipBone` = `Wing_Finger_Outer.R`.
     - Offset runtime: `bone.position + bone.up * 1.95f` (`wingtipBoneLength = 1.95f`).
     - Skala emitter: `(0.85, 0.85, 0.85)`.
     - Pemicu: kecepatan $\ge 18\text{ m/s}$ atau belok tajam ($|\text{turn}| \ge 0.45$).
  2. `VFX_Flight_SonicDashShockwave.efkefc` (Wind Hit, 0.70s, One-shot / repeat 1.15s):
     - Soket: Relatif `Visual_Dragon` di `(0, 0.25, -1.6)`.
     - Skala emitter: `(1.0, 1.0, 1.0)`.
     - Pemicu: Manuver `#14 Propeller Dash`.
  3. `VFX_Zephyria_WindGust.efkefc` (Wind Element, 0.70s, One-shot):
     - Soket: Relatif `Visual_Dragon` di `(0, 0.2, 1.1)`.
     - Skala emitter: `(1.4, 1.4, 1.4)`.
     - Pemicu: Kepakan sayap (`#06`/`#15A`) di zona tantangan Zephyria.
  4. `VFX_Lake_WaterSiphon.efkefc` (Water Status, 1.50s, Looping):
     - Soket: Relatif `/Dragon_Player` di `(0, -5.0, 0)`.
     - Skala emitter: `(1.0, 1.0, 1.0)`.
     - Pemicu: Auto-Hover di Sub-Marker Danau Zephyria (Pyrewood Tahap 1).
  5. `VFX_Pyrewood_WaterBreath.efkefc` (Water Element, 1.20s, Looping):
     - Tulang: `mouthBoneOverride` = `Head` (rotasi mengikuti `Visual_Dragon`, artikulasi rahang `Bone_Jaw`).
     - Skala emitter: `(1.0, 1.0, 1.0)`.
     - Pemicu: Pose `#15B Water Breath` di zona Hutan Terbakar.
  6. `VFX_Pyrewood_ExtinguishSteam.efkefc` (Steam Element, 1.60s, One-shot):
     - Soket: World-space anchor `SubMarker_HutanTerbakar` di `(0, 0, 0)`.
     - Skala emitter: `(2.0, 2.0, 2.0)`.
     - Pemicu: Interval 0.85s saat pemadaman api & saat desa pulih 100%.
  7. `VFX_Solaria_CelestialDewRain.efkefc` (Holy Element, 1.75s, Looping / One-shot):
     - Soket: World-space anchor `Challenge_Solaria_SkyAltar` di `(0, 0, 0)`.
     - Skala emitter: `(1.35, 1.35, 1.35)`.
     - Pemicu: Pose `#15C Double I-Pose '11'` di Sky Altar Solaria & saat desa pulih 100%.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- Pengguna dapat menyalin `assets/Export_VFX_LaptopB.zip` ke Laptop A untuk diuji coba di Three.js Studio.
- Melanjutkan tugas Laptop B: **Sesi 5 Lanjutan — Build Windows Standalone & Launcher**.

---

**Navigasi Obsidian:** [[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[Implementasi Sesi 4B VFX dan Atmosfer]] · [[laptop-a/visual-vfx/studio_animasi_threejs_fanamir]]
