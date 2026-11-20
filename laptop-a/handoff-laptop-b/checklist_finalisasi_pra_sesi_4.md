---
title: Checklist Finalisasi & Prasyarat Pra-Sesi 4 (Mekanisme Tantangan Desa)
tags:
  - checklist
  - laptop-a
  - laptop-b
  - sesi-4
  - quest-mechanics
  - cv-control
date: 2026-11-17
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[mekanisme_tantangan_desa]]"
  - "[[laptop B Patch 1 planning]]"
  - "[[planning_audit_pose_dan_clean_cv_preview]]"
  - "[[planning_revisi_titik_wajah_dan_pov]]"
  - "[[rencana_skill_vfx_effekseer_laptop_b]]"
---

# 📋 Checklist Finalisasi & Prasyarat Pra-Sesi 4 (Mekanisme Tantangan Desa)

> Dokumen ini merinci seluruh komponen, mekanik, pengujian, dan aset yang **wajib diselesaikan dan difinalisasi** sebelum masuk ke penulisan skrip `VillageZoneController.cs` dan perakitan tantangan 3 desa pada **Sesi 4**.
> 
> *Prinsip Utama:* **"Jangan bangun logika quest di atas kontrol yang belum stabil."** Pastikan input gestur 15 pose dan pergerakan naga telah teruji 100% responsif di webcam nyata.

---

## 🧭 Ringkasan 5 Pilar Prasyarat Pra-Sesi 4

```text
┌──────────────────────────────────────────────────────────────────────────────┐
│                    5 PILAR PRASYARAT SEBELUM SESI 4                         │
├──────────────────┬──────────────────┬──────────────────┬─────────────────────┤
│ 1. KONTROL & CV  │ 2. AUDIT 15 POSE │ 3. SCENE MARKER  │ 4. CONTROLLER NAGA  │
│ • Bersihkan Teks │ • Test webcam    │ • Pasang 3 Desa  │ • Auto-Hover state  │
│   (Kecuali FPS)  │   oleh pemain    │ • Pasang 3 Sub-  │ • Flap to Escape    │
│ • Tahap 2 Unity  │ • Screenshot bug │   Marker         │ • Auto-Release Lake │
│   HEAD_PITCH     │ • Tuning ambang  │ • Waypoint HUD   │ • Decouple yaw/head │
└──────────────────┴──────────────────┴──────────────────┴─────────────────────┘
                                       │
                                       ▼
                       ┌───────────────────────────────┐
                       │ 5. SISTEM VFX (EFFEKSEER)     │
                       │ • Pasang UPM Effekseer URP    │
                       │ • Slot Emitter: Kabut, Api,   │
                       │   WaterBreath, & Hujan Embun  │
                       └───────────────────────────────┘
```

---

## 🛠️ Pilar 1: Finalisasi Kontrol & CV (Laptop B)

### 1.1. Pembersihan Teks Preview CV (`active_cv_sender.py` & PIP)
- [ ] **Kosongkan seluruh teks HUD telemetri pada preview OpenCV dan feed PIP:**
  - Hapus background box hitam telemetri.
  - Hapus tampilan teks float `pitch`, `mouth_open`, dan label nama pose.
  - **Pertahankan HANYA tampilan FPS:** Angka FPS (`FPS: xx.x`) tetap ditampilkan kecil dan bersih di sudut atas layar.
- [ ] **Pertahankan visualisasi kerangka murni:**
  - Garis kerangka tubuh (skeleton lines) & titik sendi COCO.
  - 3 Titik wajah aktif: Hidung (`#4`), Dagu (`#152`), dan Mulut (`#13` & `#14`).

### 1.2. Tahap 2 Integrasi `HEAD_PITCH` ke Unity Controller
- [ ] **Koneksi Token 44 (`HEAD_PITCH`) di `DragonFlightController.cs` & `UDPPoseReceiver.cs`:**
  - Hubungkan sinyal `HEAD_PITCH` untuk mengendalikan elevasi pandangan leher/kepala naga (`DragonHeadLookController`).
  - Mendongakkan dagu ke atas memicu dorongan vertikal naik (**Fly Up**) secara nyaman tanpa mengharuskan pemain membusungkan dada.
  - Menundukkan dagu sedikit untuk membantu manuver turun (**Descend**).
- [ ] **Penguncian Kemudi Horizontal (Decoupling Rule):**
  - Pastikan gerakan kepala (tengok/miring) **TIDAK membelokkan arah terbang naga** secara horizontal.
  - Belok kiri/kanan (*yaw & roll banking*) murni dikendalikan oleh kemiringan badan dan perbedaan lengan (**Lean & Arm Tilt**).
- [ ] **Penyelarasan `#03 Grounded Look Up`:**
  - Saat naga berada di tanah (*grounded*), mengangkat dagu cukup untuk mendongakkan kepala naga ke arah langit tanpa elevasi dada.

---

## 📸 Pilar 2: Audit Empiris 15 Pose Berbasis Screenshot (Pemain & Laptop B)

Sebelum logika tantangan desa ditulis, pemain perlu memastikan seluruh pose yang menjadi syarat tantangan desa berjalan dengan mulus:
- **Desa Zephyria:** Membutuhkan Pose `#06 Flap to Climb` (kepakan sayap kontinyu).
- **Desa Pyrewood:** Membutuhkan Pose `#15 Village Action` (Mouth Open / Semburan Air).
- **Desa Solaria:** Membutuhkan Pose `#07 Fly Up / Raise Both Hands` (mengangkat kedua tangan ke langit).

### Protokol Audit Screenshot:
1. Pemain membuka game dengan webcam aktif dan membuka buku panduan [`Panduan_15_Pose_Fanamir.pdf`](file:///e:/Semester%205/Game/AR%20Dragon/Panduan_15_Pose_Fanamir.pdf).
2. Pemain memperagakan pose `#01` hingga `#15`.
3. Jika ada pose yang **tidak terdeteksi**, **sulit terpicu**, atau **sering salah terbaca (false positive)**:
   - Pemain mengambil tangkapan layar (screenshot) tampilan skeleton webcam.
   - Laptop B memeriksa nilai ambang batas (*threshold*), sudut sendi, atau *One-Euro filter* yang bersangkutan.
4. Ulangi tuning hingga ke-15 pose terverifikasi lulus 100%.

---

## 🗺️ Pilar 3: Scene Gameplay, Koordinat, & Navigasi Waypoint (`MainIsland_Gameplay.unity`)

### 3.1. Penempatan & Verifikasi Marker 3 Desa & Sub-Marker di Hierarki Unity
- [ ] **Desa Zephyria (Biome 1 — Smog Valley):** `(-562.40, 14.10, 274.30)`
  - Silinder Narasi: $R = 35\text{ m}, Y \le 40\text{ m}$.
  - Silinder Tantangan Kabut: $R = 48\text{ m}$ pada ketinggian `(-562.40, 39.10, 274.30)` ($Y \approx +25\text{ m}$).
- [ ] **Desa Pyrewood (Biome 2 — Wildfire Woods):** `(739.60, 12.90, 7.80)`
  - Silinder Narasi: $R = 35\text{ m}, Y \le 40\text{ m}$.
  - Sub-Marker 1 (Danau Zephyria Biome 1): Silinder $R = 45\text{ m}$ di atas air `SM_Biome1_Water_01`.
  - Sub-Marker 2 (Hutan Terbakar Biome 2): Silinder $R = 50\text{ m}$ di atas pepohonan hangus.
- [ ] **Desa Solaria (Biome 3 — Drought Plateau):** `(39.40, 14.70, -647.60)`
  - Silinder Narasi: $R = 35\text{ m}, Y \le 40\text{ m}$.
  - Silinder Tantangan Sky Altar: Silinder $R = 48\text{ m}$ pada ketinggian `(39.40, 54.70, -647.60)` ($Y \approx +40\text{ m}$).
- [ ] **World-Space Billboard Markers (< 150m):**
  - Pasang marker melayang di atas balai desa / sub-marker yang aktif saat jarak $\le 150\text{ m}$ menghadap kamera + label nama dan jarak.

### 3.2. Integrasi Waypoint Compass HUD (`WaypointCompass.cs`)
- [ ] Hubungkan `WaypointCompass` di HUD atas (`SCREEN 02`) dengan penanda aktif / desa terdekat yang belum selesai.
- [ ] Verifikasi jarum kompas menunjuk akurat ke arah desa tujuan saat naga terbang berputar (`Vector3.SignedAngle`).
- [ ] Tampilkan label nama lokasi dan jarak meter yang berkurang seiring naga mendekat.

---

## 🐉 Pilar 4: Logika Mekanik Auto-Hover & Escape di `DragonFlightController.cs`

Tantangan desa membutuhkan transisi penerbangan khusus agar pemain tidak kesulitan mengendalikan naga saat berdialog atau melakukan aksi tantangan:

- [ ] **Fungsi `EnterAutoHover(Vector3 targetCenter, Quaternion targetOrientation)`:**
  - Kecepatan horizontal naga melambat secara halus ke `0 m/s` dan momentum dibekukan (*frozen*).
  - Ketinggian naga dipertahankan stabil di udara (*gentle vertical bobbing* $\pm 0.3\text{ m}$).
  - Badan naga perlahan ditarik halus (*lerp*) ke titik jangkar (*anchor*) dan berotasi menghadap titik pusat desa / krisis lingkungan.
- [ ] **Mekanisme Pembatalan Darurat / Escape (`Flap/Dive to Escape`):**
  - **PENTING:** Pemain tidak boleh merasa "terkunci" atau terjebak dalam auto-hover!
  - **Di Zona Narasi & Danau/Hutan/Solaria:** Gestur **Flap (`#06`)**, **Tuck Dive (`#13`)**, atau **Propeller Dash (`#14`)** membatalkan auto-hover.
  - **Khusus Zona Tantangan Zephyria:** Karena *Flap* adalah gestur tantangan kabut, pemain escape menggunakan **Tuck Dive (`#13`)** atau **Propeller Dash (`#14`)**.
  - **Dorongan Maju (*Launch Thrust*):** Saat escape atau auto-release, naga langsung diberikan dorongan momentum awal ~30–35% agar meluncur mulus ke depan.
  - **Cooldown Re-Trigger:** Wajib keluar dari silinder radius + cooldown **`3.5 detik`** sebelum zona bisa terpicu lagi.
- [ ] **Mekanisme `Auto-Release` (Khusus Danau Biome 1):**
  - Saat mengisi air di Danau Zephyria, begitu kapasitas mencapai `100%`, auto-hover terlepas secara otomatis dan naga kembali meluncur maju.
- [ ] **Isolasi UI saat Narasi:**
  - Saat narasi aktif, seluruh HUD lain disembunyikan sementara, hanya menyisakan `PF_QuestNarrativeBox` dan `QuestObjectivePanel`.
- [ ] **Victory Trigger Selesai Stage:**
  - Begitu desa terakhir di stage mencapai 100%, naga auto-hover di tempat selama 2.5–3 detik merayakan pemulihan diiringi fanfare, lalu langsung memunculkan `PF_FinishModal`.

---

## ✨ Pilar 5: Kesiapan Sistem VFX (Plugin Effekseer di Laptop B)

Mekanisme tantangan desa sangat bergantung pada umpan balik visual (*visual feedback*) agar pemain merasakan dampak langsung dari gestur tubuh mereka:

- [ ] **Instalasi Plugin & Skill Effekseer di Laptop B:**
  - Mengacu pada [[rencana_skill_vfx_effekseer_laptop_b]], pasang paket `kanaworksai-hue/effekseer-vfx-skill`.
  - Impor plugin resmi `EffekseerForUnity` dan aktifkan `EffekseerRendererFeature` pada Forward Renderer Data URP Unity 6.
- [ ] **Kesiapan Slot VFX Tantangan 3 Desa:**
  - **Desa Zephyria:** Emitter kabut asap tebal (*Smog Particles*) yang transparansi atau densitasnya dapat di-lerp turun dari `1.0` ke `0.0` seiring kepakan sayap.
  - **Desa Pyrewood:**
    - Emitter kobaran api & asap hutan (*Wildfire Flames & Smoke*).
    - Emitter semburan air dari mulut naga (*WaterBreath Stream*) yang muncul saat `MOUTH_OPEN > 0.45`.
    - Emitter uap air putih (*Steam*) saat api tersiram air.
  - **Desa Solaria:**
    - Aura pilar energi langit (*Dew Summoning Beacon*).
    - Partikel hujan embun berkilau (*Blessed Dew Rain*) yang membasahi savana.

---

## 🎮 Urutan Eksekusi Menuju Sesi 4

| Tahap | Penanggung Jawab | Deskripsi Tugas | Status |
| :--- | :--- | :--- | :---: |
| **Tahap A** | Laptop B | Bersihkan preview CV (kecuali FPS) & hubungkan `HEAD_PITCH` di controller Unity. | ⏳ Menunggu |
| **Tahap B** | Pemain / User | Jalankan audit 15 pose dengan webcam; screenshot pose yang bermasalah. | ⏳ Menunggu |
| **Tahap C** | Laptop B | Tuning ambang batas gestur berdasarkan screenshot hasil audit. | ⏳ Menunggu |
| **Tahap D** | Laptop B | Pasang marker 3 desa, danau, hutan terbakar, & Sky Altar di `MainIsland_Gameplay.unity`. | ⏳ Menunggu |
| **Tahap E** | Laptop B | Siapkan metode `EnterAutoHover`, `Flap to Escape`, dan instal plugin Effekseer. | ⏳ Menunggu |
| **Tahap F** | **Sesi 4 Dimulai** | **Implementasi `VillageZoneController.cs`, `Session4_VillageBuilder.cs`, & Quest Flow!** | 🚀 Siap |
