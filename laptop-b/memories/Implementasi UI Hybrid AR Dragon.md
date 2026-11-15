---
title: Implementasi UI Hybrid & Sinkronisasi AR Dragon
type: session-memory
tags:
  - laptop-b
  - ui
  - unity6
  - calibration
  - hud
  - sync-protocol
  - ar-dragon
date: 2026-11-15
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[laptop-a/rencana_desain_ui]]"
  - "[[laptop-a/panduan_aset_ui_unity]]"
  - "[[laptop-a/kontrak_integrasi_ui_dan_progress]]"
---

# 🎨 Implementasi UI Hybrid & Sinkronisasi — AR Dragon: Sky Guardian

## 1. Tujuan & Konteks Sesi
Mengimplementasikan seluruh antarmuka pengguna (**Screen 00–07**, termasuk delta patch `v1.6.1`) dan scene UI *AR Dragon: Sky Guardian* di Unity 6 URP (`6000.3.7f1`, namespace `DragonWingAR.*`) berbasis paket aset `UI_Unity_Export` (**134 PNG komponen** textless + **8 referensi 1920×1080**) sesuai spesifikasi [[laptop-a/rencana_desain_ui]], [[laptop-a/panduan_aset_ui_unity]], dan [[laptop-a/kontrak_integrasi_ui_dan_progress]], **tanpa merusak pengujian mekanisme terbang** pada [`Test_Dragon_Flight.unity`](AR Dragon Sky Guardian/Assets/_Scenes/Test_Dragon_Flight.unity).

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Arsitektur UI Hybrid (Live + Placeholder + Dormant Prefab):**
   - **Live Wired:** Fitur yang backend-nya sudah aktif (`PythonProcessManager`, `UDPPoseReceiver`, `DefaultFlightInputMapper`, `DragonWingAnimator`, kamera & feed PIP `5052/5053/5054`) langsung tersambung penuh dan diuji di Play Mode.
   - **Placeholder Fungsional:** Elemen milik fitur yang belum diimplementasikan (Stage 02/03, BGM/SFX mixer, HUD Timer/Villages/Vitality, Pose 15) tampil rapi sesuai desain dengan nilai aman dan ditandai komentar `// TODO [SYNC-GAMEPLAY]` atau `// TODO [SYNC-AUDIO]`.
   - **Dormant Prefab (`SetActive(false)`):** Overlay tantangan desa (`SCREEN 03`), modal hasil misi (`SCREEN 04` Victory/Defeat), dan Waypoint Compass (`SCREEN 02`) dibuat lengkap sebagai prefab + API publik + `[ContextMenu]` Editor preview, namun nonaktif saat default agar tidak mengganggu uji terbang.
2. **Aturan Rendering & Linear Color Space Unity 6 URP:**
   - Seluruh panel perkamen dan `SolidCameraDropdown` (`UI_Dropdown_Popup_9Slice`, `#FBF6EB`) menggunakan `Image.color = Color.white` (`alpha = 1.0f` solid opaque) agar bebas efek tembus pandang pada URP Linear Color Space.
   - Sprite border dikonfigurasi via C# `Vector4(L, B, R, T)` (berbeda urutan dengan tabel `L, T, R, B` di Editor Inspector).
   - `CanvasScaler` diseragamkan ke `1920×1080` (`Scale With Screen Size`, `Match = 0.5`).
   - `UI_Panel_Parchment_Full_9Slice.png` sudah memiliki ornamen sudut di artwork sehingga kartu modal/settings tidak ditumpuki medali sudut tambahan.
3. **Perlindungan Pengujian Mekanisme & Lifecycle Python/UDP:**
   - `autoLaunchPython` tetap `false`; pengguna dapat langsung menekan Play di `Test_Dragon_Flight.unity` maupun memulai alur dari `MainMenu_Lobby.unity` (Build Index `0` $\rightarrow$ `Test_Dragon_Flight.unity` Index `1`).
   - Singleton `PythonProcessManager` hanya mengeksekusi cleanup proses Python jika `_instance == this`; `UDPPoseReceiver.ReleasePortForSceneTransition()` melepas soket secara bersih saat perpindahan scene.
   - `PauseModalUI`, `VillageChallengeUI`, dan `FinishModalUI` dilindungi flag inisialisasi (`_hasInitialized` / `_hasOpened` / `_hasShown`) agar panggilan pertama setelah `SetActive(true)` tidak menonaktifkan dirinya sendiri di `Awake()`.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset

### A. Aset Ekstrak & Editor Automation
- **Referensi Desain (di luar `Assets/`):** `docs/UI_Designs_Reference/` (`8` PNG `1920×1080`, termasuk revisi `01_Calibration_MotionTutorial.png` & `07_PoseGuide_OverlayModal.png`).
- **Komponen UI Runtime:** `AR Dragon Sky Guardian/Assets/UI/Components/` (`134` PNG).
- **Font TMP (`Assets/Fonts/`):** `UIFontSetupUtility.cs` mengonfigurasi **10 aset TMP SDF** (`Fredoka-Bold`, `Fredoka-SemiBold`, `Nunito-ExtraBold`, `Nunito-Bold`, `Nunito-Medium` + masing-masing `DynamicFallback SDF.asset`).
- **Sprite Importer Otomatis:** `Assets/Editor/UISpriteSetupUtility.cs` (`SetupAllSprites()`) mengatur seluruh **134 PNG** (`Sprite`, `Single`, `PPU = 100`, `FullRect`, `Bilinear`, `Uncompressed`, `sRGB`, `alphaIsTransparency`) termasuk **72 border 9-Slice / Horizontal 3-Slice** `(L, B, R, T)`.
- **Scene & Prefab Builder:** `Assets/Editor/UISceneAndPrefabBuilder.cs` (`BuildAll()`).

### B. Pembaruan Skrip Inti (`Assets/_Scripts/`)
- `CV_Receiver/PythonProcessManager.cs`: Persistensi singleton lintas scene (`_instance == this` guard pada cleanup), daftar kamera DirectShow (`[0..6]`), mode background (`LaunchBackgroundMode()`), external preview (`LaunchExternalPreviewMode()`), dan `StopPython()`.
- `CV_Receiver/UDPPoseReceiver.cs`: Penambahan `ReleasePortForSceneTransition()` & guard `OnDestroy`.
- `Controllers/DefaultFlightInputMapper.cs`: Field `[Range(0.5f, 3.0f)] turnSensitivity` tersinkron ke `DragonGameSettings.TurnSensitivity`.
- `Controllers/DragonWingAnimator.cs`: Penambahan `SetPreviewState` & `ClearPreviewState` untuk animasi 3D Live Dragon Preview di layar Kalibrasi.

### C. Skrip UI Baru (`Assets/_Scripts/UI/` — 16 Skrip)
1. `LobbyNavigationManager.cs`
2. `MissionsPanelUI.cs`
3. `CalibrationPanelUI.cs`
4. `LiveDragonPreviewController.cs`
5. `SettingsPanelUI.cs`
6. `CameraControlModuleUI.cs`
7. `SolidCameraDropdown.cs`
8. `DragonGameSettings.cs`
9. `UDPVideoStreamReceiver.cs`
10. `UISkeletonOverlayGraphic.cs`
11. `InGameFlightHUD.cs`
12. `PauseModalUI.cs`
13. `VillageChallengeUI.cs`
14. `FinishModalUI.cs`
15. `PoseGuideCatalog.cs` *(katalog tunggal 15 pose `#01..#15` & default MRU `[08, 06, 04, 13]`)*
16. `PoseGuideOverlayUI.cs` *(modal `SCREEN 07` Pose Guide Overlay)*

### D. Prefab & Scene Build Settings
- **Prefab UI (`Assets/Prefabs/UI/` — 7 Prefab):**
  - `PF_CameraControlModule_Compact.prefab`
  - `PF_CameraControlModule_Full.prefab`
  - `PF_SettingsPanel.prefab`
  - `PF_PauseModal.prefab`
  - `PF_VillageChallengeOverlay.prefab`
  - `PF_FinishModal.prefab`
  - `PF_PoseGuideOverlay.prefab` *(modal `SCREEN 07`, default nonaktif di `MainMenu_Lobby`)*
- **Build Settings (`EditorBuildSettings.scenes`):**
  - Index `0`: `Assets/_Scenes/MainMenu_Lobby.unity` (`enabled = true`)
  - Index `1`: `Assets/_Scenes/Test_Dragon_Flight.unity` (`enabled = true`)

---

## 4. Detail Penting yang Harus Diingat

### A. Inventaris UI Hybrid (Live vs Placeholder vs Dormant)

| Status | Layar / Elemen | Skrip / Prefab | API Publik / Integrasi |
| :--- | :--- | :--- | :--- |
| **LIVE** | Shared Lobby Top Bar & Navigasi Tab | `LobbyNavigationManager` | `SwitchTab(LobbyTab)` (`Calibration`, `Missions`, `Settings`), `LaunchFlightScene()`, `QuitApplication()` |
| **LIVE** | Stage 01 (`Smog Valley`) Card & Tombol `PLAY` | `MissionsPanelUI` | Memuat `Test_Dragon_Flight.unity` sambil membawa sesi kamera & `DragonGameSettings` |
| **LIVE** | Kalibrasi (`SCREEN 01`): Panel atas `860×540`, Kamera 5-State, PIP `5053`, Skeleton, Status `0..5`, `5054`, Live 3D Naga (`840×438`), Baris `1×4` Dinamis MRU + `OPEN POSE GUIDE` | `CalibrationPanelUI`, `CameraControlModuleUI`, `SolidCameraDropdown`, `UDPVideoStreamReceiver`, `UISkeletonOverlayGraphic`, `LiveDragonPreviewController` | `TriggerCalibration()`, `CancelCalibration()`, `ResetCalibrationProfile()` via `UDPPoseReceiver`; antrean MRU 4-slot default `[08, 06, 04, 13]` (`0.15s` dwell debounce, anti-duplikat), `ResetRecentPosesToDefault()`, `OpenPoseGuide()`, `SimulateDetectedPose(int)` |
| **LIVE** | Modal Pose Guide (`SCREEN 07` — Semua 15 Pose + Sync Live) | `PoseGuideOverlayUI`, `PoseGuideCatalog`, `LobbyNavigationManager` (`PF_PoseGuideOverlay.prefab`) | `Show(int)`, `Hide()`, `SetActivePoseHighlight(int)`, grid `3×5` (`#01..#15`, tag `MAIN` + `ACTIVE` sinkron live), `HandleEscapeAction()` menutup modal sebelum pindah tab |
| **LIVE** | Settings (`SCREEN 05/06`): Kamera 5-State, Popup Dropdown Solid `#FBF6EB`, `Turn Sensitivity` (`1.0×–3.0×`), `Master Volume` | `SettingsPanelUI`, `DragonGameSettings`, `CameraControlModuleUI`, `SolidCameraDropdown` | `PlayerPrefs` (`DragonWingAR.TurnSensitivity`), `ApplyTurnSensitivityToActiveMappers(float)`, `AudioListener.volume`, `SyncUIFromSettings()` |
| **LIVE** | In-Game HUD (`SCREEN 02`): Altitude, Airspeed, Mode Pose Aktif, PIP `16:9` (`UDP 5053`), `ESC · PAUSE` | `InGameFlightHUD`, `PauseModalUI` (`PF_PauseModal.prefab`) | `Pause()` (`Time.timeScale = 0f`), `Resume()`, `TogglePause()`, `OpenSettingsSubPanel()`, `CloseSettingsSubPanel()` |
| **PLACEHOLDER** | Kartu Stage 02 (`Wildfire`) & Stage 03 (`Drought/Locked`) | `MissionsPanelUI`, `DragonGameSettings` | `SetStageProgress(int stageIndex, int starsEarned, bool isLocked)`, `DragonGameSettings.IsStage2UnlockedPlaceholder` (`// TODO [SYNC-GAMEPLAY]`) |
| **PLACEHOLDER** | Slider `BGM` & `SFX Volume` (`%` & `PlayerPrefs` aktif) | `SettingsPanelUI`, `DragonGameSettings` | Event `OnBgmVolumeChanged`, `OnSfxVolumeChanged` (`// TODO [SYNC-AUDIO]`) |
| **PLACEHOLDER** | HUD Timer, Counter Desa `0/3`, Global Vitality Bar | `InGameFlightHUD` | `SetMissionTimer(float remainingSeconds)`, `SetVillageCount(int cleared, int total)`, `SetGlobalVitality(float normalized01)` |
| **PLACEHOLDER** | Kartu Pose ke-15 (`UI_Pose_15_VillageActions`) | `CalibrationPanelUI` | `SetVillageActionHighlight(bool active)` |
| **DORMANT** | Waypoint Compass (`UI_HUD_Waypoint_Chevron`) | `InGameFlightHUD` | `SetWaypointTarget(Transform target, string villageLabel)`, `ClearWaypoint()` |
| **DORMANT** | Overlay Tantangan Desa (`SCREEN 03`) | `VillageChallengeUI` (`PF_VillageChallengeOverlay.prefab`) | `ShowChallenge(string villageTitle, string gestureInstruction, int biomeType, float progress01)` (`BiomeSmog=0`, `BiomeFire=1`, `BiomeDrought=2`), `UpdateProgress(float progress01)`, `HideChallenge()`, + `[ContextMenu]` Editor preview |
| **DORMANT** | Modal Victory & Defeat (`SCREEN 04`) | `FinishModalUI` (`PF_FinishModal.prefab`) | `ShowVictory(int stars, float elapsedSec, int cleared, int total, int rewardBonusStars)`, `ShowDefeat(float elapsedSec, int cleared, int total, string remainingNote)`, `HideModal()`, event `OnNextLevelRequested`, `OnRetryRequested`, `OnBackToLobbyRequested`, + `[ContextMenu]` Editor preview |

### B. Protokol Sinkronisasi Otomatis (`Feature-to-UI Sync`)
Setiap sesi pengembangan fitur baru **WAJIB** sekaligus mengaktifkan dan menyambungkan elemen UI terkait dalam sesi yang sama:
1. **Saat Pulau 3D / Scene Gameplay Final Dibuat:**
   - Daftarkan scene baru di `EditorBuildSettings` dan perbarui `DragonGameSettings.FlightSceneName` / routing di `MissionsPanelUI` & `LobbyNavigationManager` (sambil tetap mempertahankan `Test_Dragon_Flight.unity` atau menyinkronkan target `PLAY`).
   - Pasang prefab/hierarki HUD (`InGameFlightHUD`, `PF_PauseModal`, `PF_VillageChallengeOverlay`, `PF_FinishModal`) ke scene gameplay baru.
2. **Saat `VillageZoneController` / Sistem Tantangan Desa Diimplementasikan:**
   - Hubungkan trigger masuk/keluar zona desa ke `VillageChallengeUI.ShowChallenge(...)`, `UpdateProgress(...)`, `HideChallenge()` dengan konstanta `BiomeSmog` (`0`), `BiomeFire` (`1`), `BiomeDrought` (`2`).
   - Aktifkan Waypoint Compass via `InGameFlightHUD.SetWaypointTarget(...)` & `ClearWaypoint()`.
   - Hubungkan gestur aksi desa ke `CalibrationPanelUI.SetVillageActionHighlight(...)` (Pose 15).
3. **Saat `GameManager` (Timer, Bintang, Level Unlock, Global Vitality) Diimplementasikan:**
   - Hubungkan loop gameplay ke `InGameFlightHUD.SetMissionTimer(...)`, `SetVillageCount(...)`, `SetGlobalVitality(...)`.
   - Hubungkan kondisi menang/kalah ke `FinishModalUI.ShowVictory(...)` / `ShowDefeat(...)` dan dengarkan `OnNextLevelRequested`, `OnRetryRequested`, `OnBackToLobbyRequested`.
   - Ganti `DragonGameSettings.IsStage2UnlockedPlaceholder` dengan progres persisten dan panggil `MissionsPanelUI.SetStageProgress(...)` untuk Stage 01–03.
4. **Saat `AudioManager` & `AudioMixer` Diimplementasikan:**
   - Subscribe ke `DragonGameSettings.OnBgmVolumeChanged` & `OnSfxVolumeChanged` serta terapkan nilai tersimpan saat startup (hapus/ganti penanda `// TODO [SYNC-AUDIO]`).
5. **Saat Terjadi Perubahan Gestur CV / Kalibrasi:**
   - Sinkronkan label/highlight kartu pose pada `CalibrationPanelUI` (`UI_Pose_01..15`) dan `InGameFlightHUD` agar teks/ikon tetap akurat dengan kelas `UDPPoseReceiver`.

### C. Hasil Verifikasi Akhir
- Kompilasi & build editor `UISceneAndPrefabBuilder.BuildAll()`: `0` Console Errors (hanya 1 warning pra-eksisting di `QuickUDPTestBuilder.cs(16,40)`).
- Uji Play Mode dari `MainMenu_Lobby` (`v1.6.1`): verifikasi visual `1920×1080` untuk `SCREEN 00–07`, `SolidCameraDropdown` 100% solid opaque `#FBF6EB` (7 kamera `[0..6]`), toggle kamera `SelectCamera(6, "OBS Virtual Camera")` + `LaunchBackgroundMode()` (`Started=True`) + `StopPython()` (`Stopped=True`), persistensi `TurnSensitivity`, rotasi MRU `1×4` (`[7,5,3,12]` -> `[11,7,5,3]` -> `[5,11,7,3]` -> `[13,5,11,7]` anti-duplikat -> `[14,13,5,11]` -> reset `[7,5,3,12]`), modal `SCREEN 07` (`15` kartu tanpa elipsis/tumpang-tindih, sinkronisasi live highlight `HighlightedPoseIndex = 12`, close button, `ESC · CLOSE`, serta prioritas `HandleEscapeAction()` `esc1=(False, Calibration)` -> `esc2=(False, Missions)`), dan `[ContextMenu]` preview `PauseModalUI`/`VillageChallengeUI`/`FinishModalUI` — **0 Console Errors/Warnings**.
- Uji Play Mode langsung dari `Test_Dragon_Flight.unity`: `player=True, ctrl=True, mapper=True, wingAnim=True, hud=True`, `pyCount=1, autoLaunch=False, pyRunning=False`, seluruh overlay dormant nonaktif (`WaypointCompass`, `PF_VillageChallengeOverlay`, `PF_FinishModal`, `PF_PauseModal` = `False`), keluar Play Mode bersih (`0` errors/warnings, `0` proses Python CV tertinggal).

---

## 5. Tugas Tersisa & Langkah Selanjutnya
1. Implementasi Pulau 3D + integrasi aset `rumah.blend` + eksekusi **Feature-to-UI Sync** (Scene / Level routing).
2. Implementasi `VillageZoneController` + **`Feature-to-UI Sync`** (`VillageChallengeUI`, Waypoint Compass, Pose 15).
3. Implementasi `GameManager` (Timer, Bintang, Level Unlock) + **`Feature-to-UI Sync`** (`InGameFlightHUD`, `FinishModalUI`, `MissionsPanelUI`).
4. Implementasi `AudioManager` (`AudioMixer`) + **`Feature-to-UI Sync`** (`DragonGameSettings.OnBgmVolumeChanged` / `OnSfxVolumeChanged`).

---

[[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[laptop-a/kontrak_integrasi_ui_dan_progress]] · [[laptop-a/rencana_desain_ui]]
