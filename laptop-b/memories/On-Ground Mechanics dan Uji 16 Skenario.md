---
title: "On-Ground Mechanics dan Uji 16 Skenario"
type: session-memory
tags:
  - session-memory
  - laptop-b
  - locomotion
  - unity
  - python-cv
  - flight-physics
date: 2026-11-13
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
---

# On-Ground Mechanics dan Uji 16 Skenario

## 1. Tujuan & Konteks Sesi
Sebelumnya, begitu game dijalankan di Unity, karakter naga langsung maju dan terbang di udara meskipun pemain belum membentuk pose. Sesi ini bertujuan memperbaiki sistem lokomosi **AR Dragon Sky Guardian** agar:
1. Naga spawn di daratan (`Grounded Idle`) dalam keadaan diam (`0 m/s`) dan hanya bergerak setelah tracking valid serta pemain membentuk pose.
2. Gerakan darat dan udara memakai satu logika input berbasis kedekatan lengan atas ke badan (**Arm-Proximity Locomotion Gate**: *tucked* = IDLE, *away* = MAJU).
3. Seluruh mekanisme transisi darat ↔ udara (*Grounded Idle*, *Grounded Walk*, *Grounded Look Up*, *Takeoff*, *Air Gentle Descent*, *Hover*, *Glide*, *Fly Up*, *Fly Down Pose Y/W*, *Dive*, dan *Propeller Dash*) terintegrasi di Python CV + Unity tanpa menyentuh rigging/animasi sayap (yang ditangguhkan).

---

## 2. Keputusan Desain / Arsitektur / Teknis
- **Pembaruan Dokumen Perencanaan (Bagian 2):** Menambahkan section `ON-GROUND MECHANICS & ARM-PROXIMITY LOCOMOTION (UPGRADE v6.1)` dan `DEFERRED — DRAGON ANIMATION & POSE PLAN` ke [[prd]] dan [[konteks]] di root project sebelum implementasi kode.
- **Pemisahan Glide vs Gentle Descent (Asumsi Desain):** **Glide** (T-pose penuh, lengan atas *away*, `cruiseSpeed = 14.0 m/s` dengan peluruhan momentum bertahap) dan **Gentle Descent** (lengan atas *tucked*, tanpa kepak, `gentleDescentSpeed = 8.5 m/s`, `gentleDescentSinkRate = 1.6 m/s`) dipisahkan sebagai dua state berbeda agar Gentle Descent lebih lambat dan lebih ringan momentumnya daripada Glide.
- **Audit Dive vs T-Pose:** Memperketat `TuckDiveClassifier` (`tuck_limit <= 1.30`, default `1.25`) di Python CV dan menghapus overlap `|| _isAPose` pada `UDPPoseReceiver.IsDiving` (`IsDiving => _isTuckDive`) serta memperketat `handsCompact < shoulderDist * 1.25f`.
- **Protokol UDP 41 Float:** Memperluas paket CSV UDP port `5052` dari 39 menjadi 41 float (`v[39] = is_fly_down_y`, `v[40] = is_fly_down_w`).
- **Arsitektur Konfigurasi Terpusat:** Parameter dan threshold dipusatkan di `DragonLocomotionConfig.cs` (`Assets/Settings/DragonLocomotionConfig.asset`) untuk Unity dan `cv_locomotion_config.json` untuk Python CV.
- **Rigging/Animasi Ditangguhkan:** Tidak ada perubahan rigging atau animasi sayap; interface `IFlightInputProvider` dan `DragonFlightController` tetap *decoupled* untuk integrasi animasi di masa mendatang.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset

### A. Python CV (`ARDragonSkyGuardian_CV/`)
- `pose_stabilizer.py`: Menambahkan perhitungan `left_arm_openness`, `right_arm_openness`, dan `upper_arm_openness` di `BiometricNormalizer.calculate_biometrics` yang dinormalisasi terhadap `sw_safe` dan `tl_safe`.
- `motion_recognizer.py`:
  - Menambahkan `UpperArmLocomotionGate` (`entry_away_threshold = 0.52`, `exit_away_threshold = 0.40`, `min_hold_sec = 0.12`, `min_confidence = 0.25`).
  - Memperketat `TuckDiveClassifier` dan memperbarui `GentleDescentClassifier`.
  - Menambahkan `FlyDownClassifier` dengan `GestureState.FLY_DOWN_Y` (`id = 7`) dan `GestureState.FLY_DOWN_W` (`id = 8`), validasi sudut siku (`>= 105°` untuk Pose Y, `10°–135°` untuk Pose W), dan temporal consistency (`0.14s`).
  - Menambahkan loader `_load_cv_locomotion_config()` dari `cv_locomotion_config.json`.
- `cv_locomotion_config.json` *(baru)*: Menyimpan threshold gate, tuck dive, gentle descent, dan fly down.
- `udp_sender.py` & `active_cv_sender.py`: Mengirimkan `confidences` ke `gesture_sm.update(...)` dan memperluas `send_pose()` ke 41 float (`v[39..40]`).

### B. Unity C# & Scene (`AR Dragon Sky Guardian/`)
- `IPoseProvider.cs` & `UDPPoseReceiver.cs`: Menambahkan `IsFlyDownY`, `IsFlyDownW`, parsing token `v[39..40]`, perbaikan `IsDiving => _isTuckDive`, serta perbaikan `wristSpeed` fallback agar hanya aktif saat `hasFlagsData == false` (mencegah flap palsu saat menaikkan/menurunkan lengan di ground).
- `IFlightInputProvider.cs` & `DefaultFlightInputMapper.cs`: Menambahkan `LeftUpperArmOpenness`, `RightUpperArmOpenness`, `IsFlyDownY`, `IsFlyDownW`, hysteresis arm gate (`0.52f` / `0.40f`, `0.12s`), `locomotionConfig`, serta mengatur `IsGentleDescent = true` saat tracking hilang agar di udara aman masuk ke Gentle Descent.
- `DragonLocomotionConfig.cs` *(baru)* & `Assets/Settings/DragonLocomotionConfig.asset` *(baru)*: ScriptableObject konfigurasi parameter darat, takeoff, udara, momentum, dan threshold gate (terpasang pada `Dragon_Player` di `Assets/_Scenes/Test_Dragon_Flight.unity`).
- `DragonFlightController.cs`:
  - Fase A: Spawn grounded di darat (`Pos=(0.00, 11.61, -220.00)`), tracking loss safe state.
  - Fase B: `HandleGroundedState()` walk (`walkSpeed = 3.5f`) vs stop (`groundFrictionRate = 6.0f`), Glide dengan peluruhan momentum (`glideMomentumDecayRate = 0.85f`), dan landing mulus `Air -> Grounded` (`landingThresholdOffset = 0.20f`) tanpa memotong momentum mendadak.
  - Fase C: Ground lean turn (`groundTurnSpeed = 65°/s`, rotasi di tempat saat IDLE), Ground Look Up (`groundLookUpMaxPitch = 25°`), Takeoff (`takeoffTargetHeight = 4.5f`, `takeoffFlapThreshold = 0.35`, `takeoffMinDuration = 0.25s`, `takeoffInitialClimbVel = 6.5f`, `takeoffInitialForwardSpeed = 7.0f`), pemisahan flapping tanpa tilt (`flapHoldLift = 1.2f`) vs `Fly Up`, serta `Fly Down Pose Y` (`-5.5 m/s` sink, `12.0 m/s` forward, `-20°` pitch) dan `Pose W` (`-3.2 m/s` sink, `0 m/s` forward, `-6°` pitch).
  - Fase D: Telemetri `ActiveFlightStateName`, `LastTransitionReason`, `CurrentPitchInput`, `LeftUpperArmOpenness`, `RightUpperArmOpenness`, dan `ApplyLocomotionConfig()`.
- `InGameFlightHUD.cs`: Menampilkan status `GROUNDED WALK`, `GROUNDED LOOK UP`, `FLY DOWN (Y/W)`, plus panel debug Fase D (`Arm Open L/R`, `Ground/Air -> Active State`, `Transisi`, `Turn / Pitch`, `Momentum`).

### C. Hasil Pengujian Nyata (Unity Play Mode via UDP `5052`)
Seluruh **16 skenario Fase D** terverifikasi lulus dengan `compilationFailed: false` dan `consoleErrors: 0`:
1. Start game: `Grounded=True`, `State="Grounded Idle"`, `Speed=0.00`, `Pos=(0.00, 11.61, -220.00)`.
2. Ground upper arms away: `State="Grounded Walk"`, `Speed=3.50`, `OpenLR=(0.75, 0.75)`.
3. Ground upper arms tucked: `State="Grounded Idle"`, `Speed=0.00`.
4. Ground lean saat IDLE: `Grounded=True`, `Speed=0.00`, `Yaw=129.9°`, `Turn=0.61` (rotasi di tempat).
5. Ground lean saat maju: `State="Grounded Walk"`, `Speed=3.50`, `Yaw=46.0°`, `Turn=-0.61`.
6. Ground tilt atas: `State="Grounded Look Up"`, `Speed=0.00`, `Pitch=21.2°` (tetap di darat).
7. Takeoff via flap: `Grounded=False`, naik ke `Pos=(0.00, 19.59, -165.02)`.
8. Flap + tilt atas: `State="Fly Up"`, `VertVel=10.75`, `Pitch=23.8°`.
9. Air Gentle Descent ke ground: dari `y=12.80` mendarat mulus ke `Grounded=True`, `Speed=0.00`, `Pos=(0.00, 11.46, -213.87)`.
10. Air Hover (tucked + forearm flap): `State="Hover"`, `Speed=0.00`, `VertVel=-0.23`.
11. Pose Y: `State="Fly Down (Y)"`, `Speed=12.00`, `VertVel=-5.50`, `Pitch=-20.0°`.
12. Pose W: `State="Fly Down Idle (W)"`, `Speed=0.00`, `VertVel=-3.20`, `Pitch=-6.0°`.
13. Air T-pose Glide: `State="Glide"`, `Speed=13.57`, `VertVel=-1.00`, tidak terbaca sebagai Dive.
14. Dive lalu Glide: Dive mencapai `Speed=28.00`, lalu Glide membawa & meluruhkan momentum (`Speed=14.75`).
15. Tracking hilang: Ground → `Grounded Idle` (`Speed=0.00`); Air → `Gentle Descent` (`Speed=8.50`, `VertVel=-1.60`).
16. Regresi: `Normal Flight` (`Speed=22.00`, `VertVel=1.10`) dan `Propeller Dash` (`Speed=26.00`, `VertVel=0.50`) tetap berfungsi.

---

## 4. Detail Penting yang Harus Diingat
- **Nilai Serialized Scene Override Default C#:** Mengubah nilai default `public float` di script C# tidak otomatis mengubah nilai yang sudah tersimpan pada `Dragon_Player` di `Test_Dragon_Flight.unity`; parameter scene harus diperbarui lewat Inspector / `script eval` + `scene.save`.
- **Auto-Launch Python CV saat Play Mode:** `UDPPoseReceiver` memiliki `autoLaunchPython = true` yang otomatis menyalakan `active_cv_sender.py`. Saat menjalankan test UDP sintetis di Play Mode, proses `active_cv_sender.py` harus dihentikan terlebih dahulu agar tidak menimpa paket test pada port `5052`.
- **Bug `wristSpeed` Fallback:** Pada `UDPPoseReceiver.cs`, fallback kecepatan pergelangan tangan (`wristSpeed > 0.8f`) tidak boleh aktif ketika `hasFlagsData == true`, karena gerakan tangan normal (mis. dari *tucked* ke *away*) bisa memicu `_flapTimer` palsu dan menyebabkan takeoff tidak sengaja.
- **Parameter Penting:** `groundContactHeight = 0.85`, `walkSpeed = 3.5`, `gentleDescentSpeed = 8.5`, `gentleDescentSinkRate = 1.6`, `cruiseSpeed = 14.0`, `glideMomentumDecayRate = 0.85`, `entryAwayThreshold = 0.52`, `exitAwayThreshold = 0.40`, `armGateMinHoldDuration = 0.12`.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] **Git Commit & Push Repo Utama (`AR-Dragon.git`):** Perubahan Fase A–D pada kode Unity, Python CV, scene, config, `prd.md`, dan `konteks.md` masih belum di-commit/push di repo utama (menunggu keputusan apakah commit khusus file Fase A–D saja atau beserta file aset lain di working tree).
- [ ] **Sesi GUI & Village Gameplay:** Melanjutkan integrasi Canvas Menu Lobby, `VillageZoneController.cs`, dan VFX 3 desa sesuai antrean di [[Tasks]].
- [ ] **Deferred — Dragon Animation & Pose Plan:** Menunggu keputusan pengguna setelah seluruh fitur gameplay utama siap.

**Navigasi Obsidian:** [[00 - Overview]] · [[konteks]] · [[prd]] · [[GDD_Pulau]] · [[progress]] · [[Tasks]] · [[Devlog]]
