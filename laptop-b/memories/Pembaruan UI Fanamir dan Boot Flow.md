---
title: "Pembaruan UI Fanamir dan Boot Flow"
type: session-memory
tags:
  - session-memory
  - laptop-b
  - ui
  - unity6
  - boot-flow
  - fanamir
date: 2026-11-21
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[Implementasi UI Hybrid AR Dragon]]"
  - "[[laptop-a/rencana_desain_ui]]"
  - "[[laptop-a/panduan_aset_ui_unity]]"
  - "[[laptop-a/kontrak_integrasi_ui_dan_progress]]"
---

# Pembaruan UI Fanamir dan Boot Flow (`v1.6.1` – `v1.8`)

## 1. Tujuan & Konteks Sesi
Menyelesaikan rangkaian pembaruan antarmuka pengguna Unity 6 URP (**Screen 00–10**, dari `v1.6.1` hingga `v1.8`) untuk **Fanamir: The Sky Guardian** di Laptop B berdasarkan spesifikasi [[laptop-a/rencana_desain_ui]], [[laptop-a/panduan_aset_ui_unity]], dan [[laptop-a/kontrak_integrasi_ui_dan_progress]], sembari tetap menjaga kompatibilitas namespace internal `DragonWingAR.*` dan pengujian mekanisme terbang langsung di [`Test_Dragon_Flight.unity`](AR%20Dragon%20Sky%20Guardian/Assets/_Scenes/Test_Dragon_Flight.unity).

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Navigasi Pose & Katalog Kalibrasi (`v1.6.1`):** Panel Kalibrasi `SCREEN 01` disederhanakan menjadi baris **`1×4` kartu dinamis (MRU Queue)** (`PoseSwitchDwellSeconds = 0.15s`, default `[08, 06, 04, 13]`) agar area Camera Mirror & Live Fanamir Preview dapat diperbesar ke proporsi `860×540` (`RenderTexture` `840×438`), sementara katalog lengkap seluruh pose dibuka melalui modal dormant `SCREEN 07` (`PF_PoseGuideOverlay.prefab` & `PoseGuideCatalog.cs`).
2. **Pemisahan Branding Publik vs Kontrak Internal (`v1.7`):** Teks antarmuka diperbarui ke **`FANAMIR: THE SKY GUARDIAN`** / **`Fanamir`**, sementara namespace C# `DragonWingAR.*`, nama scene, `/Dragon_Player`, dan kontrak integrasi dipertahankan utuh agar tidak memutus binding skrip maupun uji mekanisme.
3. **Quest Narrative *Hands-Free Auto-Play* (`SCREEN 08`):** Karena tangan pemain aktif melakukan gestur di depan kamera, box narasi [`QuestNarrativeUI.cs`](AR%20Dragon%20Sky%20Guardian/Assets/_Scripts/UI/QuestNarrativeUI.cs) dirancang tanpa tombol Continue wajib — bergerak otomatis memakai interval persisten `DragonGameSettings.NarrativeAutoNextSeconds` (`PlayerPrefs` `Setting_NarrativeAutoNextSec`, default `5.0s`, rentang `2.0s–10.0s`), berhenti saat `PauseModalUI.IsPaused`, dan memicu transisi `QuestObjectivePanel` dari `TRAVEL` ke `ACTIVE TASK` setelah baris terakhir selesai.
4. **Lobby Terbuka & Boot Flow Satu Scene (`v1.8`):** Kontainer perkamen luar `MainWindowParchment` dan pita belakang `SharedTopNavbar` dihapus agar `Background_AerialIsland` (`AspectRatioFitter.EnvelopeParent`) tampak penuh di Lobby. [`BootFlowController.cs`](AR%20Dragon%20Sky%20Guardian/Assets/_Scripts/UI/BootFlowController.cs) menampung fase **`SplashScreenPanel` (`SCREEN 09`) $\rightarrow$ `StartScreenPanel` (`SCREEN 10`) $\rightarrow$ `Lobby`** di dalam [`MainMenu_Lobby.unity`](AR%20Dragon%20Sky%20Guardian/Assets/_Scenes/MainMenu_Lobby.unity) sehingga perpindahan ke Tab `Missions` berbagi `Background_AerialIsland` tanpa jeda pemuatan scene.
5. **Linear Color Space & Input Guard:** Latar kapsul `Start_PromptCapsule` menjaga `alpha = 1.0f` dan hanya menganimasikan `CanvasGroup` pada child `PromptContentGroup` agar warna perkamen tetap solid; klik dekorasi plakat/kapsul diteruskan lewat `raycastTarget = false` kecuali `Btn_StartEscQuit` (`startQuitButtonRect`), sementara kembalinya pemain dari gameplay menyetel `BootFlowController.SkipBootScreensOnce = true` pada [`PauseModalUI.cs`](AR%20Dragon%20Sky%20Guardian/Assets/_Scripts/UI/PauseModalUI.cs) & [`FinishModalUI.cs`](AR%20Dragon%20Sky%20Guardian/Assets/_Scripts/UI/FinishModalUI.cs).

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Referensi Desain (`docs/UI_Designs_Reference/`):** Terpasang lengkap `11` PNG `1920×1080` (`00_MainMenu_LevelSelection.png` hingga `10_StartScreen.png`), termasuk penambahan `07`, `08`, `09_SplashScreen.png`, dan `10_StartScreen.png`.
- **Aset Sprite & Border (`UISpriteSetupUtility.cs`):** Pembaruan komponen ke `140` PNG (`v1.7` menambah `UI_Panel_QuestObjective_9Slice`, `UI_Panel_NarrativeBox_9Slice`, `UI_Banner_SpeakerTag_Blank`, `UI_Icon_QuestScroll_Gold`, `UI_Icon_AutoPlay_Timer`, `UI_Icon_PromptNext_Arrow`) dengan `75` konfigurasi border `Vector4(L, B, R, T)` (`UI_Banner_SpeakerTag_Blank` 3-Slice H terkunci `42 px`, `UI_Banner_Stepped_Modal_Blank` 3-Slice H terkunci `77 px`).
- **Skrip Baru & Diperbarui (`AR Dragon Sky Guardian/Assets/_Scripts/UI/` & `Editor/`):**
  - `PoseGuideCatalog.cs` & `PoseGuideOverlayUI.cs` (katalog & modal `SCREEN 07`).
  - `QuestNarrativeUI.cs` (`SCREEN 08` Auto-Play) & `PF_QuestNarrativeBox.prefab`.
  - `BootFlowController.cs` (alur `Splash` $\rightarrow$ `StartScreen` $\rightarrow$ `Lobby` + `SkipBootScreensOnce`).
  - Pembaruan `CalibrationPanelUI.cs`, `LiveDragonPreviewController.cs`, `LobbyNavigationManager.cs` (prioritas `Escape` & koordinasi `!bootFlowController.IsInLobby`), `DragonGameSettings.cs` (`NarrativeAutoNextSeconds`), `SettingsPanelUI.cs` (slider ke-5 `Text Auto-Next`), `InGameFlightHUD.cs` (`QuestObjectivePanel`, klaster PIP `420 px`, `WaypointCompass`, `GlobalVitalityBar`), `VillageChallengeUI.cs`, `PauseModalUI.cs`, `FinishModalUI.cs`, serta [`UISceneAndPrefabBuilder.cs`](AR%20Dragon%20Sky%20Guardian/Assets/Editor/UISceneAndPrefabBuilder.cs) (`BuildAll()`).

---

## 4. Detail Penting yang Harus Diingat
- **Editor Play Mode Start Scene:** Tombol **Play** Unity Editor menjalankan scene yang sedang aktif terbuka di Editor (`EditorSceneManager.playModeStartScene == null`). Pastikan [`MainMenu_Lobby.unity`](AR%20Dragon%20Sky%20Guardian/Assets/_Scenes/MainMenu_Lobby.unity) (Build Index `0`) dibuka di Editor bila ingin langsung menguji alur **Splash Screen $\rightarrow$ Start Screen $\rightarrow$ Lobby**; membuka scene gameplay secara langsung tetap mengizinkan testing mekanisme terbang tanpa melewati Splash.
- **Timing & Proteksi Input `BootFlowController.cs`:**
  - `splashFadeInDuration = 0.45s`, `splashHoldDuration = 1.80s`, `splashFadeOutDuration = 0.45s`, `splashSkipUnlockDelay = 0.50s`.
  - `startInputUnlockDelay = 0.25s`, `transitionToLobbyDuration = 0.25s` (mencegah klik Start tembus ke tombol kartu stage Tab `Missions`).
  - `Keyboard.current.escapeKey.wasPressedThisFrame` dicek lebih dulu daripada `Keyboard.current.anyKey.wasPressedThisFrame`, dan klik mouse difilter terhadap `startQuitButtonRect` agar `ESC · QUIT` menutup aplikasi alih-alih masuk Lobby.
- **Status Python CV:** `PythonProcessManager.autoLaunchPython` tetap `false` selama Splash, Start Screen, dan Lobby sampai pemain menyalakan kamera secara eksplisit, dengan verifikasi `0` proses `active_cv_sender.py` tertinggal pasca Play Mode.
- **Integrasi Dokumentasi Utama:** Ringkasan inventaris menyeluruh & protokol *Feature-to-UI Sync* tetap dipelihara bersama pada [[Implementasi UI Hybrid AR Dragon]].

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- Lanjutkan integrasi lanjutan & penyelesaian roadmap **Sesi 5 (`GameManager` timer/bintang/unlock & `AudioManager` mixer)** beserta *Feature-to-UI Sync* penuh sesuai [[Tasks]].
- Jalankan uji Play Mode langsung dari pengguna dengan input keyboard/gamepad/mouse penuh pada jendela Game View terfokus untuk memvalidasi kenyamanan rasa (*game feel*) alur Splash $\rightarrow$ Start Screen $\rightarrow$ Lobby & kamera live.

---
**Navigasi Obsidian:** [[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[Implementasi UI Hybrid AR Dragon]] · [[laptop-a/rencana_desain_ui]] · [[laptop-a/kontrak_integrasi_ui_dan_progress]]
