---
title: Memori Sesi — Kamera Dinamis, Navigasi Pause, & Pelacakan 3 Titik Wajah (Tahap 1)
type: session-memory
tags:
  - session-memory
  - laptop-b
  - camera
  - face-tracking
  - ui-ux
date: 2026-11-21
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[laptop-a/planning_kamera_dinamis_dan_navigasi_pause]]"
  - "[[laptop-a/planning_revisi_titik_wajah_dan_pov]]"
  - "[[Implementasi UI Hybrid AR Dragon]]"
---

# 🧠 Memori Sesi — Kamera Dinamis & Pelacakan 3 Titik Wajah

> Arsip ringkas sesi pengerjaan **Laptop B** untuk **Patch 1 Fokus 1 (Kamera Dinamis & Navigasi Pause)**, **Revisi Titik Wajah Tahap 1 (Hidung, Dagu, Mulut)**, serta **perbaikan 3 temuan uji kamera** pada *Fanamir: The Sky Guardian*.

---

## 1. Tujuan & Konteks Sesi
1. **Fokus 1 (`[[laptop-a/planning_kamera_dinamis_dan_navigasi_pause]]`):**
   - Mendukung rasio aspek kamera dinamis (`16:9`, `4:3`, dan vertikal `9:16` / `3:4`) baik di Python CV maupun preview UI Unity tanpa distorsi.
   - Merapikan HUD in-game agar dropdown kamera, `Scan`, dan `Matikan` tidak tampil saat terbang aktif.
   - Menyediakan alur `PF_PauseModal` $\rightarrow$ `CALIBRATION & CAMERA` $\rightarrow$ `BACK TO PAUSE` / `RESUME FLIGHT` tanpa me-reload scene gameplay.
   - Menyimpan dan memulihkan kamera terakhir secara otomatis via `PlayerPrefs` (fallback ke webcam indeks `0`).
2. **Tahap 1 (`[[laptop-a/planning_revisi_titik_wajah_dan_pov]]`):**
   - Mengunci pelacakan wajah Python CV (`MouthTracker` & `active_cv_sender.py`) pada 3 titik (**Hidung**, **Dagu**, dan **Mulut** bibir atas/bawah), menghasilkan `HEAD_PITCH` (`-1.0 .. +1.0`) murni dari Hidung & Dagu tanpa `chest_elevation`, menghasilkan `MOUTH_OPEN` (`0.0 .. 1.0`), serta menjaga nol kontaminasi ke `steering_input`.
3. **Perbaikan Temuan Uji Kamera Langsung:**
   - Mengatasi kerangka badan dobel, UI yang menabrak tepi border/overlay pada Kalibrasi & Pause, serta kestabilan pembacaan titik dari jarak jauh (`~2.5–3.5 m`).

---

## 2. Keputusan Desain / Arsitektur / Teknis
- **Rasio Kamera End-to-End tanpa Distorsi:**
  - Python `VideoUDPSender.compute_scaled_size(src_w, src_h)` menjaga proporsi asli di dalam batas `max_edge = 320` (`320x180`, `320x240`, `180x320`, `240x320`) dibanding ukuran paksa `320x180`.
  - `BiometricNormalizer.calculate_biometrics(..., aspect_ratio=...)` memakai kompensasi `aspect_scale_x = safe_aspect / (16/9)` untuk perhitungan metrik/sudut, sambil mempertahankan koordinat `[0..1]` layar pada `mid_shoulder` & `mid_hip`.
  - Unity menggunakan `AspectRatioFitter` (`AspectMode.FitInParent`) pada preview webcam & skeleton fallback di Kalibrasi maupun PIP HUD, dengan `DarkFallback` tetap aktif agar feed `9:16` mendapat pillarbox gelap simetris.
- **Unscaled Time Saat Pause In-Game:**
  - `UDPPoseReceiver`, `DefaultFlightInputMapper`, `DragonWingAnimator` (`usePreview`), dan `LiveDragonPreviewController` berjalan di `unscaledDeltaTime`/`unscaledTime` agar kalibrasi dan preview 3D Fanamir tetap responsif di `Time.timeScale == 0f`, sedangkan `DragonFlightController` kembali awal saat `Time.deltaTime <= 0f`.
- **Single-Skeleton Rule di Preview UI:**
  - Karena video UDP `5053` dari Python sudah merender kerangka tubuh + titik wajah, `UISkeletonOverlayGraphic` di Unity (`CalibrationPanelUI.cs` & `InGameFlightHUD.cs`) hanya diaktifkan saat **`!hasVideo`**, menghilangkan kerangka dobel akibat perbedaan latency/smoothing.
- **Matematika `HEAD_PITCH` & `MOUTH_OPEN` (`MouthTracker`):**
  - Geometri vektor 3D isotropik `(Chin #152 -> Nose #4)` dikompensasi terhadap *head roll* (garis pipi `#234/#454`) dan *excess lip gap* (`#13/#14`) sehingga membuka mulut lebar (`MOUTH_OPEN > 0.45`) maupun miring kepala (`banking`) tidak memicu pitch palsu.
  - Verifikasi empiris `FaceLandmarker` + `facial_transformation_matrixes` Camera 0 mengonfirmasi orientasi kanonik netral `-38.8°` (selaras `< 0.1°` dengan matriks rotasi 3D wajah), dipasangkan dengan `AdaptiveOneEuroFilter` dan *rescaled deadzone* `±2.2° .. ±16.0°`.
  - `PoseUDPSender.send_pose()` menambah parameter opsional `head_pitch` di token `43` (menjadi 44 float bila dikirim; tetap 43 float bila `None` demi kompatibilitas suite lama).
- **Optimasi Deteksi Jarak Jauh:**
  - Inferensi `YOLOv8n-pose` pada CUDA ditingkatkan ke `imgsz=960` (vs CPU `640`) dengan `--conf 0.20`, `CONFIDENCE_MIN`/`CORE_CONFIDENCE_MIN = 0.20`, plus *grace hold* `0.35s` di `active_cv_sender.py` (`DEGRADED` alih-alih langsung `LOST`).
  - `MouthTracker` menghapus floor crop `0.30 * min(w,h)` saat `shoulder_width_norm` tersedia, melakukan *upsampling* `INTER_CUBIC` crop kecil ke `224×224`, memakai threshold `FaceLandmarker` `0.30`, mempertahankan `_last_face_norm_xy` hingga `0.60s`, dan fallback pencarian `640 px`.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Python CV (`ARDragonSkyGuardian_CV/`):**
  - `camera_stream.py`: Tambah `actual_width`, `actual_height`, `_sync_actual_resolution()`, `get_resolution()`.
  - `udp_sender.py`: Tambah `VideoUDPSender.compute_scaled_size()`, guard `<= 65000` byte, dan parameter opsional `head_pitch` (`tokens[43]`) di `PoseUDPSender`.
  - `pose_stabilizer.py`: Tambah kompensasi `aspect_ratio` di `BiometricNormalizer`, turunkan `CONFIDENCE_MIN`/`CORE_CONFIDENCE_MIN` ke `0.20`.
  - `player_calibrator.py`: Tambah `neutral_head_pitch_deg` (`-38.8`) pada `PlayerBiometricProfile` & sampling di `STEP1_NEUTRAL`.
  - `mouth_tracker.py`: Upgrade ke tracker 3 titik (`Nose #4`, `Chin #152`, `Lips #13/#14/#61/#291`), `compute_nose_chin_elevation_deg()`, `normalize_head_pitch()`, crop adaptif + upsample `224×224`, hysteresis `0.45/0.35`, serta `set/recenter/reset_neutral_baseline()`.
  - `active_cv_sender.py`: Integrasi `MouthTracker.process_face()`, hapus `chest_elevation`/`head_lat_ratio` dari pitch & seated steering, `yolo_imgsz=960` (CUDA), grace hold `0.35s`, overlay 3 titik + bar `HEAD_PITCH`/`MOUTH_OPEN` (`360×298`), hotkey `'N'`/`'C'`/`'R'`.
  - `test_tahap1_face_tracking.py`: Suite validasi baru (`7/7 PASS`).
- **Unity C# & Editor (`AR Dragon Sky Guardian/Assets/`):**
  - `PythonProcessManager.cs` & `CameraControlModuleUI.cs`: Auto-persistence `PlayerPrefs` `"DragonWingAR_LastCameraDevice"` / `"DragonWingAR_LastCameraIndex"`, fallback indeks `0`.
  - `CalibrationPanelUI.cs`, `InGameFlightHUD.cs`, `PauseModalUI.cs`: `AspectRatioFitter` (`FitInParent`), penyembunyian `pipCameraControlsCard`, mode `InGamePauseMode` (`CALIBRATION & CAMERA`, `BACK TO PAUSE`, `RESUME FLIGHT`), dan aktivasi `UISkeletonOverlayGraphic` hanya saat `!hasVideo`.
  - `UDPPoseReceiver.cs`, `DefaultFlightInputMapper.cs`, `DragonFlightController.cs`, `DragonWingAnimator.cs`, `LiveDragonPreviewController.cs`: Dukungan unscaled time saat pause & pembekuan gerak naga di `Time.deltaTime <= 0f`.
  - `UISceneAndPrefabBuilder.cs`: Rebuild `PF_CalibrationPanel.prefab`, `PF_PauseModal.prefab` (scrim `0.96f`), `MainMenu_Lobby.unity`, `MainIsland_Gameplay.unity`, & `Test_Dragon_Flight.unity` (`LeftFooterActionsBar` & `RightFooterBar` dipindah ke `bottom=26 px`, `left/right=28 px` di dalam tepi `UI_Panel_Parchment_Calib_9Slice.png`).

---

## 4. Detail Penting yang Harus Diingat
- **Konstanta & Kontrak Kunci:**
  - Key `PlayerPrefs` kamera: `"DragonWingAR_LastCameraDevice"` dan `"DragonWingAR_LastCameraIndex"` (`autoLaunchPython` tetap `false`).
  - Kanonik netral `FaceLandmarker` `(Chin #152 -> Nose #4)`: **`-38.8°`** (`set_neutral_baseline` clamped `[-58.0, -18.0]`); hotkeys preview `'N'` = zero baseline kepala, `'C'` = kalibrasi, `'R'` = reset default.
  - Ambang semburan air (`MOUTH_OPEN`): **`> 0.45`** (hysteresis ON `0.45`, OFF `0.35`).
  - Tepi aman dalam *stepped card* `UI_Panel_Parchment_Calib_9Slice.png` (`224×230 px`): krim dalam dimulai di `bottom + 22 px` dan `top + 16 px` dengan lekukan sudut `27 px`; elemen footer harus di `bottom >= 26 px` dan inset horizontal `>= 28 px`.
- **Pencegahan Regresi UI & CV:**
  - `UISkeletonOverlayGraphic` wajib tetap tergating `!hasVideo` agar tidak menggandakan kerangka Python.
  - `PoseUDPSender.send_pose()` hanya menambah token `43` jika `head_pitch is not None` sehingga tes 43-token (`test_e2e_flight_physics.py`) tetap kompatibel.
- **Cakupan Verifikasi Terakhir di Sesi:**
  - Python CV: `test_tahap1_face_tracking.py` (`7/7`), `test_phase1_validation.py` (`8/8`), `test_phase4_validation.py` (`7/7`), `test_additional_gestures.py` (`10/10`), `test_e2e_flight_physics.py` (`6/6`), uji aspek rasio `VideoUDPSender`/`BiometricNormalizer` (`16:9`, `4:3`, `9:16`), dan runtime live `active_cv_sender.py` (`cuda:0`, `imgsz=960`, Cam `0` `1280×720`).
  - Unity Play Mode: `MainMenu_Lobby.unity`, `MainIsland_Gameplay.unity`, dan `Test_Dragon_Flight.unity` lulus dengan `0` compile error & `0` console error (uji `4:3`/`9:16` PIP & jangkauan `2.5–3.5 m` terverifikasi melalui generator UDP/runtime CUDA sebelum uji fisik langsung oleh user).

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- Penyesuaian Unity controller untuk **Tahap 2 (`HEAD_PITCH`)**, penyelarasan 17–19 pose & momentum bar HUD, serta `VillageZoneController` / `GameManager` dilanjutkan pada antrean aktif di [[Tasks]].
- Navigasi: [[00 - Overview]] | [[Tasks]] | [[Devlog]] | [[laptop-a/planning_kamera_dinamis_dan_navigasi_pause]] | [[laptop-a/planning_revisi_titik_wajah_dan_pov]]
