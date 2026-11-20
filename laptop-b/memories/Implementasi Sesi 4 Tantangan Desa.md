---
title: "Implementasi Sesi 4 Tantangan Desa"
type: session-memory
tags:
  - session-memory
  - laptop-b
  - session-4
  - village-challenge
  - auto-hover
  - feature-to-ui-sync
date: 2026-11-19
archived: 2026-11-21
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[laptop-a/mekanisme_tantangan_desa]]"
  - "[[laptop-a/checklist_finalisasi_pra_sesi_4]]"
  - "[[laptop-a/kontrak_integrasi_ui_dan_progress]]"
  - "[[Integrasi Pulau v2 dan Sinkronisasi UI]]"
  - "[[Sistem Momentum Terpadu dan Revisi Kontrol]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
---

# Implementasi Sesi 4 Tantangan Desa

## 1. Tujuan & Konteks Sesi
- Mengimplementasikan **Sesi 4 (Mekanisme Tantangan 3 Desa — Zephyria, Pyrewood, Solaria)** di `MainIsland_Gameplay.unity` beserta **Feature-to-UI Sync** penuh sesuai `[[laptop-a/mekanisme_tantangan_desa]]` dan `[[laptop-a/kontrak_integrasi_ui_dan_progress]]`.
- Merakit zona marker silinder, sub-marker 2 tahap Pyrewood (Danau Zephyria & Hutan Terbakar), billboard `WorldSpaceVillageMarker`, sistem **Auto-Hover + Escape Engine + Launch Thrust**, isolasi UI saat narasi (`PF_QuestNarrativeBox`), serta perbaikan transisi pasca-restorasi, bug kunci belok kanan CV, dan pembagian stage `Lvl 1/2/3 = 1/2/3 Desa`.

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Zona Silinder 3 Desa & World-Space Billboard (`WorldSpaceVillageMarker.cs`, `Session4_VillageBuilder.cs`):**
   - **Desa Zephyria (Biome 1):** Pad Narasi `(-562.40, 14.10, 274.30)` (`R=35m, Y<=40m`) & Zona Tantangan Kabut `(-562.40, 39.10, 274.30)` (`R=48m`).
   - **Desa Pyrewood (Biome 2):** Pad Narasi `(739.60, 12.90, 7.80)` (`R=35m, Y<=40m`), Sub-Marker 1 Danau Zephyria `(-292.27, 13.62, 663.83)` (`R=45m`), & Sub-Marker 2 Hutan Terbakar `(797.77, 13.68, 147.97)` (`R=50m`).
   - **Desa Solaria (Biome 3):** Pad Narasi `(39.40, 14.70, -647.60)` (`R=35m, Y<=40m`) & Zona Tantangan Sky Altar `(39.40, 54.70, -647.60)` (`R=48m`).
   - `WorldSpaceVillageMarker.cs` tampil pada `Y+12m` saat jarak `<= 150m`, menghadap `Camera.main.transform.rotation` dengan teks `"<LOKASI> · XXm"`.
2. **Auto-Hover, Escape Engine, & Launch Thrust Grace (`DragonFlightController.cs`, `DragonWingAnimator.cs`):**
   - `EnterAutoHover(anchorPos, anchorRot, isZephyriaChallenge)` mengunci naga mulus (`smoothSpeed = 3.0f`) dengan `wingRegime == "Hover"`.
   - **Escape Engine:** gestur `#06 Flap`, `#13 Tuck Dive`, atau `#14 Propeller Dash` membatalkan auto-hover, **kecuali** di Zona Tantangan Zephyria di mana `#06 Flap` adalah gestur tantangan sehingga hanya `#13` / `#14` yang memicu escape.
   - `ExitAutoHover(true)` memberikan **Launch Thrust** (`autoHoverLaunchMomentum = 0.35f`, `autoHoverLaunchSpeed = 17.5f` m/s) beserta `_launchThrustGraceTimer = 0.60f` agar penurunan lengan sehabis tantangan tidak langsung mengerem naga di dalam silinder.
3. **State Mesin Desa, Isolasi UI Narasi, & Tantangan No-Decay (`VillageZoneController.cs`, `InGameFlightHUD.cs`):**
   - State mesin 5 tahap: `Unvisited` $\rightarrow$ `NarrativeActive` $\rightarrow$ `QuestAccepted` $\rightarrow$ `ChallengeActive` $\rightarrow$ `Restored` (dengan guard `state == Restored` dan cooldown wajib keluar silinder + `3.5s`).
   - Saat `NarrativeActive`, `InGameFlightHUD.SetNarrativeIsolationMode(true)` menyembunyikan dock HUD/PIP/kompas sehingga layar hanya menampilkan `PF_QuestNarrativeBox` (`SCREEN 08` auto-play 3 baris) dan `QuestObjectivePanel`.
   - **Progres No-Decay:** Zephyria (`+0.11f/flap`), Pyrewood Tahap 1 Danau (`+0.22f/s` auto-fill + Auto-Release 100%), Pyrewood Tahap 2 Hutan (`MOUTH_OPEN > 0.35` aktif / `< 0.25` mati, `+0.15f/s`), dan Solaria Sky Altar (Double I-Pose `11` `#15C`, `+0.125f/s`).
4. **Perbaikan Stabilitas Lintas Scene & Kalibrasi CV (`UDPPoseReceiver.cs`, `player_calibrator.py`, `DragonGameSettings.cs`):**
   - Mengaktifkan `ExclusiveAddressUse = false` & `SocketOptionName.ReuseAddress` sebelum `Bind(5052)` di `UDPPoseReceiver.cs`.
   - Menambahkan validasi profil kalibrasi `AdaptiveThresholdEngine.is_profile_valid()` (`shoulder_width >= 0.07`, `torso_length >= 0.11`, `|neutral_lean_bias_deg| <= 12.0°`, clamp runtime `[-8°, +8°]`) serta syarat confidence kedua pergelangan `>= 0.20` untuk `arm_tilt_norm` guna mencegah bug terkunci belok kanan akibat `player_profile.json` korup.
   - Menetapkan default stage ke Stage `1` (`GetActiveStageIndexOrDefault(1)`, `defaultStageIfUnset = 1`, dan `ResetStaticSessionState()` pada `SubsystemRegistration`) sehingga pembagian desa per level tegas: `Stage 1 = 1 Desa (Zephyria)`, `Stage 2 = 2 Desa (Zephyria + Pyrewood)`, `Stage 3 = 3 Desa`.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Unity Gameplay, UI & Editor (`AR Dragon Sky Guardian/`):**
  - `Assets/_Scripts/Gameplay/VillageZoneController.cs` *(Baru)*, `Assets/_Scripts/Gameplay/WorldSpaceVillageMarker.cs` *(Baru)*.
  - `Assets/_Scripts/Controllers/DragonFlightController.cs`, `DragonWingAnimator.cs`, `Assets/_Scripts/CV_Receiver/UDPPoseReceiver.cs`.
  - `Assets/_Scripts/UI/InGameFlightHUD.cs`, `MissionsPanelUI.cs`, `FinishModalUI.cs`, `CalibrationPanelUI.cs`, `DragonGameSettings.cs`.
  - `Assets/Editor/Session4_VillageBuilder.cs` *(Baru)*, `Assets/Editor/Session4VerificationRunner.cs` *(Baru)*, `Assets/Editor/UISceneAndPrefabBuilder.cs`.
  - Scene diperbarui: `Assets/_Scenes/MainIsland_Gameplay.unity` dan `Assets/_Scenes/MainMenu_Lobby.unity`.
- **Python CV (`ARDragonSkyGuardian_CV/`):**
  - `player_calibrator.py`, `motion_recognizer.py`, `active_cv_sender.py`, dan penghapusan `player_profile.json` korup.
- **Hasil Verifikasi (`Unity 6000.3.7f1`):**
  - `Session4VerificationRunner.RunPlayModeVerificationAsync()`: **`100% PASS`** (marker `<=150m` & billboard, isolasi UI narasi, escape reset baris 1, cooldown keluar silinder + `3.5s`, Zephyria Flap no-escape & No-Decay, TuckDive escape + Launch Thrust, Danau auto-fill & Auto-Release, Hutan hysteresis `0.35/0.25`, Solaria pose `11`, guard `Restored`, & pembagian Stage 1/2/3).
  - `MomentumFlightVerificationRunner.RunPlayModeVerificationAsync()`: **`100% PASS`** (`0` compile/runtime errors).

---

## 4. Detail Penting yang Harus Diingat
- **Grace Timer Launch Thrust (`_launchThrustGraceTimer = 0.60f`):** Mencegah rem pasif `#09A Hover` / `#12 FlyDownW` saat pemain menurunkan lengan tepat setelah merampungkan 10 kepakan Zephyria atau keluar dari Danau Pyrewood.
- **Socket UDP `5052` Reuse:** Selalu pertahankan `SocketOptionName.ReuseAddress` dan `Join(200)` di `UDPPoseReceiver` agar perpindahan scene `MainMenu_Lobby` $\leftrightarrow$ `MainIsland_Gameplay` tidak gagal bind port `5052`.
- **Batas Aman Profil Kalibrasi CV:** `neutral_lean_bias_deg` di luar `[-12°, +12°]` atau `shoulder_width < 0.07` wajib ditolak oleh `load_profile()` dan `_finalize_calibration()` agar tidak menimbulkan drift kemudi permanen.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- Lanjut ke **Sesi 4B (Paket Visual VFX Effekseer URP, Dynamic FOV 60°–85°, & Skybox/Fog Dinamis 3 Biome)** serta **Sesi 5 (Game Loop `GameManager`, Scoring Bintang Global, Audio, & Build)** — lihat [[Implementasi Sesi 4B VFX dan Atmosfer]] dan [[Implementasi Sesi 5 Game Loop dan Scoring]].

---

**Navigasi Obsidian:** [[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[Sistem Momentum Terpadu dan Revisi Kontrol]] · [[laptop-a/mekanisme_tantangan_desa]]
