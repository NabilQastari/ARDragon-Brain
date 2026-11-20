---
title: "Sistem Momentum Terpadu dan Revisi Kontrol"
type: session-memory
tags:
  - session-memory
  - laptop-b
  - momentum
  - flight-physics
  - computer-vision
  - hud
  - pose-guide
date: 2026-11-19
archived: 2026-11-21
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[laptop-a/planning_momentum_dan_revisi_kontrol]]"
  - "[[Penyelarasan 17 Pose dan Propeller Dash]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
---

# Sistem Momentum Terpadu dan Revisi Kontrol

## 1. Tujuan & Konteks Sesi
- Mengimplementasikan **Sistem Momentum Kontinu (`0–100%`)** dan revisi kontrol terbang Fanamir (`v4.0`) sesuai `[[laptop-a/planning_momentum_dan_revisi_kontrol]]`.
- Memasang paket delta aset bersih `Export_Delta_LaptopB/` (`28` file): mengganti `17` kartu pose berkerangka CV dengan **`15` ikon medali siluet bersih** (`UI_Pose_01..15*.png`), memasang bar momentum HUD (`UI_HUD_Momentum_Track_206x20.png` & `UI_HUD_Momentum_Fill_200x14.png`), serta memperbarui `SCREEN 01` dan `SCREEN 07` (`3×5` 15 kartu utama).
- Memisahkan **`#09A Hover Active`** (L-Pose + kepakan cepat menahan udara / air brake) vs **`#09B Slow Glide`** (L-Pose statis meluncur lambat) dan **`#06B Steep Climb Flap`** (Flap + dongak kepala tanpa butuh momentum) vs **`#07 Fly Up`** (Open Y-Pose zoom climb berbasis momentum).

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Model Momentum *Soft-Cap Gradual Builder* (`DragonFlightController.cs`, `DragonLocomotionConfig.cs`):**
   - `CurrentMomentum` (`0..1`) menggunakan `AddMomentumSoftCapped(delta, cap)` sehingga aksi ber-cap rendah tidak memangkas momentum yang sudah lebih tinggi (`> cap`).
   - **Pembangun Momentum:** `#04 Takeoff` (`0.15f`), `#06 Flap` (`+0.06f/flap`, soft-cap `0.30f`), `#06B Steep Climb Flap` (`HeadPitch > 0.12f` + Flap: `+0.05f/flap`, cap `0.20f`, naik `+6.5..+8.0 m/s` pada pitch `~+24°` tanpa syarat momentum), `#11 Y-Dive` (`+0.12f/s`, cap `0.55f`), `#13 Tuck Dive` (`+0.16f/s` $\rightarrow$ `1.0f`), dan `#14 Propeller Dash` (`+0.07f/360°`, cap rate `~0.18f/s` $\rightarrow$ `1.0f`).
   - **Konsumen & Pengereman Momentum:** `#08 Glide` (`14..27 m/s`, drag `-0.035f/s`), `#07 Fly Up` (hanya dipicu Open Y-Pose; saat `CurrentMomentum > 0` melakukan zoom climb `+7.0..+9.5 m/s` dengan drain `-0.25f/s`, saat `0%` masuk *gentle stall* `-1.2 m/s`), `#09A Hover Active` (air brake `-0.40f/s`, naga tetap melaju melambat selama momentum `> 0` hingga berhenti `0 m/s`), `#09B Slow Glide` (`~7 m/s`, `-0.6 m/s`, drag `-0.10f/s`), `#10 Gentle Descent` (`-0.20f/s`), `#12 W-Pose` (`-0.40f/s`), Auto-Hover desa (`-0.85f/s`), dan Grounded reset (`0f`).
2. **Pemisahan Classifier CV (`motion_recognizer.py`):**
   - `HoverClassifier` memisahkan `#09A Hover Active` (`wrist_vert_speed >= 0.25`, `hover_power = 0.65..1.0`, hysteresis exit `< 0.20` dengan jendela `0.28s`) dari `#09B Slow Glide` (`< 0.20`, `hover_power = 0.25`), mengekspos `is_hover_flapping` & `is_slow_glide` tanpa mengubah kompatibilitas protokol UDP 44-token.
   - Menjaga `is_flapping = False` saat menahan Open Y-Pose `#07` dan menstabilkan `flap_frequency` sejak awal stroke di `FlapStrokeClassifier`.
3. **Animasi 12 Tulang Sayap & HUD `MomentumDock` (`DragonWingAnimator.cs`, `InGameFlightHUD.cs`):**
   - `DragonWingAnimator.cs` membedakan regime `FlyUp` (12 tulang sayap membentuk siluet **Y**), `SteepClimbFlap`, `Hover` (`#09A`), dan `SlowGlide` (`#09B` sayap setengah tertekuk kompak).
   - `InGameFlightHUD.cs` memasang `MomentumDock` (`420×40`) tepat di atas `TelemetryDock` dengan metode `SetMomentum(float, bool)` yang menampilkan badge `"{pct}% · Y-OK"` atau `"0% · EMPTY"`.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Aset & Dokumentasi (`Export_Delta_LaptopB/`):**
  - `AR Dragon Sky Guardian/Assets/UI/Components/05_HUD/UI_HUD_Momentum_Track_206x20.png` & `UI_HUD_Momentum_Fill_200x14.png`.
  - `AR Dragon Sky Guardian/Assets/UI/Components/06_Poses/UI_Pose_01..15*.png` (`15` ikon medali bersih).
  - Pembaruan `docs/UI_Designs_Reference/`, `assets/poses_cv_vector/` (`Pose_06B`, `Pose_09`, `Pose_09B`, `All_19_Poses_Sheet.png`), serta `Panduan_19_Pose_Fanamir_CV.pdf`/`.docx`.
- **Python CV (`ARDragonSkyGuardian_CV/`):**
  - `motion_recognizer.py`, `test_all_17_poses_alignment.py`, dan `test_momentum_and_control_revision.py` *(Baru)*.
  - **Verifikasi Python CV:** `10/10` suite lulus 100% (nol regresi pada 6 pose terkunci `#01, #02, #04, #05, #08, #13`).
- **Unity C#, Editor & Scene (`AR Dragon Sky Guardian/`):**
  - `DragonFlightController.cs`, `DragonLocomotionConfig.cs`, `DragonLocomotionConfig.asset`, `DragonWingAnimator.cs`, `IPoseProvider.cs`, `UDPPoseReceiver.cs`, `IFlightInputProvider.cs`, `DefaultFlightInputMapper.cs`.
  - `UISpriteSetupUtility.cs` (`142` PNG, `76` border, `15/15` ikon pose), `InGameFlightHUD.cs`, `PoseGuideCatalog.cs`, `PoseGuideOverlayUI.cs`, `CalibrationPanelUI.cs`, `UISceneAndPrefabBuilder.cs`, dan `MomentumFlightVerificationRunner.cs` *(Baru)*.
  - **Verifikasi Play Mode Unity (`0` errors, `0` warnings):** `MomentumFlightVerificationRunner.RunPlayModeVerificationAsync()` lulus 100% di `MainIsland_Gameplay.unity` dan `Test_Dragon_Flight.unity` (`Idle=0.00`, `Flap=0.14->0.30`, `Steep06B=vy 8.00`, `DiveFull=1.00`, `NoTrunc=0.99`, `GlideSpd=26.7 m/s`, `YClimb=vy 8.33`, `YStall=vy -1.01`, `SlowGlide09B`, `Hover09A`), serta `MainMenu_Lobby.unity` (`SCREEN 07` grid `3×5` 15 kartu bersih).

---

## 4. Detail Penting yang Harus Diingat
- **Aturan Anti-Pemangkasan Momentum (`AddMomentumSoftCapped`):** Ketika `CurrentMomentum > cap` (misal habis menukik `#13` di `99%` lalu mengepak `#06` yang ber-cap `30%`), momentum **tidak boleh** dipotong turun ke `30%`.
- **Syarat `#07 Fly Up` vs `#06B Steep Climb Flap`:** `#07` hanya aktif dari Open Y-Pose dan wajib punya `CurrentMomentum > 0` untuk naik (`zoom climb`); jika momentum `0%`, pemain naik menggunakan `#06B` (Flap sambil mendongak `HeadPitch > 0.12f`).
- **Perilaku `#09A Hover Active`:** Saat naga masih memiliki momentum tinggi, masuk ke `#09A` tidak menghentikan naga secara mendadak melainkan mengerem udara (`-0.40f/s`) hingga momentum habis (`0 m/s`).

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- Lanjut ke **Sesi 4 (Mekanisme Tantangan 3 Desa & Auto-Hover)**, **Sesi 4B (Paket Visual VFX Effekseer, Dynamic FOV, & Skybox Dinamis)**, dan **Sesi 5 (Game Loop, Scoring Bintang, Audio, & Build)** — lihat [[Implementasi Sesi 4 Tantangan Desa]], [[Implementasi Sesi 4B VFX dan Atmosfer]], serta [[Implementasi Sesi 5 Game Loop dan Scoring]].

---

**Navigasi Obsidian:** [[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[Penyelarasan 17 Pose dan Propeller Dash]] · [[laptop-a/planning_momentum_dan_revisi_kontrol]]
