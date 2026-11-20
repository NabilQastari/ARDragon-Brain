---
title: "Audit dan Penyempurnaan Kontrol Webcam"
type: session-memory
tags:
  - session-memory
  - laptop-b
  - computer-vision
  - flight-physics
  - unity
  - python-cv
  - control-audit
date: 2026-11-13
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[Rework CV dan Kontrol Terbang Baru]]"
  - "[[On-Ground Mechanics dan Uji 16 Skenario]]"
---

# Audit dan Penyempurnaan Kontrol Webcam

> **Catatan Kronologi:** Sesi pengerjaan berlangsung pada `2026-11-13` hingga `2026-11-14 00:47`, lalu diarsipkan menjadi catatan memori pada `2026-11-21`. Beberapa subsistem pada catatan ini selanjutnya mengalami iterasi lanjutan (lihat entri `2026-11-14` s/d `2026-11-21` di [[Devlog]]).

---

## 1. Tujuan & Konteks Sesi
1. Menyederhanakan scene uji awal [Test_Dragon_Flight.unity](file:///d:/Fauzih/Kuliah/SEMESTER%20V/Proyek%20Multimedia%20dan%20Game/AR%20Dragon/AR%20Dragon%20Sky%20Guardian/Assets/_Scenes/Test_Dragon_Flight.unity) agar pengujian kontrol terbang tidak terhalang kontur pulau lama (`/World_Island`), dengan menggantinya menjadi baseplate rumput datar yang luas.
2. Melakukan audit menyeluruh atas seluruh mekanisme kontrol gerak (*Python CV* → *UDP 5052/5053* → *Unity Input & Flight Physics* → *HUD*) serta memfokuskan pengembangan pada **kontrol webcam/CV murni** (tanpa ketergantungan keyboard fallback).
3. Menyelesaikan seluruh 4 prioritas temuan audit beserta perbaikan langsung dari hasil uji webcam nyata (*mirror* L/R, posisi duduk, responsivitas belok, kehalusan naik-turun, stabilisasi kamera, serta sinkronisasi HUD & trigger kalibrasi dari Unity ke Python).
4. Meninjau status [[laptop-a/Tasks]] dan [[laptop-a/Devlog]] untuk memastikan ketergantungan **Sesi GUI** sebelum mulai membangun UI Lobby.

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Lingkungan Uji Datar & Kontrol Murni Webcam:**
   - Mengganti `/World_Island` dengan `/Grass_Baseplate` (`2000 × 2 × 2000 m`) dan menonaktifkan `enableKeyboardFallback` pada `UDPPoseReceiver` di `/Dragon_Player`.
2. **Protokol Telemetry 43-Token & Kanal Perintah Dua Arah (`5054`):**
   - Memperluas UDP pose `127.0.0.1:5052` dari 41 menjadi **43 token CSV** (`[41] = is_arms_away`, `[42] = upper_arm_openness`) agar `UpperArmLocomotionGate` tervalidasi kepercayaan keypoint di Python tidak dihitung ulang dari koordinat Unity yang sudah ter-lerp.
   - Menambahkan UDP command listener pada `127.0.0.1:5054` (`CALIBRATE`, `CALIBRATE_CANCEL`, `CALIBRATE_RESET`) agar kalibrasi dapat dipicu dan dibatalkan langsung dari UI Unity, termasuk dalam mode `--headless`.
3. **Orientasi Mirror Webcam Frontal (`cv2.flip`) & Mode Duduk:**
   - Pada frame yang sudah di-flip horizontal, anatomi COCO `IDX_RIGHT_*` di sisi kiri layar harus dipetakan ke `ls/le/lw/lh` (dan `IDX_LEFT_*` ke `rs/re/rw/rh`), agar kemudi *arm tilt*, `is_forearms_extended`, dan fallback keypoint selalu searah dengan tampilan cermin webcam dan arah gerak di Unity.
   - Menambahkan sintesis pinggul virtual berbasis *shoulder roll* dan offset lateral kepala ketika `lh/rh` terpotong di bawah frame saat bermain sambil duduk, serta menyatukan kurva kemudi tunggal di Python (`linear-quadratic 0.70*r + 0.30*r^2`) dengan passthrough linier di Unity (`turnDeadzone = 0`, `steeringEasePower = 1`).
4. **Pemisahan Rotasi Kamera vs Visual Naga & Kontinuitas Vertikal:**
   - Root `/Dragon_Player` hanya berotasi **Yaw**, sementara *Pitch* & *Roll* diterapkan ke anak `/Dragon_Player/Visual_Dragon` (`ApplyAttitudeRotation()`), sehingga horizon viewport tidak terguling. Kamera mengikuti hanya `cameraPitchFollowFactor = 0.25` dengan offset Y halus.
   - *Swoop Climb* dibatasi khusus pasca-dive kecepatan tinggi (`_wasDivingHighSpeed` atau selisih `> 5.5 m/s`) dengan transisi $C^1$ `Mathf.SmoothStep`, serta sinyal `BodyPitch` (dengan *rescaled deadzone* + EMA) dijadikan *trim* kontinu saat *Flap*, *Glide*, *Gentle Descent*, *Hover*, dan *Dash*.
5. **Penahanan Sementara Sesi GUI (Saat Peninjauan Akhir Sesi 2026-11-14):**
   - Hasil pembacaan `brain/laptop-a/` menunjukkan wireframe FigCraft saat itu masih berstatus `Untitled`, desain warna visual UI & GDD Pulau $1200\times1200\text{ m}$ belum rampung, dan folder `Referensi UI/` belum tersedia di workspace lokal Laptop B sehingga eksekusi **Sesi GUI** ditahan pada penutupan sesi tersebut.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset

### A. Scene Uji & Konfigurasi Unity (`AR Dragon Sky Guardian/`)
- **`Assets/_Scenes/Test_Dragon_Flight.unity` & `Assets/Materials/Mat_Grass_Baseplate.mat`:** Menghapus `/World_Island`, memasang `/Grass_Baseplate` (`Cube` pada `(0, -1, 0)`, scale `(2000, 2, 2000)`, material URP Lit hijau rumput), memosisikan `/Dragon_Player` ke `(0, 0.85, 0)`, memasang `DefaultFlightInputMapper` terhubung ke `UDPPoseReceiver` + `DragonLocomotionConfig.asset`, menonaktifkan `enableKeyboardFallback`, dan menyinkronkan nilai scene terbaru.
- **`Assets/Settings/DragonLocomotionConfig.cs` & `.asset`:** Penyelarasan parameter duduk & vertikal (`groundTurnSpeed = 95`, `takeoffFlapThreshold = 0.28`, `takeoffMinDuration = 0.2`, `entryAwayThreshold = 0.42`, `exitAwayThreshold = 0.3`, `flapHoldLift = 3.2`).

### B. Kontrol, Input, & HUD Unity (`Assets/_Scripts/`)
- **`Controllers/DragonFlightController.cs`:** Menghapus teleportasi `+2.65 m` pada frame pertama Takeoff (`groundAvoidance` tidak mendistorsi `_isTakingOff`), mencegah `FlyUp` memotong `_isTakingOff` sebelum `_takeoffTargetY - 0.35f`, mengizinkan `TuckDive` mendarat di tanah, memisahkan `Grounded Look Up` ke `BodyPitch` murni, meningkatkan respons belok (`yawTurnSpeed = 95`, `yawSmoothSpeed = 10`, `maxRollAngle = 40`), memindahkan *pitch/roll* ke `Visual_Dragon` via `ApplyAttitudeRotation()`, meredam follow kamera (`cameraPitchFollowFactor = 0.25`), menambahkan *Swoop blending* `Mathf.SmoothStep`, serta mengintegrasikan `BodyPitch` trim kontinu (`flapHoldLift = 3.2`, `flyUpLiftPower = 7.2`, `flyUpPitch = 19`, `verticalVelocitySmoothSpeed = 3.8`).
- **`Controllers/DefaultFlightInputMapper.cs`:** Menghapus *double deadzone & curve* (`turnDeadzone = 0f`, `steeringEasePower = 1f`), dan memprioritaskan telemetry `HasUpperArmGate` dari Python.
- **`CV_Receiver/IPoseProvider.cs`, `UDPPoseReceiver.cs`, & `PythonProcessManager.cs`:** Menambahkan properti `HasUpperArmGate`, `IsArmsAway`, `UpperArmOpenness` (token `41..42`), menaikkan `smoothingSpeed = 22`, memperbaiki fallback `armTilt = _smoothedRW.y - _smoothedLW.y`, serta menambahkan sender perintah kalibrasi UDP port `5054` (`SendCommandToPython()`, `TriggerCalibration()`, `CancelCalibration()`, `ResetCalibrationProfile()`, tombol `C`, dan `--cmd-port 5054`).
- **`UI/InGameFlightHUD.cs`:** Memperbarui sinkronisasi *Gentle Descent* (`-1.6 m/s` / live `CurrentVerticalVelocity`) & *Glide*, menambahkan prioritas `PropellerDash`/`FlyDownY`/`FlyDownW` pada wireframe standby, memperlebar panel (`415 × 326 px`) & panduan bawah (`880 px`), serta menambahkan tombol kalibrasi interaktif (`MULAI KALIBRASI (C)`, `KALIBRASI ULANG (C)`, `RESET DEFAULT`, dan `BATAL`).

### C. Subsistem Python CV (`ARDragonSkyGuardian_CV/`)
- **`active_cv_sender.py`:** Menukar pemetaan mirror COCO frontal (`IDX_RIGHT_*` → kiri, `IDX_LEFT_*` → kanan), menambahkan fallback pinggul virtual saat duduk, menyinkronkan `tracker.is_core_confident(confidences)` (`CORE_CONFIDENCE_MIN = 0.25`) + perbaikan garis bahu kiri `ls[1]`, menghitung `body_pitch` dari *head tilt* & *chest elevation* dengan *rescaled deadzone* `0.20` + EMA `0.22`, membuka soket perintah UDP `--cmd-port 5054` (`create_command_socket()`, `poll_calibration_commands()`), serta mengirim `calib_state_id = 5` bila profil tersimpan (`is_calibrated=True`) aktif saat startup.
- **`player_calibrator.py`:** Menyetel `get_default_profile()` ke `is_calibrated = False`, mengkalibrasi ulang profil duduk (`max_lean = 13.5°`, `arm_tilt` hingga `14.5°`, kurva `0.70*r + 0.30*r^2`), mengaktifkan kontribusi `arm_tilt_norm`, dan menambahkan `PlayerCalibrator.reset_to_default()`.
- **`motion_recognizer.py` & `cv_locomotion_config.json`:** Memuat `_CV_CONFIG` ke seluruh classifier (`TuckDive`, `FlyUp`, `UpperArmGate`, `GentleDescent`, `FlyDown`), memperbaiki ambang `FlyUpClassifier` terkalibrasi (`min(1.80, max(1.68, neutral_ratio + 0.08))`), memperketat `HoverClassifier` (`avg_flap_height in [-1.00, 0.55]`, rata-rata tekukan siku 8-frame `<= 160°`) & `PropellerRotationClassifier` (orbit `sw` berjalan + `MIN_ANGULAR_VELOCITY = 4.5 rad/s` berbasis `dt`), memperketat `GentleDescentClassifier`, menambahkan amplop EMA asimetris `smoothed_power` di `FlapStrokeClassifier` (`0.35s` post-flap hold), serta membagi `arm_tilt_delta / 0.42`.
- **`pose_stabilizer.py`, `udp_sender.py`, `test_send_dummy_udp.py`, & `test_e2e_flight_physics.py`:** Menambahkan `CORE_CONFIDENCE_MIN = 0.25` & `is_core_confident()`, memperluas UDP CSV ke 43 token (`is_arms_away`, `upper_arm_openness`), serta memperbarui kontrak Test 5 E2E menjadi 43 token.

### D. Hasil Pengujian & Verifikasi di Akhir Sesi
- **Unity 6 (`6000.3.7f1`):** Kompilasi bersih pasca `Assets/Refresh` (`compilationFailed: false`, `consoleErrors: 0`, nol warning baru di skrip yang diubah; hanya 1 warning pra-eksisting `QuickUDPTestBuilder.cs`), dan `Test_Dragon_Flight.unity` tersimpan (`isDirty: false`).
- **Python CV Suite:** Seluruh **7 suite validasi CV** (`test_phase2_validation.py`, `test_phase3_validation.py`, `test_phase4_validation.py`, `test_phase5_validation.py`, `test_phase6_final_session.py`, `test_additional_gestures.py` [10/10], `test_e2e_flight_physics.py` [6/6]) lulus **100%** (`TOTAL FAILED: 0`), ditambah verifikasi `verify_vertical_smoothness.py` (`FlapPower` kontinu antar-stroke `min=0.377, max=0.621, max_drop=0.136`, deadzone `0.20->0.21` mulus `0.0000->0.0125`) dan `verify_udp_calibration_trigger.py` (`CALIBRATE` → `CALIBRATE_CANCEL` → `CALIBRATE_RESET` via soket UDP).

---

## 4. Detail Penting yang Harus Diingat
- **Pemetaan Mirror Webcam (`active_cv_sender.py`):** Frame kamera dibalik horizontal (`cv2.flip(frame, 1)`), sehingga keypoint COCO sisi kiri gambar selalu berasal dari `IDX_RIGHT_*` (`ls/le/lw/lh`) dan sisi kanan dari `IDX_LEFT_*` (`rs/re/rw/rh`).
- **Kontrak Kalibrasi & Telemetry 43-Token:**
  - Port UDP Pose: `5052` (43+ float CSV, dengan token `41..42` = `is_arms_away`, `upper_arm_openness`).
  - Port UDP PIP Video: `5053` (JPEG frame).
  - Port UDP Perintah Unity → Python: `5054` (`CALIBRATE`, `CALIBRATE_CANCEL`, `CALIBRATE_RESET`).
- **Sinkronisasi Scene Override di Unity:** Karena `DragonFlightController.ApplyLocomotionConfig()` di `Awake()` menyalin nilai dari `DragonLocomotionConfig.asset` dan field public Unity membaca nilai terserialisasi di scene, penyetelan parameter lokomosi wajib disinkronkan di kode C#, `.asset`, sekaligus komponen `/Dragon_Player` di scene.
- **Keterkaitan Iterasi Berikutnya (`2026-11-14 s/d 2026-11-21`):** Logika pitch vertikal & UI pada sesi ini selanjutnya dikembangkan lagi oleh sistem `HEAD_PITCH` 3-titik wajah (token `43`), *Momentum* terpadu (`0–100%`), *Dynamic Wings Rigging*, serta UI Hybrid penuh di `MainMenu_Lobby.unity` & `MainIsland_Gameplay.unity`.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- **Saat Penutupan Sesi (`2026-11-14`):** Masih menunggu `Uji Play Mode Webcam`, penyempurnaan tekstur/ekspor `rumah.blend`, penyelesaian desain visual UI & GDD Pulau di Laptop A sebelum **Sesi GUI**, **Sesi Model Pulau**, **Sesi 4**, dan **Sesi 5**.
- **Status Terkini Saat Pencatatan Memori (`2026-11-21`, merujuk [[Tasks]]):** `Sesi GUI`, `Sesi Model Pulau`, `Sesi 4`, dan `Sesi 4B` sudah selesai pada sesi-sesi lanjutan; item terbuka yang tersisa di antrean adalah:
  - [ ] **Uji Play Mode Webcam** validasi rasa gerak langsung oleh pemain di Unity Editor.
  - [ ] **Texturing & Ekspor `rumah.blend`** (lihat [[Analisis dan Tekstur Rumah Blend]]).
  - [ ] **Sesi 5 & Feature-to-UI Sync (`GameManager` & `AudioManager`, timer, bintang 1–3, & Windows build)**.

---

**Navigasi Obsidian:** [[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[Rework CV dan Kontrol Terbang Baru]] · [[On-Ground Mechanics dan Uji 16 Skenario]]
