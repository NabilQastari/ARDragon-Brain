---
title: "Penyelarasan 17 Pose dan Propeller Dash"
type: session-memory
tags:
  - session-memory
  - laptop-b
  - computer-vision
  - head-pitch
  - propeller-dash
  - pose-alignment
  - unity-flight
date: 2026-11-21
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[laptop-a/panduan_detail_pose_dan_sync_laptop_b]]"
  - "[[laptop-a/planning_revisi_titik_wajah_dan_pov]]"
  - "[[laptop-a/planning_audit_pose_dan_clean_cv_preview]]"
  - "[[laptop-a/checklist_finalisasi_pra_sesi_4]]"
  - "[[laptop-b/Tasks]]"
  - "[[laptop-b/Devlog]]"
---

# Penyelarasan 17 Pose dan Propeller Dash

## 1. Tujuan & Konteks Sesi
- Menyelaraskan **17 pose kontrol** (`#01..#14, #15A, #15B, #15C`) di Python CV, controller Unity, serta UI Calibration & Pose Guide (`SCREEN 01` & `SCREEN 07`) agar sesuai dengan panduan visual di `assets/poses_cv_vector/` dan `[[laptop-a/panduan_detail_pose_dan_sync_laptop_b]]`, sambil menjaga **nol regresi** pada 6 pose terkunci (`#01`, `#02`, `#04`, `#05`, `#08`, `#13`).
- Menyelesaikan **Pilar 1 Pra-Sesi 4**: pembersihan visual preview CV (`FPS` + skeleton + 3 titik wajah tanpa kotak HUD/label) pada UDP port `5053`, serta implementasi **Tahap 2 `HEAD_PITCH` murni** pada sisi Unity menggunakan tulang leher/kepala Fanamir.
- Menyempurnakan **`#14 Propeller Dash`** secara menyeluruh pada deteksi Python CV (orbit pergelangan mengelilingi siku saat lengan atas terentang `180°`), fisika dorongan & kamera Unity, serta animasi sayap heliks.

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Proteksi 6 Pose Terkunci & Penyelarasan Classifier CV (`motion_recognizer.py`, `pose_stabilizer.py`, `mouth_tracker.py`):**
   - Mempertahankan penuh ambang `UpperArmLocomotionGate` (`0.42 / 0.30`), `ContinuousLeanClassifier` / `AdaptiveThresholdEngine`, serta `TuckDiveClassifier` (`1.25 / 1.30`, `0.12s`).
   - Memisahkan **`#07 Fly Up` (Open Y-Pose `>= 1.55` lebar + `HEAD_PITCH`)** secara tegas dari **`#15C Solaria Dew Rain` (Double I-Pose `11` sempit `0.45..1.40`)**; menyelaraskan **`#09 Hover`** ke L-Pose `90°` statis/flutter, **`#10 Gentle Descent`** ke Low-A Pose (`~24°..66°` bawah di `pose_stabilizer.py`), **`#11 Y-Dive` & `#12 W-Pose`** pada `FlyDownClassifier`, **`#06` / `#15A`** pada `FlapStrokeClassifier`, serta ambang `#03`/`#15B` di `MouthTracker` (`PITCH_DEADZONE_DEG = 1.6`, `MOUTH_OPEN` `ON=0.35 / OFF=0.25`).
   - Menghapus ketergantungan fallback `isUpperArmsTucked` pada `isGentle` di `DefaultFlightInputMapper.cs` ketika `HasUpperArmGate` aktif agar `#01 Idle` tidak tertukar dengan `#10 Gentle Descent`.
2. **Tahap 2 `HEAD_PITCH` Murni & Rig Kepala (`DragonHeadLookController.cs`, `DragonFlightController.cs`):**
   - Memakai komponen terdedikasi `DragonHeadLookController` (`Neck_01` `25%`, `Neck_02` `35%`, `Head` `40%`, batas dongak `+28°` / tunduk `-22°`) yang digerakkan murni oleh `FlightCommand.HeadPitch` (`tokens[43]`) tanpa memengaruhi `TurnInput`, `yaw`, maupun `roll`.
   - Menghubungkan ke `#03 Grounded Look Up` (membatasi pitch badan `<= 5°` saat rig leher aktif), *camera pitch follow*, serta climb/descent udara (`Fly Up` saat `> 0.12f` dan `Pitch Down` saat `< -0.12f`) tanpa membaca `chest_elevation`.
3. **Clean CV Preview (`active_cv_sender.py`):**
   - Menghapus seluruh kotak HUD hitam, bar metrik, status pose, dan teks label wajah (`NOSE`, `CHIN`, `LIPS`, `MOUTH`) di `draw_debug_overlay()` sehingga frame PIP UDP `5053` hanya menampilkan skeleton, keypoints COCO, 3 titik wajah, dan `FPS: xx` di pojok kiri atas; paket 44-float UDP `5052` tetap utuh.
4. **Overhaul `#14 Propeller Dash` (`PropellerRotationClassifier`, `DragonFlightController.cs`, `DragonWingAnimator.cs`):**
   - **CV:** Mengukur orbit terhadap masing-masing siku dengan parameter di `cv_locomotion_config.json` (`propeller_dash`), menambahkan pelindung singularitas foreshortening 2D (`radius_norm < 0.13`), discard cabang `|dtheta| > 2.2`, debounce balik arah `2` frame, hysteresis `upper_arms_spread`, larangan akumulasi saat siku rapat (*Hover*), `reset()` saat tracking hilang, serta *grace hold* eksponensial (`math.exp(-1.5 * dt)`, dijaga `>= 0.46` hanya selama `est_omega >= 1.15`).
   - **Unity:** Menambahkan impuls burst awal (`dashBurstImpulse = 6.5f`, cooldown `0.55f`), kecepatan dash hingga `maxDashSpeed = 30f` (`dashThrustAccel = 28f`), *Ground Dash Sprint* (`walkSpeed * 2.2f` tanpa takeoff palsu), camera surge (`+5°` FOV & pullback `localPosition.z` `-0.55 m` lewat `_dashSurgeVisual01`), dan gerak sayap orbit heliks kuadratur (`sin`/`cos`) di `DragonWingAnimator.cs`.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Python CV (`ARDragonSkyGuardian_CV/`):**
  - `pose_stabilizer.py`, `mouth_tracker.py`, `cv_locomotion_config.json`, `motion_recognizer.py`, `active_cv_sender.py`.
  - Suite uji diperbarui & ditambahkan: `test_additional_gestures.py`, `test_e2e_flight_physics.py`, dan `test_all_17_poses_alignment.py`.
  - **Hasil Verifikasi Python CV (`ARDragonSkyGuardian_CV\venv\Scripts\python.exe`):** `9/9` test suite lulus 100% (`test_tahap1_face_tracking.py`, `test_phase1_validation.py`, `test_phase2_validation.py`, `test_phase4_validation.py`, `test_phase5_validation.py`, `test_phase6_final_session.py`, `test_additional_gestures.py`, `test_e2e_flight_physics.py`, `test_all_17_poses_alignment.py`), plus uji visual output JPEG UDP `5053` (`4892` bytes, `320×180`, bersih hanya `FPS: 30` + skeleton + 3 titik wajah).
- **Unity C#, Editor & Scene (`AR Dragon Sky Guardian/`):**
  - **Controller & CV Receiver:** `DragonHeadLookController.cs` *(Baru)*, `DragonFlightController.cs`, `DragonWingAnimator.cs`, `UDPPoseReceiver.cs`, `DefaultFlightInputMapper.cs`, `IFlightInputProvider.cs`, `IPoseProvider.cs`.
  - **UI & Scene Builders:** `LiveDragonPreviewController.cs`, `CalibrationPanelUI.cs`, `PoseGuideCatalog.cs`, `PoseGuideOverlayUI.cs`, `UISpriteSetupUtility.cs`, `UISceneAndPrefabBuilder.cs`, `DragonFlightTestSceneBuilder.cs`.
  - **Scene Tersimpan & Terverifikasi:** `Assets/_Scenes/MainIsland_Gameplay.unity`, `Assets/_Scenes/Test_Dragon_Flight.unity`, dan `Assets/_Scenes/MainMenu_Lobby.unity` (`0` compile/runtime errors & `0` warnings).
  - **Hasil Verifikasi Play Mode Unity:**
    - Skenario `HEAD_PITCH` (`MainIsland_Gameplay.unity` via UDP `5052`): `#03 Grounded Look Up` (`headDeg=15.67°`, posisi & `yaw=345.00°` diam), Airborne `Fly Up` (`vy=4.87 m/s`, `headDeg=20.55°`, `turn=0.000`), `Pitch Down` (`vy=-3.86 m/s`, `headDeg=-16.15°`, `turn=0.000`), serta kombinasi `Fly Up` + `Lean Steering` (`turn=0.650`, `yaw=338.89°`). Non-regresi lulus di `Test_Dragon_Flight.unity` & `MainMenu_Lobby.unity` (`headDeg=20.55°`).
    - Skenario `#14 Propeller Dash` (`MainIsland_Gameplay.unity`): Grounded Dash (`grounded=True`, `state=Propeller Dash`, `speed=7.89 m/s`), Airborne Dash (`speed=30.00 m/s`, `fov=73.99°`, `camZ=-6.83`, `vy=1.37 m/s`, `headDeg=7.15°`, `turn=0.45`, `wingRegime=PropellerDash`), rilis halus ke `Glide` (`13.65 m/s`), serta live preview `MainMenu_Lobby.unity` (`wingRegime=PropellerDash`).
- **Sinkronisasi Obsidian Brain:** Commit `44bec33`, `8f6c563`, dan `de3d095` ke `origin main` pada `brain/laptop-b/`.

---

## 4. Detail Penting yang Harus Diingat
- **Nilai Terserialisasi Scene vs Default Skrip:** Mengubah default field publik C# (mis. `maxDashSpeed = 30f`, `dashThrustAccel = 28f`) tidak otomatis menimpa nilai yang sudah terserialisasi di `.unity` scene pada `/Dragon_Player` — harus diperbarui eksplisit lewat `SerializedObject` + `EditorSceneManager.SaveScene` atau builder.
- **Konfigurasi `#14 Propeller Dash` (`cv_locomotion_config.json`):** `min_rotation_accum_rad: 2.75`, `min_angular_velocity: 2.2`, `min_side_angular_velocity: 0.8`, `min_orbit_radius_sw: 0.20`, `min_angle_radius_sw: 0.13`, `spread_horiz_enter/exit: 0.28 / 0.21`, `spread_vert_enter/exit: 0.62 / 0.78`, `grace_hold_sec: 0.28`.
- **Rig Kepala `Dragon_Model.fbx`:** Tulang `Neck_01`, `Neck_02`, `Head` berada di bawah `ARM_Dragon/Root/Pelvis/Spine_01/Spine_02`; `DragonHeadLookController` otomatis menginisialisasi saat runtime maupun di prefab preview `LiveDragonPreviewRig`.
- **Virtual Environment & Testing Windows:** Gunakan `ARDragonSkyGuardian_CV\venv\Scripts\python.exe` (bukan Python sistem), hindari membungkus pipeline PowerShell dengan `rtk`, dan perhatikan resolusi `time.sleep` Windows (`~15.6 ms` tick) pada skrip uji CV.
- **Catatan Aset Lanjutan:** Pada sesi selanjutnya (tercatat di `[[laptop-b/Devlog]]`), paket delta `Export_Delta_LaptopB/` memutakhirkan medali bersih `UI_Pose_01..15*.png` serta menerapkan sistem Momentum `v4.0`, Sesi 4 desa, dan Sesi 4B VFX/FOV/Skybox.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] **Sesi 5 & Feature-to-UI Sync (`GameManager` & `AudioManager`):** Implementasi pengontrol level 1–3, timer/scoring bintang resmi, integrasi audio BGM/SFX dari `DragonGameSettings`, penghapusan penanda `// TODO [SYNC-GAMEPLAY]` & `// TODO [SYNC-AUDIO]`, serta build Windows standalone + `start_game.bat` sesuai daftar aktif di `[[laptop-b/Tasks]]`.

---

**Navigasi Obsidian:** [[00 - Overview]] · [[laptop-b/konteks|konteks]] · [[laptop-b/prd|prd]] · [[laptop-b/GDD_Pulau|GDD_Pulau]] · [[laptop-b/Tasks|Tasks]] · [[laptop-b/Devlog|Devlog]]
