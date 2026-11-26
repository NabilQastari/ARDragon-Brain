---
title: Laptop B — Devlog (Eksekusi & Pengembangan Program)
tags:
  - devlog
  - laptop-b
owner: Laptop B
updated: 2026-11-26
---

# 🛠️ Devlog — Laptop B

> Catatan kronologis pengembangan kode, debugging, pengujian Unity/Python, dan hasil build dari **Laptop B**. Kembali ke [[00 - Overview]].

---

## 2026-11-27 — Loading Screen (`SCREEN 11`), UI Event Desa Minimalis (`416×520`), & Animasi Juicy UI Pilar 2 ([[Loading Screen UI Desa Minimalis dan Juicy UI]])
- **Catatan Memori:** [[Loading Screen UI Desa Minimalis dan Juicy UI]]
- **Ringkasan:** Mengimpor paket aset `Export_UI_Animasi_Loading_Desa_LaptopB/` dan mengimplementasikan pembaruan antarmuka utama:
  1. **Impor Aset (`UISpriteSetupUtility.cs`):** Menyalin `10` PNG `08_LoadingScreen/`, `6` PNG `05_Challenge_Minimalist/`, dan `3` referensi `Designs/` (`11_LoadingScreen.png`, `03B_SideDocked_ChallengeHUD_Improved.png`, `03C_SideDocked_3Biome_Showcase.png`), lalu mengonfigurasi seluruh **`158` PNG** (`82` 9-Slice/3-Slice borders, `FullRect`, `Bilinear`, `SpriteMode.Single`).
  2. **Loading Screen Antar-Scene (`LoadingScreenManager.cs`, `UILoadingWaveTrailGraphic.cs`, `PF_LoadingScreenOverlay.prefab`):** Membangun singleton `DontDestroyOnLoad` (`SortingOrder 999`) dengan latar senja twilight 4-stop + pulau `0.32` + vignette, crest `"FANAMIR · THE SKY GUARDIAN"`, animasi naga mengambang (`Sin(t*2.2)*14px`, rotasi Z `±4°`) dengan pita trail dinamis cyan-ke-emas (`MaskableGraphic`) menempel di ekor + bintang kelip, kartu tip acak `"TAHUKAH KAMU?"`, serta bottom progress bar + `UI_Loading_Tip_Star`. Mengintegrasikan `LoadSceneAsync` (`FadeIn 0.30s -> Async Load -> Smooth 0.9..1.0 -> Hold 0.25s -> FadeOut 0.35s`) pada `DragonGameSettings.LoadSceneCleanly`.
  3. **UI Event Desa Minimalis (`PF_VillageChallengeOverlay.prefab`, `VillageChallengeUI.cs`, `VillageZoneController.cs`):** Merombak panel samping menjadi **`416 × 520 px`** *Middle-Right* `(-32, 0)` dengan 4 zona bersih, 3 emblem custom baru (`UI_Emblem_Zephyria_WingFlap`, `UI_Emblem_Pyrewood_WaterBreath`, `UI_Emblem_Solaria_DewSummon`), badge status terpusat (`● GERAKAN TERDETEKSI!` / `○ MENUNGGU GERAKAN...`), & escape hint bersih tanpa kotak.
  4. **Animasi "Juicy UI" Pilar 2 (`UIAnimationUtils.cs`, `UIButtonJuice.cs`, & Controller UI):** Mengimplementasikan slide intro/outro `EaseOutBack`/`EaseInQuad` + punch scale `1.08x` (`VillageChallengeUI`), dual-layer trailing ghost bar `0.08s`/`0.35s` (`VillageChallengeUI` & `InGameFlightHUD`), `UnrollY` + fade + countdown shimmer sweep (`QuestNarrativeUI`), modal drop-in `EaseOutBounce` + pop bintang `0.55s`/`0.95s`/`1.35s` + sparkle burst emas bintang ke-3 (`FinishModalUI`), micro-interaction tombol `1.04x`/`0.96x`, serta *denied horizontal shake* `±10 px` (`MissionsPanelUI` & `CalibrationPanelUI`).
  5. **Verifikasi Play Mode & Screenshot (`0` Errors, `0` Warnings):** Lulus penuh pada `UIJuiceAndLoadingVerificationRunner`, `Session4VerificationRunner` (`100% PASS`), & `Session5GameLoopVerificationRunner` (`100% PASS`); menyimpan `SS_UI_LoadingScreen.png` & `SS_UI_Minimalist_VillageHUD_3Biomes.png` di `docs/figures/raw_screenshots/`; serta mengembalikan scene aktif ke `MainMenu_Lobby.unity`.

---

## 2026-11-26 — Sinkronisasi dan Push Progres Utuh Laptop B ke Repositori Project GitHub
- **Branch Baru:** `feat/laptop-b-full-implementation` -> `origin/feat/laptop-b-full-implementation`
- **Ringkasan:** Mengaudit working tree repositori game proyek utama (`AR-Dragon.git`) yang sebelumnya belum pernah dipush selama 2 minggu sejak commit `62c7e90` (12 November 2026):
  1. **Audit & Penataan `.gitignore`:** Menambahkan pengabaian file arsip biner besar/ekspor sementara (`*.zip`, `Export_Delta_LaptopB/`, `**/Export_*/`, `assets/archives/`), cache Obsidian root (`.obsidian/`), serta temporary scene crash dump (`**/[Aa]ssets/_Recovery/`) agar repositori tetap ramping dan tidak menembus batas GitHub.
  2. **Pembuatan Branch Khusus:** Membuka branch aman `feat/laptop-b-full-implementation` agar tidak menimpa branch `master` secara langsung tanpa review.
  3. **Commit Terstruktur (4 Tahap):**
     - `chore(config)`: Pembaruan `.gitignore`, `.antigravityignore`, dan `AGENTS.md`.
     - `feat(cv)`: Integrasi penuh Python CV hybrid YOLOv8 + MediaPipe Pose, stabilisasi One-Euro, motion recognizer 19 pose, dan seluruh test suite.
     - `feat(unity)`: Seluruh sistem UI Hybrid (Lobby, Calibration, HUD, Settings, Missions, FinishModal, QuestNarrative), animasi tubuh prosedural (`DragonBodyAnimator`), audio terpadu dual BGM/SFX, kontrol momentum v5.0, serta scene `MainMenu_Lobby.unity` & `MainIsland_Gameplay.unity`.
     - `feat(assets, docs)`: Pustaka VFX Effekseer, model Blender pulau 3-biome, tools bat/scripts, skill agen, serta dokumen pendukung.
     - `chore(cleanup)`: Pembersihan file legacy di root (`Assets AR Dragon Sky Guardian/` blender lama, docx/md lama yang sudah direorganisasi).
  4. **Push ke GitHub:** Berhasil mem-push branch `feat/laptop-b-full-implementation` ke `https://github.com/NabilQastari/AR-Dragon.git` dengan status tracking `origin/feat/laptop-b-full-implementation` aktif.

---

## 2026-11-26 — Implementasi Kontrol Halus, Steep Bounce Toggle, Ground Lockout, Escape Desa, & Momentum v5.0 ([[Implementasi Kontrol dan Momentum v5]])
- **Catatan Memori:** [[Implementasi Kontrol dan Momentum v5]]
- **Ringkasan:** Mengimplementasikan paket spesifikasi resmi v5.0 dari [[laptop-a/gameplay-mechanics/spesifikasi_kecepatan_momentum_v5_dan_kontrol]]:
  1. **Kemudi Belok Halus & Settings (`DragonFlightController.cs`, `DefaultFlightInputMapper.cs`, `IFlightInputProvider.cs`, `DragonGameSettings.cs`, `SettingsPanelUI.cs`, `UISceneAndPrefabBuilder.cs`):** Menetapkan deadzone tengah `±0.08`, kurva non-linier `pow((|r|-0.08)/0.92, 1.35f)` bebas *double-curve*, *Wing Overdrive Steer* `0.22f`, serta menambahkan slider `Row_SteeringSensitivity` (`Fanamir_SteeringSensitivity`, `0.5x–2.0x`, default `1.0x`) di `PF_SettingsPanel.prefab` (`SettingsCard` `960×820 px`).
  2. **Toggle Steep Bouncy Collision (`DragonGameSettings.cs`, `SettingsPanelUI.cs`, `UISceneAndPrefabBuilder.cs`, `DragonFlightController.cs`):** Menambahkan toggle `Row_SteepBounceToggle` (`Fanamir_SteepBounceEnabled`, `1 = ON / 0 = OFF`, default `1`); saat `ON` penalti `ApplyGlancingBounce()` menjadi `-10%` momentum, saat `OFF` `ApplySmoothSteepSlide()` meluncur geser menyusuri tebing tanpa gaya tolak mendadak atau guncangan kamera.
  3. **Ground Mode, Takeoff 3 Kepakan, & Air Pose Lockout (`DragonFlightController.cs`, `DragonWingAnimator.cs`):** Mewajibkan `3` kepakan bertenaga (`FlapPower >= 0.28f` dalam `2.5s`) untuk takeoff (`+10%` momentum instan), memastikan T-Pose/`UpperArmOpenness >= 0.35f` konsisten berjalan `3.5 m/s` (rapat `< 0.25f` berhenti), memblokir total seluruh pose udara saat grounded (termasuk di `DragonWingAnimator`), serta memisahkan kasual landing `3.0s` slide decay vs *Glide-to-Ground Seamless Walk* (`3.5 m/s`).
  4. **Aturan Escape Desa & Momentum v5.0 (`VillageZoneController.cs`, `DragonFlightController.cs`, `DragonLocomotionConfig.cs`/`.asset`, `EffekseerSettings.asset`):** Standarisasi escape Pyrewood & Solaria (`3x` kepakan dalam `2.5s`) vs Zephyria (`100%` hanya Tuck Dive `#13 >= 0.6s`), dorongan keluar dinamis sesuai momentum & pose aktif, pembaruan kurva kecepatan/momentum v5.0 (`16–150 m/s`), serta pengaktifan `enableURPRasterPass: 1` di `EffekseerSettings.asset`.
  5. **Verifikasi Play Mode (`0` Errors, `0` Warnings):** `MainMenu_Lobby.unity` (`SteeringSlider=0.5..2`, `SteepBounceToggle=ON/OFF`), `MomentumFlightVerificationRunner` (`ALL MOMENTUM v5.0 & FLIGHT PHYSICS CHECKS PASSED (100%)`), dan `Session4VerificationRunner` (`ALL SESSION 4 VILLAGE, SUB-MARKER, AUTO-HOVER, ESCAPE, BILLBOARD & UI ISOLATION CHECKS PASSED (100%)`), lalu scene aktif dikembalikan ke `MainMenu_Lobby.unity` (Build Index `0`).

---

## 2026-11-26 — Diagnosa Error Render Graph Execution `ArgumentNullException` ([[Diagnosa Render Graph Execution Error]])
- **Catatan Memori:** [[Diagnosa Render Graph Execution Error]]
- **Ringkasan:** Mendiagnosa error berulang di Console Unity Editor (`6000.3.7f1`):
  `Render Graph Execution error UnityEngine.GUIUtility:ProcessEvent` dengan `ArgumentNullException: Value cannot be null. Parameter name: value`.
  - **Akar Masalah:** Di Unity 6 URP Render Graph, setting `enableURPRasterPass: 0` pada `Assets/Resources/EffekseerSettings.asset` menyebabkan renderer feature Effekseer jatuh ke mode fallback `AddUnsafePass` dan melakukan casting eksplisit `(RenderTargetIdentifier)passData.colorTexture` serta `CoreUtils.SetRenderTarget(cmd, color)` saat frame repaint GUI (SceneView/GameView).
  - **Rekomendasi:** Mengaktifkan opsi **Enable URP Raster Pass** (`enableURPRasterPass: 1`) di `EffekseerSettings.asset` mengalihkan eksekusi ke native `renderGraph.AddRasterRenderPass` (`builder.SetRenderAttachment`), mengeliminasi exception pada GUI repaints.
  - Sesuai instruksi pengguna (*"cuman buat memory jangan sentuh project game"*), seluruh file proyek game dibiarkan utuh dan perubahan hanya dilakukan pada arsip memori Obsidian Brain.

---
- **Catatan Memori:** [[Integrasi Skybox Eksternal dan Awan 3D]]
- **Ringkasan:** Mengarsipkan memori sesi integrasi aset eksternal pilihan (*Fantasy Skybox FREE* dan awan 3D *Quaternius Modular Platformer Pack*) ke dalam scene gameplay dan sistem atmosfer:
  1. **Audit & Seleksi Skybox Eksternal:** Mengaudit paket `Assets/Fantasy Skybox FREE/` dan `Assets/AllSkyFree/`, memilih preset `Assets/Fantasy Skybox FREE/Panoramics/FS003/FS003_Day.mat` (`Skybox/Panoramic`, tekstur 2048×1024) sebagai preset utama atmosfer `NeutralOcean`.
  2. **Runtime Instance & Modulasi 7 State (`BiomeAtmosphereController.cs`):** Mengganti pembuatan material shader polos lama dengan runtime instance `new Material(template)` (`Runtime_ExternalBiomeSkybox`) yang dibersihkan secara aman pada `OnDestroy()`; memodulasi `_Tint` dan `_Exposure` pada 7 state biome; menyelaraskan `RenderSettings.fogColor = #D2F2FA` dan `fogEndDistance` agar batas cakrawala laut (>2000m) menyatu mulus; mengaktifkan `AmbientMode.Trilight`; serta modulasi warna awan via `MaterialPropertyBlock`.
  3. **Penguncian Permanen di Lighting Settings:** Mengatur `RenderSettings.skybox = FS003_Day.mat` di scene `MainIsland_Gameplay.unity` & `Test_Dragon_Flight.unity` (serta generator skrip `Session4_VillageBuilder.cs` dan `IslandArchipelagoSetupUtility.cs`) sehingga Edit Mode dan Play Mode langsung menampilkan langit awan yang nyata.
  4. **Awan 3D Quaternius Transparan Rendah (`PF_StylizedClouds.prefab`):** Menggunakan mesh awan 3D `Cloud1..Cloud5.fbx` dari *Quaternius Modular Platformer Pack*, dikonfigurasi dengan material `Mat_StylizedCloud3D.mat` URP Lit Transparent (`#FFFDF8`, `alpha = 0.45`, `Smoothness = 0.08`, `renderQueue = 3000`), ketinggian diturunkan ke `Y = 115–230 m` (radius `850–1550 m`), bebas collider dan bayangan (`16` gugusan awan di scene).
  5. **Verifikasi Play Mode:** `VFXAtmosphereVerificationRunner` (10/10 checks PASS) dan `MomentumFlightVerificationRunner` (100% PASS), `0` compile errors, `0` console errors/warnings, 4 screenshot lingkungan tersimpan di `docs/figures/raw_screenshots/` (`SS_Skybox_NeutralOcean.png`, `SS_Skybox_ZephyriaCrisis.png`, `SS_Skybox_PyrewoodCrisis.png`, `SS_Skybox_SolariaRestored.png`), dan scene awal dikembalikan ke `MainMenu_Lobby.unity`.

---

## 2026-11-26 — Memori Sesi: Revisi VFX Skybox dan Bouncy Landing ([[Revisi VFX Skybox dan Bouncy Landing]])
- **Catatan Memori:** [[Revisi VFX Skybox dan Bouncy Landing]]
- **Ringkasan:** Mengarsipkan memori sesi integrasi artistik & penyempurnaan gameplay loop Pilar 1:
  1. **Revisi VFX Pilar 1 (10 Efek Effekseer + Dual TrailRenderer):** Mengompilasi 5 resep reworked + 3 baru (`VFX_Lake_MouthIntake`, `VFX_Solaria_ChestGemGlow` di tulang `Neck_01`, `VFX_Pyrewood_Wildfire`) + 2 eksisting tanpa error; koreksi inversi arah maju partikel `-Z`; penambahan sepasang `TrailRenderer` pada sayap naga (`wingtipBoneLength = 1.48f`).
  2. **Kamera Dinamis Cutscene & Orbit Naga:** Penambahan event camera dinamis saat restorasi desa (diagonal frontview/up-frontview/up-backview); perbaikan `LookOrbit` berpusat di tengah tubuh naga (`targetPosition`), bukan di belakang naga; penambahan slider sensitivitas rotasi kamera di UI Settings (`0.5x–2.5x`, default `1.5x`).
  3. **Penyempurnaan Bouncy vs Ground Landing:** Pemisahan permukaan tanah pijak (`normal.y >= 0.55f`) yang beralih ke `FlightMode.Ground` (reset momentum, wajib pose `#04 Takeoff` untuk terbang lagi) dengan tebing/dinding terjal (`normal.y < 0.55f`) yang memantul elastis (`TryApplyGlancingBounce()`).
  4. **Revisi Skybox Fantasi Stylized & Parallax Clouds:** Upgrade `DynamicBiomeSkybox.shader` (gradasi 4-stop, awan kumulus FBM dua warna dengan *silver lining*, awan sirus tinggi, piringan matahari); builder prefab `PF_StylizedClouds.prefab` (14 gugusan awan non-colliding); sinkronisasi 7-state atmosfer di `BiomeAtmosphereController.cs` dan penguncian skybox permanen di scene `MainIsland_Gameplay.unity` & `Test_Dragon_Flight.unity`.
  5. Seluruh suite verifikasi Play Mode lulus 100% (`MomentumFlightVerificationRunner` & `VFXAtmosphereVerificationRunner`), 4 screenshot lingkungan tersimpan di `docs/figures/raw_screenshots/`, dan scene awal aktif dikembalikan ke `MainMenu_Lobby.unity`.

---

## 2026-11-26 — Memori Sesi: Ketahanan Motion Blur Propeller Dash ([[Ketahanan Motion Blur Propeller Dash]])
- **Catatan Memori:** [[Ketahanan Motion Blur Propeller Dash]]
- **Ringkasan:** Mengatasi kendala gesture **`#14 Propeller Dash`** yang sulit terdeteksi akibat *camera motion blur* saat pergelangan tangan berputar cepat di depan webcam. Menambahkan pemulihan piksel *Elbow-Annulus Motion-Blur Recovery* (`recover_motion_blurred_wrists()` berbasis *spatial-gradient-weighted temporal frame difference* di `pose_stabilizer.py` & `active_cv_sender.py` saat confidence pergelangan drop `< 0.42` atau kolaps ke siku `< 0.16 * sw`), melonggarkan batas kecepatan One-Euro untuk pergelangan tangan terikat siku (`dist <= 0.98 * sw`, `max_step = 7.5 * sw_scale * dt`, `beta = 0.75`), serta menyempurnakan `PropellerRotationClassifier` di `motion_recognizer.py` dengan **Dual-Center Orbital Tracking** (siku + sentroid relatif 10 frame ber-gate *2D circularity* anti-1D-flap), *spin-direction phase unwrapping* lintas 1–3 frame blur (`125°–245°`), debounce pembalikan arah 2–3 frame beruntun (mencegah reset oleh 1-frame *blur streak*), dan toleransi *elbow-spread grace* 4 frame. Seluruh 8 test suite Python CV lulus 100% tanpa regresi pada 6 pose terkunci maupun gerakan kepakan 1D.

---

## 2026-11-26 — Memori Sesi: Pengemasan Aset VFX Laptop B ([[Pengemasan Aset VFX Laptop B]])
- **Catatan Memori:** [[Pengemasan Aset VFX Laptop B]]
- **Ringkasan:** Mengumpulkan, menata, dan mengompresi seluruh aset Efek Visual (VFX) aktif proyek Unity *Fanamir: The Sky Guardian* ke dalam arsip ZIP lokal di `assets/Export_VFX_LaptopB.zip` (`869.790 byte` / `849,4 KB`, `97` berkas, uncompressed `1.423.513 byte`) untuk disalin dan diuji coba pada Three.js Studio Laptop A. Paket mencakup 7 berkas partikel `.efkefc` + subfolder `resources/` (14 tekstur PNG), 7 partikel runtime `.efk`, 7 skrip generator Python `.py` + `recipes/` + `briefs/` + `materials/` + `vfxkit/` + `vfx_manifest.json`, skrip Unity (`DragonVfxController.cs`, `VillageZoneController.cs`, `EffekseerNormalTextureGuard.cs`, `BiomeAtmosphereController.cs`, `Session4_VillageBuilder.cs`), 7 serialized `.asset` + `EffekseerSettings.asset`, material/shader (`Mat_ZoneRing_S4.mat`, `Mat_DynamicBiomeSkybox.mat`, `DynamicBiomeSkybox.shader`), serta panduan komprehensif `README_VFX.txt` (sistem koordinat, hierarki rig, tulang jangkar, offset runtime, skala emitter, dan pemicu gameplay). Folder sementara telah dibersihkan dan berkas ZIP tidak di-push ke git agar ukuran repo tetap ramping.

---

## 2026-11-26 — Memori Sesi: Sistem Audio Terpadu Fanamir — Dual BGM & Quick Switcher ([[Implementasi Sistem Audio Fanamir]])
- **Catatan Memori:** [[Implementasi Sistem Audio Fanamir]]
- **Ringkasan:** Mengimplementasikan pembaruan lengkap Sistem Audio Terpadu Fanamir: re-sinkronisasi **37 berkas audio** (`14` BGM `.mp3` + `23` SFX `.wav`, termasuk `SFX_PropellerDash_Loop.wav` Heavy Aerodynamic Whoosh Loop `176,444` bytes & `SFX_Splash_LogoReveal.wav`) dari `assets/Export_Audio_LaptopB.zip`; penggantian `BgmThemeStyle` dengan **Dual BGM Selection persisten** (`Fanamir_BgmFlightIndex` `[0=Adaptif, 1..11=BGM 1..11]` & `Fanamir_BgmMenuIndex` `[0..5=BGM Menu 1..6]`) di `DragonGameSettings.cs` & `AudioManager.cs`; penambahan dua `SolidCameraDropdown` (`Row_BgmFlightSelection` & `Row_BgmMenuSelection`) pada `PF_SettingsPanel.prefab` (`SettingsCard` `960×656`) & `SettingsPanelUI.cs`; penambahan pill pemilih cepat `SharedTopNavbar/NavBgmMenuPill` (`[🎵 BGM Menu: BGM 1 ▼]`, `198×46 px` di `(-442, 0)`) di `LobbyNavigationManager.cs` (`SCREEN 00`); integrasi `SFX_Splash_LogoReveal.wav` + `PlayMenuBGM` & lencana `"MADE WITH UNITY"` pada `SCREEN 09` (`BootFlowController.cs`); penghapusan seluruh `// TODO [SYNC-AUDIO]`; serta verifikasi Play Mode (`MainMenu_Lobby.unity` `37/37` & `MainIsland_Gameplay.unity` `MomentumFlightVerificationRunner` 100% PASS, `0` errors/warnings).

---

## 2026-11-26 — Memori Sesi: Perbaikan UI Settings dan Pose Guide
- **Catatan Memori:** [[Perbaikan UI Settings dan Pose Guide]]
- **Ringkasan:** Mengarsipkan memori sesi penghapusan section `CAMERA INPUT & AI YOLO` dari `PF_SettingsPanel.prefab` & `SettingsPanelUI.cs` (mengarahkan `navCamBadgeButton` ke `LobbyTab.Calibration` di `LobbyNavigationManager.cs`), penyesuaian gap bawah `Btn_ApplyAndClose` (`SettingsCard` `960×520`, `y = 42`) & `Btn_ClosePoseGuide` (`ModalCard` `1848×854`, `y = 48`), serta pelebaran margin dalam `26 px` (kiri/atas/kanan) & `24 px` (bawah) pada `PoseGuideCard_01..15` (`PF_PoseGuideOverlay.prefab` + `PoseGuideOverlayUI.cs`) dan `RecentPoseSlot_1..4` (`PF_CalibrationPanel.prefab`) agar bebas dari bingkai `14..18 px` state `ACTIVE`.

## 2026-11-26 — Memori Sesi: Animasi Tubuh dan Kaki Naga
- **Catatan Memori:** [[Animasi Tubuh dan Kaki Naga]]
- **Ringkasan:** Mengarsipkan memori sesi implementasi `DragonBodyAnimator.cs` (`[DefaultExecutionOrder(55)]` pada `/Dragon_Player` untuk rahang `MOUTH_OPEN` + *Victory Roar*, liukan ekor `15/30/55/75/100%` + *undulation*, *Spine Arch*, dan transisi kaki) serta pembaruan **Tahap 1** (*Flight Tuck* aerodinamis kaki depan/belakang, elevasi dada hover $+16^\circ$, *buoyant bobbing* `0.40 Hz / 0.18 m`, `DragonAnimationVerificationRunner`, 4 screenshot mandiri, dan pengembalian scene aktif ke `MainMenu_Lobby.unity`).

## 2026-11-26 — Diagnosa Error `UnityConnectWebRequestException: Token Exchange failed` ([[Diagnosa Error UnityConnect Token Exchange]])

- Memeriksa Console & status Unity Editor (`6000.3.7f1`) melalui Unity MCP serta `ProjectSettings/UnityConnectSettings.asset` (`m_Enabled: 0` untuk seluruh layanan Unity Services).
- Mengonfirmasi bahwa error `UnityEditor.Connect.UnityConnect.RequestNewServiceToken()` berasal dari proses *background token refresh* internal Unity Editor (jaringan/VPN atau sesi login Unity Hub kedaluwarsa), bukan error kompilasi maupun bug kode proyek (`compilationFailed: false`), serta mendokumentasikan langkah pemulihannya (re-login Unity Hub / Clear Console / Sign out akun Editor).

---

## 2026-11-26 — Memori Sesi: Isolasi Narasi dan Side Challenge HUD
- **Catatan Memori:** [[Isolasi Narasi dan Side Challenge HUD]]
- **Ringkasan:** Mengarsipkan memori sesi implementasi Full Narrative Isolation Mode (`PF_QuestNarrativeBox.prefab` `1440×300 px`, hanya `QuestObjectivePanel` & `PF_QuestNarrativeBox` yang tampil + sembunyikan billboard 3D) dan redesain Side-Docked Challenge HUD (`PF_VillageChallengeOverlay.prefab` `400×620 px` *Middle-Right* `(-30, 0)`, `VillageChallengeUI.cs`, `InGameFlightHUD.SetVillageChallengeMode`/`RestoreNormalFlightHUD`, & sinkronisasi `VillageZoneController.cs`) beserta verifikasi Play Mode `Session4VerificationRunner` & `Session5GameLoopVerificationRunner` (`100% PASS`).

## 2026-11-26 — Memori Sesi: Penyempurnaan Kontrol Terbang dan FlyUp
- **Catatan Memori:** [[Penyempurnaan Kontrol Terbang dan FlyUp]]
- **Ringkasan:** Mengarsipkan memori sesi audit & penyempurnaan mekanisme kontrol (`#07 Fly Up` anti-hijack `#11` & histeresis, proteksi `#06`/`#06B` dari legacy `chest_elevation`, pembalikan `flap_power` simetris, transisi `#09 -> #10`, responsivitas One-Euro `pose_stabilizer.py`, padding atas `+28%` `HybridPoseTracker`, *sliding auto-baseline* `MouthTracker`, `_takeoffFlapTimer` `DragonFlightController.cs`, dan normalisasi `#15C` `UDPPoseReceiver.cs`), terverifikasi 7/7 suite Python CV + `MomentumFlightVerificationRunner` 100% PASS.

## 2026-11-26 — Memori Sesi: Stabilisasi Kontrol Lintas Scene dan Dash
- **Catatan Memori:** [[Stabilisasi Kontrol Lintas Scene dan Dash]]
- **Ringkasan:** Mengarsipkan memori sesi stabilisasi urutan scene menu (`MainMenu_Lobby.unity` Build Index `0`), fallback keyboard (`enableKeyboardFallback`, `SIO_UDP_CONNRESET`, `IsKeyboardOverrideActive`), gerbang wajib kalibrasi (`DragonGameSettings.IsCameraCalibrated` & `OnCalibrationStatusChanged`), persistent shared UDP `5052` socket (`UDPPoseReceiver.cs` & `[DragonSessionHost]`), penghapusan warning RenderGraph + duplikasi `VFX_Sockets` + kilau VFX takeoff (`EffekseerSettings.asset`, `DragonVfxController.cs`, `VFX_Flight_WingtipTrails.py`, `VFX_Flight_SonicDashShockwave.py`), serta penyetelan ulang sensitivitas bilateral `#14 Propeller Dash` (`motion_recognizer.py`, `pose_stabilizer.py`, `cv_locomotion_config.json`).

## 2026-11-26 — Memori Sesi: Timer Stage dan CV Hibrida MediaPipe
- **Catatan Memori:** [[Timer Stage dan CV Hibrida MediaPipe]]
- **Ringkasan:** Mengarsipkan memori sesi perpanjangan timer stage Unity (`300s`/`600s`/`900s` + kartu Lobby `5/10/15 MIN`) dan pembangunan arsitektur hibrida Computer Vision Python (`hybrid_pose_tracker.py` `HybridPoseTracker` YOLOv8n-pose + MediaPipe `PoseLandmarker` `pose_landmarker_lite.task` pada ROI `+12%`, integrasi `active_cv_sender.py`, serta penyetelan One-Euro `0.40 Hz / 0.12` & *Kinematic Forearm Clamping* `1.10×` di `pose_stabilizer.py`).

## 2026-11-26 — Memori Sesi: Perbaikan Kemudi Kanan dan Finish UI Stage
- **Catatan Memori:** [[Perbaikan Kemudi Kanan dan Finish UI Stage]]
- **Ringkasan:** Mengarsipkan memori sesi audit & perbaikan bug kemudi naga stuck belok kanan terus akibat `ARDragonSkyGuardian_CV/player_profile.json` korup (`neutral_lean_bias_deg = -22.856°`) + penjaga validasi `player_calibrator.py`/`motion_recognizer.py`/`active_cv_sender.py`, pemisahan tantangan per level (`Stage 1/2/3 = 1/2/3 Desa`) lewat default fallback & `SubsystemRegistration` `DragonGameSettings.cs` + `CalibrationPanelUI.cs` + `VillageZoneController.cs`, serta integrasi `FinishModalUI` (`PF_FinishModal`) + `NEXT LEVEL` pasca-penyelesaian misi.

## 2026-11-26 — Memori Sesi: Arsip dan Push Memori Laptop B
- **Catatan Memori:** [[Arsip dan Push Memori Laptop B]]
- **Ringkasan:** Mengarsipkan memori sesi sinkronisasi dan *push* catatan memori Laptop B ke `ARDragon-Brain` (`origin main`, commit `950f2fb` untuk 4 memori: Momentum v4, Sesi 4, Sesi 4B, & Sesi 5 beserta pembaruan `[[Tasks]]` dan `[[Devlog]]`).

## 2026-11-26 — Memori Sesi: Ketahanan Motion Blur Propeller Dash
- **Catatan Memori:** [[Ketahanan Motion Blur Propeller Dash]]
- **Ringkasan:** Mengarsipkan memori sesi perbaikan deteksi `#14 Propeller Dash` terhadap *camera motion blur* (`recover_motion_blurred_wrists()`, pelonggaran *velocity outlier gate* & *confidence* lengan bawah di `RobustKeypointTracker`, serta *Dual-Center Orbital Tracking*, *phase unwrapping*, dan *blur-streak debounce* di `PropellerRotationClassifier`), terverifikasi 100% PASS pada seluruh suite Python CV.

## 2026-11-26 — Integrasi Skybox Eksternal (*Fantasy Skybox FREE* `FS003_Day`) & Awan 3D *Quaternius* (`PF_StylizedClouds`, `BiomeAtmosphereController.cs`, `Session4_VillageBuilder.cs`, `IslandArchipelagoSetupUtility.cs`, `[[laptop-a/handoff-laptop-b/prompt_eksekusi_integrasi_skybox_dan_awan_eksternal_laptop_b]]`)

- **Audit & Pemilihan Aset Eksternal (`Assets/`):**
  - Mengimpor paket Unity Asset Store **`Fantasy Skybox FREE`** (`Assets/Fantasy Skybox FREE/`) dan **`AllSky Free - 10 Sky Skybox Set`** (`Assets/AllSkyFree/`), serta mengekstrak `Cloud1.fbx..Cloud5.fbx` dari **Quaternius Modular Platformer Pack** (`assets/Platformer Pack - Nov 2018-20261126T071637Z-1-001.zip`) ke `Assets/Assets 3D/Clouds/`.
  - Memilih **`Assets/Fantasy Skybox FREE/Panoramics/FS003/FS003_Day.mat`** (`Shader: Skybox/Panoramic`, tekstur `2048×1024`, properti `_Tint`, `_Exposure`, `_Rotation`, `_MainTex`) sebagai template utama **`NeutralOcean`** karena memiliki langit biru fantasi cerah berawan kumulus & cirrus paling tajam dibanding `AllSkyFree/Cartoon Base BlueSky` (polos tanpa awan) maupun `FS000_Day_*` (`512×512`), sekaligus mendukung interpolasi `_Tint` & `_Exposure` mulus tanpa pop visual lintas 7 state biome.
- **Perubahan yang Diimplementasikan:**
  - **`BiomeAtmosphereController.cs`:** Menghapus fallback paksa ke shader lama `DragonWingAR/DynamicBiomeSkybox` di `InitRuntimeSkybox()`. Kini memakai `new Material(template)` (`Runtime_ExternalBiomeSkybox`, dibersihkan di `OnDestroy()`), memodulasi `_Tint` / `_TintColor` & `_Exposure` pada 7 state (`NeutralOcean`, `ZephyriaCrisis`/`Restored`, `PyrewoodCrisis`/`Restored`, `SolariaCrisis`/`Restored`), menyelaraskan `RenderSettings.fogColor` (`#D2F2FA` untuk `NeutralOcean`) dengan cakrawala bawah `FS003_Day`, menyinkronkan `AmbientMode.Trilight`, serta memodulasi tint awan 3D via `MaterialPropertyBlock` (tanpa mengubah aset `.mat` di disk).
  - **Lighting Permanen Scene (`Session4_VillageBuilder.cs`, `IslandArchipelagoSetupUtility.cs`):** Memasang `FS003_Day.mat` permanen pada `RenderSettings.skybox` di `MainIsland_Gameplay.unity` dan `Test_Dragon_Flight.unity` agar langsung tampil sejak Edit Mode dan tidak tertimpa balik saat builder dijalankan ulang.
  - **Awan 3D Quaternius (`PF_StylizedClouds.prefab`, `Mat_StylizedCloud3D.mat`):** Merakit `16` gugusan awan dari `Cloud1.fbx..Cloud5.fbx` (lebar terukur `~135–235 m`, radius `850–1550 m`, ketinggian diturunkan ke `Y = 115–230 m` agar sejajar lanskap pulau & horizon, tanpa collider, `shadowCastingMode = Off`, `receiveShadows = false`) dengan material `Universal Render Pipeline/Lit` **Transparent** (`_Surface = 1`, `_ZWrite = 1`, `SrcAlpha`/`OneMinusSrcAlpha`, `renderQueue = 3000`, `_EmissionColor = black`, matte putih-krem `#FFFDF8` `alpha = 0.45`, `Smoothness = 0.08`, disinkronkan dengan `stylizedCloudAlpha = 0.45f` di `BiomeAtmosphereController.cs`).
  - **`VFXAtmosphereVerificationRunner.cs`:** Memperbarui assertion agar memverifikasi `Runtime_ExternalBiomeSkybox` (bukan `DynamicBiomeSkybox`) & `PF_StylizedClouds` (`12..18` anak tanpa collider) sekaligus menangkap 4 screenshot bukti visual.
- **Hasil Verifikasi Play Mode (`MainIsland_Gameplay.unity`, `0` Errors, `0` Warnings):**
  - `VFXAtmosphereVerificationRunner`: **`ALL PILAR 1 & SESI 4B VFX (10/10), TRAIL, SOCKET & BIOME CHECKS PASSED (100%)`** (`Glide=(fov=64.99,z=-6.30)`, `Dive=(fov=78.87,z=-7.20)`, `Dash=(fov=85.00,z=-7.50)`, `Hover=(fov=60.01)`, `HeadSweep=23.3deg`, `Wildfire=2.60->1.66->0`, `ZephCrisisFog=231m->RestoredFog=3797m`).
  - `MomentumFlightVerificationRunner`: **`ALL MOMENTUM & FLIGHT PHYSICS CHECKS PASSED (100%)`**.
  - 4 screenshot terverifikasi di `docs/figures/raw_screenshots/`: `SS_Skybox_NeutralOcean.png`, `SS_Skybox_ZephyriaCrisis.png`, `SS_Skybox_PyrewoodCrisis.png`, & `SS_Skybox_SolariaRestored.png`, lalu scene aktif dikembalikan ke `MainMenu_Lobby.unity` (Build Index `0`).

---

## 2026-11-26 — Revisi Skybox Fantasi *Stylized* (Ghibli/Genshin Style), Awan 3D Parallax (`PF_StylizedClouds`), & Pemasangan Permanen Scene (`DynamicBiomeSkybox.shader`, `Mat_DynamicBiomeSkybox.mat`, `BiomeAtmosphereController.cs`, `Session4_VillageBuilder.cs`, `[[laptop-a/visual-vfx/spesifikasi_revisi_skybox_stylized]]`)

- **Akar Masalah Sebelumnya:**
  - `Fbm()` pada shader lama memiliki distribusi pusat `0.45–0.55` sehingga rumus ambang `1.0 - _CloudCoverage * 0.75` (`0.715` pada `NeutralOcean`) hampir tidak memunculkan awan sama sekali, tidak memisahkan `_CloudLitColor`/`_CloudShadowColor` maupun *silver lining*, belum memiliki lapisan *cirrus*, dan `Mat_DynamicBiomeSkybox.mat` belum terpasang di semua scene secara permanen dengan default *NeutralOcean* penuh.
- **Perubahan yang Diimplementasikan:**
  - **`Assets/Shaders/DynamicBiomeSkybox.shader`:** Menambahkan gradasi 4-stop (`_ZenithColor`, `_MidSkyColor`, `_HorizonColor` terkunci `100%` ke `RenderSettings.fogColor` di pita horizon agar bebas patahan laut, serta `_NadirColor` `#184E66`), *domain-warped procedural FBM* berbatas cel-soft (`_CloudSoftness = 0.045`) untuk awan kumulus bervolume (`_CloudLitColor`, `_CloudShadowColor`, *directional self-shadow*, & *silver lining* arah matahari), awan *cirrus* tipis atas (`_CirrusCoverage`), serta piringan matahari & halo (`_SunDiscSize`, `_SunHaloIntensity`, fallback `_MainLightPosition`) sambil menjaga properti lama (`_SkyTint`, `_CloudTint`, `_SunSize`, `_GroundColor`).
  - **`Assets/Materials/Mat_DynamicBiomeSkybox.mat` & `BiomeAtmosphereController.cs`:** Mengisi palet penuh `NeutralOcean` di disk (terlihat langsung di Edit Mode), menyelaraskan 7 state (`NeutralOcean`, `ZephyriaCrisis`/`Restored`, `PyrewoodCrisis`/`Restored`, `SolariaCrisis`/`Restored`) bersama `AmbientMode.Trilight` sinkron, isolasi runtime material via `new Material(baseSkyboxTemplate)` (`Destroy` di `OnDestroy()`), serta rotasi perlahan `stylizedCloudRing` (`0.3°/dtk`) yang otomatis nonaktif saat kabut krisis pekat (`fogEndDistance < 700m`) agar tidak meninggalkan siluet poligon di depan awan gelap krisis.
  - **`PF_StylizedClouds` & Lighting Scene (`Session4_VillageBuilder.cs`, `IslandArchipelagoSetupUtility.cs`):** Membuat prefab `Assets/Prefabs/World/PF_StylizedClouds.prefab` (`14` gugusan / `84` puff bebas collider & bebas bayangan pada radius `980–1620 m`, `Y = 305–515 m`) ber-material `Mat_StylizedCloud3D.mat`, serta memasang `Mat_DynamicBiomeSkybox.mat` permanen pada `RenderSettings.skybox` di `MainIsland_Gameplay.unity` dan `Test_Dragon_Flight.unity` (dan memperbarui `VFXAtmosphereVerificationRunner.cs` memakai `fc.CurrentCameraOffsetZ` agar kompatibel dengan kamera orbit center naga).
- **Hasil Verifikasi Play Mode (`MainIsland_Gameplay.unity`, `0` Errors):**
  - `VFXAtmosphereVerificationRunner`: **`ALL PILAR 1 & SESI 4B VFX (10/10), TRAIL, SOCKET & BIOME CHECKS PASSED (100%)`** (`Glide=(fov=64.99,z=-6.30)`, `Dive=(fov=78.88,z=-7.20)`, `Dash=(fov=85.00,z=-7.50)`, `HeadSweep=23.3deg`, `Wildfire=2.60->1.65->0`, `ZephCrisisFog=232m->RestoredFog=3797m`).
  - Non-regresi `MomentumFlightVerificationRunner`: **`ALL MOMENTUM & FLIGHT PHYSICS CHECKS PASSED (100%)`**.
  - 4 screenshot perbandingan skybox terverifikasi di `docs/figures/raw_screenshots/`: `SS_Skybox_NeutralOcean.png` (`3800m`, `clouds3D=True`), `SS_Skybox_ZephyriaCrisis.png` (`174m`, `clouds3D=False`), `SS_Skybox_PyrewoodCrisis.png` (`259m`, `ash=True`, `clouds3D=False`), dan `SS_Skybox_SolariaRestored.png` (`3968m`, `clouds3D=True`), lalu scene aktif dikembalikan ke `MainMenu_Lobby.unity`.

---

## 2026-11-26 — Perbaikan Logika Ground Landing vs Steep Collider Bounce (`DragonFlightController.cs`)

- **Akar Masalah Sebelumnya:**
  1. `OnCollisionEnter()` memanggil `ApplyGlancingBounce()` tanpa memeriksa sudut permukaan (`normal.y`), dan `TryHandleGlancingCollisionSweep()` memantulkan kontak apa pun apabila `isAttemptingLanding == false` (misalnya saat `TuckDive`, *stall* `FlyUp`, atau kepala menengadah `bodyPitchTrim > 0.12f`).
  2. Ketika jatuh/menukik menyentuh permukaan darat saat `!isAttemptingLanding`, langkah 5 `HandleAirborneState()` hanya meng-clamp ketinggian di udara (`_isGrounded` tetap `false`) serta `groundAvoidance` mendorong vertikal `+1.5f`, sehingga naga terus melayang/terpantul di udara alih-alih masuk **Ground Mode**.
- **Perbaikan yang Diimplementasikan:**
  - Menambahkan ambang kemiringan `public float bounceMaxNormalY = 0.55f;` (`~56.6°`) dan helper `IsSteepBounceSurface(Vector3 normal)`, `TryGetGroundSurface(..., out float, out Vector3)` (`QueryTriggerInteraction.Ignore`), serta `LandOnGround(float surfaceY, string reason)` yang menyetel `_isGrounded = true`, mereset `_currentMomentum = 0f`, `_currentVerticalVelocity = 0f`, `_takeoffFlapTimer = 0f`, serta seluruh flag pose udara sehingga pemain harus melakukan pose **`#04 Takeoff`** (`FlapPower >= takeoffFlapThreshold` selama `>= takeoffMinDuration`) untuk terbang kembali.
  - Membatasi `groundAvoidance` hanya untuk penerbangan skim aktif ke depan (`isActiveSkimBoost`) dan mengizinkan pendaratan ke Ground Mode untuk segala kondisi turun/jatuh non-climbing (`!_isTakingOff && _currentVerticalVelocity <= 0.15f`) selama `landedSurfaceNormal.y > bounceMaxNormalY`.
  - Memperbarui `TryHandleGlancingCollisionSweep(Vector3)` (memverifikasi overlap awal `hit.distance <= 0.001f` dengan `hit.collider.Raycast` agar tidak menghasilkan normal palsu `-moveDir`) dan `OnCollisionEnter(Collision)` (merata-ratakan normal kontak) sehingga pantulan `ApplyGlancingBounce()` **hanya** aktif pada collider vertikal/miring ekstrem (`normal.y <= 0.55f`).
- **Hasil Verifikasi Play Mode (`MainIsland_Gameplay.unity`, `0` Errors, `0` Warnings):**
  - Pendaratan darat datar & lereng `40°`: `T1_GlideLand=PASS`, `T3_DiveLand=PASS`, `T4_StallLand=PASS`, `T5A_40DegLand=PASS` (`_isGrounded = true`, `_currentMomentum = 0.00`, tanpa bounce).
  - Wajib Takeoff ulang (`T2_RequiresTakeoff=PASS`): `Glide`, `FlyUp` Y-pose, `TuckDive`, dan `PropellerDash` tetap grounded hingga `#04 Takeoff`.
  - Pantulan tebing/dinding curam: dinding vertikal `90°` (`T5B_VertBounce=PASS`, `mom=0.60 -> 0.30`, `yaw=180.0°`) & lereng curam `70°` (`T5C_70DegBounce=PASS`, `mom=0.30`).
  - Non-regresi `MomentumFlightVerificationRunner`: **`ALL MOMENTUM & FLIGHT PHYSICS CHECKS PASSED (100%)`**, lalu scene aktif dikembalikan ke `MainMenu_Lobby.unity`.

---

## 2026-11-26 — Peningkatan Kamera Orbit Berpusat di Center Naga & Slider Settings `Camera Look Rotation` (`DragonFlightController.cs`, `DragonGameSettings.cs`, `SettingsPanelUI.cs`, `UISceneAndPrefabBuilder.cs`)

- **Perubahan yang Diimplementasikan:**
  - Mengganti rotasi kamera in-place di belakang naga (`cameraPitchFollowFactor = 0.25f`, `cameraYawPeekFactor = 0.11f`, `~3.8°`) menjadi **kamera orbit penuh berpusat di center tubuh naga** (`cameraOrbitPivot = (0f, 0.45f, 0f)`) pada `DragonFlightController.UpdateCameraPolish()` (`cameraOrbitHeadPitchFactor = 0.72f`, `cameraOrbitFlightPitchFactor = 0.40f`, `cameraOrbitYawFactor = 0.70f`, maks `cameraOrbitMaxYawDeg = 65f`, `cameraOrbitMaxPitchUpDeg = 48f`, `cameraOrbitMaxPitchDownDeg = 48f`, `cameraOrbitGroundedMaxPitchUpDeg = 32f`, `SmoothDamp 0.18s`, roll horizon `0°`).
  - Menambahkan pengaturan persisten `DragonGameSettings.CameraLookRotationScale` (`PrefCameraLookScale = "Fanamir_CameraLookScale"`, `0.5f–2.5f`, default **`1.5f`**) yang dikalikan langsung ke jangkauan orbit horizontal & vertikal kamera secara live.
  - Menambahkan baris slider **`Camera Look Rotation`** (`Row_CameraLookScale`, `0.5x–2.5x`, default `1.5x`) di `SettingsPanelUI.cs` & `UISceneAndPrefabBuilder.BuildSettingsPanelPrefab()` (`SettingsCard` `960×778 px`, `AudioAndFlightSection` `880×558 px`), lalu menjalankan `BuildAll()` dan `Session4_VillageBuilder.BuildVillageZonesAndMarkers()`.
- **Hasil Verifikasi Play Mode (`Unity 6000.3.7f1`, `0` Errors, `0` Warnings):**
  - `MainMenu_Lobby.unity`: slider `Row_CameraLookScale` tampil rapi (`1.5x` -> uji live `2.2x`, `SS_Settings_CameraLookRotation.png`).
  - `MainIsland_Gameplay.unity`: `yaw@1.5x = 36.7°`, `yaw@2.5x = 61.2°` (`pos=(-5.47, 2.69, -3.00)`), `euler@2.5xUp = (326.02, 0.04, 0.00)`, serta `MomentumFlightVerificationRunner`: **`ALL MOMENTUM & FLIGHT PHYSICS CHECKS PASSED (100%)`**, `consoleErrors=0`, `consoleWarnings=0`.
  - Screenshot (`docs/figures/raw_screenshots/`): `SS_CamOrbit_LookRight.png`, `SS_CamOrbit_LookUp.png`, `SS_Settings_CameraLookRotation.png`.

---

## 2026-11-26 (Hotfix Visual) — Perbaikan Arah VFX Zephyria/Pyrewood (Kompensasi Z-Flip Effekseer) & Kamera Dinamis Event Tantangan Desa

- **Akar Masalah yang Ditemukan lewat Inspeksi Screenshot:**
  1. `EffekseerSettings.asset` memakai `isRightEffekseerHandledCoordinateSystem = 0` (koordinat Left-Handed Unity), sehingga Effekseer membalik sumbu `+Z_effekseer` menjadi `-transform.forward` Unity. Akibatnya, `VFX_Pyrewood_WaterBreath`, `VFX_Zephyria_WindGust`, dan `VFX_Lake_MouthIntake` menyembur/menghadap ke belakang (`-Z`) bukan ke depan.
  2. `Wind shear lines` pada `VFX_Zephyria_WindGust.py` memakai `billboard='DirectionalBillboard'` dengan `travel` (`Easing`), yang pada `EffekseerForUnity 1.80.8` fallback berdiri vertikal (`Billboard = 0`).
- **Perbaikan yang Diimplementasikan:**
  - **`DragonVfxController.cs` & `Session4_VillageBuilder.cs`:** Mengarahkan `-transform.forward` ke target arah depan untuk `waterBreathEmitter` (ditambah clamp `mouthForward.y >= 0.04f` agar tidak menukik ke bawah), `mouthIntakeEmitter` (`Quaternion.LookRotation(-Vector3.up, Vector3.forward)`), dan `PlayWindGust()` (memancarkan angin dinamis ke depan naga pada kipas horizontal `±28°` & sudut naik `+3°..+15°` tanpa pernah mengarah ke bawah).
  - **`VFX_Zephyria_WindGust.py`:** Mengubah `Wind pivot` / eddies / motes / mist agar tidak bias turun (`y >= 0`), mengganti `Wind shear lines` menjadi `billboard='Fixed'` (`rotation=[[82,98], [-14,14], [-10,10]]`) dengan `PVA` `velocity` ke `+Z_effekseer`, lalu `vfx lint` (`0` errors, `0` warnings) + `vfx build` dan `AssetDatabase.Refresh()`.
  - **`DragonFlightController.cs` & `VillageZoneController.cs`:** Menambahkan enum `VillageCameraMode` (`Default`, `DiagonalFront` `(4.6, 1.85, 6.8)`, `DiagonalUpFront` `(4.8, 4.6, 6.8)`, `DiagonalUpBack` `(4.2, 4.4, -6.4)`) dengan transisi `Vector3.SmoothDamp` + `Quaternion.Slerp` (`0.42s`) khusus saat tantangan desa aktif (tidak mengubah kamera saat narasi textbox): `ZephyriaChallenge` & `PyrewoodLake` $\rightarrow$ `DiagonalFront`, `SolariaChallenge` $\rightarrow$ `DiagonalUpFront`, dan `PyrewoodForest` $\rightarrow$ `DiagonalUpBack`.
- **Hasil Verifikasi Play Mode (`Unity 6000.3.7f1`, `0` Errors, `0` Warnings):**
  - `VFXAtmosphereVerificationRunner`: **`ALL PILAR 1 & SESI 4B VFX (10/10), TRAIL, SOCKET & BIOME CHECKS PASSED (100%)`** (`HeadSweep=23.2deg`, arah `WaterBreath` & `WindGust` ke depan/atas, seluruh `4` sudut kamera event desa + `Default` narasi terverifikasi, & inspeksi ulang screenshot `SS_Sesi4B_Zephyria_SmogAndWindGust.png` / `SS_Pilar1_Lake_Siphon_MouthIntake.png` / `SS_Sesi4B_Solaria_CelestialDewRain.png` / `SS_Sesi4B_Pyrewood_WaterBreath_Steam.png` bersih).
  - Non-regresi `MomentumFlightVerificationRunner`: **`100% PASS`**, dan scene aktif dikembalikan ke `MainMenu_Lobby.unity`.

---

## 2026-11-26 — Eksekusi Revisi VFX Pilar 1: 5 VFX Reworked, 3 VFX Baru, Socket `Neck_01`/`Head`/`Wingtip` + `TrailRenderer`, & Efek Desa (`[[laptop-a/visual-vfx/spesifikasi_revisi_vfx_pilar_1]]`, `[[laptop-a/handoff-laptop-b/prompt_eksekusi_revisi_vfx_pilar_1_laptop_b]]`)

- **Langkah 1 — Impor Resep Python & Kompilasi 10 Aset Effekseer (`vfx/`, `Assets/VFX/Effekseer/`):**
  - Mengekstrak `8` skrip `.py` (`Effekseer_Python_Sources/src/*.py` -> `vfx/src/`) dan `8` brief (`Effekseer_Python_Sources/briefs/*.json` -> `vfx/briefs/`) dari `Export_VFX_Revisi_LaptopB.zip` tanpa menimpa `vfx/textures/processed/*.png` (`14` tekstur `T_*.png` tetap) serta mempertahankan `VFX_Pyrewood_WaterBreath.py` & `VFX_Pyrewood_ExtinguishSteam.py`.
  - Menjalankan `vfx brief-validate` (`10/10` ok), `vfx lint` (`0` errors), dan `vfx build` (`10/10` `.efkefc` + `.efk` berhasil dibangun), menyalin `10` `.efkefc` ke `Assets/VFX/Effekseer/`, lalu memanggil `AssetDatabase.Refresh()` sehingga seluruh `10/10` `EffekseerEffectAsset` (`.asset`) siap pakai termasuk `VFX_Lake_MouthIntake.asset`, `VFX_Solaria_ChestGemGlow.asset`, dan `VFX_Pyrewood_Wildfire.asset`.
  - Memastikan `EffekseerSettings.asset` mempertahankan `RendererType: 1` (`EffekseerRendererType.Unity`), `MaintainGammaColorInLinearSpace: 1`, dan `enableDepth: 0`.
- **Langkah 2 — Perbaikan Socket, Pita Sayap `TrailRenderer`, & Emitter Baru (`DragonVfxController.cs`):**
  - Menurunkan `wingtipBoneLength` dari `1.95f` $\rightarrow$ **`1.48f`** (`1.1866 * 1.25`) dan menambahkan `wingtipLeftTrail` & `wingtipRightTrail` (`TrailRenderer`: `time = 0.42f`, `minVertexDistance = 0.08f`, `startWidth = 0.16f`, `endWidth = 0.01f`, gradasi cyan-putih `(220,250,255,200)` $\rightarrow$ `(110,225,255,0)`, `shadowCastingMode = Off`, material `Assets/Materials/Mat_WingtipTrail_Additive.mat` shader `Universal Render Pipeline/Particles/Unlit` Additive).
  - Memperbaiki rotasi `waterBreathEmitter` di `SyncSocketTransforms()` agar diposisikan di `mouthBoneOverride.TransformPoint(0, 0.34, -0.04)` dan diarahkan ke `mouthBoneOverride.TransformDirection(Vector3.up)` sehingga semburan air ikut menyapu saat kepala `Head` menoleh/menunduk.
  - Menambahkan `mouthIntakeEmitter` (`SetWaterIntakeActive(bool)` terpanggil bersama `SetWaterSiphonActive(bool)`), `chestGemEmitter` (`SetChestGemGlowActive(bool)`), serta mengubah `sonicDashEmitter` di `LateUpdate()` menjadi `SetSonicDashActive(isDashing)` (looping wind tunnel).
- **Langkah 3 & 4 — Efek Desa & Rebuild Scene (`VillageZoneController.cs`, `Session4_VillageBuilder.cs`, `MainIsland_Gameplay.unity`):**
  - Menambahkan `pyrewoodWildfireEmitters` (`5` titik `VFX_Pyrewood_Wildfire` di sekitar `SubMarker_HutanTerbakar` ter-raycast ke terrain, skala `2.3f–2.8f`, `playOnStart = true`, `isLooping = true`) dengan `UpdatePyrewoodWildfireVisuals()` yang menyala sejak stage dimulai bila Pyrewood aktif, menyusut halus `Mathf.Lerp(1.0f, 0.0f, _pyrewoodFireProgress)` saat disembur air, lalu `StopRoot()` & `localScale = Vector3.zero` ketika `Restored`.
  - Memperpanjang hujan embun Solaria pasca-restorasi menjadi `solariaPostRestoreRainDuration = 14.0f` detik (`_solariaPostRestoreRainTimer`, menjaga `solariaDewRainEmitter` + `chestGemEmitter` + `SetSolariaDewChimesLoop(true)` tetap menyala meski melewati `StopAllChallengeVfx()` atau `_pendingVictory`, lalu berhenti bersih saat timer habis atau `ConfigureForStage`/`OnDisable`).
  - Memperbarui `Session4_VillageBuilder.cs`: memasang `VFX_Lake_MouthIntake` anak `Head` `(0, 0.34, -0.04)`, memasang `VFX_Solaria_ChestGemGlow` anak `Neck_01` `(0, 0.1214, -0.1552)` skala `0.85f`, menggeser `VFX_SonicDash` ke `(0, 0.25, 0)` skala `1.1f`, dan menjalankan `DragonWingAR/Session 4/Build Village Zones and Markers` (`VFX_Sockets count=1`).
- **Langkah 5 — Verifikasi Play Mode (`Unity 6000.3.7f1`, `0` Errors, `0` Warnings) & Screenshot Mandiri:**
  - **`VFXAtmosphereVerificationRunner.RunPlayModeVerificationAsync()` (`MainIsland_Gameplay.unity`):** `ALL PILAR 1 & SESI 4B VFX (10/10), TRAIL, SOCKET & BIOME CHECKS PASSED (100%)` — `Glide=(fov=64.99,z=-6.30)`, `Dive=(fov=78.87,z=-7.20)`, `Dash=(fov=85.00,z=-7.50)` (`IsSonicDashActive=True`, `TrailRenderer` aktif), `Hover=(fov=60.06,z=-6.00)`, `HeadSweep=23.4deg` (`dot=1.00`), `Wildfire=2.60->1.65->0`, `IsWaterSiphonActive && IsWaterIntakeActive`, `IsChestGemGlowActive` pada `Neck_01`, dan `Solaria` tetap hujan pada `t+3.5s` (`>8s` tersisa).
  - **Non-Regresi:** `MomentumFlightVerificationRunner` (**100% PASS**), `consoleErrors=0`, `consoleWarnings=0`; scene aktif dikembalikan ke `MainMenu_Lobby.unity`.
  - **Screenshot (`docs/figures/raw_screenshots/`):** `SS_Sesi4B_Flight_PropellerDash_FOV85.png`, `SS_Sesi4B_Solaria_CelestialDewRain.png`, `SS_Sesi4B_Zephyria_SmogAndWindGust.png`, `SS_Pilar1_Lake_Siphon_MouthIntake.png`, `SS_Sesi4B_Pyrewood_WaterBreath_Steam.png`.

---

## 2026-11-25 — Implementasi World Boundary, Pantulan Tabrakan `Terrain`/`Rocks`, Misi Pyrewood 2-Tahap, Time Up Positif, & Dual Input Mode (`CV` vs `Keyboard`) ([[laptop-a/gameplay-mechanics/spesifikasi_gameplay_boundary_dan_dual_input_mode]], [[World Boundary Bounce dan Dual Input]])

- **Konteks & Acuan:** Menyelesaikan Langkah 4–7 Master Prompt mengacu spesifikasi resmi [[laptop-a/gameplay-mechanics/spesifikasi_gameplay_boundary_dan_dual_input_mode]].
- **Langkah 4 — World Boundary Push-Back & Terrain/Rocks Glancing Bounce (`DragonFlightController.cs`, `InGameFlightHUD.cs`, `TagManager.asset`, `IslandArchipelagoSetupUtility.cs`):**
  - Mendaftarkan layer Unity `Terrain` (`6`) dan `Rocks` (`7`) di `ProjectSettings/TagManager.asset` serta menyinkronkannya ke `SM_Archipelago_Terrain` & `SM_Rocks_*` pada `PF_Pulau_Archipelago.prefab` dan scene `MainIsland_Gameplay.unity` (`IslandArchipelagoSetupUtility.SyncTerrainAndRockLayersToPrefabAndScene()`).
  - Menambahkan **World Boundary (`> 1100 m` XZ dari `(0,0,0)`)** pada `DragonFlightController.UpdateWorldBoundaryPushback()`: mengunci kemudi pemain sementara, memutar `Yaw` naga 180° inward melalui `Mathf.SmoothDampAngle` (`smoothTime = 0.65f`, tuntas penuh ~`2.5s`) hingga radius kembali `<= 1050 m`, memutar SFX embusan angin periodik, dan menampilkan banner HUD `"Angin badai laut terlalu kuat! Kembali ke kepulauan."` di `InGameFlightHUD`.
  - Menambahkan deteksi benturan `OnCollisionEnter(Collision)` serta *forward SphereCast sweep* terhadap layer `Terrain` & `Rocks` (`TryHandleGlancingCollisionSweep` -> `ApplyGlancingBounce`): menghitung pantulan `Vector3.Reflect(incomingVel, contactNormal)` dengan sudut angkat `>= +15°`, memotong `CurrentMomentum` & `CurrentSpeed` `-50%`, memicu micro-shake kamera `0.12s` + `PlayWingFlap(heavy: true)`, dan menjaga pemulihan glide aman tanpa reload scene (pendaratan datar normal `isAttemptingLanding && normal.y > 0.65f` tetap mendarat mulus).
- **Langkah 5 — Alur Misi Pyrewood 2-Tahap & Layar Time Up Positif (`VillageZoneController.cs`, `FinishModalUI.cs`, `DragonFlightController.cs`):**
  - Memastikan penguncian Danau Zephyria hingga narasi Desa Pyrewood diterima (`IsChallengeReady(VillageId.Pyrewood)`), menampilkan `"Cadangan Air Purba: SIAP!"` saat kapasitas danau `100%`, serta memperbarui `QuestObjectivePanel` agar selalu menampilkan dua sub-tugas berjenjang (`[1] Cadangan Energi Air Purba: ...` & `[2] Pemadaman Hutan Membara: ...`).
  - Menambahkan penghitungan siklus kepakan real-time (`TotalFlapStrokes` di `DragonFlightController.cs`) dan memperbarui `FinishModalUI.ShowDefeat(...)` menjadi layar suportif **`"WAKTU HABIS! USAHA YANG BAGUS!"`** lengkap dengan durasi aktif latihan (`MM:SS`), estimasi jumlah kepakan, persentase vitalitas kepulauan, serta tombol **`COBA LAGI`** & **`KEMBALI KE LOBBY`**.
- **Langkah 6 — Dual Input Mode (`CV (Webcam)` vs `Keyboard`) (`DragonGameSettings.cs`, `SettingsPanelUI.cs`, `UISceneAndPrefabBuilder.cs`, `MissionsPanelUI.cs`, `LobbyNavigationManager.cs`, `CalibrationPanelUI.cs`, `InGameFlightHUD.cs`, `UDPPoseReceiver.cs`, `PythonProcessManager.cs`):**
  - Menambahkan `ControlInputMode` (`CvWebcam = 0`, `Keyboard = 1`) pada `PlayerPrefs` (`"Fanamir_InputControlMode"`), helper `IsKeyboardInputMode` / `IsReadyToLaunchGameplay`, dan `EnsurePersistentSessionHost()` / `UDPVideoStreamReceiver.EnsureInstance()` yang aman dari pemanggilan EditMode.
  - Memperluas `PF_SettingsPanel.prefab` (`SettingsCard` `960×716`, `UISceneAndPrefabBuilder.BuildAll()`) dengan baris dropdown `Row_InputControlMode` (`"Control Input Mode"` -> `"CV (Webcam)"` / `"Keyboard"`) yang terhubung ke `SettingsPanelUI`.
  - Di mode `Keyboard`: gerbang kalibrasi dilewati (`PLAY` aktif di `MissionsPanelUI` & `CalibrationPanelUI` dan `LaunchFlightScene()` terbuka), top navbar menampilkan badge `"KEYBOARD MODE"`, `InGameFlightHUD` menyembunyikan klaster PIP webcam & overlay kerangka, `UDPPoseReceiver` mematikan listener port `5052` tanpa error socket, dan `PythonProcessManager` tetap idle/dihentikan.
- **Langkah 7 — Verifikasi Penuh & Pengembalian Scene:**
  - **Python CV:** `test_all_17_poses_alignment.py`, `test_momentum_and_control_revision.py`, & `test_tahap1_face_tracking.py` (`8/8`) — **100% PASS**.
  - **Unity Play Mode (`MainIsland_Gameplay.unity` & `MainMenu_Lobby.unity`):** `Session4VerificationRunner`, `Session5GameLoopVerificationRunner`, `DragonAnimationVerificationRunner`, `MomentumFlightVerificationRunner`, `AudioSystemVerificationRunner` (`37/37`), plus uji runtime Langkah 4–6 (`>1100m` pushback & `<=1050m` release, `ApplyGlancingBounce` `-50%` momentum/speed `+15°` pitch, Pyrewood 2-stage objective, `"WAKTU HABIS! USAHA YANG BAGUS!"`, Settings dropdown & Lobby `"KEYBOARD MODE"` + PIP hide) — **100% PASS**, `consoleErrors=0`, `compilationFailed=false`, dan scene aktif kembali ke `MainMenu_Lobby.unity`.

---

## 2026-11-25 — Implementasi Final Dynamic Wings (Dual-Track 1:1), Dynamic Head 3-Sumbu, Kamera Melirik, & Sinkronisasi Trotting `1.4 Hz` ([[laptop-a/visual-vfx/spesifikasi_dynamic_wings_dan_head_tracking]])

- **Konteks & Acuan:** Mengimplementasikan spesifikasi terbaru Laptop A (`15e0191`) di [[laptop-a/visual-vfx/spesifikasi_dynamic_wings_dan_head_tracking]] & [[laptop-a/handoff-laptop-b/prompt_eksekusi_animasi_final_laptop_b]] sambil menjaga kompatibilitas penuh dengan rig & kurva `studio.js v15` (`evaluatePropellerDashStroke` Heavy Butterfly-Stroke 3D Orbit).
- **1. Python CV 3-Sumbu (`mouth_tracker.py`, `active_cv_sender.py`, `udp_sender.py`, `test_tahap1_face_tracking.py`):**
  - Menambahkan `compute_head_yaw_norm` (`±35°` dari deviasi `(Nose.x - MidShoulder.x) / ShoulderWidth` dipadukan asimetri wajah MediaPipe) dan `compute_head_roll_norm` (`±15°` dari garis luar kedua mata `#33 -> #263` + `AdaptiveOneEuroFilter`).
  - Memperluas `PoseUDPSender.send_pose(...)` dengan argumen opsional `head_yaw` (token `44`) & `head_roll` (token `45`) sehingga tetap kompatibel mundur (`44` token standar vs `46` token live) tanpa mengubah tampilan `Clean CV Preview`.
- **2. Kontrak Telemetri Unity (`IPoseProvider.cs`, `UDPPoseReceiver.cs`, `IFlightInputProvider.cs`, `DefaultFlightInputMapper.cs`, `DragonFlightController.cs`):**
  - Mengekspos `HeadYaw`, `HeadRoll`, `LeftWristDeltaY`, dan `RightWristDeltaY` ke `FlightCommand` serta memastikan `_isTrackingReady` / `_hasPlayerFormedInitialPose` diperbarui di `FetchFlightCommand()` pada seluruh state (Grounded, Hover, & Airborne).
- **3. Dual-Track Dynamic Wings & Wing Overdrive Steer (`DragonWingAnimator.cs`, `DragonFlightController.cs`):**
  - Memisahkan **Jalur A (1:1 Dynamic Free Tracking)** untuk `Glide`, `Lean`, `FlyUp`, `FlapClimb`, `Hover`, & `SlowGlide` (mapping `deltaY` `-0.30..+0.45 -> -30°..+40°` per sisi + bukaan membran jari `ArmOpenness`) vs **Jalur B (Preset Locked)** untuk `TuckDive`, `PropellerDash`, `GentleDescent`, `FlyDownW/Y`, `GroundedIdle`, & `Village Actions` dengan transisi `Mathf.SmoothStep (0.20s)` serta ekstraksi `EvaluatePropellerDashStroke(...)`.
  - Mengaktifkan *Wing Overdrive Steer* pada `DragonFlightController` (`deadzone 0.15`, pengali `1.5f`, `Clamp(-1, +1)`).
- **4. Pelacakan Kepala 3-Sumbu, Kunci Aerodinamis, Kamera Melirik, & Trotting `1.4 Hz` (`DragonHeadLookController.cs`, `DragonFlightController.cs`, `DragonBodyAnimator.cs`):**
  - Mengubah `DragonHeadLookController` memakai `Mathf.SmoothDamp (0.11s)` untuk `HeadPitch`, `HeadYaw` (`±35°`), & `HeadRoll` (`±15°`) distribusi `25%/35%/40%`, serta **Kunci Aerodinamis Kecepatan Tinggi** ke `0°` (`0.20s`) saat `CurrentSpeed >= 22 m/s`, `TuckDive`, atau `PropellerDash`.
  - Menambahkan **Kamera Melirik Sinematik** pada `DragonFlightController.UpdateCameraPolish()` (`cameraYawPeekFactor = 0.11f` via `Mathf.SmoothDamp 0.25s`).
  - Memperbarui siklus jalan 4 kaki (`TrottingFrequencyHz = 1.4f`, `ComputeTrottingLegAngles`) pada `DragonBodyAnimator.cs` & menyinkronkan panggilan `AudioManager.PlayDragonFootstep()` tepat pada crossing fase `0.0` dan `0.5`.
- **5. Verifikasi Kompilasi, Python CV, & Unity Play Mode (`100% PASS`):**
  - **Python CV:** `test_all_17_poses_alignment.py`, `test_momentum_and_control_revision.py`, & `test_tahap1_face_tracking.py` (`8/8` PASS, termasuk PASS 8 `HeadYaw/HeadRoll` & UDP `46`-float).
  - **Unity Play Mode (`MainIsland_Gameplay.unity` & `MainMenu_Lobby.unity`):**
    - Verifikasi khusus Dual-Track/Head/Camera: `DeadzoneTurn=0.000`, `OverdriveTurn=0.46`, `AsymWingY=(L=0.35,R=-0.18)`, `Head=(yaw=28.0°,roll=9.0°)`, `CamPeek=3.07°`, `AeroLock=(yaw=0.0°,roll=0.0°)`, `WalkSpd=3.49`.
    - `DragonAnimationVerificationRunner`: `ALL 20-POSE DRAGON ANIMATION CHECKS PASSED (100%)`.
    - `MomentumFlightVerificationRunner`: `ALL MOMENTUM & FLIGHT PHYSICS CHECKS PASSED (100%)`.
    - `AudioSystemVerificationRunner` (`MainMenu_Lobby.unity`): `ALL AUDIO SYSTEM CHECKS PASSED (37/37 clips, Mixer buses, dB curve, Dual BGM Dropdowns, Crossfade, & SFX triggers 100%)`.
    - `consoleErrors=0`, `consoleWarnings=0` di ground truth Unity, & scene aktif dikembalikan ke `MainMenu_Lobby.unity`.

---

## 2026-11-25 — Integrasi Model 3D Naga Fanamir Terbaru & Sinkronisasi Penuh 20 Pose Prosedural Studio `v15`

- **Konteks & Paket Sumber:** Mengekstrak `assets/Export_DragonModel_Anim20_LaptopB.zip` (`Dragon_Model.fbx` `2,408,268` B bebas skin-weight bleed & 3 poligon asimetris dorsal kepala diperbaiki, `UNITY_20_POSES_REFERENCE.cs.txt`, dan `Reference_AnimStudio/studio.js` `v15`).
- **Langkah 1 — Pembaruan FBX & Remap Material URP (`Dragon_Model.fbx`, `M_Dragon_Chest_Crystal.mat`):**
  - Mencadangkan FBX lama ke `assets/archives/Dragon_Model_backup_pre_anim20.fbx` dan menimpa `AR Dragon Sky Guardian/Assets/Assets 3D/Dragon_Model.fbx` tanpa menghapus `.meta`.
  - Memverifikasi 47 tulang `ARM_Dragon`, 13 `SkinnedMeshRenderer` (`updateWhenOffscreen = true`), 12 material URP Lit, soket `DragonVfxController` (`Head`, `Wing_Finger_Outer.L/R`, seluruh 6 emitter aktif), serta `localPosition = (0, -0.7937, 0.6985)` & `localScale = (1.25, 1.25, 1.25)`.
  - Mengekstrak & meremap `Assets/Assets 3D/Materials/M_Dragon_Chest_Crystal.mat` pada `ModelImporter` FBX dengan `_BaseColor = (0.18, 0.86, 1.0)` dan `_EmissionColor = (0.15, 0.85, 1.35)` (`_EMISSION` aktif) sehingga permata dada menyala biru-cyan di seluruh scene.
- **Langkah 2 & 3 — Konversi `FromStudioEulerXYZ` & Sinkronisasi 20 Pose (`50 -> 55 -> 60`):**
  - **`DragonWingAnimator.cs` (`[DefaultExecutionOrder(50)]`):** Menambahkan `DragonPoseMode` (20 pose + Rest), `FromStudioEulerXYZ(x, y, z)`, dan seluruh regime sayap Studio `v15` di atas basis rest pose FBX native (`#01..#03` lipat samping, `#04/#06/#06B` lag + modulasi CV per-sisi, `#05/#08` airfoil datar simetris, `#07` Y-Dihedral `+32°/+12°`, `#09A` flutter `1.4 Hz`, `#09B` kompak semi-L, `#10` Low-A `-28°/-14°`, `#11` Y-Dive, `#12` W-Pose datar horizontal, `#13` Z-fold zero-twist, `#14` orbit 3D butterfly `1.4 Hz`, `#15A` sapuan badai Zephyria `0.95 Hz`, `#15B` hover stabilizer `1.2 Hz`, `#15B-1` Water Siphon `1.3 Hz`, `#15C` Double I-Pose `"11"` `72°`).
  - **`DragonBodyAnimator.cs` (`[DefaultExecutionOrder(55)]`):** Menyelaraskan `Root` tanpa *double-pitch* terhadap `Visual_Dragon` (`studioRootPitch - flightPitch`), osilasi/surge aditif pada `Visual_Dragon` (`walk +0.018m`, `hover ±0.07m`, `takeoff ±0.07m`, `siphon ±0.035m`, `dash Y+0.08m/Z+0.09m`), `Spine_01/02`, *Quadruped Diagonal Trotting Gait* (`1.25 Hz` + proyeksi paha sagital), 5 regime kaki udara (termasuk `#15A Zephyria` kaki depan terlipat sedang & `#15C Solaria` cakar menyentuh permata dada), ekor counter-balance `+X` + undulation + kemudi berjenjang (`15/30/55/75/100%`), serta rahang (`MOUTH_OPEN`, `VictoryRoar`, `WaterBreath -30°`, & `WaterSiphon sipJaw -14°..-21°`).
  - **`DragonHeadLookController.cs` (`[DefaultExecutionOrder(60)]`):** Mengimplementasikan `ApplyUniversalHeadNeck` distribusi `25%/35%/40%` beserta proyeksi Yaw/Roll dan mode terkunci `#13`, `#14`, `#15A`, `#15B`, `#15B-1 Water Siphon` (`-56° / -24° / +38°` + `drinkBob`), dan `#15C Solaria`.
  - **`LiveDragonPreviewController.cs` & `VillageZoneController.cs`:** Menghubungkan `DragonBodyAnimator` penuh 47 tulang pada layar Kalibrasi UI `RenderTexture` dan menyinkronkan `ActiveVillagePoseMode` (`WaterSiphon` aktif sepanjang Auto-Hover Danau Pyrewood hingga `100%` Auto-Release, `WaterBreath` saat menyembur api, `Zephyria` saat kepakan kabut, `Solaria` saat Double-I Pose).
- **Hasil Verifikasi Play Mode (`Unity 6000.3.7f1`, `0` errors, `0` warnings):**
  - **`DragonAnimationVerificationRunner` (`MainIsland_Gameplay.unity`):** `ALL 20-POSE DRAGON ANIMATION CHECKS PASSED (100%)` — memverifikasi `Walk` (`spanZ=0.67`), `Jaw` (`0.0 / -14.0 / -30.0 / roar=-25.0`), `Tail` (`right tip=20.0/root=3.0, left tip=-20.0, undulation=80.1`), `TuckDive`, `FlyUp`, `W-Pose`, `PropellerDash` 3D orbit (`spanZ=0.77, spanY=1.37`), `Hover` (`chest=+13.6°, bobSpan=0.14m, thigh=12°`), `Zephyria`, `WaterSiphon` (`headY=-0.77`), `WaterBreath`, dan `Solaria` (`wingUpY=0.91, pawGemDist=0.47`).
  - **`MomentumFlightVerificationRunner`:** `ALL MOMENTUM & FLIGHT PHYSICS CHECKS PASSED (100%)`.
  - **`Session4VerificationRunner`:** `ALL SESSION 4 VILLAGE, SUB-MARKER, AUTO-HOVER, ESCAPE, BILLBOARD & UI ISOLATION CHECKS PASSED (100%)` (termasuk `WaterSiphon` aktif di Danau Zephyria & reset ke `None` saat Auto-Release).
  - **`Session5GameLoopVerificationRunner`:** `ALL SESSION 5 GAME LOOP, COUNTDOWN, NARRATIVE FREEZE, GLOBAL VITALITY STARS, UNLOCK & FINISH MODAL CHECKS PASSED (100%)`.
  - **`Test_Dragon_Flight.unity` & `MainMenu_Lobby.unity`:** Rig `Dragon_Player` dan `LiveDragonPreviewRig` (`CalibPreviewVerify PASS | wingReady=True bodyReady=True headReady=True diveMode=TuckDive hoverMode=Hover hoverChest=14.9`) terverifikasi bersih; scene aktif dikembalikan ke `MainMenu_Lobby.unity` (Build Index `0`).
  - **Tangkapan Layar (`docs/figures/raw_screenshots/`):** `SS_Animasi_Tahap1_HoverChest.png`, `SS_Animasi_Tahap1_FlightLegs.png`, `SS_Animasi_Dragon_JawOpen.png`, `SS_Animasi_Dragon_TailSway.png`, `SS_Anim20_WaterSiphon.png`, `SS_Anim20_Solaria.png`, `SS_Anim20_Calibration_Preview.png`.
- **Perbaikan Hotfix Render Graph Effekseer (`EffekseerNormalTextureGuard.cs`):**
  - **Akar Masalah:** Saat menguji Play Mode secara manual (`15:31:15`), `EffekseerRendererUnity.ApplyTextures` (baris `1628`, slot `_uvDistortionTex` untuk efek `AdvancedUnlit` seperti `VFX_Flight_SonicDashShockwave`) melempar `ArgumentNullException: Value cannot be null` + `Render Graph Execution error` karena `EffekseerSystem.normalTexture` merupakan `Texture2D` statis tanpa `HideFlags.HideAndDontSave` dan tidak dibuat ulang secara *lazy* bila hilang/dihancurkan (Domain Reload, transisi Play Mode, atau pembersihan aset).
  - **Solusi Permanen di `Assets/`:** Menambahkan `AR Dragon Sky Guardian/Assets/_Scripts/Controllers/EffekseerNormalTextureGuard.cs` (`[RuntimeInitializeOnLoadMethod]` + `[InitializeOnLoadMethod]` + hook `RenderPipelineManager.beginContextRendering`) yang memeriksa & memulihkan `EffekseerSystem.normalTexture` (`16x16` flat normal `HideFlags.HideAndDontSave`) sebelum URP RenderGraph mengeksekusi pass, tanpa memodifikasi `Library/PackageCache`.
  - **Verifikasi Pasca-Perbaikan:**
    - *Fault-injection* (`normalTexture = null` sebelum `PlaySonicDashShockwave()`): `restoredNotNull=True, name=EffekseerNormalTexture, hideFlags=HideAndDontSave`.
    - `VFXAtmosphereVerificationRunner` (`MainIsland_Gameplay.unity`): `ALL SESI 4B VFX, DYNAMIC FOV & BIOME ATMOSPHERE CHECKS PASSED (100%)` (seluruh 7 efek `.efkefc` aktif normal).
    - `DragonAnimationVerificationRunner`: `ALL 20-POSE DRAGON ANIMATION CHECKS PASSED (100%)`, `consoleErrors=0`, `consoleWarnings=0`.

---

## 2026-11-25 — Pembaruan Sistem Audio Terpadu (Dual Dropdown BGM `1..11` & `Menu 1..6`, Quick Switcher Pill Lobby, & Heavy Whoosh Propeller Dash) ([[Implementasi Sistem Audio Fanamir]], [[laptop-a/gameplay-mechanics/arsitektur_sistem_audio]])

- **Konteks & Acuan:** Mengadopsi spesifikasi terbaru [[laptop-a/gameplay-mechanics/arsitektur_sistem_audio]] dari Laptop A (`git -C brain pull`: `b1e76e8..25b52de`) setelah pembaruan paket audio `assets/Export_Audio_LaptopB.zip`.
- **Sinkronisasi Aset Audio & Propeller Dash Heavy Whoosh (`AudioImporterSetupUtility.cs`):**
  - Mengekstrak ulang 37 klip resmi (`14` BGM `.mp3` + `23` SFX `.wav`, termasuk pembaruan `SFX_PropellerDash_Loop.wav` Heavy Aerodynamic Whoosh Loop `176,444` bytes) ke `AR Dragon Sky Guardian/Assets/Audio/`.
  - Importer & Prefab terverifikasi: `Configured 37 audio clips in Assets/Audio/ (BGM Streaming=14, Short SFX PCM=15, Long/Loop SFX Vorbis=8, 0 reimported)` dan `Built Assets/Resources/Fanamir_AudioManager.prefab with 37/37 clips & Mixer=Fanamir_AudioMixer`.
- **Dual BGM Selection Persisten & AudioManager (`DragonGameSettings.cs`, `AudioManager.cs`, `BootFlowController.cs`):**
  - Mengganti `BgmThemeStyle` dengan dua indeks persisten `PlayerPrefs`:
    - `PrefBgmFlightSelection` (`"Fanamir_BgmFlightIndex"`, `0..11`: `[0] Adaptif (Otomatis)`, `[1..11] BGM 1..BGM 11`) + `OnBgmFlightSelectionChanged`.
    - `PrefBgmMenuSelection` (`"Fanamir_BgmMenuIndex"`, `0..5`: `[0..5] BGM Menu 1..BGM Menu 6`) + `OnBgmMenuSelectionChanged`.
  - Memperbarui `AudioManager.cs` (`_menuBgmClips[6]`, `_flightBgmClips[11]`, `_victoryBgmClip`, `_crisisBgmClips[3]`, `PlayMenuBGM`, `SetBgmFlightSelection`, `SetBgmMenuSelection`, `RestorePeacefulBGM` adaptif maupun track terkunci) dan `BootFlowController.EnterLobbyImmediate()` agar langsung memutar `PlayMenuBGM(DragonGameSettings.BgmMenuSelection)`.
- **Dual Solid Dropdown Settings & Quick BGM Menu Switcher Lobby (`SettingsPanelUI.cs`, `LobbyNavigationManager.cs`, `UISceneAndPrefabBuilder.cs`):**
  - Membangun ulang prefab & scene via `UISceneAndPrefabBuilder.BuildAll()`: `PF_SettingsPanel.prefab` (`SettingsCard` `960×656`) memakai dua `SolidCameraDropdown` (`Row_BgmFlightSelection` & `Row_BgmMenuSelection`) yang sinkron reaktif di `SettingsPanelUI.cs`.
  - Menambahkan tombol pill `SharedTopNavbar/NavBgmMenuPill` (`198×46 px` di `(-442f, 0f)`) pada `MainMenu_Lobby.unity` (`SCREEN 00`) dan wiring `quickBgmMenuButton` / `quickBgmMenuLabel` di `LobbyNavigationManager.cs` untuk *cycle* instan `BGM Menu 1..6` yang otomatis tersinkronisasi dua arah dengan dropdown di `SettingsPanelUI`.
- **Verifikasi End-to-End & Kebersihan Kode:**
  - Penanda `// TODO [SYNC-AUDIO]` berjumlah `0`.
  - `AudioSystemVerificationRunner` di Play Mode `MainMenu_Lobby.unity`: `Splash='MADE WITH UNITY' | QuickPill & DualDropdowns=PASS | ALL AUDIO SYSTEM CHECKS PASSED (37/37 clips, Mixer buses, dB curve, Dual BGM Dropdowns, Crossfade, & SFX triggers 100%)`.
  - Uji Play Mode `MainIsland_Gameplay.unity`: `adaptive=BGM_Flight_AirPrelude`, `bgm1=BGM_Flight_CelticImpulse`, `bgm11=BGM_Crisis_Solaria_CurseOfTheScarab`, `dashPlaying=True (2.0s)`, dan `MomentumFlightVerificationRunner`: `ALL MOMENTUM & FLIGHT PHYSICS CHECKS PASSED (100%)` dengan `0` console errors & `0` console warnings (`console_get` `since: 33`).

## 2026-11-25 — Peningkatan Sistem Audio Fanamir (14 BGM + 23 SFX = 37 Klip, `BgmThemeStyle` Persisten, & Splash Screen Unity Branding) ([[Implementasi Sistem Audio Fanamir]], [[laptop-a/gameplay-mechanics/arsitektur_sistem_audio]])

### Perubahan yang Diimplementasikan
1. **Sinkronisasi Paket `Export_Audio_LaptopB.zip` (37 Klip: 14 BGM + 23 SFX):**
   - Mengekstrak `assets/Export_Audio_LaptopB.zip` dan menyalin seluruh **37 berkas audio** ke `AR Dragon Sky Guardian/Assets/Audio/`:
     - **14 BGM MP3:** 7 Ghibli/Fantasy (`BGM_Lobby_FolkRound`, `BGM_Lobby_VillageConsort`, `BGM_Flight_CelticImpulse`, `BGM_Flight_AirPrelude`, `BGM_Flight_LordOfTheLand`, `BGM_Flight_TellerOfTheTales`, `BGM_Flight_ClearWaters`), 6 Cinematic Tribal (`BGM_Flight_SkyeCuillin`, `BGM_Crisis_Zephyria_HimalayanAtmosphere`, `BGM_Crisis_Pyrewood_Firesong`, `BGM_Crisis_Pyrewood_Ritual`, `BGM_Crisis_Solaria_DesertCity`, `BGM_Crisis_Solaria_CurseOfTheScarab`), dan 1 Celebration (`BGM_Victory_HeroicAge`).
     - **23 SFX WAV:** 8 Dragon, 6 Village, dan 9 UI (termasuk `SFX_Splash_LogoReveal.wav` berdurasi `3.200s`).
   - Memperbarui `AudioImporterSetupUtility.cs` & membangun ulang `Assets/Resources/Fanamir_AudioManager.prefab` (`37/37` klip terhubung ke `Fanamir_AudioMixer.mixer`).
2. **Sistem Preferensi Gaya Musik Persisten (`DragonGameSettings.cs` & `AudioManager.cs`):**
   - Menambahkan enum `BgmThemeStyle` (`Adaptive = 0`, `GhibliFantasy = 1`, `CinematicTribal = 2`), key `PrefBgmThemeStyle = "Fanamir_BgmThemeStyle"`, event `OnBgmThemeStyleChanged`, dan properti persisten `CurrentBgmThemeStyle` (`PlayerPrefs.SetInt` + `PlayerPrefs.Save()`) di `DragonGameSettings.cs`.
   - Memperluas `AudioManager.cs` untuk memuat seluruh 37 klip, berlangganan `OnBgmThemeStyleChanged` (memicu cross-fade instan `1.5s` saat gaya diubah selama jelajah santai), memilih track `Adaptive` dinamis sesuai ketinggian/posisi/kecepatan (`Y > 120m` $\rightarrow$ `AirPrelude`, Center Peak `< 140m` $\rightarrow$ `SkyeCuillin`, speed $\ge 20\text{ m/s}$ $\rightarrow$ `LordOfTheLand`, `Y < 32m` $\rightarrow$ `ClearWaters`, default `CelticImpulse`), serta memilih variasi tribal untuk Pyrewood/Solaria saat gaya `CinematicTribal` aktif.
3. **Splash Screen Audio + Unity Branding (`SCREEN 09`) & Kontrol Gaya Musik Settings (`SCREEN 05`/`06`):**
   - **`BootFlowController.cs`:** Memainkan `SFX_Splash_LogoReveal.wav` via `AudioManager.EnsureInstance().PlaySplashLogoReveal()` pada awal fade-in logo `SCREEN 09`.
   - **`UISceneAndPrefabBuilder.cs` & `SettingsPanelUI.cs`:** Menambahkan teks `Splash_UnityBranding` (`"MADE WITH UNITY"`, `#8C7F70`, `characterSpacing = 4.5f`) di bawah `Splash_Footer` pada `SCREEN 09`; menambahkan baris ke-6 `Row_BgmThemeStyle` (3 tombol segmented pill: `ADAPTIF`, `GHIBLI FANTASY`, `CINEMATIC TRIBAL`) pada `PF_SettingsPanel.prefab` (`SettingsCard` `960×586 px`) yang langsung menyimpan `Fanamir_BgmThemeStyle` & memicu cross-fade instan, lalu menjalankan `BuildAll()` ke seluruh prefab dan scene.

### Hasil Verifikasi Play Mode (`Unity 6000.3.7f1`)
- **`AudioImporterSetupUtility` & Prefab:** `Configured 37 audio clips (BGM Streaming=14, Short SFX PCM=15, Long/Loop SFX Vorbis=8)` & `Built Assets/Resources/Fanamir_AudioManager.prefab with 37/37 clips & Mixer=Fanamir_AudioMixer`.
- **`MainMenu_Lobby.unity` Play Mode:** `Splash_UnityBranding = "MADE WITH UNITY"` terverifikasi, `Row_BgmThemeStyle` (`Btn_StyleAdaptive`/`Btn_StyleGhibli`/`Btn_StyleTribal`) menyimpan `PlayerPrefs("Fanamir_BgmThemeStyle")` (`2`/`1`/`0`), dan `AudioSystemVerificationRunner` lulus **`37/37 clips, Mixer buses, dB curve, BgmThemeStyle, Crossfade, & SFX triggers 100%`**.
- **`MainIsland_Gameplay.unity` Play Mode:** Verifikasi transisi langsung `OnBgmThemeStyleChanged` (`Spawn >120m` = `BGM_Flight_AirPrelude`, `CinematicTribal` = `BGM_Flight_SkyeCuillin`, `GhibliFantasy` = `BGM_Flight_CelticImpulse`, `Adaptive 60m` = `BGM_Flight_CelticImpulse`), `PauseModal -> Settings` memiliki `Btn_StyleTribal` aktif, `AudioSystemVerificationRunner` **100% PASS**, serta non-regresi `MomentumFlightVerificationRunner` **100% PASS** (`0` console errors, `0` console warnings, `0` `// TODO [SYNC-AUDIO]`). Scene aktif dikembalikan ke `MainMenu_Lobby.unity`.

---

## 2026-11-25 — Penghapusan Section Kalibrasi/Kamera pada Settings & Perbaikan Gap/Padding UI (`PF_SettingsPanel.prefab`, `PF_PoseGuideOverlay.prefab`, `PF_CalibrationPanel.prefab`, `[[Perbaikan UI Settings dan Pose Guide]]`)

### Perubahan yang Diimplementasikan
1. **`SettingsPanelUI.cs` (`Assets/_Scripts/UI/SettingsPanelUI.cs`):**
   - Menghapus `using DragonWingAR.CV;`, field `[SerializeField] private CameraControlModuleUI cameraModule;`, serta pemanggilan `cameraModule.SetPoseReceiver(...)` pada `OnEnable()`.
2. **`UISceneAndPrefabBuilder.cs` (`Assets/Editor/UISceneAndPrefabBuilder.cs`) & `PF_SettingsPanel.prefab`:**
   - Menghapus parameter `pfCamModule`, seksi `CameraModulePlaque` (`CAMERA INPUT & AI YOLO`, instance `CameraControlModuleUI`, dan `CameraHintPanel`), serta binding properti `cameraModule` di `BuildSettingsPanelPrefab()`.
   - Menyesuaikan tinggi `SettingsCard` dari `960×720 px` (`y = -18`) menjadi `960×520 px` (`y = -10`) dan memberi jarak bawah `y = 42` pada tombol `APPLY & CLOSE` (gap `36 px` di bawah slider ke-5 & bersih di atas bingkai dalam kartu), lalu membangun ulang `Assets/Prefabs/UI/PF_SettingsPanel.prefab` (otomatis terpropagasi ke `MainMenu_Lobby.unity` dan `PF_PauseModal.prefab`).
3. **`LobbyNavigationManager.cs` (`Assets/_Scripts/UI/LobbyNavigationManager.cs`):**
   - Mengarahkan klik `navCamBadgeButton` di navbar tab Missions ke `LobbyTab.Calibration` (sebelumnya `LobbyTab.Settings`) dan mengubah teks badge error menjadi `"CAM ERROR · BUKA CALIBRATION"`.
4. **`PF_PoseGuideOverlay.prefab` & `PF_CalibrationPanel.prefab` (`UISceneAndPrefabBuilder.cs` & `PoseGuideOverlayUI.cs`):**
   - Menyesuaikan tinggi `ModalCard` dari `1848×890 px` menjadi `1848×854 px` (`y = -18`) dan menaikkan posisi `Btn_ClosePoseGuide` (`CLOSE POSE GUIDE`) dari `y = 36` menjadi `y = 48` dari tepi bawah kartu agar bebas dari bingkai dalam serta memiliki jarak seimbang di bawah `KeyRulesStrip`.
   - Karena `UI_Plaque_PoseCard_Active_9Slice.png` (`208×143`) memiliki bingkai tinta di piksel `14..18` (`~23 px` pada takik sudut), margin dalam seluruh elemen pada **`PoseGuideCard_01..15`** (`PF_PoseGuideOverlay.prefab`) dan **`RecentPoseSlot_1..4`** (`PF_CalibrationPanel.prefab`) diperlebar menjadi **`26 px` (kiri/atas/kanan)** dan **`24 px` (bawah)**:
     - `PoseGuideCard`: `PoseIcon` `(26, -26)` `54×56`, `NumberTag` `(20, -88)`, `StatusPill` `(-26, -26)` `64×22`, `Title`/`Subtitle`/`Description` (`offsetMin.x = 92`, `offsetMax.x = -26` atau `-96` saat pill tampil, `Description` `offsetMin.y = 24`, `offsetMax.y = -74`), dan `PoseGuideOverlayUI.RefreshAllCards()` disinkronkan ke `showPill ? -96f : -26f`.
     - `RecentPoseSlot`: `PoseIcon` `(26, -26)` `60×62`, `NumberTag` `(20, -94)`, `SlotBadgePill` `(-26, -26)` `132×24`, `Title` (`98..-166, -26..-52`), `Subtitle` (`98..-26, -53..-74`), `Description` (`98..-26, 24..-78`).

### Hasil Verifikasi Play Mode (`Unity 6000.3.7f1`)
- **`MainMenu_Lobby.unity` (Tab Settings):** `SettingsCard` (`960×520`, tombol `Btn_ApplyAndClose` `y = 42`) tampil bersih dengan 5 baris slider + `APPLY & CLOSE` (`CameraModulePlaque` tidak ada), klik `NavCamBadge` berpindah ke `Calibration`, klik `APPLY & CLOSE` berpindah ke `Missions` (`0` console errors, `0` console warnings).
- **`MainMenu_Lobby.unity` (Calibration & Pose Guide `SCREEN 01` / `SCREEN 07`):** Inspeksi screenshot `1920×1080` mengonfirmasi `RecentPoseSlot_1` (`SLOT 1 · ACTIVE`) dan `PoseGuideCard_08` (`ACTIVE`) beserta seluruh kartu lainnya memiliki celah krem bersih di dalam bingkai tebal `ACTIVE`, tombol `CLOSE POSE GUIDE` berjarak proporsional (`ModalCard` `1848×854`, `y = 48`), dan `closedByFooter=True, closedByEsc=True` (`0` console errors, `0` console warnings).
- **`MainIsland_Gameplay.unity` (Pause -> Settings):** Diverifikasi saat `SettingsCard` `960×496` (`Pause_SettingsSubPanel`, 5 slider, `hasCamPlaque=False`) terbuka dan tertutup normal saat `APPLY & CLOSE` ditekan (`0` console errors, `0` console warnings), dan mewarisi pembaruan `PF_SettingsPanel.prefab` (`960×520`). Scene aktif dikembalikan ke `MainMenu_Lobby.unity`.

---

## 2026-11-24 — Implementasi Sistem Audio Terpadu Fanamir (BGM Dinamis, Cross-Fade, Mixer, & 22 SFX) ([[Implementasi Sistem Audio Fanamir]], [[laptop-a/gameplay-mechanics/arsitektur_sistem_audio]])

### Perubahan yang Diimplementasikan
1. **Sinkronisasi Aset Audio & Importer (`Assets/Audio/`, `Assets/Editor/AudioImporterSetupUtility.cs`):**
   - Mengekstrak `assets/audio.zip` ke `assets/audio/` dan menyinkronkan seluruh **30 berkas audio** (**8 BGM MP3** di `BGM/Ghibli_Fantasy/`, `BGM/Cinematic_Tribal/`, `BGM/Celebration/` + **22 SFX WAV** di `SFX/Dragon/`, `SFX/Village/`, `SFX/UI/`) ke `AR Dragon Sky Guardian/Assets/Audio/`.
   - Membuat `AudioImporterSetupUtility.cs` (`AssetPostprocessor` + menu `DragonWingAR/Audio/Setup All Audio Importers`) yang menerapkan profil impor resmi: BGM MP3 (`Streaming`, `Vorbis`, `0.70f`), 15 SFX pendek `< 2s` (`DecompressOnLoad`, `PCM`), dan 7 SFX loop/stinger `>= 2s` (`CompressedInMemory`, `Vorbis`, `0.85f`).
2. **AudioMixer & Prefab Persistent (`Fanamir_AudioMixer.mixer`, `Assets/Resources/Fanamir_AudioManager.prefab`):**
   - Membangun `Assets/Audio/Fanamir_AudioMixer.mixer` dengan hirarki `Master -> BGM` (exposed `"BgmVolume"`) dan `Master -> SFX` (exposed `"SfxVolume"`) `-> UI`.
   - Menambahkan builder `DragonWingAR/Audio/Build Fanamir AudioMixer` dan `Build AudioManager Prefab` agar `AudioManager` memuat seluruh 30 klip & mixer secara deterministik di Editor maupun Build Standalone.
3. **`AudioManager.cs` (`Assets/_Scripts/Audio/AudioManager.cs`, namespace `DragonWingAR.Audio`, `[DefaultExecutionOrder(-50)]`):**
   - Singleton `DontDestroyOnLoad` yang otomatis diinisialisasi sejak `BeforeSceneLoad` melalui `DragonGameSettings.EnsurePersistentSessionHost()`.
   - Dual-Channel BGM Cross-Fader (`_bgmSourceA` & `_bgmSourceB`) dengan kurva *smoothstep* berbasis `Time.unscaledDeltaTime`, transisi otomatis saat `SceneManager.sceneLoaded` (`MainMenu_Lobby` $\rightarrow$ `BGM_Lobby_FolkRound`, `MainIsland_Gameplay`/`Test_Dragon_Flight` $\rightarrow$ `BGM_Flight_CelticImpulse`), `CrossfadeToCrisis(BiomeType)`, `RestorePeacefulBGM()`, `PlayCleansedStinger()` (dengan ducking BGM sementara), serta `PlayVictoryCelebration()` (`BGM_Victory_HeroicAge` + `SFX_Dragon_Roar_Victory` + `DragonBodyAnimator.Instance.PlayVictoryRoar()`).
   - Berlangganan penuh ke `DragonGameSettings.OnBgmVolumeChanged` & `OnSfxVolumeChanged` dengan konversi `20 * Mathf.Log10(Mathf.Max(linear, 0.0001f))` ke `"BgmVolume"` & `"SfxVolume"`, sekaligus menghapus seluruh `// TODO [SYNC-AUDIO]` pada `DragonGameSettings.cs` dan `SettingsPanelUI.cs`.
4. **Hook Audio Gameplay & UI:**
   - **`DragonFlightController.cs`:** Kepakan sayap normal/heavy (`SFX_WingFlap`/`SFX_WingFlap_Heavy`), loop `SFX_DiveRush_Loop` proporsional kecepatan (`12–30 m/s`), loop `SFX_PropellerDash_Loop`, `SFX_Launch_Whoosh` saat lepas Auto-Hover, serta langkah kaki bergantian `SFX_Dragon_Footstep_01/02` saat jalan darat.
   - **`VillageZoneController.cs`:** Deteksi radius krisis `< 250 m` dengan hysteresis `> 280 m`, `SFX_Zephyria_WindGust`, loop `SFX_Lake_Siphon_Loop`, loop `SFX_Pyrewood_WaterBreath_Loop` + `SFX_Pyrewood_Extinguish_Steam`, loop `SFX_Solaria_DewChimes`, `PlayCleansedStinger()`, dan `PlayVictoryCelebration()`.
   - **`GameManager.cs`, `FinishModalUI.cs`, & UI Lobby/Pause/Challenge:** `SFX_Countdown_Beep` (`3..2..1`) & `SFX_Countdown_Go` (`"TERBANG!"`), `SFX_Star_01..03` pada animasi bintang `FinishModalUI`, `SFX_UI_PoseLock` pada `CalibrationPanelUI` & `VillageChallengeUI`, serta `SFX_UI_Click` pada tombol navigasi `LobbyNavigationManager`, `MissionsPanelUI`, `CalibrationPanelUI`, `SettingsPanelUI`, `PauseModalUI`, dan `FinishModalUI`.

### Hasil Verifikasi Play Mode (`Unity 6000.3.7f1`)
- **Penghapusan `// TODO [SYNC-AUDIO]`:** `0` sisa penanda di seluruh `Assets/_Scripts/`.
- **`MainMenu_Lobby.unity` Play Mode:** `BGM_Lobby_FolkRound` langsung aktif (`playing=True`, `clips=30`, `AudioListener=True`), dan `AudioSystemVerificationRunner.RunPlayModeVerificationAsync()` lulus **`ALL AUDIO SYSTEM CHECKS PASSED (30/30 clips, Mixer buses, dB curve, Crossfade, & SFX triggers 100%)`** (`0` console errors, `0` console warnings).
- **Transisi `MainMenu_Lobby` $\rightarrow$ `MainIsland_Gameplay` & Proximity Hysteresis:** BGM otomatis beralih ke `BGM_Flight_CelticImpulse`; uji jarak ke Zephyria `< 250 m` (`200 m`) memicu `BGM_Crisis_Zephyria_HimalayanAtmosphere`, jarak `265 m` mempertahankan krisis (hysteresis), dan `300 m` (`> 280 m`) memulihkan `BGM_Flight_CelticImpulse`.
- **Non-Regresi:** `MomentumFlightVerificationRunner` (**100% PASS**) dan `Session5GameLoopVerificationRunner` (**100% PASS**) dengan `0` console errors & `0` console warnings; scene aktif dikembalikan ke `MainMenu_Lobby.unity`.

---

## 2026-11-24 — Penyempurnaan Mekanisme Kontrol (`#07 Fly Up`, `#06/#06B Flap`, `#09/#10`, `#14`, `#15C`, & Tracking Responsiveness)

### Akar Masalah yang Ditemukan
1. **Tabrakan & Kerapuhan `#07 Fly Up` (Open Y-Pose):**
   - `FlyDownClassifier` (`#11 Y-Dive`, Prioritas 4) mencegat `#07 Fly Up` (Prioritas 6) ketika siku pemain menekuk alami (`132°–152°`) di sekitar ketinggian bahu (`le[1] >= ls[1] - 0.02 * tl`).
   - `FlyUpClassifier` tidak memiliki *exit hysteresis* / *grace hold* pada kecepatan pergelangan (`< 0.35`), toleransi siku terhadap bahu (`+0.05 * tl`), maupun sudut belok (`<= 24°`), sehingga getaran kecil atau *bank turn* sambil mendaki memutus `FLY_UP`.
   - Legacy Path 2 (`entry_chest_elevation = 1.74` + flapping) membajak kepakan `#06` / `#06B Steep Climb Flap` pada pemain berproporsi tinggi/ramping menjadi `FLY_UP` yang menguras momentum hingga *stall*.
2. **Respon Lengan, Crop MediaPipe, & Pembalikan Kepakan:**
   - Filter One-Euro di `pose_stabilizer.py` (`lw/rw: 0.40 Hz / 0.12`, `le/re: 0.40 Hz / 0.06`) terlalu teredam (`alpha ~ 0.077` saat diam) sehingga memperlambat deteksi kepakan, Y-Pose, dan putaran propeler.
   - `HybridPoseTracker` memakai padding atas `+12%` yang berisiko memotong tangan terangkat (`#07`, `#15C`) serta menimpa `conf` wrist saat kolaps ke siku akibat *motion blur* (`#14`).
   - `FlapStrokeClassifier` hanya menghitung ulang `flap_power` pada pembalikan `UPSTROKE -> DOWNSTROKE`, belum pada `DOWNSTROKE -> UPSTROKE`.
   - `MouthTracker` mengunci `_auto_baseline_done` setelah 15 frame meski kepala masih bergerak (`spread > 4°`), dan `UDPPoseReceiver._isHandsHighUp` (`#15C`) memakai ambang `0.14f` mentah tanpa normalisasi panjang torso.

### Perubahan yang Diimplementasikan
1. **`ARDragonSkyGuardian_CV/motion_recognizer.py` & `cv_locomotion_config.json`:**
   - **`FlyDownClassifier`:** Mengetatkan syarat siku `#11 Y-Dive` (`le[1] >= ls[1] + 0.005 * tl_safe`) serta menambahkan guard `not looks_like_open_y` agar `#07 Open Y-Pose` bersiku santai tidak pernah dibajak `#11`.
   - **`FlyUpClassifier`:** Menambahkan *exit hysteresis* + *grace hold* `0.15s`, melonggarkan toleransi siku (`+0.08 / +0.12 * tl_safe`), sudut siku (`126°` masuk / `118°` keluar), dan kemiringan belok (`32°`/`35°`), serta menaikkan ambang Path 2 (`1.84 / 1.76`, terkalibrasi `+0.14 / +0.07`) dan menonaktifkannya saat `head_pitch > 0.12` agar `#06B` aman.
   - **`FlapStrokeClassifier` & `HoverClassifier`:** Menghitung `flap_power` simetris pada pembalikan `DOWNSTROKE -> UPSTROKE`, menyesuaikan `HOLD_WINDOW_SEC = 0.42s`, mempercepat transisi `Hover -> Low-A` (`straight_stationary` mulai `142°` saat tangan rendah), dan memperpendek jendela rata-rata siku `HoverClassifier` (`maxlen = 4`).
2. **`ARDragonSkyGuardian_CV/pose_stabilizer.py`, `hybrid_pose_tracker.py`, `mouth_tracker.py`, `player_calibrator.py`, & `active_cv_sender.py`:**
   - Menyetel ulang `RobustKeypointTracker` (`ls/rs/lh/rh/nose: 0.35 Hz / 0.03`, `le/re: 0.65 Hz / 0.18`, `lw/rw: 0.75 Hz / 0.45`).
   - Memperlebar crop atas `HybridPoseTracker` (`pad_top = +28%`), menolak lonjakan ekstrem MP terhadap YOLO ber-confidence tinggi (`>= 0.70`), dan mempertahankan confidence rendah saat wrist kolaps blur agar `recover_motion_blurred_wrists()` tetap aktif untuk `#14`.
   - Memperbaiki *sliding window* auto-baseline `MouthTracker` (`[-64°, +8°]`) dan mengevaluasi `face_result` / `head_pitch` sebelum `gesture_sm.update()` di `active_cv_sender.py` (nol jeda 1 frame).
3. **`DragonFlightController.cs` & `UDPPoseReceiver.cs`:**
   - Meluruhkan `_takeoffFlapTimer` secara bertahap (`-dt * 2f`) alih-alih reset instan saat jeda 1 frame; menambahkan trim pitch kepala halus pada `#07 Fly Up` dan menghapus *double-lerp* saat stall; serta menormalisasi deteksi `#15C` (`_isHandsHighUp`) terhadap `torsoLenSafe` di `UDPPoseReceiver.cs`.

### Hasil Verifikasi
- **Python CV (`ARDragonSkyGuardian_CV/venv/Scripts/python.exe`):** 7/7 suite lulus 100% (`test_all_17_poses_alignment.py` termasuk 3 kasus edge `#07` santai, `#07 + Bank Turn`, & `#06B` bertubuh tinggi; `test_momentum_and_control_revision.py`; `test_additional_gestures.py`; `test_e2e_flight_physics.py`; `test_phase1_validation.py`; `test_phase5_validation.py`; `test_tahap1_face_tracking.py`).
- **Unity 6 Play Mode (`MainIsland_Gameplay.unity`):** `MomentumFlightVerificationRunner.RunVerificationMenu()` -> **`ALL MOMENTUM & FLIGHT PHYSICS CHECKS PASSED (100%)`** (`0` console errors, `0` console warnings); scene aktif dikembalikan ke `MainMenu_Lobby.unity`.

---

## 2026-11-24 — Setup & Integrasi Graphify Code Knowledge Graph (`[[laptop-a/handoff-laptop-b/planning_graphify_laptop_b]]`, [[Setup Graphify Code Graph Laptop B]])

### Perubahan yang Diimplementasikan
1. **Instalasi CLI `graphifyy` (`v0.9.110`):** Memasang `graphifyy` beserta `tree-sitter-c-sharp`, `tree-sitter-python`, `networkx`, dan `rapidfuzz` pada environment Python 3.10 Laptop B.
2. **Ekstraksi AST Terarah (`--code-only`, Tanpa LLM API):**
   - **C# (`AR Dragon Sky Guardian/Assets/_Scripts`):** `33` file kode $\rightarrow$ `937` node, `2146` edge, `39` komunitas (`.meta` otomatis dilewati).
   - **Python (`ARDragonSkyGuardian_CV`):** `27` file kode $\rightarrow$ `397` node, `1247` edge, `17` komunitas (`venv/` otomatis dikecualikan via `.gitignore`).
   - **Penggabungan & Klasterisasi (`merge-graphs` + `cluster-only --no-label`):** Dihasilkan `1334` node, `3393` edge, dan `56` komunitas di `brain/laptop-b/code-graph/` (`graph.json` `1.75 MB`, `GRAPH_REPORT.md` `12.7 KB`, `graph.html` `1.50 MB`, `.graphify_analysis.json` `107 KB`).
3. **Portal Navigasi Obsidian:** Membuat `brain/laptop-b/code-graph/index.md` yang menautkan `[[GRAPH_REPORT]]` dan `graph.html` untuk navigasi Graph View / Juggl di Obsidian.

### Hasil Verifikasi CLI
- `graphify explain "DragonFlightController" --graph brain/laptop-b/code-graph/graph.json`: Berhasil menampilkan `Community 8`, `Degree: 88`, serta koneksi keluar/masuk (`VillageZoneController`, `IPoseProvider`, `InGameFlightHUD`, `DragonBodyAnimator`, `DragonWingAnimator`, `GameManager`, dll.).
- `graphify path "UDPPoseReceiver" "DragonWingAnimator" --undirected --graph brain/laptop-b/code-graph/graph.json`: Berhasil melacak jalur `2` hop (`UDPPoseReceiver <--references-- DragonWingAnimator.ResolveDependencies() <--method-- DragonWingAnimator`).
- `graphify god-nodes --top 5 --graph brain/laptop-b/code-graph/graph.json`: `UDPPoseReceiver` (99), `DragonFlightController` (88), `VillageZoneController` (73), `InGameFlightHUD` (63), `IPoseProvider` (63).
- Ukuran `graph.html` adalah `1.50 MB` (`< 5 MB`), sehingga aman ikut di-commit ke repository `brain/`.

---

## 2026-11-24 — Perpanjangan Timer Stage (5/10/15 Menit) & Arsitektur Hibrida YOLOv8 + MediaPipe Pose ([[Timer Stage dan CV Hibrida MediaPipe]], `[[laptop-a/cv-control/analisis_dan_solusi_jitter_glitch_cv]]`, `[[laptop-a/gameplay-mechanics/arsitektur_sesi_5_gameloop_dan_scoring]]`)

### Perubahan yang Diimplementasikan
1. **Perpanjangan Durasi Timer Stage (Unity 6 C#):**
   - **`GameManager.cs` & `VillageZoneController.cs`:** Memperbarui batas waktu default stage pada `GetStageTimeLimit(stageIndex)` dan field default menjadi Stage 1 = `300f` detik (5 Menit), Stage 2 = `600f` detik (10 Menit), Stage 3 = `900f` detik (15 Menit), dan Free Play = `0f` ($\infty$).
   - **`MissionsPanelUI.cs` & `UISceneAndPrefabBuilder.cs`:** Memperbarui label tag durasi kartu Lobby (`SCREEN 00`) menjadi `"1 VILLAGE · 5 MIN"` (`300s`), `"2 VILLAGES · 10 MIN"` (`600s`), dan `"3 VILLAGES · 15 MIN"` (`900s`).
   - **`MainIsland_Gameplay.unity` & `MainMenu_Lobby.unity`:** Menyinkronkan nilai serialized scene (`GameManager.stageTimeLimitSeconds = 300`, `timeRemaining = 300`, dan `LimitText` kartu Lobby) serta menyimpan kedua scene.
2. **Arsitektur Hibrida YOLOv8 + MediaPipe Pose (`ARDragonSkyGuardian_CV/`):**
   - **`hybrid_pose_tracker.py` (Baru):** Mengimplementasikan kelas `HybridPoseTracker` yang memuat `pose_landmarker_lite.task` melalui `mediapipe.tasks.vision.PoseLandmarker` (`IMAGE` mode, dengan fallback ke `mp.solutions.pose` bila tersedia). Metode `refine_keypoints_with_mediapipe()` memotong ROI bounding box pemain YOLO dengan margin `+12%` dan memetakan landmark MediaPipe siku & pergelangan (`MP 13..16`) ke indeks COCO (`7..10`) saat `visibility > 0.35`.
   - **`active_cv_sender.py`:** Menginisialisasi `HybridPoseTracker("pose_landmarker_lite.task")` dan menyempurnakan `raw_kpts` dari bounding box pemain utama `boxes[best_idx]` sebelum masuk ke `RobustKeypointTracker`, `BiometricNormalizer`, `GestureStateMachine`, dan overlay preview.
   - **`pose_stabilizer.py`:** Menyetel ulang `RobustKeypointTracker` One-Euro Filter (`ls`/`rs`/`lh`/`rh`/`nose`: `0.30 Hz / 0.03`, `le`/`re`: `0.40 Hz / 0.06`, `lw`/`rw`: `0.40 Hz / 0.12`), menaikkan ambang confidence (`CONFIDENCE_MIN = 0.28`, `BLUR_WRIST_CONF_MIN = 0.12` khusus dalam radius siku), dan memberlakukan *Kinematic Forearm Clamping* (`1.10 * ref_forearm_length` berbasis rasio adaptif `DEFAULT_FOREARM_RATIO = 0.72` pada seluruh tingkat confidence).
   - **Penyesuaian Test Suite Python:** Menyamakan langkah jeda loop test foreshortening di `test_all_17_poses_alignment.py` (`0.032s` $\to$ `0.025s` agar timer tick Windows tetap sesuai target `~3.3 rad/s`) dan ambang 1-frame smooth filter di `test_phase5_validation.py` (`0.42` $\to$ `0.41` sesuai tuning `0.40 Hz / 0.12`).

### Hasil Verifikasi
- **Python CV (`ARDragonSkyGuardian_CV/venv/Scripts/python.exe`):**
  - `test_all_17_poses_alignment.py`: **ALL 17 POSE ALIGNMENT TESTS PASSED 100%**.
  - `test_momentum_and_control_revision.py`: **ALL MOMENTUM & CONTROL REVISION TESTS PASSED 100%**.
  - `test_additional_gestures.py`: **10/10 SKENARIO UJI BERHASIL 100%**.
  - `test_e2e_flight_physics.py`: **6/6 SUITES PASSED (100%)**.
  - `test_phase1_validation.py`: **8/8 TESTS PASSED (100%)**.
  - `test_phase5_validation.py`: **7/7 TESTS PASSED (100%)**.
  - `test_tahap1_face_tracking.py`: **7/7 TESTS PASSED (100%)**.
  - Uji startup live webcam headless `active_cv_sender.py --headless --cam 0` pada `USB2.0 HD UVC WebCam (1280x720)` + CUDA YOLOv8n-pose + `HybridPoseTracker` (`pose_landmarker_lite.task`) aktif dan berjalan bersih tanpa exception.
- **Unity 6 Play Mode (`6000.3.7f1`):**
  - `MainIsland_Gameplay.unity`: Kapsul HUD `MissionCapsule` terverifikasi `05:00 · VILLAGES 0/1` (`300s`) pada Stage 1, `10:00 · VILLAGES 0/2` (`600s`) pada Stage 2, dan `15:00 · VILLAGES 0/3` (`900s`) pada Stage 3.
  - `Session5GameLoopVerificationRunner.RunVerificationMenu()`: **`ALL SESSION 5 GAME LOOP, COUNTDOWN, NARRATIVE FREEZE, GLOBAL VITALITY STARS, UNLOCK & FINISH MODAL CHECKS PASSED (100%)`** (`0` console errors, `0` console warnings).
  - `MainMenu_Lobby.unity`: Label durasi kartu Lobby terverifikasi `300s` / `600s` / `900s` (`0` console errors, `0` console warnings).

---

## 2026-11-23 — Perbaikan UI In-Game: Full Narrative Isolation Mode & Redesain Side-Docked Challenge HUD (`[[laptop-a/ui-ux/planning_perbaikan_ui_ingame_dan_event_desa]]`, [[Isolasi Narasi dan Side Challenge HUD]])

### Perubahan yang Diimplementasikan
1. **Perbesaran `PF_QuestNarrativeBox.prefab` (`UISceneAndPrefabBuilder.BuildQuestNarrativePrefab`):**
   - Root diubah ke anchor *Bottom-Center* `(0.5, 0)`, `anchoredPosition = (0, 36)`, `sizeDelta = (1440, 300)` untuk keterbacaan *exergaming* jarak `1.5–2.5 m`.
   - `SpeakerBanner` (`UI_Banner_SpeakerTag_Blank`): `420 × 52 px`, font `Fredoka-Bold SDF 28 pt`.
   - `EmblemCard`: `160 × 180 px`, ikon `96 × 96 px` + nama biome `Fredoka-SemiBold SDF 18 pt`.
   - `NarrativeBodyText`: `880 × 134 px`, font `Nunito-ExtraBold SDF 34 pt` dengan `lineSpacing = 20` (`1.2×`).
   - `WhatToDoStrip` (`880 × 58 px`): soket `46 × 46 px` + teks petunjuk `Fredoka-Bold SDF 26 pt`.
   - `LineCounterPill` (`UI_Tag_Stage_Gold_Blank`, `288 × 44 px`, `Fredoka-Bold SDF 20 pt`) & `AutoPlayWidget` (`CountdownTimerText` `Fredoka-Bold SDF 24 pt`, bar pengisi `CountdownFill` tinggi `14 px`).
2. **Full Narrative Isolation Mode (`InGameFlightHUD.cs`, `VillageZoneController.cs`, `Session4_VillageBuilder.cs`):**
   - Memperbarui `SetNarrativeIsolationMode(bool active)` dan fallback `ResolveDependencies()` + `EnsureHudIsolationRefsWired()`: saat `active == true`, menyembunyikan `MissionCapsule`, `Btn_EscPause`, `GlobalVitalityBar`, `TelemetryDock`, `MomentumDock`, `PIP_CameraControlsCard`, `PIP_CameraPanel`, `WaypointCompass`, serta *world-space billboard markers* (`HideAllWorldMarkers()`), menyisakan **hanya** `QuestObjectivePanel` dan `PF_QuestNarrativeBox`.
3. **Redesain Side-Docked Challenge HUD (`PF_VillageChallengeOverlay.prefab`, `VillageChallengeUI.cs`, `InGameFlightHUD.cs`, `VillageZoneController.cs`):**
   - Mengganti modal tengah `720 × 670 px` dengan panel samping kanan *Middle-Right* `(1.0, 0.5)`, `anchoredPosition = (-30, 0)`, ukuran `400 × 620 px` (`UI_Panel_Parchment_Calib_9Slice`).
   - Komposisi: `HeaderBanner` (`UI_Banner_Stepped_SubPanel_Blank` `360 × 48 px`, `Fredoka Bold 22 pt`) + `CrisisSubtitle` (`Nunito ExtraBold 15 pt`), `PoseIndicatorCard` (`360 × 220 px` dengan ikon pose `84 × 84 px`, komando `Fredoka Bold 28 pt`, hint `15.5 pt`, dan badge live feedback `320 × 36 px` `● GERAKAN TERDETEKSI!` vs `○ MENUNGGU GERAKAN...`), progress bar `360 × 48 px` berhaluskan `Mathf.SmoothDamp` + label numerik `22 pt`, serta `EscapeHintChip` (`360 × 38 px`).
   - Menambahkan `SetVillageChallengeMode(bool active)` dan `RestoreNormalFlightHUD()` di `InGameFlightHUD.cs` serta menghubungkan deteksi gestur real-time di `VillageZoneController.cs` (`SetGestureFeedback(isFlapping / true / _isMouthWaterActive / handsHighUp)` + label progres No-Decay).
   - Menambahkan re-wiring otomatis `VillageZoneController` & `GameManager` pada `UISceneAndPrefabBuilder.UpgradeGameplayScene()` setelah `BuildAll()`.

### Hasil Verifikasi Play Mode (`MainIsland_Gameplay.unity`)
- Kompilasi skrip & `UISceneAndPrefabBuilder.BuildAll()`: `0` errors, `0` warnings baru.
- **Narrative Isolation Mode (Pad Zephyria):** Hanya `QuestObjectivePanel` & `PF_QuestNarrativeBox` (`1440×300` at `(0, 36)`) yang aktif; seluruh HUD lain & billboard 3D tersembunyi.
- **Side-Docked Challenge HUD (Tantangan Kabut Zephyria):** Panel kanan `400×620` at `(-30, 0)` aktif bersama `QuestObjectivePanel`, `MissionCapsule`, & `Btn_EscPause`; `GlobalVitalityBar`, `TelemetryDock`, `MomentumDock`, `PIP_CameraPanel`, & `WaypointCompass` tersembunyi; naga di tengah layar 100% bebas halangan; badge `GERAKAN TERDETEKSI!` menyala hijau saat mengepak dan bar progres naik (`2/10 KEPAKAN (22%)`, `9/10 KEPAKAN (99%)`) tanpa decay.
- `Session4VerificationRunner.RunPlayModeVerificationAsync()`: **`ALL SESSION 4 VILLAGE, SUB-MARKER, AUTO-HOVER, ESCAPE, BILLBOARD & UI ISOLATION CHECKS PASSED (100%)`** (`0` console errors/warnings).
- `Session5GameLoopVerificationRunner.RunPlayModeVerificationAsync()`: **`ALL SESSION 5 GAME LOOP, COUNTDOWN, NARRATIVE FREEZE, GLOBAL VITALITY STARS, UNLOCK & FINISH MODAL CHECKS PASSED (100%)`** (`0` console errors/warnings).
- **Tangkapan Layar Mandiri (`docs/figures/raw_screenshots/`):**
  - `docs/figures/raw_screenshots/SS_UI_Narrative_Isolation.png`
  - `docs/figures/raw_screenshots/SS_UI_SideDocked_Challenge_Waiting.png`
  - `docs/figures/raw_screenshots/SS_UI_SideDocked_Challenge_Flapping.png`

---

## 2026-11-23 — Penyetelan Ulang Sensitivitas `#14 Propeller Dash` (`motion_recognizer.py`, `pose_stabilizer.py`, `cv_locomotion_config.json`)

### Root Cause Over-Sensitivitas
1. `PropellerRotationClassifier` (`motion_recognizer.py`) sebelumnya memakai `est_omega = max(omega_l, omega_r)`, `radius_ok` dengan `or`, `has_2d_loop` dengan `or`, bypass sisi lambat hingga `0.20 rad/s`, serta `effective_accum = max + 0.5*min`, sehingga ayunan 1 lengan atau gerakan kecil ~82° sudah menembus `2.15 rad`.
2. Gate 2D orbit (`span >= 0.055`, `aspect >= 0.20`, `len >= 4`) dan spread bahu (`spread_horiz_enter = 0.22`, `spread_vert_enter = 0.68`) terlalu longgar sehingga *1D flapping*, *T-pose* condong, atau goyangan tangan ringan bisa memicu Priority-1 `PROPELLER_DASH`, lalu ditahan oleh `grace_hold_sec = 0.42s` (`or has_2d_loop`).
3. `recover_motion_blurred_wrists()` (`pose_stabilizer.py`) aktif pada `c_w < 0.42` & `elbow_span_sw >= 1.08` dengan ambang `diff >= 13.0`, berisiko menyuntikkan lonjakan keypoint latar ke estimator rotasi saat keypoint masih wajar.

### Perbaikan
- **`cv_locomotion_config.json` & `motion_recognizer.py` (`PropellerRotationClassifier`):**
  - Mewajibkan keaktifan **bilateral kedua lengan**: `avg_omega = 0.5 * (omega_l + omega_r) >= 2.85 rad/s`, `min(omega_l, omega_r) >= 1.65 rad/s` (menghapus bypass `0.20 rad/s`), `avg_accum >= 2.05 rad` dan `min_single_accum >= 1.90 rad` (tanpa penjumlahan `max + 0.5*min`), `both_2d_loops` (`is_2d_l and is_2d_r`) untuk entry, serta `radius_ok` di kedua sisi (`mean_rad >= 0.135 * sw`).
  - Memperketat 2D orbit (`maxlen = 16`, `len >= 7`, `min_2d_span_sw = 0.13`, `min_2d_aspect = 0.28`) dan spread lengan atas horizontal (`spread_horiz_enter = 0.40`, `spread_vert_enter = 0.46`, `elbow_span >= 1.65`).
  - Menambahkan konfirmasi entry `min_entry_frames = 2`, menaikkan deadzone `dtheta` (`0.020 -> 0.032`), mempercepat peluruhan diam (`0.86`) dan reset pembalikan 2 frame (`rev_limit = 2`), serta memangkas `grace_hold_sec` (`0.42s -> 0.18s`) tanpa klausa `or has_2d_loop`.
- **`pose_stabilizer.py` (`recover_motion_blurred_wrists`):** Memperketat `elbow_span_sw >= 1.45`, `c_w < 0.28` / `dist_we_sw < 0.14`, dan `diff >= 18.0`.
- **`test_all_17_poses_alignment.py`:** Menambahkan uji penolakan over-sensitivitas (putar 1 lengan, kepakan 1D ber-goyang X, putaran lambat `< 2 rad/s`, dan penurunan lengan menuju Low-A).

### Hasil Verifikasi (`ARDragonSkyGuardian_CV/venv/Scripts/python.exe`)
- `test_all_17_poses_alignment.py`: **ALL 17 POSE ALIGNMENT TESTS PASSED 100%** (termasuk penolakan over-sensitivitas & ketahanan *motion blur*).
- `test_momentum_and_control_revision.py`: **ALL MOMENTUM & CONTROL REVISION TESTS PASSED 100%**.
- `test_additional_gestures.py`: **10/10 SKENARIO UJI BERHASIL 100%**.
- `test_e2e_flight_physics.py`: **6/6 SUITES PASSED (100%)**.

---

## 2026-11-23 — Peningkatan Animasi Naga Fanamir Tahap 1: Anatomi Kaki Terbang Aerodinamis & Elevasi Dada Hover (`DragonBodyAnimator.cs`, `DragonAnimationVerificationRunner.cs`)

### Eksekusi Utama (`[[laptop-a/visual-vfx/planning_perbaikan_animasi_dan_pose]]`, `[[laptop-a/visual-vfx/prompt_eksekusi_animasi_tahap_1_laptop_b]]`)
1. **Perbaikan Anatomi Kaki Terbang (*Flight Tuck*, `legTuckWeight -> 1.0f`, Kecepatan $\ge 12\text{ m/s}$):**
   - Menambahkan dukungan sumbu roll lokal (`AxisRoll`) dan *inward roll* simetris (`sideSign = +1` `.L`, `-1` `.R`) pada `DragonBodyAnimator.cs`.
   - **Kaki Depan (*Forelegs* — `Shoulder_Front`, `UpperArm_Front`, `Forearm_Front`, `Paw_Front`):** Menyelaraskan sudut lipatan (`FrontTuckPitchDeg = { -6f, -62f, -32f, -42f }`, `FrontTuckRollInDeg = { 4f, 0f, 0f, 0f }`) sehingga kaki depan terlipat rapi ke arah belakang menempel di bawah rongga dada (*under chest*) dengan cakar menghadap ke belakang (`pawY = -0.13`) tanpa menjuntai ke bawah.
   - **Kaki Belakang (*Hindlegs* — `Thigh_Hind`, `Shin_Hind`, `Metatarsus_Hind`, `Foot_Hind`):** Menyelaraskan sudut ekstensi (`HindTuckPitchDeg = { -88f, 32f, -52f, -65f }`, `HindTuckRollInDeg = { 10f, 0f, 0f, 0f }`) sehingga paha, tulang kering, dan cakar belakang melurus merapat sejajar di sepanjang pangkal ekor (`footZ = -0.98`, *streamlined rearward pose*).
2. **Postur Hover (*Air Idle* / `IsHovering` / `IsHoverActiveBrake` / Kecepatan $< 6\text{ m/s}$):**
   - **Elevasi Dada (`+16°`):** Menambahkan elevasi pitch dada $+8^\circ$ pada `Spine_01` dan $+8^\circ$ pada `Spine_02` yang dihaluskan dengan `Mathf.SmoothDamp` (`0.25s`) agar tubuh naga tegap memperlihatkan permata dada (*Chest Gem*) ke arah depan-bawah.
   - **Buoyant Hover Bobbing:** Menambahkan osilasi apung vertikal lembut (`0.40 Hz`, amplitudo `0.18 m`, `bobSpan = 0.36 m`) pada `visualRoot.localPosition` (di-reset bersih di `OnDisable()`).
   - **Kaki Hover Drop (`legTuckWeight -> 0.0f`):** Memperbarui sudut rileks kaki depan (`{ 0f, -25f, 8f, -20f }`) dan kaki belakang (`{ -35f, 12f, -16f, -38f }`) sehingga keempat kaki menjuntai santai siap menapak daratan.

### Hasil Verifikasi Play Mode (`Unity 6000.3.7f1`) & Screenshot Mandiri
- Kompilasi skrip Unity: `0` errors, `0` warnings baru (domain reload `codeLoadCount = 21`).
- Scene `/Dragon_Player` terverifikasi memiliki `DragonBodyAnimator` di `MainIsland_Gameplay.unity` dan `Test_Dragon_Flight.unity`; scene aktif dikembalikan ke `MainMenu_Lobby.unity` (Build Index `0`).
- `DragonAnimationVerificationRunner.RunPlayModeVerificationAsync()` di `MainIsland_Gameplay.unity`: **`ALL DRAGON BODY ANIMATION CHECKS PASSED (100%)`** (`0` console errors, `0` console warnings):
  - `Jaw(closed=0.0, partial=-14.0, open=-30.0, roar=-25.0)`
  - `Tail(right tip=20.0/root=3.0, left tip=-20.0, undulation=48.5)`
  - `Spine(dive=-6.0, flyUp=5.0, hoverChest=+16.0°, bobSpan=0.36m)`
  - `Legs(dive spd=20.8 tuck=1.00 thigh=88° footZ=-0.98 pawY=-0.13, hover spd=0.0 tuck=0.00 thigh=35° transition=0.43s)`
  - `WingRegimes=[GroundedIdle,GentleDescent,Glide,TuckDive,FlyUp,Hover]`
- Non-regresi `MomentumFlightVerificationRunner.RunPlayModeVerificationAsync()`: **`ALL MOMENTUM & FLIGHT PHYSICS CHECKS PASSED (100%)`** (`0` console errors, `0` console warnings).
- **Tangkapan Layar Mandiri (`docs/figures/raw_screenshots/`):**
  - `docs/figures/raw_screenshots/SS_Animasi_Tahap1_FlightLegs.png`
  - `docs/figures/raw_screenshots/SS_Animasi_Tahap1_HoverChest.png`

---

## 2026-11-22 — Perbaikan Hilangnya Kontrol Naga Lintas Scene (`MainMenu_Lobby` -> `MainIsland_Gameplay`) & Bug Kilau VFX Saat Takeoff

### Akar Masalah yang Ditemukan
1. **Kontrol Naga Mati Saat Game Mulai & Saat Kembali ke Kalibrasi (Khusus Start dari `MainMenu_Lobby`):**
   - Ketika game dijalankan dari `MainMenu_Lobby.unity` menggunakan webcam (`active_cv_sender.py`), `LobbyControllers/UDPPoseReceiver` mengikat port `5052` dengan `ReuseAddress = true`.
   - Saat transisi ke `MainIsland_Gameplay.unity`, `Dragon_Player/UDPPoseReceiver` mengikat port `5052` baru sementara socket `0.0.0.0:5052` lama milik proses Unity masih tertahan (`netstat` mengonfirmasi **2 socket `0.0.0.0:5052` aktif** di PID Unity sementara `5053` hanya 1). Akibatnya paket UDP `5052` terserap oleh socket lama (`UDP.IsConnected = False` di `MainIsland_Gameplay` meskipun `PythonProcessManager.IsProcessRunning = True` dan video `5053` tetap `True`). Kondisi ini juga menyebabkan saat membuka halaman Kalibrasi pasca-start, preview naga ikut tidak menerima sinyal pose.
   - Selain itu, keberadaan `PythonProcessManager` non-persistent pada `LobbyControllers` di `MainMenu_Lobby.unity` berisiko membuat scene object mengambil alih `PythonProcessManager.Instance` sebelum `[DragonSessionHost]` terbentuk dan mematikan proses Python saat unload scene.
2. **Bug VFX Cahaya Mengkilat Saat Naga Takeoff:**
   - `EffekseerSettings.asset` memiliki `enableDepth: 1` pada jalur non-raster URP RenderGraph Unity 6 (`PC_RPAsset`), yang memicu `CommandBuffer: temporary render texture not found while executing (SetGlobalTexture)` tepat saat emitter VFX pertama kali aktif ketika takeoff/belok.
   - `Session4_VillageBuilder.cs` sebelumnya mencari `VFX_Sockets` di root `Dragon_Player` alih-alih di bawah `Visual_Dragon`, menyisakan **3 set `VFX_Sockets` bertumpuk (`18` emitter di `MainIsland_Gameplay.unity`, harusnya `8`)**.
   - `DragonVfxController.cs` langsung memicu `VFX_Flight_WingtipTrails` (ber-sprite `T_Glow01` `size=0.85`, `emissive=1.75`) saat `IsTakingOff` bila ada input belok, dan `VFX_Flight_SonicDashShockwave` memiliki layer `Boom flash` (`radius=3.8, emissive=1.8`).

### Solusi yang Diimplementasikan
1. **Persistent Shared Socket UDP 5052 (`UDPPoseReceiver.cs`):**
   - Memindahkan socket listener `5052` dan thread penerima ke socket statis persistent (`_sharedUdpClient` + `_sharedReceiveThread`, `ExclusiveAddressUse = true` tanpa `ReuseAddress`), dikelola oleh satu `_activeReceiver` lintas pergantian scene dan ditutup bersih hanya saat `SubsystemRegistration`, `ExitingPlayMode`, atau `OnApplicationQuit`.
2. **Inisialisasi Host Persistent Sebelum Scene Load (`DragonGameSettings.cs`, `DragonWingAnimator.cs`, `UISceneAndPrefabBuilder.cs`, `MainMenu_Lobby.unity`):**
   - Menambahkan `EnsurePersistentSessionHost()` ke `DragonGameSettings.EnsureInit()` (`RuntimeInitializeLoadType.BeforeSceneLoad`), menghapus `PythonProcessManager` redundan dari `LobbyControllers` (`MainMenu_Lobby.unity` & `UISceneAndPrefabBuilder.cs`), dan memanggil `ResolveDependencies()` secara konsisten di `DragonWingAnimator.OnEnable()`.
3. **Pembersihan VFX Takeoff & RenderGraph (`EffekseerSettings.asset`, `DragonFlightController.cs`, `DragonVfxController.cs`, `Session4_VillageBuilder.cs`, `VFX_Flight_WingtipTrails.py`, `VFX_Flight_SonicDashShockwave.py`, `MainIsland_Gameplay.unity`):**
   - Mengatur `enableDepth: 0` di `EffekseerSettings.asset` sehingga warning `SetGlobalTexture` RenderGraph hilang `100%`.
   - Membersihkan `2` duplikat `VFX_Sockets` di `MainIsland_Gameplay.unity` (`18 -> 8` emitter) dan memperbaiki pembersihan di `Session4_VillageBuilder.cs`.
   - Menahan `WingtipTrails` saat `flightController.IsTakingOff`, serta menala ulang `VFX_Flight_WingtipTrails.py` (`size=0.38, emissive=0.90`) dan `VFX_Flight_SonicDashShockwave.py` (`Boom flash` `radius=1.8, emissive=1.0`), lalu mengompilasi ulang `.efkefc` via `vfx lint` & `vfx build` ke `Assets/VFX/Effekseer/`.

### Hasil Pengujian & Verifikasi
- **Live Webcam End-to-End (`MainMenu_Lobby` -> `active_cv_sender.py` -> `MainIsland_Gameplay` -> `Pause/Calibration` -> `BackToLobby/Calibration`):**
  - `MainIsland_Gameplay`: `PM=[DragonSessionHost] running=True`, `UDP: conn=True pose=True calib=True state=5`, `VID: conn=True`, `FC: lock=False state=Grounded Look Up`.
  - `netstat`: tepat **1 socket `0.0.0.0:5052`** aktif di PID Unity, lalu bersih `0` socket saat Play Mode berhenti.
  - `Pause -> Calibration` & `BackToLobby -> Calibration`: `UDP.conn=True pose=True`, preview & kontrol tetap aktif.
- **VFX & Physics Verification (`MainIsland_Gameplay.unity`):**
  - Simulasi Takeoff & `WingtipTrails`: `0` warning `SetGlobalTexture`, `[DuringTakeoff] IsTakingOff=True Wingtip=False` -> `[PostTakeoff] IsTakingOff=False Speed=19.50 Wingtip=True`.
  - `DragonAnimationVerificationRunner`: **100% PASS**.
  - `MomentumFlightVerificationRunner`: **100% PASS** (`0` console warnings/errors).

---

## 2026-11-22 — Pencegahan Play Stage Sebelum Kalibrasi Kamera Selesai (`DragonGameSettings.cs`, `LobbyNavigationManager.cs`, `CalibrationPanelUI.cs`, `MissionsPanelUI.cs`, `UISceneAndPrefabBuilder.cs`)

### Masalah & Kebutuhan Fitur
- Pengguna meminta agar pemain **tidak dapat memulai / memainkan stage permainan** apabila kamera webcam pemain belum dikalibrasi.
- Jika stage dimainkan tanpa kalibrasi, baseline postur netral, ambang batas elevasi kepala, rentang kepakan, dan kemudi tidak terdefinisi dengan tepat sesuai postur pemain, yang dapat menyebabkan anomali kontrol penerbangan.

### Implementasi Solusi
1. **Pengecekan Status Terpusat (`DragonGameSettings.cs`):**
   - Menambahkan key `PrefCameraCalibrated = "Fanamir_CameraCalibrated"` dan event statis `OnCalibrationStatusChanged`.
   - Properti `DragonGameSettings.IsCameraCalibrated`:
     - Memeriksa live `UDPPoseReceiver.IsCalibrated` (`_calibStateId == 5.0`). Jika terkalibrasi, otomatis menyimpan ke PlayerPrefs `1` dan memicu event.
     - Jika kamera terhubung (`IsConnected == true`) namun belum terkalibrasi (`IsCalibrated == false`), mengembalikan `false` agar pemain tidak bisa langsung terbang tanpa kalibrasi profil sesi live saat ini.
     - Jika stream idle/offline, mengembalikan nilai tersimpan dari `PlayerPrefs.GetInt(PrefCameraCalibrated, 0) == 1`.
   - Menambahkan metode `ResetCalibrationStatus()` untuk mereset status kalibrasi ke `false`.
2. **Penguncian & Navigasi di `LobbyNavigationManager.cs`:**
   - Menjaga method `LaunchFlightScene()`: jika `!DragonGameSettings.IsCameraCalibrated`, log warning dan hentikan pemuatan scene, alihkan tab otomatis ke `LobbyTab.Calibration`, serta tampilkan instruksi kalibrasi pada `CalibrationPanelUI`.
   - Menambahkan pembaruan badge kamera di pojok kanan atas (`UpdateDynamicCameraBadge()`): menampilkan `"CAM LIVE · BELUM KALIBRASI"` (latar amber) jika kamera aktif namun belum terkalibrasi, dan `"CAM READY"` (hijau) jika sudah terkalibrasi.
   - Memastikan `SwitchTab()` memanggil `missionsPanel.RefreshStageCards()` dan `calibrationPanel.SetInGamePauseMode(false)` sehingga visual kartu selalu sinkron saat pergantian tab.
3. **Penyelarasan Kartu Misi (`MissionsPanelUI.cs`):**
   - Mendengarkan `DragonGameSettings.OnCalibrationStatusChanged` secara reaktif.
   - Label tombol aksi Stage 01, Stage 02, Stage 03, dan Free Play berubah dinamis menjadi `"CALIBRATE"` / `"FREE PLAY · CALIBRATE"` saat uncalibrated, dan `"PLAY"` / `"FREE PLAY · OPEN"` saat calibrated.
   - Jika tombol aksi diklik saat uncalibrated: memunculkan banner status amber `"Kamera belum dikalibrasi! Selesaikan kalibrasi di tab CALIBRATION terlebih dahulu."`, mengalihkan tab ke Calibration, dan mencegah transisi scene.
4. **Penyelarasan Panel Kalibrasi (`CalibrationPanelUI.cs`):**
   - Mendengarkan `DragonGameSettings.OnCalibrationStatusChanged` untuk pembaruan visual instan.
   - Tombol utama penerbangan (`readyToFlyButton`) menampilkan label `"BELUM KALIBRASI"` jika belum dikalibrasi, dan `"READY TO FLY"` jika terkalibrasi.
   - Menambahkan method publik `ShowCalibrationRequiredNotice(string message)` untuk memperbarui teks instruksi kalibrasi secara jelas.
   - Menghubungkan tombol `"RESET DEFAULT"` dengan `DragonGameSettings.ResetCalibrationStatus()` untuk memungkinkan pemain menghapus kalibrasi tersimpan.
5. **Perakitan Scene & Builder (`UISceneAndPrefabBuilder.cs` & `MainMenu_Lobby.unity`):**
   - Memastikan `PythonProcessManager` terpasang pada `LobbyControllers` di scene `MainMenu_Lobby.unity` dan teregistrasi pada `UISceneAndPrefabBuilder.cs`.

### Hasil Pengujian & Verifikasi
- **11-Step Verification Suite di Play Mode `MainMenu_Lobby.unity`:**
  - `[Step 1]` Initial Uncalibrated: **PASS** (`IsCalibrated=False`)
  - `[Step 2]` Stage 1 Action Label: **PASS** (`'CALIBRATE'`)
  - `[Step 3]` Click Stage 1 while uncalibrated -> Switched to tab: **PASS** (`Calibration`)
  - `[Step 4]` ReadyToFly Button Label: **PASS** (`'BELUM KALIBRASI'`)
  - `[Step 5]` Click ReadyToFly while uncalibrated -> Notice shown: **PASS** (`'Kamera belum dikalibrasi! Tekan RECALIBRATE (C)...'`)
  - `[Step 6]` Calibrated State Set: **PASS** (`IsCalibrated=True`)
  - `[Step 7]` Stage 1 Action Label after calibration: **PASS** (`'PLAY'`)
  - `[Step 8]` ReadyToFly Button Label after calibration: **PASS** (`'READY TO FLY'`)
  - `[Step 9]` Reset Default Clicked -> IsCalibrated: **PASS** (`IsCalibrated=False`)
  - `[Step 10]` ReadyToFly Button Label after Reset: **PASS** (`'BELUM KALIBRASI'`)
  - `[Step 11]` Stage 1 Action Label after Reset: **PASS** (`'CALIBRATE'`)
- **Pemuatan Gameplay Scene:**
  - Memverifikasi klik `"PLAY"` saat terkalibrasi berhasil memuat `MainIsland_Gameplay.unity` secara mulus (`0` errors).
- **Python CV Unit Tests:**
  - `test_all_17_poses_alignment.py`: **100% PASS**
  - `test_momentum_and_control_revision.py`: **100% PASS**

---

## 2026-11-22 — Penguatan Kontrol In-Game, Fallback Keyboard, Resiliensi Runtime Domain Reload, & Urutan Scene Menu

### Masalah yang Ditemukan & Diperbaiki
1. **Kontrol In-Game Tidak Merespons Tanpa Webcam / Fallback Keyboard Nonaktif:**
   - Variabel `enableKeyboardFallback` pada `UDPPoseReceiver` di kedua scene gameplay (`MainIsland_Gameplay.unity` dan `Test_Dragon_Flight.unity`) sebelumnya bernilai `false` (0) pasca-audit kontrol webcam.
   - `DefaultFlightInputMapper.cs` memeriksa `_poseProvider.IsConnected && _poseProvider.IsPoseDetected` yang mewajibkan paket socket UDP aktif dari Python. Akibatnya penekanan tombol keyboard tidak memvalidasi status tracking penerbangan.
   - **Solusi:** Menambahkan properti `IsKeyboardOverrideActive` pada `UDPPoseReceiver.cs` dan menghubungkannya ke `DefaultFlightInputMapper.cs` (`bool kbOverride = _poseProvider is UDPPoseReceiver udp && udp.IsKeyboardOverrideActive; bool isTrackingValid = (_isConnected && _isPoseDetected) || kbOverride;`). Mengaktifkan `enableKeyboardFallback = true` di scene dan `UISceneAndPrefabBuilder.cs`. Menyelaraskan blok `#if ENABLE_INPUT_SYSTEM` dan `#else` (W/Space Flap, S Tuck Dive, T Glide, U Fly Up, H Hover, G Gentle Descent, Shift Dash, A/Q/Left Belok Kiri, D/Right Belok Kanan, 1/2/3 Aksi Khusus Desa).
2. **Kerapuhan Socket UDP Windows (`SIO_UDP_CONNRESET`):**
   - Di Windows, pengiriman paket UDP ke port yang belum siap/tertutup dapat menyebabkan OS mengirimkan pesan ICMP Port Unreachable yang memicu `SocketException (10054)` pada thread receive C#, memutus listener UDP secara permanen.
   - **Solusi:** Menambahkan `IOControl((IOControlCode)SIO_UDP_CONNRESET, new byte[] { 0, 0, 0, 0 }, null)` pada socket listener `UDPPoseReceiver.cs`.
3. **Resiliensi Runtime & Domain Reload (`GameManager.cs` & `DragonBodyAnimator.cs`):**
   - `GameManager.Instance` statis tidak di-reset saat script recompilation di PlayMode -> ditambahkan `[RuntimeInitializeOnLoadMethod(RuntimeInitializeLoadType.SubsystemRegistration)]`.
   - `GameManager.BeginStage()` belum memastikan `Time.timeScale = 1f;`, menyebabkan restart dari FinishModal atau script_eval tertahan jika TimeScale masih 0 -> ditambahkan `Time.timeScale = 1f;` di `BeginStage()`.
   - `DragonBodyAnimator.cs` mengalami `NullReferenceException` pada `UpdateSpineAndTailAnimation` setelah domain reload karena field array rig non-serialized (`_spine`, `_tail`, `_frontLegs`, `_backLegs`) kembali bernilai null sementara `[SerializeField] rigReady` tetap true -> ditambahkan auto-recache rig otomatis jika field array bernilai null di `OnEnable()` dan `LateUpdate()`, serta null-guard pada metode animasi.
4. **Peringatan Audio Listener & Scene Booting Index:**
   - Memastikan `MainMenu_Lobby.unity` berada pada Build Index `0` (`EditorBuildSettings.scenes`), dan menambahkan komponen `AudioListener` ke `Main Camera` di `MainMenu_Lobby.unity` dan `UISceneAndPrefabBuilder.cs` sehingga peringatan ketiadaan AudioListener di console teratasi 100%.

### Hasil Pengujian & Verifikasi
- **Python CV Unit Tests:**
  - `test_all_17_poses_alignment.py`: **100% PASS** (Seluruh 17 pose, ketahanan motion blur #14 Propeller Dash, clean CV preview).
  - `test_momentum_and_control_revision.py`: **100% PASS** (#09A/#09B, #07 isolasi Y-Pose vs HEAD_PITCH, #06 telemetri, 6 locked poses).
- **Unity Play Mode Verification:**
  - `Session5GameLoopVerificationRunner`: **100% PASS** (`[Session5Verify] ALL SESSION 5 GAME LOOP, COUNTDOWN, NARRATIVE FREEZE, GLOBAL VITALITY STARS, UNLOCK & FINISH MODAL CHECKS PASSED (100%)`).
  - `DragonAnimationVerificationRunner`: **100% PASS** (`[BodyAnimVerify] ALL DRAGON BODY ANIMATION CHECKS PASSED (100%)`).
  - **Console Errors:** `0` errors.

---

## 2026-11-22 — Implementasi Animasi Tubuh Prosedural Naga Fanamir (`DragonBodyAnimator.cs`, `VillageZoneController.cs`, `DragonAnimationVerificationRunner.cs`)

### Eksekusi Utama (`[[laptop-a/visual-vfx/arsitektur_animasi_naga_fanamir]]`, `[[laptop-a/visual-vfx/prompt_eksekusi_animasi_naga_laptop_b]]`, `[[laptop-a/handoff-laptop-b/panduan_screenshot_mandiri_dan_laporan]]`)
1. **Skrip Prosedural `DragonBodyAnimator.cs` (`Assets/_Scripts/Controllers/DragonBodyAnimator.cs`, namespace `DragonWingAR.Controllers`, `[DefaultExecutionOrder(55)]`):**
   - Dipasang pada `/Dragon_Player` di `MainIsland_Gameplay.unity` dan `Test_Dragon_Flight.unity` (serta diproteksi di `Session4_VillageBuilder.cs`), berjalan tepat setelah `DragonFlightController` & `DragonWingAnimator (50)` dan sebelum `DragonHeadLookController (60)` tanpa menyentuh tulang `Wing_*` maupun `Neck_*`/`Head`.
   - *Auto-cache* rig tulang aktual `ARM_Dragon`: rahang (`Bone_Jaw` / `Jaw`), punggung (`Spine_01`, `Spine_02`, opsional `Spine_03`), ekor 5 segmen (`Tail_01..Tail_04`, `Tail_05` / `Tail_05_Fin`), kaki depan (`Shoulder_Front`, `UpperArm_Front`, `Forearm_Front`, `Paw_Front` `.L/.R`), dan kaki belakang (`Thigh_Hind`, `Shin_Hind`, `Metatarsus_Hind`, `Foot_Hind` `.L/.R`).
2. **Artikulasi Rahang Buka Mulut (`Jaw`) & Victory Roar:**
   - Membaca sinyal `MOUTH_OPEN` (`0.0..1.0`) dari `IPoseProvider` / `UDPPoseReceiver`: `< 0.25` tertutup rapat (`0°`), `> 0.40` terbuka lebar (`-30°` pitch lokal), dihaluskan dengan `Mathf.SmoothDamp` (`0.12s`).
   - Menambahkan `PlayVictoryRoar(float seconds)` (`-25°`) yang dipicu otomatis oleh `VillageZoneController.CompleteVillageRestoration()` selama durasi `victoryCelebrateDelay` (`2.5s`).
3. **Liukan Ekor Dinamis (`Tail Sway`), *Cruising Undulation*, & *Spine Arch*:**
   - *Hierarchical wave lag* berlawanan arah kemudi (`Tail_01: 15%`, `Tail_02: 30%`, `Tail_03: 55%`, `Tail_04: 75%`, `Tail_05_Fin: 100%`) dengan kecepatan damping makin melambat ke ujung ekor.
   - *Cruising Undulation* sinusoidal (`0.6 Hz`, amplitudo `6°`, beda fase `0.45 rad`) saat meluncur lurus dan meredup halus saat belok tajam, grounded, atau hover.
   - *Spine Arch* (`Spine_01..02`): melengkung `-6°`/segmen saat `#13 Tuck Dive` dan melenting `+5°`/segmen saat `#07 Fly Up` / `#06B Steep Climb Flap`.
4. **Lipatan Kaki & Cakar Aerodinamis (*Flight Tuck* vs *Hover Drop*):**
   - Kecepatan $\ge 12\text{ m/s}$ (Glide/Dive/Dash) melipat keempat kaki merapat aerodinamis ke bawah tubuh/pangkal ekor (*Flight Tuck*); kecepatan $< 6\text{ m/s}$ atau status `IsHovering` menjuntaikan kaki turun santai (*Hover Drop*) dengan transisi `0.5s` (`MoveTowards` + `SmoothStep`) dan hysteresis `6..12 m/s`, serta posisi menapak netral saat grounded.

### Hasil Verifikasi Play Mode (`Unity 6000.3.7f1`) & Screenshot Mandiri
- `DragonAnimationVerificationRunner.RunPlayModeVerificationAsync()` di `MainIsland_Gameplay.unity`: **`ALL DRAGON BODY ANIMATION CHECKS PASSED (100%)`** (`0` console errors):
  - Rahang: `closed=0.0°` (`MOUTH_OPEN=0.10`), `partial=-14.0°` (`0.32`), `open=-30.0°` (`0.85`), `Victory Roar=-25.0°` -> kembali tertutup.
  - Ekor & Kemudi: belok kanan `tip=+20.0° / root=+3.0°`, belok kiri `tip=-20.0°`, *cruising undulation* range `48.5°`.
  - Punggung: `TuckDive=-6.0°`, `FlyUp=+4.9°`.
  - Kaki: `TuckDive (20.8 m/s, tuck=1.00, thigh=72°)` $\rightarrow$ `Hover (0.0 m/s, tuck=0.00, transisi=0.46s)`.
  - Non-regresi sayap: seluruh regime `[GroundedIdle, Glide, TuckDive, FlyUp, Hover]` teramati utuh.
- Non-regresi `MomentumFlightVerificationRunner.RunPlayModeVerificationAsync()` di `MainIsland_Gameplay.unity`: **`ALL MOMENTUM & FLIGHT PHYSICS CHECKS PASSED (100%)`** (`0` console errors).
- **Tangkapan Layar Mandiri (`docs/figures/raw_screenshots/`):**
  - `docs/figures/raw_screenshots/SS_Animasi_Dragon_JawOpen.png`
  - `docs/figures/raw_screenshots/SS_Animasi_Dragon_TailSway.png`

---

## 2026-11-22 — Peningkatan Ketahanan Camera Motion Blur pada `#14 Propeller Dash` (`pose_stabilizer.py`, `motion_recognizer.py`, `active_cv_sender.py`, `cv_locomotion_config.json`)

### Temuan Akar Masalah Camera Motion Blur pada `#14 Propeller Dash`
1. **Pembekuan Pergelangan Tangan oleh *Velocity Outlier Gate* (`RobustKeypointTracker.update_joint`):**
   - Saat lengan bawah diputar cepat untuk memicu `#14 Propeller Dash`, *motion blur* kamera menurunkan confidence YOLO pada `lw`/`rw` ke rentang `0.08–0.45` (`< CONFIDENCE_HIGH = 0.50`, atau `< CONFIDENCE_MIN = 0.20`), sekaligus menghasilkan perpindahan antar-frame `step > max_step` (`3.2 * sw_scale * dt`). Akibatnya, `update_joint()` menolak perpindahan tersebut dan mengembalikan `prev_x, prev_y` (membekukan titik pergelangan di tempat).
2. **Kolapsnya Titik Pergelangan ke Siku & Penyusutan Radius Orbit 2D Akibat *Motion Blur* + Perspektif Kerucut 3D:**
   - Pada frame yang mengalami *motion blur* berat, *heatmap* YOLO untuk pergelangan tangan menyatu sepanjang bayangan lengan bawah menuju siku yang stasioner/tajam, atau lintasan putar 3D yang condong ke depan kamera terproyeksi menjadi elips kecil (`radius ~ 0.06–0.15 * sw`) yang titik pusatnya bergeser dari titik siku `le`/`re` (sehingga sudut `atan2(lw - le)` hanya berayun bolak-balik dan tidak pernah mengakumulasi `2\pi`).
3. **Reset Akumulasi Sudut oleh *Single-Frame Blur-Streak Reversal*, *Phase Aliasing* `>126°–180°`, & *Elbow Wobble*:**
   - Lonjakan 1 frame ke ujung belakang bayangan *motion blur* (`|dtheta| >= 0.45`), lompatan fase `126°–235°` setelah 1–2 frame blur terlewat, atau goyangan keypoint siku selama 1 frame (`not upper_arms_spread`) sebelumnya langsung me-reset `accumulated_angle` ke `0.0`.

### Perbaikan & Peningkatan Pipeline CV
1. **Pemulihan Piksel *Elbow-Annulus Motion-Blur Recovery* (`pose_stabilizer.py`, `active_cv_sender.py`):**
   - Menambahkan `recover_motion_blurred_wrists()` yang mengevaluasi *temporal frame difference* berbobot gradien spasial frame saat ini (`diff * (grad_curr + 6.0)`, untuk menekan bayangan frame `t-1`) di dalam cincin lengan bawah (`0.13 * sw .. 0.80 * sw`, di luar area torso) di sekitar siku `le`/`re` ketika confidence pergelangan turun `< 0.42` atau titik pergelangan kolaps ke siku (`< 0.16 * sw`).
   - Mengirimkan koordinat `raw_lw`/`raw_rw` ber-lag rendah ke `GestureStateMachine` (`0.60 * raw + 0.40 * filtered` di `PropellerRotationClassifier`) agar radius putaran tidak menyusut oleh low-pass filter.
2. **Filter & *Velocity Gating* Ramah Motion Blur (`pose_stabilizer.py`):**
   - Menurunkan ambang penerimaan pergelangan yang terikat secara anatomis ke siku (`<= 1.25 * sw`) hingga `BLUR_WRIST_CONF_MIN = 0.08` (`BLUR_ELBOW_CONF_MIN = 0.12`), menaikkan `beta` One-Euro Filter pergelangan ke `0.75`, dan membebaskan pergelangan yang terikat siku (`<= 0.98 * sw`) dari penolakan/pembekuan `conf < 0.50` dengan batas langkah `max_step = 7.5 * sw_scale * eff_dt`.
3. **Pelacakan Orbit *Dual-Center*, *Phase Unwrapping*, & Debounce Blur (`motion_recognizer.py`, `cv_locomotion_config.json`):**
   - **Dual-Center Orbital Tracking:** Melacak sudut putaran terhadap **Siku (`le`/`re`)** sekaligus **Sentroid Lintasan Pergelangan (`rel_history` 10 frame)** yang dikunci oleh syarat *2D circularity* (`span_x >= 0.055 * sw`, `span_y >= 0.055 * sw`, `aspect_2d >= 0.20`, `|sin(dtheta)| >= 0.10`) sehingga putaran kerucut 3D yang bergeser dari siku tetap terdeteksi penuh sementara kepakan 1D vertikal (`#04`, `#06`, `#15A`) tetap ditolak 100%.
   - **Spin-Direction Phase Unwrapping & Streak Debounce:** Meng-unwrap lompatan sudut maju `125°–245°` lintas 1–3 frame blur searah putaran aktif (`accum >= 0.45`), menghapus reset 1-frame `|dtheta| >= 0.45` (wajib `2–3` frame berlawanan berturut-turut untuk reset), menambahkan *elbow-spread grace* `4` frame terhadap goyangan blur siku, serta memperbarui konfigurasi `cv_locomotion_config.json` (`min_rotation_accum_rad: 2.15`, `min_angular_velocity: 1.85`, `min_side_angular_velocity: 0.45`, `min_orbit_radius_sw: 0.11`, `min_angle_radius_sw: 0.065`, `grace_hold_sec: 0.42`).

### Hasil Verifikasi (`ARDragonSkyGuardian_CV`)
- `test_all_17_poses_alignment.py` (termasuk suite baru `test_propeller_dash_motion_blur_resilience`: low-conf `0.12..0.32` + *streak reversal* + *elbow wobble*, *3D forward-angled cone* di luar siku, serta *pixel-level Elbow-Annulus Motion-Blur Recovery* saat `c_w = 0.02`): **100% PASS** (`0` regresi pada 6 pose terkunci `#01, #02, #04, #05, #08, #13` dan seluruh 17 pose).
- Regresi penuh `test_phase1_validation.py`, `test_phase2_validation.py`, `test_phase4_validation.py`, `test_additional_gestures.py`, `test_momentum_and_control_revision.py`, `test_tahap1_face_tracking.py`, & `test_e2e_flight_physics.py`: **100% PASS**.

---

## 2026-11-21 — Memori Sesi: Implementasi Sesi 5 Game Loop dan Scoring
- **Catatan Memori:** [[Implementasi Sesi 5 Game Loop dan Scoring]]
- **Ringkasan:** Mengarsipkan memori sesi implementasi `GameManager.cs` (`CountdownStart` 3..2..1..TERBANG!, jeda timer saat narasi, evaluasi `TimeUp` lulus $\ge 1$ bintang), pembakuan formula 3 bintang berbasis `Global Vitality` (`33.4% / 66.7% / 100%`), progresi pembukaan level berjenjang + mode Free Play di `PlayerPrefs`, animasi bintang sekuensial `FinishModalUI.cs`, sinkronisasi `MissionsPanelUI.cs`, serta 2 screenshot mandiri (`SS_Sesi5_FinishModal_Victory.png`, `SS_Sesi5_Lobby_StageProgress.png`).

## 2026-11-21 — Memori Sesi: Implementasi Sesi 4B VFX dan Atmosfer
- **Catatan Memori:** [[Implementasi Sesi 4B VFX dan Atmosfer]]
- **Ringkasan:** Mengarsipkan memori sesi pemasangan skill `effekseer-vfx` + plugin `EffekseerForUnity` (`1.80.8`) di Unity 6 URP, kompilasi 7 berkas partikel `.efkefc`, Dynamic Camera FOV (`60°–85°`) & jarak Z (`-6.0m .. -7.5m`), shader `DynamicBiomeSkybox.shader` + `BiomeAtmosphereController.cs` (transisi krisis `<250m` & restorasi `2.75s`), integrasi `DragonVfxController.cs`, serta 4 screenshot mandiri Sesi 4B.

## 2026-11-21 — Memori Sesi: Implementasi Sesi 4 Tantangan Desa
- **Catatan Memori:** [[Implementasi Sesi 4 Tantangan Desa]]
- **Ringkasan:** Mengarsipkan memori sesi implementasi tantangan 3 desa (`VillageZoneController.cs`), sub-marker 2 tahap Pyrewood, `WorldSpaceVillageMarker.cs`, `EnterAutoHover`/`ExitAutoHover` + Escape Engine + Launch Thrust (`_launchThrustGraceTimer = 0.60s`), isolasi UI narasi (`PF_QuestNarrativeBox`), perbaikan `ReuseAddress` UDP `5052`, proteksi kalibrasi belok kanan CV, serta pembagian stage `Lvl 1/2/3 = 1/2/3 Desa`.

## 2026-11-21 — Memori Sesi: Sistem Momentum Terpadu dan Revisi Kontrol
- **Catatan Memori:** [[Sistem Momentum Terpadu dan Revisi Kontrol]]
- **Ringkasan:** Mengarsipkan memori sesi sistem momentum kontinu (`0–100%`, *Soft-Cap Gradual Builder*), pemisahan `#09A Hover Active` vs `#09B Slow Glide` dan `#06B Steep Climb Flap` vs `#07 Fly Up`, pemasangan paket delta `Export_Delta_LaptopB/` (`15` ikon medali bersih `UI_Pose_01..15*.png` + `MomentumDock` HUD), serta verifikasi 10 suite Python CV & Unity Play Mode (`MomentumFlightVerificationRunner`).

## 2026-11-21 — Implementasi Sesi 5 Dasar: Pembakuan Game Loop, Countdown Awal, Skor 3 Bintang Global Vitality, & Progresi Level

### Eksekusi Utama (`[[laptop-a/arsitektur_sesi_5_gameloop_dan_scoring]]`, `[[laptop-a/prompt_eksekusi_sesi_5_gameloop_dan_scoring_laptop_b]]`, `[[laptop-a/mekanisme_tantangan_desa]]`, `[[laptop-a/panduan_screenshot_mandiri_dan_laporan]]`)
1. **Pembakuan Formula Bintang & Persistensi `PlayerPrefs` (`DragonGameSettings.cs`):**
   - Membakukan formula bintang berbasis **Progres Vitalitas Global** (`CalculateStars`: `>= 1.000f -> 3`, `>= 0.667f -> 2`, `>= 0.334f -> 1`, `< 0.334f -> 0`).
   - Mematikan `UnlockAllStagesForTesting = false` untuk mode permainan normal, menyelaraskan key `PlayerPrefs` (`Fanamir_Stage01..03_Unlocked`, `Fanamir_FreePlay_Unlocked`, `Fanamir_Stage01..03_BestStars`, `Fanamir_Stage01..03_BestTime`), serta pembukaan sekuensial saat meraih $\ge 1$ bintang (`Stage 1 -> Stage 2 -> Stage 3 -> Free Play Mode`).
2. **`GameManager.cs`, State Machine, Countdown 3 Detik, & Jeda Narasi (`GameManager.cs`, `DragonFlightController.cs`, `VillageZoneController.cs`):**
   - Menambahkan `GameManager.cs` (`GameState`: `LevelInit`, `CountdownStart`, `Playing`, `Paused`, `TimeUp`, `Victory`) dan `DragonFlightController.ControlsLocked` untuk mengunci kendali terbang dengan aman saat fase `CountdownStart` (`3.. 2.. 1.. TERBANG!`) maupun saat `TimeUp` (melambat mulus ke hover).
   - Menghubungkan `VillageZoneController.ComputeGlobalVitality()` secara kontinu (termasuk Stage 2: 1 desa pulih = `50%` = `1` Bintang, desa kedua $+16.7\%$ = `66.7%` = `2` Bintang; Stage 3: `1/2/3` desa = `1/2/3` Bintang) serta mode **Free Play** (`StageIndex = 4`, 3 desa aktif tanpa batas waktu).
   - Memastikan timer misi (`120s` / `240s` / `360s`) **PAUSE** otomatis saat narasi `PF_QuestNarrativeBox` aktif dan **RESUME** setelah selesai; saat `TimeUp (00:00)`, pemain dengan progres $\ge 33.4\%$ ($\ge 1$ bintang) tetap dinyatakan **LULUS** dan membuka stage berikutnya, sedangkan $< 33.4\%$ menampilkan status **WAKTU HABIS**.
3. **Penyempurnaan UI Sesi 5 (`FinishModalUI.cs`, `MissionsPanelUI.cs`, `InGameFlightHUD.cs`, `CalibrationPanelUI.cs`, `UISceneAndPrefabBuilder.cs`, `Session4_VillageBuilder.cs`):**
   - `FinishModalUI.cs` (`SCREEN 04`): animasi pengisian bintang berurutan `0.40s` berbasis `WaitForSecondsRealtime` (`Time.timeScale == 0`), statistik `Waktu Bermain (MM:SS)`, `Desa Pulih (X / N)`, dan `VITALITAS GLOBAL (%)`, serta navigasi `LEVEL BERIKUTNYA` / `MODE FREE PLAY` (pasca Stage 3), `COBA LAGI`, dan `KEMBALI KE LOBBY`.
   - `MissionsPanelUI.cs` (`SCREEN 00`): menampilkan bintang perolehan, rekor waktu tercepat (`BEST MM:SS`), gembok dinamis `LOCKED`/`PLAY`, dan tombol `Btn_FreePlay` (`FREE PLAY · LOCKED` $\rightarrow$ `FREE PLAY · OPEN`).
   - `InGameFlightHUD.cs`: menambahkan `CountdownOverlay` (`SetCountdownDisplay`), mode `FREE PLAY` pada `MissionCapsule`, serta membersihkan penanda `// TODO [SYNC-GAMEPLAY]`.
   - Merakit ulang seluruh prefab & scene via `UISceneAndPrefabBuilder.BuildAll()` dan `Session4_VillageBuilder.BuildVillageZonesAndMarkers()`.

### Hasil Verifikasi Play Mode (`Unity 6000.3.7f1`) & Screenshot Mandiri
- `Session5GameLoopVerificationRunner.RunPlayModeVerificationAsync()`: **`ALL SESSION 5 GAME LOOP, COUNTDOWN, NARRATIVE FREEZE, GLOBAL VITALITY STARS, UNLOCK & FINISH MODAL CHECKS PASSED (100%)`** (`0` console errors).
- Non-regresi `MainIsland_Gameplay.unity`: `MomentumFlightVerificationRunner` (**100% PASS**, `0` errors) & `Session4VerificationRunner` (**100% PASS**, `0` errors).
- Verifikasi `MainMenu_Lobby.unity`: kartu Stage `01/02/03`, bintang, rekor waktu, lock/unlock sekuensial, & tombol `FREE PLAY · OPEN` pasca Stage 3 terverifikasi (`0` errors, `0` warnings).
- **Tangkapan Layar Mandiri (`docs/figures/raw_screenshots/`):**
  - `docs/figures/raw_screenshots/SS_Sesi5_FinishModal_Victory.png`
  - `docs/figures/raw_screenshots/SS_Sesi5_Lobby_StageProgress.png`

---

## 2026-11-21 — Memori Sesi: Setup Optimasi Token Laptop B
- **Catatan Memori:** [[Setup Optimasi Token Laptop B]]
- **Ringkasan:** Mengarsipkan memori sesi implementasi *The Optimal Lean Stack* di Laptop B (`.antigravityignore`, instalasi `rtk 1.2.0` via `winget` & verifikasi aktif di `PATH`, serta penambahan aturan efisiensi token & *Ponytail-Lite / YAGNI* di `AGENTS.md`).

## 2026-11-21 — Memori Sesi: Penyelarasan 17 Pose dan Propeller Dash
- **Catatan Memori:** [[Penyelarasan 17 Pose dan Propeller Dash]]
- **Ringkasan:** Mengarsipkan memori sesi penyelarasan 17 pose kontrol (nol regresi pada 6 pose terkunci), penyelesaian Pilar 1 Pra-Sesi 4 (Clean CV Preview UDP `5053` + `DragonHeadLookController` `HEAD_PITCH` Tahap 2), dan peningkatan `#14 Propeller Dash` (deteksi orbit siku CV anti-foreshortening, impuls burst `6.5f` / `30 m/s`, camera surge, serta animasi sayap heliks).

## 2026-11-21 — Memori Sesi: Integrasi Pulau v2, Scene `MainIsland_Gameplay`, & Sinkronisasi UI Missions
- **Catatan Memori:** [[Integrasi Pulau v2 dan Sinkronisasi UI]]
- **Ringkasan:** Mengarsipkan memori sesi ekspor `Pulau_Archipelago.fbx` (`349` mesh gameplay), konversi `69` tekstur PBR (`*_R.png` -> `*_MS.png` URP Smoothness), pembuatan `PF_Pulau_Archipelago.prefab` (`2400×2400 m`, `5` `MeshCollider`), perakitan `MainIsland_Gameplay.unity` (Build Index `1`) beserta Feature-to-UI Sync yang menjaga keutuhan `Test_Dragon_Flight.unity`, perbaikan `StarSlot_1..3` & `FinishModal` `STAR 01..03`, serta penguncian default Stage 02–03 di `MissionsPanelUI`.

## 2026-11-21 — Memori Sesi: Kamera Dinamis, Navigasi Pause, & 3 Titik Wajah (Tahap 1)
- **Catatan Memori:** [[Kamera Dinamis dan Titik Wajah]]
- **Ringkasan:** Mengarsipkan memori sesi implementasi kamera dinamis (`16:9`, `4:3`, `9:16`), perapian HUD PIP & navigasi Pause $\rightarrow$ Calibration (`PlayerPrefs`), deteksi 3 titik wajah (`Nose`, `Chin`, `Mouth` $\rightarrow$ `HEAD_PITCH` / `MOUTH_OPEN`), serta perbaikan kerangka dobel, batas footer UI kalibrasi, dan pembacaan titik jarak jauh (`imgsz=960`, upsample `224×224`, grace hold `0.35s`).

## 2026-11-21 — Memori Sesi: Dynamic Wings Movement & Kontrol Input Kamera
- **Catatan Memori:** [[Dynamic Wings dan Kontrol Kamera]]
- **Ringkasan:** Mengarsipkan memori sesi kontrol dropdown kamera DirectShow + dual mode aktivasi YOLO (`Latar Belakang` vs `Jendela Preview`), ekspor ulang `Dragon_Model.fbx` 47 tulang & implementasi `DragonWingAnimator.cs`, serta perbaikan *rest-pose front lift* lewat penetralan basis airfoil *zero-twist*, penghapusan bias positif `PitchUp`, dan pelipatan jari bebas tumpang-tindih.

## 2026-11-21 — Memori Sesi: Penataan Struktur Folder Proyek
- **Catatan Memori:** [[Penataan Struktur Folder Proyek]]
- **Ringkasan:** Mengarsipkan memori sesi penataan struktur folder proyek (`assets/blender/`, `assets/blender/scripts/`, `assets/vfx/`, `assets/archives/`, `assets/screenshots/`, `docs/`, dan `tools/`) serta pembersihan root tanpa mengubah path inti Unity & Python CV.

## 2026-11-21 — Memori Sesi: Fisika Terbang Fly Up, Hover Mode, dan Gentle Descent di Unity
- **Catatan Memori:** [[Fisika Terbang Fly Up Hover Descent]]
- **Ringkasan:** Menyelesaikan integrasi Phase B pada skrip Unity C# (`UDPPoseReceiver.cs`, `IFlightInputProvider.cs`, `DefaultFlightInputMapper.cs`, `DragonFlightController.cs`, `InGameFlightHUD.cs`) beserta keyboard fallback (`H`, `G`, `U`), verifikasi kompilasi Unity 6 (`0` errors), dan pengujian `test_e2e_flight_physics.py` (`6/6`), `test_additional_gestures.py` (`10/10`), `test_phase5_validation.py` (`7/7`), serta `test_phase6_final_session.py` (`7/7`).

## 2026-11-21 — Memori Sesi: Pembaruan UI Fanamir dan Boot Flow
- **Catatan Memori:** [[Pembaruan UI Fanamir dan Boot Flow]]
- **Ringkasan:** Menyimpan ringkasan lengkap pembaruan UI `v1.6.1` hingga `v1.8` (**Fanamir: The Sky Guardian**, modal Pose Guide `SCREEN 07`, Quest Narrative `SCREEN 08` Auto-Play, Lobby berlatar pulau penuh tanpa panel perkamen luar, serta alur boot `BootFlowController` `Splash SCREEN 09` $\rightarrow$ `Start SCREEN 10` $\rightarrow$ Lobby) agar mudah dirujuk lintas sesi.

## 2026-11-21 — Memori Sesi: Setup Obsidian Brain Laptop B
- **Catatan Memori:** [[Setup Obsidian Brain Laptop B]]
- **Ringkasan:** Menyimpan dokumentasi lengkap sesi setup Obsidian Brain, migrasi 4 catatan `.md` lokal, penyederhanaan nama 5 file memori di `brain/laptop-b/memories/`, pembaruan `[[wikilinks]]`, serta pembuatan `AGENTS.md` di Laptop B.

## 2026-11-21 — Implementasi Sesi 4B: Paket Visual & Flight Feel (VFX Effekseer URP, Dynamic FOV 60°–85°, & Skybox/Fog Dinamis)

### Eksekusi Utama (`[[laptop-a/planning_vfx_fov_skybox_dan_animasi]]`, `[[laptop-a/prompt_eksekusi_vfx_skybox_dan_fov_laptop_b]]`, `[[laptop-a/panduan_screenshot_mandiri_dan_laporan]]`)
1. **Instalasi Skill Agen & Plugin Effekseer Unity 6 URP:**
   - Memasang skill `effekseer-vfx` & `vfx-review` ke `.agents/skills/` dari `kanaworksai-hue/effekseer-vfx-skill` beserta toolchain `EffekseerCore 1.80.7` + `.NET 9.0.20` (`~/.cache/effekseer-vfx`), dan menginisialisasi workspace `vfx/` (`vfx init --no-plugin`).
   - Memasang package `EffekseerForUnity` `1.80.8` via UPM (`https://github.com/effekseer/EffekseerForUnity_Release.git?path=Assets/Effekseer` — branch `#package` pada repo non-release tidak tersedia), mendaftarkan `EffekseerURPRenderPassFeature` pada `Assets/Settings/PC_Renderer.asset` & `Assets/Settings/Mobile_Renderer.asset`, serta membuat `Assets/Resources/EffekseerSettings.asset` (`RendererType = Unity`, `enableDistortion = false`).
2. **Pembuatan & Kompilasi 7 Berkas Partikel Effekseer (`AR Dragon Sky Guardian/Assets/VFX/Effekseer/`):**
   - Menulis 7 brief (`vfx/briefs/VFX_*.json`) & 7 resep (`vfx/src/VFX_*.py`), lulus `vfx brief-validate` + `vfx lint` (`0` errors, `0` warnings) dan terkompilasi via `EffekseerCore` menjadi 7 `.efkefc` + `resources/` (`VFX_Zephyria_WindGust.efkefc`, `VFX_Lake_WaterSiphon.efkefc`, `VFX_Pyrewood_WaterBreath.efkefc`, `VFX_Pyrewood_ExtinguishSteam.efkefc`, `VFX_Solaria_CelestialDewRain.efkefc`, `VFX_Flight_WingtipTrails.efkefc`, `VFX_Flight_SonicDashShockwave.efkefc`), seluruhnya terimpor sebagai `EffekseerEffectAsset` di Unity.
3. **Dynamic Camera FOV (60°–85°) & Jarak Z (`DragonFlightController.cs`):**
   - Memperbarui `UpdateCameraPolish()` dengan `Mathf.SmoothDamp` (`cameraSmoothTime = 0.30s`) untuk FOV & Offset Z kamera: Grounded/Auto-Hover (`60°`, `-6.0 m`), Cruising Glide (`65°–68°`, `-6.3 m`), Tuck to Dive `#13` (`78°–80°`, `-7.2 m` + micro-rumble), dan Propeller Dash `#14` (`85°`, `-7.5 m`), serta `SetExternalCameraRumble()` untuk semburan air `#15B`.
4. **Skybox & URP Fog Dinamis (`BiomeAtmosphereController.cs`, `DynamicBiomeSkybox.shader`, `Mat_DynamicBiomeSkybox.mat`):**
   - Mengimplementasikan shader skybox URP kustom (`DragonWingAR/DynamicBiomeSkybox`) sehingga `_HorizonColor` identik 100% dengan `RenderSettings.fogColor` (menghilangkan patahan tepi laut & artefak hijau horizon).
   - Menangani transisi `<250 m` untuk 3 biome krisis (Zephyria smog kelabu-hijau `150 m`, Pyrewood langit oranye pekat + partikel abu melingkar lembut, Solaria langit terik berdebu) serta transisi restorasi `2.75 detik` saat `VillageZoneController.CompleteVillageRestoration()` dipanggil.
5. **Integrasi Gameplay (`DragonVfxController.cs`, `VillageZoneController.cs`, `Session4_VillageBuilder.cs`):**
   - Memasang socket & `DragonVfxController` pada `/Dragon_Player` (tracking ujung tulang `Wing_Finger_Outer.L/R` untuk `WingtipTrails` saat `speed >= 18 m/s` atau belok tajam, `SonicDashShockwave` saat `#14`, `WindGust`, `WaterSiphon`, dan `WaterBreath`) serta emitter desa (`Pyrewood_ExtinguishSteam`, `Solaria_CelestialDewRain`) di `MainIsland_Gameplay.unity`.

### Hasil Verifikasi Play Mode (`Unity 6000.3.7f1`) & Screenshot Mandiri
- `VFXAtmosphereVerificationRunner.RunPlayModeVerificationAsync()`: **`ALL SESI 4B VFX, DYNAMIC FOV & BIOME ATMOSPHERE CHECKS PASSED (100%)`** (`Glide=(fov=64.99, z=-6.30)`, `Dive=(fov=78.88, z=-7.20)`, `Dash=(fov=85.00, z=-7.50)`, `Hover=(fov=60.07, z=-6.00)`, `ZephCrisisFog=243m -> RestoredFog=3999m`, `0` compile/runtime errors).
- Non-regresi Sesi 4 & Momentum (`MainIsland_Gameplay.unity`): `Session4VerificationRunner.RunPlayModeVerificationAsync()` (**100% PASS**) & `MomentumFlightVerificationRunner.RunPlayModeVerificationAsync()` (**100% PASS**).
- **Tangkapan Layar Mandiri (`docs/figures/raw_screenshots/`):**
  - `docs/figures/raw_screenshots/SS_Sesi4B_Flight_PropellerDash_FOV85.png`
  - `docs/figures/raw_screenshots/SS_Sesi4B_Zephyria_SmogAndWindGust.png`
  - `docs/figures/raw_screenshots/SS_Sesi4B_Pyrewood_WaterBreath_Steam.png`
  - `docs/figures/raw_screenshots/SS_Sesi4B_Solaria_CelestialDewRain.png`

---

## 2026-11-21 (Patch Fix) — Pembagian Tantangan per Level (Lvl 1/2/3 = 1/2/3 Desa) & Integrasi Finish UI (`PF_FinishModal`)

### Temuan Investigasi & Perbaikan
1. **Akar Masalah Tantangan Tergabung di Level 1:**
   - `DragonGameSettings.GetActiveStageIndexOrDefault(int fallback = 3)` dan `VillageZoneController.defaultStageIfUnset = 3` (tersimpan di `MainIsland_Gameplay.unity` & `Session4_VillageBuilder.cs`) menyebabkan sesi yang langsung Play di `MainIsland_Gameplay` atau melalui tombol `READY TO FLY` di `CalibrationPanelUI` (ketika `SelectedStageIndex == 0`) jatuh ke Stage 3 (seluruh 3 desa aktif).
   - Selain itu, `DragonGameSettings.SelectedStageIndex` tidak direset pada `SubsystemRegistration`, sehingga nilai `3` dari sesi Play Mode sebelumnya bertahan di memori saat Domain Reload tidak penuh.
   - **Perbaikan:** Menetapkan default Stage `1` pada `GetActiveStageIndexOrDefault(1)`, `VillageZoneController.defaultStageIfUnset = 1`, `Session4_VillageBuilder.cs`, `MainIsland_Gameplay.unity`, serta `CalibrationPanelUI.OnReadyToFlyClicked()`, dan menambahkan `[RuntimeInitializeOnLoadMethod(RuntimeInitializeLoadType.SubsystemRegistration)]` `ResetStaticSessionState()` pada `DragonGameSettings.cs` agar tiap mulai Play Mode bersih di Stage `1` (`Lvl 1 = 1 Zephyria`, `Lvl 2 = 2 Zephyria+Pyrewood`, `Lvl 3 = 3 Zephyria+Pyrewood+Solaria`).
2. **Integrasi Finish UI, Timer Stage, Bintang, & Next Level (`VillageZoneController.cs`, `FinishModalUI.cs`, `MissionsPanelUI.cs`, `DragonGameSettings.cs`):**
   - Menghubungkan `VillageZoneController.finishModal` (`PF_FinishModal`) ke alur akhir stage: setelah desa terakhir stage pulih (`ClearedVillageCount >= ActiveVillageCount`), naga lepas dengan Launch Thrust dan `PF_FinishModal` tampil otomatis setelah jeda selebrasi `2.5s` (`ShowVictory` + menyimpan bintang `1–3` ke `DragonGameSettings.RecordStageCompletion` & menyembunyikan tombol `NEXT LEVEL` di Stage 3).
   - Menambahkan timer per stage sesuai `mekanisme_tantangan_desa.md` (`120s` / `240s` / `360s`, jeda saat narasi, `hud.SetMissionTimer`, & `ShowDefeat` jika waktu habis), memperbarui `FinishModalUI` (`HandleNextLevelClicked` lanjut ke Stage 2/3 & manajemen `Time.timeScale`), serta menampilkan capaian bintang tersimpan + durasi pada kartu Lobby `MissionsPanelUI`.

### Hasil Verifikasi Unity 6 (`6000.3.7f1`)
- `Session4_VillageBuilder.BuildVillageZonesAndMarkers()`: berhasil memperbarui `MainIsland_Gameplay.unity` (`defaultStageIfUnset: 1`, `finishModal` terhubung).
- Play Mode `MainIsland_Gameplay.unity` (`InitialBoot: stage=1, count=1` + `Session4VerificationRunner.RunPlayModeVerificationAsync()`): **`ALL SESSION 4 VILLAGE, SUB-MARKER, AUTO-HOVER, ESCAPE, BILLBOARD & UI ISOLATION CHECKS PASSED (100%)`** (termasuk kemunculan otomatis `PF_FinishModal` seusai Stage 1, Stage 2 `2` desa, & Stage 3 `3` desa).
- Play Mode `MainIsland_Gameplay.unity` (`MomentumFlightVerificationRunner.RunPlayModeVerificationAsync()`): **`ALL MOMENTUM & FLIGHT PHYSICS CHECKS PASSED (100%)`**.
- Transisi `FinishModalUI` `Btn_NextLevel` (`Stage 1 -> Stage 2`, `stage=2, count=2`) & penyembunyian `Btn_NextLevel` di Stage 3 terverifikasi lulus; `MainMenu_Lobby.unity` tombol Stage 2 memuat `MainIsland_Gameplay` (`LoadedStage=2, Villages=2`). `0` compilation errors.

---

## 2026-11-20 (Patch Fix) — Perbaikan Bug Kontrol Terkunci Belok Kanan Terus (`player_calibrator.py`, `motion_recognizer.py`, & `active_cv_sender.py`)

### Temuan Investigasi & Perbaikan
1. **Akar Masalah (`ARDragonSkyGuardian_CV/player_profile.json`):**
   - Berkas profil kalibrasi lokal (`2026-11-19 20:40:39`) tersimpan dengan nilai korup/ekstrem (`shoulder_width=0.044`, `torso_length=0.085`, `neutral_lean_bias_deg=-22.856°`).
   - Pada `AdaptiveThresholdEngine.compute_adaptive_steering()`, kompensasi `corrected_angle = raw_lean_angle_deg - (-22.856°)` menghasilkan `+22.856°` saat pemain berdiri/duduk tegak (`0°`), sehingga UDP mengirim `steering_input = +1.0` (`LEAN_RIGHT`) terus-menerus (bahkan tetap `LEAN_RIGHT` saat condong kiri `-15°`).
   - Berkas korup `ARDragonSkyGuardian_CV/player_profile.json` telah dihapus agar kembali ke Profil Standar (`neutral_lean_bias_deg=0.0°`).
2. **Validasi & Clamp Kalibrasi (`ARDragonSkyGuardian_CV/player_calibrator.py`):**
   - Menambahkan `AdaptiveThresholdEngine.is_profile_valid()` (`shoulder_width >= 0.07`, `torso_length >= 0.11`, `abs(neutral_lean_bias_deg) <= 12.0°`) serta batas aman runtime `neutral_bias` ke `[-8.0°, +8.0°]` pada `_recalculate_adaptive_parameters()`.
   - Memvalidasi sampel netral di `_finalize_calibration()` agar kalibrasi pada deteksi parsial/miring ekstrem ditolak tanpa menimpa/menyimpan JSON korup, serta menolak profil korup di `load_profile()` agar otomatis fallback ke `get_default_profile()`.
3. **Proteksi Oklusi Pergelangan & Fallback Pinggul (`ARDragonSkyGuardian_CV/motion_recognizer.py`, `active_cv_sender.py`):**
   - Di `GestureStateMachine.update()`, `arm_tilt_norm` / flap height delta hanya dicampurkan ke kemudi ketika kedua pergelangan tangan terdeteksi yakin (`conf >= 0.20`), mencegah posisi fallback pergelangan yang keluar frame memicu kemudi miring palsu.
   - Di `active_cv_sender.py` fallback pinggul duduk, menstabilkan penyebut horizontal bahu (`>= 0.05 * aspect_scale_x`) dan menolkan `seated_lean_deg` saat bahu bersilang (`raw_rs_x - raw_ls_x < 0.04`).

---

## 2026-11-19 (Patch Fix) — Perbaikan Transisi Pasca-Zephyria (Launch Thrust Grace, Guard `Restored`, & UDP 5052 Reuse)

### Temuan Investigasi & Perbaikan
1. **`DragonFlightController.cs` & `MainIsland_Gameplay.unity` (`ExitAutoHover` & `_launchThrustGraceTimer`):**
   - Menyelaraskan nilai terserialisasi `/Dragon_Player` di `MainIsland_Gameplay.unity` (`Session4_VillageBuilder.cs`) ke `autoHoverLaunchMomentum = 0.35f` (`35%`) dan `autoHoverLaunchSpeed = 17.5f` m/s (sebelumnya masih terserialisasi `0.32f` / `17f`).
   - Memastikan `ExitAutoHover(bool)` selalu mereset `_isHovering`, `_hasHoverAnchor`, `_isZephyriaChallengeHover`, dan `_hoverWaitEscapeRelease` ke `false`, serta menambahkan `_launchThrustGraceTimer = 0.60f` agar rem udara pasif (`#09A Hover` / `#12 FlyDownW`) saat pemain menurunkan tangan seusai 10 kepakan tidak langsung memangkas momentum ke `0%` dan menghentikan laju maju sebelum keluar silinder (sementara manuver aktif `Glide`, `Flap`, `FlyUp`, `TuckDive`, `PropellerDash`, & `TurnInput` tetap responsif seketika).
2. **`VillageZoneController.cs` (Guard `Restored`, Cooldown `3.5s`, & Pelepasan UI Defensif):**
   - Menambahkan pengecekan eksplisit `state != Unvisited` / `state == Restored` pada `EnterNarrativeSession` dan `EnterChallengeSession`, serta memanggil `_sessionTrigger?.StartExitCooldown(ExitCooldownSeconds)` saat tantangan desa selesai.
   - Pada `CompleteVillageRestoration` & Auto-Release Danau Pyrewood: memanggil `flightController.ExitAutoHover(true)` tanpa syarat tambahan `IsHovering` serta memastikan `challengeUI?.HideChallenge()`, `narrativeUI?.HideNarrative()`, dan `hud?.SetNarrativeIsolationMode(false)` bersih.
3. **`UDPPoseReceiver.cs` (Perbaikan Socket Port `5052` Lintas Scene):**
   - Menangani temuan log console sesi uji manual (`[UDPPoseReceiver] Gagal mengikat port 5052: Only one usage of each socket address`) dengan mengaktifkan `ExclusiveAddressUse = false` + `SocketOptionName.ReuseAddress` sebelum `Bind` di `StartUDPListener()` dan penutupan + `Dispose()` + `Join(200)` thread yang bersih di `StopUDPListener()`.

### Hasil Verifikasi
- `MomentumFlightVerificationRunner.RunPlayModeVerificationAsync()`: **100% PASS**.
- `Session4VerificationRunner.RunPlayModeVerificationAsync()`: **100% PASS** (termasuk verifikasi pasca-`Restored` di dalam silinder `R=48m`: `IsHovering=False`, Launch Thrust aktif, bebas re-trigger, kemudi & glide bergerak `>20m` dalam `1.6s`).
- Simulasi Stage 02 Pasca-Zephyria -> Pyrewood (`0.4s` transisi arm-drop `#09A` + `Glide/Lean/Flap`): `release=(mom=0.35, spd=17.59)`, `spdAfterDrop=18.20`, `distMoved=48.9m`, `vit=50%`, `wp='DESA PYREWOOD · 1294m'`, `isolated=False`.
- Transisi berulang `MainMenu_Lobby` $\leftrightarrow$ `MainIsland_Gameplay` dengan stream UDP 5052 30Hz aktif: `UDPConnected=True`, `0` errors, `0` warnings.

---

## 2026-11-19 — Implementasi Sesi 4 (Mekanisme Tantangan 3 Desa, Sub-Marker, Auto-Hover & Escape, World Billboard, Isolasi UI, & Stage Testing Unlock)

### Eksekusi Utama (`[[laptop-a/mekanisme_tantangan_desa]]`, `[[laptop-a/checklist_finalisasi_pra_sesi_4]]`, `[[laptop-a/kontrak_integrasi_ui_dan_progress]]`)
1. **Pembukaan Semua Stage untuk Testing (`DragonGameSettings.cs`, `MissionsPanelUI.cs`, `UISceneAndPrefabBuilder.cs`):**
   - Menambahkan `public static bool UnlockAllStagesForTesting = true;` dan pelacakan `SelectedStageIndex` (`GetActiveStageIndexOrDefault(3)`) pada `DragonGameSettings.cs`.
   - Memperbarui `MissionsPanelUI.cs` & `UISceneAndPrefabBuilder.BuildMissionsPanel()` agar Stage 01, Stage 02, dan Stage 03 seluruhnya terbuka saat flag aktif: menyembunyikan `LockedScrim`, mengganti badge menjadi `STAGE XX · OPEN` (`UI_Tag_Stage_Green_Blank`), mengganti tombol menjadi **`PLAY`** hijau (`UI_Btn_Primary_Green_9Slice`), serta meluncurkan `MainIsland_Gameplay.unity` dengan konfigurasi desa aktif (`Stage 01`: 1 desa Zephyria, `Stage 02`: 2 desa Zephyria+Pyrewood, `Stage 03`: 3 desa lengkap).
2. **Pilar 3: Marker Silinder & World-Space Billboard (`WorldSpaceVillageMarker.cs`, `Session4_VillageBuilder.cs`):**
   - Merakit hierarki `/VillageZones_Session4` di `MainIsland_Gameplay.unity` melalui menu `DragonWingAR/Session 4/Build Village Zones and Markers`:
     - **Desa Zephyria (Biome 1):** Pad `(-562.40, 14.10, 274.30)` (`R=35m, Y<=40m`) & Challenge Kabut `(-562.40, 39.10, 274.30)` (`R=48m`).
     - **Desa Pyrewood (Biome 2):** Pad `(739.60, 12.90, 7.80)` (`R=35m, Y<=40m`), Sub-Marker 1 Danau Zephyria `(-292.27, 13.62, 663.83)` (`R=45m` di atas `SM_Biome1_Water_01`), & Sub-Marker 2 Hutan Terbakar `(797.77, 13.68, 147.97)` (`R=50m` di sentroid 59 pohon hangus Biome 2).
     - **Desa Solaria (Biome 3):** Pad `(39.40, 14.70, -647.60)` (`R=35m, Y<=40m`) & Challenge Sky Altar `(39.40, 54.70, -647.60)` (`R=48m`, `Y ~ +40m`).
   - Membuat `WorldSpaceVillageMarker.cs` (`Y+12m`, aktif saat jarak `<= 150m`, billboard menghadap `Camera.main.transform.rotation`, distance-scaling halus, teks dinamis `"<LOKASI> · XXm"`) serta cincin batas zona non-colliding (`Mat_ZoneRing_S4.mat`).
3. **Pilar 4: Auto-Hover, Escape Engine, & Launch Thrust (`DragonFlightController.cs`, `DragonWingAnimator.cs`):**
   - Mengimplementasikan `EnterAutoHover(Vector3 anchorPosition, Quaternion anchorRotation, bool isZephyriaChallenge = false)` & `ExitAutoHover(bool applyLaunchThrust = true)`: lerp posisi & rotasi (`smoothSpeed = 3.0f`), kecepatan horizontal turun mulus ke `0 m/s`, animasi sayap `wingRegime == "Hover"` diprioritaskan di `DragonWingAnimator.cs`, serta momentum dibekukan selama hover.
   - Menambahkan **Escape Engine** (`OnAutoHoverEscaped`): gestur **Flap (`#06`)**, **Tuck Dive (`#13`)**, atau **Propeller Dash (`#14`)** membatalkan auto-hover (kecuali di **Zona Tantangan Zephyria**, di mana **Flap TIDAK membatalkan auto-hover** sehingga hanya Tuck Dive / Propeller Dash yang escape), diikuti **Launch Thrust** (`autoHoverLaunchMomentum = 0.35f`, `autoHoverLaunchSpeed = 17.5 m/s`) agar naga tidak stall.
4. **State Mesin Desa, Isolasi UI, & Logika Tantangan (`VillageZoneController.cs`, `InGameFlightHUD.cs`):**
   - State mesin per desa (`Unvisited` $\rightarrow$ `NarrativeActive` $\rightarrow$ `QuestAccepted` $\rightarrow$ `ChallengeActive` $\rightarrow$ `Restored`) lengkap dengan **Exit Cooldown** (wajib keluar silinder + jeda `3.5 detik`).
   - **Isolasi UI Narasi (`InGameFlightHUD.SetNarrativeIsolationMode(bool)`):** saat `NarrativeActive`, `MomentumDock`, `TelemetryDock`, `PIP_CameraPanel`, `WaypointCompass`, `MissionCapsule`, `GlobalVitalityBar`, & `Btn_EscPause` disembunyikan sehingga layar **HANYA** menampilkan `PF_QuestNarrativeBox` (`SCREEN 08` auto-play 3 baris) & `QuestObjectivePanel` (`SCREEN 02`); escape di tengah narasi mengembalikan UI normal dan mereset narasi ke baris 1.
   - **Tantangan No-Decay & Feature-to-UI Sync:** Zephyria (`+0.11f/kepakan`), Pyrewood Tahap 1 Danau (`+0.22f/s` + Auto-Release 100%), Pyrewood Tahap 2 Hutan (`MOUTH_OPEN > 0.35` aktif / `< 0.25` mati, `+0.15f/s`), Solaria Sky Altar (Double I-Pose `11` `#15C`, `+0.125f/s`), update `PF_VillageChallengeOverlay` (`SCREEN 03`), `WaypointCompass` dinamis ke desa/sub-marker terdekat, `SetVillageCount`, `SetGlobalVitality` (`100%` / `50%->100%` / `33%->67%->100%`), serta highlight `#15` `CalibrationPanelUI`.

### Status Perubahan UI (Feature-to-UI Sync)
| Elemen UI | Status Sebelum | Status Sekarang | API / Sinkronisasi |
| :--- | :--- | :--- | :--- |
| `Stage 02` & `Stage 03` Cards (`SCREEN 00`) | Placeholder Terkunci | **Live (Mode Testing)** | `DragonGameSettings.UnlockAllStagesForTesting`, `MissionsPanelUI` |
| `WaypointCompass` (`SCREEN 02`) | Dormant (`false`) | **Live** | `InGameFlightHUD.SetWaypointTarget` / `ClearWaypoint` |
| `QuestObjectivePanel` & `GlobalVitalityBar` (`SCREEN 02`) | Placeholder `0%` | **Live** | `SetQuestObjective`, `SetVillageCount`, `SetGlobalVitality` |
| `PF_QuestNarrativeBox` (`SCREEN 08`) | Dormant (`false`) | **Live** | `ShowNarrativeSequence` + `SetNarrativeIsolationMode` |
| `PF_VillageChallengeOverlay` (`SCREEN 03`) | Dormant (`false`) | **Live** | `ShowChallenge`, `UpdateProgress`, `HideChallenge` |

### Hasil Verifikasi Unity 6 (`6000.3.7f1`)
- **Build & Kompilasi:** `UISceneAndPrefabBuilder.BuildAll()` & `Session4_VillageBuilder.BuildVillageZonesAndMarkers()` lulus bersih (`compilationFailed: false`, `0` error/warning baru).
- **Play Mode `MainMenu_Lobby.unity`:** Ketiga kartu Stage `01/02/03` tampil tanpa `LockedScrim` dengan tombol hijau `PLAY`; klik Stage 02 `PLAY` memuat `MainIsland_Gameplay.unity` (`stage=2`, `activeVillages=2`, `VILLAGES 0/2`, `0%`, `DESA ZEPHYRIA · 638m`).
- **Play Mode `MainIsland_Gameplay.unity` (`Session4VerificationRunner` + `MomentumFlightVerificationRunner`):**
  - `MomentumFlightVerificationRunner`: **`ALL MOMENTUM & FLIGHT PHYSICS CHECKS PASSED (100%)`** (zero-regression pada 6 pose terkunci & Momentum `0–100%`).
  - `Session4VerificationRunner`: **`ALL SESSION 4 VILLAGE, SUB-MARKER, AUTO-HOVER, ESCAPE, BILLBOARD & UI ISOLATION CHECKS PASSED (100%)`** (`WorldSpaceVillageMarker` >150m/<=150m & billboard kamera, isolasi UI narasi, escape reset baris 1, cooldown keluar silinder + `3.5s`, Flap Zephyria no-escape & No-Decay, TuckDive escape + Launch Thrust `>=30%`/`>=16 m/s`, Danau auto-fill & Auto-Release, Hutan hysteresis `0.35/0.25`, Solaria pose `11`, & Vitality Stage 1/2/3).
- **Play Mode `Test_Dragon_Flight.unity`:** Non-regresi lulus (`0` console errors, `0` console warnings).

---

## 2026-11-19 — Sistem Momentum Terpadu (`0–100%`), Revisi Kontrol (`v4.0`), Delta Aset Bersih, & HUD Momentum Bar

### Eksekusi Utama (`[[laptop-a/planning_momentum_dan_revisi_kontrol]]`)
1. **Pemasangan Paket Delta Aset (`Export_Delta_LaptopB/`):**
   - Menyalin `UI_HUD_Momentum_Track_206x20.png` & `UI_HUD_Momentum_Fill_200x14.png` ke `AR Dragon Sky Guardian/Assets/UI/Components/05_HUD/`.
   - Menghapus `17` kartu `Pose_01..15C.png` berkerangka CV dari `Assets/UI/Components/06_Poses/` dan menggantinya dengan `15` ikon medali siluet bersih `UI_Pose_01..15*.png`.
   - Menyalin referensi desain terbaru ke `docs/UI_Designs_Reference/`, vektor CV `Pose_06B`, `Pose_09`, `Pose_09B`, & `All_19_Poses_Sheet.png` ke `assets/poses_cv_vector/`, serta `Panduan_19_Pose_Fanamir_CV.pdf`/`.docx` ke `docs/` dan root workspace.
2. **Pembaruan Python CV (`ARDragonSkyGuardian_CV/motion_recognizer.py`, `test_all_17_poses_alignment.py`, `test_momentum_and_control_revision.py`):**
   - Memisahkan `#09A Hover Active` (`L-Pose + flutter`, `wrist_vert_speed >= 0.25`, `hover_power = 0.65..1.0`, hysteresis exit `< 0.20` dengan jendela `0.28s`) vs `#09B Slow Glide` (`L-Pose diam`, `hover_power = 0.25`), dan mengekspos flag `is_hover_flapping` & `is_slow_glide` sambil menjaga protokol UDP 44-token kompatibel.
   - Memastikan `#07 Fly Up` hanya dipicu oleh Open Y-Pose (bukan `HEAD_PITCH` sendirian) serta menjaga `is_flapping=False` saat tahan Open Y-Pose agar `#06B` (Flap + `HEAD_PITCH > 0.12`) dan `#07` tidak saling tumpang-tindih.
   - Menstabilkan `flap_frequency` sejak awal stroke pada `FlapStrokeClassifier` (`effective_freq`) untuk integrasi momentum kepakan yang mulus di Unity.
3. **Fisika Momentum Terpadu & Animasi Sayap Unity (`DragonFlightController.cs`, `DragonLocomotionConfig.cs`/`.asset`, `DragonWingAnimator.cs`, `IPoseProvider.cs`, `UDPPoseReceiver.cs`, `IFlightInputProvider.cs`, `DefaultFlightInputMapper.cs`):**
   - Menambahkan `CurrentMomentum` (`0..1`), `CanFlyUpClimb`, `IsSteepClimbFlap`, `IsSlowGlide`, dan `IsHoverActiveBrake` di `DragonFlightController.cs` beserta 24 parameter momentum di `DragonLocomotionConfig` (`AddMomentumSoftCapped` anti-pemangkasan & `DrainMomentum`):
     - `#01` / Grounded / Landing: reset `0%`; `#04` Takeoff: gradual cap `0.15f`; `#06` Flap (dagu netral): `+0.06/flap` soft-cap `0.30f` (tanpa memotong `> 0.30f`); **`#06B Steep Climb Flap`** (`HeadPitch > 0.12f` + Flap): tanpa butuh momentum, pitch `~+24°`, naik `+6.5..+8.0 m/s`, soft-cap `0.20f`.
     - `#11` Y-Dive (`+0.12/s`, cap `0.55f`), `#13` Tuck Dive (`+0.16/s` -> `1.0f`), `#14` Propeller Dash (`+0.07/360°`, cap rate `~0.18/s` -> `1.0f`).
     - `#08` Glide (`14 -> 27 m/s`, drag `-0.035/s`), **`#07` Fly Up** (hapus pemicu `HeadPitch` tunggal; butuh `CurrentMomentum > 0` zoom climb `+7..+9.5 m/s` & `-0.25/s`, atau *gentle stall* `-1.2 m/s` saat `0%`), **`#09A` Hover Active** (air brake `-0.40/s`, tetap maju melambat saat momentum `> 0` hingga `0 m/s`), **`#09B` Slow Glide** (`~7 m/s`, `-0.6 m/s`, drag `-0.10/s`), `#10` (`-0.20/s`), `#12` (`-0.40/s`), & Auto-Hover desa (`-0.85/s`).
   - Memperbarui `DragonWingAnimator.cs`: memisahkan `FlyUp` (12 tulang sayap terangkat membentuk siluet **Y**), `SteepClimbFlap`, `Hover` (`#09A` kepak ringan menahan udara), dan `SlowGlide` (`#09B` sayap setengah terlipat kompak).
4. **Pembaruan UI HUD & Pose Guide (`UISpriteSetupUtility.cs`, `InGameFlightHUD.cs`, `PoseGuideCatalog.cs`, `PoseGuideOverlayUI.cs`, `CalibrationPanelUI.cs`, `UISceneAndPrefabBuilder.cs`):**
   - Menjalankan `UISpriteSetupUtility.SetupAllSprites()` (`142` PNG, `76` border, `15/15` ikon medali bersih `UI_Pose_01..15`).
   - Memasang `MomentumDock` (`420×40`) tepat di atas `TelemetryDock` pada `SCREEN 02` (`SetMomentum(float, bool)` live ke `DragonFlightController.CurrentMomentum` dengan badge `"{pct}% · Y-OK"` / `"0% · EMPTY"`).
   - Mengembalikan `SCREEN 07` (`PF_PoseGuideOverlay.prefab`, `3×5` 15 kartu utama) dan `SCREEN 01` (`15 POSES`) dengan pembaruan deskripsi `#06/#06B`, `#07`, `#08`, `#09A/#09B`, `#13`, `#14`, `#15`, lalu menjalankan `UISceneAndPrefabBuilder.BuildAll()`.

### Hasil Verifikasi
- **Python CV:** **10/10 suite lulus 100%** (9 suite regresi + `test_momentum_and_control_revision.py` baru; zero regression pada 6 pose terkunci `#01, #02, #04, #05, #08, #13`).
- **Unity Play Mode (`0` Errors & `0` Warnings):**
  - `MainIsland_Gameplay.unity` & `Test_Dragon_Flight.unity` via `MomentumFlightVerificationRunner` (UDP `5052` 44-token): terverifikasi `Idle=(0.00, '0% · EMPTY')`, `Flap=0.14->0.30 (vy=4.69)`, `Steep06B=(vy=8.00, pitch=22.8°, SteepClimbFlap)`, `DiveFull=1.00`, `NoTrunc=0.99`, `GlideSpd=26.7 m/s`, `YClimb=(vy=8.33, FlyUp)`, `YStall=(mom=0.01, vy=-1.01)`, `DashMom=0.57`, `SlowGlide09B=(spd=10.90, SlowGlide)`, `Hover09A=(early=9.66->final=0.00, Hover)`, dan live `MomentumDock` (`79% · Y-OK`) + capture visual Game View.
  - `MainMenu_Lobby.unity`: terverifikasi `SCREEN 07` (`3×5`, `15/15` kartu `UI_Pose_*` bersih + highlight `#07 ACTIVE`) dengan `0` errors & `0` warnings.

---

## 2026-11-18 — Peningkatan `#14 Propeller Dash` (Deteksi CV Orbit Siku, Fisika Burst `30 m/s`, & Animasi Sayap Heliks)

### Eksekusi Utama
1. **Penyempurnaan Classifier CV (`ARDragonSkyGuardian_CV/motion_recognizer.py`, `cv_locomotion_config.json`, `test_all_17_poses_alignment.py`):**
   - Mengonfigurasi blok `propeller_dash` di `cv_locomotion_config.json`: mempercepat respons pemicu (`min_rotation_accum_rad: 2.75`, `min_angular_velocity: 2.2`, `min_side_angular_velocity: 0.8`, `min_orbit_radius_sw: 0.20`), serta menambah hysteresis rentangan lengan atas (`spread_horiz_enter/exit: 0.28 / 0.21`, `spread_vert_enter/exit: 0.62 / 0.78`, dan jarak antar-siku `1.42 / 1.26 * sw`).
   - Menambahkan pelindung singularitas foreshortening 2D (`radius_norm < 0.13`), pembatalan lonjakan cabang `|dtheta| > 2.2`, debounce 2-frame terhadap jitter arah balik, larangan akumulasi rotasi saat lengan atas rapat (`Hover`), serta `grace_hold_sec: 0.28s` dengan peluruhan eksponensial yang tetap terjaga di atas `0.40` hanya saat kecepatan sudut masih aktif (`est_omega >= 1.15`).
   - Menambah `reset()` saat tracking hilang dan `COOLDOWN_DASH_TO_FLAP = 0.16s` pada `GestureStateMachine`.
2. **Peningkatan Fisika, Kamera, & Animasi Sayap Unity (`DragonFlightController.cs`, `DragonWingAnimator.cs`, `CalibrationPanelUI.cs`, `MainIsland_Gameplay.unity`, `Test_Dragon_Flight.unity`):**
   - Menambahkan **impuls lonjakan masuk (`dashBurstImpulse = 6.5f`, cooldown `0.55s`)**, menaikkan `maxDashSpeed` (`26 -> 30 m/s`) dan `dashThrustAccel` (`22 -> 28 m/s²`), mendukung dash sprint di darat (`walkSpeed * 2.2`, tanpa takeoff tidak disengaja), serta sinkronisasi nilai terserialisasi pada `/Dragon_Player` di kedua scene terbang.
   - Menambahkan efek kecepatan kamera saat Dash (`_dashSurgeVisual01`: tambahan `+5°` FOV surge & pullback `localPosition.z` hingga `-0.55 m`) dalam `UpdateCameraPolish()`.
   - Mengubah animasi sayap `PropellerDash` di `DragonWingAnimator.cs` menjadi gerakan **heliks/orbital kuadratur (`sin`/`cos` pada `ForearmFlapUp`, `ForearmSweepBack`, `ForearmPitchUp`)** berfrekuensi `2.15×–2.75×` serta menyelaraskan simulasi speed `30f` di `CalibrationPanelUI.cs`.

### Hasil Verifikasi
- **Python CV:** **9/9 suite lulus 100%** (termasuk skenario baru kecepatan rotasi nyaman `~3.3 rad/s` + drop foreshortening 2-frame serta pemeriksaan nol-regresi pada Hover, Low-A, 1D Flap, Chest-front, dan pose terkunci).
- **Unity Play Mode (`0` Compile/Runtime Errors & `0` Warnings):**
  - `MainIsland_Gameplay.unity`: Grounded Dash (`grounded=True`, `state=Propeller Dash`, `speed=7.89 m/s`), Airborne Dash (`state=Propeller Dash`, `speed=30.00 m/s`, `fov=73.99°`, `camZ=-6.83`, `vy=1.37 m/s`, `headDeg=7.15°`, `turn=0.45`, `wingRegime=PropellerDash`), serta rilis halus ke `Glide`.
  - `MainMenu_Lobby.unity`: Live preview `#14 Propeller Dash` terverifikasi `wingRegime=PropellerDash` dengan `0` errors/warnings.

---

## 2026-11-18 — Eksekusi Pilar 1 Pra-Sesi 4: Clean CV Preview & `DragonHeadLookController` (`HEAD_PITCH` Tahap 2)

### Eksekusi Utama (`[[laptop-a/checklist_finalisasi_pra_sesi_4]]`, `[[laptop-a/planning_revisi_titik_wajah_dan_pov]]`)
1. **`DragonHeadLookController.cs` & Integrasi `HEAD_PITCH` Murni (`DragonFlightController.cs`):**
   - Menambahkan komponen `DragonHeadLookController` (`Neck_01` `25%`, `Neck_02` `35%`, `Head` `40%`, batas dongak `+28°` / tunduk `-22°` di sumbu pitch lokal terhadap rig `Dragon_Model.fbx`) murni dari `FlightCommand.HeadPitch` (`tokens[43]`) tanpa memengaruhi `yaw`/`roll` horizontal maupun `TurnInput`.
   - Menghubungkan ke `DragonFlightController.cs`: mode darat `#03 Grounded Look Up` menjaga posisi/yaw diam dengan pembatasan pitch badan (`<= 5°`) saat rig kepala siap; mode udara mengikuti pitch kepala halus (`cameraPitchFollowFactor`) serta memicu transisi **`Fly Up`** (`HeadPitch > 0.12f`) dan **`Pitch Down`** (`HeadPitch < -0.12f`) tanpa ketergantungan `chest_elevation`.
   - Memasang & menyambungkan `DragonHeadLookController` di `LiveDragonPreviewController.cs` (live preview kalibrasi `SCREEN 01`), `UISceneAndPrefabBuilder.cs` (preview rig & `/Dragon_Player` di `UpgradeGameplayScene`), serta `DragonFlightTestSceneBuilder.cs`, lalu menjalankan ulang `UISceneAndPrefabBuilder.BuildAll()` (`0` errors).
2. **Verifikasi Visual Clean CV Preview & Regresi Python (`active_cv_sender.py`):**
   - Memverifikasi output JPEG UDP port `5053` melalui `draw_debug_overlay()` + `VideoUDPSender` (`4892` bytes, `320×180`): frame hanya menampilkan skeleton, keypoints, 3 titik wajah (Hidung, Dagu, Bibir), dan angka `FPS: 30` di sudut kiri atas (nol kotak HUD/teks telemetri).
   - Menjalankan ulang ke-9 rangkaian test Python CV (`test_tahap1_face_tracking.py`, `test_phase1_validation.py`, `test_phase2_validation.py`, `test_phase4_validation.py`, `test_phase5_validation.py`, `test_phase6_final_session.py`, `test_additional_gestures.py`, `test_e2e_flight_physics.py`, `test_all_17_poses_alignment.py`) via `ARDragonSkyGuardian_CV\venv\Scripts\python.exe` — seluruhnya **100% PASS**.
3. **Verifikasi Play Mode Unity 6 (`0` Errors, `0` Warnings):**
   - `MainIsland_Gameplay.unity` via UDP `5052` (protokol 44-token):
     - Grounded + `HEAD_PITCH = +0.75` $\rightarrow$ `#03 Grounded Look Up`, `headDeg = 15.67°`, `speed = 0.00`, `vy = 0.00`, `turn = 0.000`, posisi & `yaw = 345.00°` tetap diam.
     - Airborne + `HEAD_PITCH = +0.75` (tanpa chest flag) $\rightarrow$ `#07 Fly Up` (`vy = 4.87 m/s`, `headDeg = 20.55°`, `bodyPitch = 13.03°`, `turn = 0.000`, `yaw = 345.00°` terkunci).
     - Airborne + `HEAD_PITCH = -0.75` $\rightarrow$ `Pitch Down` (`vy = -3.86 m/s`, `headDeg = -16.15°`, `bodyPitch = -14.02°`, `turn = 0.000`, `yaw = 345.00°` terkunci).
     - Airborne + `HEAD_PITCH = +0.75` + `Lean/Steering = +0.65` $\rightarrow$ `Fly Up` (`vy = 4.87 m/s`, `headDeg = 20.55°`) sekaligus berbelok mulus (`turn = 0.650`, `yaw` bergerak ke `338.89°`).
   - `Test_Dragon_Flight.unity` (`headReady = True`, `0` errors, `0` warnings) & `MainMenu_Lobby.unity` (`LiveDragonPreviewController` + `DragonHeadLookController` `headReady = True`, `headDeg = 20.55°`, `0` errors, `0` warnings).

---

## 2026-11-18 — Penyelarasan 17 Pose Kontrol (`#03, #06, #07, #09–#12, #14, #15A–C`), Tahap 2 `HEAD_PITCH`, Clean CV Preview, & UI `SCREEN 01`/`07`

### Eksekusi Utama (`[[laptop-a/panduan_detail_pose_dan_sync_laptop_b]]`, `[[laptop-a/planning_revisi_titik_wajah_dan_pov]]`, `[[laptop-a/planning_audit_pose_dan_clean_cv_preview]]`)
1. **Proteksi Non-Regresi 6 Pose Teruji (`#01, #02, #04, #05, #08, #13`):**
   - Mempertahankan penuh logika & ambang `UpperArmLocomotionGate` (`0.42 / 0.30`), `ContinuousLeanClassifier` / `AdaptiveThresholdEngine`, serta `TuckDiveClassifier` (`1.25 / 1.30`, `0.12s`) tanpa regresi.
2. **Perbaikan Classifier Python CV (`pose_stabilizer.py`, `mouth_tracker.py`, `motion_recognizer.py`, `cv_locomotion_config.json`):**
   - **`#03` & `#15B` (`mouth_tracker.py`):** Menurunkan deadzone `HEAD_PITCH` (`2.2° -> 1.6°`) dan ambang `MOUTH_OPEN` (`ON=0.35`, `OFF=0.25`, `mar_scaled=(mar-0.04)*2.65`, `jaw_open*1.10`) agar dongakan & bukaan mulut lebih responsif tanpa drift netral.
   - **`#06` & `#15A` (`FlapStrokeClassifier`):** Menambahkan hysteresis kecepatan (`0.36 / 0.24`), jendela penahan `0.34s`, serta evaluasi daya kepakan pada kedua fase `UPSTROKE` dan `DOWNSTROKE` (termasuk menyapu turun `#15A` dari posisi terangkat).
   - **`#07` vs `#15C` (`FlyUpClassifier` & `GestureStateMachine`):** Menambahkan deteksi **Open Y-Pose** (`avg_flap_height >= 0.36`, `wingspan_ratio >= 1.55`, `avg_elbow_angle >= 132°`, siku sejajar/di atas bahu, ditahan stabil `0.14s`) di samping jalur legacy *chest elevation + flap*, dan memisahkan tegas dari `#15C Solaria Dew Rain` (**Double I-Pose `11`** sempit `0.45..1.40` di atas kepala).
   - **`#09 Hover` (`HoverClassifier`):** Menyelaraskan ke **L-Pose `90°`** (`55°..130°`, `forearm_lateral_spread >= 1.05`, `wingspan_ratio >= 1.15`) baik dalam mode **tahan statis (`hover_power >= 0.65`)** maupun kepakan ringan lengan bawah (`wrist_vert_speed >= 0.25`), dengan rilis instan saat lengan diluruskan diam.
   - **`#10 Gentle Descent` (`GentleDescentClassifier`, `pose_stabilizer.py`):** Mengubah logika dari *Tucked T-Pose* menjadi **Low-A Pose** (`left/right_arm_down_angle_deg` dalam `24°..66°`, siku lurus `>= 135°`, `wingspan_ratio >= 1.38`, `avg_flap_height <= -0.22`) yang terpisah aman dari `#01 Idle` maupun `#08 Glide`.
   - **`#11 Y-Dive` & `#12 W-Pose` (`FlyDownClassifier`):** Memisahkan `#12 W-Pose` berdasarkan siku turun rendah (`>= 0.16 * tl` di bawah bahu), sudut siku `15°..118°`, dan lengan bawah vertikal (`<= 0.65 * sw`, `wingspan <= 2.40`) tanpa terkendala `is_arms_tucked`, serta `#11 Y-Dive` berdasarkan siku sejajar/di bawah bahu dengan lengan bawah melebar keluar (`80°..152°`).
   - **`#14 Propeller Dash` (`PropellerRotationClassifier`):** Mengukur orbit pergelangan terhadap **siku masing-masing (`le`/`re`)** sambil memvalidasi bentangan lengan atas horizontal `180°` (`abs(le_x - ls_x)/sw >= 0.30`), dan mengangkat prioritas `PROPELLER_DASH` ke urutan teratas agar tidak tertutup `FlyDownY`.
3. **Clean CV Preview (`active_cv_sender.py`):**
   - Menghapus kotak HUD telemetri besar serta label teks (`NOSE`, `CHIN`, `MOUTH`, `LIPS`) pada frame `draw_debug_overlay()` (port `5053`), menyisakan **skeleton + keypoints + 3 titik wajah + indikator ringkas `FPS: xx`** di pojok kiri atas, serta meneruskan `head_pitch` ke `gesture_sm.update()`.
4. **Tahap 2 `HEAD_PITCH` & Sinkronisasi Unity (`IPoseProvider.cs`, `UDPPoseReceiver.cs`, `IFlightInputProvider.cs`, `DefaultFlightInputMapper.cs`, `DragonFlightController.cs`):**
   - Membaca `tokens[43]` (`HeadPitch`) di `UDPPoseReceiver.cs`, menyelaraskan `_smoothedMouth > 0.35f` (`#15B`), dan memperketat `_isHandsHighUp` ke Double I-Pose `11` (`wristSpanRatio <= 1.40f && !IsFlyUp`).
   - Menghapus fallback paksa `|| isUpperArmsTucked` pada `isGentle` di `DefaultFlightInputMapper.cs` saat stream gate aktif agar `#01 Idle` tidak tertukar `#10 Gentle Descent`.
   - Menghubungkan `HeadPitch` murni ke `DragonFlightController.cs` untuk `#03 Grounded Look Up`, `#07 Fly Up` (termasuk static Y-Pose), dan trim vertikal tanpa memengaruhi kemudi `TurnInput`.
5. **Pembaruan 17 Kartu Pose & Teks UI (`SCREEN 01` & `SCREEN 07`):**
   - Mengekstrak `assets/poses_cv_vector/` (`Pose_01_GroundedIdle.png` .. `Pose_15C_Solaria_DewRain.png`), menyalin ke `Assets/UI/Components/06_Poses/`, dan mendaftarkannya di `UISpriteSetupUtility.cs` (`157` PNG, `75` border, `17/17` official pose sprites).
   - Memperbarui `PoseGuideCatalog.cs` (`PoseCount = 17` untuk `#01..#14, #15A, #15B, #15C`), `PoseGuideOverlayUI.cs`, `CalibrationPanelUI.cs` (live `#03`, `#15B`, `#15C`), dan `UISceneAndPrefabBuilder.cs` (grid `3×6` modal `POSE GUIDE · ALL 17 POSES`, badge `17 POSES`, serta teks `ARM GATE RULE` dengan `Low-A = Gentle Descent`), lalu menjalankan `BuildAll()`.

### Hasil Verifikasi
- **Python CV (9 Suite, 100% PASS):** `test_tahap1_face_tracking.py` (`7/7`), `test_phase1_validation.py` (`8/8`), `test_phase2_validation.py` (`6/6`), `test_phase4_validation.py` (`7/7`), `test_phase5_validation.py` (`7/7`), `test_phase6_final_session.py` (`7/7`), `test_additional_gestures.py` (`10/10`), `test_e2e_flight_physics.py` (`6/6`), dan `test_all_17_poses_alignment.py` (6 pose terkunci zero-regresi + 11 pose update + clean CV preview).
- **Unity 6 (`6000.3.7f1`):** `UISpriteSetupUtility.SetupAllSprites()` (`157` sprites, `17/17` pose sprites), `UISceneAndPrefabBuilder.BuildAll()` (`0` errors, `0` warnings), Play Mode `MainMenu_Lobby.unity` (kartu `1×4` & modal `SCREEN 07` 17 kartu + highlight `#15B`), `MainIsland_Gameplay.unity` (UDP `#03 Grounded Look Up`, `#15B` + `#15C`), dan `Test_Dragon_Flight.unity` (UDP `#07 Fly Up`) seluruhnya **lulus dengan `0` errors & `0` warnings**.

---

## 2026-11-18 — Implementasi & Konfigurasi Optimasi Token Laptop B (*The Optimal Lean Stack*)

*(Lihat ringkasan memori sesi di [[Setup Optimasi Token Laptop B]].)*

### Eksekusi Utama (`[[laptop-a/planning_optimasi_token_laptop_b]]`)
1. **`.antigravityignore` di Root Workspace:**
   - Membuat `.antigravityignore` sesuai template Bagian 3A (`planning_optimasi_token_laptop_b.md`) untuk mengecualikan direktori build/temp/metadata Unity (`/[Ll]ibrary/`, `/[Tt]emp/`, `/[Oo]bj/`, `/[Bb]uild/`, `/[Bb]uilds/`, `/[Ll]ogs/`, `/[Uu]ser[Ss]ettings/`, `/[Mm]emoryCaptures/`, `*.meta`, dll.), cache IDE/Python (`**/__pycache__/`, `**/*.pyc`, `.pytest_cache/`, `.venv/`, dll.), model/aset 3D berat (`*.blend`, `*.fbx`, `*.obj`, dll.), arsip & dokumen (`*.zip`, `*.xlsx`, `*.docx`, `*.pdf`, dll.), media dump, serta isolasi `brain/laptop-a/memories/` dan `brain/.obsidian/`.
2. **Instalasi Rust Token Killer (`rtk`):**
   - Mengecek `rtk --version` (belum terpasang), lalu menginstal `rtk-ai.rtk` versi `1.2.0` melalui `winget install rtk-ai.rtk` (binary terpasang di `C:\Users\ASUS\AppData\Local\Microsoft\WinGet\Packages\rtk-ai.rtk_Microsoft.Winget.Source_8wekyb3d8bbwe\rtk.exe` dan terdaftar di User `PATH`; memerlukan restart shell/IDE agar panggilan bare `rtk` mewarisi User `PATH` terbaru).
3. **Pembaruan `AGENTS.md`:**
   - Menambahkan section `## ⚡ Aturan Efisiensi Token & Eksekusi Minimalis (Laptop B)` yang mencakup aturan kompresi terminal via `rtk`, prinsip coding minimalis (*Ponytail-Lite / YAGNI* dengan pengecualian wajib menjaga kontrak `kontrak_integrasi_ui_dan_progress.md`, *Hybrid UI*, `[SerializeField]`, dan namespace `DragonWingAR.*`), serta *Session Hygiene* (`/compact`).

### Hasil Verifikasi (Sanity Check)
- **`rtk --version` & `rtk git status`:** Awalnya terverifikasi via `rtk.exe` (`rtk 1.2.0`, `* master...origin/master`, exit code `0`); setelah restart sesi/IDE, pemanggilan bare `rtk --version` & `rtk git -C brain status` terverifikasi aktif langsung dari `PATH`.
- **Keterbacaan Skrip Inti:** Berkas `.cs` (`Assets/_Scripts/`), `.py` & `.json` (`ARDragonSkyGuardian_CV/`), serta `.md` (`AGENTS.md`, `brain/`) tetap terbaca dan terindeks normal tanpa terblokir `.antigravityignore`.

---

## 2026-11-16 — Tahap 1 (Perbaikan Temuan Uji Kamera): Kerangka Dobel, UI Over-Area, & Deteksi Jarak Jauh

### Perbaikan 3 Temuan Visual & Jarak Jauh
1. **Kerangka Badan Dobel (`CalibrationPanelUI.cs`, `InGameFlightHUD.cs`):**
   - Menjadikan wireframe `UISkeletonOverlayGraphic` aktif **hanya saat `!hasVideo`** (`!hasVideo && poseReceiver != null && poseReceiver.IsPoseDetected`), karena frame video UDP `5053` dari `active_cv_sender.py` sudah membawa overlay kerangka tubuh + 3 titik wajah sehingga tidak lagi menggambar dua kerangka bertumpuk.
2. **UI Over-Area di Kalibrasi & Pause (`UISceneAndPrefabBuilder.cs`):**
   - Memasukkan `LeftFooterActionsBar` (`TrackingStatusBadge`, `Btn_Recalibrate`, `Btn_ResetDefault`) serta `RightFooterBar` baru (`FooterStatusPill`, `FooterMotionPill` via `HorizontalLayoutGroup` + `autoSizing`) pada `PF_CalibrationPanel.prefab` ke posisi aman di dalam tepi stepped parchment `UI_Panel_Parchment_Calib_9Slice.png` (`bottom = 26 px`, `left/right = 28 px`), serta menaikkan `MirrorAndSidebar` & `PreviewViewport` (`bottom = 70 px`).
   - Menambahkan scrim gelap (`RGBA(0.082, 0.075, 0.067, 0.960)`) pada `Pause_CalibrationSubPanel` (`PF_PauseModal.prefab`) dan merakit ulang seluruh UI via `UISceneAndPrefabBuilder.BuildAll()`.
   - Merampingkan panel HUD telemetry OpenCV di `active_cv_sender.py` (`360x298`) agar tidak terlalu memenuhi frame kamera kecil (`480p`).
3. **Pembacaan Titik dari Jarak Jauh (`active_cv_sender.py`, `mouth_tracker.py`, `pose_stabilizer.py`):**
   - Menaikkan resolusi inferensi `YOLOv8n-pose` pada CUDA menjadi `imgsz=960` (tetap `640` di CPU) dengan default `--conf 0.20` serta menurunkan `CONFIDENCE_MIN` & `CORE_CONFIDENCE_MIN` ke `0.20`.
   - Menambahkan *grace hold* pose `0.35s` di `active_cv_sender.py` (memegang pose terakhir dengan state `DEGRADED` saat 1–2 frame drop jarak jauh agar tidak berkedip `LOST`/`STANDBY`).
   - Menghapus batas minimum crop `0.30 * min(w,h)` di `MouthTracker` saat `shoulder_width_norm` tersedia, menambahkan *upsampling* `INTER_CUBIC` crop kecil ke `224×224`, mempertahankan jangkar `_last_face_norm_xy`, fallback full-frame `640 px`, serta ambang `FaceLandmarker` `0.30` / hold `10` frame (`0.40s`).

### Hasil Verifikasi Lanjutan
- **Python CV:** `test_tahap1_face_tracking.py` (`7/7`), `test_phase1_validation.py` (`8/8`), `test_phase4_validation.py` (`7/7`), `test_additional_gestures.py` (`10/10`), `test_e2e_flight_physics.py` (`6/6`), serta uji live `active_cv_sender.py` CUDA `imgsz=960` (`10` paket UDP 44-float & JPEG `7493` byte lulus).
- **Unity Play Mode (`MainIsland_Gameplay.unity`):** Terverifikasi visual bebas kerangka dobel (`pipSkelActive=False` & `calibSkelActive=False` saat video aktif), seluruh footer bar Kalibrasi berada rapi di dalam border perkamen, scrim Pause Calibration bersih, dan `0` compile/console errors.

---

## 2026-11-16 — Tahap 1: Revisi 3 Titik Pelacakan Wajah (Hidung, Dagu, Mulut) & `HEAD_PITCH` di Python CV

### Eksekusi Utama (`[[laptop-a/planning_revisi_titik_wajah_dan_pov]]` — Tahap 1)
1. **Tracker 3 Titik Wajah & Anti-Jitter (`ARDragonSkyGuardian_CV/mouth_tracker.py`):**
   - Meningkatkan `MouthTracker` pada crop wajah adaptif (`shoulder_width_norm` + offset dagu + fallback full-frame berasio) untuk melacak **3 titik wajah MediaPipe `FaceLandmarker`**: **Hidung (`IDX_NOSE_TIP = 4`)**, **Dagu (`IDX_CHIN = 152`)**, dan **Mulut (`IDX_UPPER_LIP = 13`, `IDX_LOWER_LIP = 14`, sudut `#61/#291`)**.
   - Implementasi `compute_nose_chin_elevation_deg()` secara isotropik menggunakan vektor 3D `(Chin -> Nose)` lengkap dengan kompensasi *face roll* (garis pipi `#234/#454`) serta kompensasi penurunan rahang (*excess lip gap*) agar membuka mulut lebar (`MOUTH_OPEN`) tidak memicu sinyal mendongak palsu.
   - Verifikasi empiris pada `FaceLandmarker` + `facial_transformation_matrixes` Camera 0 mengunci konstanta netral kanonik `DEFAULT_NEUTRAL_ELEVATION_DEG = -38.8°` (bersesuaian `< 0.1°` terhadap matriks rotasi 3D wajah), diikuti `AdaptiveOneEuroFilter` dan kurva *rescaled deadzone* (`±2.2° .. ±16.0°`) untuk menghasilkan `HEAD_PITCH` kontinu dalam rentang `[-1.0 .. +1.0]`.
   - Menghitung `MOUTH_OPEN` (`[0.0 .. 1.0]`) kontinu bebas step biner lama `0.20 -> 1.0`, menggunakan jarak bibir isotropik (`MAR` vs `jawOpen`), filter One-Euro, serta ambang hysteresis `0.45 / 0.35`.
   - Menyediakan `set_neutral_baseline()`, `recenter_neutral_baseline()`, dan `reset_neutral_baseline()` serta mempertahankan `process_crop()` demi kompatibilitas penuh.
2. **Kalibrasi Baseline Netral & Protokol UDP 5052 (`player_calibrator.py`, `udp_sender.py`):**
   - `player_calibrator.py`: Menambahkan `neutral_head_pitch_deg` pada `PlayerBiometricProfile` yang ikut disampel pada `STEP1_NEUTRAL` dan tersimpan di profil JSON.
   - `udp_sender.py`: Menambahkan argumen opsional `head_pitch` di `PoseUDPSender.send_pose()` sebagai token ke-44 (`tokens[43]`), sekaligus menjaga format 43 token saat `head_pitch=None` agar suite lama tetap lulus 100%.
3. **Integrasi Pipeline & Dekoupling Total dari Dada/Kemudi (`active_cv_sender.py`):**
   - **Menghapus sepenuhnya `chest_elevation` / `chest_up_signal` dan `mid_shoulder - nose` dari perhitungan pitch (`raw_body_pitch`):** kini `BODY_PITCH` (`tokens[21]`) dan `HEAD_PITCH` (`tokens[43]`) membaca 100% dari `MouthTracker.process_face()` (Hidung & Dagu), sedangkan `udp_mouth_val` (`tokens[12]`) mengirim nilai kontinu `mouth_open` (`0.0 .. 1.0`).
   - Menghapus komponen `head_lat_ratio` pada estimasi pinggul virtual saat duduk (`seated_lean_deg = shoulder_roll_deg * 1.45`) sehingga pergerakan kepala tidak pernah mencemari kemudi horizontal `steering_input`.
   - Memperbarui `draw_debug_overlay()` untuk menggambar penanda visual `NOSE` (sian), `CHIN` (kuning-oranye), garis penghubung vertikal Hidung-Dagu, bibir atas-bawah (`LIPS` / hijau `MOUTH` saat `> 0.45`), meter visual `HEAD_PITCH`, readout `MOUTH_OPEN`, serta pintasan `'N'` (*Zero Head Baseline*), `'C'` (Kalibrasi), dan `'R'` (Reset).

### Hasil Verifikasi Tahap 1
- **Unit & Geometry Suite (`test_tahap1_face_tracking.py`):** **7/7 PASS**: Netral (`-38.81° -> +0.000`), Mendongak `+8°/+16°` (`+0.420 / +1.000`), Menunduk `-8°/-16°` (`-0.420 / -1.000`), Invariansi jarak (`1.8x` vs `0.55x` identik `+0.565`), Invariansi roll `25°` (`+0.000`), Buka mulut lebar di netral (`+0.000`), Kalibrasi baseline, serta paket UDP `44-float` (`MOUTH_OPEN=0.68`, `HEAD_PITCH=+0.55`, `Steering=0.00`).
- **Suite Regresi Python CV:** `test_phase1_validation.py` (`8/8` PASS), `test_phase4_validation.py` (`7/7` PASS), `test_additional_gestures.py` (`10/10` PASS), dan `test_e2e_flight_physics.py` (`6/6` PASS).
- **Live GUI Preview & Unity Non-Regression:**
  - Uji live `active_cv_sender.py` GUI preview (`[0] USB2.0 HD UVC WebCam 1280x720`, CUDA): menerima `12` paket UDP 44-float + frame JPEG `8123` byte tanpa error.
  - Uji Play Mode Unity (`MainIsland_Gameplay.unity`) menerima paket 44-float dari Python via `UDPPoseReceiver`: `mouth=0.72`, `waterBreath=True`, `bodyPitch=0.65`, `turn=0.00`, `0` console error/warning baru.

---

## 2026-11-16 — Patch 1 Fokus 1: Sistem Kamera Dinamis, Perapian HUD PIP, Navigasi Pause $\rightarrow$ Calibration, & Auto-Persistence

### Eksekusi Utama
1. **Rasio Kamera Dinamis (`16:9`, `4:3`, & `9:16` Vertikal) — Python CV & Unity:**
   - `ARDragonSkyGuardian_CV/camera_stream.py`: Sinkronisasi resolusi aktual kamera (`actual_width`, `actual_height`, `_sync_actual_resolution()`, `get_resolution()`).
   - `ARDragonSkyGuardian_CV/udp_sender.py`: Memperbarui `VideoUDPSender.compute_scaled_size(src_w, src_h)` agar rasio aspek asli frame terjaga di dalam `max_edge = 320` (`1280x720 -> 320x180`, `640x480 -> 320x240`, `720x1280 -> 180x320`, `480x640 -> 240x320`) dengan batas aman UDP JPEG `<= 65000` byte.
   - `ARDragonSkyGuardian_CV/pose_stabilizer.py` & `player_calibrator.py`: Menambahkan kompensasi rasio aspek (`aspect_ratio`, `aspect_scale_x`) di `BiometricNormalizer.calculate_biometrics()` serta `left_arm_reach_norm` / `right_arm_reach_norm` agar perhitungan metrik biometrik dan sudut tubuh tetap invarian di kamera `16:9`, `4:3`, dan `9:16`.
   - `ARDragonSkyGuardian_CV/active_cv_sender.py`: Membaca resolusi aktual frame, menyesuaikan seated virtual hips dengan `aspect_scale_x`, dan meng-clamp dimensi `draw_debug_overlay()` agar aman pada frame vertikal sempit.
   - Unity UI (`CalibrationPanelUI.cs`, `InGameFlightHUD.cs`, `UISceneAndPrefabBuilder.cs`): Memasang `AspectRatioFitter` (`AspectMode.FitInParent`) pada `mirrorVideoImage`, `skeletonOverlay`, `pipVideoRawImage` (`VideoRaw`), dan `pipSkeletonOverlay`, memanggil `UpdateCameraAspectRatio(width, height)` saat resolusi `Texture2D` berubah, serta menjaga latar pillarbox gelap simetris.
2. **Perapian HUD In-Game (`InGameFlightHUD.cs`):**
   - Menyembunyikan `pipCameraControlsCard` (`PIP_CameraControlsCard`) & `pipCameraModule` selama gameplay aktif; pojok kanan bawah hanya menampilkan kotak PIP webcam/skeleton + badge status `PipStatusBadge` (`CV: LIVE` / `CV: OFF`).
3. **Alur Pause $\rightarrow$ Calibration & Camera Tanpa Reload Scene:**
   - `PF_PauseModal.prefab`, `PauseModalUI.cs`, `CalibrationPanelUI.cs`, `UISceneAndPrefabBuilder.cs`: Menambahkan tombol `Btn_Calibration` (`CALIBRATION & CAMERA`, `UI_Btn_TopChrome_Cream_9Slice`) di antara `Btn_Resume` dan `Btn_Settings`. Saat diklik, Pause Card disembunyikan dan overlay `Pause_CalibrationSubPanel` aktif secara live (`Time.timeScale = 0f`) dengan `InGamePauseMode` (`BACK TO PAUSE` pada tombol pojok & `RESUME FLIGHT`).
   - `UDPPoseReceiver.cs`, `DefaultFlightInputMapper.cs`, `DragonWingAnimator.cs`, `DragonFlightController.cs`, `LiveDragonPreviewController.cs`: Menggunakan `unscaledDeltaTime`/`unscaledTime` agar mirror skeleton & preview Fanamir 3D tetap responsif saat `Time.timeScale = 0f` sementara naga gameplay membeku aman (`if (Time.deltaTime <= 0f) return;`).
4. **Auto-Persistence Kamera (`PlayerPrefs`):**
   - `PythonProcessManager.cs` & `CameraControlModuleUI.cs`: Menyimpan perangkat terakhir ke `PlayerPrefs` (`"DragonWingAR_LastCameraDevice"`, `"DragonWingAR_LastCameraIndex"`) melalui `SelectCamera()` dan otomatis memilih kamera tersimpan atau fallback indeks `0` melalui `GetSavedOrDefaultCamera()` / `ApplySavedOrDefaultCameraSelection()` saat startup (tetap mempertahankan `autoLaunchPython = false`).

### Hasil Verifikasi
- **Python CV:**
  - `test_phase1_validation.py`: `8/8` PASS (`0.02s`).
  - `test_additional_gestures.py`: `10/10` PASS (`5.97s`).
  - `test_e2e_flight_physics.py`: `6/6` SUITES PASSED (`100%`).
  - Uji `VideoUDPSender.compute_scaled_size()` & invariansi `BiometricNormalizer.calculate_biometrics()`: metrik identik (`wingspan_ratio=4.5, chest_elevation=1.875, lean_angle_deg=0.0, avg_elbow_angle=180.0, upper_arm_openness=0.875`) lintas `16:9`, `4:3`, dan `9:16`.
- **Unity Build & Play Mode (`MainMenu_Lobby.unity`, `MainIsland_Gameplay.unity`, `Test_Dragon_Flight.unity`):**
  - `MainMenu_Lobby.unity`: Auto-select default webcam indeks `0` (`USB2.0 HD UVC WebCam`), verifikasi `9:16` (`tex=180x320`, `fitterRatio=0.5625`, pillarbox simetris & skeleton selaras), serta persistensi `PlayerPrefs` (`OBS Virtual Camera` indeks `6` dan kembali ke `USB2.0 HD UVC WebCam` indeks `0`) terverifikasi saat restart Play Mode.
  - `MainIsland_Gameplay.unity`: `PIP_CameraControlsCard` tersembunyi, `InGameFlightHUD` menyesuaikan `AspectRatioFitter` dinamis untuk `16:9` (`320x180`, `1.7778`, live Cam `0` dengan badge `CV: LIVE`), `4:3` (`320x240`, `1.3333`), dan `9:16` (`180x320`, `0.5625`); alur penuh `ESC · PAUSE` $\rightarrow$ `CALIBRATION & CAMERA` $\rightarrow$ `OPEN POSE GUIDE` / `RECALIBRATE` $\rightarrow$ `BACK TO PAUSE` $\rightarrow$ `RESUME FLIGHT` berjalan mulus dan `DragonFlightController` langsung lanjut terbang.
  - `Test_Dragon_Flight.unity`: Non-regression terverifikasi (`PIP_CameraControlsCard` tersembunyi, `CALIBRATION & CAMERA` berfungsi penuh, `autoLaunchPython=false`, `0` compile errors & `0` Play Mode console errors).

---

## 2026-11-15 — Integrasi Model Pulau v2 (`Pulau_Archipelago.fbx`), Setup URP, Scene `MainIsland_Gameplay`, & Feature-to-UI Sync
- **Catatan Memori:** [[Integrasi Pulau v2 dan Sinkronisasi UI]]

### Eksekusi Utama
- **Ekspor & Persiapan Aset Kepulauan 3-Biome (`assets/blender/upgrade model pulau/Pulau_Archipelago.blend`):**
  - Menjalankan inspeksi & ekspor Blender `5.1.2` CLI hanya pada `349` objek mesh gameplay (`COL_Island_Terrain`, `COL_Biome1_Water`, `COL_Island_Rocks`, `COL_Village_Houses`, `COL_Foliage_Trees`; mengecualikan `COL_Cameras_Lights` dan `COL_Reference`) dengan orientasi `-Z Forward, Y Up` & `FBX_SCALE_UNITS` menjadi `AR Dragon Sky Guardian/Assets/Assets 3D/Pulau_Archipelago/Pulau_Archipelago.fbx` (`4,584,124` bytes).
  - Menyiapkan `69` tekstur PBR aktif ke `Assets/Assets 3D/Pulau_Archipelago/Textures/`: menyalin `23 *_BC.png` (sRGB) & `23 *_N.png` (NormalMap Linear), serta mengonversi `23` peta grayscale *Roughness* (`*_R.png`) dari Blender menjadi peta RGBA *Metallic-Smoothness* URP (`*_MS.png`: `R=G=B=0`, `A = 255 - Roughness`) agar permukaan terrain, batu, pohon, dan rumah desa tetap bergaya *stylized matte* (bebas artefak basah/plastik di Unity URP).
- **Otomasi Setup URP, Prefab, & Scene (`Assets/Editor/IslandArchipelagoSetupUtility.cs`):**
  - Mengonfigurasi `TextureImporter` (`69` tekstur), membuat `23` material `Universal Render Pipeline/Lit` + `Mat_Archipelago_Ocean.mat` (Transparent `#184E66`, `alpha = 0.86`, smoothness `0.90`), dan memetakan ulang seluruh `23` material pada `Pulau_Archipelago.fbx` (`bakeAxisConversion = true`).
  - Merakit `Assets/Prefabs/World/PF_Pulau_Archipelago.prefab` dengan `Archipelago_Model` berskala `(6, 6, 6)` (`2400 × 2400 m`), memasang `MeshCollider` secara ketat hanya pada `SM_Archipelago_Terrain` dan `4` grup `SM_Rocks_*` (`5` collider total), menempatkan `Ocean_Surface` (`Y = 0`) & `SM_Biome1_Water_01` pada layer `Ignore Raycast` tanpa collider, serta membuat objek `Markers` hasil *raycast* (`Spawn_CenterPeak (0.00, 165.00, 15.00)`, `Village_B1_SmogValley (-562.40, 14.10, 274.30)`, `Village_B2_WildfireWoods (739.60, 12.90, 7.80)`, `Village_B3_DroughtPlateau (39.40, 14.70, -647.60)`).
  - Membuat scene gameplay baru `Assets/_Scenes/MainIsland_Gameplay.unity` dengan mengekstrak & menyelaraskan `/Dragon_Player` ke `Spawn_CenterPeak (0.00, 165.85, 15.00)` menghadap Yaw `-15°` ke lembah kaldera *Smog Valley*, `maxAltitude = 260f`, kamera `nearClipPlane = 0.5f` / `farClipPlane = 4200f`, serta *Linear Fog* `1150..4200 m`.
- **Feature-to-UI Sync & Pelestarian `Test_Dragon_Flight.unity`:**
  - `DragonFlightController.cs`: menaikkan default `maxAltitude` dari `160f` menjadi `260f` guna mengakomodasi elevasi puncak kepulauan (`~165 m` dasar + ruang terbang bebas di atas gunung).
  - `DragonGameSettings.cs`: menetapkan `DragonUITheme.FlightSceneName = "MainIsland_Gameplay"`, menambahkan `DragonUITheme.TestFlightSceneName = "Test_Dragon_Flight"`, serta mengunci default Stage 02 & Stage 03 (`IsStage2UnlockedPlaceholder` default `0` / `false`, plus penambahan `IsStage3UnlockedPlaceholder` default `0` / `false`).
  - `FinishModalUI.cs` & `InGameFlightHUD.cs`: memakai `SceneManager.GetActiveScene().name` agar tombol `REPLAY` me-reload scene aktif saat ini dan kapsul misi menampilkan `"TEST FLIGHT (0/3)"` di `Test_Dragon_Flight` vs `"ARCHIPELAGO (0/3)"` di `MainIsland_Gameplay`.
  - `MissionsPanelUI.cs` & `UISceneAndPrefabBuilder.cs`: memperbarui label Stage 01 menjadi `"ARCHIPELAGO"`, merefaktor `UpgradeFlightScene` -> `UpgradeGameplayScene(...)` untuk merakit HUD & prefab dormant pada `MainIsland_Gameplay.unity` (Build Index `1`) maupun `Test_Dragon_Flight.unity` (Build Index `2`), mendaftarkan ketiga scene (`0: MainMenu_Lobby`, `1: MainIsland_Gameplay`, `2: Test_Dragon_Flight`) di `EditorBuildSettings`, menyempurnakan `StarSlot_1..3` pada kartu misi (`80×80`, `StarIcon` `(0, 11)`, `StarLabel` `STAR 1..3` di `(0, 8.5)`) & `PF_FinishModal.prefab` (`y = 6 -> 11`), serta mengunci penuh **Stage 02 (`WILDFIRE CANYON`) & Stage 03 (`DROUGHT VALLEY`)** secara default (`STAGE 02 · LOCKED` `"CLEAR STAGE 01"`, `STAGE 03 · LOCKED` `"CLEAR STAGE 02"`, scrim terkunci pas bingkai gambar `(438×242)`, tombol charcoal `LOCKED` berkontras `#F2EFE7` yang memblokir peluncuran gameplay dan menampilkan toast `HintToast`, serta API `SetStage2Locked` / `SetStage3Locked` via `ApplyStageLockVisual`).

### Status UI Live vs Dormant (`MainIsland_Gameplay.unity`)
| Elemen UI / Prefab | Status di `MainIsland_Gameplay` | Sinkronisasi / Integrasi |
| :--- | :--- | :--- |
| `InGameFlightHUD` | **Live** | Aktif (`ARCHIPELAGO (0/3)`, altitude `165.9 m`, kontrol CV UDP, pip, & kompas) |
| `PF_PauseModal` (`PauseModalUI`) | **Dormant** (`active=False`) | Teruji toggle pause (`PauseToggleOK=True`) |
| `PF_QuestNarrativeBox` | **Dormant** (`active=False`) | Terpasang & siap dipanggil Sesi 4 |
| `PF_VillageChallengeOverlay` | **Dormant** (`active=False`) | Terpasang & siap menerima progres tantangan desa Sesi 4 |
| `PF_FinishModal` (`FinishModalUI`) | **Dormant** (`active=False`) | Terpasang; `HandleRetryClicked()` me-reload scene aktif (`MainIsland_Gameplay`) |

### Verifikasi Unity 6 (`6000.3.7f1`)
- Kompilasi skrip C# (`compilationFailed: false`, `0` errors) dan `IslandArchipelagoSetupUtility.SetupAllAndBuildMainIslandScene()`: `Renderers=350`, `BadShader=0`, `NullBaseTex=0`, `Colliders(5)`, `0` console error / warning.
- Inspeksi visual `capture_scene_view` & `capture_game_view` (Pulau 3 biome, Biome 1 Smog Valley, Biome 2 Wildfire Woods, lobby Missions, & Play Mode `MainIsland_Gameplay`): tekstur matte akurat, pohon terbakar/hidup & perumahan desa rapi, naga spawn `GROUNDED IDLE` (`ALT: 165.9 m | SPD: 0.0 m/s`, kapsul `--:-- · ARCHIPELAGO (0/3)`).
- Verifikasi Play Mode 3 scene (`MainIsland_Gameplay`, `MainMenu_Lobby` -> Stage 02 & 03 terkunci `Scrims=[True,True]` & memunculkan `HintToast` tanpa masuk scene terbang + `SetStageProgress` unlock/relock + `Stage 01 PLAY` transisi ke `MainIsland_Gameplay` + slot bintang `STAR 1..3` kosong/emas & `PF_FinishModal` `STAR 01..03`, dan regresi `Test_Dragon_Flight` `/Grass_Baseplate`): **lulus dengan `0` console error dan `0` console warning runtime**.

---

## 2026-11-15 — Implementasi UI v1.8 Boot Flow (`SCREEN 09` Splash Screen $\rightarrow$ `SCREEN 10` Start Screen $\rightarrow$ Lobby)

### Eksekusi Utama
- **Referensi Desain `v1.8`:** Memasang `docs/UI_Designs_Reference/09_SplashScreen.png` dan `docs/UI_Designs_Reference/10_StartScreen.png` (`1920×1080`, total `11` referensi desain) tanpa menambah/mengubah aset komponen (`140` PNG eksisting di `Assets/UI/Components/`).
- **`BootFlowController.cs` (`Assets/_Scripts/UI/BootFlowController.cs`):**
  - Mengimplementasikan fase boot berurutan `Splash` -> `StartScreen` -> `Lobby` di dalam `MainMenu_Lobby.unity` (berbagi `Background_AerialIsland` agar transisi ke `SCREEN 00 Missions` mulus tanpa jeda loading).
  - Menangani input **New Input System** pada `StartScreenPanel`: `Keyboard.current.anyKey.wasPressedThisFrame` (kecuali `Escape` yang langsung memanggil `QuitFromBoot()` / `QuitApplication()`), `Gamepad` button press (`ButtonControl` langsung di `Gamepad.all`), `Mouse` click / `Button` penuh layar (dengan filter posisi terhadap `startQuitButtonRect` & raycast `Btn_StartEscQuit` di lapisan atas), plus jeda aman `splashSkipUnlockDelay = 0.5s`, `startInputUnlockDelay = 0.25s`, dan fade transisi `0.25s` menuju Lobby (`Tab Missions`) agar klik tidak menembus ke kartu stage.
  - Menyediakan `public static bool SkipBootScreensOnce` serta menghubungkan `PauseModalUI.BackToLobby()` dan `FinishModalUI` (`HandleBackToLobbyClicked()` / placeholder `HandleNextLevelClicked()`) agar pemain yang kembali dari `Test_Dragon_Flight.unity` langsung membuka Tab `Missions` tanpa mengulang Splash/Start Screen.
  - Memastikan `LobbyNavigationManager.Update()` & `Start()` berkoordinasi aman (`IsInLobby` guard) sehingga `Escape` / tab switching tidak bentrok saat berada di Splash maupun Start Screen, dan `PythonProcessManager.autoLaunchPython` tetap `false`.
- **Perakitan `UISceneAndPrefabBuilder.cs` (`BuildLobbyScene()`):**
  - Merakit `SplashScreenPanel` (`#F2EFE7` solid opaque, `OuterParchmentFrame` `UI_Panel_Parchment_Full_9Slice` responsif `StretchFull` tanpa clipping tepi pada layar `16:9` maupun ultrawide, `UI_Crest_Baroque_Scrollwork`, `Splash_SubPlaque` `860×350`, pill `ANCIENT DEW DRAGON`, judul `FANAMIR`, `UI_Banner_Stepped_Modal_Blank` terkunci `600×77` `THE SKY GUARDIAN`, & footer `WEBCAM EXERGAMING · BODY-FLIGHT ADVENTURE`).
  - Merakit `StartScreenPanel` di atas `Background_AerialIsland` (`Start_TitlePlaque` `884×267` di langit atas `y = -144`, `Start_Crest`, `Start_GoldTag` `SKY EXERGAMING AR`, `FANAMIR`, `UI_Banner_Stepped_Modal_Blank` `600×77` `THE SKY GUARDIAN`, kapsul `Start_PromptCapsule` `UI_HUD_Capsule_Parchment_9Slice` `590×66` solid opaque + `PromptContentGroup` ber-`UI_Diamond_Fleuron_18` & pulse halus `PRESS ANY BUTTON TO START`, serta `Btn_StartEscQuit` `ESC · QUIT` sejajar `-52, -28`).

### Verifikasi Unity 6 (`6000.3.7f1`)
- Rekompilasi (`compilationFailed: false`, `errors: []`) dan `UISceneAndPrefabBuilder.BuildAll()` (`0` errors, `0` warnings).
- **Play Mode `MainMenu_Lobby.unity` (`capture_game_view` `1920×1080` + `EventSystem` Raycast):**
  - Startup membuka `SplashScreenPanel` (`09_SplashScreen` 1:1 dengan seluruh sudut & ornamen utuh), otomatis berpindah ke `StartScreenPanel` (`10_StartScreen` solid opaque, `autoLaunchPython = False`, `IsProcessRunning = False`).
  - Uji klik di area kapsul `Start_PromptCapsule` (`GraphicRaycaster` hitting `StartScreenPanel`) masuk mulus ke `BootPhase.Lobby` (`Tab = Missions`), sedangkan klik `Btn_StartEscQuit` (`hit = Btn_StartEscQuit`) memanggil `QuitApplication()` dan menghentikan Play Mode bersih.
- **Play Mode `Test_Dragon_Flight.unity` & Cross-Scene Return:**
  - Startup langsung di `Test_Dragon_Flight.unity` bersih (`/Dragon_Player`, `InGameFlightHUD`, `PauseModalUI`, `FinishModalUI`, `QuestNarrativeUI` dormant `False`, `autoLaunchPython = False`, `0` errors/warnings).
  - Uji memanggil `PauseModalUI.Pause()` -> `PauseModalUI.BackToLobby()` memuat `MainMenu_Lobby` langsung ke `BootPhase.Lobby` (`Tab = Missions`) dengan `SplashScreenPanel`/`StartScreenPanel` nonaktif, `SkipBootScreensOnce` tereset kembali ke `False`, `0` errors, `0` warnings, dan `0` proses Python CV tertinggal pasca Play Mode.

---

## 2026-11-15 — Penghapusan Panel Perkamen Luar Lobby agar Background Aerial Island Tampil Penuh (`MainMenu_Lobby.unity`)

### Masalah & Eksekusi
- **Masalah:** Pada `MainMenu_Lobby.unity`, kontainer luar `MainWindowParchment` (`1864×1036` memakai `01_Panels/UI_Panel_Parchment_Full_9Slice.png`) bersama pita belakang `SharedTopNavbar` (`02_Banners/UI_Header_Bar_Parchment_9Slice.png`) menutup hampir seluruh area `Background_AerialIsland` (`07_Backgrounds/UI_Bg_Lobby_AerialIsland.png`).
- **Perbaikan (`Assets/Editor/UISceneAndPrefabBuilder.cs`):**
  - Menghapus kontainer `MainWindowParchment` dan latar perkamen pada `SharedTopNavbar` sehingga navbar mengambang transparan langsung di atas langit (`RectTransform` top stretch `-104×64` di `(0, -20)`).
  - Menambahkan `AspectRatioFitter` (`AspectMode.EnvelopeParent`, `1920f / 1080f`) pada `Background_AerialIsland` agar latar pulau memenuhi viewport secara penuh tanpa *pillarboxing*.
  - Mengubah induk `TabContentRoot` langsung di bawah `Canvas` dengan rentang vertikal diperluas (`offsetMin (36, 22)`, `offsetMax (-36, -104)`, tinggi konten `954 px` pada kanvas `1920×1080`), lalu menjalankan ulang `UISceneAndPrefabBuilder.BuildAll()`.

### Verifikasi Unity 6 (`6000.3.7f1`)
- Rekompilasi (`compilationFailed: false`, `errors: []`) dan `UISceneAndPrefabBuilder.BuildAll()` (`0` errors, `0` warnings).
- **Play Mode `MainMenu_Lobby.unity` (`capture_game_view` `1920×1080`):** Ketiga tab (`Missions`, `Calibration`, `Settings`) menampilkan `Background_AerialIsland` secara penuh di belakang kartu/panel yang mengambang dengan header atas (`FANAMIR: THE SKY GUARDIAN`, `TabsRail`, `NavCamBadge`, `Btn_EscChrome`) tetap rapi tanpa clipping/overlap, `0` console errors, `0` warnings.
- **Play Mode `Test_Dragon_Flight.unity` (non-regresi):** Startup bersih (`/Dragon_Player`, `InGameFlightHUD`, `QuestObjectivePanel` default `TRAVEL`, `PF_QuestNarrativeBox` dormant `False`, `PythonProcessManager.autoLaunchPython = False`, `0` errors, `0` warnings, dan `0` proses Python CV tertinggal pasca Play Mode).

---

## 2026-11-15 — Implementasi UI v1.7 (`Fanamir: The Sky Guardian`, Quest Objective, `Text Auto-Next`, & `SCREEN 08` Auto-Play)

### Eksekusi Utama
- **Pemasangan Aset `assets/UI_Unity_Exportv2.zip`:**
  - Mengekstrak **6 PNG komponen baru** ke `AR Dragon Sky Guardian/Assets/UI/Components/` (`134 -> 140` PNG total: `01_Panels/UI_Panel_QuestObjective_9Slice.png`, `01_Panels/UI_Panel_NarrativeBox_9Slice.png`, `02_Banners/UI_Banner_SpeakerTag_Blank.png`, `06_Icons/UI_Icon_QuestScroll_Gold.png`, `06_Icons/UI_Icon_AutoPlay_Timer.png`, `06_Icons/UI_Icon_PromptNext_Arrow.png`) dan memperbarui `7` artboard referensi `1920×1080` (`00, 01, 02, 05, 06, 07, 08`) di `docs/UI_Designs_Reference/`.
  - Memperbarui `Assets/Editor/UISpriteSetupUtility.cs` untuk border C# `Vector4(L, B, R, T)` (`UI_Panel_QuestObjective_9Slice` `(24, 29, 24, 24)`, `UI_Panel_NarrativeBox_9Slice` `(30, 36, 30, 30)`, `UI_Banner_SpeakerTag_Blank` Horizontal 3-Slice `(8, 0, 8, 0)`), serta menambah alias menu `DragonWingAR/UI/Setup All UI Sprites` sembari menjaga menu lama tetap berfungsi. Hasil: `140` PNG terkonfigurasi (`75` border 9-Slice/3-Slice).
- **Pembaruan Branding Publik (`FANAMIR: THE SKY GUARDIAN` & `Fanamir`):**
  - Memperbarui teks UI publik pada `SharedTopNavbar/BrandTitle` (`"FANAMIR: THE SKY GUARDIAN"`), banner `PreviewHeaderTitle` (`"LIVE FANAMIR PREVIEW · {poseTitle}"` di `UISceneAndPrefabBuilder.cs` & `CalibrationPanelUI.cs`), dan deskripsi pose `#01–#03` pada `PoseGuideCatalog.cs` (`Dragon` -> `Fanamir`), dengan **tetap mempertahankan** namespace `DragonWingAR.*`, nama scene `Test_Dragon_Flight.unity`, `/Dragon_Player`, dan kontrak skrip internal.
- **Quest Objective Panel `SCREEN 02` (`InGameFlightHUD.cs` & `UISceneAndPrefabBuilder.cs`):**
  - Merakit `QuestObjectivePanel` di pojok kiri atas HUD (kompensasi padding PNG: `RectTransform (50, -30)` `444×137` → bodi visual `424×112` di `(60, 40)`, terpisah bersih `~128 px` di atas `GlobalVitalityBar`), memakai `UI_Panel_QuestObjective_9Slice`, `UI_Socket_Medallion_38_Dark` + `UI_Icon_QuestScroll_Gold`, pil `QUEST OBJECTIVE`, badge status dinamis (`TRAVEL` krem / `ACTIVE TASK` hijau `UI_Badge_Active_Green_Blank` + teks `#FFFFFF`), serta API `SetQuestObjective(...)` & `ResetQuestObjectiveToTravel(string villageName = "Smog Valley")` yang otomatis menampilkan state `TRAVEL` saat scene dimuat.
- **Pengaturan `Text Auto-Next` `SCREEN 05` (`DragonGameSettings.cs`, `SettingsPanelUI.cs`, `PF_SettingsPanel.prefab`):**
  - Menambahkan properti persisten `DragonGameSettings.NarrativeAutoNextSeconds` (`PlayerPrefs` key `"Setting_NarrativeAutoNextSec"`, rentang `2.0f–10.0f`, default `5.0f`) dan menyambungkannya ke `SettingsPanelUI` + `APPLY & CLOSE`.
  - Membangun ulang `PF_SettingsPanel.prefab` (`SettingsCard` `960×720` di `(0, -18)` agar bersih di `TabContentRoot` `1920×1080` maupun Game View docked tanpa menyentuh `TabsRail`), menampilkan `CrestEmblem`, banner `UI_Banner_Stepped_Modal_Blank` (`SETTINGS & AUDIO CONTROLS`), **5 baris slider `UI_Plaque_StatRow_9Slice` (`h = 58 px`, step `65 px`)** + soket gelap + ikon (`Master`, `BGM`, `SFX`, `Turn Sensitivity`, dan baris ke-5 `Text Auto-Next` ber-fill amber), modul kamera + hint panel, dan `Btn_ApplyAndClose` (`68 px`).
- **Quest Narrative Box Auto-Play `SCREEN 08` (`QuestNarrativeUI.cs` & `PF_QuestNarrativeBox.prefab`):**
  - Membuat skrip `QuestNarrativeUI.cs` (`QuestLineData`, `ShowNarrativeSequence(...)`, `HideNarrative()`, dan `[ContextMenu("Debug/Preview Quest Narrative")]`) serta prefab `PF_QuestNarrativeBox.prefab` yang terpasang **dormant (`SetActive(false)` default)** pada `Test_Dragon_Flight.unity` (`RectTransform (144, 22)` `1260×242` → bodi visual `1236×212` di kiri-bawah `x: 156..1392`, aman dari Track Vitality kiri & klaster PIP/Telemetri kanan `x >= 1440`).
  - Tanpa tombol Continue: memakai banner overlap atas-kiri tinggi terkunci `42 px` (`UI_Banner_SpeakerTag_Blank`, teks `#2D2926`), plakat biome `UI_Illust_Frame_Dark_9Slice`, teks krisis + strip `WHAT TO DO:` (`UI_Icon_PromptNext_Arrow`), serta widget kanan `LINE X / Y · AUTO-PLAY` + `AUTO-PLAY · HANDS-FREE` (`NEXT IN X.Xs / Y.Ys`, bar `Filled Horizontal Left` dari `DragonGameSettings.NarrativeAutoNextSeconds`, otomatis jeda saat `Time.timeScale <= 0` / `PauseModal.IsPaused`), dan setelah baris terakhir selesai langsung menutup diri & memanggil `hud.SetQuestObjective("ACTIVE TASK", true, ...)`.

### Verifikasi Unity 6 (`6000.3.7f1`)
- Rekompilasi (`codeLoadCount: 5`, `compilationFailed: false`, `errors: []`), `UISpriteSetupUtility.SetupAllSprites()` (`140` PNG sprites, `75` border, `0` errors), dan `UISceneAndPrefabBuilder.BuildAll()` (`0` errors, `0` warnings).
- **Play Mode `MainMenu_Lobby.unity` (`capture_game_view` `1920×1080` & `script_eval`):**
  - `SharedTopNavbar/BrandTitle = "FANAMIR: THE SKY GUARDIAN"`, Calibration banner `"LIVE FANAMIR PREVIEW · GLIDE (T-POSE)"`, Pose `#01–#03` di `PoseGuideOverlayUI` memakai `"Fanamir"`, Settings `SettingsCard` `960×720` bersih tanpa clipping (`CrestEmblem` tidak menyentuh `TabsRail`), 5 baris slider dengan `Row_TextAutoNext` default `5.0s` (`min=2, max=10`), pengubahan ke `4.0f` + `APPLY & CLOSE` tersimpan persisten di `PlayerPrefs` (`saved4=4`), `SolidCameraDropdown` opaque `#FBF6EB`, dan `PythonProcessManager.autoLaunchPython=False` / `IsProcessRunning=False`.
- **Play Mode `Test_Dragon_Flight.unity` (`capture_game_view` `1920×1080` & `script_eval`):**
  - Startup non-regresi bersih: `QuestObjectivePanel` tampil default `TRAVEL` (`"Fly to the marked village (Smog Valley)"`), `PF_QuestNarrativeBox` & modal dormant nonaktif (`False`), `/Dragon_Player` (`DragonFlightController`, `UDPPoseReceiver`) utuh, `Pause_SettingsSubPanel` memiliki `Row_TextAutoNext`.
  - Pengujian `QuestNarrativeUI.DebugPreviewQuestNarrative()`: animasi hitung mundur `NEXT IN X.Xs / Y.Ys` & fill bar berjalan mulus, `PauseModal.Pause()` membekukan timer akurat (`0.21s -> 0.21s`), menyelesaikan seluruh 3 baris (`2.0s × 3`) otomatis menutup box (`narrativeActive=False`) dan memicu `QuestObjectivePanel` beralih ke `ACTIVE TASK` (`"Disperse the smog — Flap your wings!"`), serta `ResetQuestObjectiveToTravel("Wildfire Woods")` mengembalikan state `TRAVEL`. Console akhir `0` errors, `0` warnings, dan `0` proses Python CV tertinggal pasca Play Mode.

---

## 2026-11-15 — Perbaikan *Rest-Pose Front Lift* pada Dynamic Wing Movement (`DragonWingAnimator.cs`)

### Masalah yang Ditemukan
- Model `Dragon_Model.fbx` memiliki pose awal impor di mana tulang lengan depan (`Wing_Shoulder` naik `+12.9°`, `Wing_Forearm` naik curam `+35.5°` dengan posisi pergelangan *wrist* lebih tinggi dari kepala `y = 0.554`) sedangkan keempat jari menjuntai curam ke bawah (`-19°` hingga `-55°`).
- Karena `CacheWingRig()` menyalin `bone.localRotation` secara langsung sebagai `RestLocalRotation` dan hampir semua regime di `ComputeWingTargets()` masih menambahkan bias positif `ShoulderPitchUp` / `ForearmPitchUp` (mis. `+24°/+10°` di `GroundedIdle`, `+15°/+8°` di `Hover`, `+18°` di `GentleDescent`, `+22°/+10°` di `FlyDownY`), animasi sayap dinamis mempertahankan kemiringan bagian depan sayap yang terangkat curam.

### Eksekusi Utama
- **Netralisasi Basis Sayap Zero-Twist (`DragonWingAnimator.cs`):**
  - Menambahkan `OrientWingBoneNeutral()` & `MirrorDirX()` pada `BuildWingSide()` untuk mengorientasikan ulang `Wing_Shoulder` → `Wing_Forearm` → 4 `Wing_Finger` secara berurutan ke bidang airfoil horizontal (`NeutralShoulderRightDir`, `NeutralForearmRightDir`, `NeutralFingerRightDirs`) sebelum merekam `RestLocalRotation` dan sumbu ortogonal (`AxisUp`, `AxisForward`, `AxisPitchUp`). Hasil mesh awal memelankan selisih depan-belakang ke hampir rata (`FrontY = +0.018`, `BackY = -0.024`).
- **Penyetelan Regime & Dekomposisi Fleksi Jari:**
  - Menghapus bias positif `PitchUp` berlebih pada `GroundedIdle` (`-4°/-2°`), `GroundedWalk` (`-3°/-2°`), `Takeoff`/`FlyUp`/`Flapping` (`stroke * 6f`), `Hover` (`3f + stroke * 6f`), `FlyDownY` (`6f`), `FlyDownW` (`3f`), `GentleDescent` (`4f`), dan `Glide` (`-1°`).
  - Memperbarui `FingerFoldSweepDeg` menjadi `{ 18f, -10f, -40f, -64f }` agar saat terlipat penuh (`GroundedIdle`, `TuckDive`) jari dalam (`Wing_Finger_Inner`) merapat sejajar sisi tubuh tanpa menembus punggung/ekor, serta memproyeksikan fleksi vertikal jari ke `FingerLateralFlapWeights` (sumbu depan) dan `FingerBackPitchWeights` (sumbu pitch).

### Verifikasi Unity 6 (`6000.3.7f1`)
- Rekompilasi bersih (`compilationFailed: false`, `consoleErrors: 0`).
- **Play Mode `MainMenu_Lobby.unity` (Calibration Live Preview):** Terverifikasi visual pada `capture_game_view` untuk `GLIDE (T-POSE)` (bentang sayap horizontal aerodinamis tanpa ujung depan terangkat), `TUCK TO DIVE` (lipatan streamline rapi sejajar sisi tanpa tumpukan di tulang punggung), dan `FLAP TO CLIMB`.
- **Play Mode `Test_Dragon_Flight.unity` (UDP `5052` Multi-State):** Terverifikasi `GroundedIdle` (`FrontY = +0.032`, `BackY = +0.014`), `Flapping`, `GentleDescent` (`ALT 106.9 m`), dan respons asimetris `cv_asym` (`FrontY` datar tanpa efek tenda depan) dengan `consoleErrors = 0`, `consoleWarnings = 0`, dan `isDirty = false`.

---

## 2026-11-15 — Penyelarasan Visual UI In-Game (`SCREEN 02`, `SCREEN 03`, `SCREEN 04`, & `PauseModal`)

### Eksekusi Utama
- **Penyesuaian `SCREEN 02` In-Game Flight HUD (`UISceneAndPrefabBuilder.cs`, `InGameFlightHUD.cs`, `PF_CameraControlModule_Compact.prefab`):**
  - Memindahkan `Btn_EscPause` (`148×48`) ke pojok kanan atas `(-60, -40)`, menambahkan soket medali gelap `UI_Socket_Medallion_38_Dark` (`38×38`) serta `UI_HUD_CheckDot_Green` ke `MissionCapsule`, dan memosisikan `WaypointCompass` di langit atas `(0, -108)` di bawah `MissionCapsule` agar tidak menutupi naga.
  - Merampingkan `GlobalVitalityBar` di kiri menjadi track tunggal `40×484` (`UI_HUD_Vitality_Track_40x480`) dengan pil mengambang mint-cream `82×41` (`#E0E9DC` `alpha = 1.0f`) di atasnya tanpa bingkai `SubParchment` besar.
  - Menyusun ulang sudut kanan bawah menjadi **klaster terpadu selebar `420 px`** (`right = -60, bottom = 40`): kapsul `TelemetryDock` (`420×44` dengan `ALT:` / `SPD:` ramping dan `Badge_PoseMode`), `PIP_CameraControlsCard` (`420×115`, `PF_CameraControlModule_Compact` memakai dropdown upward `36 px` + label tombol penuh `Latar Belakang` / `Jendela Preview`), serta `PIP_CameraPanel` (`420×240` `16:9` dengan `ViewfinderBrackets` margin `(12,14)` dan lencana status bawah-kiri `PipStatusBadge` yang tersinkron lewat `InGameFlightHUD.UpdatePipStatusBadge`).
- **Penyesuaian `SCREEN 03` (`VillageChallengeUI.cs`, `PF_VillageChallengeOverlay.prefab`):**
  - Membangun ulang kartu menjadi panel perkamen terpusat `720×670` (`y = -26` agar `CrestEmblem` tidak bertabrakan dengan `MissionCapsule`), `UI_Banner_Stepped_Modal_Blank`, bingkai ilustrasi gelap `400×219` (`UI_Illust_Dragon_WaterBreath.png` untuk `BiomeFire`), strip instruksi cekung, teks `% RESTORED` + progress bar horizontal, dan pil `STATUS: ACTION ACTIVE` di bawah.
- **Penyesuaian `SCREEN 04` & `PauseModal` (`FinishModalUI.cs`, `PF_FinishModal.prefab`, `PF_PauseModal.prefab`):**
  - Merombak `FinishModal` menjadi modal vertikal terpusat `680×776` (`y = -34`): `CrestEmblem`, banner bertinta gelap `#2D2926` (`STAGE COMPLETE` saat menang; coral `#9E2F26` jelas di `DefeatNoteText` saat kalah), 3 slot bintang `100×94` berlabel `STAR 01..03`, `StatsSubPanel` (`560×278` berisi 3 `StatRow` horizontal), bubble `+X STARS` (`rewardCalloutRoot`), tombol bertumpuk `NEXT LEVEL` + `REPLAY`, tombol kanan atas `ESC · LOBBY` (`160×46`), serta penanganan tombol keyboard `Escape` pada `FinishModalUI.Update()`.
  - Menambahkan `CrestEmblem`, scrim `0.58f`, dan subjudul lebih kontras (`#5C5145`) pada `PF_PauseModal.prefab`.

### Verifikasi Unity 6 (`6000.3.7f1`)
- Kompilasi & regenerasi `UISceneAndPrefabBuilder.BuildAll()` bersih (`compilationFailed: false`, `consoleErrors: 0`, `consoleWarnings: 0`).
- **Play Mode `Test_Dragon_Flight.unity` (`capture_game_view` `1920×1080` + `script_eval`):**
  - Startup non-regresi bersih: `Dragon_Player` (`DragonFlightController`, `DefaultFlightInputMapper`, `DragonWingAnimator`), `PythonProcessManager` (`pmCount=1`, `autoLaunchPython=False`, `IsProcessRunning=False`), `UDPPoseReceiver`, `InGameFlightHUD` aktif, serta `PauseModal`, `VillageChallenge`, `FinishModal`, dan `WaypointCompass` semuanya nonaktif.
  - Uji visual & fungsi: HUD `02:15 · VILLAGES 1/3`, `33%` vitality, `WaypointCompass` (`WILDFIRE · 139m`) jernih di langit atas, dropdown solid `#FBF6EB` di `PIP_CameraControlsCard` membuka ke atas (`ddIsOpen=True` / `ddIsOpen=False`), `VillageChallengeUI` (`VILLAGE 2: WILDFIRE WOODS`, `70% RESTORED`), `FinishModalUI` (`STAGE COMPLETE`, `01:44`, `3 / 3`, `+3 STARS`, serta `MISSION FAILED` `DefeatNoteText`), dan `PauseModalUI` (`Pause()` / `Resume()`) terverifikasi tanpa tumpang-tindih maupun terpotong (`consoleErrors = 0`, `consoleWarnings = 0`, `0` proses Python CV tertinggal pasca Play Mode).
- **Non-Regresi `MainMenu_Lobby.unity` Play Mode:** `CalibrationPanelUI` `1×4` defaults `[7,5,3,12]`, `PoseGuideOverlayUI` `Show()`/`Hide()`, serta `Settings` `SolidCameraDropdown` (`ddH = 44.0`) `Open()`/`Close()` berjalan bersih dengan `consoleErrors = 0` dan `consoleWarnings = 0`.

---

## 2026-11-15 — Revisi UI Kalibrasi (`SCREEN 01` 1×4 Dinamis MRU) & Modal Pose Guide (`SCREEN 07` · v1.6.1)

### Eksekusi Utama
- **Referensi Visual (`docs/UI_Designs_Reference/`):**
  - Memperbarui `01_Calibration_MotionTutorial.png` dan menambahkan `07_PoseGuide_OverlayModal.png` di luar folder `Assets/` (total `8` PNG referensi; tidak ada perubahan pada `134` PNG `Assets/UI/Components/` maupun `UISpriteSetupUtility.cs`).
- **Penyesuaian `SCREEN 01` (`CalibrationPanelUI.cs`, `LiveDragonPreviewController.cs`, `UISceneAndPrefabBuilder.cs`):**
  - Memperbesar proporsi panel atas Kalibrasi (`TopPanelsRow` `568 px` menuju rasio `860×540`) agar viewport mirror/skeleton dan **Live 3D Dragon Preview** (`RenderTexture` `840×438`, area preview `~446 px`) tampil lebih lega di `1920×1080`.
  - Menambahkan strip tengah `RECENT POSES · MAX 4` (`UI_Chip_Bar_Cream_9Slice` + `UI_Badge_Label_Amber_Blank`) dan tombol **`OPEN POSE GUIDE [15 POSES]`** (`UI_Btn_Secondary_Cream_9Slice`, tinggi `60 px`).
  - Mengganti grid `3×5` lama di panel Kalibrasi dengan baris **`1×4` Kartu Pose Dinamis (MRU Queue)**: default launch/reset `[#08 Glide, #06 Flap To Climb, #04 Takeoff, #13 Tuck To Dive]`, dwell debounce `poseSwitchDwellSeconds = 0.15f`, promosi/sisipan ke Slot 1 tanpa duplikat, `ResetRecentPosesToDefault()`, serta dukungan `SetVillageActionHighlight(bool)` (`#15`).
- **Penambahan `SCREEN 07` (`PoseGuideCatalog.cs`, `PoseGuideOverlayUI.cs`, `PF_PoseGuideOverlay.prefab`, `LobbyNavigationManager.cs`):**
  - Membuat katalog terpusat `PoseGuideCatalog.cs` untuk seluruh **15 pose (`#01..#15`)** lengkap dengan kategori, subtitle warna, deskripsi aksi, dan status `MAIN`.
  - Membangun `PF_PoseGuideOverlay.prefab` & `PoseGuideOverlayUI.cs` (`SetActive(false)` default di `MainMenu_Lobby.unity`): backdrop scrim `#1B1815` (`alpha 0.58f`), `ESC · CLOSE`, panel `1824×824` (`UI_Panel_Parchment_Full_9Slice`), `UI_Crest_Baroque_Scrollwork`, banner `UI_Banner_Stepped_Modal_Blank` bertinta gelap `#2D2926`, 4 pill kategori (`01-03 · GROUND`, `04-05 · LAUNCH & STEER`, `06-12 · CORE FLIGHT`, `13-15 · DIVE · DASH · VILLAGE`), grid `3×5` berisi seluruh 15 kartu pose (`MAIN` + sinkronisasi highlight `ACTIVE` live), strip `KEY POSE RULES`, serta tombol `CLOSE POSE GUIDE` (`64 px`).
  - Mengaktifkan `enableAutoSizing` (`11.5–14.5 pt` di modal, `12–16 pt` di slot MRU) sehingga judul panjang seperti `#12 FLY DOWN IDLE (POSE W)` tampil utuh tanpa elipsis.
  - Memperbarui `LobbyNavigationManager.HandleEscapeAction()` agar penekanan `Escape` memprioritaskan penutupan `PoseGuideOverlayUI` sebelum keluar dari tab `Calibration` ke `Missions`.

### Verifikasi Unity 6 (`6000.3.7f1`)
- `script_recompile_status` (`compilationFailed: false`, `errors: []`) & `UISceneAndPrefabBuilder.BuildAll()` selesai bersih (`0` Console Errors).
- **Play Mode `MainMenu_Lobby.unity` (`capture_game_view` `1920×1080` + `script_eval`):**
  - Default launch slot `1×4`: `[7, 5, 3, 12]` (`GLIDE`, `FLAP TO CLIMB`, `TAKEOFF`, `TUCK TO DIVE`), modal tertutup (`GuideOpen=False`).
  - Uji rotasi MRU: `SimulateDetectedPose(11)` → `[11, 7, 5, 3]`; `(5)` → `[5, 11, 7, 3]`; `(13)` → `[13, 5, 11, 7]`; repeat `(13)` idempoten `[13, 5, 11, 7]`; `SetVillageActionHighlight(true)` → `[14, 13, 5, 11]`; `ResetRecentPosesToDefault()` → `[7, 5, 3, 12]`.
  - Uji modal `SCREEN 07`: `Btn_OpenPoseGuide` membuka modal dengan 15 kartu pose terbaca utuh tanpa elipsis/tumpang-tindih, `SimulateDetectedPose(12)` menyinkronkan highlight modal ke `#13` (`HighlightedPoseIndex = 12`), tombol `CLOSE POSE GUIDE` & `ESC · CLOSE` menutup modal bersih, dan `HandleEscapeAction()` dua kali menghasilkan `(False, Calibration)` lalu `(False, Missions)` (`groundTruth.consoleErrors = 0`, `consoleWarnings = 0`).
- **Uji Non-Regresi `Test_Dragon_Flight.unity`:** Play Mode & Exit Play Mode bersih (`player=True`, `ctrl=True`, `mapper=True`, `wingAnim=True`, `pyCount=1`, `autoLaunch=False`, `pyRunning=False`, `udp=True`, `hud=True`, seluruh overlay dormant nonaktif, `groundTruth.consoleErrors = 0`, `consoleWarnings = 0`, dan `0` proses Python CV tertinggal).

---

## 2026-11-15 — Implementasi Sistem UI Hybrid (Screen 00–06) & Protokol Feature-to-UI Sync

### Eksekusi Utama
- **Ekstraksi & Setup Aset UI (`UI_Unity_Export`):**
  - Menempatkan `134` PNG komponen (20 subfolder) di `AR Dragon Sky Guardian/Assets/UI/Components/` dan `7` artboard referensi `1920×1080` di luar build runtime pada `docs/UI_Designs_Reference/`.
  - Membuat & menjalankan `Assets/Editor/UISpriteSetupUtility.cs` (`SetupAllSprites()`) untuk konfigurasi otomatis `134` sprite (`Single`, `PPU = 100`, `FullRect`, `Bilinear`, `Uncompressed`, `sRGB`, `alphaIsTransparency`) beserta **72 border 9-Slice / Horizontal 3-Slice** dengan urutan C# `Vector4(L, B, R, T)`.
  - Membuat & menjalankan `Assets/Editor/UIFontSetupUtility.cs` menghasilkan **10 aset TMP SDF** (`Fredoka-Bold`, `Fredoka-SemiBold`, `Nunito-ExtraBold`, `Nunito-Bold`, `Nunito-Medium` + masing-masing `DynamicFallback SDF.asset`) di `Assets/Fonts/`.
- **Arsitektur UI Hybrid, 14 Skrip UI, 6 Prefab, & 2 Scene Build Index:**
  - Membuat `Assets/Editor/UISceneAndPrefabBuilder.cs` (`BuildAll()`) serta **14 skrip C# UI** di `Assets/_Scripts/UI/` (`LobbyNavigationManager`, `MissionsPanelUI`, `CalibrationPanelUI`, `LiveDragonPreviewController`, `SettingsPanelUI`, `CameraControlModuleUI`, `SolidCameraDropdown`, `DragonGameSettings`, `UDPVideoStreamReceiver`, `UISkeletonOverlayGraphic`, `InGameFlightHUD`, `PauseModalUI`, `VillageChallengeUI`, `FinishModalUI`) dan **6 prefab** di `Assets/Prefabs/UI/` (`PF_CameraControlModule_Compact`, `PF_CameraControlModule_Full`, `PF_SettingsPanel`, `PF_PauseModal`, `PF_VillageChallengeOverlay`, `PF_FinishModal`).
  - Memperbarui skrip inti tanpa regresi uji terbang: `PythonProcessManager.cs` (`_instance == this` guard, `autoLaunchPython = false`, DirectShow list), `UDPPoseReceiver.cs` (`ReleasePortForSceneTransition()`), `DefaultFlightInputMapper.cs` (`turnSensitivity`), dan `DragonWingAnimator.cs` (`SetPreviewState`/`ClearPreviewState`).
  - Mendaftarkan `MainMenu_Lobby.unity` (Build Index `0`) dan `Test_Dragon_Flight.unity` (Build Index `1`):
    - **LIVE:** Lobby Top Bar & 3 Tab (`CALIBRATION`, `MISSIONS`, `SETTINGS`), Stage 01 `PLAY`, Kalibrasi (kamera 5-state, PIP `UDP 5053` + skeleton overlay, status `0..5` `UDP 5052`, `RECALIBRATE`/`RESET DEFAULT` `UDP 5054`, Live 3D Dragon Preview via `RenderTexture`, auto-highlight 14 pose `01..14`), Settings (`SolidCameraDropdown` 100% opaque `#FBF6EB`, `Turn Sensitivity` `1.0×–3.0×` persisten, `Master Volume`), dan `InGameFlightHUD` (Altitude, Airspeed, Mode Pose, PIP `5053`, `PauseModalUI`).
    - **PLACEHOLDER (`// TODO [SYNC-GAMEPLAY]` / `// TODO [SYNC-AUDIO]`):** Stage 02/03 (`SetStageProgress`), slider `BGM`/`SFX Volume` (`OnBgmVolumeChanged`/`OnSfxVolumeChanged`), HUD Timer/Villages/Vitality (`SetMissionTimer`/`SetVillageCount`/`SetGlobalVitality`), dan Pose 15 (`SetVillageActionHighlight`).
    - **DORMANT PREFAB (`SetActive(false)` default + `[ContextMenu]` preview):** `VillageChallengeUI` (`PF_VillageChallengeOverlay.prefab`), `FinishModalUI` (`PF_FinishModal.prefab`), dan Waypoint Compass (`SetWaypointTarget`).
- **Hasil Verifikasi Unity 6 (`6000.3.7f1`):**
  - Build & kompilasi bersih (`0` Console Errors; hanya 1 warning pra-eksisting di `QuickUDPTestBuilder.cs(16,40)`).
  - Uji Play Mode dari `MainMenu_Lobby` & langsung dari `Test_Dragon_Flight.unity` lolos dengan **0 Console Errors/Warnings**, dropdown kamera solid `#FBF6EB` (7 kamera `[0..6]`), `SelectCamera(6, "OBS Virtual Camera")` + `LaunchBackgroundMode()` (`Started=True`) + `StopPython()` (`Stopped=True`), persistensi `TurnSensitivity`, preview `[ContextMenu]` `SCREEN 02B/03/04`, dan zero lingering Python CV process saat keluar Play Mode.
- Detail lengkap matriks Live/Placeholder/Dormant & **Protokol `Feature-to-UI Sync`** dicatat di [[Implementasi UI Hybrid AR Dragon]].

---

## 2026-11-14 — Implementasi Dynamic Wings Movement Naga 3D (`Dragon_Model.fbx` Rigged & `DragonWingAnimator.cs`)
- **Aksi:**
  - Mengatasi masalah `Dragon_Model.fbx` sebelumnya yang hanya berisi mesh statis karena `ARM_Dragon` dalam kondisi *hidden* di `assets/blender/Dragon_Model.blend`: mengekspor ulang `AR Dragon Sky Guardian/Assets/Assets 3D/Dragon_Model.fbx` via Blender 5.1.2 dengan membuka visibilitas `ARM_Dragon` di memori (tanpa mengubah file `.blend`) sehingga 13 mesh `SM_Dragon_*` terimpor di Unity sebagai `SkinnedMeshRenderer` terhubung ke 47 tulang.
  - Memperbarui `/Dragon_Player/Visual_Dragon/Dragon_Model` di `Test_Dragon_Flight.unity` menjadi prefab instance rigged `Dragon_Model.fbx` (`localPosition = (0, -0.7937, 0.6985)`, `localScale = (1.25, 1.25, 1.25)`, `updateWhenOffscreen = true`).
  - Membuat `AR Dragon Sky Guardian/Assets/_Scripts/Controllers/DragonWingAnimator.cs` (`[DefaultExecutionOrder(50)]`, namespace `DragonWingAR.Controllers`) yang memetakan sumbu rotasi kanonik `Visual_Dragon` ke frame lokal masing-masing tulang sayap dan hanya menggerakkan **12 tulang sayap** (`Wing_Shoulder.L/R`, `Wing_Forearm.L/R`, serta 4 jari `Wing_Finger_Outer`, `Wing_Finger_MidOuter`, `Wing_Finger_MidInner`, `Wing_Finger_Inner` `.L/R`), sementara 35 tulang tubuh lainnya tetap pada rest pose.
  - Mengimplementasikan engine animasi *Hybrid* (`DragonFlightController` state & fisika + telemetri CV `IPoseProvider` / `UDPPoseReceiver` dengan peredaman eksponensial):
    - `GroundedIdle`: sayap terlipat simetris di kedua sisi tubuh (`ShoulderSweepBack = 31°`, `ForearmSweepBack = 9°`, `FingerFanFold = 0.62`) tanpa bertumpukan di atas ekor, disertai osilasi napas halus (`0.34 Hz`, `±2.4°`), dan perlahan membuka saat `GroundedWalk` atau lengan terbuka.
    - `Takeoff`, `Flapping`, `FlyUp`: ayunan kepakan asimetris dengan *phase lag* pada siku (`-0.72 rad`) dan cambuk jari bertingkat (`-1.35 rad`, skala jari luar ke dalam `1.15 -> 0.45`) serta *feathering* pitch.
    - `Glide` & `SwoopClimb`: bentangan aerodinamis responsif terhadap `Airspeed`, asimetri sayap dalam/luar saat `CurrentTurnInput` berbelok, serta modulasi tinggi & bukaan lengan CV kiri/kanan independen (`LeftUpperArmOpenness`, `RightUpperArmOpenness`, `LeftWrist`/`RightWrist`).
    - `Hover`, `TuckDive`, `PropellerDash`, `GentleDescent`, `FlyDownY`, dan `FlyDownW` sesuai gestur penerbangan.
  - Memasang `DragonWingAnimator` pada `/Dragon_Player` di `Test_Dragon_Flight.unity` dan memperbarui `DragonFlightTestSceneBuilder.cs`.
- **Verifikasi:**
  - Kompilasi Unity (`Assets/Refresh`) selesai bersih (`compilationFailed: false`, `consoleErrors: 0`, `consoleWarnings: 0` untuk seluruh skrip yang ditambah/diubah).
  - Pengujian Play Mode via paket UDP `127.0.0.1:5052` (43-token) dengan inspeksi visual `capture_game_view` dan pengukuran posisi bone `script_eval`:
    - `GroundedIdle`: sayap terlipat rapi di kiri-kanan punggung dengan ruang ekor bersih.
    - `FlyUp` / `Flapping`: upstroke mengangkat siku tinggi dengan jari melengkung alami; downstroke mengayun kuat dengan sebaran membran penuh tanpa robekan mesh.
    - `Glide + Turn (-0.90)`: sayap dalam kiri menekuk dan merapat halus sementara sayap luar kanan terangkat penuh.
    - `cv_asym` (`Arm Open L/R = 1.00 / 0.30`): sayap kiri terangkat lebih tinggi (`TipL.y = 31.68` vs `TipR.y = 31.31`, `ElbowL.y = 31.14` vs `ElbowR.y = 31.05`).
    - `Hover`, `GentleDescent`, dan `TuckDive` (`0.08x` slow-motion di udara pada ketinggian `98.8 m`) terverifikasi visual sesuai karakteristik masing-masing gestur; `scene` tersimpan bersih (`isDirty: false`) dan tidak ada proses Python CV tertinggal.

## 2026-11-14 — Dropdown Input Kamera & Dual Mode Aktivasi YOLO (`Latar Belakang` vs `Jendela Preview`)
- **Aksi:**
  - Menonaktifkan peluncuran otomatis Python CV saat Play/Startup (`autoLaunchPython = false` di `PythonProcessManager.cs`, `UDPPoseReceiver.cs`, dan `Test_Dragon_Flight.unity`), serta mereset seluruh flag pose/flap/dash di `UDPPoseReceiver.cs` saat tidak terhubung.
  - Menambahkan enumerasi kamera DirectShow cepat berbasis `ctypes` COM `ICreateDevEnum` (`enumerate_dshow_cameras()` & `resolve_camera_index()` di `camera_stream.py`, flag `--list-json` di `check_camera.py`) dan dukungan `--cam-name` serta deteksi penutupan jendela `[X]` di `active_cv_sender.py`.
  - Memperbarui `PythonProcessManager.cs` dengan `CameraLaunchMode` (`None`, `BackgroundInGameOnly`, `ExternalPreviewWindow`), `RefreshCameraListAsync()`, `SelectCamera()`, `LaunchBackgroundMode()`, `LaunchExternalPreviewMode()`, `PYTHONIOENCODING=utf-8`, serta guard race-condition `ReferenceEquals` pada lifecycle proses Python.
  - Merombak panel kamera kanan atas di `InGameFlightHUD.cs`: dropdown pemilih perangkat kamera (7 kamera terdeteksi dengan popup solid opaque `alpha = 1.0f` bebas tembus teks pada Linear Color Space), tombol `Scan`, tombol `Matikan` saat aktif, serta dua tombol aktivasi (`Latar Belakang` untuk feed PIP in-game saja vs `Jendela Preview` yang juga membuka jendela OpenCV eksternal).
- **Verifikasi:**
  - Kompilasi Unity (`Assets/Refresh`) selesai tanpa error maupun warning (`compilationFailed: false`, `consoleErrors: 0`).
  - Uji Play Mode pada `Test_Dragon_Flight.unity`: zero proses Python CV saat startup (`selectedCameraIndex = -1`, status `STANDBY`), dropdown menampilkan 7 kamera `[0..6]` dengan tombol `Scan` sejajar, mode `Latar Belakang` pada `[6] OBS Virtual Camera` mengalirkan live PIP in-game tanpa jendela eksternal, peralihan ke `Jendela Preview` membuka window OpenCV (`MainWindowHandle` non-zero) sambil tetap streaming PIP, dan tombol `Matikan` + keluar Play Mode menghentikan seluruh proses anak Python CV dengan bersih (`0` console error/warning, scene `isDirty: false`).

## 2026-11-21 — Inventarisasi Total Pose Kombinasi Tangan di Brain
- **Aksi:** Mengaudit dokumentasi kontrol terkini (`v6.0 Sky Peck` + `v6.1 On-Ground & Arm-Proximity Locomotion`) pada [[konteks]], [[On-Ground Mechanics dan Uji 16 Skenario]], dan [[Rework CV dan Kontrol Terbang Baru]] untuk merangkum total kombinasi pose tangan (**10 pose lokomosi/terbang → 13 state kontrol**, **2 pose khusus aksi desa**, dan **1 modifier kemudi *Arm Tilt***).
- **Catatan Memori:** [[Inventarisasi Pose Kombinasi Tangan]]

## 2026-11-14 — Penataan Struktur Folder Proyek (`assets/`, `docs/`, `tools/`)
- **Aksi:**
  - **Aset (`assets/`):**
    - Memindahkan seluruh file `.blend`/`.blend1` dari `Assets AR Dragon Sky Guardian/` ke `assets/blender/` (`Dragon_Model.blend`, `Nagcu.blend*`, `pohon*.blend*`, `pulau.blend`, `rumah*.blend*`) dan skrip Blender Python ke `assets/blender/scripts/` (`apply_textures*.py`, `inspect_slots.py`, `list_pohon.py`), lalu menghapus folder kosong `Assets AR Dragon Sky Guardian/`.
    - Meratakan struktur `vfx/vfx/*` ke `assets/vfx/` dan memindahkan `vfx.zip` ke `assets/archives/vfx.zip`.
    - Memindahkan 8 screenshot preview dari root project Unity (`AR Dragon Sky Guardian/*.png`) ke `assets/screenshots/`.
  - **Dokumen & Launcher (`docs/`, `tools/`):**
    - Memindahkan `Dokumentasi Projek.docx` dan `TS & List Asset.xlsx` ke `docs/`.
    - Memindahkan `jalankan_dragon.bat` dan `Run Hermes Desktop.bat` ke `tools/`.
  - **Kebersihan Root:** Menghapus file sementara `test_profile_tmp.json` di root, sementara `AR Dragon Sky Guardian/` dan `ARDragonSkyGuardian_CV/` tetap di root agar referensi path `PythonProcessManager.cs` dan impor modul Python CV tetap utuh.

## 2026-11-13 — Prioritas 4: Sinkronisasi `InGameFlightHUD` & Trigger Kalibrasi Unity -> Python CV (UDP Port 5054)
- **Catatan Memori Sesi:** [[Audit dan Penyempurnaan Kontrol Webcam]] (merangkum Baseplate rumput, audit 15 kendala, Prioritas 1–4, perbaikan mirror webcam/mode duduk/vertikal, & peninjauan status UI Laptop A).
- **Aksi:**
  - **Sinkronisasi HUD & Wireframe (`InGameFlightHUD.cs`):**
    - Menyelaraskan laju turun `Gentle Descent` (`-1.6 m/s` fallback + bacaan dinamis `flightController.CurrentVerticalVelocity`) dan `Glide` (`~ -1.0 m/s` + pitch live), menambahkan `[FLY DOWN Y]` / `[FLY DOWN W]` ke baris aksi HUD, memperlebar kartu HUD (`360 -> 415 px`) serta panduan bawah (`740 -> 880 px`), dan menambahkan prioritas `Propeller Dash`, `Fly Down Y`, & `Fly Down W` di `DrawSkeletonWireframe()`.
    - Menambahkan tombol UI kalibrasi langsung pada panel HUD (`MULAI KALIBRASI (C)`, `KALIBRASI ULANG (C)`, `RESET DEFAULT`, serta `BATALKAN KALIBRASI` + tombol `BATAL` pada banner atas).
  - **Channel Perintah Kalibrasi Unity <-> Python (`UDPPoseReceiver.cs`, `PythonProcessManager.cs`, `active_cv_sender.py`, `player_calibrator.py`):**
    - Menambahkan pengiriman UDP port `5054` pada `UDPPoseReceiver` (`SendCommandToPython()`, `TriggerCalibration()`, `CancelCalibration()`, `ResetCalibrationProfile()`, plus pintasan opsional tombol `C` melalui Input System) serta argumen `--cmd-port 5054` dari `PythonProcessManager.cs`.
    - Menambahkan listener UDP non-blocking `create_command_socket()` / `poll_calibration_commands()` pada `active_cv_sender.py` dan metode `PlayerCalibrator.reset_to_default()` pada `player_calibrator.py`, sekaligus memastikan profil personal yang sudah tersimpan (`is_calibrated=True`) langsung melaporkan `calib_state_id = 5` saat startup.
- **Verifikasi:** Unity `Assets/Refresh` compile bersih (`compilationFailed: false`, `consoleErrors: 0`, `Test_Dragon_Flight.unity` tersimpan), `verify_udp_calibration_trigger.py` (siklus `CALIBRATE` -> `CALIBRATE_CANCEL` -> `CALIBRATE_RESET` via soket UDP) serta seluruh 7 suite validasi Python CV (`test_phase2..6`, `test_additional_gestures` 10/10, `test_e2e_flight_physics` 6/6) lulus `TOTAL FAILED: 0`.

## 2026-11-13 — Penghalusan Kontrol Naik-Turun (`BodyPitch` Trim, Swoop Blending, & Camera Stabilization)
- **Temuan Akar Masalah:**
  - `Main Camera` merupakan child langsung `/Dragon_Player` yang menerima penuh rotasi `transform.rotation = (-_currentPitch, _currentYaw, _currentRoll)`, sehingga setiap perubahan sudut *pitch* (`-45°` ↔ `+24°` ↔ `-4°`) maupun *roll* menghentak garis horizon di viewport.
  - `maxFlapSpeed = 22 m/s` melewati ambang *Swoop Climb* (`cruiseSpeed + 2.0f = 16 m/s`), menyebabkan naga menyentak naik otomatis setelah berhenti mengepak lalu mendadak turun ke `-1.0 m/s` saat melambat.
  - Terdapat selisih daya angkat tajam `9x` antara `Flap` biasa (`flapHoldLift = 1.2 m/s`) dan `FlyUp` (`11.0 m/s` pada *lerp* `8.0f`), `BodyPitch` tidak terhubung ke kontrol trim terbang saat `Glide`/`Flap`/`GentleDescent`, *deadzone* `0.20` pada `body_pitch` melompat `0.0 -> 0.201` tanpa *rescaling* serta tanpa EMA, dan `FlapPower` meluruh ke `0` di antara tiap ayunan stroke.
- **Perbaikan:**
  - **Unity C#, Config & Scene (`DragonFlightController.cs`, `DragonLocomotionConfig.cs`, `DragonLocomotionConfig.asset`, `Test_Dragon_Flight.unity`):**
    - Memindahkan rotasi *pitch* & *roll* visual naga ke `Visual_Dragon` lewat `ApplyAttitudeRotation()`, menjaga root `Dragon_Player` hanya berotasi *yaw* agar horizon kamera datar (`roll = 0°`), dan meredam follow pitch kamera (`cameraPitchFollowFactor = 0.25`, `lerp dt * 3.5`) + offset vertikal halus (`clamp ±0.45 m`) di `UpdateCameraPolish()`.
    - Menghubungkan *Swoop Climb* secara ketat dengan kondisi paska-dive kecepatan tinggi (`_wasDivingHighSpeed` atau excess `> 5.5 m/s`) memakai transisi $C^1$ `Mathf.SmoothStep`, menyatukan rentang vertikal `Flap` (`flapHoldLift = 3.2`) dengan `FlyUp` (`flyUpLiftPower = 7.2`, `flyUpPitch = 19`), mengintegrasikan trim kontinu `_activeCommand.BodyPitch` pada `Flap`, `Glide`, `GentleDescent`, `Hover`, & `Dash`, serta menyeragamkan *damping* (`verticalVelocitySmoothSpeed = 3.8`, `pitchSmoothSpeed = 3.8`, `rollSmoothSpeed = 5.5`, `maxFlapSpeed = 19.5`).
  - **Python CV (`active_cv_sender.py`, `motion_recognizer.py`):**
    - Merescale *deadzone* `0.20` pada `head_signal` dan `chest_up_signal` dari `0.0` dan menambahkan EMA temporal `0.22` untuk `smoothed_body_pitch`.
    - Menambahkan amplop *asymmetric EMA* (`smoothed_power`) pada `FlapStrokeClassifier` agar gaya angkat kepakan terasa mengalir kontinu tanpa denyut antar-stroke.
- **Verifikasi:** Unity compile `0 errors` (`compilationFailed: false`, `consoleErrors: 0`), scene `Test_Dragon_Flight.unity` tersimpan, 7 suite Python CV (`test_phase2..6`, `test_additional_gestures` 10/10, `test_e2e_flight_physics` 6/6) lulus `TOTAL FAILED: 0`, dan pengecekan `verify_vertical_smoothness.py` lulus (`min=0.377, max=0.621, max_drop=0.136`, boundary `0.20->0.21` kontinu `0.0000->0.0125`).

## 2026-11-13 — Perbaikan Kemudi Belok Live Webcam (Mirror L/R Swap, Mode Duduk, & Responsivitas Unity)
- **Temuan Akar Masalah:**
  - `active_cv_sender.py` melakukan `cv2.flip(frame, 1)` sebelum inferensi YOLOv8n-pose namun tetap membaca `IDX_LEFT_*` sebagai `ls/lw` dan `IDX_RIGHT_*` sebagai `rs/rw`. Karena pose frontal COCO menempatkan anatomi `LEFT` pada sisi kanan layar (`x` lebih besar), *arm tilt* (`left_flap_height - right_flap_height`) **terbalik arah** dan saling meniadakan dengan *torso lean* (`0°` netral/melawan arah), `is_forearms_extended` gagal aktif, dan fallback posisi hilang menyeberang ke sisi berlawanan.
  - Saat duduk/dekat webcam laptop, pinggul (`lh`/`rh`) sering terpotong di bawah frame (`conf < 0.30` atau `y > 0.95`), sehingga `lean_angle_deg` dan `torso_length` kacau/terkunci.
  - Kurva kemudi default (`max_lean = 18°`, `pow(ratio, 1.35)`), *arm tilt* `/ 0.55`, ambang *arms away* (`0.52`), serta kecepatan belok Unity (`yawTurnSpeed = 65°/s`, `groundTurnSpeed = 65°/s`) terlalu berat saat dimainkan di kursi.
- **Perbaikan:**
  - **Python CV (`active_cv_sender.py`, `player_calibrator.py`, `motion_recognizer.py`, `cv_locomotion_config.json`):**
    - Menukar pemetaan mirror frontal COCO (`IDX_RIGHT_*` -> `ls/le/lw/lh`, `IDX_LEFT_*` -> `rs/re/rw/rh`) serta menambahkan sintesis pinggul virtual berbasis *shoulder roll* + offset lateral kepala saat pinggul terpotong/occluded (`c_lh/c_rh < 0.30` atau `y > 0.95`).
    - Mengkalibrasi ulang profil default (`max_lean = 13.5°`, `lean_deadzone >= 2.3°`, kurva `0.70*r + 0.30*r^2`, *arm tilt* hingga `14.5°` dengan pembagi `/ 0.42`), mempercepat rilis pasca-kepak (`0.45s -> 0.35s`), dan menurunkan *UpperArmGate* (`0.42 / 0.30`).
  - **Unity (`DragonFlightController.cs`, `DragonLocomotionConfig.cs`, `DragonLocomotionConfig.asset`, `UDPPoseReceiver.cs`, `Test_Dragon_Flight.unity`):**
    - Meningkatkan respons belok (`yawTurnSpeed = 95`, `groundTurnSpeed = 95`, `yawSmoothSpeed = 10`, `maxRollAngle = 40`), menurunkan `takeoffFlapThreshold = 0.28` (`0.20s`), menyelaraskan `entryAwayThreshold = 0.42` / `exitAwayThreshold = 0.30`, memperbaiki `tiltDelta = _smoothedRW.y - _smoothedLW.y` pada fallback `UDPPoseReceiver`, serta menyimpan scene `Test_Dragon_Flight.unity`.
- **Verifikasi:** Unity compile `0 errors` (`compilationFailed: false`, `consoleErrors: 0`), seluruh 7 suite Python CV (`test_phase2..6`, `test_additional_gestures` 10/10, `test_e2e_flight_physics` 6/6) lulus 100%, serta lolos verifikasi orientasi mirror YOLOv8n-pose & *seated steering*.

## 2026-11-13 — Implementasi Perbaikan Kontrol Prioritas 3 (Hover Wrist Band, Propeller Velocity/Scale, Core Conf & Test Suite CV)
- **Aksi:**
  - **Python CV (`motion_recognizer.py`, `pose_stabilizer.py`, `active_cv_sender.py`):**
    - **`HoverClassifier`:** Menambahkan validasi zona ketinggian pergelangan tangan (`avg_flap_height in [-1.00, 0.55]`) agar tangan yang menggantung rendah atau melambai di atas kepala tidak memicu Hover, serta rata-rata tekukan siku (`mean avg_elbow_angle <= 160°`, window 8 frame) untuk menolak lengan lurus yang berayun tanpa memblokir titik nadir kepakan lengan bawah.
    - **`PropellerRotationClassifier`:** Menggunakan rata-rata berjalan `shoulder_width` (`sw_history`) untuk menormalkan radius orbit pergelangan tangan (`MIN_ORBIT_RADIUS_SW = 0.25`), menghitung kecepatan sudut (`est_omega = max(omega_l, omega_r)`) berbasis `dt` aktual frame, serta menegakkan `MIN_ANGULAR_VELOCITY = 4.5 rad/s` agar gerak memutar lambat maupun jitter mikro di bahu tidak memicu Dash.
    - **`RobustKeypointTracker` & `active_cv_sender.py`:** Menambahkan `CORE_CONFIDENCE_MIN = 0.25`, `compute_core_confidence()`, dan `is_core_confident()` terpisah dari keypoint pergelangan tangan serta mengganti batas `core_conf >= 0.20` di `active_cv_sender.py` menjadi `tracker.is_core_confident(confidences)` (`>= 0.25`) sehingga batas `LOST`/`DEGRADED` dan `GestureStateMachine` selalu sinkron. Sekaligus memperbaiki *typo* gambar garis bahu kiri (`ls[1]` menggantikan `rs[1]`) pada debug overlay `active_cv_sender.py:159`.
    - **`GentleDescentClassifier`:** Mensyaratkan `is_forearms_extended` (`Tucked T-Pose`) & `upper_arm_proximity <= threshold` alih-alih menerima pose tangan menggantung lemas di sisi badan (`Scenario 3`), sehingga pose diam santai di depan webcam tetap berstatus `NEUTRAL_GLIDE` sementara `is_arms_tucked = True` tetap dikirim ke Unity `DefaultFlightInputMapper`.
  - **Suite Tes CV (`test_e2e_flight_physics.py`):** Memperbarui `Test 5` dari kontrak lama 39 token ke protokol **43 token CSV** (`token 34..42`).
  - **Verifikasi:** Seluruh suite CV lulus 100% tanpa regresi — `test_additional_gestures.py` (10/10), `test_e2e_flight_physics.py` (6/6), `test_phase2_validation.py` (6/6), `test_phase3_validation.py` (7/7), `test_phase4_validation.py` (7/7), `test_phase5_validation.py` (7/7), `test_phase6_final_session.py` (7/7), `verify_priority2.py`, dan `verify_priority3.py`.

## 2026-11-13 — Implementasi Perbaikan Kontrol Prioritas 2 (Arm Tilt Steering, Double Curve, UpperArm Gate 43-Token & Wiring Scene)
- **Aksi:**
  - **Python CV (`player_calibrator.py`, `motion_recognizer.py`, `udp_sender.py`, `active_cv_sender.py`, `test_send_dummy_udp.py`):**
    - Mengaktifkan kontribusi `arm_tilt_norm` di `AdaptiveThresholdEngine.compute_adaptive_steering()` (dengan zona mati lengan `0.12` dan kontribusi tambahan hingga `12°` tanpa memangkas bobot `1.0x` pada *torso lean*) serta menghitung `arm_tilt_norm = clamp((left_flap_height - right_flap_height) / 0.55, -1, 1)` di `GestureStateMachine.update()`.
    - Memperluas protokol UDP 5052 dari 41 ke **43 token CSV** (`41`: `is_arms_away`, `42`: `upper_arm_openness`), mengirimkan hasil `UpperArmLocomotionGate` yang sudah tervalidasi kepercayaan keypoint dari Python (serta memperbarui `test_send_dummy_udp.py`).
  - **Unity C# & Scene (`IPoseProvider.cs`, `UDPPoseReceiver.cs`, `DefaultFlightInputMapper.cs`, `Test_Dragon_Flight.unity`):**
    - Menambahkan `HasUpperArmGate`, `IsArmsAway`, dan `UpperArmOpenness` ke `IPoseProvider` dan mem-parsing token `41..42` pada `UDPPoseReceiver` (aktif saat `tokens.Length >= 43` dan pose terkoneksi/terdeteksi, tetap kompatibel mundur untuk stream lama).
    - Menghilangkan *double steering curve* & *double deadzone* di `DefaultFlightInputMapper.cs` (`turnDeadzone = 0.0f`, `steeringEasePower = 1.0f`, skip `Mathf.Pow` saat `steeringEasePower == 1f`) serta memprioritaskan `HasUpperArmGate` Python dibanding kalkulasi ulang lokal dari joint yang sudah ter-lerp Unity.
    - Memasang komponen `DefaultFlightInputMapper` langsung ke `/Dragon_Player` di `Test_Dragon_Flight.unity`, menghubungkan `poseProviderSource -> UDPPoseReceiver`, `locomotionConfig -> Assets/Settings/DragonLocomotionConfig.asset`, `DragonFlightController.flightInputSource -> DefaultFlightInputMapper`, mengatur `UDPPoseReceiver.smoothingSpeed = 22`, dan menyimpan scene.
  - **Verifikasi:** Kompilasi Unity 6 URP `0 errors` (`compilationFailed: false`), suite validasi Python CV Phase 2 (6/6), Phase 4 (7/7), Phase 5 (7/7), serta verifikasi unit `verify_priority2.py` (*arm tilt*, *GSM combine + upper-arm gate exit*, dan UDP 43-token roundtrip) seluruhnya lulus.

## 2026-11-13 — Implementasi Perbaikan Kontrol Prioritas 1 (Takeoff, Landing, FlyUp Threshold, Config CV & Grounded Look Up)
- **Aksi:**
  - **Unity (`DragonFlightController.cs` & `Test_Dragon_Flight.unity`):**
    - Menghapus *hard-snap* `safePos.y = landedSurfaceY + minGroundClearance` (`3.5 m`) yang menyebabkan naga teleport `+2.65 m` pada frame pertama `Takeoff`, serta menjaga agar `groundAvoidance` tidak mendistorsi kurva naiknya `_isTakingOff`.
    - Mencegah `FlyUp` membatalkan `_isTakingOff` sebelum naga mencapai ketinggian target (`_takeoffTargetY - 0.35f`).
    - Mengizinkan `TuckDive` yang mencapai permukaan tanah untuk memicu transisi mendarat (`TriggerLanding()`) alih-alih tertahan meluncur di atas tanah.
    - Memisahkan `Grounded Look Up` dari `PitchInput` (tangan terangkat) sehingga di darat naga hanya mendongak berdasarkan `BodyPitch`.
    - Menonaktifkan `enableKeyboardFallback` pada `UDPPoseReceiver` di `/Dragon_Player` (`Test_Dragon_Flight.unity`) sesuai keputusan fokus murni kontrol webcam/CV.
  - **Python CV (`player_calibrator.py`, `motion_recognizer.py`, `cv_locomotion_config.json`, `active_cv_sender.py`):**
    - Mengubah `get_default_profile()` menjadi `is_calibrated=False` dan memperbaiki rumus adaptif `FlyUpClassifier` saat terkalibrasi (`min(1.80, max(1.68, neutral_ratio + 0.08))`) sehingga ambang `Fly Up` kembali responsif di `1.74` (default) dan `<= 1.80` (calibrated), bukan `1.9075`.
    - Menghubungkan pembacaan `_CV_CONFIG` (`cv_locomotion_config.json`) ke `TuckDiveClassifier`, `FlyUpClassifier`, `UpperArmLocomotionGate`, `GentleDescentClassifier`, dan `FlyDownClassifier`.
    - Memperkaya sinyal `body_pitch` di `active_cv_sender.py` dengan menggabungkan *head tilt* (`head_signal`) dan busung dada (`chest_elevation`) tanpa terkontaminasi posisi pergelangan tangan.
  - **Verifikasi:** Kompilasi Unity 6 URP `0 errors` (`compilationFailed: false`) dan suite validasi Python CV Phase 2, 4, 5 serta unit test `FlyUpClassifier` lulus 100%.

## 2026-11-13 — Ganti Pulau dengan Grass Baseplate & Audit Kendala Mekanisme Kontrol
- **Aksi:**
  - Menghapus `/World_Island` dari hierarki `Test_Dragon_Flight.unity` dan menggantinya dengan `/Grass_Baseplate` (`Cube` ukuran `2000 x 2 x 2000` di `(0, -1, 0)` dengan material `Assets/Materials/Mat_Grass_Baseplate.mat`, shader `Universal Render Pipeline/Lit`), serta memindahkan `/Dragon_Player` ke `(0, 0.85, 0)` dan menyimpan scene.
  - Melakukan audit penuh terhadap seluruh mekanisme kontrol (Python CV, protokol UDP 41-float, `UDPPoseReceiver`, `DefaultFlightInputMapper`, `DragonFlightController`, `InGameFlightHUD`, dan suite tes CV).
  - Menemukan 15 kendala utama (termasuk bug takeoff teleport `+2.65 m`, `FlyUp` threshold default naik ke `1.9075`, dead config `cv_locomotion_config.json`, double smoothing/steering `^1.8225`, split `UpperArmLocomotionGate`, `DefaultFlightInputMapper` belum terpasang di scene, celah keyboard fallback `A`/`Dash`/`FlyDownY/W`/harus menahan `T`, serta kegagalan pada `test_additional_gestures.py` Skenario 3 dan `test_e2e_flight_physics.py` Test 5).

## 2026-11-13 — Penyederhanaan Nama File Memori (Laptop B)
- **Aksi:** Menyederhanakan seluruh nama file di `brain/laptop-b/memories/` menjadi judul ringkas (3–6 kata) sesuai standar baru di [[00 - Overview]] serta memperbarui seluruh `[[wikilinks]]` di `Devlog.md` dan `Tasks.md`.

## 2026-11-13 — Upgrade On-Ground Mechanics, Arm-Proximity Locomotion Gate, Takeoff, Fly Up/Down & Verifikasi 16 Skenario
- **Aksi:**
  - Menyelesaikan pembaruan dokumen perencanaan (`prd.md` & `konteks.md`) serta implementasi penuh **Fase A–D** pada Python CV (`pose_stabilizer.py`, `motion_recognizer.py`, `udp_sender.py`, `active_cv_sender.py`, `cv_locomotion_config.json`) dan Unity (`DragonFlightController.cs`, `DefaultFlightInputMapper.cs`, `UDPPoseReceiver.cs`, `IFlightInputProvider.cs`, `IPoseProvider.cs`, `DragonLocomotionConfig.cs`, `InGameFlightHUD.cs`).
  - Memverifikasi seluruh **16 skenario pengujian** di Unity Play Mode via UDP `5052` dengan `compilationFailed: false` dan `consoleErrors: 0`.
  - Catatan lengkap: [[On-Ground Mechanics dan Uji 16 Skenario]].

## 2026-11-13 — Setup OmniRoute, Migrasi Endpoint Hermes, & Launcher `Run Hermes Desktop.bat`
- **Aksi:**
  - Menyusun prompt autonomous setup OmniRoute untuk Hermes Agent, panduan mematikan server 9Router (port `20128`), solusi error `better-sqlite3` (`NODE_MODULE_VERSION 127` vs `137`) pada OmniRoute `v3.8.48`, serta command migrasi endpoint Hermes ke `http://localhost:20128/v1`.
  - Membuat file `Run Hermes Desktop.bat` di root project untuk menjalankan OmniRoute (jika port `20128` belum aktif) dan meluncurkan Hermes Desktop secara otomatis.
  - Catatan lengkap: [[Setup OmniRoute dan Launcher Hermes Desktop]].

## 2026-11-13 — Mekanik Sky Peck, Auto-Launch Python Process Manager, & Update PRD/Konteks v6.0
- **Catatan Sesi Lengkap:** [[Mekanik Sky Peck dan Auto-Launch Python]]
- **Ringkasan Eksekusi:**
  - Menerapkan mekanik penerbangan ala **Sky Peck** di `DragonFlightController.cs`, `UDPPoseReceiver.cs`, `InGameFlightHUD.cs`, dan `active_cv_sender.py` (*Flap to Climb* decoupled tanpa *pitch nodding*, *Lean to Bank/Turn* 50% arm tilt + 50% torso lean, *Tuck to Dive* hingga $28\text{ m/s}$, *Swoop Climb*, *Glide*, serta *Propeller Dash* hingga $26\text{ m/s}$).
  - Menambahkan `PythonProcessManager.cs` pada `Dragon_Player` di `Test_Dragon_Flight.unity` agar `active_cv_sender.py` otomatis berjalan saat Unity Play Mode dimulai dan otomatis berhenti bersih (`taskkill /F /T`) saat keluar Play Mode.
  - Menambahkan arsitektur input modular (`IFlightInputProvider.cs`, `DefaultFlightInputMapper.cs`) dan memperluas protokol UDP 5052 hingga 39-float.
  - Memverifikasi kompilasi bebas error (`0 errors`) dan pengujian live Unity Play Mode (termasuk PIP webcam stream port 5053 dan terminasi bersih PID Python).
  - Memutakhirkan `konteks.md` & `prd.md` ke **v6.0** dan push ke repo project (`origin/master` commit `6f03436`, `2b4833b`, `62c7e90`).

## 2026-11-13 — Analisis Aset `rumah.blend` & Prompt Tekstur Hermes Desktop (Laptop B)
- **Ringkasan:** [[Analisis dan Tekstur Rumah Blend]]
- **Aksi:**
  - Menganalisis struktur proyek dan menginspeksi `rumah.blend`, `Nagcu.blend`, `pulau.blend`, dan `pohon.blend` via Blender 5.1 CLI.
  - Menyusun prompt eksekusi untuk **Hermes Desktop** pada path `Assets AR Dragon Sky Guardian` dengan gaya *stylized low-poly matte* ala `Nagcu.blend`, menggunakan palet warna rumah alami (terakota `#B84A39`, plester krem `#E8DFCE`, kayu `#5C381E`, batu `#54575E`).

## 2026-11-13 — Rework Computer Vision Motion Control & Tambahan Kontrol Terbang (Fly Up, Hover, Gentle Descent)
- **Catatan Memori:** [[Rework CV dan Kontrol Terbang Baru]]
- **Aksi & Hasil:**
  - Mengimplementasikan deteksi & fisika **Fly Up** (chest raised + flapping), **Hover Mode** (upper arms tucked + forearm flapping), dan **Gentle Descent** (tucked T-pose tanpa flapping) dengan strict priority arbitration di `motion_recognizer.py` dan `DragonFlightController.cs`.
  - Memperluas protokol UDP CSV ke 39 token (`34..38`), memperbarui `IPoseProvider.cs`, `UDPPoseReceiver.cs` (termasuk keyboard fallback `H`, `G`, `U`), `IFlightInputProvider.cs`, `DefaultFlightInputMapper.cs`, dan `InGameFlightHUD.cs`.
  - Validasi lulus 100%: `test_e2e_flight_physics.py` (6/6), `test_additional_gestures.py` (10/10), `test_phase5_validation.py` (7/7), `test_phase6_final_session.py` (7/7), dan kompilasi Unity 6 URP 0 error.

## 2026-11-13 — Setup Obsidian Brain & Migrasi Catatan Lokal (Laptop B)
- **Aksi:**
  - Menambahkan `brain/` ke `.gitignore` root project dan meng-clone `ARDragon-Brain.git` ke `brain/`.
  - Memindahkan seluruh file `.md` lokal dari root project (`GDD_Pulau.md`, `konteks.md`, `prd.md`, `progress.md`) ke `brain/laptop-b/`.
  - Membuat `AGENTS.md` di root project untuk aturan kolaborasi dual-laptop.

## 2026-11-13 — Inisialisasi Folder Brain Laptop B
- Folder `brain/laptop-b/` dibuat untuk memisahkan log eksekusi kode & implementasi teknis dari catatan desain di `brain/laptop-a/`.
- Baseline yang sudah terverifikasi sebelum sinkronisasi vault:
  - Python CV Dual-Port UDP (`5052` pose CSV, `5053` JPEG PIP) aktif.
  - Sistem terbang 3D Pose Y-T-A dan model naga `Dragon_Model.blend` aktif di Unity 6 (`6000.3.7f1`).
  - Tugas berikutnya menanti eksekusi **Sesi GUI** berdasarkan [[laptop-a/rencana_desain_ui]].
