---
title: Revisi VFX Skybox dan Bouncy Landing
type: session-memory
tags:
  - memory
  - laptop-b
  - vfx
  - skybox
  - camera
  - flight-controller
  - village-zone
date: 2026-11-26
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[kontrak_integrasi_ui_dan_progress]]"
---

# Revisi VFX Skybox dan Bouncy Landing

## 1. Tujuan & Konteks Sesi
Sesi ini berfokus pada integrasi artistik dan penyempurnaan gameplay loop Pilar 1 berdasarkan revisi Three.js Studio (`dragon_anim_studio/` `v24`) dari paket `Export_VFX_Revisi_LaptopB.zip`, serta penyesuaian dinamis kamera, logika pendaratan vs bouncing, dan visual skybox bergaya stylized Ghibli/Genshin.

Fokus utama yang diselesaikan:
1. **Revisi VFX Pilar 1 (10 Efek Effekseer + TrailRenderer):**
   - Mengompilasi 5 resep reworked + 3 resep baru (`VFX_Lake_MouthIntake`, `VFX_Solaria_ChestGemGlow`, `VFX_Pyrewood_Wildfire`) + 2 resep eksisting.
   - Mengatasi inversi arah koordinat Effekseer (-Z vs +Z) pada partikel semburan air dan hembusan angin.
   - Memasang sepasang `TrailRenderer` pada ujung sayap naga untuk menghasilkan pita sayap dinamis.
   - Mengoreksi penjangkaran permata dada pada tulang `Neck_01` (bukan `Spine_02`).
2. **Kamera Dinamis & Orbit Berpusat di Tengah Naga:**
   - Menambahkan event camera dinamis saat cutscene desa: diagonal frontview saat sayap mengepak di Zephyria, diagonal frontview saat minum air di danau Pyrewood, diagonal up-frontview saat Solaria, dan diagonal up-backview saat semburan pemadaman Pyrewood.
   - Mengubah titik orbit `LookOrbit` kamera naga agar berpusat di center naga (`TargetPosition`), bukan di belakang naga.
   - Menambahkan slider sensitivitas rotasi look di Settings (`0.5x` hingga `2.5x`, default `1.5x`).
3. **Penyempurnaan Logika Bouncy vs Ground Landing:**
   - Memisahkan permukaan darat yang bisa didarati (`normal.y >= 0.55f`) dengan tebing/dinding curam yang memantul (`normal.y < 0.55f`).
   - Mendarat di darat langsung mengalihkan mode ke `FlightMode.Ground`, mereset momentum, dan mewajibkan pose `#04 Takeoff` untuk terbang kembali.
4. **Revisi Skybox Fantasi Stylized & Parallax Clouds:**
   - Shader gradasi 4-stop (`_ZenithColor`, `_MidSkyColor`, `_HorizonColor`, `_NadirColor`) dengan awan kumulus FBM dua warna bertepi cerah (*silver lining*) dan awan sirus tinggi.
   - Prefab cincin awan 3D non-colliding `PF_StylizedClouds` (14 gugusan awan di radius `950–1650 m`, ketinggian `Y = 300–520 m`).
   - Integrasi 7 preset atmosfer dinamis pada `BiomeAtmosphereController.cs` dan penguncian skybox permanen di scene `MainIsland_Gameplay.unity` & `Test_Dragon_Flight.unity`.

---

## 2. Keputusan Desain / Arsitektur / Teknis

1. **Effekseer Local vs Global Forward (-Z Axis):**
   - Resep Effekseer yang diekspor menggunakan konvensi maju arah `-Z` pada koordinat lokal emitter.
   - Penyelarasan arah moncong (`mouthBoneOverride`) dan sayap disesuaikan dengan `TransformDirection(-Vector3.forward)` atau rotasi `180° Y` pada soket lokal agar partikel meluncur ke depan naga, bukan ke belakang atau ke bawah.
2. **Kamera Orbit Dragon-Centered:**
   - Di `DragonFlightController.cs`, `LookOrbit` sebelumnya menghitung titik pandang dari posisi kamera yang tertinggal di belakang. Ini menyebabkan putaran tampak mengayun di belakang naga.
   - Diperbaiki dengan mengarahkan rotasi mengitari `targetPosition` (posisi aktual tubuh naga) lalu mengalikan offset jarak mundur `-forward * currentDistance`.
   - Rasio pengali orbit ditautkan ke `GameSettingsState.cameraLookRotationMultiplier` yang dapat dikonfigurasi melalui slider UI Settings.
3. **Pemisahan Ambang Normal Bouncing (`bounceMaxNormalY = 0.55f`):**
   - Permukaan dengan `hitNormal.y >= 0.55f` (~sudut kemiringan < 56.6°) diklasifikasikan sebagai daratan yang dapat dipijak (`LandOnGround()`).
   - Permukaan dengan `hitNormal.y < 0.55f` (tebing tegak, dinding tebing curam) tetap memantulkan naga (`TryApplyGlancingBounce()`) dengan peredam kecepatan `bounceDamping = 0.65f`.
4. **Isolasi Material Skybox Runtime:**
   - `BiomeAtmosphereController.cs` membuat instance `Material` baru saat runtime (`Instantiate(skyboxMaterial)`) sehingga nilai transisi warna antar-desa tidak mengotori berkas `.mat` di disk selama sesi Play Mode.
   - Sinkronisasi warna kabut `RenderSettings.fogColor` dikunci identik dengan `_HorizonColor` untuk menyamarkan batas cakrawala laut.
5. **URP Render Graph Compatibility (`enableURPRasterPass: 1`):**
   - Di Unity 6 (`6000.3.7f1`), Render Graph aktif secara native. Mode fallback `AddUnsafePass` pada `EffekseerURPRenderPassFeature` saat `enableURPRasterPass: 0` memicu `Render Graph Execution error: ArgumentNullException: Value cannot be null. Parameter name: value` pada event GUI `GUIUtility:ProcessEvent` karena pemanggilan `(RenderTargetIdentifier)passData.colorTexture` tanpa raster attachment.
   - Mengaktifkan `enableURPRasterPass: 1` di `Assets/Resources/EffekseerSettings.asset` mengalihkan eksekusi ke `renderGraph.AddRasterRenderPass` (`ExecuteRasterRenderGraphPass`) sehingga Render Graph Unity 6 berjalan mulus tanpa error GUIUtility (`0` errors).

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset

### A. VFX & Controllers
- `Assets/VFX/Effekseer/*.efkefc`, `*.efk`, `*.asset`:
  - 10 efek terkompilasi bersih tanpa error:
    1. `VFX_Flight_WingTrail`
    2. `VFX_Flight_SonicDashShockwave`
    3. `VFX_Zephyria_WindGust`
    4. `VFX_Zephyria_AirRestoration`
    5. `VFX_Pyrewood_WaterBreath`
    6. `VFX_Pyrewood_ExtinguishSteam`
    7. `VFX_Pyrewood_Wildfire` *(Baru)*
    8. `VFX_Solaria_ChestGemGlow` *(Baru)*
    9. `VFX_Solaria_DewRain`
    10. `VFX_Lake_MouthIntake` *(Baru)*
- `Assets/_Scripts/Controllers/DragonVfxController.cs`:
  - Panjang tulang `wingtipBoneLength` dikoreksi ke `1.48f`.
  - Penambahan `TrailRenderer` pada sayap kiri dan kanan dengan alpha gradasi cyan-putih lembut (`time = 0.42f`, `startWidth = 0.16f`, `endWidth = 0.01f`).
  - Soket `mouthIntakeEmitter` terpasang di moncong kepala naga (`Head`).
  - Soket `chestGemEmitter` terpasang di tulang `Neck_01` pada posisi lokal `(0f, 0.1214f, -0.1552f)`.
- `Assets/_Scripts/Gameplay/VillageZoneController.cs`:
  - Array emitter `pyrewoodWildfireEmitters` untuk kebakaran hutan aktif Pyrewood yang mengecil mengikuti progres pemadaman api.
  - Durasi selebrasi hujan Solaria diperpanjang menjadi 14 detik.
  - Penambahan kamera sinematik dinamis per event desa.

### B. Kontrol Penerbangan & Kamera
- `Assets/_Scripts/Controllers/DragonFlightController.cs`:
  - Titik orbit `LookOrbit` berpusat di tengah naga (`targetPosition`).
  - Integrasi pengali sensitivitas rotasi kamera dari `GameSettingsState.cameraLookRotationMultiplier`.
  - Logika pemisahan pantulan curam (`normal.y < 0.55f`) vs pendaratan tanah (`LandOnGround()`).
- `Assets/_Scripts/UI/SettingsMenuController.cs`:
  - Penambahan UI slider `cameraLookRotationSlider` (`0.5x` - `2.5x`).
- `Assets/_Scripts/Core/GameSettingsState.cs`:
  - Penambahan variabel `cameraLookRotationMultiplier` dengan default `1.5f`.

### C. Shader, Skybox, & Lingkungan
- `Assets/Shaders/DynamicBiomeSkybox.shader`:
  - Shader 4-stop gradient (`_ZenithColor`, `_MidSkyColor`, `_HorizonColor`, `_NadirColor`).
  - Awan kumulus FBM prosedural dengan warna bagian terang (`_CloudLitColor`), bayangan (`_CloudShadowColor`), serta *silver lining* dan *cirrus streaks*.
  - Piringan matahari (`_SunColor`, `_SunSize`, `_SunHaloSize`) dengan arah dinamis `_WorldSpaceLightPos0`.
- `Assets/_Scripts/Environment/BiomeAtmosphereController.cs`:
  - Preset atmosfer 7-state: `NeutralOcean`, `ZephyriaCrisis`, `ZephyriaRestored`, `PyrewoodCrisis`, `PyrewoodRestored`, `SolariaCrisis`, `SolariaRestored`.
  - Sinkronisasi `AmbientMode.Trilight` (Sky, Equator, Ground) dan pengaturan rotasi awan 3D `PF_StylizedClouds`.
- `Assets/Prefabs/Environment/PF_StylizedClouds.prefab`:
  - 14 gugusan awan volumetrik stylized (84 puff mesh sphere non-colliding, bayangan dimatikan).
- `Assets/_Scenes/MainIsland_Gameplay.unity` & `Assets/_Scenes/Test_Dragon_Flight.unity`:
  - Skybox permanen `Mat_DynamicBiomeSkybox.mat` terpasang pada `RenderSettings.skybox`.

---

## 4. Detail Penting yang Harus Diingat
- **Soket Permata Dada:** Wajib berada di bawah tulang `Neck_01` (bobot mesh 56.6%), **jangan pernah dipindah ke `Spine_02`** karena akan melorot di bawah dada naga.
- **Nilai Ambang Bouncing:** `bounceMaxNormalY = 0.55f`. Normal bidang di atas nilai ini dianggap daratan mendarat, bukan bidang pantul.
- **Kamera Offset Z:** Jarak dinamis kamera naga saat hovering/terbang dikalkulasikan via `CurrentCameraOffsetZ` untuk mempertahankan jarak pandang optimal tanpa menggeser pusat rotasi naga.
- **Konvensi Arah Maju VFX Effekseer:** Perhatikan nilai `-forward` pada emitter semburan jika resep Effekseer diekspor menghadap `-Z`.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
1. Uji visual di perangkat mobile AR/VR target jika diperlukan verifikasi performa GPU pada shader kumulus procedural.
2. Lanjutkan integrasi gameplay Sesi 4 (Village Builder & Quest Flow) pada tahapan berikutnya.
3. Tetap pertahankan scene `MainMenu_Lobby.unity` sebagai scene awal build / aktif saat sesi ditutup.

---
*Dibuat oleh Laptop B sesuai protokol `brain/00 - Overview.md`.*
*Tautan Terkait: [[00 - Overview]] | [[Tasks]] | [[Devlog]]*
