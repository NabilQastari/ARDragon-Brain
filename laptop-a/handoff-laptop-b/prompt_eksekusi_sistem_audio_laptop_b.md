---
title: "Master Prompt Eksekusi Sistem Audio Fanamir (Laptop B)"
tags:
  - handoff
  - prompt
  - audio
  - laptop-b
  - unity
status: ready-to-copy
updated: 2026-11-24
---

# 📋 Master Prompt Eksekusi Sistem Audio Fanamir (Laptop B)

> **Untuk Pengguna / AI Agent di Laptop B:**
> Seluruh aset audio lengkap (7 BGM MP3 orkestra/tribal + 22 SFX WAV 44.1kHz) telah selesai diproduksi dan siap di `assets/audio/`. Salin seluruh prompt di bawah ini ke sesi chat **Laptop B** untuk mengimplementasikan sistem audio terpadu di Unity 6 URP!

---

```markdown
Halo! Ini adalah instruksi resmi dari Laptop A untuk mengimplementasikan **Sistem Audio Lengkap (BGM Dinamis & SFX)** di Unity 6 URP (`Assets/`), menyambungkan `AudioManager.cs`, menghubungkan hook `DragonGameSettings.OnBgmVolumeChanged` & `OnSfxVolumeChanged`, dan menghapus penanda lama `// TODO [SYNC-AUDIO]`.

Rujukan arsitektur resmi: `brain/laptop-a/gameplay-mechanics/arsitektur_sistem_audio.md`.

---

### 🎯 TUGAS KITA DI SESI INI:
1. **Sinkronisasi / Impor Aset Audio ke Unity (`Assets/Audio/`):**
   - Salin seluruh folder `assets/audio/` dari root workspace ke `AR Dragon Sky Guardian/Assets/Audio/`:
     - `Assets/Audio/BGM/Ghibli_Fantasy/` (3 lagu)
     - `Assets/Audio/BGM/Cinematic_Tribal/` (4 lagu)
     - `Assets/Audio/BGM/Celebration/` (1 lagu)
     - `Assets/Audio/SFX/Dragon/` (8 file WAV)
     - `Assets/Audio/SFX/Village/` (6 file WAV)
     - `Assets/Audio/SFX/UI/` (8 file WAV)
   - Konfigurasi Audio Importer di Unity:
     - BGM: *Compressed In Memory* / *Vorbis 70%*, Streaming.
     - SFX Pendek (< 2s): *Decompress On Load*, PCM 16-bit.
     - SFX Looping: *Compressed In Memory*, Vorbis 85%.

2. **Buat `Fanamir_AudioMixer.mixer` (`Assets/Audio/`):**
   - Buat bus hierarchy:
     - `Master` (0 dB)
       - `BGM` -> Expose parameter `"BgmVolume"`
       - `SFX` -> Expose parameter `"SfxVolume"`
       - `UI` (anak dari SFX atau Master)
   - Expose parameter dinamai persis: `BgmVolume` dan `SfxVolume`.

3. **Implementasikan `AudioManager.cs` (`Assets/_Scripts/Audio/`):**
   - Singleton pattern (`DontDestroyOnLoad`), namespace `DragonWingAR.Audio`.
   - Miliki 2 `AudioSource` BGM (`bgmSourceA` dan `bgmSourceB`) dengan output ke bus `BGM`.
   - Implementasikan coroutine `CrossfadeBGM(AudioClip newClip, float duration = 1.8f)`:
     - Fade-out track aktif dan fade-in track baru secara halus dengan kurva kuadratik/smoothstep.
   - Sambungkan event reaktif ke `DragonGameSettings`:
     ```csharp
     DragonGameSettings.OnBgmVolumeChanged += SetBgmVolume;
     DragonGameSettings.OnSfxVolumeChanged += SetSfxVolume;
     ```
   - Konversi linier ke dB:
     ```csharp
     float db = 20f * Mathf.Log10(Mathf.Max(linearVolume, 0.0001f));
     audioMixer.SetFloat("BgmVolume", db);
     ```
   - Siapkan pool / metode `PlaySFX(AudioClip clip, float volume = 1f)` dan `PlaySFX3D(AudioClip clip, Vector3 position, float volume = 1f)`.
   - Sediakan metode looping SFX: `PlayLooping(AudioClip clip, ref AudioSource source, float targetVolume)`.

4. **Sambungkan Hook Audio ke Skrip Eksisting:**
   - **`DragonFlightController.cs`:**
     - Kepakan sayap: mainkan `SFX_WingFlap.wav` saat downstroke kepakan sayap (`IsFlapping == true`).
     - Dive rush: aktifkan looping `SFX_DiveRush_Loop.wav` saat `#13 Tuck to Dive` dengan volume proporsional terhadap `Airspeed` (14 s.d. 30 m/s).
     - Propeller Dash: aktifkan looping `SFX_PropellerDash_Loop.wav` selama `#14 Propeller Dash` aktif.
     - Launch thrust: mainkan `SFX_Launch_Whoosh.wav` saat keluar dari auto-hover.
   - **`VillageZoneController.cs`:**
     - Transisi krisis: saat jarak naga ke desa $< 250\text{m}$ dan desa belum pulih, panggil `AudioManager.Instance.CrossfadeToCrisis(biomeType)`:
       - Zephyria: `BGM_Crisis_Zephyria_HimalayanAtmosphere.mp3`
       - Pyrewood: `BGM_Crisis_Pyrewood_Firesong.mp3`
       - Solaria: `BGM_Crisis_Solaria_DesertCity.mp3`
     - Saat keluar radius krisis ($> 280\text{m}$), cross-fade kembali ke BGM Free Roam santai (`BGM_Flight_CelticImpulse.mp3`).
     - Danau Zephyria: looping `SFX_Lake_Siphon_Loop.wav` saat proses pengisian air.
     - Pyrewood Hutan: looping `SFX_Pyrewood_WaterBreath_Loop.wav` dan trigger `SFX_Pyrewood_Extinguish_Steam.wav` saat pemadaman.
     - Solaria Sky Altar: looping `SFX_Solaria_DewChimes.wav` saat pose 11 embun suci.
     - Momen 100% pulih: mainkan stinger `SFX_Village_Cleansed_Stinger.wav` dan cross-fade kembali ke BGM santai.
     - Selebrasi akhir: mainkan `BGM_Victory_HeroicAge.mp3` dan trigger auman kemenangan `DragonBodyAnimator.PlayVictoryRoar()`.
   - **`GameManager.cs`:**
     - Fase countdown: mainkan `SFX_Countdown_Beep.wav` saat 3.. 2.. 1.. dan `SFX_Countdown_Go.wav` saat TERBANG!
   - **`FinishModalUI.cs`:**
     - Pengisian bintang: mainkan `SFX_Star_01.wav`, `SFX_Star_02.wav`, `SFX_Star_03.wav` berurutan sesuai bintang yang didapat.
   - **`LobbyNavigationManager.cs` & `CalibrationPanelUI.cs`:**
     - Klik tombol: mainkan `SFX_UI_Click.wav`.
     - Pose terdeteksi (badge hijau): mainkan `SFX_UI_PoseLock.wav`.

5. **Verifikasi Play Mode:**
   - Jalankan Play Mode dari `MainMenu_Lobby.unity` $\to$ masuk ke `MainIsland_Gameplay.unity`.
   - Pastikan BGM Lobby berputar, transisi ke scene gameplay memutar BGM jelajah, slider volume Settings mengatur volume secara nyata, dan seluruh SFX terdengar tanpa error console (`0` errors).
```

---
*Navigasi:* [[00 - Overview]] | [[Tasks]] | [[Devlog]] | [[laptop-a/gameplay-mechanics/arsitektur_sistem_audio]]
