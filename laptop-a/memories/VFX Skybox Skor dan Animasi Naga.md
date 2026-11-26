---
title: VFX Skybox Skor dan Animasi Naga
type: session-memory
tags:
  - laptop-a
  - session-memory
  - capstone-report
  - vfx-effekseer
  - dynamic-fov
  - skybox
  - game-loop
  - star-scoring
  - dragon-animation
date: 2026-11-26
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[visual-vfx/planning_vfx_fov_skybox_dan_animasi]]"
  - "[[visual-vfx/prompt_eksekusi_vfx_skybox_dan_fov_laptop_b]]"
  - "[[gameplay-mechanics/arsitektur_sesi_5_gameloop_dan_scoring]]"
  - "[[gameplay-mechanics/prompt_eksekusi_sesi_5_gameloop_dan_scoring_laptop_b]]"
  - "[[visual-vfx/arsitektur_animasi_naga_fanamir]]"
  - "[[visual-vfx/prompt_eksekusi_animasi_naga_laptop_b]]"
  - "[[handoff-laptop-b/panduan_screenshot_mandiri_dan_laporan]]"
---

# 🌌 VFX Skybox Skor dan Animasi Naga

> **Konteks Sesi:** Sesi kolaborasi perancangan dan arsitektur komprehensif di **Laptop A** yang mencakup penyematan 12 gambar ke draf Laporan Capstone `.docx`, perumusan SOP screenshot mandiri Laptop B, finalisasi Paket Visual Sesi 4B (VFX Effekseer, Dynamic FOV 85°, & Dynamic Biome Skybox), pembakuan sistem skor bintang berbasis Progres Global Sesi 5, serta perancangan sistem animasi tubuh prosedural naga Fanamir (`DragonBodyAnimator.cs`).

---

## 1. Tujuan & Konteks Sesi

1. **Penyusunan & Embedding Aset Laporan Capstone:** Menyematkan seluruh 12 gambar yang tersedia di Laptop A langsung ke dalam berkas Microsoft Word [`docs/Laporan_Capstone_Fanamir.docx`](file:///e:/Semester%205/Game/AR%20Dragon/docs/Laporan_Capstone_Fanamir.docx) (~4.6 MB), menyisakan 5 placeholder bertanda tegas untuk tangkapan layar langsung oleh Laptop B.
2. **SOP Screenshot Mandiri Laptop B:** Menjawab kebutuhan dokumentasi visual otomatis pasca-sesi dengan menyusun panduan screenshot mandiri (C# Unity, Python CV, & OS-level) dan membuat folder staging `docs/figures/raw_screenshots/`.
3. **Paket Visual & Flight Feel (Sesi 4B):** Mengunci formula Dynamic Camera FOV ($60^\circ–85^\circ$), Dynamic Biome-Adaptive Skybox & URP Horizon Fog Blending 3 Biome, serta katalog 7 efek visual partikel Effekseer (`.efkefc`).
4. **Game Loop & Sistem Penilaian 3 Bintang (Sesi 5 Dasar):** Mengunci logika evaluasi bintang murni berbasis **Progres Vitalitas Global** ($33.4\%, 66.7\%, 100\%$), aturan Stage 2 ($1\text{ desa} = 50\% = 1\text{ Bintang}$), syarat kelulusan minimal 1 Bintang, jeda timer saat narasi (*narrative time-freeze*), dan persistensi `PlayerPrefs`.
5. **Blueprint & Arsitektur Animasi Tubuh Naga (`DragonBodyAnimator`):** Menjawab kekakuan tubuh naga di luar sayap dengan merancang sistem prosedural untuk artikulasi rahang buka mulut `Bone_Jaw` (sinkronisasi `MOUTH_OPEN` CV), liukan ekor dinamis (*Tail Sway*), lengkungan punggung aerodinamis (*Spine Arch*), dan lipatan cakar kaki (*Flight Tuck vs Hover Drop*).

---

## 2. Keputusan Desain / Arsitektur / Teknis

1. **Penyematan Gambar Laporan & 5 Placeholder Laptop B:**
   - 12 Gambar Laptop A berhasil di-*embed*: Gbr 2.1 (COCO Keypoints), Gbr 3.1 (GDLC Flowchart), Gbr 3.2 (Dual-Port UDP Architecture), Gbr 3.4 (Peta Genshin), Gbr 3.5 (Rig X-Ray Naga), Gbr 3.6 (19 Pose Sheet), Gbr 3.7 (Lobby UI), Gbr 3.8 (Calibration UI), Gbr 3.9 (HUD UI), Gbr 4.2 (Render Kepulauan 3D), Gbr 4.5 (Quest Narrative UI), dan Gbr 4.6 (Village Challenge UI).
   - 5 Placeholder bertanda disiapkan untuk Laptop B: Gbr 3.3 (Webcam 3 titik wajah), Gbr 4.1 (Clean CV preview FPS only), Gbr 4.3 (Unity Scene View silinder desa), Gbr 4.4 (In-game billboard marker $<150\text{ m}$), dan Gbr 4.7 (Foto fisik tester).
2. **Dynamic Camera FOV & Speed Sense:**
   - Grounded / Auto-Hover: **`60°`** (Z offset `-6.0 m`).
   - Cruising Glide (`#08`): **`65°–68°`** (Z offset `-6.3 m`).
   - Tuck to Dive (`#13`): **`78°–80°`** (Z offset `-7.2 m` + getaran kamera mikro).
   - Propeller Dash (`#14`): **`85°`** (Z offset `-7.5 m` dorongan impulsif).
   - Damping: `Mathf.SmoothDamp` ($0.30\text{s}$) untuk kenyamanan mata dan bebas pusing (*motion sickness*).
3. **Skybox Dinamis & URP Horizon Fog Blending 3 Biome:**
   - *Area Netral / Laut:* Langit biru cerah fantasi bergaya Ghibli/Genshin, warna kabut URP menyatu 100% dengan batas horizon untuk menghilangkan garis potong laut.
   - *Radius Krisis Biome ($<250\text{ m}$):* Zephyria (kabut polusi kelabu-hijau $150\text{ m}$), Pyrewood (kabut asap hitam & bara api membara), Solaria (terik menyengat gersang kekuningan & kabut debu panas).
   - *Transisi Restorasi ($100\%$ Pulih):* Langit secara dramatis terbuka dan cerah kembali dalam $2.5–3.0\text{ detik}$ (toska segar, hijau hutan sejuk, atau pilar keemasan).
4. **Sistem Bintang Berbasis Progres Global Vitalitas (Sesi 5):**
   - ⭐ **1 Bintang:** $\ge 33.4\%$ Progres Global (Syarat kelulusan membuka stage berikutnya).
   - ⭐⭐ **2 Bintang:** $\ge 66.7\%$ Progres Global.
   - ⭐⭐⭐ **3 Bintang:** $100.0\%$ Progres Global (Pemulihan Sempurna).
   - Aturan Stage 2 ($N=2$, $50\%$ per desa): 1 desa pulih = $50\% \rightarrow$ tetap **1 Bintang** karena belum mencapai $66.7\%$. Begitu desa kedua terisi minimal $+16.7\%$ ($66.7\%$), naik ke **2 Bintang**.
   - Timer Countdown: Stage 1 ($120\text{s}$), Stage 2 ($240\text{s}$), Stage 3 ($360\text{s}$), Free Play ($\infty$). Timer **PAUSE** saat narasi 3 baris aktif.
   - Penanganan Waktu Habis (`00:00`): Pemain tetap dinilai bintangnya dari progres akhir; jika $\ge 33.4\%$ tetap dinyatakan Lulus dan membuka level selanjutnya.
5. **Animasi Tubuh Prosedural Naga (`DragonBodyAnimator.cs`):**
   - *Rahang Bawah (`Bone_Jaw`):* Rotasi `0°..-30°` mengikuti sinyal `MOUTH_OPEN` ($>0.40$ buka penuh) saat Water Breath `#15B`, auto-open `-25°` saat Victory Roar.
   - *Ekor & Punggung (`Spine & Tail Sway`):* Secondary motion berjenjang ($15\% \rightarrow 100\%$) saat belok kemudi, osilasi santai $0.6\text{ Hz}$ amplitudo $6^\circ$ saat cruising glide, serta Spine cembung ke bawah saat Tuck Dive dan melenting ke atas saat Fly Up.
   - *Lipatan Kaki Aerodinamis:* Flight Tuck merapat ke bawah tubuh saat kecepatan $\ge 12\text{ m/s}$, Hover Drop menjuntai rileks saat kecepatan $< 6\text{ m/s}$ atau Auto-Hover.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset

1. **Berkas Dokumen Laporan & Staging Gambar:**
   - Dibuat: [`docs/Laporan_Capstone_Fanamir.docx`](file:///e:/Semester%205/Game/AR%20Dragon/docs/Laporan_Capstone_Fanamir.docx) (4.59 MB, 12 gambar tersemat rapi).
   - Dibuat folder penampung staging: `docs/figures/raw_screenshots/`.
   - Dibuat: `brain/laptop-a/handoff-laptop-b/panduan_screenshot_mandiri_dan_laporan.md`.
2. **Berkas Paket Visual & Handoff Laptop B (Sesi 4B):**
   - Diperbarui: `brain/laptop-a/visual-vfx/planning_vfx_fov_skybox_dan_animasi.md`.
   - Dibuat: `brain/laptop-a/visual-vfx/prompt_eksekusi_vfx_skybox_dan_fov_laptop_b.md`.
3. **Berkas Game Loop, Scoring, & Handoff Laptop B (Sesi 5):**
   - Diperbarui: `brain/laptop-a/gameplay-mechanics/mekanisme_tantangan_desa.md`.
   - Dibuat: `brain/laptop-a/gameplay-mechanics/arsitektur_sesi_5_gameloop_dan_scoring.md`.
   - Dibuat: `brain/laptop-a/gameplay-mechanics/prompt_eksekusi_sesi_5_gameloop_dan_scoring_laptop_b.md`.
4. **Berkas Animasi Tubuh Naga & Handoff Laptop B:**
   - Dibuat: `brain/laptop-a/visual-vfx/arsitektur_animasi_naga_fanamir.md`.
   - Dibuat: `brain/laptop-a/visual-vfx/prompt_eksekusi_animasi_naga_laptop_b.md`.
5. **Sinkronisasi Pelacakan & Peta Pengetahuan:**
   - Diperbarui: `brain/00 - Overview.md`, `brain/laptop-a/Tasks.md`, dan `brain/laptop-a/Devlog.md`.
   - Seluruh perubahan di-commit dan di-push ke remote Git `main`.

---

## 4. Detail Penting yang Harus Diingat

* **Batas FOV Maksimal:** Tepat **`85°`** saat Propeller Dash (`#14`), dengan redaman `Mathf.SmoothDamp` $0.30\text{s}$ dan tarikan kamera ke `-7.5 m`.
* **7 Berkas Partikel Effekseer Kanonik:** `VFX_Zephyria_WindGust`, `VFX_Lake_WaterSiphon`, `VFX_Pyrewood_WaterBreath`, `VFX_Pyrewood_ExtinguishSteam`, `VFX_Solaria_CelestialDewRain`, `VFX_Flight_WingtipTrails`, dan `VFX_Flight_SonicDashShockwave`.
* **Threshold Bintang Global Vitality:**
  * $< 33.4\% \rightarrow 0$ Bintang
  * $\ge 33.4\% \rightarrow 1$ Bintang (Minimal untuk unlock level berikutnya)
  * $\ge 66.7\% \rightarrow 2$ Bintang
  * $= 100.0\% \rightarrow 3$ Bintang
* **Pengecualian Stage 2:** 1 desa selesai bernilai $50\%$ progres global, sehingga **tetap 1 Bintang** karena belum mencapai $66.7\%$.
* **Urutan Buka Level di PlayerPrefs:** `Stage 1 -> Stage 2 -> Stage 3 -> Free Play Mode` (terbuka otomatis saat meraih $\ge 1$ bintang).
* **Execution Order C# DragonBodyAnimator:** `[DefaultExecutionOrder(55)]` agar berjalan tepat setelah `DragonFlightController` (0) dan `DragonWingAnimator` (50).

---

## 5. Tugas Tersisa & Langkah Selanjutnya

1. **Laptop B — Eksekusi Animasi Tubuh Prosedural (`DragonBodyAnimator.cs`):**
   - Menjalankan prompt dari `prompt_eksekusi_animasi_naga_laptop_b.md` untuk mengimplementasikan artikulasi rahang `Bone_Jaw`, *Tail Sway*, dan *Flight Leg Tucking*.
2. **Laptop B — Sesi 5 Lanjutan (`AudioManager` & Build Standalone):**
   - Pemasangan BGM/SFX terpusat di `AudioManager.cs` dan pembuatan build Windows standalone (`.exe` + `start_game.bat`).
3. **Laptop B — Dokumentasi Tangkapan Layar untuk Laporan:**
   - Memindahkan / mengganti 5 placeholder gambar di `docs/Laporan_Capstone_Fanamir.docx` dengan tangkapan layar live dari folder `docs/figures/raw_screenshots/`.

---
*Navigasi Vault:* [[00 - Overview]] | [[Tasks]] | [[Devlog]] | [[visual-vfx/planning_vfx_fov_skybox_dan_animasi]] | [[gameplay-mechanics/arsitektur_sesi_5_gameloop_dan_scoring]] | [[visual-vfx/arsitektur_animasi_naga_fanamir]]
