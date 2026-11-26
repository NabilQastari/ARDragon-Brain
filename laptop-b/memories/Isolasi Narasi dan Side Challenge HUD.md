---
title: "Isolasi Narasi dan Side Challenge HUD"
type: session-memory
tags:
  - session-memory
  - laptop-b
  - ui-ux
  - in-game-hud
  - quest-narrative
  - village-challenge
date: 2026-11-26
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[Implementasi Sesi 4 Tantangan Desa]]"
  - "[[Implementasi Sesi 5 Game Loop dan Scoring]]"
  - "[[laptop-a/ui-ux/planning_perbaikan_ui_ingame_dan_event_desa]]"
---

# Isolasi Narasi dan Side Challenge HUD

## 1. Tujuan & Konteks Sesi
- Mengimplementasikan perbaikan UI in-game di Unity 6 (`6000.3.7f1`) sesuai dokumen desain resmi `[[laptop-a/ui-ux/planning_perbaikan_ui_ingame_dan_event_desa]]`:
  1. **Full Narrative Isolation Mode & Textbox Narasi Besar (`PF_QuestNarrativeBox.prefab`):** Mengisolasi seluruh elemen HUD umum ketika dialog narasi desa muncul agar hanya menyisakan `QuestObjectivePanel` dan `PF_QuestNarrativeBox`, serta memperbesar kotak narasi dan tipografi untuk keterbacaan *exergaming* jarak jauh (`1.5–2.5 m`).
  2. **Redesain Side-Docked Challenge HUD (`PF_VillageChallengeOverlay.prefab`):** Menghapus modal perkamen raksasa `720×670 px` di tengah layar yang menutupi naga Fanamir 3D dan menggantinya menjadi panel samping kanan ramping (`400×620 px`) berisi header desa/krisis, kartu indikator pose target + badge *live feedback*, progress bar pemulihan No-Decay, dan hint escape, sambil menyembunyikan HUD telemetri/vitality/PIP yang tidak relevan.

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Perbesaran & Tata Letak `PF_QuestNarrativeBox.prefab` (`UISceneAndPrefabBuilder.BuildQuestNarrativePrefab`):**
   - Root diubah ke anchor *Bottom-Center* `(0.5, 0)`, `anchoredPosition = (0, 36)`, `sizeDelta = (1440, 300)` (`UI_Panel_NarrativeBox_9Slice.png`).
   - `SpeakerBanner` (`UI_Banner_SpeakerTag_Blank.png`): `420 × 52 px` di `(38, -4)` overlap tepi atas kiri, font `Fredoka-Bold SDF` (`28 pt`, auto-size `18..28`).
   - `EmblemCard` (`UI_Illust_Frame_Dark_9Slice.png`): `160 × 180 px` di `(36, -56)` berisi ikon biome `96 × 96 px` dan `VillageEmblemText` (`Fredoka-SemiBold SDF 18 pt`).
   - Kolom tengah (`x = 220, lebar = 880 px`): `HeadlineText` (`Fredoka-Bold SDF 21 pt`), `NarrativeBodyText` (`880 × 134 px`, `Nunito-ExtraBold SDF 34 pt`, auto-size `24..34`, `lineSpacing = 20f` = `1.2×` di TMP karena `currentEmScale = fontSize * 0.01f`), dan `WhatToDoStrip` (`880 × 58 px`, soket `46 × 46 px`, `InstructionText` `Fredoka-Bold SDF 26 pt`).
   - Kolom kanan (`lebar = 288 px, x = -28`): `LineCounterPill` (`02_Tags/UI_Tag_Stage_Gold_Blank.png`, `288 × 44 px`, `Fredoka-Bold SDF 20 pt`) dan `AutoPlayWidget` (`288 × 186 px`, `CountdownTimerText` `Fredoka-Bold SDF 24 pt`, `CountdownTrack` `264 × 20 px` dengan `CountdownFill` offset `3 px` sehingga tinggi bar pengisi tepat `14 px`).
2. **Isolasi Total Narasi & Tantangan Samping di `InGameFlightHUD.cs`:**
   - Menambahkan state `_isVillageChallengeMode` (`IsVillageChallengeMode`) serta fallback `ResolveDependencies()` untuk `pipCameraControlsCard`, `waypointCompassRoot`, `questObjectiveRoot`, dan `escPauseButton`.
   - `UpdateWaypointCompass()` ikut menahan `waypointCompassRoot` saat `_isNarrativeIsolated || _isVillageChallengeMode`.
   - **Tiga mode visibilitas terpusat:**
     - `SetNarrativeIsolationMode(bool active)`: saat `true`, menyembunyikan `missionCapsuleRoot`, `escPauseButton`, `vitalityBarRoot`, `telemetryDockRoot`, `momentumDockRoot`, `pipCameraControlsCard`, `pipPanelRoot`, `waypointCompassRoot`, dan `villageChallengeUI`; memastikan hanya `questObjectiveRoot` dan `PF_QuestNarrativeBox` yang tampil.
     - `SetVillageChallengeMode(bool active)`: saat `true`, menyembunyikan `vitalityBarRoot`, `telemetryDockRoot`, `momentumDockRoot`, `pipCameraControlsCard`, `pipPanelRoot`, dan `waypointCompassRoot`; mempertahankan `missionCapsuleRoot`, `escPauseButton`, dan `questObjectiveRoot`, serta mengaktifkan `villageChallengeUI`.
     - `RestoreNormalFlightHUD()`: mengembalikan HUD penerbangan normal (`HideInGameCameraControls()` tetap menjaga `pipCameraControlsCard` nonaktif sesuai Fokus 1), menonaktifkan `villageChallengeUI` & `questNarrativeUI`, lalu memanggil `UpdateWaypointCompass()`.
3. **Redesain `PF_VillageChallengeOverlay.prefab` & `VillageChallengeUI.cs`:**
   - Root diubah ke anchor *Middle-Right* `(1.0, 0.5)`, `anchoredPosition = (-30, 0)`, `sizeDelta = (400, 620)` (`UI_Panel_Parchment_Calib_9Slice.png`).
   - **Header (`HeaderBanner` + `CrisisSubtitle`):** `UI_Header_Bar_Parchment_9Slice.png` memiliki lip bawah bertingkat tebal yang sempat menabrak teks dua baris di uji visual awal, sehingga diganti memakai `02_Banners/UI_Banner_Stepped_SubPanel_Blank.png` (`360 × 48 px` di `y = -26`) untuk `VillageTitle` (`Fredoka-Bold SDF 22 pt`) dengan `CrisisSubtitle` (`Nunito-ExtraBold SDF 15 pt`) tepat di bawahnya (`y = -78`, `360 × 22 px`).
   - **Kartu Pose (`PoseIndicatorCard`, `360 × 220 px` di `y = -102`):** `UI_Plaque_PoseCard_Active_9Slice.png`, `PoseIcon` (`84 × 84 px`), `PoseCommandText` (`Fredoka-Bold SDF 28 pt`), `PoseHintText` (`Nunito-Bold SDF 15.5 pt`), dan dua badge *live feedback* (`320 × 36 px` di `y = -172`): `FeedbackBadgeActive` (`UI_Badge_Cam_Live_Emerald_9Slice.png`, teks `"GERAKAN TERDETEKSI!"` + animasi pulse skala `±3%`) dan `FeedbackBadgeWaiting` (`UI_Badge_Cam_Standby_Amber_9Slice.png`, teks `"MENUNGGU GERAKAN..."`).
   - **Progress Bar & Escape Hint:** `ProgressTrack` (`360 × 48 px` di `y = -386`) dengan `ProgressFill` dihaluskan lewat `Mathf.SmoothDamp` (`smoothTime = 0.12f`), `ProgressNumeric` (`Fredoka-Bold SDF 22 pt` di `y = -446`), dan `EscapeHintChip` (`360 × 38 px` di bottom `y = 28`, `Fredoka-SemiBold SDF 15 pt`).
   - Mempertahankan wrapper `ShowChallenge(...)`, `UpdateProgress(float)`, dan `HideChallenge()` di samping `ShowSideChallenge(...)`, `UpdateProgress(float, string)`, `SetGestureFeedback(bool)`, dan `HideSideChallenge()` demi kompatibilitas pemanggil lama.
4. **Sinkronisasi `VillageZoneController.cs` & Idempotensi Builder:**
   - Menambahkan `HideAllWorldMarkers()` saat masuk `NarrativeActive` maupun `ChallengeActive` (dipulihkan lagi lewat `SyncWorldMarkers()` saat keluar/selesai).
   - Menghubungkan `EnterChallengeSession`, `UpdateZephyriaChallenge`, `UpdatePyrewoodLake`, `UpdatePyrewoodForest`, dan `UpdateSolariaChallenge` ke `hud.SetVillageChallengeMode(true)`, `challengeUI.ShowSideChallenge(...)`, `challengeUI.SetGestureFeedback(...)`, dan `challengeUI.UpdateProgress(progress, label)` (dengan format `$"{flaps} / 10 KEPAKAN ({pct}%)"`, `"{pct}% TERISI"`, `"{pct}% PADAM"`, `"{pct}% PULIH"`).
   - Pada `UISceneAndPrefabBuilder.UpgradeGameplayScene()`, ditambahkan re-wiring otomatis `VillageZoneController` (`hud`, `narrativeUI`, `challengeUI`, `finishModal`) dan `GameManager` (`hud`) agar `BuildAll()` tidak memutus referensi di `MainIsland_Gameplay.unity`. `Session4_VillageBuilder.EnsureHudIsolationRefsWired()` juga dilengkapi `pipCameraControlsCard`, `waypointCompassRoot`, `questObjectiveRoot`, dan `escPauseButton`.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Skrip & Prefab yang Diubah:**
  - `AR Dragon Sky Guardian/Assets/_Scripts/UI/InGameFlightHUD.cs`
  - `AR Dragon Sky Guardian/Assets/_Scripts/UI/VillageChallengeUI.cs`
  - `AR Dragon Sky Guardian/Assets/_Scripts/Gameplay/VillageZoneController.cs`
  - `AR Dragon Sky Guardian/Assets/Editor/UISceneAndPrefabBuilder.cs`
  - `AR Dragon Sky Guardian/Assets/Editor/Session4_VillageBuilder.cs`
  - Prefab & scene hasil `UISceneAndPrefabBuilder.BuildAll()`: `PF_QuestNarrativeBox.prefab`, `PF_VillageChallengeOverlay.prefab`, `MainMenu_Lobby.unity`, `MainIsland_Gameplay.unity`, `Test_Dragon_Flight.unity`.
- **Verifikasi Play Mode (`MainIsland_Gameplay.unity`, `0` compile/console errors, `0` new warnings):**
  - **Narrative Isolation (Pad Zephyria):** `ZephyriaState=NarrativeActive`, `IsNarrativeIsolated=True`; hanya `QuestObjectivePanel` & `PF_QuestNarrativeBox` (`1440×300` di `(0, 36)`) yang aktif, seluruh HUD lain & billboard 3D tersembunyi.
  - **Side-Docked Challenge HUD (Zephyria):** `anchor=(1.0, 0.5)`, `pos=(-30, 0)`, `size=(400, 620)`, `IsChallengeMode=True`; hanya `QuestObjectivePanel`, `MissionCapsule`, `Btn_EscPause`, & `PF_VillageChallengeOverlay` yang aktif; naga di tengah layar bebas 100%; badge `GERAKAN TERDETEKSI!` menyala hijau saat mengepak dan bar progres meningkat tanpa decay (`2 / 10 KEPAKAN (22%)`, `9 / 10 KEPAKAN (99%)`).
  - `Session4VerificationRunner.RunPlayModeVerificationAsync()`: **`ALL SESSION 4 VILLAGE, SUB-MARKER, AUTO-HOVER, ESCAPE, BILLBOARD & UI ISOLATION CHECKS PASSED (100%)`**.
  - `Session5GameLoopVerificationRunner.RunPlayModeVerificationAsync()`: **`ALL SESSION 5 GAME LOOP, COUNTDOWN, NARRATIVE FREEZE, GLOBAL VITALITY STARS, UNLOCK & FINISH MODAL CHECKS PASSED (100%)`**.
- **Tangkapan Layar (`docs/figures/raw_screenshots/`):**
  - `SS_UI_Narrative_Isolation.png`
  - `SS_UI_SideDocked_Challenge_Waiting.png`
  - `SS_UI_SideDocked_Challenge_Flapping.png`

---

## 4. Detail Penting yang Harus Diingat
- Di Unity TextMeshPro (`TMP_Text`), `lineSpacing` berskala `fontSize * 0.01f` (`currentEmScale`), sehingga `lineSpacing = 20f` menghasilkan spasi baris `1.2×` (bukan `1.2f`).
- `UI_Header_Bar_Parchment_9Slice.png` (`240×68`) memiliki margin transparan atas/bawah dengan pita isi sempit, sehingga tidak cocok untuk dua baris teks; gunakan `UI_Banner_Stepped_SubPanel_Blank.png` (`396×57`, 3-slice `42,0,42,0`) pada panel samping/sub-panel agar judul tidak bertumpukan dengan garis border.
- `InGameFlightHUD.RestoreNormalFlightHUD()` wajib tetap memanggil `HideInGameCameraControls()` agar `PIP_CameraControlsCard` tetap tersembunyi selama penerbangan aktif (kontrol kamera hanya melalui `Pause -> Calibration & Camera`).
- Saat menjalankan `UISceneAndPrefabBuilder.BuildAll()` pada `MainIsland_Gameplay.unity`, `UpgradeGameplayScene()` menghancurkan dan merakit ulang `InGameUICanvas`, sehingga blok re-wiring `VillageZoneController` & `GameManager` di bagian akhir `UpgradeGameplayScene()` harus dipertahankan.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- Ikuti status antrean terbaru di `[[Tasks]]` (uji langsung webcam pengguna di depan kamera untuk kenyamanan gestur & build Windows standalone akhir).

---
**Navigasi Obsidian:** [[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[Implementasi Sesi 4 Tantangan Desa]] · [[Implementasi Sesi 5 Game Loop dan Scoring]]
