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
date: 2026-11-24
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[laptop-a/gameplay-mechanics/arsitektur_sistem_audio]]"
  - "[[laptop-a/handoff-laptop-b/prompt_eksekusi_sistem_audio_laptop_b]]"
---

# 🎵 Implementasi Sistem Audio Terpadu Fanamir

## 1. Tujuan & Konteks Sesi
Mengimplementasikan sistem audio terpadu *Fanamir: The Sky Guardian* di Laptop B (`AR Dragon Sky Guardian`, Unity 6 `6000.3.7f1` URP) sesuai dokumen arsitektur [[laptop-a/gameplay-mechanics/arsitektur_sistem_audio]]:
- Menyalin dan mengimpor 30 berkas audio (8 BGM MP3 + 22 SFX WAV) dari paket `assets/audio.zip` ke `Assets/Audio/`.
- Membangun `Fanamir_AudioMixer.mixer` beserta bus `Master`, `BGM`, `SFX`, dan `UI`.
- Mengimplementasikan `AudioManager.cs` dengan Dual-Channel Cross-Fade, menghubungkan slider `DragonGameSettings`, menyambungkan seluruh hook gameplay & UI, serta menghapus seluruh penanda `// TODO [SYNC-AUDIO]`.

## 2. Keputusan Desain / Arsitektur / Teknis
- **Distribusi Runtime Aman Editor & Build (`Resources/Fanamir_AudioManager.prefab`):** Seluruh 30 klip audio dan `Fanamir_AudioMixer.mixer` tetap disimpan di `Assets/Audio/` sesuai spesifikasi, sementara referensi ter-serialisasi disimpan di prefab `Assets/Resources/Fanamir_AudioManager.prefab`. `DragonGameSettings.EnsurePersistentSessionHost()` memanggil `AudioManager.EnsureInstance()` sejak `BeforeSceneLoad` agar BGM & SFX tersedia di seluruh scene maupun standalone build.
- **Anti Fake-Null Unity:** Pada `AudioManager.cs`, seluruh pengecekan `UnityEngine.Object` (`AudioSource`, `AudioMixerGroup`, `AudioClip`, `DragonBodyAnimator`) menghindari operator C# `?.` / `??` agar tidak terkena *fake-null* `MissingComponentException` atau `UnassignedReferenceException`.
- **Dual-Channel Cross-Fade & Ducking:** `_bgmSourceA` dan `_bgmSourceB` bertransisi dengan kurva *smoothstep* berbasis `Time.unscaledDeltaTime` (aman saat pause/transisi), ditambah coroutine *ducking* `0.25x` saat `SFX_Village_Cleansed_Stinger` diputar.
- **Hysteresis Radius Krisis Desa:** `VillageZoneController.cs` memicu `CrossfadeToCrisis(BiomeType)` saat jarak XZ ke desa belum pulih `< 250 m` (`CrisisBgmEnterRadius`) dan memanggil `RestorePeacefulBGM()` baru setelah menjauh `> 280 m` (`CrisisBgmExitRadius`), mencegah pergantian lagu berulang di perbatasan zona.
- **Singleton `DragonBodyAnimator.Instance`:** Menambahkan properti `DragonBodyAnimator.Instance` dengan reset `SubsystemRegistration` agar `AudioManager.PlayVictoryCelebration()` dapat memanggil `PlayVictoryRoar()` secara langsung.

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Aset Audio (`assets/audio/` & `AR Dragon Sky Guardian/Assets/Audio/`):**
  - 8 BGM MP3 (`BGM/Ghibli_Fantasy/`, `BGM/Cinematic_Tribal/`, `BGM/Celebration/`).
  - 22 SFX WAV (`SFX/Dragon/`, `SFX/Village/`, `SFX/UI/`).
  - `Assets/Audio/Fanamir_AudioMixer.mixer` (`Master -> BGM [BgmVolume], SFX [SfxVolume] -> UI`).
  - `Assets/Resources/Fanamir_AudioManager.prefab` (terhubung ke 30/30 klip dan mixer).
- **Skrip Editor:**
  - `Assets/Editor/AudioImporterSetupUtility.cs` (`AssetPostprocessor` + menu `Setup All Audio Importers`, `Build Fanamir AudioMixer`, `Build AudioManager Prefab`).
  - `Assets/Editor/AudioSystemVerificationRunner.cs` (menu `DragonWingAR/Verify/Run Audio System Verification`).
- **Skrip Runtime:**
  - `Assets/_Scripts/Audio/AudioManager.cs` (`DragonWingAR.Audio`, `BiomeType`, `[DefaultExecutionOrder(-50)]`).
  - `Assets/_Scripts/UI/DragonGameSettings.cs` & `SettingsPanelUI.cs` (penghapusan 7 penanda `// TODO [SYNC-AUDIO]` + inisialisasi `AudioManager.EnsureInstance()`).
  - `Assets/_Scripts/Controllers/DragonBodyAnimator.cs` (`Instance`), `DragonFlightController.cs` (flap normal/heavy, DiveRush loop, PropellerDash loop, LaunchWhoosh, Footstep 01/02), `Gameplay/VillageZoneController.cs` (krisis 250m/280m, WindGust, LakeSiphon, WaterBreath, ExtinguishSteam, DewChimes, CleansedStinger, VictoryCelebration), `Gameplay/GameManager.cs` (Countdown Beep/Go), `UI/FinishModalUI.cs` (`SFX_Star_01..03` + click), `UI/VillageChallengeUI.cs` (`SFX_UI_PoseLock`), `UI/CalibrationPanelUI.cs`, `UI/LobbyNavigationManager.cs`, `UI/MissionsPanelUI.cs`, & `UI/PauseModalUI.cs`.

## 4. Detail Penting yang Harus Diingat
- **Profil AudioImporter:** BGM MP3 (`Streaming`, `Vorbis`, `0.70f`, `loadInBackground = true`); 15 SFX pendek `< 2s` (`DecompressOnLoad`, `PCM`); 7 SFX loop/stinger `>= 2s` (`CompressedInMemory`, `Vorbis`, `0.85f`).
- **Exposed Parameters AudioMixer:** Nama wajib persis `"BgmVolume"` dan `"SfxVolume"` dengan rumus $\text{dB} = 20 \cdot \log_{10}(\max(\text{linear}, 0.0001))$. Catatan Unity Editor: `AudioMixer.SetFloat()` mengembalikan `false` saat Edit Mode dan aktif penuh saat Play Mode / runtime.
- **Hasil Verifikasi:** `AudioSystemVerificationRunner` (di `MainMenu_Lobby` & `MainIsland_Gameplay`), uji transisi `MainMenu_Lobby -> MainIsland_Gameplay`, uji proximity hysteresis `200m / 265m / 300m`, `MomentumFlightVerificationRunner`, dan `Session5GameLoopVerificationRunner` seluruhnya **100% PASS** (`0` console errors, `0` console warnings).

## 5. Tugas Tersisa & Langkah Selanjutnya
- Menyelesaikan sisa **Sesi 5 Lanjutan**: Build Windows Standalone (`.exe`) dan skrip launcher terpadu `start_game.bat` untuk menjalankan game dan proses Python CV secara bersamaan.

---
*Navigasi:* [[00 - Overview]] | [[Tasks]] | [[Devlog]] | [[laptop-a/gameplay-mechanics/arsitektur_sistem_audio]]
