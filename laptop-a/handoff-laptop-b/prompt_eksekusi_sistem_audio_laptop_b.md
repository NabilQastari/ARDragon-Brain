---
title: "Master Prompt Eksekusi Sistem Audio Fanamir (Laptop B)"
tags:
  - handoff
  - prompt
  - audio
  - laptop-b
  - unity
status: ready-to-copy
updated: 2026-11-25
---

# 📋 Master Prompt Eksekusi Sistem Audio Fanamir (Laptop B)

> **Untuk Pengguna / AI Agent di Laptop B:**
> Seluruh suite audio resmi (**14 BGM MP3 orkestra Ghibli & tribal + 23 SFX WAV 44.1kHz 16-bit — total 37 klip**) telah selesai diproduksi dan dikemas rapi dalam:
> - Paket ZIP: [`assets/Export_Audio_LaptopB.zip`](file:///e:/Semester%205/Game/AR%20Dragon/assets/Export_Audio_LaptopB.zip) (~91.6 MB)
> - Folder siap salin: [`assets/Export_Audio_LaptopB/`](file:///e:/Semester%205/Game/AR%20Dragon/assets/Export_Audio_LaptopB/) lengkap dengan skrip batch otomatis `copy_to_unity.bat`.
>
> Salin seluruh blok instruksi di bawah ini ke sesi chat **Laptop B** untuk mengimplementasikan sistem audio terpadu di Unity 6 URP!

---

```markdown
Halo! Ini adalah sesi eksekusi implementasi **Sistem Audio Terpadu Fanamir (BGM Dinamis 14 Track, Transisi Cross-Fade, Persistensi Settings, & Suite 23 SFX Lengkap)** di **Laptop B** (`AR Dragon Sky Guardian`).

Sesuai aturan `AGENTS.md` dan dokumen arsitektur resmi di `brain/laptop-a/gameplay-mechanics/arsitektur_sistem_audio.md`:
1. Jalankan `git -C brain pull` untuk menyambung konteks terbaru dari Laptop A.
2. Baca `brain/00 - Overview.md`, `brain/laptop-b/Tasks.md`, `brain/laptop-b/Devlog.md`, dan `brain/laptop-a/gameplay-mechanics/arsitektur_sistem_audio.md`.
3. Ingat aturan anti-collision: Laptop B hanya menulis/mengedit catatan di `brain/laptop-b/` (read-only untuk `laptop-a/`).

---

## 🎯 TUJUAN & TARGET SESI INI:
Mengimplementasikan sistem audio terpadu di Unity 6 URP (`Assets/`), membangun `Fanamir_AudioMixer.mixer`, mengimplementasikan `AudioManager.cs` lengkap dengan *Dual-Channel Cross-Fade*, menghubungkan slider volume `DragonGameSettings.OnBgmVolumeChanged` & `OnSfxVolumeChanged`, menghubungkan sistem preferensi gaya musik BGM (`BgmThemeStyle`) yang tersimpan persisten di `PlayerPrefs`, mengintegrasikan efek suara Splash Screen (`SFX_Splash_LogoReveal.wav`) dan branding "MADE WITH UNITY" pada `SCREEN 09`, menghubungkan seluruh hook audio pada skrip gameplay & UI eksisting, dan menghapus penanda lama `// TODO [SYNC-AUDIO]`.

---

## 🛠️ PANDUAN EKSEKUSI TEKNIS LANGKAH DEMI LANGKAH:

### LANGKAH 1: Sinkronisasi Aset Audio ke Unity (`Assets/Audio/`)
Tersedia dua opsi praktis untuk memasang suite audio resmi (14 BGM MP3 + 23 SFX WAV = 37 berkas):
* **Opsi Otomatis:** Buka folder `assets/Export_Audio_LaptopB/` di root workspace lalu jalankan skrip **`copy_to_unity.bat`**. Seluruh hierarki audio akan disalin ke `AR Dragon Sky Guardian/Assets/Audio/`.
* **Opsi Ekstraksi ZIP:** Ekstrak arsip `assets/Export_Audio_LaptopB.zip` dan letakkan folder `Assets/Audio/` ke dalam proyek Unity.

**Daftar Lengkap 37 Aset Audio:**
- `Assets/Audio/BGM/Ghibli_Fantasy/` (6 MP3):
  - `BGM_Lobby_FolkRound.mp3`
  - `BGM_Lobby_VillageConsort.mp3`
  - `BGM_Flight_CelticImpulse.mp3`
  - `BGM_Flight_AirPrelude.mp3`
  - `BGM_Flight_LordOfTheLand.mp3`
  - `BGM_Flight_TellerOfTheTales.mp3`
  - `BGM_Flight_ClearWaters.mp3`
- `Assets/Audio/BGM/Cinematic_Tribal/` (6 MP3):
  - `BGM_Flight_SkyeCuillin.mp3`
  - `BGM_Crisis_Zephyria_HimalayanAtmosphere.mp3`
  - `BGM_Crisis_Pyrewood_Firesong.mp3`
  - `BGM_Crisis_Pyrewood_Ritual.mp3`
  - `BGM_Crisis_Solaria_DesertCity.mp3`
  - `BGM_Crisis_Solaria_CurseOfTheScarab.mp3`
- `Assets/Audio/BGM/Celebration/` (1 MP3):
  - `BGM_Victory_HeroicAge.mp3`
- `Assets/Audio/SFX/Dragon/` (8 WAV):
  - `SFX_WingFlap.wav`, `SFX_WingFlap_Heavy.wav`, `SFX_DiveRush_Loop.wav`, `SFX_PropellerDash_Loop.wav`, `SFX_Launch_Whoosh.wav`, `SFX_Dragon_Footstep_01.wav`, `SFX_Dragon_Footstep_02.wav`, `SFX_Dragon_Roar_Victory.wav`
- `Assets/Audio/SFX/Village/` (6 WAV):
  - `SFX_Zephyria_WindGust.wav`, `SFX_Lake_Siphon_Loop.wav`, `SFX_Pyrewood_WaterBreath_Loop.wav`, `SFX_Pyrewood_Extinguish_Steam.wav`, `SFX_Solaria_DewChimes.wav`, `SFX_Village_Cleansed_Stinger.wav`
- `Assets/Audio/SFX/UI/` (9 WAV):
  - `SFX_Splash_LogoReveal.wav` (Logo reveal chime & swell), `SFX_UI_Click.wav`, `SFX_UI_Hover.wav`, `SFX_UI_PoseLock.wav`, `SFX_Countdown_Beep.wav`, `SFX_Countdown_Go.wav`, `SFX_Star_01.wav`, `SFX_Star_02.wav`, `SFX_Star_03.wav`

2. Buat skrip Editor pembantu `Assets/Editor/AudioImporterSetupUtility.cs` (mirip `UISpriteSetupUtility.cs`) untuk mengonfigurasi otomatis seluruh audio importer:
   - **BGM (MP3):** `loadType = AudioClipLoadType.Streaming`, `compressionFormat = AudioCompressionFormat.Vorbis`, `quality = 0.70f`.
   - **SFX Pendek (< 2s):** `loadType = AudioClipLoadType.DecompressOnLoad`, `compressionFormat = AudioCompressionFormat.PCM`.
   - **SFX Loop / Stinger (> 2s):** `loadType = AudioClipLoadType.CompressedInMemory`, `compressionFormat = AudioCompressionFormat.Vorbis`, `quality = 0.85f`.

---

### LANGKAH 2: Pembuatan `Fanamir_AudioMixer.mixer`
1. Di folder `Assets/Audio/`, buat aset AudioMixer bernama `Fanamir_AudioMixer.mixer`.
2. Susun hirarki bus:
   ```text
   Master (0 dB)
     ├── BGM (0 dB)  --> Expose Parameter: "BgmVolume"
     └── SFX (0 dB)  --> Expose Parameter: "SfxVolume"
           └── UI (0 dB)
   ```
3. Pastikan kedua parameter ter-expose dinamai persis: **`BgmVolume`** dan **`SfxVolume`**.

---

### LANGKAH 3: Implementasi `AudioManager.cs` (`Assets/_Scripts/Audio/`)
Buat skrip `AudioManager.cs` (namespace `DragonWingAR.Audio`, `[DefaultExecutionOrder(-50)]`):
1. **Pola Singleton & Persistensi:**
   - Singleton `Instance` dengan `DontDestroyOnLoad(gameObject)`.
2. **Komponen AudioSource Internal:**
   - 2 AudioSource untuk BGM: `_bgmSourceA` dan `_bgmSourceB` (output ke bus `BGM`, `loop = true`).
   - 1 AudioSource 2D untuk UI/SFX one-shot: `_sfx2DSource` (output ke bus `SFX`).
   - Sediakan referensi AudioSource loop untuk naga & desa: `_diveRushSource`, `_propellerDashSource`, `_villageLoopSource`.
3. **Penyambungan Reaktif ke `DragonGameSettings` & Pengaturan Gaya Musik (Hapus `// TODO [SYNC-AUDIO]`):**
   - Di `DragonGameSettings.cs`:
     ```csharp
     public enum BgmThemeStyle {
         Adaptive = 0,        // Otomatis berganti sesuai lokasi & ketinggian (Default)
         GhibliFantasy = 1,   // Selalu alunan ceria Ghibli/Genshin (Celtic Impulse / Air Prelude)
         CinematicTribal = 2  // Selalu alunan etnik highland & ambient mistis (Skye Cuillin)
     }

     public const string PrefBgmThemeStyle = "Fanamir_BgmThemeStyle";
     public static event Action<BgmThemeStyle> OnBgmThemeStyleChanged;

     public static BgmThemeStyle CurrentBgmThemeStyle {
         get => (BgmThemeStyle)PlayerPrefs.GetInt(PrefBgmThemeStyle, 0);
         set {
             PlayerPrefs.SetInt(PrefBgmThemeStyle, (int)value);
             PlayerPrefs.Save();
             OnBgmThemeStyleChanged?.Invoke(value);
         }
     }
     ```
   - Di `AudioManager.cs`:
     - Di `OnEnable()` / `Start()`:
       ```csharp
       DragonGameSettings.OnBgmVolumeChanged += SetBgmVolume;
       DragonGameSettings.OnSfxVolumeChanged += SetSfxVolume;
       DragonGameSettings.OnBgmThemeStyleChanged += OnBgmThemeStyleChanged;
       SetBgmVolume(DragonGameSettings.BgmVolume);
       SetSfxVolume(DragonGameSettings.SfxVolume);
       ```
     - Di `OnDisable()`: Unsubscribe seluruh event.
     - Fungsi konversi linier ke dB:
       ```csharp
       public void SetBgmVolume(float linear) {
           float db = 20f * Mathf.Log10(Mathf.Max(linear, 0.0001f));
           audioMixer.SetFloat("BgmVolume", db);
       }
       public void SetSfxVolume(float linear) {
           float db = 20f * Mathf.Log10(Mathf.Max(linear, 0.0001f));
           audioMixer.SetFloat("SfxVolume", db);
       }
       ```
     - Handler pergantian gaya musik seketika:
       ```csharp
       private void OnBgmThemeStyleChanged(BgmThemeStyle newStyle) {
           if (_isPeacefulFlightActive) {
               RestorePeacefulBGM(1.5f);
           }
       }
       ```
4. **Sistem Cross-Fade BGM Dinamis:**
   - Coroutine `CrossfadeBGM(AudioClip newClip, float duration = 1.8f)`:
     - Jika track sama, abaikan.
     - Lakukan lerp volume track aktif $1.0 \to 0.0$ dan track baru $0.0 \to 1.0$ secara halus dengan kurva kuadratik/smoothstep, lalu hentikan track lama.
   - Metode `CrossfadeToCrisis(BiomeType biome)`:
     - Zephyria: `BGM_Crisis_Zephyria_HimalayanAtmosphere`
     - Pyrewood: `BGM_Crisis_Pyrewood_Firesong`
     - Solaria: `BGM_Crisis_Solaria_DesertCity`
   - Metode `RestorePeacefulBGM(float duration = 2.0f)`:
     - Tentukan lagu berdasarkan `DragonGameSettings.CurrentBgmThemeStyle`:
       - `Adaptive`: jika ketinggian naga $Y > 120\text{m}$, gunakan `BGM_Flight_AirPrelude`; jika dekat Center Peak, gunakan `BGM_Flight_SkyeCuillin`; selain itu `BGM_Flight_CelticImpulse`.
       - `GhibliFantasy`: gunakan `BGM_Flight_CelticImpulse` (atau `Air Prelude` saat tinggi).
       - `CinematicTribal`: gunakan `BGM_Flight_SkyeCuillin`.
     - Lakukan cross-fade halus ke track terpilih.
   - Metode `PlayCleansedStinger()`:
     - Mainkan `SFX_Village_Cleansed_Stinger.wav` (one-shot berprioritas tinggi) sambil ducking BGM sejenak, lalu restore peaceful BGM.
   - Metode `PlayVictoryCelebration()`:
     - Mainkan `BGM_Victory_HeroicAge.mp3` dan panggil `DragonBodyAnimator.Instance.PlayVictoryRoar()`.

---

### LANGKAH 4: Sambungkan Hook Audio ke Skrip Eksisting

0. **`BootFlowController.cs` (`SCREEN 09` - Splash Screen):**
   - **Audio Reveal Logo:** Saat coroutine boot memulai transisi fade-in logo utama Fanamir, mainkan `SFX_Splash_LogoReveal.wav` via `AudioManager.Instance.PlaySFX(...)` untuk memberikan kesan megah, hangat, dan sinematik.
   - **Engine Branding:** Pastikan pada canvas `SCREEN 09` terdapat teks / lencana **`MADE WITH UNITY`** di bagian bawah layar (posisi `y ≈ 966`, font netral uppercase, warna `#8C7F70`) berdampingan dengan subtitle *WEBCAM EXERGAMING · BODY-FLIGHT ADVENTURE*.

1. **`DragonFlightController.cs`:**
   - **Kepakan Sayap:** Mainkan `SFX_WingFlap.wav` saat downstroke kepakan sayap normal (`IsFlapping == true`), atau `SFX_WingFlap_Heavy.wav` saat `#06B Steep Climb` / takeoff.
   - **Tuck Dive Rush:** Saat `#13 Tuck to Dive` aktif, nyalakan `SFX_DiveRush_Loop.wav` pada looping source dengan volume proporsional terhadap `Airspeed / maxAirspeed` ($14\text{--}30\text{ m/s}$). Matikan/fade-out saat keluar dari Tuck Dive.
   - **Propeller Dash:** Nyalakan `SFX_PropellerDash_Loop.wav` selama `#14 Propeller Dash` aktif.
   - **Launch Thrust:** Mainkan `SFX_Launch_Whoosh.wav` saat lepas dari auto-hover / escape.
   - **Langkah Kaki di Darat:** Mainkan selang-seling `SFX_Dragon_Footstep_01/02.wav` saat siklus jalan darat `#02 Grounded Walk`.

2. **`VillageZoneController.cs`:**
   - **Deteksi Radius Krisis (< 250m):**
     - Pada `Update()`, jika jarak naga ke desa $< 250\text{m}$ dan desa belum pulih (`!isRestored`), panggil `AudioManager.Instance.CrossfadeToCrisis(biome)`.
     - Jika naga menjauh $> 280\text{m}$ (hysteresis 30m), panggil `AudioManager.Instance.RestorePeacefulBGM()`.
   - **Aksi Tantangan 3 Desa:**
     - Zephyria: Mainkan `SFX_Zephyria_WindGust.wav` setiap kepakan sayap berhasil dihitung.
     - Danau Zephyria (Pyrewood Stage 1): Nyalakan looping `SFX_Lake_Siphon_Loop.wav` selama pengisian air danau.
     - Pyrewood Hutan (Stage 2): Nyalakan looping `SFX_Pyrewood_WaterBreath_Loop.wav` saat buka mulut menyemburkan air, dan mainkan `SFX_Pyrewood_Extinguish_Steam.wav` saat api padam.
     - Solaria Sky Altar: Nyalakan looping `SFX_Solaria_DewChimes.wav` selama pose 11 embun suci ditahan.
   - **Momen Pemulihan Desa ($100\%$):**
     - Panggil `AudioManager.Instance.PlayCleansedStinger()`.
   - **Momen Kemenangan Seluruh Desa:**
     - Panggil `AudioManager.Instance.PlayVictoryCelebration()`.

3. **`GameManager.cs`:**
   - Pada coroutine `CountdownStart`:
     - Saat angka `3..`, `2..`, `1..` muncul: mainkan `SFX_Countdown_Beep.wav`.
     - Saat kata `"TERBANG!"` muncul: mainkan `SFX_Countdown_Go.wav`.

4. **`FinishModalUI.cs`:**
   - Pada animasi sekuensial bintang (`ShowStarProgressSequence` / `0.4s` interval):
     - Bintang 1: mainkan `SFX_Star_01.wav`.
     - Bintang 2: mainkan `SFX_Star_02.wav`.
     - Bintang 3: mainkan `SFX_Star_03.wav`.

5. **`LobbyNavigationManager.cs` & `CalibrationPanelUI.cs` & UI Buttons:**
   - Mainkan `SFX_UI_Click.wav` pada setiap tombol navigasi utama / pergantian tab.
   - Mainkan `SFX_UI_PoseLock.wav` saat pose target terdeteksi valid (`GERAKAN TERDETEKSI!`).

6. **`SettingsPanelUI.cs` (`SCREEN 05`):**
   - Tambahkan baris pemilih kontrol **Gaya Musik (BGM Style)** pada panel pengaturan (`SCREEN 05`) menggunakan dropdown atau segmented pill control:
     - `[0] ADAPTIF (DEFAULT)`: Musik berganti secara dinamis mengikuti biome dan ketinggian.
     - `[1] GHIBLI / GENSHIN FANTASY`: Suasana orkestra akustik hangat & seruling khas Celtic/Ghibli.
     - `[2] CINEMATIC TRIBAL & AMBIENT`: Suasana etnik mistis highland & ambient perkusi.
   - Hubungkan ke properti `DragonGameSettings.CurrentBgmThemeStyle = (BgmThemeStyle)index;`.
   - Pastikan nilai tersimpan secara persisten ke `PlayerPrefs.SetInt("Fanamir_BgmThemeStyle", (int)value); PlayerPrefs.Save();` dan dimuat kembali saat inisialisasi awal boot, sehingga preferensi pemain tetap tersimpan meskipun keluar dari game.
   - Mengubah opsi ini di Play Mode akan langsung memicu event `OnBgmThemeStyleChanged` dan meng-crossfade lagu jelajah di `AudioManager` secara instan.

---

### LANGKAH 5: Verifikasi & Pengujian Play Mode
1. Buat skrip runner pengujian `Assets/Editor/AudioSystemVerificationRunner.cs`:
   - Uji pemuatan seluruh **37 klip audio** (14 BGM + 23 SFX, pastikan 0 null reference).
   - Uji koneksi bus AudioMixer (`Master`, `BGM`, `SFX`).
   - Uji simulasi perubahan slider volume di `DragonGameSettings` $\to$ verifikasi decibel AudioMixer berubah sesuai rumus logaritmik.
   - Uji pergantian `BgmThemeStyle` di Settings $\to$ verifikasi `PlayerPrefs` tersimpan dan track musik berganti.
   - Uji simulasi transisi cross-fade BGM Free Roam $\leftrightarrow$ Krisis Biome.
   - Uji simulasi trigger SFX Splash Screen logo reveal, kepakan sayap, dive rush, semburan air, dew chimes, countdown, dan bintang Finish Modal.
2. Jalankan Play Mode penuh dari `MainMenu_Lobby.unity` $\to$ `MainIsland_Gameplay.unity`:
   - Verifikasi BGM Lobby terdengar, transisi ke terbang memutar BGM jelajah, dan seluruh event berbunyi tanpa console errors (`0` errors, `0` warnings baru).

---

### LANGKAH 6: Dokumentasi Brain & Git Push
1. Perbarui `brain/laptop-b/Tasks.md` (tandai selesai Sesi 5 Lanjutan / Sistem Audio & `// TODO [SYNC-AUDIO]`).
2. Tulis catatan kronologis di `brain/laptop-b/Devlog.md` (dan buat catatan memori `brain/laptop-b/memories/Implementasi Sistem Audio Fanamir.md`).
3. Jalankan `git -C brain add .`, `commit`, dan `push` sub-repo `brain/`.
```

---
*Navigasi:* [[00 - Overview]] | [[Tasks]] | [[Devlog]] | [[laptop-a/gameplay-mechanics/arsitektur_sistem_audio]]
