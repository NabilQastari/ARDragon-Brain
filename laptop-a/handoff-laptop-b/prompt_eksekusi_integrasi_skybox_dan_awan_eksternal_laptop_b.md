---
title: Prompt Eksekusi Integrasi Skybox Eksternal & Awan 3D Quaternius (Laptop B)
type: handoff-prompt
tags:
  - prompt
  - laptop-b
  - skybox
  - clouds
  - urp
  - external-assets
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

# 🚀 Prompt Eksekusi Integrasi Skybox Eksternal (*AllSky Free* / *Fantasy Skybox FREE*) & Awan 3D (*Quaternius*) di Laptop B

> **Konteks:** Pengguna telah meng-install/memasukkan aset eksternal berikut ke **Laptop B**:
> 1. **AllSky Free - 10 Sky / Skybox Set** (*Unity Asset Store*)
> 2. **Fantasy Skybox FREE** (*Unity Asset Store*)
> 3. **Quaternius - Modular Platformer Pack** (*berisi model awan 3D low-poly*)
>
> **Petunjuk untuk Pengguna:** Di Laptop B, jalankan `git -C brain pull`, lalu salin seluruh isi kotak **"MASTER PROMPT EKSEKUSI UNTUK LAPTOP B"** di bawah dan kirimkan ke AI Agent di Laptop B.

---

## 📋 MASTER PROMPT EKSEKUSI UNTUK LAPTOP B (SIAP SALIN)

```markdown
Halo Laptop B! Saya sudah mengimpor/memasukkan aset eksternal ke proyek Unity di Laptop B:
1. **AllSky Free - 10 Sky / Skybox Set**
2. **Fantasy Skybox FREE**
3. **Quaternius Modular Platformer Pack** (untuk model 3D awan)

Tolong integrasikan aset eksternal tersebut ke scene gameplay (`MainIsland_Gameplay.unity` dan opsional `Test_Dragon_Flight.unity`) menggantikan tampilan shader polos lama agar langit berawan terlihat nyata baik di **Edit Mode** maupun **Play Mode**, dengan langkah-langkah berikut:

### 1. Audit Aset Skybox & Awan yang Sudah Masuk di `Assets/`
- Scan folder `Assets/` untuk menemukan lokasi aset hasil import dari **AllSky Free**, **Fantasy Skybox FREE**, dan **Quaternius Modular Platformer Pack**.
- Periksa seluruh preset material skybox yang tersedia di *AllSky Free* dan *Fantasy Skybox FREE* (termasuk shader dan propertinya: `_Tint`, `_Exposure`, `_Rotation`, atau properti cubemap/panoramic), lalu pilih preset terbaik yang **berawan cerah fantasi** untuk **`NeutralOcean`** (mis. langit biru cerah berawan tebal gaya anime/fantasi; laporkan preset yang dipilih di Devlog).
- Jika di dalam kedua paket tersebut ada preset yang pas untuk kondisi **krisis/restored** (mis. mendung/berkabut untuk `ZephyriaCrisis`, senja/api untuk `PyrewoodCrisis`, terik/keemasan untuk `SolariaCrisis`/`SolariaRestored`), manfaatkan sebagai variasi atau gunakan preset langit cerah utama yang di-tint/dimodulasi oleh `BiomeAtmosphereController.cs`.

### 2. Perbarui `BiomeAtmosphereController.cs` (Pakai Skybox Eksternal, Jangan Timpa dengan Shader Polos Lama)
- Ubah `BiomeAtmosphereController.InitRuntimeSkybox()` agar **TIDAK** membuat material baru dari `DragonWingAR/DynamicBiomeSkybox` polos lama, melainkan:
  - Memakai **instance runtime** (`new Material(externalSkyboxTemplate)`) dari material skybox eksternal pilihan (dan membersihkannya di `OnDestroy()` agar file `.mat` asli milik paket eksternal tidak berubah/dirty di disk).
  - Memodulasi properti warna/tint (`_Tint` atau `_TintColor` jika ada pada shader skybox eksternal), `_Exposure`, serta `RenderSettings.fogColor` dan `RenderSettings.fogEndDistance` pada ke-7 state (`NeutralOcean`, `ZephyriaCrisis`, `ZephyriaRestored`, `PyrewoodCrisis`, `PyrewoodRestored`, `SolariaCrisis`, `SolariaRestored`).
  - Jika dipakai multi-material swap (mis. preset mendung saat krisis), pastikan transisi pergantian atau tint + kabut URP tetap mulus (`2.5–2.75s` saat `NotifyVillageRestored` / `CompleteVillageRestoration()`), serta **horizon kabut (`RenderSettings.fogColor`) selaras** dengan warna cakrawala bawah skybox agar batas tepi laut jauh (>2000m) tetap menyatu mulus.
  - Sinkronkan secara halus `RenderSettings.ambientMode = AmbientMode.Trilight` agar pencahayaan pulau & Fanamir selaras dengan skybox baru.

### 3. Pasang Permanen di *Lighting Settings* Scene
- Set `RenderSettings.skybox` di `MainIsland_Gameplay.unity` (dan opsional `Test_Dragon_Flight.unity`) langsung mengarah ke material skybox eksternal utama (`NeutralOcean`) dan simpan scenenya, supaya di **Edit Mode** pun skybox eksternal langsung terlihat (tidak lagi tampak seperti `Default-Skybox` Unity).
- Pastikan script builder (`Session4_VillageBuilder.cs` / `IslandArchipelagoSetupUtility.cs`) jika dijalankan ulang tidak mengembalikan skybox ke material lama.

### 4. Integrasikan Model Awan 3D dari Quaternius (`PF_StylizedClouds`)
- Temukan mesh awan 3D dari **Quaternius Modular Platformer Pack** di `Assets/` (mis. `Cloud*.fbx` / `Cloud*.obj` / `Cloud*.glb`). *(Pastikan materialnya dikonversi/memakai `Universal Render Pipeline/Lit` matte putih-krem `#FFFDF8` `Smoothness=0.08` atau `URP/Simple Lit` agar tidak pink di Unity 6 URP).*
- Buat prefab `Assets/Prefabs/World/PF_StylizedClouds.prefab` dan instansiasi di `MainIsland_Gameplay.unity` (`12–18` gugusan awan bervariasi ukuran & rotasi, sebar pada **radius `850–1650 m`** dan **ketinggian `Y = 260–480 m`**, tanpa collider, `shadowCastingMode = Off`, `receiveShadows = false`) agar memberi kedalaman parallax 3D tanpa menghalangi jalur penerbangan maupun marker desa.
- *(Opsional)* Hubungkan material awan 3D tersebut ke `BiomeAtmosphereController.cs` agar warna/tint awan sedikit ikut beradaptasi saat biome krisis.

### 5. Verifikasi Play Mode & Tangkap Screenshot
- Perbarui `VFXAtmosphereVerificationRunner.cs` bila ada assertion yang masih mengecek nama shader lama (`DynamicBiomeSkybox`), pastikan runner memverifikasi skybox eksternal aktif + fog transition + seluruh 10 VFX & FOV (`100% PASS`).
- Jalankan `VFXAtmosphereVerificationRunner` dan `MomentumFlightVerificationRunner` di `MainIsland_Gameplay.unity` (`0` compile errors, `0` console errors/warnings).
- Tangkap 4 screenshot bukti visual ke `docs/figures/raw_screenshots/`:
  1. `SS_Skybox_NeutralOcean.png` (di atas Center Peak memperlihatkan skybox baru + awan 3D Quaternius + kepulauan)
  2. `SS_Skybox_ZephyriaCrisis.png`
  3. `SS_Skybox_PyrewoodCrisis.png`
  4. `SS_Skybox_SolariaRestored.png`
- Kembalikan scene aktif ke `MainMenu_Lobby.unity` (Build Index `0`), perbarui `brain/laptop-b/Devlog.md` dan `brain/laptop-b/Tasks.md`, lalu `commit` & `push` perubahan `brain/`.
```
