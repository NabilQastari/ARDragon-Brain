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
