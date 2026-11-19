---
title: Mekanisme Tantangan Desa & Flow Misi (Sesi 4 & 5) — Final Approved
tags:
  - gameplay
  - quest
  - village
  - laptop-a
  - mechanics
  - sesi-4
owner: Laptop A
updated: 2026-11-19
status: approved
---

# 📜 Mekanisme Tantangan Desa & Flow Quest — Fanamir: The Sky Guardian

Dokumen ini adalah **Single Source of Truth Final** untuk implementasi mekanisme tantangan 3 desa (*Desa Zephyria, Desa Pyrewood, Desa Solaria*), sistem interaksi silinder, *Auto-Hover*, *Flap/Dive to Escape*, pelacakan sub-objektif, *World-Space Floating Markers*, isolasi UI narasi, dan *Victory Trigger* pada **Sesi 4** (`VillageZoneController.cs`, `Session4_VillageBuilder.cs`, World Markers, UI Integration) serta penyelarasan menuju **Sesi 5** (`GameManager.cs`, Scoring/Stars, Free Play) di Laptop B.

---

## 1. Identitas, Tematik, & Titik Jangkar 3 Desa

| Biome & Nama Resmi Desa | Masalah Krisis Lingkungan | Koordinat Pad Desa (`MainIsland_Gameplay`) | Titik Sub-Objektif & Ketinggian Tantangan |
| :--- | :--- | :--- | :--- |
| **Biome 1 — Desa Zephyria** *(Valley of Winds)* | Kabut polusi pekat merayap dari lembah jurang | `(-562.40, 14.10, 274.30)` | • **Zona Narasi Desa:** Pad pemukiman desa `(Y ~ 14.1m)`<br>• **Zona Tantangan Kabut:** Melayang di atas lembah `(-562.40, 39.10, 274.30)` (`Y ~ +25m`) |
| **Biome 2 — Desa Pyrewood** *(Cinder Forest)* | Hutan pinus terbakar hebat oleh sambaran petir | `(739.60, 12.90, 7.80)` | • **Zona Narasi Desa:** Pad pemukiman desa `(Y ~ 12.9m)`<br>• **Sub-Objektif 1 (Danau Zephyria Biome 1):** Di atas air `SM_Biome1_Water_01` `(-450.0, 16.0, 180.0)`<br>• **Sub-Objektif 2 (Hutan Terbakar Biome 2):** Di atas pepohonan hangus `(770.0, 25.0, 50.0)` |
| **Biome 3 — Desa Solaria** *(Arid Highlands)* | Kekeringan ekstrem, tanah retak & mata air kering | `(39.40, 14.70, -647.60)` | • **Zona Narasi Desa:** Pad pemukiman plateau `(Y ~ 14.7m)`<br>• **Zona Tantangan Sky Altar:** Puncak tebing plato `(39.40, 54.70, -647.60)` (`Y ~ +40m`) |

---

## 2. Arsitektur Interaksi Zona & State Mesin Desa

Interaksi zona menggunakan **Silinder Vertikal (XZ Distance + Threshold Ketinggian Y)** untuk mencegah aktivasi tak sengaja saat pemain terbang tinggi melintas:

```text
       ┌────────────────────────────────────────────────────────┐
       │                ZONA 1: NARASI DESA                     │
       │ • Bentuk: Silinder Vertikal (R = 35m, Y <= 40m)        │
       │ • Kondisi: Pemain terbang rendah mendekati desa        │
       │ • Aksi: Ditarik ke Anchor Desa, Hadap Balai Desa       │
       │ • Kamera: Fixed Flight Cam (standar tanpa disorientasi)│
       │ • Timer Stage: PAUSE (Berhenti)                        │
       │ • UI: ISOLASI PENUH -> Hanya Quest Box & Objective     │
       │ • Escape: Flap / Tuck Dive / Dash -> Reset baris 1     │
       └───────────────────────────┬────────────────────────────┘
                                   │ Narasi selesai (Accepted)
                                   ▼
       ┌────────────────────────────────────────────────────────┐
       │                ZONA 2: TANTANGAN SPESIFIK              │
       │ • Bentuk: Silinder Vertikal Lebih Lebar (R = 45–50m)   │
       │ • Lokasi: Di atas lembah / Danau / Hutan / Sky Altar   │
       │ • Aksi: Ditarik ke Anchor Tantangan, Hadap Pusat Krisis│
       │ • Timer Stage: BERJALAN (Aktif untuk kalkulasi skor)   │
       │ • UI: PF_VillageChallengeOverlay (Progress Bar + Pose) │
       │ • Progres: NO DECAY (Tidak berkurang saat jeda/escape) │
       │ • Escape Zephyria: Tuck Dive (#13) / Propeller Dash    │
       │ • Escape Zona Lain: Flap (#06) / Tuck Dive / Dash      │
       └────────────────────────────────────────────────────────┘
```

### A. Detail Zona 1 — Narasi Desa
1. **Trigger Silinder:** Jarak horizontal $XZ \le 35\text{ m}$ dan ketinggian naga $Y \le 40\text{ m}$ (jika melintas di atas $Y > 40\text{ m}$, narasi tidak terpicu).
2. **Auto-Hover & Anchor:** Naga ditarik halus (*lerp position & rotation*) ke **Titik Jangkar Narasi Desa**, otomatis memainkan animasi terbang di tempat (*hover*), dan badan naga berputar menghadap balai desa.
3. **Kamera:** Menggunakan **Fixed Flight Cam (Opsi B)** — kamera tetap di posisi *chase camera* normal agar pemain tidak mengalami disorientasi pandangan.
4. **Isolasi UI Narasi:** Saat narasi aktif, seluruh widget HUD (dock telemetri, kompas, PIP kamera) **disembunyikan sementara**, menyisakan **HANYA**:
   - `PF_QuestNarrativeBox` (`SCREEN 08`, teks auto-play dialog 3-baris).
   - `QuestObjectivePanel` (`SCREEN 02`, kiri atas).
5. **Timer Stage:** **PAUSE** (waktu misi berhenti berhitung selama narasi diputar).
6. **Mekanik Escape & Reset:** Pemain dapat membatalkan auto-hover kapan saja dengan gestur **Flap (`#06`)**, **Tuck Dive (`#13`)**, atau **Propeller Dash (`#14`)**.
   - Jika *escape* terjadi di tengah narasi, timer stage kembali berjalan, UI HUD normal kembali, dan quest belum diambil.
   - **Aturan Reset:** Jika pemain masuk kembali ke desa tersebut, **narasi diulang dari baris 1**.
   - **Cooldown Anti Re-Trigger:** Pemain wajib keluar dari silinder radius dan ada jeda aman **`3.5 detik`** sebelum zona desa dapat terpicu kembali.

### B. Detail Zona 2 — Zona Tantangan Spesifik
1. **Trigger Silinder:** Muncul setelah narasi desa selesai (`Accepted`). Radius silinder dibuat **lebih lebar ($R = 45\text{–}50\text{ m}$)** agar naga yang terbang kencang ($20\text{–}30\text{ m/s}$) mudah memasukinya tanpa meleset.
2. **Auto-Hover & Anchor:** Naga ditarik halus ke **Titik Jangkar Tantangan** dan berputar menghadap pusat bencana lingkungan.
3. **Timer Stage:** **BERJALAN** (waktu terus berkurang demi perhitungan bintang 1–3).
4. **Tampilan UI:** Memunculkan overlay tantangan **`PF_VillageChallengeOverlay` (`SCREEN 03`)** dengan instruksi gestur dan progress bar $0\% \rightarrow 100\%$.
5. **Aturan No Decay:** Seluruh progres pengisian bar mengadopsi aturan **No Decay (Zero Regression)**: jika pemain berhenti bergerak untuk mengatur napas atau melakukan *escape* sementara, progres **tetap bertahan** pada angka terakhir yang dicapai.
6. **Mekanik Escape Khusus Zona Tantangan:**
   - **Desa Zephyria:** Karena *Flap* adalah gestur inti untuk membersihkan kabut, **Flap TIDAK MEMBATALKAN auto-hover**. Untuk escape/keluar sebelum selesai, pemain menggunakan gestur **Tuck to Dive (`#13`)** atau **Propeller Dash (`#14`)**.
   - **Danau Zephyria / Hutan Pyrewood / Solaria:** Pemain dapat escape menggunakan **Flap (`#06`)**, **Tuck Dive (`#13`)**, atau **Propeller Dash (`#14`)**.
   - **Cooldown:** Wajib keluar dari silinder + jeda **`3.5 detik`** sebelum zona bisa terpicu lagi.
7. **Perilaku Momentum:** Saat memasuki Auto-Hover, momentum penerbangan naga dibekukan (*frozen*). Saat *escape* maupun *auto-release*, naga diberikan **dorongan dorong awal (*launch thrust*, momentum ~30–35%)** ke arah depan agar langsung meluncur mulus tanpa macet (*stall*).

---

## 3. Detail Mekanisme & Balancing per Tantangan Desa

### A. Desa Zephyria (Biome 1 — Smog Valley)
* **Krisis:** Kabut asap tebal meracuni lembah hijau.
* **Titik Tantangan:** Di atas pemukiman desa `(-562.40, 39.10, 274.30)` ($Y \approx +25\text{ m}$ di atas tanah).
* **Gestur:** **Flap Wings (Kepakkan Sayap)** secara berulang (`#06` / `#15A`).
* **Balancing:**
  - Butuh akumulasi **`8–10` kali kepakan** sayap bertenaga (~`10%–12%` per kepakan).
  - Durasi target: **`~8–10 detik`**.
  - **No Decay:** Berhenti mengepak tidak mengurangi bar progres.
* **Penyelesaian (100%):** Kabut menghilang, auto-hover terlepas dengan dorongan maju, status desa tercentang pulih.

### B. Desa Pyrewood (Biome 2 — Wildfire Woods) — 2 Tahap
* **Krisis:** Hutan pinus di samping pemukiman dilalap api membara.
* **Tahap 1 — Pengisian Air (Danau Zephyria Biome 1):**
  - Pemain dipandu terbang menuju Danau Zephyria `SM_Biome1_Water_01`.
  - Masuk silinder danau ($R = 45\text{ m}$, di atas permukaan air) $\rightarrow$ naga auto-hover.
  - Tampil `PF_VillageChallengeOverlay` dengan judul *"Mengisi Cadangan Air Purba (0% → 100%)"*.
  - Bar terisi secara otomatis dengan laju `20%–25%/detik` (durasi **`4–5 detik`**).
  - **Auto-Release:** Tepat saat air mencapai `100%`, auto-hover **langsung terlepas otomatis** disertai dorongan terbang maju (*launch thrust*).
* **Tahap 2 — Pemadaman Hutan Terbakar (Hutan Biome 2):**
  - Naga terbang kembali ke Biome 2 menuju area hutan terbakar.
  - Masuk silinder hutan ($R = 50\text{ m}$) $\rightarrow$ naga auto-hover menghadap kobaran api pohon hangus.
  - Tampil `PF_VillageChallengeOverlay` memandu gestur **Open Mouth (`#15B`, Water Breath)**.
  - **Deteksi CV (Hysteresis):** Aktif saat `MOUTH_OPEN > 0.35`, mati saat `MOUTH_OPEN < 0.25`.
  - **Balancing:** Butuh akumulasi menahan mulut terbuka selama **`~6–8 detik`** (~`14%–16%/detik`).
  - **No Decay:** Menutup mulut sejenak tidak mengurangi bar progres pemadaman api.
* **Penyelesaian (100%):** Api padam berganti kepulan uap air sejuk, auto-hover lepas dengan dorongan maju, hutan selamat.

### C. Desa Solaria (Biome 3 — Drought Plateau)
* **Krisis:** Kekeringan tanah retak tandus di plato batu terik.
* **Titik Tantangan:** Sky Altar Solaria di udara puncak tebing `(39.40, 54.70, -647.60)` ($Y \approx +40\text{ m}$ di atas plato).
* **Gestur:** **Double I-Pose `11` (`#15C`, Dew Summoning)**.
  - Kedua tangan terangkat lurus vertikal sejajar di atas kepala (terpisah tegas dari `#07 Fly Up` serong Y).
* **Balancing:**
  - Butuh menahan pose `11` secara stabil selama **`~7–9 detik`** (~`12%–14%/detik`).
  - **No Decay:** Menurunkan tangan untuk istirahat tidak mengurangi bar progres.
* **Penyelesaian (100%):** Hujan embun turun, 2 cekungan danau kering terisi air, tanah kembali hijau, auto-hover lepas dengan dorongan maju.

---

## 4. World-Space Billboard Markers (< 150m) & Navigasi

Selain navigasi makro pada `WaypointCompass` di bagian atas HUD, sistem dilengkapi **World-Space Billboard Marker (Opsi A)**:
1. **Jarak Pemicu:** Saat jarak naga dengan target desa/sub-objektif $\le 150\text{ m}$.
2. **Visual Komponen:**
   - Ikon medali/crest desa melayang di udara tepat di atas balai desa / titik tantangan ($Y + 12\text{ m}$).
   - Teks nama lokasi + indikator jarak dinamis: misal `DESA ZEPHYRIA · 85m` atau `DANAU ZEPHYRIA · 120m`.
3. **Billboard Behaviour:** Selalu menghadap ke kamera pemain (*Camera.main Billboard*).
4. **Distance Scaling:** Ukuran ikon dan teks membesar/mengecil secara halus mengikuti jarak kamera sehingga selalu terbaca jelas tanpa menutupi lingkungan.

---

## 5. Kebebasan Urutan Misi (Open-World Multi-Village)

Pada **Stage 02 (2 Desa)** dan **Stage 03 (3 Desa)**:
- Pemain **BEBAS memilih urutan desa** mana saja yang ingin diselamatkan terlebih dahulu (*non-linear freedom*).
- `WaypointCompass` dan `QuestObjectivePanel` secara otomatis mengarahkan ke **desa terdekat yang belum diselesaikan**, atau berganti fokus ke desa yang sedang didekati pemain.
- Progres `GlobalVitalityBar` bertambah secara proporsional:
  - **Stage 01 (1 Desa):** `0% → 100%`.
  - **Stage 02 (2 Desa):** `0% → 50% → 100%`.
  - **Stage 03 (3 Desa):** `0% → 33% → 67% → 100%`.

---

## 6. Selesai Misi & Victory Trigger (Opsi A)

Begitu desa terakhir pada stage tersebut berhasil dipulihkan (Global Vitality mencapai $100\%$):
1. **Auto-Hover Selebrasi:** Naga langsung masuk auto-hover di tempat.
2. **Jingle Kemenangan Singkat:** Pemain menikmati pemandangan desa yang telah pulih selama **`~2.5–3.0 detik`** diiringi suara fanfare kemenangan (*Victory Fanfare*).
3. **Trigger Finish Modal:** Layar **`PF_FinishModal` (`SCREEN 04`)** langsung muncul di layar:
   - Evaluasi bintang $1–3$ berdasarkan sisa waktu stage.
   - Tombol `LEVEL BERIKUTNYA` (jika Stage 01/02), `COBA LAGI`, atau `KEMBALI KE LOBBY`.
   - Pemain **tidak perlu** terbang kembali ke Center Peak untuk menyelesaikan misi.

---

## 7. Naskah Narasi Auto-Play (`PF_QuestNarrativeBox`)

Dialog dimainkan otomatis 3 baris (*hands-free auto-play*, kecepatan diatur slider `Text Auto-Next`, default 5.0 detik/baris):

### A. Desa Zephyria (Tetua Rowan)
* **Baris 1:** *"Fanamir! Syukurlah kau mendengar seruan kami. Kabut polusi gelap telah merayap dari jurang dan mencekik seluruh lembah kami!"*
* **Baris 2:** *"Warga kami terbatuk-batuk dan tanaman mulai layu. Udara ini terlalu pekat untuk ditembus!"*
* **Baris 3:** *"Terbanglah ke atas desa, lalu **kepakkan sayapmu sekuat tenaga** untuk menyapu bersih kabut beracun ini!"*

### B. Desa Pyrewood (Penjaga Rimba Keith)
* **Baris 1:** *"Tolong kami, Naga Pelindung! Sambaran petir telah membakar seisi hutan pinus di samping desa kami!"*
* **Baris 2:** *"Kobaran api merembet cepat ke pemukiman, dan air sumur kami tak cukup untuk memadamkannya!"*
* **Baris 3:** *"Cepatlah **ambil air di Danau Zephyria**, lalu kembali ke hutan dan **buka mulutmu untuk menyemburkan air**!"*

### C. Desa Solaria (Tetua Mira)
* **Baris 1:** *"Wahai Fanamir yang agung... Terik surya tanpa henti telah mengeringkan seluruh mata air di dataran batu ini."*
* **Baris 2:** *"Tanah kami pecah merekah, danau mengering, dan kehidupan kami terancam jika tak ada setetes air pun."*
* **Baris 3:** *"**Terbanglah tinggi ke puncak tebing**, lalu **angkat kedua tanganmu lurus ke langit** untuk memanggil hujan embun suci pemulih tanah!"*

---

## 7. Parameter Waktu (Timer), Skor Bintang, & Win/Loss Condition (Alokasi Resmi: Sesi 5)

> *Catatan Roadmap:* Sistem batas waktu ketat, kalkulasi 1–3 bintang, kondisi Defeat, dan evaluasi `FinishModalUI` secara penuh dialokasikan pada **Sesi 5 (`GameManager.cs`)**. Pada **Sesi 4**, fokus murni pada mekanisme interaksi desa, auto-hover, sub-marker, dan pengisian progres tantangan (No Decay).

### A. Pembukaan Seluruh Level Sementara (Testing Mode — Sesi 4)
Agar pemain/developer dapat langsung menguji mekanisme tantangan di ketiga desa tanpa terhambat sistem progresi:
- **Default Stage Lock:** Sementara seluruh kartu level (**Stage 01, Stage 02, Stage 03**) **dibuka penuh (`UNLOCKED`)** di Lobby `SCREEN 00`.
- Scrim gembok (`UI_Scrim_LockedCard_Overlay`) dinonaktifkan, tombol charcoal `LOCKED` diganti menjadi tombol hijau `PLAY`, dan pemain bebas memilih serta menguji Stage mana pun langsung dari Lobby.
- Parameter konfigurasi: `DragonGameSettings.UnlockAllStagesForTesting = true`.

### B. Tabel Acuan Batas Waktu & Kriteria Bintang (Untuk Sesi 5)

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

## 9. Ringkasan Parameter & Konstanta Teknis untuk Kode C#

```csharp
// Parameter Silinder & Jarak
public const float NARRATIVE_RADIUS = 35.0f;
public const float NARRATIVE_MAX_ALTITUDE = 40.0f;
public const float CHALLENGE_RADIUS = 48.0f;
public const float WORLD_MARKER_VISIBLE_DIST = 150.0f;

// Parameter Cooldown & Transisi
public const float ESCAPE_COOLDOWN_SEC = 3.5f;
public const float LAUNCH_MOMENTUM_IMPULSE = 32.0f; // Dorongan maju saat escape/auto-release

// Parameter Kecepatan Progres (No Decay)
public const float ZEPHYRIA_FLAP_PER_STROKE = 0.11f;   // 8-10 kepakan = 100%
public const float PYREWOOD_LAKE_FILL_RATE = 0.22f;    // ~4.5 detik = 100%
public const float PYREWOOD_MOUTH_FIRE_RATE = 0.15f;   // ~6.7 detik tahan mulut = 100%
public const float SOLARIA_DEW_SUMMON_RATE = 0.125f;   // ~8.0 detik tahan pose 11 = 100%

// Hysteresis Bukaan Mulut Pyrewood
public const float MOUTH_OPEN_ENTER = 0.35f;
public const float MOUTH_OPEN_EXIT = 0.25f;
```

---
*Navigasi Vault:* [[00 - Overview]] | [[Tasks]] | [[Devlog]] | [[checklist_finalisasi_pra_sesi_4]] | [[kontrak_integrasi_ui_dan_progress]]
