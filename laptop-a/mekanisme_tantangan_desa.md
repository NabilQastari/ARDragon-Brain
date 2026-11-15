---
title: Mekanisme Tantangan Desa & Flow Misi (Sesi 4 & 5)
tags:
  - gameplay
  - quest
  - village
  - laptop-a
  - mechanics
owner: Laptop A
updated: 2026-11-15
---

# 📜 Mekanisme Tantangan Desa & Flow Quest — Fanamir: The Sky Guardian

Dokumen ini adalah **Single Source of Truth** untuk alur misi desa (*Quest Narrative, Auto-Hover, Sub-Objectives, Challenge Gestures, & Free Play Mode*) yang akan diimplementasikan pada **Sesi 4** (`VillageZoneController.cs`, `Session4_VillageBuilder.cs`, VFX) dan **Sesi 5** (`GameManager.cs`, Scoring/Stars, Free Play) di Laptop B.

---

## 1. Identitas & Tematik 3 Desa

| Biome | Nama Resmi Desa | Masalah Lingkungan | Vibe & Visual |
| :--- | :--- | :--- | :--- |
| **Biome 1** (Smog Valley) | **Desa Zephyria** *(Valley of Winds)* | Kabut asap tebal / polusi udara dari lembah | Lembah hijau basah tertutup asap pekat (`SM_Rocks_Biome1`, Danau Utama) |
| **Biome 2** (Wildfire Woods) | **Desa Pyrewood** *(Cinder Forest)* | Kebakaran hutan hebat melalap pepohonan | Hutan pinus terbakar, abu beterbangan, pohon hangus |
| **Biome 3** (Drought Plateau) | **Desa Solaria** *(Arid Highlands)* | Kekeringan ekstrem, tanah retak & tandus | Tebing batu tinggi terik, 2 cekungan danau kering (*dry basins*) |

---

## 2. Alur Interaksi & State Mesin Desa

Setiap desa memiliki 2 lingkaran radius interaksi:
1. **Radius Narasi / Misi (`Radius = 35m`, ground level desa):**
   - Ketika Fanamir masuk ke dalam radius desa:
     - Naga **Auto-Hover** (terbang di tempat) dan perlahan menghadap ke arah balai desa.
     - **Timer Stage PAUSE** (waktu permainan berhenti berhitung sementara).
     - Tampil kotak narasi **`PF_QuestNarrativeBox` (`SCREEN 08`)** dengan auto-play (`3.2s – 5.0s` per baris dialog, *hands-free*).
     - **Mekanisme Pembatalan (Escape):** Pemain dapat membatalkan auto-hover dan narasi kapan saja cukup dengan **mengepakkan sayap (Flap gestur)** untuk terbang menjauh. Narasi akan menutup dan timer stage kembali berjalan.
     - **Pengambilan Misi:** Jika pemain menunggu di auto-hover sampai narasi selesai, quest otomatis diambil (`Accepted`). **Quest Objective HUD** di atas Vitality Bar diperbarui.

2. **Radius Zona Tantangan (Spawn setelah quest diambil):**
   - Lingkaran radius baru muncul di lokasi spesifik tantangan (bisa di atas desa, di danau, atau di hutan).
   - Ketika Fanamir masuk ke zona tantangan:
     - Naga masuk **Auto-Hover Tantangan**.
     - **Timer Stage TIDAK PAUSE** (waktu tetap berjalan, tantangan harus diselesaikan dengan cepat untuk meraih bintang 3!).
     - Muncul overlay tantangan **`PF_VillageChallengeOverlay` (`SCREEN 03`)** dengan indikator gestur dan progres bar.
     - Pemain juga tetap bisa **escape** (lepas dari auto-hover) kapan saja dengan **mengepakkan sayap (Flap)** jika ingin reposisi.

---

## 3. Detail Mekanisme Tantangan per Desa

### A. Desa Zephyria (Biome 1 — Smog Valley)
* **Kondisi Awal:** Lembah tertutup partikel kabut/smog tebal.
* **Spawn Radius Tantangan:** Muncul di atas desa pada ketinggian sedikit lebih tinggi dari radius desa (`Y ~ +25m` di atas dataran desa).
* **Mekanisme Tantangan (Opsi A - Linear Hold):**
  - Gestur: **Flap Wings (Kepakkan Sayap)** secara ritmik/kontinyu.
  - Setiap kepakkan sayap menghembuskan angin kencang yang menyapu partikel kabut (`Smog VFX`).
  - Bar progres terisi dari `0%` $\rightarrow$ `100%` seiring kepakkan.
  - Jika pemain istirahat/berhenti sejenak, progres tidak berkurang (no decay).
* **Penyelesaian (100%):**
  - Partikel kabut menghilang total, cuaca menjadi cerah.
  - Auto-hover lepas, sub-objective tercentang selesai.

---

### B. Desa Pyrewood (Biome 2 — Wildfire Woods)
* **Kondisi Awal:** Hutan di samping desa terbakar api berkobar dan asap hitam.
* **Spawn Radius Tantangan:** Memunculkan **2 titik radius aktif**:
  1. **Radius Danau (Lake Water Source di Biome 1):**
     - Sub-misi: *"Ambil air dari Danau Zephyria untuk mengisi cadangan air (0/100)"*.
     - Naga terbang ke danau Biome 1 dan masuk ke dalam radius danau.
     - Naga masuk auto-hover di atas permukaan air.
     - Kapasitas air naga (`Water Energy`) terisi secara otomatis dari `0` sampai `100%`.
     - **Auto-Release:** Begitu kapasitas air penuh `100%`, auto-hover **langsung terlepas otomatis** (naga bebas terbang tanpa harus bingung cara keluar).
  2. **Radius Hutan Terbakar (Burned Forest di Biome 2):**
     - Sub-misi: *"Padamkan api hutan dengan semprotan air"*.
     - Naga terbang kembali ke Biome 2 dan memasuki radius hutan terbakar.
     - Naga masuk auto-hover tantangan. (Bisa escape dengan kepak sayap jika melintas tanpa sengaja).
     - Gestur Tantangan: **Open Mouth (Membuka Mulut)**.
     - Ketika mulut terbuka, naga menyemburkan semburan air sejuk (`WaterBreath VFX`) ke pepohonan yang terbakar.
     - Api berangsur padam, bar progress kebakaran terisi hingga `100%`.
* **Penyelesaian (100%):**
  - Seluruh kobaran api padam, menyisakan uap air sejuk.
  - Hutan diselamatkan, auto-hover lepas.

---

### C. Desa Solaria (Biome 3 — Drought Plateau)
* **Kondisi Awal:** Tanah retak kering tandus, terik matahari membakar, danau kering kerontang.
* **Analisis & Rancangan Tantangan (Ritual Embun Suci / Dew Summoning):**
  - *Kenapa rancangan ini terbaik?*
    - Desa A melatih kardio tangan samping (**Flap Wings**).
    - Desa B melatih navigasi antar-pulau + ekspresi wajah (**Open Mouth**).
    - Desa C melengkapi latihan kebugaran exergaming dengan melatih **peregangan bahu & postur atas** menggunakan pose CV **`Fly Up / Hands Up` (Mengangkat Kedua Tangan ke Atas)**!
* **Spawn Radius Tantangan:** Muncul di langit tinggi di atas puncak tebing Desa Solaria (`Y ~ +40m` di atas plateau).
* **Mekanisme Tantangan (Opsi A - Linear Hold):**
  - Naga terbang mendaki ke titik ketinggian tersebut dan masuk auto-hover.
  - Gestur Tantangan: **Raise Both Hands (Angkat Kedua Tangan Tinggi ke Atas)** secara stabil.
  - Lore: Fanamir adalah *Ancient Dew Dragon* (Naga Penjaga Embun). Dengan mengangkat kedua tangan ke langit, Fanamir memfokuskan energi langit untuk memanggil awan mendung dan memicu badai hujan embun pemulih (*Blessed Dew Rain*).
  - Partikel awan berkumpul, petir embun biru berkilau, dan hujan lebat mulai membasahi tanah retak.
  - Progres bar pemulihan terisi dari `0%` $\rightarrow$ `100%`.
* **Penyelesaian (100%):**
  - Hujan membasahi tanah, cekungan danau kering terisi air jernih, rumput savana mulai hijau kembali.
  - Auto-hover lepas, seluruh desa Biome 3 berhasil diselamatkan!

---

## 4. Flow Teks Quest Objective HUD (`SCREEN 02`)

Hierarki tampilan teks pada Quest Objective Panel:

```text
[GLOBAL OBJECTIVE]
"Pulihkan seluruh desa kepulauan (0/3)"

Saat misi desa diambil (misal Desa Zephyria):
[VILLAGE OBJECTIVE]
"Selesaikan misi Desa Zephyria (0%)"
  └─ "Kepakkan sayap untuk membersihkan polusi kabut"

Saat di Desa Pyrewood:
[VILLAGE OBJECTIVE]
"Selesaikan misi Desa Pyrewood (0%)"
  ├─ "Ambil air dari Danau Zephyria (Kapasitas: 0/100)"
  └─ "Padamkan api hutan dengan membuka mulut"

Saat di Desa Solaria:
[VILLAGE OBJECTIVE]
"Selesaikan misi Desa Solaria (0%)"
  └─ "Terbang tinggi dan angkat kedua tangan untuk memanggil hujan embun"
```

---

## 5. Mode Permainan di Lobby (`SCREEN 00`)

Pada Lobby Selection, terdapat 4 Kartu Level:
1. **Stage 01:** Desa Zephyria (Tantangan Kabut Asap) — Tutorial & Easy.
2. **Stage 02:** Desa Zephyria + Desa Pyrewood (Kabut & Hutan Terbakar) — Medium.
3. **Stage 03:** Ketiga Desa Sekaligus (Zephyria + Pyrewood + Solaria) — Hard / Final Exam.
4. **Free Play Mode (Level 4 - Jelajah Bebas):**
   - **Status Awal:** `LOCKED` (Terkunci dengan gembok emas).
   - **Syarat Terbuka:** Terbuka otomatis setelah pemain menyelesaikan **Stage 01, Stage 02, dan Stage 03** (masing-masing minimal meraih 1 Bintang).
   - **Aturan Free Play:**
     - Bebas tanpa batasan waktu (Timer nonaktif / `FREE FLIGHT`).
     - Seluruh pulau dalam kondisi hijau asri & bersih.
     - Pemain bebas terbang mengeksplorasi seluruh archipelago, melatih 15 pose gestur, dan menikmati pemandangan *Fanamir: The Sky Guardian*.

---

## 6. Naskah Narasi Dialog Desa (`PF_QuestNarrativeBox` · `SCREEN 08`)

Narasi diputar secara *hands-free auto-play* (3 baris teks, durasi `3.5s – 5.0s` per baris sesuai setting slider `Text Auto-Next`):

### A. Desa Zephyria (Pembicara: `TETUA ROWAN · ELDER ROWAN`)
* **Line 1/3:** *"Fanamir! Syukurlah kau mendengar seruan kami. Kabut polusi gelap telah merayap dari jurang dan mencekik seluruh lembah kami!"*
* **Line 2/3:** *"Warga kami terbatuk-batuk dan tanaman mulai layu. Udara ini terlalu pekat untuk ditembus!"*
* **Line 3/3:** *"Terbanglah sedikit lebih tinggi ke atas desa, lalu **kepakkan sayapmu sekuat tenaga** untuk menyapu bersih kabut beracun ini!"*

### B. Desa Pyrewood (Pembicara: `PENJAGA RIMBA KEITH · RANGER KEITH`)
* **Line 1/3:** *"Tolong kami, Naga Pelindung! Sambaran petir telah membakar seisi hutan pinus di samping desa kami!"*
* **Line 2/3:** *"Kobaran api merembet sangat cepat ke pemukiman, dan air di sumur kami tak cukup untuk memadamkannya!"*
* **Line 3/3:** *"Cepatlah **terbang ke Danau Zephyria untuk mengisi cadangan air**, lalu kembali ke hutan dan **buka mulutmu untuk menyemburkan air**!"*

### C. Desa Solaria (Pembicara: `TETUA MIRA · ELDER MIRA`)
* **Line 1/3:** *"Wahai Fanamir yang agung... Terik surya tanpa henti telah mengeringkan seluruh mata air di dataran batu ini."*
* **Line 2/3:** *"Tanah kami pecah merekah, danau mengering, dan kehidupan kami terancam jika tak ada setetes air pun."*
* **Line 3/3:** *"**Terbanglah tinggi ke puncak tebing**, lalu **angkat kedua tanganmu ke langit** untuk memanggil hujan embun suci pemulih tanah!"*

---

## 7. Parameter Waktu (Timer), Skor Bintang, & Win/Loss Condition (Sesi 5)

Mengingat dimensi kepulauan `2400 × 2400 m` (kecepatan terbang jelajah `~20–25 m/s` dan waktu jelajah antar biome `~25–35s`), batas waktu dan bintang dirancang seimbang dan adil:

| Parameter | Stage 01 (Tutorial) | Stage 02 (Medium) | Stage 03 (Hard / Final) |
| :--- | :--- | :--- | :--- |
| **Desa Aktif** | Desa Zephyria (1 Desa) | Zephyria + Pyrewood (2 Desa) | Zephyria + Pyrewood + Solaria (3 Desa) |
| **Batas Waktu Total** | **120 Detik** (2 Menit) | **240 Detik** (4 Menit) | **360 Detik** (6 Menit) |
| **Status Timer saat Narasi** | **PAUSE** (Berhenti) | **PAUSE** (Berhenti) | **PAUSE** (Berhenti) |
| **Status Timer saat Tantangan** | **BERJALAN** (Aktif) | **BERJALAN** (Aktif) | **BERJALAN** (Aktif) |
| ⭐⭐⭐ **3 Bintang** | Selesai, sisa waktu $\ge 60\text{s}$ | Selesai, sisa waktu $\ge 90\text{s}$ | Selesai, sisa waktu $\ge 120\text{s}$ |
| ⭐⭐ **2 Bintang** | Selesai, sisa waktu $20–59\text{s}$ | Selesai, sisa waktu $30–89\text{s}$ | Selesai, sisa waktu $30–119\text{s}$ |
| ⭐ **1 Bintang** | Selesai sebelum batas waktu | Selesai sebelum batas waktu | Selesai sebelum batas waktu |
| ❌ **Defeat / Gagal** | Waktu habis (`0s`) & desa belum pulih | Waktu habis (`0s`) & ada desa belum pulih | Waktu habis (`0s`) & ada desa belum pulih |

---

## 8. Daftar Kebutuhan Audio & SFX (`AudioManager.cs` · Sesi 5)

### A. Background Music (BGM)
1. `BGM_Lobby.mp3`: Musik perkamen akustik/harpa fantasi tenang saat berada di Start Screen & Main Menu Lobby.
2. `BGM_Flight_Adventure.mp3`: Musik petualangan orkestra megah yang memberi rasa kebebasan saat terbang melintasi kepulauan.
3. `BGM_Village_Challenge.mp3`: Musik dinamis dengan tempo drum mantap saat naga berada di dalam zona tantangan aktif.

### B. Sound Effects (SFX)
1. **Fanamir & Penerbangan:**
   - `SFX_Dragon_Flap.wav`: Suara kepakan sayap naga bertenaga menghembus angin.
   - `SFX_Dragon_GlideWind.wav`: Desau angin halus (*ambient loop*) saat naga meluncur (*Glide*).
   - `SFX_Dragon_DiveWhoosh.wav`: Desau angin kencang berkecepatan tinggi saat naga menukik (*Tuck Dive*).
   - `SFX_Water_Breath.wav`: Semburan air bertekanan deras saat pose buka mulut di hutan terbakar.
   - `SFX_Dew_Summon_Chime.wav`: Bunyi dentang kristal suci saat pose mengangkat tangan ke langit memanggil hujan embun.
2. **Interaksi Misi & Lingkungan:**
   - `SFX_Water_Absorb.wav`: Suara gelembung air mengisi cadangan energi naga di danau Biome 1.
   - `SFX_Fire_Extinguish.wav`: Suara desisan uap dan api padam tersiram air.
   - `SFX_Smog_Disperse.wav`: Hembusan angin kencang menyapu dan melarutkan kabut asap.
   - `SFX_Village_Restored_Fanfare.wav`: Jingle sukacita saat suatu desa berhasil pulih 100%.
3. **UI & Game Loop:**
   - `SFX_UI_Click.wav`: Ketukan tombol perkamen RPG.
   - `SFX_Quest_Accepted.wav`: Denting gulungan perkamen misi resmi dibuka.
   - `SFX_Dialogue_Next.wav`: Suara tuts halus saat auto-play berganti baris teks narasi.
   - `SFX_Victory_Fanfare.wav`: Fanfare kemenangan orkestra dan suara gemerincing bintang emas saat Finish Modal muncul.
   - `SFX_Defeat_Gong.wav`: Suara gong berat saat waktu habis / misi gagal.
