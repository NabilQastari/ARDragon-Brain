---
title: "Ketahanan Motion Blur Propeller Dash"
type: session-memory
tags:
  - session-memory
  - laptop-b
  - computer-vision
  - propeller-dash
  - motion-blur
  - pose-stabilizer
  - motion-recognizer
date: 2026-11-26
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Penyelarasan 17 Pose dan Propeller Dash]]"
  - "[[laptop-a/cv-control/panduan_detail_pose_dan_sync_laptop_b]]"
  - "[[laptop-b/Tasks]]"
  - "[[laptop-b/Devlog]]"
---

# Ketahanan Motion Blur Propeller Dash

## 1. Tujuan & Konteks Sesi
- Mengatasi masalah pose **`#14 Propeller Dash`** yang sulit terdeteksi pada webcam saat lengan bawah diputar cepat akibat **camera motion blur**.
- Menjaga **nol regresi** pada **6 pose terkunci** (`#01` Grounded Idle, `#02` Grounded Walk, `#04` Takeoff, `#05` Lean & Arm Tilt, `#08` Glide, `#13` Tuck to Dive) serta memastikan gerakan **1D flapping** (`#04`, `#06`, `#15A`) dan putaran di depan dada (*chest-front*) tetap ditolak dari `#14 Propeller Dash`.

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Identifikasi Akar Masalah Motion Blur pada `#14`:**
   - **Velocity Gate & Confidence Cutoff (`RobustKeypointTracker.update_joint`):** *Motion blur* menurunkan confidence YOLO pergelangan (`lw`/`rw`) ke kisaran `0.08–0.45` (`< CONFIDENCE_HIGH = 0.50` / `< CONFIDENCE_MIN = 0.20`), sekaligus memicu `step > max_step` (`3.2 * sw_scale * dt`), sehingga posisi pergelangan dibekukan ke `prev_x, prev_y`.
   - **Kolaps Keypoint ke Siku & Pergeseran Pusat Orbit 3D:** Jejak blur lengan bawah menarik prediksi YOLO mendekati siku yang lebih tajam, sementara putaran kerucut 3D yang condong ke depan kamera memproyeksikan elips kecil dengan pusat orbit bergeser dari titik siku (`le`/`re`), membuat sudut `atan2(lw - le)` berayun lokal tanpa mencapai `2π`.
   - **Reset Akumulasi Terlalu Sensitif:** *Blur-streak jitter* 1 frame (`|dtheta| >= 0.45`), lompatan fase `126°–235°` setelah 1–2 frame blur, atau goyangan keypoint siku 1 frame (`not upper_arms_spread`) langsung mengosongkan `accumulated_angle` ke `0.0`.
2. **Pemulihan Piksel *Elbow-Annulus Motion-Blur Recovery* (`pose_stabilizer.py`, `active_cv_sender.py`):**
   - Menambahkan fungsi `recover_motion_blurred_wrists()` yang berjalan pada grayscale `320×180` ketika lengan atas cukup terentang (`elbow_span_sw >= 1.08`) dan confidence pergelangan turun (`c_w < 0.42`) atau titik pergelangan kolaps ke siku (`dist_we_sw < 0.16`).
   - Menghitung *temporal frame difference* berbobot gradien spasial frame saat ini (`diff * (grad_curr + 6.0)`, agar bayangan ghost frame `t-1` tertekan) di dalam cincin lengan bawah `r_min = 0.13 * sw_px` s.d. `r_max = 0.80 * sw_px` (di luar area torso) untuk memulihkan lokasi pergelangan tangan, serta meneruskan `raw_lw`/`raw_rw` ber-lag rendah ke `GestureStateMachine` (`0.60 * raw + 0.40 * filtered` di `PropellerRotationClassifier`).
3. **Pelonjakan Responsivitas Tracker & Pelacakan Orbit *Dual-Center* (`pose_stabilizer.py`, `motion_recognizer.py`):**
   - Menambahkan ambang khusus blur (`BLUR_WRIST_CONF_MIN = 0.08`, `BLUR_ELBOW_CONF_MIN = 0.12`), menaikkan `beta` One-Euro Filter pergelangan (`lw`/`rw`: `(1.45, 0.75)`), dan membebaskan pergelangan yang terikat secara anatomis ke siku (`<= 0.98 * sw`) dari pembekuan `conf < 0.50` (`max_step = 7.5 * sw_scale * eff_dt`).
   - Melengkapi `PropellerRotationClassifier` dengan **Dual-Center Orbital Tracking**: sudut terhadap **Siku (`le`/`re`)** sekaligus **Sentroid Lintasan Pergelangan (`rel_history` 10 frame)** yang dikunci oleh syarat *2D circularity* (`span_x >= 0.055 * sw`, `span_y >= 0.055 * sw`, `aspect_2d >= 0.20`, `|sin(dtheta)| >= 0.10`) agar putaran 3D offset tetap terakumulasi sambil menolak flapping 1D.
   - Menambahkan *spin-direction phase unwrapping* lintas 1–3 frame blur (`125°–245°` searah `accum` aktif), menghapus reset 1-frame `|dtheta| >= 0.45` (wajib `2–3` frame berlawanan berturut-turut), serta menambahkan *elbow-spread grace* `4` frame.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **`ARDragonSkyGuardian_CV/cv_locomotion_config.json`:** Pembaruan blok `propeller_dash`.
- **`ARDragonSkyGuardian_CV/pose_stabilizer.py`:** Penambahan `BLUR_WRIST_CONF_MIN`, `BLUR_ELBOW_CONF_MIN`, tuning `AdaptiveOneEuroFilter` (`lw`/`rw` `beta=0.75`, `le`/`re` `beta=0.30`), relaksasi *velocity outlier gate* pergelangan terikat siku, dan fungsi baru `recover_motion_blurred_wrists()`.
- **`ARDragonSkyGuardian_CV/motion_recognizer.py`:** Peningkatan `PropellerRotationClassifier` (blending `raw_lw`/`raw_rw`, *elbow-spread grace*, *dual-center centroid orbit* ber-gate *2D circularity*, *phase unwrapping*, & debounce *blur-streak*) serta penyaluran `raw_lw`/`raw_rw` di `GestureStateMachine.update()`.
- **`ARDragonSkyGuardian_CV/active_cv_sender.py`:** Konversi `curr_gray_small` (`320×180`), pemanggilan `recover_motion_blurred_wrists()` sebelum filter `lw`/`rw`, pembaruan `kpts` & `confidences`, dan penyertaan `raw_lw`/`raw_rw` ke `gesture_sm.update()`.
- **`ARDragonSkyGuardian_CV/test_all_17_poses_alignment.py`:** Penambahan `test_propeller_dash_motion_blur_resilience()` (3 skenario uji: *low-conf* `0.12..0.32` + *streak reversal* + *elbow wobble*, *3D forward-angled cone* offset dari siku, dan *pixel-level Elbow-Annulus Motion-Blur Recovery* saat `c_w = 0.02`).
- **Hasil Verifikasi (`ARDragonSkyGuardian_CV\venv\Scripts\python.exe`):**
  - `test_all_17_poses_alignment.py` (termasuk `test_propeller_dash_motion_blur_resilience`, 6 pose terkunci, 17 pose, & Clean CV Preview): **100% PASS**.
  - Regresi penuh `test_phase1_validation.py`, `test_phase2_validation.py`, `test_phase4_validation.py`, `test_additional_gestures.py`, `test_momentum_and_control_revision.py`, `test_tahap1_face_tracking.py`, dan `test_e2e_flight_physics.py`: **100% PASS**.

---

## 4. Detail Penting yang Harus Diingat
- **Parameter `propeller_dash` di `cv_locomotion_config.json`:**
  - `min_rotation_accum_rad: 2.15`, `min_angular_velocity: 1.85`, `min_side_angular_velocity: 0.45`, `min_orbit_radius_sw: 0.11`, `min_angle_radius_sw: 0.065`, `spread_horiz_enter: 0.22`, `spread_horiz_exit: 0.15`, `spread_vert_enter: 0.68`, `spread_vert_exit: 0.85`, `grace_hold_sec: 0.42`.
- **Gate Anti-False-Positive di `PropellerRotationClassifier`:**
  - `raw_spread_ok` mensyaratkan rata-rata `le_dx, re_dx >= horiz_req`, `min(le_dx, re_dx) >= horiz_req * 0.68`, `le_dy/re_dy <= vert_req`, dan `elbow_span_sw >= elbow_span_req` (`1.26` enter / `1.14` exit).
  - `clearly_tucked` (`elbow_span_sw < 1.05` atau kedua/salah satu siku sangat rapat & turun) langsung mereset akumulasi agar `#09 Hover` dan putaran depan dada tetap ditolak.
  - Tidak mensyaratkan `wrist_span_sw >= 0.98` pada `raw_spread_ok` karena saat lengan bawah berputar ke sisi dalam, jarak antar-pergelangan tangan bisa menyempit sesaat.
- **Konstanta `recover_motion_blurred_wrists()` (`pose_stabilizer.py`):** Aktif hanya jika `elbow_span_sw >= 1.08`, `c_e >= 0.12`, dan `(c_w < 0.42 or dist_we_sw < 0.16)`; memboosting confidence hasil pemulihan ke minimal `0.36`.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- Uji langsung di depan webcam nyata pada kondisi pencahayaan ruangan & kecepatan putar tangan aktual pemain untuk memastikan kenyamanan aktivasi `#14 Propeller Dash` di Unity Gameplay (`MainIsland_Gameplay.unity`).

---

**Navigasi Obsidian:** [[00 - Overview]] · [[laptop-b/konteks|konteks]] · [[laptop-b/prd|prd]] · [[laptop-b/GDD_Pulau|GDD_Pulau]] · [[laptop-b/Tasks|Tasks]] · [[laptop-b/Devlog|Devlog]]
