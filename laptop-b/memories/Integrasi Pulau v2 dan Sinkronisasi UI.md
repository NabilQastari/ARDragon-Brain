---
title: Integrasi Pulau v2 dan Sinkronisasi UI
type: session-memory
tags:
  - laptop-b
  - island
  - unity6
  - urp
  - blender
  - ui
  - feature-to-ui-sync
date: 2026-11-15
archived: 2026-11-21
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[Implementasi UI Hybrid AR Dragon]]"
  - "[[laptop-a/kontrak_integrasi_ui_dan_progress]]"
  - "[[GDD_Pulau]]"
---

# 🏝️ Integrasi Pulau v2 (`Pulau_Archipelago`) & Sinkronisasi UI

## 1. Tujuan & Konteks Sesi
Mengekspor model kepulauan 3-biome yang sudah selesai dari **Laptop A** ([`Pulau_Archipelago.blend`](file:///d:/Fauzih/Kuliah/SEMESTER%20V/Proyek%20Multimedia%20dan%20Game/AR%20Dragon/assets/blender/upgrade%20model%20pulau/Pulau_Archipelago.blend)), mengonversi tekstur aktif agar akurat di **Unity 6 URP** (`6000.3.7f1`), merakit prefab & scene gameplay utama (`MainIsland_Gameplay.unity`) pada skala `1:6` (`2400 × 2400 m`), menjalankan **Feature-to-UI Sync** sesuai [[laptop-a/kontrak_integrasi_ui_dan_progress]] tanpa merusak scene uji terbang `Test_Dragon_Flight.unity`, sekaligus memperbaiki tata letak kotak bintang & status kunci default Stage 02–03 pada halaman level `SCREEN 00 (Missions)`.

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Pemisahan Scene Gameplay vs Scene Uji Mekanisme:**
   - Membuat scene gameplay khusus `Assets/_Scenes/MainIsland_Gameplay.unity` (**Build Index `1`**) hasil kloning rig `/Dragon_Player` terkalibrasi + UI dari `Assets/_Scenes/Test_Dragon_Flight.unity`, sementara `Test_Dragon_Flight.unity` (**Build Index `2`**, `/Grass_Baseplate`, `TEST FLIGHT (0/3)`) dipertahankan utuh untuk uji regresi.
   - Mengarahkan `DragonUITheme.FlightSceneName` ke `"MainIsland_Gameplay"` (dan menambahkan `TestFlightSceneName = "Test_Dragon_Flight"`), serta membuat `FinishModalUI.HandleRetryClicked()` me-reload scene aktif via `SceneManager.GetActiveScene().name`.
2. **Konversi Peta Roughness Blender (`*_R.png`) ke Metallic-Smoothness URP (`*_MS.png`):**
   - Peta `*_R.png` dari Blender bernilai grayscale *Roughness* (`0..242` untuk terrain/batu/pohon/rumah *matte*, `0..20` untuk air halus). Jika dipakai langsung di `_MetallicGlossMap` URP/Lit, kanal Alpha terbaca sebagai *Smoothness* sehingga seluruh objek tampak basah/plastik.
   - Seluruh `23 *_R.png` dikonversi menjadi RGBA `*_MS.png` dengan `R = G = B = 0` dan **`Alpha = 255 - Roughness`** untuk menjaga gaya visual *stylized matte* pada daratan/desa dan kilap realistis hanya pada air.
3. **Ekspor & Collider Selektif:**
   - Ekspor Blender hanya menyertakan **`349` objek mesh gameplay** (`COL_Island_Terrain`, `COL_Biome1_Water`, `COL_Island_Rocks`, `COL_Village_Houses`, `COL_Foliage_Trees`) dengan `-Z Forward, Y Up` & `FBX_SCALE_UNITS` (mengecualikan `COL_Cameras_Lights` dan `COL_Reference`), lalu Unity `ModelImporter.bakeAxisConversion = true` menyelaraskan orientasi tepat sesuai referensi [`Peta_Denah_Resmi_Genshin_Style.jpg`](file:///d:/Fauzih/Kuliah/SEMESTER%20V/Proyek%20Multimedia%20dan%20Game/AR%20Dragon/assets/blender/upgrade%20model%20pulau/Peta_Denah_Resmi_Genshin_Style.jpg).
   - `MeshCollider` hanya dipasang pada `SM_Archipelago_Terrain` dan `4` grup `SM_Rocks_*` (**`5` collider total**); `Ocean_Surface` (`Y = 0`) & `SM_Biome1_Water_01` diletakkan di layer `Ignore Raycast` tanpa collider agar bebas tembus/tidak menghalangi raycast daratan.
4. **Perbaikan Visual Kotak Bintang & Penguncian Default Level 2–3 (`SCREEN 00`):**
   - Menyamakan skala aktual PNG `UI_StarBox_Card_Slot_80x76.png` (`80×80`), menggeser ikon bintang ke atas ke tengah area krem, dan menambahkan label `STAR 1..3` di pill gelap bawah.
   - Mengunci **Stage 02 (`WILDFIRE CANYON`)** maupun **Stage 03 (`DROUGHT VALLEY`)** secara default pada saat sesi ini (`IsStage2UnlockedPlaceholder` & `IsStage3UnlockedPlaceholder` default `0` / `false`), memblokir peluncuran gameplay saat diklik dengan menampilkan `HintToast`, dan mengepas ukuran `LockedScrim` agar tidak meluber keluar bingkai gambar biome.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset

### A. Ekspor Blender, Tekstur URP, Material, Prefab, & Scene
- **Blender CLI Inspection & Export:** Memverifikasi `353` objek di `Pulau_Archipelago.blend` (`350` MESH, `2` CAMERA, `1` LIGHT; `0` modifier, `0` missing UV) lalu mengekspor `349` mesh gameplay dengan Blender `5.1.2` CLI menjadi `AR Dragon Sky Guardian/Assets/Assets 3D/Pulau_Archipelago/Pulau_Archipelago.fbx` (`4,584,124` bytes).
- **Tekstur URP (`69` file):** `AR Dragon Sky Guardian/Assets/Assets 3D/Pulau_Archipelago/Textures/` (`23 *_BC.png`, `23 *_N.png`, dan `23 *_MS.png`).
- **Setup Otomatis URP & World Prefab (`AR Dragon Sky Guardian/Assets/Editor/IslandArchipelagoSetupUtility.cs`):**
  - Mengonfigurasi `69` `TextureImporter` (`_BC` sRGB max `8192/2048`, `_N` NormalMap Linear max `4096/2048`, `_MS` Default Linear alpha `FromInput`).
  - Membuat `23` material `Universal Render Pipeline/Lit` + `Mat_Archipelago_Ocean.mat` (Transparent `#184E66`, `alpha = 0.86`, smoothness `0.90`).
  - Merakit `AR Dragon Sky Guardian/Assets/Prefabs/World/PF_Pulau_Archipelago.prefab` (`Archipelago_Model` skala `(6, 6, 6)`, `5` `MeshCollider`, & `Markers` raycast-grounded) dan membangun `AR Dragon Sky Guardian/Assets/_Scenes/MainIsland_Gameplay.unity`.

### B. Feature-to-UI Sync & Perbaikan Halaman Level (`Missions`)
- `AR Dragon Sky Guardian/Assets/_Scripts/Controllers/DragonFlightController.cs`: menaikkan default `maxAltitude` dari `160f` ke **`260f`** guna mengakomodasi puncak kepulauan (`~165 m`).
- `AR Dragon Sky Guardian/Assets/_Scripts/UI/DragonGameSettings.cs`: `DragonUITheme.FlightSceneName = "MainIsland_Gameplay"`, penambahan `TestFlightSceneName = "Test_Dragon_Flight"`, serta default `0` (`false`) untuk `IsStage2UnlockedPlaceholder` dan penambahan `IsStage3UnlockedPlaceholder`.
- `AR Dragon Sky Guardian/Assets/_Scripts/UI/FinishModalUI.cs`: `HandleRetryClicked()` me-reload scene aktif menggunakan `SceneManager.GetActiveScene().name` (fallback ke `DragonUITheme.FlightSceneName`).
- `AR Dragon Sky Guardian/Assets/_Scripts/UI/InGameFlightHUD.cs`: inisialisasi `_missionVillageStr` pada `Awake()` ke `"TEST FLIGHT (0/3)"` untuk `Test_Dragon_Flight` vs `"ARCHIPELAGO (0/3)"` untuk `MainIsland_Gameplay`.
- `AR Dragon Sky Guardian/Assets/_Scripts/UI/MissionsPanelUI.cs`: label Stage 01 `"ARCHIPELAGO"`, refactoring penguncian Stage 02 & Stage 03 melalui helper bersama `ApplyStageLockVisual(...)`, `SetStage2Locked(...)`, `SetStage3Locked(...)`, pembaruan `SetStageProgress(...)`, serta blokir klik Stage 02/03 terkunci dengan `ShowLockHint(...)`.
- `AR Dragon Sky Guardian/Assets/Editor/UISceneAndPrefabBuilder.cs`:
  - Menambah `MainIslandScenePath`, merefaktor `UpgradeFlightScene` $\rightarrow$ `UpgradeGameplayScene(...)`, serta mendaftarkan `3` scene di `EditorBuildSettings` (`0: MainMenu_Lobby`, `1: MainIsland_Gameplay`, `2: Test_Dragon_Flight`).
  - Memperbaiki `BuildStageCard` `StarSlot_1..3` ke `80×80`, `StarIcon` (`44×44`, `preserveAspect = true`, `(0f, 11f)`), penambahan `StarLabel` (`STAR 1..3`, `_fontNum`, `PureWhite`, `NoWrap`, auto-size `7.5f..10f`, `(0f, 8.5f)`, `(-12f, 18f)`), dan menyelaraskan `StarLabel` `PF_FinishModal.prefab` (`y = 6f -> 11f`).
  - Mengonfigurasi `Stage02_Card` (`CLEAR STAGE 01`) & `Stage03_Card` (`CLEAR STAGE 02`) terkunci default (`STAGE 02/03 · LOCKED`, `UI_Btn_Locked_Charcoal_9Slice`, teks `LOCKED` warna `DragonUITheme.ParchmentBase`), serta menyempurnakan `LockedScrim` (`438×242` pada `(0f, -152.5f)` di belakang `LimitCallout`, `padlock.preserveAspect = true`).

---

## 4. Detail Penting yang Harus Diingat

### A. Koordinat & Parameter Lingkungan `MainIsland_Gameplay`
- **Spawn `/Dragon_Player` (`Spawn_CenterPeak`):** Posisi `(0.00, 165.85, 15.00)` tepat di atas pad raycast `(0.00, 165.00, 15.00)`, orientasi **Yaw `-15°`** menghadap lembah kaldera *Smog Valley*.
- **Kamera & Atmosfer:** `nearClipPlane = 0.5f`, `farClipPlane = 4200f`, *Linear Fog* `1150..4200 m`, `maxAltitude = 260f`.
- **Koordinat `Markers` Hasil Raycast (`PF_Pulau_Archipelago`, Skala `6×`):**
  - `Village_B1_SmogValley`: `(-562.40, 14.10, 274.30)`
  - `Village_B2_WildfireWoods`: `(739.60, 12.90, 7.80)`
  - `Village_B3_DroughtPlateau`: `(39.40, 14.70, -647.60)`
  - Referensi kolam/air Biome 1 (`Water`): `(-50.02, 2.35, 110.93)` (skala Blender 1× -> `(-300.12, 14.10, 665.58)` pada `6×`).

### B. Status Verifikasi & Guard Kolaborasi
- **Verifikasi Editor & Visual (`Unity 6000.3.7f1`):** `IslandArchipelagoSetupUtility.SetupAllAndBuildMainIslandScene()` melaporkan `Renderers=350`, `BadShader=0`, `NullBaseTex=0`, `Colliders(5)`, serta inspeksi visual screenshot 3 biome, desa B1/B2, lobby `Missions`, `FinishModal`, dan Play Mode `MainIsland_Gameplay` lulus bersih (`compilationFailed: false`, `0` console error / warning runtime).
- **Verifikasi Play Mode 3 Scene:**
  - `MainMenu_Lobby`: Stage 02 & 03 terkunci (`Scrims=[True,True]`, `Tags=[STAGE 01 · OPEN|STAGE 02 · LOCKED|STAGE 03 · LOCKED]`, `Btns=[LOCKED|LOCKED]`), klik Stage 02/03 memunculkan `HintToast` tanpa memuat scene terbang, `SetStageProgress` unlock/relock lulus, `StarSlot_1..3` kosong/emas & `STAR 1..3` bebas overlap, dan klik Stage 01 `PLAY` memuat `MainIsland_Gameplay`.
  - `MainIsland_Gameplay`: `Pos=(0.00, 165.85, 15.00)`, `IsGrounded=True`, `MaxAlt=260`, `Dormant=[N:False, C:False, F:False, P:False]`, `PauseToggleOK=True`, `PF_FinishModal` (`STAR 01..03`) bersih.
  - `Test_Dragon_Flight` (regresi): `HasBaseplate=True`, `DragonPos=(0.00, 0.85, 0.00)`, `IsGrounded=True`, kapsul `--:-- · TEST FLIGHT (0/3)`.
- **Kendala Build All & Domain Reload:** Saat mengedit skrip Editor & menjalankan `UISceneAndPrefabBuilder.BuildAll()`, pastikan Unity sudah selesai recompile (`codeLoadCount` bertambah / `compiling == false`) sebelum memanggil `BuildAll()` via `script_eval`, karena eksekusi sebelum domain reload selesai akan memakai assembly lama.
- **Aturan Kerja Workspace:** Seluruh perubahan `brain/` hanya boleh ditulis di `brain/laptop-b/` (`brain/laptop-a/` read-only); commit sesi ini tercatat pada `4f267bd`, `5227fc5`, dan `ded85df`.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- **Langkah lanjutan pasca sesi (`2026-11-15`):** Sesi 4 (`VillageZoneController` & Tantangan 3 Desa), Sesi 4B (Paket Visual VFX & Skybox Dinamis), dan Sesi 5 (`GameManager`, Timer, Bintang, Audio/SFX, & Build Standalone).
- **Status Vault Terkini (`2026-11-21`):** Sesi 4 (`3144c23`) dan Sesi 4B (`21f2103`) telah selesai dan tercatat di `[[Tasks]]` & `[[Devlog]]`; tugas utama yang masih tersisa adalah **Sesi 5 & Feature-to-UI Sync (`GameManager.cs`, sistem bintang progres global, Audio/SFX, & build Windows standalone)** sesuai `[[laptop-a/prompt_eksekusi_sesi_5_gameloop_dan_scoring_laptop_b]]`.

---

[[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[Implementasi UI Hybrid AR Dragon]] · [[GDD_Pulau]] · [[laptop-a/kontrak_integrasi_ui_dan_progress]]
