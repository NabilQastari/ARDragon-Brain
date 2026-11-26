---
title: "Penyempurnaan Kontrol Terbang dan FlyUp"
type: session-memory
tags:
  - session-memory
  - laptop-b
  - computer-vision
  - fly-up
  - flight-physics
  - motion-recognizer
  - pose-stabilizer
date: 2026-11-26
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Penyelarasan 17 Pose dan Propeller Dash]]"
  - "[[Sistem Momentum Terpadu dan Revisi Kontrol]]"
  - "[[Ketahanan Motion Blur Propeller Dash]]"
  - "[[laptop-a/cv-control/panduan_detail_pose_dan_sync_laptop_b]]"
  - "[[laptop-a/gameplay-mechanics/planning_momentum_dan_revisi_kontrol]]"
  - "[[laptop-b/Tasks]]"
  - "[[laptop-b/Devlog]]"
---

# Penyempurnaan Kontrol Terbang dan FlyUp

## 1. Tujuan & Konteks Sesi
- Mengaudit dan menyempurnakan mekanisme kontrol penerbangan yang belum bekerja maksimal di webcam nyata (dikerjakan pada `2026-11-24`, diarsipkan pada `2026-11-26`), khususnya **`#07 Fly Up` (Open Y-Pose)**, **`#04 Takeoff` / `#06` / `#06B` Flapping**, transisi **`#09 Hover` $\rightarrow$ `#10 Gentle Descent`**, **`#14 Propeller Dash`**, **`#15C Solaria Dew Rain`**, serta responsivitas tracking CV dan baseline `HEAD_PITCH`.
- Menjaga **nol regresi** pada **6 pose terkunci** (`#01` Grounded Idle, `#02` Grounded Walk, `#04` Takeoff, `#05` Lean & Arm Tilt, `#08` Glide, `#13` Tuck to Dive) maupun kontrak sistem Momentum Terpadu (`0–100%`).

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Penyelesaian Konflik & Kerapuhan `#07 Fly Up` (`motion_recognizer.py`, `cv_locomotion_config.json`):**
   - **Anti-Hijack `#11 Fly Down (Y-Dive)`:** `FlyDownClassifier` (Prioritas 4) sebelumnya memakai `le[1] >= ls[1] - 0.02 * tl_safe` hingga `avg_elbow_angle <= 152°`, sehingga pemain yang melakukan `#07 Open Y-Pose` (Prioritas 6) dengan siku sedikit menekuk (`~132°–150°`) dekat ketinggian bahu langsung dibajak menjadi `#11 FLY_DOWN_Y`. Diperbaiki dengan mewajibkan `le[1] >= ls[1] + 0.005 * tl_safe` dan penjaga `not looks_like_open_y`.
   - **Histeresis & Dukungan Bank Turn `#07`:** `FlyUpClassifier` diberi *exit hysteresis* + *grace hold* `0.15s` (`last_y_valid_time`), toleransi siku terhadap bahu (`+0.08` / `+0.12 * tl_safe`), sudut siku (`126°` masuk / `118°` keluar), kecepatan pergelangan (`0.48` masuk / `0.72` tahan), serta batas miring tubuh `32°`/`35°` agar pemain bisa menanjak sambil berbelok.
   - **Proteksi `#06` & `#06B Steep Climb Flap`:** Ambang *legacy* Path 2 (`chest_elevation` + flapping) dinaikkan ke `1.84 / 1.76` (terkalibrasi `min(1.90, max(1.78, neutral_ratio + 0.14))` / `min(1.82, max(1.70, neutral_ratio + 0.07))`) dan dinonaktifkan saat `head_pitch > 0.12`, sehingga kepakan `#06` / `#06B` pada postur tubuh tinggi/tegak tidak dibajak menjadi `FLY_UP` (yang menguras momentum lalu *stall*).
2. **Simetri Pembalikan Kepakan & Transisi `#09 -> #10` (`motion_recognizer.py`):**
   - `FlapStrokeClassifier` sebelumnya hanya menghitung ulang `flap_power` pada pembalikan `UPSTROKE -> DOWNSTROKE`. Kini `DOWNSTROKE -> UPSTROKE` juga menghitung `flap_power` secara simetris, dengan `VELOCITY_THRESHOLD = 0.34`, `CONTINUE_VELOCITY_THRESHOLD = 0.22`, `MIN_AMPLITUDE = 0.18`, dan `HOLD_WINDOW_SEC = 0.42s`.
   - `HoverClassifier` memakai `elbow_angle_history` lebih pendek (`maxlen = 4`) dan melonggarkan `straight_stationary` menjadi `avg_elbow_angle >= 142.0` saat `wrist_vert_speed < 0.18` dan `avg_flap_height <= -0.22`, sehingga transisi `#09 -> #10 Low-A` langsung lepas tanpa tertahan *grace window* `0.25s`.
3. **Responsivitas Tracking, Crop MediaPipe, & `HEAD_PITCH` (`pose_stabilizer.py`, `hybrid_pose_tracker.py`, `mouth_tracker.py`, `player_calibrator.py`, `active_cv_sender.py`):**
   - **One-Euro Filter (`RobustKeypointTracker`):** Mengurangi lag lengan dari `0.40 Hz / 0.12` (`lw`/`rw`) menjadi `ls/rs/lh/rh/nose: (0.35 Hz, 0.03)`, `le/re: (0.65 Hz, 0.18)`, dan `lw/rw: (0.75 Hz, 0.45)`.
   - **Crop & Guard `HybridPoseTracker`:** Memperluas padding atas ROI MediaPipe dari `+12%` menjadi `+28%` (`pad_w = +14%`, `pad_bot = +12%`) agar tangan terangkat (`#07`, `#15C`) tidak terpotong; menolak lonjakan ekstrem MediaPipe saat YOLO ber-confidence `>= 0.70`; serta tidak menaikkan confidence wrist saat YOLO `< 0.25` dan wrist MediaPipe kolaps ke siku (`< 0.14 * sw_px`) agar `recover_motion_blurred_wrists()` tetap aktif untuk `#14`.
   - **Auto-Baseline Wajah & Nol Jeda 1 Frame:** `MouthTracker` menggunakan *sliding window* `15` frame (`spread <= 4.0°`) dan rentang clamp diperluas `[-64°, +8°]` (diselaraskan dengan `player_calibrator.py`). Di `active_cv_sender.py`, `face_result` dan `head_pitch` dievaluasi sebelum `gesture_sm.update()`.
4. **Penyelarasan Fisika & Normalisasi `#15C` di Unity (`DragonFlightController.cs`, `UDPPoseReceiver.cs`):**
   - `DragonFlightController`: `_takeoffFlapTimer` meluruh bertahap (`-dt * 2.0f`) saat jeda 1 frame alih-alih reset instan `0f`; `#07 Fly Up` menerima trim halus `Mathf.Max(0f, bodyPitchTrim) * 3.0f`; dan mode *Fly Up Stall* menetapkan `targetPitch = -3.0f` langsung tanpa *double-lerp*.
   - `UDPPoseReceiver`: `_isHandsHighUp` (`#15C Solaria Dew Rain`) dinormalisasi terhadap `torsoLenSafe` (`>= 0.42f * torsoLenSafe`) dan `swSafe` (`wristSpanRatio <= 1.40f`) agar jarak-invarian seperti Python CV.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Python CV (`ARDragonSkyGuardian_CV/`):**
  - `cv_locomotion_config.json`, `motion_recognizer.py`, `pose_stabilizer.py`, `hybrid_pose_tracker.py`, `mouth_tracker.py`, `player_calibrator.py`, `active_cv_sender.py`, dan `test_all_17_poses_alignment.py` (+3 kasus uji: `#07` siku santai anti-hijack `#11`, `#07 + Bank Turn`, dan `#06B` postur tegak).
- **Unity C# (`AR Dragon Sky Guardian/Assets/_Scripts/`):**
  - `Controllers/DragonFlightController.cs` dan `CV_Receiver/UDPPoseReceiver.cs`.
- **Hasil Verifikasi:**
  - **Python CV (`ARDragonSkyGuardian_CV\venv\Scripts\python.exe`) — 7/7 Suite Lulus 100%:** `test_all_17_poses_alignment.py`, `test_momentum_and_control_revision.py`, `test_additional_gestures.py` (`10/10`), `test_e2e_flight_physics.py` (`6/6`), `test_phase1_validation.py` (`8/8`), `test_phase5_validation.py` (`7/7`), dan `test_tahap1_face_tracking.py` (`7/7`).
  - **Unity 6 Play Mode (`MainIsland_Gameplay.unity`):** `MomentumFlightVerificationRunner.RunVerificationMenu()` $\rightarrow$ **`ALL MOMENTUM & FLIGHT PHYSICS CHECKS PASSED (100%)`** (`0` console errors, `0` console warnings); scene aktif dikembalikan ke `MainMenu_Lobby.unity`.

---

## 4. Detail Penting yang Harus Diingat
- **Batas Aman `FlyDownClassifier` vs `FlyUpClassifier`:** `#11 Y-Dive` mensyaratkan siku di/bawah bahu (`>= +0.005 * tl_safe`) dan `not looks_like_open_y`; jangan turunkan syarat ini di bawah garis bahu karena akan kembali menyergap `#07 Open Y-Pose`.
- **Ambang `FlyUpClassifier` Legacy Path 2:** Default `1.84 / 1.76` (terkalibrasi `+0.14 / +0.07`) dan wajib tetap terblokir saat `head_pitch > 0.12` agar `#06B Steep Climb Flap` tetap masuk ke `FLAPPING` di Python & Unity.
- **Konstanta `RobustKeypointTracker` (`pose_stabilizer.py`):** `ls/rs/lh/rh/nose = (0.35, 0.03)`, `le/re = (0.65, 0.18)`, `lw/rw = (0.75, 0.45)` — jangan turunkan `lw/rw` kembali ke `(0.40, 0.12)` karena menimbulkan lag signifikan pada gerakan kepakan & Y-Pose.
- **Crop `HybridPoseTracker`:** Pertahankan `pad_top = 0.28` serta penjaga `is_blur_collapsed_wrist` (tidak menaikkan confidence saat `yolo_conf < 0.25` dan wrist `< 0.14 * sw_px` dari siku) demi menjaga deteksi `#07`, `#15C`, dan pemulihan blur `#14`.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- Uji coba langsung di depan webcam oleh pengguna pada skenario gameplay nyata (`#07 Fly Up`, `#06`/`#06B`, `#09`/`#10`, `#14`, `#15C`) untuk validasi rasa kontrol ergonomis.

---

**Navigasi Obsidian:** [[00 - Overview]] · [[laptop-b/konteks|konteks]] · [[laptop-b/prd|prd]] · [[laptop-b/GDD_Pulau|GDD_Pulau]] · [[laptop-b/Tasks|Tasks]] · [[laptop-b/Devlog|Devlog]]
