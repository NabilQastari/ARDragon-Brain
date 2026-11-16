---
title: Laptop B — Devlog (Eksekusi & Pengembangan Program)
tags:
  - devlog
  - laptop-b
owner: Laptop B
updated: 2026-11-16
---

# 🛠️ Devlog — Laptop B

> Catatan kronologis pengembangan kode, debugging, pengujian Unity/Python, dan hasil build dari **Laptop B**. Kembali ke [[00 - Overview]].

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
