---
title: Prompt Eksekusi Revisi Skybox Fantasi Stylized (Laptop B)
type: handoff-prompt
tags:
  - prompt
  - laptop-b
  - skybox
  - shader
  - urp
  - atmosphere
owner: Laptop A
updated: 2026-11-26
status: ready-for-execution
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[visual-vfx/spesifikasi_revisi_skybox_stylized]]"
  - "[[visual-vfx/planning_vfx_fov_skybox_dan_animasi]]"
---

# 🚀 Prompt Eksekusi Revisi Skybox Fantasi *Stylized* untuk Laptop B

> **Petunjuk untuk Pengguna:** Setelah melakukan `git -C brain pull` di **Laptop B**, salin (*copy*) seluruh kotak **"MASTER PROMPT EKSEKUSI UNTUK LAPTOP B"** di bawah dan kirimkan ke AI Agent di Laptop B.

---

## 📋 MASTER PROMPT EKSEKUSI UNTUK LAPTOP B (SIAP SALIN)

```markdown
Halo Laptop B! Setelah evaluasi visual langsung di Unity, skybox saat ini masih terlihat polos seperti skybox bawaan Unity (terutama di area netral & Edit Mode). Sekarang jalankan eksekusi **Revisi Skybox Fantasi Stylized (Ghibli/Genshin Style + Awan Kumulus + Lighting Scene Permanen)** mengacu pada dokumen brain:
- `brain/laptop-a/visual-vfx/spesifikasi_revisi_skybox_stylized.md`

Mohon jalankan langkah-langkah berikut dengan tetap menjaga 100% kompatibilitas `BiomeAtmosphereController.cs` dan `VFXAtmosphereVerificationRunner.cs`:

### 1. Upgrade Shader `Assets/Shaders/DynamicBiomeSkybox.shader` (Unity 6 URP)
- Perbarui `DynamicBiomeSkybox.shader` agar menampilkan langit fantasi *stylized* bergaya Ghibli/Genshin yang jelas berbeda dari `Default-Skybox` Unity:
  1. **Gradasi 4-Stop Kaya Warna:** `_ZenithColor` (puncak azur dalam), `_MidSkyColor` (biru cyan langit tengah), `_HorizonColor` (wajib 100% cocok dengan `RenderSettings.fogColor` agar batas laut >2000m menyatu mulus), dan `_NadirColor` (`#184E66` refleksi laut di bawah horizon).
  2. **Awan Kumulus *Stylized* Berlapis (Domain-Warped Procedural FBM + Cel-Soft Edge):**
     - Proyeksi kubah perspektif (`dir.xz / (max(dir.y, 0.03) + 0.18)`) sehingga awan melebar perspektif ke cakrawala.
     - Pisahkan warna sisi atas terang (`_CloudLitColor`) dan perut bayangan awan (`_CloudShadowColor`) plus kilau tepi matahari (*silver lining*) dan animasi gerak lembut (`_CloudSpeed`).
     - Tambahkan lapisan *cirrus* tipis di langit atas (`y > 0.18`) dan pudarkan halus ke `_HorizonColor` di dekat garis cakrawala (`y <= 0.04`).
  3. **Piringan Matahari & Halo Atmosfer:** Mengikuti `_MainLightPosition.xyz` (dengan fallback arah matahari valid) menggunakan `_SunColor`, `_SunDiscSize`, dan `_SunHaloIntensity`.
  4. Pertahankan alias properti lama yang dipakai di kode/test eksisting (mis. `_SkyTint` jika ada) agar tidak terjadi regresi.

### 2. Pembaruan `Mat_DynamicBiomeSkybox.mat` & `BiomeAtmosphereController.cs`
- Atur nilai default `Assets/Materials/Mat_DynamicBiomeSkybox.mat` ke palet **`NeutralOcean`** (`_ZenithColor=#1C67CC`, `_MidSkyColor=#57B3F2`, `_HorizonColor=#C5EBFA`, `_NadirColor=#184E66`, `_CloudLitColor=#FFFDF5`, `_CloudShadowColor=#8DB6DC`, `_CloudCoverage=0.48`) agar terlihat jelas di Edit Mode maupun Play Mode.
- Perbarui `AtmosphereProfile` dan ke-7 state pada `BiomeAtmosphereController.cs` (`NeutralOcean`, `ZephyriaCrisis`, `ZephyriaRestored`, `PyrewoodCrisis`, `PyrewoodRestored`, `SolariaCrisis`, `SolariaRestored`) sesuai tabel di `spesifikasi_revisi_skybox_stylized.md` sehingga bukan hanya tint langit yang berubah, tetapi juga cakupan awan (`_CloudCoverage`), warna terang/bayangan awan, dan intensitas halo matahari.
- Pastikan `InitRuntimeSkybox()` membuat instance material runtime (`new Material(templateMat)`) dan membersihkannya di `OnDestroy()` agar file `.mat` di disk tidak kotor saat Play Mode.
- Sinkronkan secara opsional `RenderSettings.ambientMode = AmbientMode.Trilight` secara lembut mengikuti profil biome agar pencahayaan naga & pulau menyatu dengan langit.

### 3. Tambahkan Awan 3D Parallax Kepulauaan (`PF_StylizedClouds`)
- Buat prefab/builder ringan awan 3D stylized (`12–16` gugusan awan non-colliding, `shadowCastingMode = Off`, `receiveShadows = false`) di cincin luar kepulauan (`radius 950–1650 m`, `Y = 300–520 m`) melalui `Session4_VillageBuilder.cs` / `IslandArchipelagoSetupUtility.cs` agar muncul kedalaman parallax saat terbang tanpa menghalangi jalur desa atau marker.

### 4. Pasang Permanen di Lighting Settings Scene (`MainIsland_Gameplay.unity` & `Test_Dragon_Flight.unity`)
- Pastikan `RenderSettings.skybox` di scene `MainIsland_Gameplay.unity` (dan opsional `Test_Dragon_Flight.unity`) langsung merujuk ke `Assets/Materials/Mat_DynamicBiomeSkybox.mat` dan tersimpan di file `.unity` (bukan hanya di-assign saat `Awake()`).

### 5. Verifikasi Play Mode & Tangkap Screenshot Perbandingan
- Jalankan ulang `VFXAtmosphereVerificationRunner.RunPlayModeVerificationAsync()` dan `MomentumFlightVerificationRunner` di `MainIsland_Gameplay.unity` (pastikan **100% PASS**, `0` compile errors, `0` console errors/warnings).
- Ambil minimal **4 screenshot visual skybox** ke `docs/figures/raw_screenshots/`:
  1. `SS_Skybox_NeutralOcean.png` (pandangan di atas Center Peak menghadap pulau & langit berawan)
  2. `SS_Skybox_ZephyriaCrisis.png` (kabut smog + langit awan kelabu-hijau)
  3. `SS_Skybox_PyrewoodCrisis.png` (langit asap bara oranye)
  4. `SS_Skybox_SolariaRestored.png` (langit biru keemasan pasca restorasi)
- Kembalikan scene aktif ke `MainMenu_Lobby.unity` (Build Index `0`).
- Perbarui `brain/laptop-b/Devlog.md` dan `brain/laptop-b/Tasks.md`, lalu `commit` & `push` perubahan `brain/`.
```
