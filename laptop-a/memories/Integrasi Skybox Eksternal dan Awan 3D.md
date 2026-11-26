---
title: Integrasi Skybox Eksternal dan Awan 3D
type: session-memory
tags:
  - session-memory
  - laptop-a
  - skybox
  - clouds
  - urp
  - visual-vfx
date: 2026-11-26
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[visual-vfx/spesifikasi_revisi_skybox_stylized]]"
  - "[[visual-vfx/planning_vfx_fov_skybox_dan_animasi]]"
  - "[[handoff-laptop-b/prompt_eksekusi_integrasi_skybox_dan_awan_eksternal_laptop_b]]"
  - "[[handoff-laptop-b/prompt_eksekusi_revisi_skybox_laptop_b]]"
---

# 🌤️ Integrasi Skybox Eksternal dan Awan 3D

## 1. Tujuan & Konteks Sesi
- Pengguna mengevaluasi bahwa tampilan skybox saat ini masih terlihat polos menyerupai `Default-Skybox` bawaan Unity (karena shader awal `DynamicBiomeSkybox.shader` baru berupa gradasi warna prosedural polos dengan transparansi awan tipis dan baru diaktifkan saat Play Mode).
- Menemukan solusi visual terbaik: merancang spesifikasi skybox fantasi *stylized* (Cara 1 - Prosedural), lalu mengadopsi preferensi pengguna untuk menggunakan **aset eksternal yang sudah ada** (AllSky Free, Fantasy Skybox FREE, dan model awan 3D Quaternius).
- Menyusun arsitektur dan Master Prompt integrasi aset luar tersebut ke `BiomeAtmosphereController.cs`, `Lighting Settings`, dan scene `MainIsland_Gameplay.unity` di Laptop B agar tidak tertimpa shader polos lama.

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Diagnosis Tampilan Skybox Bawaan:**
   - Shader lama menggunakan `_CloudCoverage = 0.35` dan `_CloudTint.a = 0.35–0.42` tanpa tekstur awan atau noise kontras, sehingga tampak seperti gradasi polos.
   - Pemasangan material skybox lama baru terjadi saat runtime via `BiomeAtmosphereController.InitRuntimeSkybox()` di Play Mode, sehingga di Edit Mode langit tetap menampilkan skybox default.
2. **Pemilihan & Kurasi Aset Eksternal:**
   - **Skybox:** Pengguna menginstal paket gratis Unity Asset Store:
     - **AllSky Free - 10 Sky / Skybox Set** (by *rpgwhitelock*)
     - **Fantasy Skybox FREE** (by *Render Knight*)
   - **Model Awan 3D:** Menggunakan **Quaternius Modular Platformer Pack** (lisensi CC0) berisi mesh awan *low-poly* (`.fbx` / `.obj` / `.blend`).
3. **Arsitektur Integrasi di Laptop B:**
   - **Non-Destructive Runtime Instantiation:** `BiomeAtmosphereController.InitRuntimeSkybox()` dimodifikasi agar **TIDAK** menimpa ulang dengan shader lama, melainkan membuat instance runtime (`new Material(externalTemplate)`) dari preset skybox eksternal pilihan (`NeutralOcean`).
   - **Modulasi Atmosfer 7 State:** Tetap memodulasi properti warna/tint (`_Tint`/`_TintColor`), `_Exposure`, serta `RenderSettings.fogColor` dan `RenderSettings.fogEndDistance` untuk transisi krisis 3 desa (`ZephyriaCrisis`, `PyrewoodCrisis`, `SolariaCrisis`) dan restorasi `2.75s`.
   - **Penyatuan Horizon:** Warna kabut URP (`RenderSettings.fogColor`) diselaraskan dengan kaki cakrawala skybox eksternal agar batas laut jauh (>2000m) menyatu tanpa patahan.
   - **Prefab Awan 3D (`PF_StylizedClouds`):** Mengonversi material awan Quaternius ke `URP/Lit` matte putih-krem (`#FFFDF8`, `Smoothness = 0.08`), diinstansiasi 12–18 gugusan di cincin luar kepulauan (`radius 850–1650 m`, `Y = 260–480 m`, non-colliding, no shadow casting) untuk kedalaman parallax.
   - **Pemasangan Permanen:** Material skybox eksternal diset langsung pada `RenderSettings.skybox` di scene agar terlihat langsung di Edit Mode.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Penyusunan Spesifikasi Teknis & Shader:**
  - Membuat `brain/laptop-a/visual-vfx/spesifikasi_revisi_skybox_stylized.md` (rancangan 4-stop gradient, FBM cumulus, silver lining, dan matriks 7 state).
  - Membuat `brain/laptop-a/handoff-laptop-b/prompt_eksekusi_revisi_skybox_laptop_b.md` (prompt alternatif prosedural).
- **Penyusunan Master Prompt Integrasi Aset Eksternal:**
  - Membuat `brain/laptop-a/handoff-laptop-b/prompt_eksekusi_integrasi_skybox_dan_awan_eksternal_laptop_b.md` berisi 5 langkah eksekusi audit preset AllSky/Fantasy Skybox, modifikasi `BiomeAtmosphereController.cs`, perakitan `PF_StylizedClouds` awan Quaternius, dan verifikasi non-regresi Play Mode.
- **Pembaruan Navigasi & Pelacakan:**
  - Memperbarui `brain/00 - Overview.md`, `brain/laptop-a/Tasks.md`, dan `brain/laptop-a/Devlog.md`.
  - Berhasil di-commit dan di-push ke sub-repo `brain/` (`origin main`).

---

## 4. Detail Penting yang Harus Diingat
- **Paket Asset Store di Laptop B:** *AllSky Free* dan *Fantasy Skybox FREE* di-download dan di-import melalui Unity **Package Manager $\to$ My Assets** langsung di Unity Editor Laptop B.
- **Model Awan Quaternius:** File `.fbx` awan dari file ZIP Quaternius perlu di-drag/dimasukkan ke folder `Assets/Assets 3D/Clouds/` di Laptop B.
- **Resistensi Terhadap Overwrite:** Pastikan script builder (`Session4_VillageBuilder.cs` / `IslandArchipelagoSetupUtility.cs`) tidak secara tidak sengaja me-reset skybox material kembali ke shader lama saat dieksekusi ulang.
- **Assertion Test Runner:** `VFXAtmosphereVerificationRunner.cs` perlu disesuaikan jika sebelumnya ada pengecekan kaku terhadap string nama shader `DynamicBiomeSkybox`.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
1. **Di Laptop B:**
   - Jalankan `git -C brain pull`.
   - Pastikan paket *AllSky Free*, *Fantasy Skybox FREE*, dan `.fbx` awan Quaternius sudah ter-import di `Assets/`.
   - Jalankan Master Prompt dari `brain/laptop-a/handoff-laptop-b/prompt_eksekusi_integrasi_skybox_dan_awan_eksternal_laptop_b.md`.
   - Verifikasi Play Mode (`0` error/warning) dan simpan 4 tangkapan layar visual baru ke `docs/figures/raw_screenshots/`.
2. **Di Laptop A:**
   - Melanjutkan perencanaan Pilar 2 (Animasi & Juice UI) dan Pilar 3 (Kontrol & Ergonomi Exergaming).
   - Menyelesaikan draf Bab 4.6–4.8 dan Bab 5 Laporan Capstone.

---
*Navigasi Obsidian:* [[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[visual-vfx/spesifikasi_revisi_skybox_stylized]] · [[handoff-laptop-b/prompt_eksekusi_integrasi_skybox_dan_awan_eksternal_laptop_b]]
