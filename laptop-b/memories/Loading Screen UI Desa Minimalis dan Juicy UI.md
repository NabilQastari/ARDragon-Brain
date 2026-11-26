---
title: Loading Screen, UI Event Desa Minimalis, dan Juicy UI Pilar 2
type: session-memory
tags:
  - ui-ux
  - loading-screen
  - village-hud
  - ui-animation
  - juicy-ui
  - laptop-b
date: 2026-11-27
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[laptop-a/gameplay-mechanics/planning_post_animasi_vfx_ui_dan_kontrol]]"
  - "[[laptop-a/ui-ux/planning_perbaikan_ui_ingame_dan_event_desa]]"
  - "[[laptop-a/handoff-laptop-b/prompt_eksekusi_ui_animasi_loading_dan_desa_laptop_b]]"
---

# Loading Screen (`SCREEN 11`), UI Event Desa Minimalis (`416×520`), & Animasi Juicy UI (Pilar 2)

## 1. Tujuan & Konteks Sesi
Mengimpor paket aset UI baru (`assets/Export_UI_Animasi_Loading_Desa_LaptopB/` — `16` komponen PNG + `3` referensi `Designs/`), membangun **Loading Screen Antar-Scene (`SCREEN 11`)** beranimasi siluet naga mengambang + pita trail dinamis, merombak **`PF_VillageChallengeOverlay`** menjadi versi minimalis **`416 × 520 px`** dengan 3 emblem custom baru, serta mengimplementasikan seluruh **Animasi "Juicy UI" (Pilar 2)** di Unity 6 (`6000.3.7f1`).

## 2. Keputusan Desain / Arsitektur / Teknis
- **Custom Canvas Dynamic Wave Trail (`UILoadingWaveTrailGraphic.cs`):** Karena `PF_LoadingScreenOverlay` berjalan pada Canvas `ScreenSpaceOverlay` (`SortingOrder 999`), pita trail ekor naga diimplementasikan sebagai turunan `MaskableGraphic` (bukan `LineRenderer`/`TrailRenderer` world-space) agar terender langsung di kanvas UI, mengikuti `CanvasGroup.alpha` saat fade, serta mengunci ujung kanannya ke `TailAnchor` `(0.084, 0.167)` pada `UI_Loading_Dragon_Silhouette` yang mengambang naik-turun (`Mathf.Sin(Time.unscaledTime * 2.2f) * 14f` px) + anggukan rotasi Z (`±4°`).
- **Singleton Loading Persisten (`LoadingScreenManager.cs`):** `DontDestroyOnLoad` dari `Assets/Resources/PF_LoadingScreenOverlay.prefab` dengan reset statis `[RuntimeInitializeOnLoadMethod(RuntimeInitializeLoadType.SubsystemRegistration)]`. Memakai `rootCanvas.enabled = false` saat idle (menjaga `GameObject` root tetap aktif agar `StartCoroutine` tidak pernah gagal pada transisi berulang), serta memanggil `DragonGameSettings.ReleasePoseReceiversForTransition()` sebelum `SceneManager.LoadSceneAsync`.
- **UI Event Desa Minimalis (`PF_VillageChallengeOverlay.prefab` & `VillageChallengeUI.cs`):** Mengganti kartu `400×620` lama menjadi `416×520 px` *Middle-Right* `(-32, 0)` di atas `UI_Panel_Challenge_Minimalist_Frame` dengan tepat 4 zona bersih. Karena font `Fredoka-Bold SDF.asset` belum memuat glif Unicode `●` (`9679`) dan `○` (`9675`), badge status memakai `HorizontalLayoutGroup` terpusat berisi lingkaran `UIAnimationUtils.GetCircleSprite(bool hollow)` (`●` `#FFFDF5` / `○` `#231E18`) + `TMP_Text` + `LayoutRebuilder.ForceRebuildLayoutImmediate`.
- **Utilitas Animasi Tanpa Plugin Eksternal (`UIAnimationUtils.cs` & `UIButtonJuice.cs`):** Seluruh kurva (`EaseOutBack`, `EaseInQuad`, `EaseOutQuad`, `EaseOutBounce`), *trailing ghost bar* (`SmoothDamp 0.08s` utama vs `0.35s` ghost `#FFFDF5` alpha `0.35` di atas track gelap `#231E18`), *parchment unroll* (`localScale.y` `0 -> 1` dari pivot bawah `0` + fade-in konten + *shimmer sweep*), *modal drop-in* + pop bintang berurutan (`0.55s`, `0.95s`, `1.35s`) + *sparkle burst* emas bintang ke-3, *micro-interaction* tombol (`1.04x` hover / `0.96x` press), dan *denied shake* (`±10 px`, `0.30s`) berjalan dengan `Time.unscaledDeltaTime`.

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Impor Aset & Sprite Setup (`UISpriteSetupUtility.cs`):**
  - Ekstraksi paket ke `assets/Export_UI_Animasi_Loading_Desa_LaptopB/`, salin `10` PNG ke `Assets/UI/Components/08_LoadingScreen/`, `6` PNG ke `Assets/UI/Components/05_Challenge_Minimalist/`, dan `3` referensi (`11_LoadingScreen.png`, `03B_SideDocked_ChallengeHUD_Improved.png`, `03C_SideDocked_3Biome_Showcase.png`) ke `docs/UI_Designs_Reference/`.
  - Total **`158` PNG** dikonfigurasi (`82` 9-Slice/3-Slice borders, `FullRect`, `Bilinear`, `SpriteMode.Single`, `15/15` pose sprites).
- **Skrip Baru (`Assets/_Scripts/UI/`):**
  - `LoadingScreenManager.cs`, `UILoadingWaveTrailGraphic.cs`, `UIAnimationUtils.cs`, `UIButtonJuice.cs`.
- **Skrip Diperbarui (`Assets/_Scripts/UI/`, `Assets/_Scripts/Gameplay/`, `Assets/Editor/`):**
  - `DragonGameSettings.cs` (`ReleasePoseReceiversForTransition` + routing `LoadSceneCleanly` ke `LoadingScreenManager.Instance.LoadSceneAsync`).
  - `VillageChallengeUI.cs` & `VillageZoneController.cs` (layout 4 zona minimalis, 3 emblem custom + `UI_Pose_09_HoverMode` untuk sub-tahap Danau, intro/outro slide, punch scale, & trailing ghost bar).
  - `InGameFlightHUD.cs` (`momentumGhostFillImage` & `SetVillageChallengeMode(false)` menjaga animasi outro).
  - `QuestNarrativeUI.cs` (`UnrollY` + `contentCanvasGroup` + `countdownShimmerRect`).
  - `FinishModalUI.cs` (`ModalCard` drop-in `EaseOutBounce`, jadwal pop-in bintang `0.55s`/`0.95s`/`1.35s`, & `SpawnGoldSparkleBurst` bintang ke-3).
  - `MissionsPanelUI.cs`, `CalibrationPanelUI.cs`, `LobbyNavigationManager.cs`, `PauseModalUI.cs`, `SettingsPanelUI.cs` (`UIButtonJuice` & *denied horizontal shake*).
  - `UISceneAndPrefabBuilder.cs` (`BuildLoadingScreenOverlayPrefab`, `BuildVillageChallengePrefab` `416×520`, `BuildQuestNarrativePrefab`, `BuildFinishModalPrefab`, `UpgradeGameplayScene`) & `UIJuiceAndLoadingVerificationRunner.cs`.
- **Prefab & Scene Diperbarui:** `Assets/Prefabs/UI/PF_LoadingScreenOverlay.prefab`, `Assets/Resources/PF_LoadingScreenOverlay.prefab`, `PF_VillageChallengeOverlay.prefab`, `PF_QuestNarrativeBox.prefab`, `PF_FinishModal.prefab`, `MainMenu_Lobby.unity`, `MainIsland_Gameplay.unity`, `Test_Dragon_Flight.unity`.

## 4. Detail Penting yang Harus Diingat
- **Loading Sequence Timing:** Fade-In `0.30s` $\rightarrow$ `SceneManager.LoadSceneAsync` (`allowSceneActivation = false` s.d. `0.9f`) $\rightarrow$ Smooth Bar `0.9 -> 1.0` (`0.18s`) $\rightarrow$ Hold `0.25s` $\rightarrow$ Fade-Out `0.35s` (seluruhnya `Time.unscaledDeltaTime`).
- **Jadwal Bintang `FinishModalUI`:** `0.55s` (★1), `0.95s` (★2), `1.35s` (★3 + sparkle burst), tetap kompatibel dengan batas waktu asersi `Session5GameLoopVerificationRunner` (`900ms` untuk 1★, `<3.0s` untuk 3★).
- **Verifikasi Play Mode (`0` Errors, `0` Warnings):**
  - `UIJuiceAndLoadingVerificationRunner` (Lobby uncalibrated denied-shake + `PLAY` $\rightarrow$ `SCREEN 11` $\rightarrow$ `MainIsland_Gameplay` + 3 biome minimalis `416×520` + `GhostBar` + intro/outro + zero text truncation + `UIButtonJuice=16`) **PASSED**.
  - Non-regresi `Session4VerificationRunner` **100% PASSED** & `Session5GameLoopVerificationRunner` **100% PASSED**.
  - Tangkapan layar verifikasi tersimpan di `docs/figures/raw_screenshots/`: `SS_UI_LoadingScreen.png` dan `SS_UI_Minimalist_VillageHUD_3Biomes.png`.
  - Scene aktif di Unity Editor dikembalikan ke `Assets/_Scenes/MainMenu_Lobby.unity` (Build Index `0`).

## 5. Tugas Tersisa & Langkah Selanjutnya
- Pengujian visual & kenyamanan langsung dengan webcam oleh pengguna, diikuti eksekusi build Windows standalone (`MainMenu_Lobby`, `MainIsland_Gameplay`, `Test_Dragon_Flight`) + `start_game.bat`.

---
[[00 - Overview]] | [[Tasks]] | [[Devlog]] | [[laptop-a/gameplay-mechanics/planning_post_animasi_vfx_ui_dan_kontrol]] | [[laptop-a/ui-ux/planning_perbaikan_ui_ingame_dan_event_desa]]
