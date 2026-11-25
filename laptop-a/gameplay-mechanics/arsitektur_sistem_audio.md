---
title: "Arsitektur Sistem Audio Fanamir: The Sky Guardian (BGM & SFX)"
tags:
  - audio
  - bgm
  - sfx
  - game-design
  - architecture
  - laptop-a
status: approved
updated: 2026-11-24
---

# 🎵 Arsitektur Sistem Audio — Fanamir: The Sky Guardian

Dokumen ini adalah **Single Source of Truth** perancangan sistem audio (BGM, SFX, AudioMixer, dan transisi dinamis) untuk game *Fanamir: The Sky Guardian*, dirancang di **Laptop A** untuk diimplementasikan secara terpadu oleh **Laptop B** pada skrip `AudioManager.cs`, `DragonGameSettings.cs`, `VillageZoneController.cs`, `GameManager.cs`, dan scene Unity.

---

## 1. Visi Artistik & Arah Gaya Musikal (Dual-Style System)

Sesuai preferensi perancangan, sistem musik Fanamir mengusung **Dual-Style System** yang memadukan dua atmosfer musikal:
1. **Gaya A: Ghibli / Genshin Impact (Fantasy Orchestral):**
   - Didominasi instrumen dawai akustik (Celtic harp, acoustic guitar, lute), flute kayu bernada melayang, dan string ensemble megah yang membangkitkan kebebasan meluncur di langit biru.
2. **Gaya B: Cinematic Tribal & Ethereal Ambient:**
   - Didominasi instrumen perkusi etnik (taiko, frame drums, shakers), seruling padang pasir/highland (duduk, low whistle), dan lapisan drone atmosferik yang memperkuat ketegangan bencana alam.

### Matriks Pemetaan Lagu (BGM Tracks Catalog)

| Kategori / State Game | Nama File Aset | Gaya Musikal & Komposisi | Pemicu Transisi di Unity |
| :--- | :--- | :--- | :--- |
| **Lobby & Kalibrasi** | `BGM_Lobby_FolkRound.mp3` | **Ghibli Fantasy:** Flute akustik, dawai lute, damai & ramah | Saat berada di `MainMenu_Lobby.unity` (`Missions`, `Calibration`, `Settings`) |
| **Jelajah Santai (Free Roam 1)** | `BGM_Flight_CelticImpulse.mp3` | **Genshin Style:** Tempo ceria, seruling Celtic cepat, meluncur di atas pulau hijau | Default BGM saat terbang di atas laut & daratan netral ($> 250\text{m}$ dari desa) |
| **Jelajah Bebas (Free Roam 2)** | `BGM_Flight_AirPrelude.mp3` | **Cinematic Orchestral:** Gesekan biola megah, suasana angkasa tinggi & awan luas | Variasi BGM jelajah atau saat terbang di atas ketinggian $Y > 120\text{m}$ |
| **Jelajah Kuno (Free Roam 3)** | `BGM_Flight_SkyeCuillin.mp3` | **Tribal Ambient:** Seruling highland kuno, pegunungan megah dan sunyi | Variasi tema alternatif atau saat mendekati pegunungan tengah (*Center Peak*) |
| **Krisis 1 — Desa Zephyria** | `BGM_Crisis_Zephyria_HimalayanAtmosphere.mp3` | **Eerie Ambient:** Singing bowls, desau angin dingin, perkusi tertekan misterius | Masuk radius $< 250\text{m}$ Desa Zephyria (status desa belum pulih) |
| **Krisis 2 — Desa Pyrewood** | `BGM_Crisis_Pyrewood_Firesong.mp3` | **Cinematic Tribal:** Perkusi cepat 115 BPM, taiko membara, gesekan string tegang | Masuk radius $< 250\text{m}$ Desa Pyrewood / Hutan Terbakar (status belum pulih) |
| **Krisis 3 — Desa Solaria** | `BGM_Crisis_Solaria_DesertCity.mp3` | **Arid Mystical:** Seruling padang pasir, ritme lambat dahaga, atmosfer terik terisolasi | Masuk radius $< 250\text{m}$ Desa Solaria / Plato Kering (status belum pulih) |
| **Kemenangan & Finish Modal** | `BGM_Victory_HeroicAge.mp3` | **Triumphant Fanfare:** Bras orkestra megah, simbal, nada bangga kepahlawanan | Terpicu saat Global Vitality $100\%$ atau pembukaan `PF_FinishModal` |

### 1.1 Pengaturan Preferensi Gaya BGM di Menu Settings (`SCREEN 05`)
Untuk memberikan kebebasan selera musikal kepada pemain, menu **Settings** (`SCREEN 05`) dilengkapi dengan pemilih **Gaya Musik (Music Theme Style)**:

1. **`BgmThemeStyle.Adaptive` (`0` — Default):**
   - Game secara otomatis dan dinamis memilih lagu terbaik berdasarkan ketinggian dan lokasi (Folk Round di Lobby, Celtic Impulse saat jelajah rendah, Air Prelude saat terbang tinggi $Y > 120\text{m}$, Skye Cuillin di Center Peak).
2. **`BgmThemeStyle.GhibliFantasy` (`1`):**
   - Mengunci musik jelajah bebas agar selalu memutar alunan ceria khas Ghibli / Genshin Impact (`Celtic Impulse` dan `Air Prelude`).
3. **`BgmThemeStyle.CinematicTribal` (`2`):**
   - Mengunci musik jelajah bebas agar memutar nuansa etnik highland & ambient mistis (`Skye Cuillin`).

*Catatan:* Pilihan gaya musik pemain disimpan secara persisten di `PlayerPrefs` (`Fanamir_BgmThemeStyle`) dan dapat diubah secara langsung kapan saja di menu pause/settings in-game dengan efek cross-fade instan. Musik krisis desa tetap berjalan spesifik per biome untuk menjaga ketegangan atmosferik saat mendekati desa bencana.

---

## 2. Mekanik Transisi Musik Dinamis (*Cross-Fade Architecture*)

Untuk menghindari perpindahan musik yang patah (*abrupt cut*), `AudioManager.cs` mengimplementasikan **Dual AudioSource Cross-Fader**:

```text
               ┌────────────────────────────────────────────────────────┐
               │              AudioManager (Singleton)                 │
               │  [AudioSource bgmA]        [AudioSource bgmB]          │
               └───────────┬────────────────────────────┬───────────────┘
                           │                            │
             (Sedang Mainkan Free Roam)       (Target Lagu Krisis Desa)
                     Volume: 1.0                     Volume: 0.0
                           │                            │
                           ▼                            ▼
           Jarak Naga ke Desa < 250m  ──►  Mulai Coroutine Crossfade (1.5s - 2.0s)
                           │                            │
                     Volume: 1.0 -> 0.0              Volume: 0.0 -> 1.0
                           │                            │
                           ▼                            ▼
                      [bgmA Stop]             [bgmB Jadi Sumber Aktif]
```

### Logika & Aturan Transisi:
1. **Memasuki Radius Krisis ($< 250\text{m}$):**
   - Jika desa belum pulih (`!isRestored`), lakukan cross-fade halus ($1.8\text{ detik}$) dari BGM Jelajah ke BGM Krisis desa terkait.
2. **Keluar Radius Krisis ($> 280\text{m}$, dengan Hysteresis $30\text{m}$):**
   - Lakukan cross-fade kembali ($2.0\text{ detik}$) dari BGM Krisis ke BGM Jelajah santai.
3. **Momen Pemulihan Desa ($100\%$ Restored):**
   - Hentikan BGM Krisis seketika dengan pemutaran stinger instan: `SFX_Village_Cleansed_Stinger.wav` (akor mayor surgawi dengan paduan suara megah, $2.8\text{s}$).
   - Bersamaan dengan itu, cross-fade lembut kembali ke BGM Jelajah santai yang kini terasa melegakan.
4. **Victory Trigger (Tahap Akhir):**
   - Saat seluruh desa tuntas, jalankan `BGM_Victory_HeroicAge.mp3` disusul animasi pengisian bintang emas Finish Modal.

---

## 3. Katalog Efek Suara (SFX Suite — 18 Berkas Terpadu)

Semua aset SFX dibuat secara prosedural dalam format **WAV 16-bit 44.1 kHz PCM (Linear)** berkualitas tinggi dan bebas hak cipta, siap digunakan langsung oleh Unity Audio Source:

### A. Lokomosi & Aksi Karakter Naga (`Assets/Audio/SFX/Dragon/`)
1. **`SFX_WingFlap.wav` (0.35s):**
   - *Karakter:* Hantaman kepakan membran sayap kanvas/kulit tebal berbobot (*heavy leather whoosh*), perpaduan *brown noise displacement* dan sub-bass 45 Hz.
   - *Pemicu:* Setiap siklus downstroke kepakan sayap normal (`DragonFlightController.IsFlapping`).
2. **`SFX_WingFlap_Heavy.wav` (0.45s):**
   - *Karakter:* Kepakan ekstra bertenaga dengan dorongan angin ganda.
   - *Pemicu:* Dipicu saat `#06B Steep Climb Flap` atau kepakan dorongan awal Takeoff.
3. **`SFX_DiveRush_Loop.wav` (3.0s Seamless Loop):**
   - *Karakter:* Desau angin supersonik beroktaf rendah (*deep wind rush*) dengan resonansi siulan aerodinamis.
   - *Pemicu:* Aktif saat `#13 Tuck to Dive` atau airspeed $> 22\text{ m/s}$. Volume & pitch dinaikkan proporsional mengikuti kecepatan naga.
4. **`SFX_PropellerDash_Loop.wav` (2.0s Seamless Loop):**
   - *Karakter:* Turbin kepakan badai berkecepatan tinggi dengan modulasi ritmis $3.8\text{ Hz}$ persis mengikuti frekuensi kepakan sayap naga.
   - *Pemicu:* Aktif selama gestur `#14 Propeller Dash` berjalan.
5. **`SFX_Launch_Whoosh.wav` (0.55s):**
   - *Karakter:* Dorongan impuls udara cepat (*supersonic push*).
   - *Pemicu:* Saat terlepas dari Auto-Hover desa (Launch Thrust 32% momentum).
6. **`SFX_Dragon_Footstep_01.wav` & `_02.wav` (0.25s):**
   - *Karakter:* Dentuman langkah cakar naga di atas tanah rumput/batu (`Thud & Rustle`).
   - *Pemicu:* Siklus jalan quadruped darat (`#02 Grounded Walk`).
7. **`SFX_Dragon_Roar_Victory.wav` (2.4s):**
   - *Karakter:* Auman naga elemen air purba yang agung dan berwibawa (resonansi 120–165 Hz dengan vibrato alami, bukan raungan monster buas).
   - *Pemicu:* Dipicu otomatis oleh `DragonBodyAnimator.PlayVictoryRoar()` saat desa pulih 100%.

### B. Tantangan & Pemulihan 3 Desa (`Assets/Audio/SFX/Village/`)
8. **`SFX_Zephyria_WindGust.wav` (1.2s):**
   - *Karakter:* Sapuan angin kencang bertekanan tinggi yang menyapu kabut.
   - *Pemicu:* Setiap kepakan sayap pemain di zona tantangan kabut Zephyria.
9. **`SFX_Lake_Siphon_Loop.wav` (3.0s Seamless Loop):**
   - *Karakter:* Pusaran air danau terhisap ke atas (*water vortex swirl*) dengan gelembung magis.
   - *Pemicu:* Saat Auto-Hover di atas Danau Zephyria (pengisian tangki air $0\% \to 100\%$).
10. **`SFX_Pyrewood_WaterBreath_Loop.wav` (3.0s Seamless Loop):**
    - *Karakter:* Semburan air deras bertekanan tinggi (*high-pressure torrential spray*).
    - *Pemicu:* Gestur buka mulut `#15B Water Breath` di hutan terbakar Pyrewood.
11. **`SFX_Pyrewood_Extinguish_Steam.wav` (1.4s):**
    - *Karakter:* Desis kepulan uap air panas mendesis saat bara api padam tersiram air.
    - *Pemicu:* Saat semprotan air mengenai pohon terbakar / progres pemadaman naik.
12. **`SFX_Solaria_DewChimes.wav` (2.5s):**
    - *Karakter:* Rangkaian denting lonceng embun kristal surgawi bertingkat (harmoni C6 - E6 - G6 - B6 - C7).
    - *Pemicu:* Selama menahan gestur `#15C Double-I Pose` di Sky Altar Solaria.
13. **`SFX_Village_Cleansed_Stinger.wav` (2.8s):**
    - *Karakter:* Akor mayor 9th surgawi yang mengembang megah dengan shimmer tremolo.
    - *Pemicu:* Tepat saat bar tantangan desa mencapai $100\%$ (momen desa selamat).

### C. Antarmuka UI & Umpan Balik Game Loop (`Assets/Audio/SFX/UI/`)
14. **`SFX_UI_Click.wav` (0.04s):**
    - *Karakter:* Ketukan kayu/perkamen hangat (*wooden tabletop tap*).
    - *Pemicu:* Klik tombol menu, ganti tab lobby, toggle checkbox.
15. **`SFX_UI_Hover.wav` (0.06s):**
    - *Karakter:* Gesekan halus perkamen (*soft parchment rustle*).
    - *Pemicu:* Pointer enter pada tombol navigasi.
16. **`SFX_UI_PoseLock.wav` (0.28s):**
    - *Karakter:* Nada kristal konfirmasi dua-tingkat (E6 $\to$ B6).
    - *Pemicu:* Saat pose target terdeteksi valid (sinkron dengan badge `● GERAKAN TERDETEKSI!`).
17. **`SFX_Countdown_Beep.wav` (0.18s):**
    - *Karakter:* Nada marimba/woodblock hangat 440 Hz.
    - *Pemicu:* Hitungan mundur awal pada angka `3..`, `2..`, dan `1..`.
18. **`SFX_Countdown_Go.wav` (0.75s):**
    - *Karakter:* Akor tiupan terompet/horn naga yang bersemangat.
    - *Pemicu:* Hitungan mundur saat kata `"TERBANG!"` muncul.
19. **`SFX_Star_01.wav`, `_02.wav`, `_03.wav` (0.65s):**
    - *Karakter:* Tiga nada lonceng bintang emas berurutan (Do: C6, Mi: E6, Sol: G6).
    - *Pemicu:* Animasi pengisian bintang 1, 2, dan 3 pada `FinishModalUI` (`0.4s` jeda).

---

## 4. Arsitektur Teknis AudioMixer & Scripting (`AudioManager.cs`)

### A. Struktur Bus AudioMixer (`Fanamir_AudioMixer.mixer`)
```text
[Master Bus] (0 dB)
  ├── [BGM Bus]  --> Expose Param: "BgmVolume" (-80 dB s.d. 0 dB)
  ├── [SFX Bus]  --> Expose Param: "SfxVolume" (-80 dB s.d. 0 dB)
  │     ├── [DragonSFX] (Duck saat BGM Stinger)
  │     └── [VillageSFX]
  └── [UI Bus]   --> Reroute ke SFX Bus
```

### B. Konversi Logaritmik Slider ke Decibel
Slider UI di `SettingsPanelUI.cs` memiliki nilai `0.0f` s.d. `1.0f`. Rumus konversi standar ke decibel AudioMixer:
$$\text{dB} = 20 \times \log_{10}(\max(\text{volume}, 0.0001))$$
*Contoh:*
- Volume $1.0 = 0.0\text{ dB}$ (Maksimal)
- Volume $0.5 \approx -6.0\text{ dB}$ (Separuh kenyaringan persepsi)
- Volume $0.0 \to -80.0\text{ dB}$ (Mute hening)

### C. Kontrak Skrip `AudioManager.cs`
Skrip `AudioManager.cs` bertindak sebagai *persistent singleton* (`DontDestroyOnLoad`):
* **Penyambungan Otomatis:**
  - `DragonGameSettings.OnBgmVolumeChanged += SetBgmVolume;`
  - `DragonGameSettings.OnSfxVolumeChanged += SetSfxVolume;`
  - `DragonGameSettings.OnBgmThemeStyleChanged += OnBgmThemeStyleChanged;`
* **Metode Publik Utama:**
  - `PlayBGM(AudioClip clip, float fadeDuration = 1.8f)`
  - `CrossfadeToCrisis(BiomeType biome)`
  - `RestorePeacefulBGM()`
  - `SetBgmThemeStyle(BgmThemeStyle style)`
  - `PlaySFX(AudioClip clip, float volume = 1f)`
  - `PlaySFX3D(AudioClip clip, Vector3 worldPos, float volume = 1f)`
  - `PlayLoopingSFX(AudioClip clip, ref AudioSource sourceHolder, float volume = 1f)`
  - `StopLoopingSFX(ref AudioSource sourceHolder, float fadeDuration = 0.3f)`
  - `PlayStarScoreSequence(int starsCount)`

---

## 5. Lokasi Berkas & Handoff ke Laptop B

Seluruh aset audio hasil generasi & kurasi telah disiapkan di root workspace pada folder `assets/audio/`:
- `assets/audio/BGM/Ghibli_Fantasy/` (Lobby, Flight Celtic, Flight Air Prelude)
- `assets/audio/BGM/Cinematic_Tribal/` (Flight Skye, Pyrewood Firesong, Solaria Desert City, Zephyria Ambient)
- `assets/audio/BGM/Celebration/` (Victory Heroic Age)
- `assets/audio/SFX/Dragon/` (7 berkas lokomosi & naga)
- `assets/audio/SFX/Village/` (6 berkas aksi desa)
- `assets/audio/SFX/UI/` (8 berkas antarmuka & countdown)

Semua aset ini siap disalin ke Unity project di Laptop B pada folder:
`AR Dragon Sky Guardian/Assets/Audio/`

---
*Navigasi:* [[00 - Overview]] | [[Tasks]] | [[Devlog]] | [[laptop-a/gameplay-mechanics/mekanisme_tantangan_desa]] | [[laptop-a/handoff-laptop-b/prompt_eksekusi_sistem_audio_laptop_b]]
