---
title: Implementasi Kontrol Halus, Ground Lockout, Escape Desa, & Momentum v5.0
type: session-memory
tags:
  - session-memory
  - laptop-b
  - flight-physics
  - momentum
  - steering
  - ground-mode
  - village-escape
  - settings-ui
date: 2026-11-26
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[laptop-a/gameplay-mechanics/spesifikasi_kecepatan_momentum_v5_dan_kontrol]]"
---

# Implementasi Kontrol Halus, Ground Lockout, Escape Desa, & Momentum v5.0

## 1. Tujuan & Konteks Sesi
Mengimplementasikan seluruh pembaruan kontrol, ground mode, aturan escape desa, perbaikan Render Graph Effekseer, dan profil kecepatan & momentum v5.0 (`16–150 m/s`) mengacu dokumen SSOT Laptop A [[laptop-a/gameplay-mechanics/spesifikasi_kecepatan_momentum_v5_dan_kontrol]].

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Kemudi Belok Halus Anti Double-Curve:**
   - `DefaultFlightInputMapper` menyimpan `RawTurnInput` + `HasRawTurnInput` di `FlightCommand` dan mengevaluasi kurva `EvaluateSteeringCurve(r, 0.08f, 1.35f)` untuk kebutuhan preview kalibrasi mandiri.
   - `DragonFlightController.FetchFlightCommand()` menggunakan `RawTurnInput` (saat `HasRawTurnInput == true`) untuk menerapkan deadzone tunggal `steeringDeadzone = 0.08f`, kurva non-linier `Mathf.Sign(r) * Mathf.Pow((Mathf.Abs(r) - 0.08f) / (1.0f - 0.08f), 1.35f)`, pengali `DragonGameSettings.SteeringSensitivity` (`0.5x–2.0x`), serta menaikkan ambang *Wing Overdrive Steer* ke `wingOverdriveSteerThreshold = 0.22f`.
2. **Toggle Steep Bouncy Collision (`ON/OFF`):**
   - `DragonGameSettings.SteepBounceEnabled` (`Fanamir_SteepBounceEnabled`, default `1`) menentukan perilaku saat menabrak permukaan `normal.y <= bounceMaxNormalY`:
     - `ON`: memanggil `ApplyGlancingBounce()` dengan penalti momentum `-10%` (`Mathf.Max(0f, _currentMomentum - 0.10f)`) dan pantulan `+15°`.
     - `OFF`: memanggil `ApplySmoothSteepSlide()` menggunakan `Vector3.ProjectOnPlane` menyusuri dinding tanpa gaya tolak mendadak, tanpa penalti momentum, dan tanpa guncangan kamera.
3. **Ground Mode, Takeoff 3 Kepakan, & Air Pose Lockout:**
   - Takeoff dari darat mewajibkan `requiredTakeoffFlaps = 3` kepakan (`FlapPower >= 0.28f`) dalam `takeoffFlapWindow = 2.5f` detik; jeda antarkepakan di mana `HasPoseInput == false` tidak mereset `_takeoffFlapCounter` selama `_isTrackingReady == true` dan jendela `2.5s` masih aktif. Kepakan ke-3 langsung memberikan `+10%` momentum instan.
   - Seluruh pose udara (`#07`, `#13`, `#14`, `#09A/#09B`, `#10`, `#11`, `#12`) diblokir total saat `_isGrounded == true` baik pada `DragonFlightController` maupun `DragonWingAnimator` (menghapus ground dash sprint sebelumnya).
   - Ground Walk (`3.5 m/s`) dipicu konsisten oleh T-Pose (`#08 Glide`) atau `UpperArmOpenness >= 0.35f` / `IsUpperArmsAway`, dan kembali diam (`0 m/s`) bila lengan dirapatkan (`UpperArmOpenness < 0.25f`).
   - Pendaratan datar dengan pose Glide `#08` langsung bertransisi ke `Grounded Walk` (`3.5 m/s`), sedangkan pendaratan kasual meluncur melambat (`_casualLandingTimer = 3.0f`) hingga `0 m/s` & `0%` momentum dalam `3.0` detik.
4. **Standarisasi Escape Desa & Dynamic Launch Thrust:**
   - **Zephyria (Narasi & Tantangan):** kepakan sayap tidak pernah membatalkan auto-hover; satu-satunya escape adalah **Tuck Dive (`#13`)** ditahan `>= 0.6s` (`zephyriaEscapeTuckHoldDuration`).
   - **Pyrewood & Solaria (Narasi & Tantangan):** wajib `3x` kepakan sayap dalam jendela `2.5s` (`requiredEscapeFlaps = 3`).
   - `ExitAutoHover(true)` menghitung kecepatan awal dinamis dari sisa `_currentMomentum` dan pose aktif pemain (`TuckDive`, `PropellerDash`, `Flap`, `FlyUp`, atau `Glide`).
5. **Profil Kecepatan & Momentum v5.0 + Render Graph Fix:**
   - Menyelaraskan parameter default C#, `DragonLocomotionConfig.asset` (ditambah properti kecepatan/kontrol v5.0 agar menimpa nilai lama scene YAML saat `Awake()`), serta `EffekseerSettings.asset` (`enableURPRasterPass: 1`).

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- `AR Dragon Sky Guardian/Assets/_Scripts/Controllers/IFlightInputProvider.cs` — tambah `RawTurnInput` & `HasRawTurnInput`.
- `AR Dragon Sky Guardian/Assets/_Scripts/Controllers/DefaultFlightInputMapper.cs` — tambah `steeringDeadzone = 0.08f`, `EvaluateSteeringCurve`, dan konsistensi `UpperArmOpenness >= 0.35f` / `IsTPose`.
- `AR Dragon Sky Guardian/Assets/_Scripts/Controllers/DragonFlightController.cs` — kemudi halus `±0.08` + `SteeringSensitivity`, `wingOverdriveSteerThreshold = 0.22f`, toggle `SteepBounceEnabled` (`-10%` bounce vs smooth slide), 3x flap takeoff (`+10%`), Air Pose Lockout, Ground Walk konsisten, Glide-to-Walk & 3s casual landing, aturan escape desa 3x flap vs 0.6s Tuck Dive, serta profil kecepatan & momentum v5.0 (`16–150 m/s`).
- `AR Dragon Sky Guardian/Assets/_Scripts/Controllers/DragonWingAnimator.cs` — blokir `PropellerDash` di `ClassifyPose()` saat `isGrounded == true`.
- `AR Dragon Sky Guardian/Assets/_Scripts/Controllers/DragonLocomotionConfig.cs` & `Assets/Settings/DragonLocomotionConfig.asset` — pembaruan penuh parameter v5.0.
- `AR Dragon Sky Guardian/Assets/_Scripts/Gameplay/VillageZoneController.cs` — isolasi aturan escape Zephyria vs Pyrewood/Solaria pada narasi/tantangan & pembaruan teks petunjuk escape UI.
- `AR Dragon Sky Guardian/Assets/_Scripts/UI/DragonGameSettings.cs`, `SettingsPanelUI.cs`, & `Assets/Editor/UISceneAndPrefabBuilder.cs` — tambah `SteeringSensitivity` (`Row_SteeringSensitivity`) & `SteepBounceEnabled` (`Row_SteepBounceToggle`) serta `BuildAll()` prefab/scene UI (`SettingsCard` `960×820 px`).
- `AR Dragon Sky Guardian/Assets/Resources/EffekseerSettings.asset` — set `enableURPRasterPass: 1`.
- `AR Dragon Sky Guardian/Assets/Editor/MomentumFlightVerificationRunner.cs` & `Session4VerificationRunner.cs` — pembaruan dan verifikasi Play Mode v5.0.

## 4. Detail Penting yang Harus Diingat
- `MainIsland_Gameplay.unity` menyimpan nilai serialized lama (`maxDiveSpeed: 28`, `maxDashSpeed: 30`) di komponen scene; `DragonLocomotionConfig.asset` + `ApplyLocomotionConfig()` di `Awake()` memastikan nilai v5.0 (`100` / `150 m/s`, `0.08` deadzone, `0.22` overdrive, `3` takeoff/escape flaps, `0.6s` Zephyria tuck hold) selalu aktif di runtime.
- `BootFlowController` menonaktifkan `TabContentRoot` selama Splash/Start Screen sehingga `SettingsPanelUI.Awake()` baru berjalan saat Lobby aktif; seluruh kontrol Settings terverifikasi bekerja saat `activeInHierarchy == true`.
- Verifikasi Play Mode lulus 100%:
  - `MainMenu_Lobby.unity`: `SteeringSlider=0.5..2 (val=1.6x)`, `SteepBounceToggle=ON <-> OFF`.
  - `MainIsland_Gameplay.unity`: `MomentumFlightVerificationRunner` (`ALL MOMENTUM v5.0 & FLIGHT PHYSICS CHECKS PASSED (100%)`) dan `Session4VerificationRunner` (`ALL SESSION 4 VILLAGE, SUB-MARKER, AUTO-HOVER, ESCAPE, BILLBOARD & UI ISOLATION CHECKS PASSED (100%)`), dengan `0` console errors & `0` console warnings.
- Scene aktif dikembalikan ke `MainMenu_Lobby.unity` (Build Index `0`, `isDirty: false`).

## 5. Tugas Tersisa & Langkah Selanjutnya
- Uji coba langsung dengan webcam live (`active_cv_sender.py`) untuk merasakan kenyamanan kemudi deadzone `±0.08`, 3x takeoff flap, dan Boost `150 m/s`.
- Lanjut ke build Windows standalone (`MainMenu_Lobby`, `MainIsland_Gameplay`, `Test_Dragon_Flight`).

---
Navigasi: [[00 - Overview]] | [[Tasks]] | [[Devlog]]
