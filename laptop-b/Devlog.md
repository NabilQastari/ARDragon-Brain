---
title: Laptop B — Devlog (Eksekusi & Pengembangan Program)
tags:
  - devlog
  - laptop-b
owner: Laptop B
updated: 2026-11-14
---

# 🛠️ Devlog — Laptop B

> Catatan kronologis pengembangan kode, debugging, pengujian Unity/Python, dan hasil build dari **Laptop B**. Kembali ke [[00 - Overview]].

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
