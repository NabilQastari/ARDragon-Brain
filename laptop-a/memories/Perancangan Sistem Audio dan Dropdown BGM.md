---
title: "Perancangan Sistem Audio dan Dropdown BGM"
type: session-memory
tags:
  - session-memory
  - laptop-a
  - audio
  - bgm
  - sfx
  - ui
date: 2026-11-26
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[arsitektur_sistem_audio]]"
  - "[[prompt_eksekusi_sistem_audio_laptop_b]]"
  - "[[rencana_desain_ui]]"
---

# 🧠 Memori Sesi: Perancangan Sistem Audio dan Dropdown BGM

Catatan memori sesi perancangan, kurasi, sintesis prosedural, pemaketan, dan penyelarasan UI untuk **Sistem Audio Terpadu Fanamir** (BGM Dinamis, Suite 23 SFX WAV, Dual Dropdown BGM, Quick Switcher Menu, SFX Propeller Dash Whoosh Berat, dan Master Prompt Laptop B).

---

## 1. Tujuan & Konteks Sesi
- **Penyesuaian Alur Kerja:** Melompat sementara dari implementasi animasi naga ke perancangan arsitektur audio agar segera dapat disinkronkan dengan `AudioManager.cs` dan menuntaskan penanda lama `// TODO [SYNC-AUDIO]` di Unity (Laptop B).
- **Audit & Kebutuhan Audio:** Menginventarisasi seluruh kebutuhan audio dari dokumen GDD (`mekanisme_tantangan_desa.md`, `konteks.md`, dll.) mencakup lokomosi naga, aksi tantangan 3 biome, transisi krisis-cleansed, selebrasi kemenangan, dan antarmuka UI.
- **Arah Musikal & Permintaan Pengguna:**
  1. Menyediakan pilihan BGM lengkap yang memadukan tema **Ghibli / Genshin Fantasy** (orkestra akustik riang) dan **Cinematic Tribal & Ambient** (etnik mistis krisis).
  2. Mengganti pembatasan 3 gaya umum di Settings menjadi sistem **Dropdown Pemilihan Lagu Langsung** dengan nama **`BGM 1`**, **`BGM 2`**, dst., serta dropdown khusus lagu latar Menu.
  3. Menambahkan **Pill Pemilih Cepat BGM Menu** langsung pada navbar Menu Utama (`SCREEN 00`) agar pemain bisa mengganti lagu latar Lobby secara instan tanpa harus masuk ke Settings.
  4. Menyediakan efek suara khusus **Splash Screen Logo Reveal (`SFX_Splash_LogoReveal.wav`)** dan teks branding engine **`MADE WITH UNITY`** pada `SCREEN 09`.
  5. Memperbaiki efek suara **Propeller Dash (`SFX_PropellerDash_Loop.wav`)** agar berkarakter **Whoosh Berat** bertenaga besar tanpa siulan berdenging.
  6. Mengemas seluruh suite audio ke dalam folder mandiri [`assets/Export_Audio_LaptopB/`](file:///e:/Semester%205/Game/AR%20Dragon/assets/Export_Audio_LaptopB/) dan arsip kompresi tunggal [`assets/Export_Audio_LaptopB.zip`](file:///e:/Semester%205/Game/AR%20Dragon/assets/Export_Audio_LaptopB.zip) (~91.6 MB).

---

## 2. Keputusan Desain / Arsitektur / Teknis

### A. Kurasi & Pemetaan 14 Track BGM (MP3 44.1kHz)
1. **Ghibli / Genshin Fantasy (6 Tracks):**
   - `BGM_Lobby_FolkRound.mp3` (Lobby 1, dawai lute & flute ramah damai)
   - `BGM_Lobby_VillageConsort.mp3` (Lobby 2, ansambel desa ceria)
   - `BGM_Flight_CelticImpulse.mp3` (Jelajah 1 / BGM 1, seruling Celtic lincah)
   - `BGM_Flight_AirPrelude.mp3` (Jelajah 2 / BGM 2, simfoni megah angkasa tinggi $Y > 120\text{m}$)
   - `BGM_Flight_LordOfTheLand.mp3` (Jelajah 3 / BGM 3, petualangan pastoral epik)
   - `BGM_Flight_TellerOfTheTales.mp3` (Jelajah 4 / BGM 4, alunan dongeng fantasi hangat)
   - `BGM_Flight_ClearWaters.mp3` (Jelajah 5 / BGM 5, denting piano jernih danau)
2. **Cinematic Tribal & Ethereal Ambient (6 Tracks):**
   - `BGM_Flight_SkyeCuillin.mp3` (Jelajah 6 / BGM 6, seruling highland etnik & Center Peak)
   - `BGM_Crisis_Zephyria_HimalayanAtmosphere.mp3` (Krisis Zephyria / BGM 7, desau kabut mistis)
   - `BGM_Crisis_Pyrewood_Firesong.mp3` (Krisis Pyrewood / BGM 8, ritme perkusi api berkobar)
   - `BGM_Crisis_Pyrewood_Ritual.mp3` (Variasi Pyrewood / BGM 9, tabuhan shamanic magis)
   - `BGM_Crisis_Solaria_DesertCity.mp3` (Krisis Solaria / BGM 10, instrumen eksotis gurun)
   - `BGM_Crisis_Solaria_CurseOfTheScarab.mp3` (Variasi Solaria / BGM 11, ketukan dramatis altar langit)
3. **Celebration Fanfare (1 Track):**
   - `BGM_Victory_HeroicAge.mp3` (Victory selebrasi pemulihan seluruh pulau & Finish Modal)

### B. Arsitektur Pemilihan Lagu & Persistensi Antar Sesi
- **Dropdown BGM In-Game di Settings (`SCREEN 05`):**
  - Opsi: `[0] Adaptif (Otomatis Sesuai Biome)`, `[1] BGM 1` s.d. `[11] BGM 11`.
  - Disimpan persisten di `PlayerPrefs` via key `Fanamir_BgmFlightIndex`.
- **Dropdown BGM Menu di Settings (`SCREEN 05`):**
  - Opsi: `[0] BGM Menu 1` s.d. `[5] BGM Menu 6`.
  - Disimpan persisten di `PlayerPrefs` via key `Fanamir_BgmMenuIndex`. Mengubah opsi langsung meng-crossfade lagu Menu saat itu juga ($1.2\text{s}$).
- **Pill Pemilih Cepat di Navbar Menu (`SCREEN 00`):**
  - Widget kapsul solid `#FBF6EB` `[🎵 BGM Menu: BGM 1 ▼]` di sebelah badge status kamera. Pemain bisa mengganti musik Lobby langsung saat berada di Menu tanpa membuka Settings.
- **Persistensi Boot:**
  - `DragonGameSettings` otomatis memuat kedua indeks saat game pertama kali dibuka, sehingga lagu pilihan pemain **tidak pernah ter-reset** meskipun keluar dari game (`Quit`).

### C. Sintesis SFX Propeller Dash: "Whoosh Berat"
- Mengganti modulasi flutter 3.8 Hz berdesau/bersiul tinggi dengan **sintesis multi-band SOS (Second-Order Sections)**:
  - Band Sub-Bass: $30\text{--}110\text{ Hz}$ (dorongan gelombang massa udara dalam).
  - Band Low-Mid: $100\text{--}320\text{ Hz}$ (desau bodi udara berbobot).
  - Band Upper-Air: $280\text{--}750\text{ Hz}$ & $650\text{--}1600\text{ Hz}$ (aerodynamic air shear tanpa siulan).
  - Modulasi asimetris $2.5\text{ Hz}$ (hentakan kompresi udara cepat diikuti pelepasan bertahap).
  - Hasil: Karakter audio terdengar seperti dorongan jet supersonik/turbin naga yang perkasa dan berbobot.

### D. Splash Screen Visual & Audio (`SCREEN 09`)
- Audio: `SFX_Splash_LogoReveal.wav` (durasi 3.2s: sub-bass orchestral swell C2-C3, celestial chord C4-G4-C5-E5-G5, sparkling logo reveal chime C6-E6-G6-B6-C7, & shimmer tail).
- Visual: Menambahkan lencana / teks **`MADE WITH UNITY`** di bawah teks subtitle exergaming.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset

1. **Aset Audio Fisik di Root Workspace:**
   - [`assets/audio/BGM/`](file:///e:/Semester%205/Game/AR%20Dragon/assets/audio/BGM/): 14 Track MP3 lengkap (>500KB s.d. 10.7MB).
   - [`assets/audio/SFX/`](file:///e:/Semester%205/Game/AR%20Dragon/assets/audio/SFX/): 23 Berkas WAV 44.1kHz 16-bit PCM terproseduralisasi.
   - [`assets/Export_Audio_LaptopB/`](file:///e:/Semester%205/Game/AR%20Dragon/assets/Export_Audio_LaptopB/): Direktori ekspor mandiri + skrip `copy_to_unity.bat` + `README.md`.
   - [`assets/Export_Audio_LaptopB.zip`](file:///e:/Semester%205/Game/AR%20Dragon/assets/Export_Audio_LaptopB.zip): Arsip kompresi tunggal (~91.6 MB) terverifikasi utuh.

2. **Skrip Pembantu yang Dibuat & Dimutakhirkan:**
   - [`misc/generate_fanamir_audio_suite.py`](file:///e:/Semester%205/Game/AR%20Dragon/misc/generate_fanamir_audio_suite.py): Generator 22 SFX awal + downloader 8 BGM awal, dimutakhirkan dengan formula Whoosh Berat Propeller Dash.
   - [`misc/expand_fanamir_audio.py`](file:///e:/Semester%205/Game/AR%20Dragon/misc/expand_fanamir_audio.py): Generator `SFX_Splash_LogoReveal.wav` + downloader 6 BGM ekspansi.
   - [`misc/build_audio_export_package.py`](file:///e:/Semester%205/Game/AR%20Dragon/misc/build_audio_export_package.py): Skrip perakit folder ekspor, skrip batch instalasi, dan kompresi ZIP.

3. **Dokumentasi Brain Laptop A:**
   - [`brain/laptop-a/gameplay-mechanics/arsitektur_sistem_audio.md`](file:///e:/Semester%205/Game/AR%20Dragon/brain/laptop-a/gameplay-mechanics/arsitektur_sistem_audio.md): Single Source of Truth arsitektur sistem audio Fanamir.
   - [`brain/laptop-a/ui-ux/rencana_desain_ui.md`](file:///e:/Semester%205/Game/AR%20Dragon/brain/laptop-a/ui-ux/rencana_desain_ui.md): Penambahan pill pemilih BGM di `SCREEN 00`, dual dropdown di `SCREEN 05`, dan Splash SFX + Made with Unity di `SCREEN 09`.
   - [`brain/laptop-a/handoff-laptop-b/prompt_eksekusi_sistem_audio_laptop_b.md`](file:///e:/Semester%205/Game/AR%20Dragon/brain/laptop-a/handoff-laptop-b/prompt_eksekusi_sistem_audio_laptop_b.md): Master prompt eksekusi teknis 6 langkah siap salin untuk Laptop B.
   - [`brain/laptop-a/Tasks.md`](file:///e:/Semester%205/Game/AR%20Dragon/brain/laptop-a/Tasks.md) & [`brain/laptop-a/Devlog.md`](file:///e:/Semester%205/Game/AR%20Dragon/brain/laptop-a/Devlog.md): Pelacakan status dan pencatatan kronologis.

---

## 4. Detail Penting yang Harus Diingat
- **Kunci PlayerPrefs Persistensi Audio:**
  - `Fanamir_BgmFlightIndex` (int): `0` = Adaptif (Default), `1..11` = BGM 1 s.d. BGM 11.
  - `Fanamir_BgmMenuIndex` (int): `0` = BGM Menu 1 (Default), `1..5` = BGM Menu 2 s.d. BGM Menu 6.
  - `Fanamir_BgmVolume` (float): Slider $0.0\text{--}1.0$.
  - `Fanamir_SfxVolume` (float): Slider $0.0\text{--}1.0$.
- **Formula Decibel AudioMixer:** $\text{dB} = 20 \times \log_{10}(\max(\text{linearVolume}, 0.0001))$. Parameter ter-expose: `"BgmVolume"` dan `"SfxVolume"`.
- **Durasi Transisi Cross-Fade:**
  - Pergantian BGM Menu: $1.2\text{ detik}$.
  - Masuk Radius Krisis Desa ($< 250\text{m}$): $1.8\text{ detik}$.
  - Keluar Radius Krisis ($> 280\text{m}$, hysteresis $30\text{m}$): $2.0\text{ detik}$.
  - Pemulihan Stinger Desa: Pemutaran one-shot instan dengan ducking BGM singkat.
- **Strategi Transfer Audio ke Laptop B:**
  - Jika Laptop B sudah punya folder audio sebelumnya: **Cukup kirim 1 berkas `SFX_PropellerDash_Loop.wav` (~176 KB)** untuk ditimpa di `Assets/Audio/SFX/Dragon/`.
  - Jika Laptop B belum pernah menerima audio: **Kirim arsip utuh `Export_Audio_LaptopB.zip` (~91.6 MB)** dan jalankan `copy_to_unity.bat`.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] **Monitoring Eksekusi di Laptop B:** Memastikan agen di Laptop B menerima prompt eksekusi, membuat `Fanamir_AudioMixer.mixer`, mengimplementasikan `AudioManager.cs`, menghubungkan hook audio skrip eksisting, dan menghapus `// TODO [SYNC-AUDIO]`.
- [ ] **Melanjutkan Roadmap Animasi Naga:**
  - Melanjutkan Sesi 4 Three.js Studio di Laptop A (Village Actions `#15A..C`, finalisasi C# snippet Unity, dan kompresi `Fanamir_AnimStudio_Standalone.zip`).
- [ ] **Dokumentasi Laporan Capstone:**
  - Menyiapkan integrasi screenshot audio settings dan gameplay live dari Laptop B ke folder `docs/figures/raw_screenshots/`.

---
*Navigasi:* [[00 - Overview]] | [[Tasks]] | [[Devlog]] | [[arsitektur_sistem_audio]] | [[prompt_eksekusi_sistem_audio_laptop_b]] | [[rencana_desain_ui]]
