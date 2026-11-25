---
title: Implementasi Sistem Audio Fanamir
type: session-memory
tags:
  - session-memory
  - audio
  - bgm
  - sfx
  - unity
  - laptop-b
date: 2026-11-25
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[laptop-a/gameplay-mechanics/arsitektur_sistem_audio]]"
---

# 🎵 Implementasi Sistem Audio Terpadu Fanamir (37 Klip, Dual Dropdown BGM, Quick Switcher, & Heavy Propeller Dash)

## 1. Tujuan & Konteks Sesi
Mengimplementasikan dan memperbarui sistem audio terpadu *Fanamir: The Sky Guardian* di Laptop B (`AR Dragon Sky Guardian`, Unity 6 `6000.3.7f1` URP) sesuai [[laptop-a/gameplay-mechanics/arsitektur_sistem_audio]]:
- Mengekstrak & menyinkronkan ulang **37 berkas audio** (**14 BGM MP3 + 23 SFX WAV**, termasuk `SFX_PropellerDash_Loop.wav` Heavy Aerodynamic Whoosh Loop `176,444` bytes & `SFX_Splash_LogoReveal.wav`) dari `assets/Export_Audio_LaptopB.zip` ke `Assets/Audio/`.
- Membangun `Fanamir_AudioMixer.mixer` (`Master -> BGM ["BgmVolume"], SFX ["SfxVolume"] -> UI`) serta prefab persistent `Assets/Resources/Fanamir_AudioManager.prefab`.
- Mengimplementasikan `AudioManager.cs` dengan Dual-Channel Cross-Fade, **Dual BGM Dropdowns** (`Flight 0..11` & `Menu 0..5`) persisten di `PlayerPrefs`, **Pill Quick BGM Switcher** di Navbar Lobby (`SCREEN 00`), SFX Splash Screen + branding `"MADE WITH UNITY"`, serta menghapus seluruh penanda `// TODO [SYNC-AUDIO]`.

## 2. Keputusan Desain / Arsitektur / Teknis
- **Distribusi Runtime Aman Editor & Build (`Resources/Fanamir_AudioManager.prefab`):** Seluruh 37 klip audio dan `Fanamir_AudioMixer.mixer` berada di `Assets/Audio/`, sedangkan referensi ter-serialisasi disimpan di `Assets/Resources/Fanamir_AudioManager.prefab`. `DragonGameSettings.EnsurePersistentSessionHost()` memastikan `AudioManager.EnsureInstance()` aktif sejak `BeforeSceneLoad`.
- **Dual BGM Selection Persisten (`DragonGameSettings.cs`):** Menggantikan `BgmThemeStyle` dengan dua preferensi independen yang tersimpan di `PlayerPrefs`:
  - `PrefBgmFlightSelection = "Fanamir_BgmFlightIndex"` (`BgmFlightSelection` `0..11`: `[0] Adaptif (Otomatis)`, `[1..11] BGM 1..BGM 11`) + event `OnBgmFlightSelectionChanged`.
  - `PrefBgmMenuSelection = "Fanamir_BgmMenuIndex"` (`BgmMenuSelection` `0..5`: `[0..5] BGM Menu 1..BGM Menu 6`) + event `OnBgmMenuSelectionChanged`.
- **Adaptasi Ketinggian/Lokasi vs Track Terkunci (`AudioManager.cs`):**
  - Mode `[0] Adaptif` mengevaluasi kondisi terbang (`Y > 120m` $\rightarrow$ `BGM_Flight_AirPrelude`, Center Peak `< 140m` $\rightarrow$ `BGM_Flight_SkyeCuillin`, speed $\ge 20\text{ m/s}$ $\rightarrow$ `BGM_Flight_LordOfTheLand`, `Y < 32m` $\rightarrow$ `BGM_Flight_ClearWaters`, default `BGM_Flight_CelticImpulse`).
  - Pilihan `[1..11]` mengunci BGM In-Game ke track `_flightBgmClips[index - 1]` (termasuk lagu krisis desa bila dipilih manual).
- **Quick BGM Menu Switcher (`LobbyNavigationManager.cs` & `MainMenu_Lobby.unity`):** Tombol pill `SharedTopNavbar/NavBgmMenuPill` (`[🎵 BGM Menu: BGM 1 ▼]`, `198×46 px` di `(-442, 0)`) men-cycle indeks `0..5` dan sinkron dua arah dengan dropdown `Row_BgmMenuSelection` di `SettingsPanelUI.cs`.
- **Hysteresis Radius Krisis Desa:** `VillageZoneController.cs` memakai `< 250 m` (enter crisis) dan `> 280 m` (restore peaceful BGM).

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Aset Audio (`AR Dragon Sky Guardian/Assets/Audio/`):**
  - 14 BGM MP3 (`7` `Ghibli_Fantasy`, `6` `Cinematic_Tribal`, `1` `Celebration`) & 23 SFX WAV (`8` `Dragon` termasuk `SFX_PropellerDash_Loop.wav` `176,444` bytes, `6` `Village`, `9` `UI` termasuk `SFX_Splash_LogoReveal.wav`).
  - `Assets/Audio/Fanamir_AudioMixer.mixer` (`Master -> BGM [BgmVolume], SFX [SfxVolume] -> UI`).
  - `Assets/Resources/Fanamir_AudioManager.prefab` (`37/37` klip & mixer terpasang).
- **Skrip Editor:**
  - `Assets/Editor/AudioImporterSetupUtility.cs`, `Assets/Editor/AudioSystemVerificationRunner.cs`, & `Assets/Editor/UISceneAndPrefabBuilder.cs` (`Splash_UnityBranding` `"MADE WITH UNITY"`, `PF_SettingsPanel.prefab` `SettingsCard` `960×656 px` dengan `Row_BgmFlightSelection` & `Row_BgmMenuSelection` `SolidCameraDropdown`, `MainMenu_Lobby.unity` dengan `NavBgmMenuPill` & `NavCamBadge` `264×46` di `(-168, 0)`, `BuildAll()`).
- **Skrip Runtime:**
  - `Assets/_Scripts/Audio/AudioManager.cs` (`_menuBgmClips[6]`, `_flightBgmClips[11]`, `_victoryBgmClip`, `_crisisBgmClips[3]`, `PlayMenuBGM`, `SetBgmFlightSelection`, `SetBgmMenuSelection`, `RestorePeacefulBGM`, `CrossfadeToCrisis`, `PlayCleansedStinger`, `PlayVictoryCelebration`, `PlaySplashLogoReveal`).
  - `Assets/_Scripts/UI/DragonGameSettings.cs` (`PrefBgmFlightSelection`, `PrefBgmMenuSelection`, `OnBgmFlightSelectionChanged`, `OnBgmMenuSelectionChanged`, `0` `// TODO [SYNC-AUDIO]`).
  - `Assets/_Scripts/UI/SettingsPanelUI.cs` (`bgmFlightDropdown`, `bgmMenuDropdown`), `LobbyNavigationManager.cs` (`quickBgmMenuButton`, `quickBgmMenuLabel`), `BootFlowController.cs` (`PlaySplashLogoReveal()` & `PlayMenuBGM(DragonGameSettings.BgmMenuSelection)`), `FinishModalUI.cs`, `VillageChallengeUI.cs`, `CalibrationPanelUI.cs`, `MissionsPanelUI.cs`, `PauseModalUI.cs`.
  - `Assets/_Scripts/Controllers/DragonBodyAnimator.cs`, `DragonFlightController.cs`, `Gameplay/VillageZoneController.cs`, `Gameplay/GameManager.cs`.

## 4. Detail Penting yang Harus Diingat
- **Profil AudioImporter (`37` klip):** `14` BGM MP3 (`Streaming`, `Vorbis`, `0.70f`), `15` SFX pendek `< 2s` (`DecompressOnLoad`, `PCM`), `8` SFX loop/stinger/reveal `>= 2s` (`CompressedInMemory`, `Vorbis`, `0.85f`).
- **Exposed Parameters AudioMixer:** `"BgmVolume"` dan `"SfxVolume"` ($\text{dB} = 20 \cdot \log_{10}(\max(\text{linear}, 0.0001))$).
- **Hasil Verifikasi Play Mode:**
  - `MainMenu_Lobby.unity`: `Splash='MADE WITH UNITY' | QuickPill & DualDropdowns=PASS | ALL AUDIO SYSTEM CHECKS PASSED (37/37 clips, Mixer buses, dB curve, Dual BGM Dropdowns, Crossfade, & SFX triggers 100%)`.
  - `MainIsland_Gameplay.unity`: `adaptive=BGM_Flight_AirPrelude | bgm1=BGM_Flight_CelticImpulse | bgm11=BGM_Crisis_Solaria_CurseOfTheScarab | dashPlaying=True (2.0s) | ALL MOMENTUM & FLIGHT PHYSICS CHECKS PASSED (100%)`.
  - `console_get`: `0` console errors, `0` console warnings; `// TODO [SYNC-AUDIO]` count = `0`.

## 5. Tugas Tersisa & Langkah Selanjutnya
- Menyelesaikan **Sesi 5 Lanjutan**: Build Windows Standalone (`.exe`) dan skrip launcher `start_game.bat`.

---
*Navigasi:* [[00 - Overview]] | [[Tasks]] | [[Devlog]] | [[laptop-a/gameplay-mechanics/arsitektur_sistem_audio]]
