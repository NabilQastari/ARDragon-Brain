---
title: Integrasi Skybox Eksternal dan Awan 3D
type: session-memory
tags:
  - memory
  - laptop-b
  - skybox
  - vfx
  - clouds
  - lighting
  - atmosphere
date: 2026-11-26
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[kontrak_integrasi_ui_dan_progress]]"
  - "[[spesifikasi_revisi_skybox_stylized]]"
  - "[[prompt_eksekusi_integrasi_skybox_dan_awan_eksternal_laptop_b]]"
---

# Integrasi Skybox Eksternal dan Awan 3D

## 1. Tujuan & Konteks Sesi
Sesi ini bertujuan untuk mengintegrasikan aset eksternal yang diimpor pengguna ke proyek Unity di Laptop B:
1. **Fantasy Skybox FREE** (`Assets/Fantasy Skybox FREE/`)
2. **AllSky Free - 10 Sky / Skybox Set** (`Assets/AllSkyFree/`)
3. **Quaternius Modular Platformer Pack** (`Assets/Assets 3D/Clouds/Cloud1.fbx..Cloud5.fbx`)

Integrasi ini menggantikan tampilan shader skybox prosedural polos lama (`DragonWingAR/DynamicBiomeSkybox`) pada scene gameplay (`MainIsland_Gameplay.unity` dan `Test_Dragon_Flight.unity`) agar pemandangan langit berawan fantasi terlihat nyata, menyatu dengan cakrawala laut (>2000m), dan tampak indah baik di **Edit Mode** maupun **Play Mode**. Selain itu, awan 3D Quaternius disesuaikan agar posisinya lebih rendah dan memiliki transparansi lembut sesuai permintaan langsung pengguna ("buat model awan lebih rendah, buat transparent").

---

## 2. Keputusan Desain / Arsitektur / Teknis

1. **Pemilihan Preset Skybox Utama (`FS003_Day.mat`):**
   - Setelah mengaudit material dari *AllSky Free* dan *Fantasy Skybox FREE*, dipilih `Assets/Fantasy Skybox FREE/Panoramics/FS003/FS003_Day.mat` sebagai preset utama untuk kondisi atmosfer **`NeutralOcean`**.
   - Shader yang digunakan adalah `Skybox/Panoramic` dengan tekstur panorama `2048×1024` (`FS003_Day.png`), yang mengekspos properti `_Tint` (atau `_TintColor`) dan `_Exposure` sehingga dapat dimodulasi secara halus oleh controller atmosfer saat terjadi transisi status krisis biome.

2. **Isolasi Runtime Material (`BiomeAtmosphereController.cs`):**
   - Menghindari pembuatan material dari shader polos lama. Sebagai gantinya, runtime memuat template material eksternal pilihan (`FS003_Day.mat`) dan menginstansiasi instance runtime baru via `new Material(externalSkyboxTemplate)` dengan nama `Runtime_ExternalBiomeSkybox`.
   - Instance runtime ini dimodifikasi selama Play Mode dan dibersihkan secara aman pada `OnDestroy()` (`Destroy(runtimeMaterial)`), sehingga file `.mat` asli milik paket eksternal tidak menjadi kotor (*dirty*) di disk.

3. **Penyelarasan Cakrawala Laut & Kabut (`RenderSettings.fogColor`):**
   - Warna kabut dasar untuk `NeutralOcean` diselaraskan ke `#D2F2FA` (cyan langit terang lembut) agar batas cakrawala laut pada jarak jauh (>2000m) menyatu sempurna dengan bagian bawah panorama skybox tanpa garis patah yang kontras.
   - Mengaktifkan `RenderSettings.ambientMode = AmbientMode.Trilight` dengan palet warna langit, khatulistiwa, dan daratan yang selaras dengan nuansa langit baru.

4. **Awan 3D Transparan Rendah (`PF_StylizedClouds.prefab`):**
   - Menggunakan 5 variasi model 3D awan low-poly dari Quaternius (`Cloud1.fbx` hingga `Cloud5.fbx`).
   - Material `Mat_StylizedCloud3D.mat` dikonfigurasi ke mode **Transparent** pada shader `Universal Render Pipeline/Lit`:
     - `_Surface = 1` (Transparent), `_Blend = 0` (Alpha), `_ZWrite = 1` (ZWrite on agar depth ordering awan rapi).
     - Render Queue dikunci ke `3000` (Transparent).
     - Warna dasar `_BaseColor = (1.0f, 0.992f, 0.973f, 0.45f)` (`alpha = 0.45f` / transparan 45%).
   - Ketinggian awan diturunkan dari semula `Y = 260–480 m` menjadi **`Y = 115–230 m`** dengan sebaran radius **`850–1550 m`** (16 gugusan awan di sekeliling kepulauan). Hal ini memberikan nuansa awan melayang dekat dengan lintasan terbang naga tanpa menghalangi pandangan pemain.
   - Seluruh collider dinonaktifkan, `shadowCastingMode = Off`, dan `receiveShadows = false`.
   - Pewarnaan dinamis awan 3D saat biome krisis dikendalikan via `MaterialPropertyBlock` pada seluruh child renderer di `PF_StylizedClouds`.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset

### Aset yang Dibuat & Dikonfigurasi
1. `Assets/Materials/Mat_StylizedCloud3D.mat`:
   - Material URP Lit Transparent dengan albedo putih-krem `#FFFDF8`, alpha `0.45`, smoothness `0.08`, `renderQueue = 3000`.
2. `Assets/Prefabs/World/PF_StylizedClouds.prefab`:
   - Prefab cincin 16 gugusan awan Quaternius (`Cloud1`..`Cloud5`) dengan variasi rotasi dan skala `(135–235 m)` di ketinggian `Y = 115–230 m`.
3. Screenshot hasil verifikasi Play Mode di `docs/figures/raw_screenshots/`:
   - `SS_Skybox_NeutralOcean.png` (langit cerah berawan fantasi + awan transparan lembut).
   - `SS_Skybox_ZephyriaCrisis.png` (kabut polusi tebal mendung kehijauan).
   - `SS_Skybox_PyrewoodCrisis.png` (langit oranye kemerahan kebakaran hutan).
   - `SS_Skybox_SolariaRestored.png` (langit keemasan cerah pasca pemulihan embun).

### Berkas Skrip & Konfigurasi yang Dimodifikasi
1. `Assets/_Scripts/Gameplay/BiomeAtmosphereController.cs`:
   - Menambahkan field `stylizedCloudAlpha = 0.45f` ber-range `[0.1f, 1.0f]`.
   - Menambahkan cache `_cloudRenderers` dan `_cloudPropBlock` untuk modulasi warna awan tanpa merusak material bersama.
   - Memperbarui `InitRuntimeSkybox()` untuk menginstansiasi `externalSkyboxTemplate` (`FS003_Day.mat`).
   - Memperbarui `AtmosphereProfile` pada seluruh 7 state biome (`NeutralOcean`, `ZephyriaCrisis`, `ZephyriaRestored`, `PyrewoodCrisis`, `PyrewoodRestored`, `SolariaCrisis`, `SolariaRestored`) dengan properti `skyboxTint` dan warna kabut selaras `#D2F2FA`.
2. `Assets/Editor/Session4_VillageBuilder.cs`:
   - Mengganti referensi material skybox lama dengan `GetExternalSkyboxTemplate()` yang memprioritaskan `FS003_Day.mat`.
   - Mengonfigurasi `BuildAndPlaceStylizedCloudsInScene()` untuk menghasilkan awan transparan URP (`_Surface=1`, `renderQueue=3000`, `alpha=0.45`) di rentang `Y = 115–230 m`.
   - Memperbarui `ApplyEditModeNeutralAtmosphere()` dan `EnsureTestFlightSceneSkybox()` agar menggunakan palet warna dan skybox baru secara permanen.
3. `Assets/Editor/IslandArchipelagoSetupUtility.cs`:
   - Memperbarui instalasi skybox bawaan ke `FS003_Day.mat` dan `fogColor` `#D2F2FA`.
4. `Assets/Editor/VFXAtmosphereVerificationRunner.cs`:
   - Menyesuaikan verifikasi pengujian visual atmosfer agar kompatibel dengan runtime external skybox.
5. `Assets/_Scenes/MainIsland_Gameplay.unity`:
   - `RenderSettings.skybox` diperbarui ke `FS003_Day.mat`.
   - Instance `PF_StylizedClouds` diperbarui dengan material transparan dan ketinggian yang lebih rendah.

---

## 4. Detail Penting yang Harus Diingat

- **Skybox Utama:** `Assets/Fantasy Skybox FREE/Panoramics/FS003/FS003_Day.mat` (Shader: `Skybox/Panoramic`, `_Tint`, `_Exposure`).
- **Warna Horizon Fog Neutral:** `#D2F2FA` (RGB: `0.824, 0.949, 0.980`), menyamarkan batas laut kepulauan (>2000m).
- **Parameter Ketinggian Awan 3D:**
  - Ketinggian Y: `115–230 m` (sebelumnya 260–480 m).
  - Jarak Radius: `850–1550 m`.
  - Skala Lebar Awan: `135–235 m`.
  - Alpha Transparansi: `0.45f` (`Mat_StylizedCloud3D.mat` URP Lit Transparent, `renderQueue = 3000`).
- **Penguncian Permanen di Scene:**
  - Di Edit Mode, `RenderSettings.skybox` mengarah langsung ke `FS003_Day.mat`.
  - Di Play Mode, `BiomeAtmosphereController` membuat clone instance runtime `Runtime_ExternalBiomeSkybox` dan menghancurkannya saat scene dibongkar (`OnDestroy()`).
- **Verifikasi Lulus 100%:**
  - `VFXAtmosphereVerificationRunner`: 10/10 PASS (VFX sockets, head tracking sweep 23.3°, wildfire decay, dynamic FOV, fog distances).
  - `MomentumFlightVerificationRunner`: 100% PASS (seluruh fisika penerbangan & momentum naga stabil).
  - Console Errors: `0`, Console Warnings: `0`.
- **Urutan Scene Aktif:**
  - Scene awal aktif dikembalikan ke `MainMenu_Lobby.unity` (Build Index `0`).

---

## 5. Tugas Tersisa & Langkah Selanjutnya

1. **Sesi 5 Lanjutan — Build Windows Standalone & Launcher:**
   - Melakukan build executables Windows standalone untuk 3 scene utama:
     - `MainMenu_Lobby.unity` (Build Index `0`)
     - `MainIsland_Gameplay.unity` (Build Index `1`)
     - `Test_Dragon_Flight.unity` (Build Index `2`)
   - Menyediakan script launcher otomatis `start_game.bat` yang memeriksa dependensi Python CV dan meluncurkan game secara mulus.
2. **Uji Langsung Pemain (User Playtest):**
   - Menguji kenyamanan visual awan transparan rendah saat terbang manuver menukik dan menanjak di sekeliling 3 biome pulau.

---

### Navigasi Vault
- [[00 - Overview]]
- [[Tasks]]
- [[Devlog]]
- [[spesifikasi_revisi_skybox_stylized]]
- [[prompt_eksekusi_integrasi_skybox_dan_awan_eksternal_laptop_b]]
