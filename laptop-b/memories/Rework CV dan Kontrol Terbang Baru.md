---
title: "Rework CV dan Kontrol Terbang Baru"
type: session-memory
tags:
  - session-memory
  - laptop-b
  - computer-vision
  - flight-physics
  - sky-peck
  - unity
date: 2026-11-13
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
---

# Rework CV dan Kontrol Terbang Baru

## 1. Tujuan & Konteks Sesi
Sesi ini berfokus pada pengerjaan dua rangkaian besar peningkatan sistem kontrol gerak tubuh (*Sky Peck-inspired gameplay*) di **AR Dragon Sky Guardian** (Unity `6000.3.7f1` URP + Python CV):
1. **Master Development Plan (Phase 0–6):** Audit dan perombakan menyeluruh sistem Computer Vision, stabilisasi keypoint, deteksi gestur temporal, input mapping terdecouple, fisika terbang kontinu, serta validasi stres & performa.
2. **Additional Flight Control Requirements (Phase A & Phase B):** Penambahan tiga mekanisme terbang baru yang dibedakan secara ketat dari *Normal Flight* dan *Normal Dive*:
   - **Fly Up (Upward Flight):** Mengangkat dada + mengepakkan tangan ritmis untuk menanjak tajam dengan akselerasi vertikal dan peluruhan momentum alami saat berhenti.
   - **Hover Mode (Stay on Air):** Merapatkan lengan atas ke dada + menekuk siku + mengepakkan lengan bawah agar naga berhenti melaju maju dan mempertahankan ketinggian di udara (serta turun perlahan jika kepakan berhenti).
   - **Forward Glide / Gentle Descent (Tucked T-Pose):** Merapatkan lengan atas ke dada + merentangkan lengan bawah menyerupai huruf T tanpa kepakan untuk meluncur maju dengan penurunan ketinggian yang jauh lebih lembut daripada dive normal.

Batasan utama yang dijaga sepanjang sesi: seluruh gestur dan mekanisme baru hanya mengendalikan **gameplay flight physics** dan HUD; **wing rigging dan animasi tulang sayap (skeletal bone animation) tidak disentuh**.

---

## 2. Keputusan Desain / Arsitektur / Teknis
- **Hierarki Prioritas State Terbang (Strict Priority Arbitration):**
  State machine pada `GestureStateMachine` (Python) dan `HandleSkyPeckFlightPhysics()` (Unity) menerapkan urutan prioritas eksklusif agar tidak ada konflik input:
  1. `PROPELLER_DASH` (rotasi lengan orbital kontinu)
  2. `TUCK_DIVE` / Normal Dive (pergelangan tangan rapat di sternum, siku mengarah ke bawah)
  3. `HOVER` (lengan atas rapat ke dada + kepakan lengan bawah)
  4. `GENTLE_DESCENT` (lengan atas rapat ke dada + lengan bawah membentang membentuk T, tanpa kepakan)
  5. `FLY_UP` (dada terangkat + kepakan tangan aktif terbuka)
  6. `FLAP_TO_CLIMB` (kepakan normal terbuka)
  7. `NEUTRAL_GLIDE` / Swoop Climb (meluncur santai atau konversi kecepatan berlebih pasca-dive/dash)

- **Temuan Invarian Biometrik & Perbaikan Threshold:**
  - **Hover Mode:** Selama pengepakan lengan bawah, sudut siku (`avg_elbow_angle`) berayun lebar dari $\sim 20^\circ$ (puncak upstroke) hingga $\sim 155^\circ$ (dasar downstroke). Pembatasan sudut siku pada rentang $[50^\circ, 135^\circ]$ merusak deteksi. Solusinya, `HoverClassifier` menggunakan invarian fisik: `upper_arm_proximity <= 0.38` (lengan atas rapat ke torso + siku di bawah bahu), kecepatan vertikal pergelangan tinggi (`wrist_vert_speed >= 0.40`), kecepatan siku tetap rendah (`elbow_vert_speed <= 0.50`), dan *temporal hold* `100 ms`.
  - **Fly Up Hysteresis:** Postur berdiri netral memiliki rasio `chest_elevation` ($dy_{\text{spine}} / S_{w,\text{safe}}$) sekitar `1.60`. Threshold exit awal `1.58` membuat state gagal keluar saat dada diturunkan. Diperbaiki menggunakan hysteresis `entry >= 1.74` dan `exit < 1.66` (atau adaptif $+9\%$ / $+4\%$ terhadap baseline kalibrasi).
  - **Gentle Descent:** Memastikan pose T pada lengan bawah tidak tertukar dengan T-pose standar ataupun *Normal Dive* menggunakan syarat `upper_arm_proximity <= 0.38`, `forearm_spread >= 1.15`, `wrist_vert_speed < 0.38`, dan *temporal hold* `120 ms`.

- **Ekspansi Protokol UDP CSV (Port 5052):**
  Payload diperluas menjadi **39 token float** (backward-compatible dengan parser lama). Token `34..38` membawa: `is_hover`, `is_gentle_descent`, `is_fly_up`, `chest_elevation`, dan `forearm_flap_power`.

- **Pemisahan Auto-Hover Desa vs In-Flight Hover:**
  Auto-hover saat berada di area desa (`_isHovering` via `SetHoverState()`) dipertahankan terpisah dari `_isInFlightHover` di `HandleSkyPeckFlightPhysics()` agar fitur desa tidak terganggu dan pemain tetap memiliki kendali yaw saat in-flight hover.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset

### A. Subsistem Python CV (`ARDragonSkyGuardian_CV/`)
- `pose_stabilizer.py`: Menambahkan perhitungan `chest_elevation`, `upper_arm_proximity`, `left_elbow_angle`, `right_elbow_angle`, `avg_elbow_angle`, `left_forearm_lateral`, `right_forearm_lateral`, `forearm_lateral_spread`, `is_upper_arms_tucked`, dan `is_forearms_extended`.
- `motion_recognizer.py`: Menambahkan `GestureState.HOVER`, `GestureState.GENTLE_DESCENT`, `GestureState.FLY_UP`, implementasi `HoverClassifier`, `GentleDescentClassifier`, `FlyUpClassifier`, serta arbitrase prioritas di `GestureStateMachine`.
- `udp_sender.py`: Menambahkan parameter dan serialisasi token `34..38` di `PoseUDPSender.send_pose()`.
- `active_cv_sender.py`: Mengirimkan status gestur baru lewat UDP dan menampilkan telemetri status di HUD OpenCV.
- `test_e2e_flight_physics.py` *(baru dibuat)*: Suite simulasi fisika Unity, roundtrip UDP 39-token, dan arbitrase state machine.

### B. Subsistem Unity C# (`AR Dragon Sky Guardian/Assets/_Scripts/`)
- `CV_Receiver/IPoseProvider.cs`: Menambahkan properti `IsHover`, `IsGentleDescent`, `IsFlyUp`, `ChestElevation`, `ForearmFlapPower`, dan `ActiveStateId`.
- `CV_Receiver/UDPPoseReceiver.cs`: Memperbarui `ParsePoseCSV()` untuk token `34..38`, menyalin data secara thread-safe di `Update()`, serta menambahkan tombol uji keyboard fallback `H` (Hover), `G` (Gentle Descent), dan `U` (Fly Up) pada New Input System maupun Legacy Input Manager.
- `Controllers/IFlightInputProvider.cs`: Menambahkan field `IsHover`, `HoverPower`, `IsGentleDescent`, `IsFlyUp`, `ChestElevation`, dan `ActiveStateId` pada struct `FlightCommand`.
- `Controllers/DefaultFlightInputMapper.cs`: Memetakan properti baru dari `IPoseProvider` ke `FlightCommand` dengan prioritas aman.
- `Controllers/DragonFlightController.cs`: Menambahkan parameter Inspector untuk ketiga mode baru dan mengimplementasikan fisika prioritas 1–7 di `HandleSkyPeckFlightPhysics()`.
- `UI/InGameFlightHUD.cs`: Memperbarui `DrawPiPSkeleton()` dan `DrawFlightHUD()` dengan label serta warna indikator untuk `HOVER MODE`, `GENTLE DESCENT`, dan `FLY UP`.

### C. Hasil Pengujian & Verifikasi yang Diamati Langsung
- `.\ARDragonSkyGuardian_CV\venv\Scripts\python.exe ARDragonSkyGuardian_CV/test_e2e_flight_physics.py`: **6/6 suites PASSED (100%)**
  - Test 1 (Fly Up): `Speed=21.00 m/s`, `Vy=11.00 m/s`, `Pitch=23.9 deg`
  - Test 2 (Hover Hold): `Speed=0.03 m/s`, `Vy=0.00 m/s`, `Pitch=0.0 deg`
  - Test 3 (Hover Flap Ceased): `Speed=0.00 m/s`, `Vy=-1.57 m/s`, `Pitch=0.0 deg`
  - Test 4 (Gentle Descent): `Speed=15.94 m/s`, `Vy=-1.79 m/s`, `Pitch=-8.0 deg`
  - Test 5 (UDP Serialization 39 tokens): roundtrip socket loopback `127.0.0.1:5098` terverifikasi 39 token
  - Test 6 (State Machine Arbitration): `HOVER`, `GENTLE_DESCENT`, `FLY_UP` terverifikasi
- `.\ARDragonSkyGuardian_CV\venv\Scripts\python.exe ARDragonSkyGuardian_CV/test_additional_gestures.py`: **10/10 skenario PASSED (5.82 detik)**.
- `.\ARDragonSkyGuardian_CV\venv\Scripts\python.exe ARDragonSkyGuardian_CV/test_phase5_validation.py`: **7/7 tests PASSED**.
- `.\ARDragonSkyGuardian_CV\venv\Scripts\python.exe ARDragonSkyGuardian_CV/test_phase6_final_session.py`: **7/7 tests PASSED**.
- **Unity Editor Compilation (`console_get` via Unity MCP):** `compilationFailed: false`, `consoleErrors: 0` (3 warning lama yang tidak berasal dari perubahan sesi ini).

---

## 4. Detail Penting yang Harus Diingat
- **Parameter Inspector `DragonFlightController.cs`:**
  - Fly Up: `flyUpLiftPower = 11.0f`, `flyUpThrustAccel = 14.0f`, `flyUpPitch = 24.0f`
  - In-Flight Hover: `hoverDecelRate = 4.0f`, `inFlightHoverBobAmp = 0.35f`, `inFlightHoverBobFreq = 2.2f`, sink rate saat kepakan berhenti `-1.6f`
  - Gentle Descent: `gentleDescentSpeed = 16.0f`, `gentleDescentSinkRate = 1.8f`, `gentleDescentPitch = -8.0f`
  - Bandingkan dengan Normal Dive: `tuckDivePitch = -40f`, `maxDiveSpeed = 28f`, `diveAcceleration = 18f`
- **Keyboard Fallback Test Keys:**
  - `H` = Hover Mode, `G` = Gentle Descent, `U` = Fly Up (selain `Space/W` Flap, `S` Tuck Dive, `T` Glide, `Q/D` atau panah kiri/kanan belok).
- **Python Environment:** Menggunakan `.\ARDragonSkyGuardian_CV\venv\Scripts\python.exe`.
- **Artifact Sesi:** Laporan akhir juga tersimpan di direktori artifact conversation (`MASTER_DEVELOPMENT_FINAL_REPORT.md`).

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] Uji Play Mode langsung di Unity Editor menggunakan webcam asli dengan pemain berdiri untuk fine-tuning rasa terbang dan kenyamanan transisi *Fly Up*, *Hover Mode*, serta *Gentle Descent*.
- [ ] Lanjutkan antrean implementasi utama di [[Tasks]]:
  - **Sesi GUI:** Implementasi Canvas Menu Lobby (`Panel_Missions`, `Panel_Calibration`, `Panel_Settings`) & Victory/Defeat Modal sesuai [[laptop-a/rencana_desain_ui]].
  - **Sesi Model Pulau:** Integrasi aset pulau 3 biome dari Blender ke Unity 6 URP.
  - **Sesi 4:** `Session4_VillageBuilder.cs`, `VillageZoneController.cs`, dan VFX 3 desa.
  - **Sesi 5:** `GameManager.cs`, audio/SFX, dan build Windows standalone + `start_game.bat`.

---

**Navigasi Obsidian:** [[00 - Overview]] · [[konteks]] · [[prd]] · [[progress]] · [[GDD_Pulau]] · [[Tasks]] · [[Devlog]]
