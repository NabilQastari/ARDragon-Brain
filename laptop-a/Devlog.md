---
title: Laptop A — Devlog (Desain, Ide, & Arsitektur)
tags:
  - devlog
  - laptop-a
owner: Laptop A
updated: 2026-11-19
---

# 📝 Devlog — Laptop A

> Catatan kronologis keputusan desain, ide, arsitektur, dan pembaruan dokumentasi dari **Laptop A**. Kembali ke [[00 - Overview]].

---

## 2026-11-22 — Finalisasi Arsitektur Animasi Tubuh Naga Fanamir & Master Prompt Laptop B ([[visual-vfx/arsitektur_animasi_naga_fanamir]])
- **Keputusan Desain Animasi Prosedural (`DragonBodyAnimator.cs`):**
  - Mengunci arsitektur animasi tubuh modular untuk menggerakkan 35 tulang pada rig `ARM_Dragon` (47 tulang):
    1. **Artikulasi Rahang Buka Mulut (`Bone_Jaw`):** Rotasi lokal `0°` (tertutup) hingga `-30°` (terbuka lebar) terhubung langsung ke telemetri `MOUTH_OPEN` CV saat Water Breath `#15B`, dengan auto-trigger `-25°` saat selebrasi kemenangan desa (*Victory Roar*).
    2. **Liukan Ekor Dinamis & Punggung (`Spine & Tail Sway`):** Distribusi lenturan ekor berjenjang (*wave lag* $15\%$ di `Tail_01` hingga $100\%$ di `Tail_05`) saat belok, osilasi santai $0.6\text{ Hz}$ saat meluncur (*cruising*), dan lengkungan aerodinamis cembung ke bawah saat *Tuck Dive* (`#13`) serta melenting ke atas saat *Fly Up* (`#07`).
    3. **Lipatan Kaki & Cakar Aerodinamis:** Kaki depan dan belakang melipat rapat ke bawah tubuh (*Flight Tuck*) saat meluncur cepat ($\ge 12\text{ m/s}$), dan menjuntai turun rileks (*Hover Drop*) saat auto-hover di desa atau kecepatan $< 6\text{ m/s}$.
  - **Pendekatan Teknis:** Menggunakan skrip prosedural Unity `DragonBodyAnimator.cs` (`[DefaultExecutionOrder(55)]`) agar responsif seketika terhadap input kemudi dan CV tanpa delay/clipping animasi baking.
- **Penerbitan Master Prompt Eksekusi:** Menyusun `brain/laptop-a/visual-vfx/prompt_eksekusi_animasi_naga_laptop_b.md` siap salin bagi Laptop B.
- **Pembaruan Dokumen:** Memperbarui [[visual-vfx/arsitektur_animasi_naga_fanamir]], [[visual-vfx/prompt_eksekusi_animasi_naga_laptop_b]], [[00 - Overview]], [[Tasks]], dan [[Devlog]].

---

## 2026-11-21 — Restrukturisasi & Penataan Folder Kategori Brain Laptop A
- **Aksi Penataan Ruang Kerja:**
  - Melakukan penataan menyeluruh terhadap 19 file dokumentasi lepas yang sebelumnya bertumpuk di root `brain/laptop-a/`.
  - Membuat 5 subfolder kategori modular berbasis domain pengembangan:
    1. `ui-ux/`: Berkas perencanaan antarmuka, aset Unity 6, dan kontrak integrasi UI (`rencana_desain_ui.md`, `panduan_aset_ui_unity.md`, `kontrak_integrasi_ui_dan_progress.md`).
    2. `gameplay-mechanics/`: Berkas rancangan gameplay tantangan 3 desa, fisika terbang & momentum, game loop, sistem scoring, kamera dinamis, serta prompt eksekusi sesi 5 (`mekanisme_tantangan_desa.md`, `planning_momentum_dan_revisi_kontrol.md`, `arsitektur_sesi_5_gameloop_dan_scoring.md`, `prompt_eksekusi_sesi_5_gameloop_dan_scoring_laptop_b.md`, `planning_kamera_dinamis_dan_navigasi_pause.md`).
    3. `cv-control/`: Berkas panduan 17 pose kontrol, protokol audit pose berbasis screenshot, revisi 3 titik wajah, dan generator prompt visual pose CV (`panduan_detail_pose_dan_sync_laptop_b.md`, `planning_audit_pose_dan_clean_cv_preview.md`, `planning_revisi_titik_wajah_dan_pov.md`, `prompt_visual_pose_cv_kerangka.md`).
    4. `visual-vfx/`: Berkas perencanaan skill VFX Effekseer, dynamic FOV, skybox 3 biome adaptif, blueprint animasi naga, dan master prompt eksekusi visual (`rencana_skill_vfx_effekseer_laptop_b.md`, `planning_vfx_fov_skybox_dan_animasi.md`, `prompt_eksekusi_vfx_skybox_dan_fov_laptop_b.md`).
    5. `handoff-laptop-b/`: Berkas perencanaan patch, checklist pra-sesi 4, cetak biru optimasi token (*The Optimal Lean Stack*), dan SOP screenshot mandiri untuk Laptop B (`laptop B Patch 1 planning.md`, `checklist_finalisasi_pra_sesi_4.md`, `planning_optimasi_token_laptop_b.md`, `panduan_screenshot_mandiri_dan_laporan.md`).
  - **Kepatuhan Arsitektur Anti-Collision & AGENTS.md:**
    - Mempertahankan dokumen inti pelacakan (`Tasks.md`, `Devlog.md`, `konteks.md`, `prd.md`) dan folder catatan memori (`memories/`) tetap berada di root `brain/laptop-a/` agar simetris dan konsisten dengan `brain/laptop-b/` serta mematuhi aturan penulisan di `AGENTS.md`.
    - Memperbarui peta navigasi visual pada `brain/00 - Overview.md`.

---

## 2026-11-21 — Finalisasi Desain Visual & Flight Feel: Dynamic FOV (85°), Dynamic Skybox 3 Biome, & Katalog 7 VFX Effekseer ([[planning_vfx_fov_skybox_dan_animasi]])
- **Keputusan Desain Disetujui Pengguna:**
  - **Dynamic Camera FOV:** Ditetapkan rentang FOV `60°` (normal/hover), `65°–68°` (glide cruising), `78°–80°` (tuck dive), dan batas atas **`85°`** saat melesat kencang (*Propeller Dash*) dengan `Mathf.SmoothDamp` ($0.30\text{s}$) dan tarikan mundur kamera (`-6.0m` ke `-7.5m`).
  - **Skybox & Atmosfer Dinamis 3 Biome (`BiomeAtmosphereController.cs`):** 
    - Wilayah netral laut lepas mengusung langit cerah fantasi bergaya Ghibli/Genshin dengan awan kumulus bertingkat + penyatuan kabut cakrawala URP (*Horizon Fog Blending*).
    - Memasuki radius desa ($<250\text{m}$), langit dan kabut URP bertransisi dinamis mengikuti status krisis biome: Zephyria (*toxic smog* kelabu-hijau), Pyrewood (kabut asap hitam & bara api membara), Solaria (terik menyengat gersang kekuningan & kabut debu panas).
    - Tepat saat tantangan 100% selesai (*Restored*), atmosfer secara dramatis dibersihkan dalam transisi $2.5–3\text{ detik}$ menjadi langit cerah toska/hutan/keemasan yang segar dan hidup.
  - **Katalog 7 Efek Partikel Effekseer:** Membakukan 7 berkas efek partikel `.efkefc`: (1) `VFX_Zephyria_WindGust`, (2) `VFX_Lake_WaterSiphon`, (3) `VFX_Pyrewood_WaterBreath`, (4) `VFX_Pyrewood_ExtinguishSteam`, (5) `VFX_Solaria_CelestialDewRain`, (6) `VFX_Flight_WingtipTrails`, dan (7) `VFX_Flight_SonicDashShockwave`.
- **Penerbitan Master Prompt Eksekusi untuk Laptop B ([[prompt_eksekusi_vfx_skybox_dan_fov_laptop_b]]):**
  - Menyusun dokumen master prompt siap pakai yang berisi 5 tahapan instruksi lengkap: (1) instalasi skill agen + package Unity URP Effekseer, (2) pembuatan 7 partikel `.efkefc`, (3) implementasi `DynamicFlightCameraController.cs`, (4) implementasi `BiomeAtmosphereController.cs`, dan (5) penyambungan emitter ke `VillageZoneController.cs` serta tangkapan layar mandiri ke `docs/figures/raw_screenshots/`.
- **Perancangan Arsitektur Sesi 5: Game Loop, Timer Countdown, & Penilaian 3 Bintang ([[arsitektur_sesi_5_gameloop_dan_scoring]]):**
  - Mengunci aturan sistem bintang universal berbasis Progres Global Vitalitas: $33.4\%$ (1 Bintang), $66.7\%$ (2 Bintang), dan $100\%$ (3 Bintang).
  - Menetapkan aturan adil Stage 2 di mana 1 desa pulih ($50\%$) bernilai 1 Bintang karena belum mencapai $66.7\%$.
  - Mengunci syarat kelulusan membuka stage berikutnya cukup dengan **minimal 1 Bintang** ($\ge 33.4\%$).
  - Menetapkan timer countdown (Stage 1: 120s, Stage 2: 240s, Stage 3: 360s, Free Play: $\infty$) yang otomatis **PAUSE** saat narasi auto-play 3 baris aktif.
  - Merumuskan arsitektur `GameManager.cs` (Lifecycle State Machine, evaluasi persisten `PlayerPrefs`, dan integrasi `PF_FinishModal` `SCREEN 04`).
- **Penerbitan Master Prompt Eksekusi Sesi 5 Dasar untuk Laptop B ([[prompt_eksekusi_sesi_5_gameloop_dan_scoring_laptop_b]]):**
  - Menyusun dokumen master prompt siap pakai bagi Laptop B untuk membakukan penilaian bintang progres global, transisi countdown 3s awal, TimeUp evaluation, dan progresi gembok level berjenjang.
- **Pembaruan Dokumen:** Memperbarui [[planning_vfx_fov_skybox_dan_animasi]], [[prompt_eksekusi_vfx_skybox_dan_fov_laptop_b]], [[mekanisme_tantangan_desa]], [[arsitektur_sesi_5_gameloop_dan_scoring]], [[prompt_eksekusi_sesi_5_gameloop_dan_scoring_laptop_b]], [[00 - Overview]], [[Tasks]], dan [[Devlog]].

---

## 2026-11-19 — Arsip Memori Sesi: Mekanisme Momentum, Pembaruan Pose v4, & Paket Delta Ekspor ([[Mekanisme Momentum dan Pembaruan Pose v4]], [[planning_momentum_dan_revisi_kontrol]])
- **Ringkasan Memori Sesi:** Mengarsipkan rangkaian keputusan perancangan sistem momentum kontinu ($0–100\%$) naga Fanamir, aturan inersia dan pengereman perlahan ($\sim 2.2–2.5\text{s}$), soft-cap laju penambahan bertahap per pose, pemisahan `#07 Fly Up` sebagai *Y-Glide Up* (sayap 3D Y-dihedral, gentle stall) vs `#06B Steep Climb` (chin-up flap $+7\text{ m/s}$), pemisahan `#09A Hover` (flutter lengan bawah ke $0\text{ m/s}$) vs `#09B Slow Glide` ($\sim 6–8\text{ m/s}$), perbaruan 15 medali siluet UI bersih (tanpa kerangka/titik CV) pada Kalibrasi `SCREEN 01` & Guide `SCREEN 07`, penambahan `Momentum Dock` $420 \times 40\text{ px}$ pada HUD `SCREEN 02`, penyusunan paket delta 28 file `assets/Export_Delta_LaptopB/` (`Export_Delta_LaptopB.zip`), penerbitan manual visual resmi `v4.0` 12 halaman (`Panduan_19_Pose_Fanamir_CV.pdf` & `.docx`), serta penyusunan master prompt sinkronisasi untuk Laptop B di [[planning_momentum_dan_revisi_kontrol]]. Catatan lengkap diarsipkan di [[Mekanisme Momentum dan Pembaruan Pose v4]].

---

## 2026-11-19 — Finalisasi Penuh Mekanisme Tantangan 3 Desa, Auto-Hover, World Markers, & Blueprint Sesi 4 ([[mekanisme_tantangan_desa]])
- **Keputusan Desain & Parameter Final Sesi 4 Disetujui Pengguna:**
  - **Bentuk Zona Silinder:** Menggunakan silinder vertikal (jarak XZ + threshold ketinggian Y) untuk mencegah aktivasi saat terbang tinggi: Silinder Narasi ($R = 35\text{ m}, Y \le 40\text{ m}$), Silinder Tantangan lebih lebar ($R = 48\text{–}50\text{ m}$).
  - **Auto-Hover & Anchor:** Naga ditarik halus (*lerp*) ke titik jangkar spesifik menghadap pusat bencana lingkungan; memutar animasi hover tanpa pemain perlu bergerak.
  - **Mekanik Escape & Anti-Konflik Zephyria:** Karena Desa Zephyria memakai gestur *Flap* untuk menyapu kabut, pemain escape dari tantangan Zephyria menggunakan **Tuck to Dive (`#13`)** atau **Propeller Dash (`#14`)**. Di zona lain, *Flap* / *Dive* / *Dash* berfungsi normal untuk escape.
  - **Cooldown Re-Trigger & Momentum:** Wajib keluar radius + cooldown **`3.5 detik`**. Momentum dibekukan saat auto-hover, dan saat escape / auto-release diberikan **dorongan maju (*launch thrust*, momentum ~30–35%)** agar tidak *stall*.
  - **Isolasi UI Narasi:** Saat narasi aktif, seluruh HUD lain disembunyikan sementara, menyisakan **HANYA** `PF_QuestNarrativeBox` (`SCREEN 08`) dan `QuestObjectivePanel` (`SCREEN 02`).
  - **Sistem No Decay:** Diterapkan seragam ke seluruh desa—progres pengisian bar tidak akan berkurang saat pemain beristirahat atau escape sementara.
  - **Balancing Target Waktu:** Zephyria Flap (~8–10 kepakan / ~8–10s), Danau Zephyria (isi air auto ~4–5s lalu Auto-Release), Pyrewood Hutan (Open Mouth `#15B` ~6–8s), Solaria Sky Altar (Double I-Pose `11` `#15C` ~7–9s).
  - **World-Space Billboard Markers (< 150m):** Ditambahkan marker melayang di udara di atas balai desa / sub-marker dengan nama lokasi dan indikator jarak (*Camera.main Billboard*).
  - **Kamera & Victory Trigger:** Kamera tetap memakai *Fixed Flight Cam* standar. Saat desa terakhir mencapai 100%, naga auto-hover selebrasi 2.5–3 detik diiringi fanfare kemenangan, lalu langsung memunculkan `PF_FinishModal` (Opsi A).
- **Pembaruan Dokumen:** Memperbarui [[mekanisme_tantangan_desa]], [[checklist_finalisasi_pra_sesi_4]], [[Tasks]], dan [[Devlog]].
- **Penerbitan Template & Draf Laporan Capstone Project (`.docx` & `.md`) dengan 12 Gambar Tersemat:**
  - Menerbitkan dokumen laporan resmi Capstone Project sesuai ketentuan Lampiran 6A ([`docs/Laporan_Capstone_Fanamir.docx`](file:///e:/Semester%205/Game/AR%20Dragon/docs/Laporan_Capstone_Fanamir.docx) dan [`.md`](file:///e:/Semester%205/Game/AR%20Dragon/docs/Laporan_Capstone_Fanamir.md)).
  - Dokumen mencakup format margin akademik (Kiri 4 cm, Atas 3 cm, Kanan 3 cm, Bawah 3 cm), jenis huruf Times New Roman (12 pt, spasi 1.5, justified), isi lengkap Bab I, Bab II, Bab III, dan Bab IV (bagian implementasi yang sudah selesai di Sesi 4).
  - **Penyematan 12 Gambar Laptop A:** Menjalankan script Python (`scratch/generate_capstone_docx.py`) yang berhasil menyematkan 12 gambar berkualitas tinggi yang ada di Laptop A (diagram topologi COCO, flowchart GDLC, arsitektur UDP ganda, denah kepulauan, preview rig naga X-Ray, sheet 19 pose CV, desain UI Main Menu, Kalibrasi, HUD, Quest Narrative, Village Challenge, dan render kepulauan 3D) ke dalam file `.docx` (ukuran file ~4.6 MB).
  - **5 Placeholder Khusus Laptop B:** Menyisakan kotak bertanda tegas untuk 5 gambar yang memerlukan eksekusi/lingkungan runtime Unity & webcam di Laptop B (Webcam 3 titik wajah, Clean CV Preview FPS Only, Unity Scene View Marker Desa, In-Game Billboard Marker $\le 150\text{ m}$, dan Foto Pengujian Tester).
- **Penerbitan Panduan Screenshot Mandiri untuk Laptop B ([[panduan_screenshot_mandiri_dan_laporan]]):**
  - Menyusun panduan teknis bagi Laptop B untuk melakukan *capture* mandiri (C# `ScreenCapture`, Python OpenCV `cv2.imwrite`, dan terminal `ImageGrab`).
  - Menetapkan folder *staging* resmi: `docs/figures/raw_screenshots/`.
  - Memetakan 5 placeholder gambar laporan ke nama file yang disarankan serta SOP pasca-sesi agar setiap perkembangan otomatis didokumentasikan visualnya.
- **Perencanaan Pasca Sesi 4: Dynamic FOV, Skybox Fantasi, & Blueprint Animasi Naga ([[planning_vfx_fov_skybox_dan_animasi]]):**
  - **Dynamic FOV & Speed Sense:** Merumuskan parameter adaptif kamera di mana FOV melebar dari `60°` (cruising) hingga `80°` saat *Tuck to Dive* dan `85°` saat *Propeller Dash* disertai tarikan mundur kamera (`-6.0m` ke `-7.5m`) untuk sensasi kecepatan terbang imersif.
  - **Skybox Fantasi & Horizon Fog:** Merancang atmosfer langit stylized (Ghibli/Genshin style) dengan gradasi biru-cyan hangat, awan kumulus bertingkat, dan penyelarasan warna kabut URP (*Horizon Fog Blending*) untuk menghilangkan batas kliping lautan.
  - **Blueprint Animasi Naga:** Mengidentifikasi kebutuhan artikulasi tulang lanjutan di luar sayap (liukan ekor *tail sway*, lengkungan tulang belakang, rotasi rahang bawah saat buka mulut semburan air `#15B`, dan lipatan cakar kaki) dengan keputusan bahwa eksekusi teknis animasi akan dialokasikan ke sesi tersendiri agar fokus tidak tumpang-tindih dengan VFX.
- **Addendum Sesi 4 (Fokus Murni Mekanisme & Buka Semua Level):**
  - **Sistem Bintang Tetap di Sesi 5:** Sesuai arahan pengguna, sistem evaluasi bintang 1–3, countdown batas waktu ketat, dan `GameManager.cs` tetap berada di Sesi 5 (tidak dimajukan ke Sesi 4) agar ruang lingkup Sesi 4 terisolasi bersih pada mekanisme inti interaksi 3 desa.
  - **Pembukaan Semua Level Sementara:** Di Sesi 4, cukup buka penuh seluruh kartu level (**Stage 01, 02, 03**) di Lobby `SCREEN 00` (`DragonGameSettings.UnlockAllStagesForTesting = true`, nonaktifkan scrim gembok, tombol hijau `PLAY` aktif) agar ketiga desa dapat langsung diuji secara bebas.

---

## 2026-11-18 — Perancangan Sistem Momentum Terpadu, Pembaruan UI HUD & Guide, & Buku Panduan `v4.0` (`19` Pose CV) ([[planning_momentum_dan_revisi_kontrol]])
- **Diskusi & Keputusan Desain Mekanisme (`[[planning_momentum_dan_revisi_kontrol]]`):**
  - Memperkenalkan reservoir kontinu **`Momentum` (`0–100%`)** berbasis hukum inersia tunggal: selama masih ada momentum, Fanamir terus melaju maju dan pengereman hanya melambat bertahap (`~2.2–2.5 detik`), tidak berhenti mendadak `0 m/s`.
  - **Penambahan Berangsur & Limit *Soft-Cap*:** Pose biasa mengisi bertahap dengan batas limit (`#04` `15%`, `#06B` `20%`, `#06` `30%`, `#11` `55%`) tanpa memangkas momentum lebih tinggi; hanya **`#13 Tuck Dive` (`+16%/s`)** dan **`#14 Propeller Dash` (`+7%/putaran`)** yang bisa mencapai **`100%`** bila dilakukan berulang/berkali-kali.
  - **Pemisahan 3 Mode Menanjak & Sayap 3D:** `#06 Flap` (dagu netral = naik landai), **`#06B Steep Climb`** (angkat sedikit dagu + kepak = naik curam tanpa momentum), dan **`#07 Fly Up (Y-Pose)`** (`Y-Glide Up` wajib momentum `>0%`, sayap 3D membentuk huruf Y, *gentle stall* saat habis; terpisah dari `HEAD_PITCH` tunggal).
  - **Pemisahan `#09A` vs `#09B`:** L-Pose `90°` + **kepak lengan bawah** = `#09A Hover Brake` menuju `0 m/s`, sedangkan L-Pose **diam** = `#09B Slow Glide` (`~6–8 m/s`). Ditambahkan *assisted braking* lembut saat Auto-Hover radius desa `R = 35m`.
- **Pembaruan FigCraft (`UI AR Dragon.figcraft`, `2,868,089` B) & Ekspor Aset:**
  - **Page `Pose` (`assets/poses_cv_vector/`):** Memperbarui `Pose_09_Hover.png` dengan kepak lengan bawah (*flutter*), menambahkan `Pose_06B_SteepClimb_ChinUpFlap.png` & `Pose_09B_SlowGlide_LPose.png`, serta mengekspor lembar lengkap **`All_19_Poses_Sheet.png`**.
  - **UI `SCREEN 01`, `02`, `07`, & `03 - Unity Export Kit` (`assets/UI_Unity_Export/`):** Menambahkan **`Momentum Dock`** (`420×40` bar + badge `Y-OK`) di `SCREEN 02` beserta komponen modular `05_HUD/UI_HUD_Momentum_Track_206x20.png` & `UI_HUD_Momentum_Fill_200x14.png`, memperbarui medali bersih `06_Poses/UI_Pose_09_HoverMode.png` (L-pose + kepak tanpa titik/kerangka CV), serta menyelaraskan deskripsi `#06, #07, #08, #09, #13, #14` pada `01_Calibration_MotionTutorial.png`, `02_InGame_FlightHUD.png`, & `07_PoseGuide_OverlayModal.png`.
- **Pembuatan Paket Delta Ekspor Laptop B (`assets/Export_Delta_LaptopB/` & `Export_Delta_LaptopB.zip`):** Membandingkan secara byte-level SHA-256 terhadap `UI_Unity_Exportv2.zip` (`149` entri) dan `assets/poses_cv_vector.zip` (`21` entri), lalu mengemas **`28` file baru/terbarui** (`15` medali bersih `UI_Pose_01..15.png`, `2` komponen `UI_HUD_Momentum_*`, `5` desain referensi `1920×1080`, `4` vektor CV baru/update, serta `Panduan_19_Pose_Fanamir_CV.pdf`/`.docx`) dilengkapi `MANIFEST_DELTA.md` dan `Export_Delta_LaptopB.zip` (`6,250,581` B).
- **Penerbitan Manual Visual Baru (`12` Halaman, `v4.0`):** Menghasilkan [`Panduan_19_Pose_Fanamir_CV.pdf`](file:///e:/Semester%205/Game/AR%20Dragon/Panduan_19_Pose_Fanamir_CV.pdf) (`956,918` B) & [`.docx`](file:///e:/Semester%205/Game/AR%20Dragon/Panduan_19_Pose_Fanamir_CV.docx) (`2,598,036` B) di root dan [`docs/`](file:///e:/Semester%205/Game/AR%20Dragon/docs/), plus prompt sinkronisasi siap salin untuk Laptop B di [[planning_momentum_dan_revisi_kontrol]].

## 2026-11-18 — Arsip Memori Sesi: Revisi Kontrol Wajah, Clean CV Preview, & Checklist Pra-Sesi 4 ([[Revisi Kontrol Wajah dan Pra Sesi 4]])
- **Ringkasan Sesi:** Mengarsipkan seluruh rangkaian keputusan desain kontrol wajah 3 titik (Hidung, Dagu, Mulut) & decoupling `HEAD_PITCH` dari dada/kemudi horizontal ([[planning_revisi_titik_wajah_dan_pov]]), aturan pembersihan teks preview OpenCV/PIP kecuali `FPS` ([[planning_audit_pose_dan_clean_cv_preview]]), penyusunan 5 Pilar prasyarat menuju tantangan desa ([[checklist_finalisasi_pra_sesi_4]]), serta sinkronisasi status terbaru bahwa **Pilar 1 (Clean CV Preview + `DragonHeadLookController` & `HEAD_PITCH` Unity) dan Pilar 2 (Penyelarasan 17 Pose + Upgrade `#14 Propeller Dash`)** telah selesai dieksekusi di Laptop B (`9/9` suite Python & Unity Play Mode `0` errors). Menetapkan fokus sesi berikutnya untuk langsung berdiskusi dan memfinalisasi rancangan **Tantangan Desa (Sesi 4)** bersama **Pilar 3–5**. Catatan memori lengkap disimpan di [[Revisi Kontrol Wajah dan Pra Sesi 4]].

## 2026-11-18 — Penerbitan Buku Panduan Pose Baru Edisi Vector Stickman + CV Detection (`Panduan_17_Pose_Fanamir_CV.docx` & `.pdf`, `v3.0`)
- **Pembuatan Manual Visual Baru (`11` Halaman, `2,521,358` B DOCX / `916,576` B PDF):**
  - Membangun skrip generator baru (`generate_cv_pose_manual.py`) untuk menerbitkan [`Panduan_17_Pose_Fanamir_CV.docx`](file:///e:/Semester%205/Game/AR%20Dragon/Panduan_17_Pose_Fanamir_CV.docx) & [`Panduan_17_Pose_Fanamir_CV.pdf`](file:///e:/Semester%205/Game/AR%20Dragon/Panduan_17_Pose_Fanamir_CV.pdf) (beserta salinan di [`docs/`](file:///e:/Semester%205/Game/AR%20Dragon/docs/)).
  - Memasangkan langsung seluruh **`17` gambar kartu vektor Stickman + CV Keypoints** (`assets/poses_cv_vector/Pose_01_GroundedIdle.png` s.d. `Pose_15C_Solaria_DewRain.png`) pada kolom visual kartu pose 2-kolom anti-pecah (*`w:cantSplit`*), serta menyertakan **Quick Reference Sheet** [`All_17_Poses_Sheet.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/poses_cv_vector/All_17_Poses_Sheet.png) di Halaman 3.
  - Seluruh isi disesuaikan dengan pembaruan skema pose final: legenda warna sendi YOLOv8n-pose (Oranye kanan, Cyan kiri, 5 titik wajah + panah/lengkungan motion), kemudi searah `#05`, pemisahan tegas `#07 Y-Pose` vs `#15C Solaria Double-I (11)`, `#09 Hover (90° L-Pose)`, `#10 Gentle Descent (Low-A ~45°)`, `#14 Propeller Dash` (lengan atas melebar horizontal `180°` + lengan bawah berputar di siku), aksi 3 desa `#15A/B/C`, 3 combo manuver, dan standar setup webcam.
  - Seluruh 11 halaman PDF diverifikasi secara visual via `pypdf` dan inspeksi gambar halaman per halaman tanpa kartu terpotong atau header tabel yatim.

## 2026-11-18 — Penyelarasan Ikon & Deskripsi Pose UI Calibration + Pose Guide (`SCREEN 01` & `SCREEN 07`, Tanpa CV Skeleton)
- **Konteks Permintaan Pengguna:** Menyelaraskan ikon siluet *stickman* pada bagian *guide* menu Kalibrasi (`SCREEN 01`) dan modal `Pose Guide · All 15 Poses` (`SCREEN 07`) di [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) agar sesuai bentuk pose final terbaru (`#01`–`#15`), **tetap bergaya siluet bersih UI tanpa kerangka kawat maupun titik CV**.
- **Pembaruan di FigCraft & Ekspor (`scale: 1` PNG):**
  - Memperbarui ke-`15` komponen modular medali `06_Poses/UI_Pose_01..15` (`1687..1701`, `64×64` px) pada page `03 - Unity Export Kit` serta menyinkronkan seluruh kartu pose di artboard **`SCREEN 01 - Calibration`** (`id: 314`, 4 kartu Recent Poses `#08, #06, #04, #13`) dan **`SCREEN 07 - Pose Guide Overlay Modal`** (`id: 1893`, seluruh 15 kartu):
    - `#02 Grounded Walk` & `#08 Glide`: kaki lurus sejajar `||` dan lengan T-pose horizontal.
    - `#03 Grounded Look Up`: kaki sejajar + kepala mendongak + panah atas `↑` (`Look up / tilt head skyward (arms down at sides)...`).
    - `#04 Takeoff` & `#06 Flap to Climb`: lengkung kepak sayap/lengan + tanda gerak dinamis simetris.
    - `#05 Lean & Arm Tilt`: arah kemiringan torso dan kemiringan lengan searah + kaki lurus sejajar.
    - `#07 Fly Up`: bentangan lengan Y lebar di atas kepala + panah atas `↑`.
    - `#09 Hover Mode`: lengan atas menempel, siku tekuk `90°` (**L-pose**) + busur melayang di bawah kaki.
    - `#10 Gentle Descent`: mengganti *Tucked T-Pose* menjadi kedua lengan lurus serong bawah `~45°` (**Low-A pose**) + panah bawah `↓`.
    - `#11 Fly Down (Pose Y)`, `#12 Fly Down Idle (Pose W)`, & `#13 Tuck to Dive`: pose `Y`-dive, `W`-pose, dan lipatan lengan rapat di dada atas (`Fold forearms tightly across upper chest...`).
    - `#14 Propeller Dash`: lengan atas melebar horizontal `180°` + lengan bawah vertikal + lengkung rotasi melingkar di kedua siku (`Spread upper arms wide horizontally & spin forearms in circles at elbows...`).
    - `#15 Village Actions`: pose ganda `11` (`Solaria`) + gelombang angin ke bawah (`Zephyria`) dan pembaruan teks ringkas `#15A/B/C`.
  - Menjalankan verifikasi screenshot `SCREEN 01`, `SCREEN 07`, dan ke-15 ikon modular, menyimpan [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) (`2,725,286` B), serta mengekspor ulang ke-15 PNG ke [`assets/UI_Unity_Export/Components/06_Poses/`](file:///e:/Semester%205/Game/AR%20Dragon/assets/UI_Unity_Export/Components/06_Poses/) dan 2 referensi `1920×1080` (`01_Calibration_MotionTutorial.png` & `07_PoseGuide_OverlayModal.png`) ke [`assets/UI_Unity_Export/Designs/`](file:///e:/Semester%205/Game/AR%20Dragon/assets/UI_Unity_Export/Designs/).
- **Pembaruan Dokumentasi & Sinkronisasi Laptop B:** Memperbarui bagian UI pada [[panduan_detail_pose_dan_sync_laptop_b]] agar Laptop B mengimpor ke-15 ikon medali bersih `06_Poses/UI_Pose_01..15.png` (bukan menimpa medali dengan kartu kerangka CV `poses_cv_vector/`), serta mencatat progres di [[Tasks]] dan [[Devlog]].

## 2026-11-18 — Arsip Memori Sesi: Pembaruan UI `Fanamir` (`v1.7` & `v1.8` Boot Flow) ([[Pembaruan UI Fanamir v1.7 dan v1.8]])
- **Ringkasan Sesi:** Mengarsipkan seluruh keputusan desain dan eksekusi UI sesi ini: pengubahan branding publik ke **`Fanamir: The Sky Guardian`**, penambahan **Quest Objective Panel (`SCREEN 02`)**, **Quest Narrative Box Auto-Play *hands-free* (`SCREEN 08`)**, slider ke-5 **`Text Auto-Next` (`SCREEN 05`)**, ekspor `140` PNG komponen + `9` desain `1920×1080` (`v1.7`), serta perancangan **Splash Screen (`SCREEN 09`)** dan **Start Screen (`SCREEN 10` · `PRESS ANY BUTTON TO START`)** menggunakan komponen eksisting (`11` desain referensi total `v1.8`, prompt §5 & §6 di [[kontrak_integrasi_ui_dan_progress]]). Catatan lengkap disimpan di [[Pembaruan UI Fanamir v1.7 dan v1.8]].

## 2026-11-18 — Arsip Memori Sesi: Perencanaan Optimasi Token Laptop B ([[Perencanaan Optimasi Token Laptop B]])
- **Ringkasan Sesi:** Melakukan analisis 6 kandidat tool optimasi token (RTK, Ponytail Antigravity IDE, Token-Saver Skill, Ponytail Original, Native Antigravity Skills, dan Rules/.antigravityignore) khusus untuk lingkungan Laptop B (Unity 6 C# dan Python CV). Merumuskan arsitektur *The Optimal Lean Stack* (kombinasi `.antigravityignore` masif, kompresor terminal CLI `rtk`, aturan minimal coding Ponytail-Lite di `AGENTS.md`, isolasi skill Blender, dan hygiene `/compact`). Menyusun berkas panduan [[planning_optimasi_token_laptop_b]] dan prompt eksekusi siap pakai untuk Laptop B. Catatan lengkap disimpan di [[Perencanaan Optimasi Token Laptop B]].

## 2026-11-18 — Panduan Setup Lengkap & Prompt Onboarding Brain Antigravity + Obsidian ([[Panduan Setup Brain Antigravity Obsidian]])
- **Penyusunan Master Guide & Prompt Wizard:**
  - Menyusun panduan menyeluruh berbasis arsitektur nyata project AR Dragon / Fanamir ke dalam [`prompt-setup-brain.md`](file:///e:/Semester%205/Game/AR%20Dragon/prompt-setup-brain.md).
  - Merancang tag `<SYSTEM_ONBOARDING_INSTRUCTION>` agar ketika pengguna mem-paste dokumen ke chat Antigravity di project baru mana pun, AI langsung bertindak sebagai Onboarding Wizard interaktif langkah-demi-langkah.
  - Mendokumentasikan 5 pilar sistem Brain: Git sub-repo mandiri terisolasi, arsitektur anti-collision multi-device (0% merge conflict), graf visual Obsidian, otomasi SOP `AGENTS.md`, dan standarisasi protokol memori 5-bagian (`"jadikan sesi ini memori"`).
  - Mengonfirmasi universalitas arsitektur untuk proyek game, web/mobile SaaS, AI/ML research, skripsi/tesis, maupun solo developer.
  - Catatan memori sesi lengkap diarsipkan di [[Panduan Setup Brain Antigravity Obsidian]].

## 2026-11-18 — Arsip Memori Sesi: Model Kepulauan 3D, Rumah (`5` Varian), & Pohon (`12` Varian, `3.0–10.0 m`) ([[Model Kepulauan 3D Rumah dan Pohon]])
- **Ringkasan Sesi (Eksekusi 2026-11-14 s/d 2026-11-15):** Merangkum seluruh iterasi pembangunan dan penyempurnaan [`Pulau_Archipelago.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/Pulau_Archipelago.blend) (`2,077,219` B, skala `1:6`, `352` objek) — mulai dari peninggian dataran tengah gunung Central (`27.50 m` / puncak `37.50 m`), penyederhanaan Biome 1 ke `1` danau bertepian tanah, penyambungan 4 daratan di level rendah bawah air dalam 1 mesh *watertight* `SM_Archipelago_Terrain` (`48,376` tris) bertransisi rumput-ke-tanah, ekspansi `5` varian rumah (`27` unit) & `12` varian pohon dengan variasi tinggi bertingkat `3.0–10.0 m` (`316` unit: pohon sehat, kering *Drought*, sebagian gosong & hangus tanpa daun *Wildfire*), penataan ulang area *Wildfire* & *Drought*, hingga penghapusan model naga statis di gunung Central (`qa_check.py` & `scene_audit.py` lulus). Catatan lengkap disimpan di [[Model Kepulauan 3D Rumah dan Pohon]].

## 2026-11-18 — Penyusunan Panduan Detail 17 Pose & Prompt Sinkronisasi Laptop B ([[panduan_detail_pose_dan_sync_laptop_b]])
- **Dokumentasi Detail 17 Pose Resmi:**
  - Merumuskan penjelasan teknis dan gameplay untuk seluruh 17 pose (`#01` s/d `#15C`) yang merujuk langsung ke 18 berkas ekspor terbaru di [`assets/poses_cv_vector/`](file:///e:/Semester%205/Game/AR%20Dragon/assets/poses_cv_vector/).
- **Audit Gap Kode Laptop B vs Gambar Pose Baru (Berbasis Hasil Uji Webcam):**
  - **6 Pose Terkunci Lolos Uji (*Zero Regression*, jangan ubah logika CV-nya):** `#01 Grounded Idle`, `#02 Grounded Walk`, `#04 Takeoff`, `#05 Lean & Arm Tilt`, `#08 Glide`, dan `#13 Tuck to Dive`.
  - **11 Pose Target Perbaikan (`#03, #06, #07, #09, #10, #11, #12, #14, #15A/B/C`):**
    - `#03 Grounded Look Up`: sambungkan penuh Tahap 2 `HEAD_PITCH` murni di Unity.
    - `#06 Flap to Climb`: tuning responsivitas & hysteresis kepakan udara.
    - `#07 Fly Up (Y-Pose)` vs `#15C Solaria (Double I-Pose 11)`: pisahkan bentangan serong lebar `Y` dari vertikal sejajar `11`.
    - `#09 Hover`: dukung tahan **L-Arm Pose (`90°`)** stabil selain kepak ringan lengan bawah.
    - `#10 Gentle Descent`: ganti dari *Tucked T-Pose* menjadi **Low-A Pose (`~45°` serong bawah)**.
    - `#11 Fly Down (Y-Dive)` & `#12 Fly Down Idle (W-Pose)`: tuning batas sudut siku & posisi pergelangan agar akurat dan mudah dibentuk.
    - `#14 Propeller Dash`: lengan atas melebar horizontal `180°` ke samping + lengan bawah berputar melingkar mengelilingi siku.
    - `#15A` kepak bawah tegas, `#15B` bukaan mulut `MOUTH_OPEN`, serta pembaruan UI `PoseGuideCatalog.cs` & `PoseGuideOverlayUI.cs` (`SCREEN 07`).
- **Penerbitan Prompt Laptop B:** Menyertakan prompt implementasi siap salin di [[panduan_detail_pose_dan_sync_laptop_b]].
- **Dampak Dokumentasi:** Memperbarui [[Tasks]] dan [[Devlog]].

## 2026-11-18 — Penyusunan Rencana Optimasi Token & Prompt Eksekusi untuk Laptop B ([[planning_optimasi_token_laptop_b]])
- **Analisis & Audit 6 Kandidat Tool Optimasi Token:**
  - Melakukan evaluasi mendalam terhadap 6 kandidat (RTK, Ponytail Antigravity IDE, Token-Saver Skill, Ponytail Original, Native Antigravity Skills, dan Rules/.antigravityignore) berdasarkan 7 parameter teknis ketat (kompatibilitas Antigravity, penghematan input/output token, otomatisasi, tumpang-tindih, potensi konflik arsitektur Unity, overhead prompt, dan verifikasi dokumentasi).
- **Perumusan "The Optimal Lean Stack" untuk Laptop B:**
  - Mengelompokkan solusi ke dalam kombinasi 4 pilar efisien dengan overhead mendekati nol:
    1. **`.antigravityignore` (Wajib / Prioritas 1):** Menghentikan scanning/indexing file binary, cache, dan metadata Unity (`Library/`, `Temp/`, `obj/`, `*.meta`) serta Python (`__pycache__/`, `.pytest_cache/`, `.venv/`), menghemat 5.000–30.000+ input token per pencarian.
    2. **RTK (Rust Token Killer):** Memangkas output log panjang terminal saat menjalankan `git status/diff` dan test suite Python CV (`pytest`) hingga 60–80%.
    3. **Ponytail-Lite Rules:** Mengadopsi 5 prinsip minimal coding (YAGNI, standard lib first, targeted diffs) ke dalam `AGENTS.md` Laptop B dengan klausul proteksi ketat agar tidak memotong kontrak arsitektur Unity di `kontrak_integrasi_ui_dan_progress.md`.
    4. **Clean Skills & Session Hygiene:** Mengisolasi 28 skill Blender agar tidak dimuat di workspace Laptop B, serta memanfaatkan slash command native `/compact` antar-tugas.
- **Penerbitan Dokumen Planning & Prompt Siap Pakai:**
  - Menghasilkan dokumen lengkap [[planning_optimasi_token_laptop_b]] berisi spesifikasi isi berkas `.antigravityignore`, blok aturan `AGENTS.md`, langkah instalasi bertahap, dan prompt eksekusi siap salin (*copy-paste*) untuk diinstruksikan langsung ke agen Laptop B.
- **Dampak Dokumentasi:** Memperbarui [[00 - Overview]], [[Tasks]], dan [[Devlog]].

## 2026-11-18 — Perbaikan Pose #14 Propeller Dash & Penguatan Visual Motion Blur (#14, #15A, #06) di FigCraft
- **Perbaikan Pose & Gerak `#14 · Propeller Dash`:**
  - Mengubah pose yang sebelumnya menyilang di depan dada menjadi pose mekanika CV sebenarnya: **kedua lengan atas melebar lurus horizontal ke samping** (`180°` dari bahu ke siku), dan **kedua lengan bawah berputar melingkar** (*twin propeller orbit*) mengelilingi sumbu siku.
  - Menambahkan jejak gerak rotasi penuh (*4-phase motion-blur ghost forearms* lengkap dengan tulang & titik pergelangan transparan), cincin orbit melingkar di kedua siku, serta panah arah putaran cyan di puncak dan dasar lingkaran orbit.
- **Penguatan Visual Gerak Dinamis pada `#15A · Zephyria Wind Flap` & `#06 · Flap to Climb`:**
  - **`#15A · Zephyria Wind Flap`:** Menambahkan dua lapis bayangan lengan atas (*ghost arms* dari posisi tinggi & tengah), busur lintasan ayunan melengkung ke bawah dengan panah ujung cyan (`↓`), serta gelombang hembusan angin dua lapis di bawah tangan agar gerakan kepakan turun ke bawah tidak terlihat statis.
  - **`#06 · Flap to Climb`:** Melengkapi efek *multi-exposure motion blur* (2 lapis *ghost arms* bawah/tengah lengkap dengan titik sendi transparan) serta busur lintasan kepak melengkung naik dengan panah arah panjat cyan di kedua ujung sayap.
- **Ekspor Gambar PNG (`scale: 2x`) ke Folder Resmi:**
  - Membuat folder khusus [`assets/poses_cv_vector/`](file:///e:/Semester%205/Game/AR%20Dragon/assets/poses_cv_vector/) dan mengekspor seluruh 17 kartu pose (`Pose_01_GroundedIdle.png` s/d `Pose_15C_Solaria_DewRain.png`, ukuran `480×500 px`) beserta lembar penuh `All_17_Poses_Sheet.png` (total 18 file PNG bersih).
- **Verifikasi Visual:** Seluruh kartu pose di halaman `Pose` [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) serta hasil ekspor PNG di [`assets/poses_cv_vector/`](file:///e:/Semester%205/Game/AR%20Dragon/assets/poses_cv_vector/) telah diperiksa dan terverifikasi tajam.
- **Dampak Dokumentasi:** Memperbarui [[Devlog]] dan [[Tasks]].

## 2026-11-18 — Perbaikan Arah Kemudi & Sudut Lengan Pose 05 (Lean & Arm Tilt) di FigCraft
- **Penyelarasan Arah Lean Tubuh dan Kemiringan Lengan:**
  - Memperbaiki ketidaksesuaian arah pada kartu `#05 Lean & Arm Tilt`: sebelumnya tubuh condong ke kanan (ke arah sisi Cyan), namun lengan Orange berada di bawah dan Cyan di atas (berlawanan dengan arah lean).
  - Menyinkronkan arah kemudi sesuai referensi gerak pesawat/kemudi belok (*banking*): saat tubuh condong ke kanan (ke sisi belok kiri stickman / viewer's right), lengan kanan (Orange) terangkat tinggi ke atas-kiri (`-30°`), dan lengan kiri (Cyan) menukik ke bawah-kanan (`+30°`).
  - Menyesuaikan kemiringan bahu trapesium torso (`M 129 91 ...`), garis tulang belakang (*spine wireframe*), clavicle, dan seluruh titik sendi agar sejajar rapi dengan garis lengan miring, sementara kedua kaki tetap tegak lurus sempurna.
- **Verifikasi Visual:** Lembar Segmen 2 dan seluruh lembar pose 17 kartu telah di-render ulang dan diverifikasi.
- **Dampak Dokumentasi:** Memperbarui [[Devlog]].

## 2026-11-18 — Refactoring Torso Trapesium & Bahu Organik Seluruh 17 Pose di FigCraft
- **Penghapusan Total Bentuk "T-Shape" Torso:**
  - Menghapus konstruksi dua kotak kaku (*Shoulders* 46px + *Waist* 24px) yang sebelumnya menimbulkan siluet mirip huruf "T" dan bahu kaku seperti gantungan baju.
  - Menggantinya dengan satu bentuk vektor tunggal **Trapesium Atletis (*Athletic Trapezoid Torso*)** yang organik di seluruh 17 kartu pose di [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft).
- **Spesifikasi Geometri & Bahu Baru:**
  - **Kerah Leher Halus:** Garis collar horizontal 8px di pangkal leher `(116..124, 86)`.
  - **Kelengkungan Bahu Alami:** Menurun melandai dari kerah ke sudut sendi bahu (`y = 86 -> 88`, `w = 34.4 px`) dengan sudut membulat bezier `Q`, menyambung secara anatomis dengan pangkal lengan.
  - **Penyempitan Trapesium ke Pinggang:** Meramping halus dari 34.4 px di bagian atas ke 24 px di panggul (`y = 138`), membentuk rasio trapesium ideal `~1.43 : 1` (sedikit lebih lebar di atas).
  - Memberikan ruang negatif (*clearance*) alami antara tangan yang menggantung dengan pinggang di pose netral (`#01`, `#03`, `#15B`), serta transisi mulus saat lengan terangkat (`#02`, `#04`, `#07`, `#08`, dsb.).
- **Verifikasi Lembar Lengkap:** Seluruh lembar pose di-render ulang dan dipastikan 100% konsisten, bersih, dan estetik.
- **Dampak Dokumentasi:** Memperbarui [[Tasks]] dan [[Devlog]].

## 2026-11-18 — Penyelesaian Seluruh 17 Visual Pose Kontrol di FigCraft (Minimalist Vector CV Sheet)
- **Pembangunan Seluruh 17 Kartu Pose di FigCraft (Page `Pose`, `id: 2260`):**
  - Menyelesaikan perakitan 17 kartu pose vektor minimalis berlatar terang (*Light Card Theme* `#F8FAFC`, border `#CBD5E1`, corner radius 16px) dengan siluet stickman charcoal gelap kontras tinggi (`#0F172A`) di [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft).
  - Mengelompokkan kartu ke dalam 4 baris segmen rapi dan proporsional (`240 × 250 px` per kartu):
    1. **Segmen 1 · Grounded Poses:** `#01 Grounded Idle`, `#02 Grounded Walk`, `#03 Grounded Look Up` (panah pandangan cyan).
    2. **Segmen 2 · Core Flight & Locomotion:** `#04 Takeoff` (6-lengan motion flap blur), `#05 Lean & Arm Tilt` (palang kemudi diagonal miring dengan tubuh & kaki tegak lurus), `#06 Flap to Climb` (sayap melengkung naik + lintasan kepak), `#07 Fly Up` (pose Y 45° overhead + panah naik), `#08 Glide` (pose T 180° horizontal + garis aliran angin).
    3. **Segmen 3 · Special Maneuvers:** `#09 Hover` (pose L siku 90° + cincin hover cyan di kaki), `#10 Gentle Descent` (pose A rendah 45° + panah turun), `#11 Fly Down` (Y-Dive menurun + speed lines), `#12 Fly Down Idle` (pose W siku tegak), `#13 Tuck to Dive` (lengan terlipat rapat di dada + sonic cone chevron di atas kepala), `#14 Propeller Dash` (lengan menyilang X di dada + cincin rotasi propeller cyan).
    4. **Segmen 4 · Village Actions:** `#15A Zephyria · Wind Flap` (kepakan sayap ke bawah + gelombang hembusan angin), `#15B Pyrewood · Open Mouth` (titik mulut terpisah + busur semburan air/water breath), `#15C Solaria · Dew Rain` (pose lengan 11 vertikal ke atas + rintik embun bercahaya).
- **Ketelitian Proporsi & Elemen CV (Computer Vision):**
  - Menerapkan proporsi ramping atletis (*tapered athletic torso*): palang bahu membulat 46px, pinggang ramping 24px, celah ketiak 2.5px, celah selangkangan kaki 4px, menghindari siluet tubuh kotak yang terlalu lebar.
  - Seluruh sendi dilengkapi kerangka kawat CV tebal (*wireframe skeleton lines*) dan titik sendi dual-tone bercahaya:
    - Sisi Kanan: Tulang `#FB923C`, Titik Sendi `#EA580C`.
    - Sisi Kiri: Tulang `#38BDF8`, Titik Sendi `#0284C7`.
    - Tulang Belakang & Leher/Panggul: `#94A3B8` & `#E2E8F0`.
    - Wajah: 5 titik cyan terang (`#00E5FF`) untuk mata, hidung, mulut, dan dagu.
- **Verifikasi Visual:** Lembar kartu lengkap telah diperiksa dan diverifikasi konsistensi gayanya tanpa distorsi.
- **Dampak Dokumentasi:** Memperbarui [[Tasks]] dan [[Devlog]].

## 2026-11-18 — Implementasi Vektor Kartu Pose di FigCraft: Tema Terang dengan Bahu & Kerangka CV Lengkap
- **Penyempurnaan Desain Vektor di FigCraft (Page `Pose`):**
  - Mengadopsi pilihan pengguna untuk **Opsi B (Light Card Theme)**: Background Card `#F8FAFC` terang bersih berpadu dengan Siluet Karakter `#0F172A` (Charcoal Gelap) yang menghasilkan kontras tajam.
  - Menambahkan **kontur bahu solid** pada torso agar pangkal lengan menyambung secara anatomis dan tidak mengambang.
  - Membangun **garis kerangka kawat CV (*wireframe skeleton lines*)** penuh yang menghubungkan seluruh sendi:
    - Garis tulang belakang (*Spine*) & palang bahu (*Shoulders*) serta panggul (*Hips*).
    - Tulang lengan dan kaki kanan warna Oranye / Amber (`#FB923C`).
    - Tulang lengan dan kaki kiri warna Cyan / Biru (`#38BDF8`).
    - Titik sendi bulat tebal dual-tone di setiap persendian serta titik wajah cyan di area muka.
  - Menyelaraskan proporsi kartu menjadi `240 × 250 px` yang presisi dan seimbang.
- **Dampak Dokumentasi:** Memperbarui [[Tasks]] dan [[Devlog]].

## 2026-11-17 — Pengarsipan Aset Gambar Segmen 2 (Pose 04–08) & Kesiapan Prompt Segmen 3 ([[prompt_visual_pose_cv_kerangka]])
- **Penyimpanan Aset Segmen 2:**
  - Seluruh 5 gambar hasil generasi pengguna untuk Segmen 2 telah berhasil disimpan dan diarsipkan ke:
    1. [`assets/poses_cv_guide/Pose_04_Takeoff.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/assets/poses_cv_guide/Pose_04_Takeoff.jpg) (Tangan mengepak 6 lengan motion blur)
    2. [`assets/poses_cv_guide/Pose_05_LeanArmTilt.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/assets/poses_cv_guide/Pose_05_LeanArmTilt.jpg) (Kemudi belok miring, kaki tegak lurus sempurna)
    3. [`assets/poses_cv_guide/Pose_06_FlapToClimb.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/assets/poses_cv_guide/Pose_06_FlapToClimb.jpg) (Kepakan melayang multi-arm motion blur)
    4. [`assets/poses_cv_guide/Pose_07_FlyUp.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/assets/poses_cv_guide/Pose_07_FlyUp.jpg) (Full Y-Pose simetris overhead)
    5. [`assets/poses_cv_guide/Pose_08_Glide.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/assets/poses_cv_guide/Pose_08_Glide.jpg) (Full T-Pose horizontal)
    Serta duplikatnya di [`Referensi/Referensi Pose CV/`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi/Referensi%20Pose%20CV/).
  - Hasil Segmen 2 terverifikasi 100% konsisten, proporsional, dan estetik (efek motion blur 6-lengan pada kepakan sayap bekerja sempurna, dan kaki terkunci tegak tanpa tekukan).
- **Kesiapan Segmen 3:**
  - Menyusun set prompt khusus Segmen 3 (Pose 09 s/d 14: Hover Mode, Gentle Descent dengan panah turun, Fly Down Y-Dive, W-Pose Descent, Aerodynamic Tuck Dive, dan Propeller Dash dengan revolving motion blur).
- **Dampak Dokumentasi:** Memperbarui [[prompt_visual_pose_cv_kerangka]], [[Tasks]], dan [[Devlog]].

## 2026-11-17 — Pengarsipan Aset Gambar Segmen 1 (Pose 01–03) & Penyeragaman Formula Segmen 2–4 ([[prompt_visual_pose_cv_kerangka]])
- **Penyimpanan Aset Segmen 1:**
  - Ketiga gambar hasil generasi pengguna untuk Segmen 1 telah disimpan dan diarsipkan resmi ke dua direktori proyek:
    1. [`assets/poses_cv_guide/Pose_01_GroundedIdle.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/assets/poses_cv_guide/Pose_01_GroundedIdle.jpg)
    2. [`assets/poses_cv_guide/Pose_02_GroundedWalk.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/assets/poses_cv_guide/Pose_02_GroundedWalk.jpg)
    3. [`assets/poses_cv_guide/Pose_03_GroundedLookUp.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/assets/poses_cv_guide/Pose_03_GroundedLookUp.jpg)
    Serta duplikatnya di [`Referensi/Referensi Pose CV/`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi/Referensi%20Pose%20CV/).
  - Hasil Segmen 1 terverifikasi 100% konsisten, proporsional, dan estetik (piktogram siluet 2D solid, titik dual-tone amber/cyan, titik wajah cyan kontras tinggi, dan panah elevasi mendongak pada Pose 03).
- **Penyelarasan Prompt Segmen 2–4:**
  - Menyamakan seluruh teks awalan (*base prefix*) pada Segmen 2 (Pose 04–08), Segmen 3 (Pose 09–14), dan Segmen 4 (Pose 15A–15C) dengan formula presisi yang telah sukses di Segmen 1, serta memastikan kaki selalu tegak/stasioner (*straight legs*).
- **Dampak Dokumentasi:** Memperbarui [[prompt_visual_pose_cv_kerangka]], [[Tasks]], dan [[Devlog]].

## 2026-11-17 — Koreksi Prompt Takeoff: Visual Tangan Mengepak Sayap ([[prompt_visual_pose_cv_kerangka]])
- **Koreksi Anatomi Pose Takeoff (#04):**
  - Mengoreksi kesalahan visual Takeoff yang sebelumnya mendeskripsikan tangan mengarah ke bawah paha (inverted V), menjadi **murni pose tangan mengepak sayap (*wide wing-flapping posture* `\_o_/`)** sesuai aset resmi `UI_Pose_04_Takeoff.png`.
  - Kedua lengan terentang lebar ke samping dengan siku ditekuk dan lengan bawah mengarah ke atas-luar layaknya sayap naga yang sedang mengepak kuat dari darat untuk lepas landas.
- **Dampak Dokumentasi:** Memperbarui [[prompt_visual_pose_cv_kerangka]], [[Tasks]], dan [[Devlog]].

## 2026-11-17 — Revisi Prompt Visual Pose CV: Semi-Stickman Anatomis & Pose To-The-Point ([[prompt_visual_pose_cv_kerangka]])
- **Penyempurnaan Karakter & Penegasan Pose:**
  - Mengubah karakter menjadi **semi-stickman dengan proporsi tubuh manusia yang akurat** (figur geometris ramping, sendi/anggota tubuh berasio anatomi manusia, kepala bulat minimalis, bebas tekstur kulit/pakaian).
  - Menegaskan seluruh bentuk pose agar **to the point & tanpa ragu**:
    - `#02 Grounded Walk`: Dipertegas menjadi **Full T-Pose di darat** (kaki di tanah, tangan lurus horizontal 180°).
    - `#01 Grounded Idle`: Full I-Pose (tangan merapat lurus ke samping paha).
    - `#07 Fly Up`: Full Y-Pose simetris overhead.
    - `#12 Fly Down Idle`: Full W-Pose simetris di samping telinga.
    - `#09 Hover Mode`: Full L-Arm Pose (siku 90° ke depan dada).
    - `#13 Tuck to Dive`: Full aerodynamic tuck rapat di dada.
- **Dampak Dokumentasi:** Memperbarui [[prompt_visual_pose_cv_kerangka]], [[Tasks]], dan [[Devlog]].

## 2026-11-17 — Penyempurnaan Katalog Prompt Visual Pose CV Minimalis (Biometric Mannequin UI) ([[prompt_visual_pose_cv_kerangka]])
- **Penyelarasan Desain Sesuai Referensi Visual Pengguna:**
  - Menyesuaikan formula prompt 100% mengikuti referensi visual biometrik: siluet manekin manusia 3D abu-abu transparan dengan *soft rim lighting*, garis kerangka tipis presisi, titik sendi dual-tone (**Amber/Kuning** di satu sisi, **Cyan/Teal** di sisi lain, titik wajah Cyan), serta latar frame grid kotak biru gelap minimalis dengan *corner reticle brackets*.
  - Menghapus seluruh ornamen fantasi, partikel api/air/angin, efek sihir, serta teks HUD yang membuat gambar ramai agar hasil gambar murni berfokus pada kejelasan postur anatomi Computer Vision.
- **Dampak Dokumentasi:** Memperbarui [[prompt_visual_pose_cv_kerangka]], [[Tasks]], dan [[Devlog]].

## 2026-11-17 — Penyusunan Katalog Prompt Visual Pose CV Berbasis Titik & Kerangka ([[prompt_visual_pose_cv_kerangka]])
- **Keputusan & Arahan Desain Visual:**
  - Menyusun set prompt image generation untuk seluruh pose kontrol *Fanamir: The Sky Guardian* dengan gaya visual murni **Computer Vision Keypoints + Skeleton Wireframe** (ala YOLOv8-pose / MediaPipe).
  - Membagi pose secara sistematis ke dalam **4 Segmen Gerak Resmi**:
    1. *Segmen 1: Grounded Poses (Pose Darat)* — Pose 01 Grounded Idle, Pose 02 Grounded Walk, Pose 03 Grounded Look Up (Head Pitch UP, dagu terangkat, dada rileks).
    2. *Segmen 2: Locomotion & Penerbangan Inti* — Pose 04 Takeoff, Pose 05 Lean & Arm Tilt (kemudi belok kiri/kanan), Pose 06 Flap to Climb, Pose 07 Fly Up (Y-Pose + dagu naik), Pose 08 Glide (T-Pose sejajar).
    3. *Segmen 3: Maneuver Khusus* — Pose 09 Hover Mode (tangan di depan dada), Pose 10 Gentle Descent (dagu tunduk), Pose 11 Fly Down (Y-Dive), Pose 12 Fly Down Idle (W-Descent), Pose 13 Tuck to Dive (-45° tuck), Pose 14 Propeller Dash (putaran tangan melingkar).
    4. *Segmen 4: Village Actions (Aksi Pemulihan 3 Biome Desa)* — Pose 15A Zephyria Wind Flap (meniup kabut), Pose 15B Pyrewood Open Mouth Water Breath (semburan air via tracking bukaan mulut), Pose 15C Solaria Raise Both Hands (hujan embun suci).
  - Menyertakan *Master Style Directive*, pembagian warna titik (Cyan untuk titik wajah Hidung/Dagu/Mulut, Amber/Emas untuk tangan/bahu, Biru Kobalt untuk torso), dan parameter teknis AI Generator (`--ar 1:1 --v 6.0 --style raw`).
- **Dampak Dokumentasi:** Membuat [[prompt_visual_pose_cv_kerangka]], memperbarui [[Tasks]], dan [[Devlog]].

## 2026-11-17 — Penyusunan Checklist Finalisasi & Prasyarat Pra-Sesi 4 ([[checklist_finalisasi_pra_sesi_4]])
- **Keputusan & Arahan Teknis:**
  - Menyusun inventarisasi menyeluruh prasyarat teknis sebelum implementasi mekanisme tantangan desa (Sesi 4: `VillageZoneController.cs`, `Session4_VillageBuilder.cs`).
  - Mengelompokkan pekerjaan pra-Sesi 4 ke dalam **5 Pilar Utama**:
    1. *Pilar 1 (Kontrol & CV):* Pembersihan teks preview CV (kecuali FPS) di Python & integrasi `HEAD_PITCH` di controller Unity (Tahap 2 Face Tracking).
    2. *Pilar 2 (Audit 15 Pose Berbasis Screenshot):* Pengujian empiris webcam oleh pemain untuk pose `#01`–`#15` (khususnya `#06 Flap`, `#07 Fly Up`, `#15 Mouth Open` yang menjadi inti tantangan 3 desa).
    3. *Pilar 3 (Scene Markers & Navigasi Waypoint):* Penempatan dan verifikasi koordinat GameObject 3 desa (`(-562.4, 14.1, 274.3)`, `(739.6, 12.9, 7.8)`, `(39.4, 14.7, -647.6)`) serta 3 sub-marker (Danau Zephyria, Hutan Terbakar, Sky Altar Solaria) dan hookup `WaypointCompass.cs`.
    4. *Pilar 4 (Mekanik Auto-Hover & Escape di Controller Naga):* Logika `EnterAutoHover`, `Flap to Escape`, dan auto-release saat pengisian air di Danau Zephyria mencapai 100%.
    5. *Pilar 5 (Kesiapan Sistem VFX Effekseer):* Setup plugin `EffekseerForUnity` dan penyiapan emitter partikel kabut, api hutan, semburan air, dan hujan embun.
- **Dampak Dokumentasi:** Membuat [[checklist_finalisasi_pra_sesi_4]], memperbarui [[laptop B Patch 1 planning]], [[Tasks]], dan [[Devlog]].

## 2026-11-17 — Planning Pembersihan Teks Preview CV & Protokol Audit 15 Pose Berbasis Screenshot ([[planning_audit_pose_dan_clean_cv_preview]])
- **Keputusan & Arahan Teknis:**
  - **1. Pembersihan Teks Preview CV (`active_cv_sender.py` & PIP):**
    - Mengosongkan seluruh teks indikator telemetri (kotak HUD teks, nilai float `HEAD_PITCH`, angka bukaan mulut, status pose, dll.) pada frame preview kamera, **kecuali angka FPS** yang tetap ditampilkan kecil dan bersih di sudut layar.
    - Menampilkan **murni visual titik (*keypoints*) dan garis kerangka (*skeleton*)** serta FPS agar pandangan ke tubuh pemain bersih dan tidak terhalang teks.
  - **2. Protokol Audit & Penyelarasan Pose Berbasis Screenshot:**
    - Pemain akan menguji 15 pose di hadapan webcam mengacu pada [[Panduan_15_Pose_Fanamir.pdf]].
    - Setiap pose yang gagal terdeteksi, lambat terpicu, atau tidak akurat akan di-screenshot oleh pemain (menampilkan feed kamera dengan titik dan kerangka).
    - Laptop B menggunakan data tangkapan layar tersebut untuk menganalisis kesalahan geometri dan merevisi nilai ambang batas (*threshold* / *hysteresis*) di `motion_recognizer.py` / `active_cv_sender.py` serta damping di `DragonFlightController.cs`.
- **Dampak Dokumentasi:** Membuat [[planning_audit_pose_dan_clean_cv_preview]], memperbarui [[laptop B Patch 1 planning]], [[Tasks]], dan [[Devlog]].

## 2026-11-16 — Penyusunan Panduan Setup Lengkap & Prompt Onboarding Brain Antigravity + Obsidian (`prompt-setup-brain.md`)
- **Penyusunan Master Guide & Prompt Wizard:**
  - Menyusun panduan menyeluruh berbasis arsitektur nyata project AR Dragon / Fanamir ke dalam [`prompt-setup-brain.md`](file:///e:/Semester%205/Game/AR%20Dragon/prompt-setup-brain.md).
  - Merancang tag `<SYSTEM_ONBOARDING_INSTRUCTION>` agar ketika pengguna mem-paste dokumen ke chat Antigravity di project baru mana pun, AI langsung bertindak sebagai Onboarding Wizard interaktif langkah-demi-langkah.
  - Mendokumentasikan 5 pilar sistem Brain:
    1. *Pemisahan Sub-Repo Git Terisolasi:* Sub-repo `brain/` dikelola terpisah dari repositori kode/game (`.gitignore` root mengabaikan `brain/`, `.gitignore` brain mengabaikan `.obsidian/`).
    2. *Arsitektur Anti-Collision Multi-Device:* Folder eksklusif `laptop-a/` dan `laptop-b/` dengan aturan strict write-own dan read-partner untuk eliminasi 100% merge conflict.
    3. *Konektivitas Graf Pengetahuan Obsidian:* Format markdown, YAML frontmatter, dan `[[wikilinks]]` untuk visualisasi relasi ide, task, dan memori.
    4. *Otomasi `AGENTS.md`:* Kontrak startup (`git -C brain pull`, auto-read overview & context) dan end-of-task handover.
    5. *Protokol Sesi Memori (`"jadikan sesi ini memori"`):* Standarisasi 5 bagian wajib (Tujuan, Keputusan, Eksekusi, Parameter Kritis, Sisa Tugas) dan auto-commit/push.
  - Menyertakan template lengkap siap pakai untuk `00 - Overview.md`, `AGENTS.md` (Laptop A, Laptop B, Single Device), `Tasks.md`, `Devlog.md`, `konteks.md`, dan `prd.md`.
- **Dampak Dokumentasi:** Memperbarui [[Tasks]] dan [[Devlog]].

## 2026-11-16 — Penerbitan Buku Panduan Resmi 15 Pose & Sistem Kemudi (`.docx` & `.pdf`)
- **Penerbitan Dokumen Panduan Gerak:**
  - Membuat naskah lengkap dan mengekspor dokumen resmi **`Panduan_15_Pose_Fanamir.docx`** (`1.92 MB`) dan **`Panduan_15_Pose_Fanamir.pdf`** (`4.50 MB`, `13 halaman`) di root workspace dan folder `docs/`.
  - Dokumen mencakup:
    1. *Halaman Sampul:* Visual hero Fanamir, metadata rilis v2.0, dan platform info.
    2. *Filosofi Exergaming:* Pembebasan dada dari kelelahan otot, pemisahan sumbu kemudi secara alami.
    3. *Prinsip Inti Kemudi:* 5 pilar kemudi arah (kiri/kanan = badan miring, atas/bawah = kepala naik/turun, sayap = tenaga, mulut = semburan air).
    4. *Katalog 15 Pose:* Rincian kartu visual untuk 4 Kategori (Grounded #01–#03, Locomotion #04–#08, Maneuver #09–#14, dan Village Actions #15) lengkap dengan panduan tubuh, respon game, telemetri AI, dan tips eksekusi.
    5. *Aksi 3 Desa & Quest Flow:* Rincian krisis dan mekanisme pemulihan di Desa Zephyria, Pyrewood, dan Solaria.
    6. *Kombinasi Manuver Akrobatik:* Spiral Climb, Dive to Swoop, Quick Stop to Hover, dan Soft Landing.
    7. *Panduan Setup Webcam & Ergonomi:* Pencahayaan, jarak 1.5–2.5m, mode duduk/berdiri, rasio aspek kamera, dan ganti kamera in-game via Pause Modal.
- **Dampak Dokumentasi:** Memperbarui [[Tasks]] dan [[Devlog]].

## 2026-11-16 — Strategi 2 Tahap: Revisi Titik Wajah (Tahap 1) Lalu Penyesuaian Pose Vertikal Tanpa Dada (Tahap 2) ([[planning_revisi_titik_wajah_dan_pov]])
- **Keputusan Alur Eksekusi:**
  - **Tahap 1 (Revisi Titik Wajah):** Fokus pertama murni pada deteksi stabil 3 titik di Python CV (Hidung, Dagu, Mulut), sinyal normalisasi `HEAD_PITCH` (naik `+` dan turun `-`), serta rasio bukaan mulut `MOUTH_OPEN`.
  - **Tahap 2 (Penyesuaian Beberapa Pose Terkait):**
    - `#03 Grounded Look Up`: Dada tidak perlu diangkat/mendongak, pemain cukup mengangkat sedikit dagu untuk membuat naga mendongak ke langit.
    - Kontrol Vertikal Terbang: Angkat dagu/hidung $\rightarrow$ naga mendongak & terbang menanjak naik (*Fly Up*); turunkan dagu sedikit $\rightarrow$ naga menunduk & meluncur turun (*Pitch Down*). Seluruh pitch vertikal dikendalikan kepala tanpa perlu membusungkan/mengangkat dada!
    - Tegasan Kemudi Horizontal: Gerakan kepala tidak mempengaruhi belok kiri/kanan. Kemudi belok horizontal murni 100% dari *Lean & Arm Tilt*.
- **Dampak Dokumentasi:** Memperbarui [[planning_revisi_titik_wajah_dan_pov]], [[Tasks]], dan [[Devlog]].

## 2026-11-16 — Penajaman Kontrol Titik Wajah: Hidung & Dagu untuk POV & Fly Up Vertikal ([[planning_revisi_titik_wajah_dan_pov]])
- **Klarifikasi & Penguncian Desain:**
  - Gerakan mengangkat Hidung & Dagu menggerakkan orientasi kepala naga menengadah ke atas (POV) dengan **tujuan utama mengarahkan naga untuk TERBANG MENANJAK NAIK (Fly Up)**.
  - Sinyal elevasi kepala ini tidak mencemari atau membelokkan kemudi horizontal (Yaw/Roll belok kiri-kanan tetap 100% dikendalikan oleh *Lean & Arm Tilt*).
  - Mulut murni digunakan untuk semburan air purba (*Water Breath*).
- **Dampak Dokumentasi:** Memperbarui [[planning_revisi_titik_wajah_dan_pov]], [[Tasks]], dan [[Devlog]].

## 2026-11-16 — Spesifikasi Revisi Titik Wajah (Hidung, Dagu, Mulut) & Pemisahan Kontrol POV ([[planning_revisi_titik_wajah_dan_pov]])
- **Keputusan Desain & Pemisahan Kontrol:**
  - **3 Titik Wajah Inti:** Hidung (`Nose`), Dagu (`Chin`), dan Mulut (`Mouth`).
  - **Pemisahan Mutlak (*Decoupling*) POV vs Lintasan Terbang:**
    - Gerakan mengangkat Hidung & Dagu ke atas dihitung sebagai `HEAD_PITCH_POV` yang memutar tulang kepala/leher naga (`DragonHeadLookController.cs`) dan mengarahkan kamera ke langit untuk melihat pemandangan ke atas.
    - **TIDAK mengubah atau membelokkan arah lintasan terbang Fanamir** (vektor laju maju dan ketinggian naga tetap stabil mengikuti gestur sayap).
  - **Aksi Semburan Air Purba:**
    - Bukaan celah bibir vertikal (`MouthOpen > 0.45`) murni digunakan untuk memicu `VFX_Dragon_WaterBreath_Stream` saat memadamkan kebakaran hutan di Desa Pyrewood.
- **Dampak Dokumentasi:** Membuat [[planning_revisi_titik_wajah_dan_pov]], memperbarui [[Tasks]], dan [[Devlog]].

## 2026-11-16 — Spesifikasi Teknis Fokus 1: Sistem Kamera Dinamis & Navigasi In-Game ke Kalibrasi ([[planning_kamera_dinamis_dan_navigasi_pause]])
- **Rincian Dokumen:** Menyusun spesifikasi implementasi detail untuk Laptop B mencakup:
  1. Penanganan rasio aspek kamera dinamis (`16:9`, `4:3`, dan vertikal `9:16` ponsel) via `AspectRatioFitter.AspectMode.FitInParent` di Unity (`RawImage` Kalibrasi & HUD PIP) serta inferensi YOLO letterboxed simetris di Python.
  2. Perapian HUD In-Game: menyembunyikan dropdown kamera & tombol Scan/Matikan saat mode terbang.
  3. Alur navigasi `In-Game -> ESC -> PF_PauseModal -> CALIBRATION & CAMERA -> Live Calibration Overlay -> RESUME` tanpa me-reload scene.
  4. Auto-persistence kamera via `PlayerPrefs` (`"DragonWingAR_LastCameraDevice"`).
  5. Penyusunan prompt eksekusi modular untuk dieksekusi oleh agen di Laptop B.

## 2026-11-16 — Perumusan Checklist Pra-Sesi 4 Laptop B: Kamera Dinamis, Alur Pause ke Kalibrasi, & Validasi 15 Pose ([[laptop B Patch 1 planning]])
- **Keputusan & Arahan Teknis Laptop B:**
  - **Fokus 1 (Sistem Kamera & Rasio Dinamis):**
    - Dukungan rasio input kamera fleksibel (`16:9`, `4:3`, dan vertikal `9:16` seperti DroidCam/Iriun) menggunakan `AspectRatioFitter.AspectMode.FitInParent` di Unity (`RawImage` Kalibrasi & PIP) serta padding *letterbox* simetris di Python OpenCV tanpa peregangan paksa.
    - Menghapus kontrol ganti kamera langsung dari HUD gameplay agar tidak mengganggu pemain saat terbang.
    - Menyediakan alur ganti kamera & kalibrasi dari in-game via tombol `ESC` $\rightarrow$ `PF_PauseModal` $\rightarrow$ tombol `CALIBRATION & CAMERA` $\rightarrow$ overlay Kalibrasi live (tanpa reload scene / tanpa kembali ke Lobby) $\rightarrow$ `RESUME`.
    - Auto-persistence kamera via `PlayerPrefs` (`LastCameraDevice`) dengan fallback kamera default bawaan laptop (indeks 0).
  - **Fokus 2 (Validasi 15 Pose & Finalisasi Kontrol Fanamir):**
    - Pengujian penuh 15 pose dengan webcam fisik di `MainIsland_Gameplay.unity` (Grounded, Takeoff, Lean/Arm Tilt, Flap, Fly Up Y, Glide T, Hover, Descent, Dive, Dash, dan Village Actions).
    - Polishing deadzone kemudi belok ($\pm 2.5^\circ$), hysteresis *Flap vs Glide*, kelancaran animasi sayap 12-tulang `DragonWingAnimator.cs`, serta verifikasi batas ketinggian `maxAltitude = 260f` melewati puncak Center Peak (`165.85m`).
  - **Checklist Tambahan Pra-Sesi 4:**
    - Perapian HUD in-game, verifikasi posisi marker 3 desa di terrain archipelago, dan instalasi plugin Effekseer VFX di Laptop B sebelum mengimplementasikan `VillageZoneController.cs`.
- **Dampak Dokumentasi:** Memperbarui [[laptop B Patch 1 planning]], [[Tasks]], dan [[Devlog]].

## 2026-11-16 — Finalisasi Naskah Dialog Narasi, Parameter Timer/Bintang Stage 01–03, & Daftar Audio/SFX ([[mekanisme_tantangan_desa]])
- **Persetujuan User:**
  - Menyetujui nama resmi 3 desa: **Desa Zephyria** (Biome 1 - Smog), **Desa Pyrewood** (Biome 2 - Wildfire), dan **Desa Solaria** (Biome 3 - Drought).
  - Menyetujui mekanisme tantangan Desa Solaria (*Ritual Embun Suci / Dew Summoning* dengan gestur *Raise Both Hands / Fly Up*).
- **Pembaruan Isi Dokumen ([[mekanisme_tantangan_desa]]):**
  - **1. Naskah Dialog Narasi 3 Baris (`SCREEN 08` `PF_QuestNarrativeBox`):**
    - *Desa Zephyria (Tetua Rowan):* Masalah kabut jurang beracun $\rightarrow$ Tanaman layu & warga batuk $\rightarrow$ Arahan terbang naik & kepakkan sayap sekuat tenaga (*Flap Wings*).
    - *Desa Pyrewood (Penjaga Rimba Keith):* Sambaran petir membakar hutan $\rightarrow$ Merembet ke pemukiman $\rightarrow$ Arahan terbang ke Danau Zephyria untuk isi air, lalu kembali semprotkan air dengan buka mulut (*Open Mouth*).
    - *Desa Solaria (Tetua Mira):* Terik surya keringkan mata air $\rightarrow$ Tanah merekah & ternak kehausan $\rightarrow$ Arahan terbang tinggi ke puncak tebing & angkat kedua tangan ke langit panggil hujan embun (*Raise Hands*).
  - **2. Parameter Batas Waktu & Kriteria Bintang (Stage 01–03):**
    - Stage 01 (1 Desa): 120s (⭐⭐⭐ $\ge 60\text{s}$, ⭐⭐ $20–59\text{s}$, ⭐ $< 20\text{s}$).
    - Stage 02 (2 Desa): 240s (⭐⭐⭐ $\ge 90\text{s}$, ⭐⭐ $30–89\text{s}$, ⭐ $< 30\text{s}$).
    - Stage 03 (3 Desa): 360s (⭐⭐⭐ $\ge 120\text{s}$, ⭐⭐ $30–119\text{s}$, ⭐ $< 30\text{s}$).
    - Selama narasi timer berhenti (PAUSE); selama tantangan desa timer terus berjalan (TIDAK PAUSE).
  - **3. Daftar Audio & SFX (`AudioManager.cs` Sesi 5):** 3 BGM (`BGM_Lobby`, `BGM_Flight_Adventure`, `BGM_Village_Challenge`), 5 SFX Naga/Penerbangan (`Flap`, `GlideWind`, `DiveWhoosh`, `WaterBreath`, `DewSummonChime`), 4 SFX Interaksi Misi (`WaterAbsorb`, `FireExtinguish`, `SmogDisperse`, `VillageRestoredFanfare`), dan 5 SFX UI/Loop (`UIClick`, `QuestAccepted`, `DialogueNext`, `VictoryFanfare`, `DefeatGong`).

## 2026-11-15 — Perencanaan Pemasangan Skill VFX Effekseer di Laptop B ([[rencana_skill_vfx_effekseer_laptop_b]] & [[Perencanaan Skill VFX Effekseer]])
- **Latar Belakang & Kebutuhan:**
  - Menindaklanjuti kebutuhan sistem VFX untuk pemulihan 3 desa pada Sesi 4 ([[mekanisme_tantangan_desa]]), disusun dokumen panduan komprehensif bagi Laptop B untuk mengadopsi paket *Agent Skills* open-source **`kanaworksai-hue/effekseer-vfx-skill`**.
- **Poin-Poin Utama Rencana ([[rencana_skill_vfx_effekseer_laptop_b]]):**
  - **1. Profil Skill:** Menyediakan dua kapabilitas agen, yaitu `effekseer-vfx` (authoring efek partikel prosedural & flipbooks berbasis ~30 layer primitives + compiler `EffekseerCore`) dan `vfx-review` (evaluasi kualitas rendering independen).
  - **2. Metode Instalasi di Laptop B:** Menggunakan perintah CLI `npx skills add kanaworksai-hue/effekseer-vfx-skill` (atau clone manual subfolder `skills/` ke `.agents/skills/`).
  - **3. Integrasi Unity 6 URP:** Mengimpor paket `EffekseerForUnity` (`https://github.com/effekseer/EffekseerForUnity.git#package`) via Unity Package Manager dan mendaftarkan `EffekseerRendererFeature` pada aset URP Forward Renderer Data.
  - **4. Katalog Kebutuhan VFX Game Fanamir:**
    - *Desa Zephyria (Smog Valley):* `VFX_WindGust_Flap` (busur sabit angin & pusaran spiral), `VFX_Smog_Dissipation` (penguraian kabut beracun), dan `VFX_Zephyria_Restoration_Glow`.
    - *Desa Pyrewood (Wildfire Woods):* `VFX_Water_Gathering` (hisapan air di danau), `VFX_Dragon_WaterBreath_Stream` (semburan air deras dari socket mulut Fanamir), serta `VFX_Fire_Extinguish_Steam` (uap putih pemadam api).
    - *Desa Solaria (Drought Plateau):* `VFX_DewSummon_Aura` (cincin embun zamrud auto-hover), `VFX_Radiant_DewRain` (hujan kristal pemulih), dan `VFX_Solaria_Bloom_Sprouts`.
    - *Flight Dynamics:* `VFX_Wingtip_Trail`, `VFX_Dive_WindCone`, dan `VFX_Dash_SonicRing`.
  - **5. Integrasi Skrip:** Penyiapan pemanggilan komponen `EffekseerEmitter` di `VillageZoneController.cs` selaras dengan state mesin tantangan dan `VillageChallengeUI`.
- **Dampak Dokumentasi:** Memperbarui [[00 - Overview]], [[laptop B Patch 1 planning]], [[Tasks]], dan [[Devlog]].

## 2026-11-15 — Finalisasi Mekanisme Tantangan Desa, Penamaan 3 Desa, & Mode Free Play ([[mekanisme_tantangan_desa]])
- **Keputusan Game Logic & Mekanisme Tantangan (Sesi 4 & 5):**
  - **1. Penamaan 3 Desa:**
    - Biome 1 (Smog Valley): **Desa Zephyria** *(Valley of Winds)*.
    - Biome 2 (Wildfire Woods): **Desa Pyrewood** *(Cinder Forest)*.
    - Biome 3 (Drought Plateau): **Desa Solaria** *(Arid Highlands)*.
  - **2. Dua Radius Interaksi & State Mesin:**
    - *Radius Narasi (Ground Desa, R=35m):* Naga auto-hover menghadap desa, **Timer Stage PAUSE**, `PF_QuestNarrativeBox` auto-play. Dapat dibatalkan (*escape*) kapan saja dengan kepak sayap (Flap). Jika ditunggu selesai, quest diambil dan objective ter-update.
    - *Radius Zona Tantangan (Spawn pasca quest diambil):* Naga auto-hover tantangan, **Timer Stage TETAP BERJALAN** (tidak pause agar ada tensi waktu). Pemain tetap bisa escape dengan kepak sayap jika ingin reposisi.
  - **3. Mekanisme Tantangan per Desa (Opsi A: Linear Hold):**
    - *Desa Zephyria (Biome 1):* Radius baru spawn sedikit lebih tinggi (`Y +25m`). Tantangan: Flap sayap ritmik untuk mengusir polusi kabut (`0% -> 100%`).
    - *Desa Pyrewood (Biome 2):* 2 radius interaktif. (1) Danau Zephyria di Biome 1 untuk isi kapasitas air naga `0 -> 100%` (auto-release saat penuh); (2) Hutan terbakar di Biome 2, tantangan buka mulut (*Open Mouth*) untuk semprot air (*WaterBreath*) memadamkan api.
    - *Desa Solaria (Biome 3):* Radius spawn tinggi di puncak tebing (`Y +40m`). Tantangan: *Raise Both Hands (Angkat Kedua Tangan Tinggi ke Atas)* secara stabil untuk ritual memanggil awan & hujan embun (*Dew Summoning*) memulihkan tanah retak & danau kering (`0% -> 100%`). Variasi ini sempurna melengkapi kardio sayap (A) dan buka mulut (B) dengan peregangan bahu/postur (C) untuk exergaming.
  - **4. Hierarki Teks Quest Objective:** Global Objective (`Pulihkan semua desa 0/3`) $\rightarrow$ Village Objective (`Selesaikan misi Desa X 0%`) $\rightarrow$ Sub-Objectives ringkas.
  - **5. Mode Free Play di Lobby:** Kartu ke-4 di Lobby (`SCREEN 00`) berstatus `LOCKED` dan terbuka otomatis setelah menyelesaikan Stage 01, 02, dan 03. Mode bebas terbang tanpa quest & timer di seluruh kepulauan yang sudah pulih hijau.

## 2026-11-15 — Pembaruan `TS & List Asset.xlsx` & Audit Struktur Folder Laptop A
- **Pembaruan Excel ([`TS & List Asset.xlsx`](file:///e:/Semester%205/Game/AR%20Dragon/TS%20&%20List%20Asset.xlsx), backup di [`misc/TS & List Asset_backup_20261115.xlsx`](file:///e:/Semester%205/Game/AR%20Dragon/misc/TS%20&%20List%20Asset_backup_20261115.xlsx)):** Memutakhirkan sheet `List Asset` menjadi `7` kategori (Model Naga Fanamir, 5 Varian Rumah, Pulau 3 Biome, 12 Varian Pohon, Aset UI/UX `v1.8`, VFX Particles `40%`, & Audio/SFX `0%`) serta memperluas sheet `TS` (`A. Brainstorming & Desain`, `B. Assets`, `C. Setup & Teknologi Inti` `100%`, dan kategori baru `D. Implementasi Gameplay, UI, & Build`).

## 2026-11-15 — Penyusunan Planning Patch 1 untuk Laptop B ([[laptop B Patch 1 planning]])
- **Ringkasan:** Menyusun daftar prioritas perbaikan & target implementasi lanjutan untuk Laptop B di [[laptop B Patch 1 planning]], mencakup: (1) *improve* CV & kontrol agar lebih smooth, (2) rasio kamera dinamis/*selectable* + default/*last-selected* camera, (3) animasi UI lebih smooth, (4) uji Play Mode webcam nyata di `MainIsland_Gameplay.unity`, (5) Sesi 4 (`VillageZoneController.cs`, VFX, & sync `PF_QuestNarrativeBox`/`PF_VillageChallengeOverlay`/Waypoint/Pose 15), dan (6) Sesi 5 (`GameManager.cs`, `AudioManager.cs`, sync HUD/Finish/Missions/Audio, & build `Fanamir_The_Sky_Guardian.exe` + `start_game.bat`).

## 2026-11-15 — Desain UI `v1.8`: Alur Boot `Splash Screen (SCREEN 09)` $\rightarrow$ `Start Screen (SCREEN 10 · Press Any Button)` $\rightarrow$ `Lobby`
- **Keputusan Desain & Alur (`Splash -> Start Screen -> Lobby`):**
  - Menambahkan dua fase layar pembuka sebelum masuk ke Lobby `MainMenu_Lobby` (`SCREEN 00`), selaras penuh dengan gaya *Fantasy Parchment-and-Ink Tabletop Card UI* dan merakitnya **100% dari komponen FigCraft yang sudah diekspor (`140` PNG eksisting — 0 komponen PNG baru dibutuhkan)**:
    1. **`SCREEN 09 - Splash Screen`** ([`09_SplashScreen.png`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/Designs/09_SplashScreen.png), `1920×1080`, `54,750` B): Latar kanvas perkamen krem solid `#F2EFE7`, bingkai arsitektural stepped penuh `UI_Panel_Parchment_Full_9Slice`, mahkota `UI_Crest_Baroque_Scrollwork`, sub-plakat `UI_Plaque_SubParchment_9Slice`, tag atas `ANCIENT DEW DRAGON` (`UI_Badge_Label_Amber_Blank`), judul hero **`FANAMIR`**, banner stepped **`THE SKY GUARDIAN`** (`UI_Banner_Stepped_Modal_Blank` 3-Slice H terkunci `77 px`), dan footer `WEBCAM EXERGAMING · BODY-FLIGHT ADVENTURE`. Tampil singkat (~`2.5s` fade-in/hold/fade-out; skippable ke Start Screen setelah `0.5s`).
    2. **`SCREEN 10 - Start Screen`** ([`10_StartScreen.png`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/Designs/10_StartScreen.png), `1920×1080`, `1,349,407` B): Berbagi latar `UI_Bg_Lobby_AerialIsland` dengan Lobby untuk transisi mulus tanpa jeda loading; menampilkan tombol pojok kanan atas **`ESC · QUIT`** (`UI_Btn_TopChrome_Cream_9Slice` + `UI_IconBadge_CloseX_20`), gugus plakat judul hero di langit atas (`UI_Crest_Baroque_Scrollwork`, `UI_Panel_Parchment_Full_9Slice`, `UI_Tag_Stage_Gold_Blank` `"SKY EXERGAMING AR"`, **`FANAMIR`**, dan `UI_Banner_Stepped_Modal_Blank` `"THE SKY GUARDIAN"`) agar pemandangan kepulauan di tengah layar terbuka lebar, serta kapsul **`PRESS ANY BUTTON TO START`** (`UI_HUD_Capsule_Parchment_9Slice` + 2× `UI_Diamond_Fleuron_18`) di bagian bawah-tengah yang berdenyut halus dan menerima tombol/klik apa saja menuju Lobby (`SkipBootScreensOnce` dilewati saat kembali dari gameplay).
- **Catatan Sinkronisasi & Dokumentasi:**
  - Karena MCP server `figcraft` belum terhubung kembali di sesi aktif pasca *server restart*, `09_SplashScreen.png` dan `10_StartScreen.png` dirakit langsung memakai komponen PNG resmi `UI_Unity_Export/Components/` dengan kalkulasi 9-slice / 3-slice H 1:1 dan font `Fredoka`; artboard sumber di [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) dapat ditambahkan belakangan secara opsional tanpa memengaruhi implementasi Laptop B.
  - Memperbarui [[00 - Overview]] (`11` desain referensi), [[rencana_desain_ui]] (`v1.8`, bagian `2.0`), [[panduan_aset_ui_unity]] (`SCREEN 09–10` & `BootFlowController.cs`), [[kontrak_integrasi_ui_dan_progress]] (**§6 Prompt Implementasi `v1.8` Laptop B**), dan [[Tasks]].

## 2026-11-15 — Pembaruan UI `v1.7`: Branding `Fanamir: The Sky Guardian`, Quest Objective Panel (`SCREEN 02`), Quest Narrative Auto-Play (`SCREEN 08`), & Slider `Text Auto-Next` (`SCREEN 05`)
- **Perubahan di [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) (`1,306,400` B):**
  - **1. Pembaruan Identitas & Branding Publik:** Mengganti seluruh kemunculan teks `AR DRAGON · SKY GUARDIAN` / `Dragon` (di `Design` `SCREEN 00, 01, 05, 06, 07`, `Wireframe`, serta header/catatan `03 - Unity Export Kit`) ke **`FANAMIR: THE SKY GUARDIAN`** & **`Fanamir`** (termasuk `LIVE FANAMIR PREVIEW · GLIDE (T-POSE)` di Calibration dan deskripsi pose `#01–#03`). Nama aset teknis (`UI_Illust_Dragon_WaterBreath`) & namespace `DragonWingAR.*` tetap dijaga.
  - **2. Quest Objective Panel (`SCREEN 02 - In-Game Flight HUD`, `id: 348`):** Menambahkan panel *Stepped Parchment* (`60, 40`, `424×112`, jeda aman `128 px` di atas badge `33%` Vitality Bar dan `286 px` sebelum kapsul Timer tengah) dengan medali gulungan emas & pill **`QUEST OBJECTIVE`**:
    - State spawn (`TRAVEL`): *"Fly to the marked village (Smog Valley)"* + waypoint disinkronkan ke `SMOG VALLEY · 140m`.
    - State pasca-narasi (`ACTIVE TASK`, hijau `#5C8D46`): *"Disperse the smog — Flap your wings!"*.
  - **3. Quest Narrative Text Box Auto-Play (`SCREEN 08 - Quest Narrative & Objective`, `id: 2114` baru di `(4200, 2400)`):** Artboard overlay khusus saat **Fanamir** masuk area desa dan memainkan *Idle Fly* (`POSE: IDLE FLY`), menempatkan **Quest Narrative Box (`156, 828`, `1236×212`)** di area bawah-kiri/tengah dengan jarak bersih `18–36 px` dari Vitality Bar kiri (`x=80..120, y=330..810`) dan `48 px` sebelum klaster PIP/Telemetri kanan bawah (`x >= 1440`). **Tanpa tombol Continue** agar pemain tetap *hands-free* melakukan pose di webcam: berisi tag pembicara `SMOG VALLEY · ELDER ROWAN`, pill **`LINE 1 / 3 · AUTO-PLAY`**, emblem biome kiri, narasi `2–3` baris, strip `WHAT TO DO:`, serta panel kanan **`AUTO-PLAY · HANDS-FREE`** dengan countdown `NEXT IN 3.2s / 5.0s`, progress bar horizontal hijau, dan keterangan *"Auto-updates Quest Objective · Speed adjustable in Settings"*.
  - **4. Pengaturan `Text Auto-Next` di `SCREEN 05` (`Settings & Audio Modal`, `id: 400`):** Memadatkan 4 baris slider (`70 px -> 58 px`, step `65 px`) dan menambahkan slider ke-5 **`Text Auto-Next`** (`5.0s`, ikon jam timer `#E5A93B`, warna track amber `#E0A23B`) di atas `Camera Input & AI YOLO Module` agar kecepatan auto-play text box dapat diatur pemain di Settings nanti.
- **Ekspor Modular & Verifikasi Aset (`UI_Unity_Export/`):**
  - Menambahkan **`6` komponen *textless* baru (`clip: false`)** pada `03 - Unity Export Kit` (total kini **`140` PNG** di `Components/` + **`9` PNG `1920×1080`** di `Designs/`):
    - `01_Panels/UI_Panel_QuestObjective_9Slice.png` (`240×135`, `2,018` B, Border Unity `24, 24, 24, 29`)
    - `01_Panels/UI_Panel_NarrativeBox_9Slice.png` (`304×200`, `2,287` B, Border Unity `30, 30, 30, 36`)
    - `02_Banners/UI_Banner_SpeakerTag_Blank.png` (`240×42`, `483` B, Horizontal 3-Slice `8, 0, 8, 0`)
    - `06_Icons/UI_Icon_QuestScroll_Gold.png` (`48×48`, `697` B)
    - `06_Icons/UI_Icon_AutoPlay_Timer.png` (`48×48`, `1,209` B)
    - `06_Icons/UI_Icon_PromptNext_Arrow.png` (`48×48`, `869` B, opsional)
  - Mengekspor ulang `Designs/` (`00, 01, 02, 05` [`1,031,973` B], `06, 07`) + baru [`08_QuestNarrative_Overlay.png`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/Designs/08_QuestNarrative_Overlay.png) (`160,102` B); memverifikasi seluruh `9` desain tepat `1920×1080` & memeriksa render screenshot `SCREEN 00, 01, 02, 05, 07, 08` serta section Export Kit `1263, 1264, 1268`.
  - Memperbarui [[00 - Overview]], [[rencana_desain_ui]] (`v1.7`), [[panduan_aset_ui_unity]], [[kontrak_integrasi_ui_dan_progress]] (menambahkan **§5 Prompt Penyesuaian `v1.7`** untuk Laptop B), & [[Tasks]].

## 2026-11-15 — Revisi UI Kalibrasi (`SCREEN 01` Max 4 Kartu Dinamis + Panel Diperbesar) & Modal `SCREEN 07` Pose Guide (`v1.6.1`)
- **Perubahan Desain di [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) (`1,175,475` B, Page `Design` kini `8` artboard):**
  - **`SCREEN 01 - Calibration & Motion Tutorial - Design` (`id: 314`, `1920×1080`):**
    - Memperbesar `Left Panel` & `Right Panel` dari `860×404` ke **`860×540 px`** sehingga viewport cermin skeleton (`~380 px` tinggi) dan `RenderTexture` Live 3D Dragon Preview (`~438 px` tinggi) jauh lebih lega dan mudah diamati saat kalibrasi.
    - Mengganti grid `3×5` lama dengan `Pose History Strip` (`RECENT POSES · MAX 4`, `1372×50`), tombol **`OPEN POSE GUIDE`** + pill emas **`15 POSES`** (`368×54`), dan **`1×4` baris kartu pose dinamis (`422×166 px`, gap `24 px`)** yang menampilkan **maksimal 4 mini card**. Default saat awal permainan menampilkan **4 pose utama** (`#08 Glide`, `#06 Flap To Climb`, `#04 Takeoff`, `#13 Tuck To Dive`), lalu Slot 1–4 berotasi dinamis (*MRU anti-duplikat*) menampilkan pose terakhir yang dilakukan pemain.
  - **`SCREEN 07 - Pose Guide Overlay Modal - Design` (`id: 1893`, `1920×1080`):**
    - Artboard modal overlay baru (`#1B1815` scrim `0.58`, kontainer perkamen `1824×824`, banner `POSE GUIDE · ALL 15 POSES`, strip legenda 4 kategori gerak, grid **`3×5` seluruh `15` pose (`340×154 px`)** lengkap dengan deskripsi fungsi + badge `MAIN` pada `#04/#06/#08/#13`, strip `KEY POSE RULES`, tombol `ESC · CLOSE`, & `CLOSE POSE GUIDE`).
- **Audit Dampak Aset Ekspor & Dokumentasi:**
  - **`UI_Unity_Export/Components/` (`134` PNG):** **0 perubahan / 0 tambahan aset** — seluruh elemen `SCREEN 01` revisi dan `SCREEN 07` terakit penuh memakai sprite komponen yang sudah diekspor sebelumnya di Laptop B.
  - **`UI_Unity_Export/Designs/` (`8` PNG `1920×1080`):** Menimpa [`01_Calibration_MotionTutorial.png`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/Designs/01_Calibration_MotionTutorial.png) (`669,757` B) dan mengekspor baru [`07_PoseGuide_OverlayModal.png`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/Designs/07_PoseGuide_OverlayModal.png) (`561,510` B).
  - Memperbarui [[00 - Overview]], [[rencana_desain_ui]], [[panduan_aset_ui_unity]], dan menambahkan **Section 4 (`Delta Patch Prompt v1.6.1`)** pada [[kontrak_integrasi_ui_dan_progress]] yang disesuaikan langsung dengan implementasi Unity Laptop B (`CalibrationPanelUI.cs`, `UISceneAndPrefabBuilder.cs`, `LobbyNavigationManager.cs`, dan komponen baru `PoseGuideOverlayUI.cs`).

## 2026-11-15 — Hapus Naga Gunung Central, Variasi Tinggi Pohon `3.0–10.0 m`, & Pohon Hangus Tanpa Daun Wildfire (`11–12`) ([`pohon.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/pohon.blend), [`Pulau_Archipelago.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/Pulau_Archipelago.blend))
- **Penghapusan Model Naga dari Adegan Kepulauan:**
  - Sebelum dihapus, versi bernaga dicadangkan ke `scratch/backups/Pulau_Archipelago_with_dragon.blend` (`2,629,052` B).
  - Menghapus bersih `ARM_Dragon`, `13` objek/mesh `SM_Dragon_*`, `12` material `M_Dragon_*`, data armature, serta seluruh koleksi `COL_Dragon*` dari [`Pulau_Archipelago.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/Pulau_Archipelago.blend) (puncak gunung Central kini bersih karena naga dimuat sebagai objek pemain terpisah di runtime Unity).
- **Ekspansi Tinggi Kanonik & 2 Varian Pohon Hangus Tanpa Daun di [`assets/pohon.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/pohon.blend) (`239,285` B, `12` varian):**
  - Tinggi kanonik seluruh varian `01–10` disesuaikan agar perpustakaan aset mencakup spektrum tinggi alami (`1:1`: `3.81 m` hingga `10.00 m`).
  - Menambahkan `SM_Pohon_11_BurntLeafless` (`200` verts, `344` tris, `1:1` `[3.6681, 2.9611, 5.1559]` m / `1:6` `[0.6113, 0.4935, 0.8593]` m): ranting & dahan hangus gundul hitam-arang tanpa daun (`M_Pohon_11_BurntLeafless`).
  - Menambahkan `SM_Pohon_12_BurntCharredSpire` (`200` verts, `344` tris, `1:1` `[2.3954, 2.2756, 9.694]` m / `1:6` `[0.3992, 0.3793, 1.6157]` m): batang pinus hangus tinggi menjulang dengan puntung dahan arang tanpa jarum daun (`M_Pohon_12_BurntCharredSpire`).
  - Me-bake `6` map PBR baru ke `assets/upgrade model pulau/textures/`: `T_Pohon_11_BC.png` (`836,466` B), `T_Pohon_11_R.png` (`130,563` B), `T_Pohon_11_N.png` (`208,609` B), `T_Pohon_12_BC.png` (`877,186` B), `T_Pohon_12_R.png` (`131,531` B), dan `T_Pohon_12_N.png` (`219,735` B).
- **Sebaran Variasi Tinggi & Hutan *Wildfire* di [`Pulau_Archipelago.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/Pulau_Archipelago.blend) (`2,077,219` B, `352` objek, `316` pohon):**
  - Menggunakan `15` *tier* tinggi pohon bertahap tiap **`0.5 m`** dari **`3.0 m` sampai `10.0 m`** (`scale ∈ [0.62, 1.38]` dengan radius tabrakan terskala). Seluruh `15` bin terisi merata (`3.0m: 16`, `3.5m: 12`, `4.0m: 26`, `4.5m: 20`, `5.0m: 10`, `5.5m: 26`, `6.0m: 23`, `6.5m: 22`, `7.0m: 24`, `7.5m: 26`, `8.0m: 24`, `8.5m: 22`, `9.0m: 23`, `9.5m: 22`, `10.0m: 20`), dengan tinggi *bounding-box* `1:1` terukur `2.9998–10.0003 m`.
  - Total populasi pohon menjadi **`316` unit** (`B2: 210`, `B3: 48`, `B1: 40`, `CENTER: 18`): di area hutan terbakar **Biome 2 (*Wildfire*, `(133.0, 24.0)`)** ditanam **`125` pohon** campuran **`26` pohon hangus tanpa daun** (`15` `SM_Pohon_11_BurntLeafless` + `11` `SM_Pohon_12_BurntCharredSpire`), **`33` pohon setengah gosong** (`18` `HalfBurntPine` + `15` `HalfBurntOak`), dan pohon hijau sehat (`01–05`); seluruh `48` pohon **Biome 3 (*Drought*)** tetap kering (`15` `DeadDry`, `14` `DroughtScrub`, `19` `DryPine`); `27` rumah desa tetap utuh (`B1: 9`, `B2: 9`, `B3: 9`).
- **Verifikasi QA, Audit, Grounding, & Visual:**
  - Pemeriksaan data Blender memastikan `dragon_hits` kosong total (`objects/meshes/armatures/materials/collections/actions = []`) dan seluruh `VIEW_3D` bershading `MATERIAL`.
  - `qa_check.py`: Ke-`17` templat kanonik (`SM_Rumah_01..05` & `SM_Pohon_01..12`) lulus **`pass=True` (`errors=[]`, `warnings=[]`, `ground_z=0.0`)**; terrain + air + 4 grup batu lulus `pass=True` (`errors=[]`).
  - `scene_audit.py`: `ok=True`, `objects=352`, `missing=[]`, `packed=0`, `absolute_paths=[]`, `orphans=0`, `linked_libraries=[]`.
  - *Grounding BVH*: `COL_Village_Houses` (`27` unit, `missing_hits=0`, `dz ∈ [-0.0151, -0.015] m`), `COL_Foliage_Trees` (`316` unit, `missing_hits=0`, `dz ∈ [-0.1963, -0.0158] m`).
  - Inspeksi gambar pratinjau lulus pada [`island_final_topdown.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_topdown.png), [`island_final_perspective.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_perspective.png), [`island_final_village_b1.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_village_b1.png), [`island_final_village_b2_forest.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_village_b2_forest.png), dan [`island_final_village_b3.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_village_b3.png).

## 2026-11-15 — Penetapan Identitas & Judul Resmi Game: `Fanamir: The Sky Guardian` & Planning UI Quest Objective + Narrative Text Box
- **Keputusan Desain Identitas:** Mengubah judul publik game dari *AR Dragon: Sky Guardian* menjadi **`Fanamir: The Sky Guardian`**, dengan **`Fanamir`** sebagai nama resmi karakter naga (*Ancient Dew Water Dragon*). Namespace internal C# `DragonWingAR.*` dan struktur folder proyek tetap dipertahankan agar tidak memicu regresi kode di Laptop B.
- **Keputusan Desain Alur Quest & UI In-Game (`SCREEN 02`):**
  1. **Quest Objective (di atas Progress Bar):** Muncul di atas *Global Vitality Progress Bar* sisi kiri layar segera setelah game load & pemain spawn. Dimulai dengan objective awal mengarahkan pemain menuju desa yang memiliki penanda (*mark*).
  2. **Transisi Masuk Area Desa (*Idle Fly* + Narrative Text Box):** Saat **Fanamir** memasuki area desa, naga memainkan animasi **Idle Fly** (terbang di tempat) di atas desa, lalu muncul **Text Box Narasi Quest/Misi** yang menjelaskan masalah desa tersebut dan apa yang harus dilakukan pemain.
  3. **Update Objective:** Setelah narasi text box selesai, **Quest Objective** di atas progress bar diperbarui sesuai tugas pemulihan desa yang harus diselesaikan (detail mekanisme tantangan lanjutan akan dibahas setelah pembaruan UI selesai).
- **Tindak Lanjut:** Menyiapkan prompt khusus Sesi Desain UI (FigCraft) untuk memperbarui branding judul, merancang komponen Quest Objective + Narrative Text Box di `UI AR Dragon.figcraft`, dan mengekspor asetnya ke `UI_Unity_Export/`.

## 2026-11-14 — Varian Pohon Kering Biome Drought (`06–08`) & Sebagian Gosong Biome Wildfire (`09–10`) ([`pohon.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/pohon.blend), [`Pulau_Archipelago.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/Pulau_Archipelago.blend))
- **Penambahan 5 Varian Pohon Baru ke [`assets/pohon.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/pohon.blend) (`221,569` B, total `10` varian + `15` map PBR baru `T_Pohon_06..10_{BC,R,N}.png`):**
  - `SM_Pohon_06_DeadDry` (`234` verts, `408` tris, `1:1` `[4.3476, 3.579, 7.3755]` m / `1:6` `[0.7246, 0.5965, 1.2292]` m): pohon mati tanpa daun berbatang dan ranting kering lapuk kecokelatan (`M_Pohon_06_DeadDry`).
  - `SM_Pohon_07_DroughtScrub` (`270` verts, `488` tris, `1:1` `[7.1771, 5.579, 6.2141]` m / `1:6` `[1.1962, 0.9298, 1.0357]` m): pohon savana kering bercabang melebar dengan tajuk pipih jarang berwarna jerami/oker kering (`M_Pohon_07_DroughtScrub`).
  - `SM_Pohon_08_DryPine` (`148` verts, `256` tris, `1:1` `[2.8513, 2.9296, 7.5562]` m / `1:6` `[0.4752, 0.4883, 1.2594]` m): cemara kering berbatang tinggi dengan ranting bawah gundul & tajuk kerucut kering kecokelatan (`M_Pohon_08_DryPine`).
  - `SM_Pohon_09_HalfBurntPine` (`176` verts, `304` tris, `1:1` `[3.3566, 3.3168, 8.1056]` m / `1:6` `[0.5594, 0.5528, 1.3509]` m): cemara **sebagian gosong** berbatang & puntung dahan arang hitam, 2 tingkat tajuk bawah terbakar cokelat-jelaga, & puncak tajuk hijau kusam yang selamat (`M_Pohon_09_HalfBurntPine`).
  - `SM_Pohon_10_HalfBurntOak` (`382` verts, `696` tris, `1:1` `[5.8205, 5.4436, 8.5346]` m / `1:6` `[0.9701, 0.9073, 1.4224]` m): pohon ek **sebagian gosong** dengan batang arang, dahan kiri gundul hangus, tajuk tengah hangus kecokelatan, & separuh tajuk kanan masih hijau (`M_Pohon_10_HalfBurntOak`).
- **Distribusi Biome Baru di [`Pulau_Archipelago.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/Pulau_Archipelago.blend) (`2,629,052` B, `352` objek, `301` pohon total):**
  - **Biome 3 (*Drought*):** Seluruh **`48` pohon** diganti menjadi varian pohon kering (`17` `SM_Pohon_06_DeadDry`, `18` `SM_Pohon_07_DroughtScrub`, `13` `SM_Pohon_08_DryPine`), termasuk `18` pohon di sekeliling cekungan danau kering & pinggiran desa serta `30` di dataran/perbukitan kering lainnya (`0` pohon hijau di Drought).
  - **Biome 2 (*Wildfire*, total `195` pohon):** Dari **`110` pohon** pada area hutan terbakar `(133.0, 24.0)`, hanya **sebagian saja (`49` pohon = `30` `HalfBurntPine` + `19` `HalfBurntOak`)** yang menggunakan model sebagian gosong (terkonsentrasi di inti area *burned* `wf_burn`), sementara sisa area hutan terbakar & `85` pohon sekitarnya (`146` pohon B2 lainnya), `40` pohon B1, dan `18` pohon Center tetap hijau sehat (`Pohon_01..05`).
- **Verifikasi QA & Visual:**
  - `qa_check.py`: Seluruh `10` varian pohon di `pohon.blend` dan seluruh `15` templat (`SM_Rumah_01..05`, `SM_Pohon_01..10`) di `Pulau_Archipelago.blend` lulus `pass=True` (`errors=[]`, `warnings=[]`, `uv_overlap_pct=0.0%`); terrain + air + 4 grup batu lulus `errors=[]`.
  - `scene_audit.py`: `ok=True` (`missing=[]`, `packed=0`, `absolute_paths=[]`, `orphans=0`).
  - *Grounding BVH*: `COL_Village_Houses` (`27` unit, `dz ∈ [-0.0151, -0.0150] m`), `COL_Foliage_Trees` (`301` unit, `dz ∈ [-0.2027, -0.0250] m`).
  - Inspeksi visual lulus pada [`verify_pohon_new_variants.png`](file:///C:/Users/nmast/.gemini/antigravity/brain/217c256d-2f17-4542-8616-2c9cde2ddab0/scratch/verify_pohon_new_variants.png), [`island_final_village_b2_forest.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_village_b2_forest.png), [`island_final_village_b3.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_village_b3.png), [`island_final_topdown.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_topdown.png), [`island_final_perspective.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_perspective.png), serta live Blender `look` pada cluster Wildfire & Drought.

## 2026-11-14 — Penataan Hutan Wildfire & Village Tengah + Relokasi Danau Kering & Tampilan Lebih Kering Biome Drought ([`Pulau_Archipelago.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/Pulau_Archipelago.blend))
- **Backup Sebelum Rework:** Disimpan di `scratch/backups/Pulau_Archipelago_pre_wildfire_drought_fix.blend`.
- **Biome 2 (*Wildfire* — Hutan Event Kebakaran + Relokasi Village ke Tengah):**
  - Memindahkan pusat **Village Biome 2 (`9` rumah, `COL_Village_Houses`)** dari sudut selatan `(112.0, -26.0)` ke tengah dataran Biome 2 di **`(123.0, 1.0)`**, tepat di sebelah barat daya area tanah *burned* `(133.0, 24.0)` (`d_desa` & `soil_vil` diperbarui di terrain).
  - Membuka akses penanaman di tanah *burned* (`is_burn_ground` di `add_houses_and_trees.py`) serta memindahkan `SM_Rocks_Biome2_Wildfire` keluar dari tengah area *burned* ke pinggiran hutan (`(113, 26)`, `(146, 39)`, `(102, 10)`, `(88, 48)`).
  - Menanam **`111` pohon hutan rapat** langsung di atas area *burned* + rim (`wf_burn`), ditambah pepohonan di bukit sekitarnya sehingga Biome 2 kini memiliki **`195` pohon** (total keseluruhan pulau menjadi **`273` pohon** `1:6` dari 5 varian `SM_Pohon_*` + **`27` rumah** dari 5 varian `SM_Rumah_*`).
- **Biome 3 (*Drought* — Relokasi Danau Kering ke Samping Village + Visual Lebih Kering):**
  - Memindahkan kedua cekungan danau kering (`dry_basins`) dari sisi tenggara jauh `(36, -132)` / `(52, -114)` ke tepat di samping timur **Village Biome 3** `(6.0, -108.0)`, menjadi **`(27.5, -116.0)`** (`rx=11.2, ry=8.2`) dan **`(41.5, -106.0)`** (`rx=8.4, ry=6.4`) lengkap dengan apron tanah kering yang menyatu langsung dengan tepi timur desa.
  - Menambahkan atribut loop **`Col_DroughtArid`** (`drought_arid` & `drought_rock_dry`, dinaikkan dari tepi pantai `dc_isl = 0..3.2 m` agar menyatu langsung ke bibir tanah pantai tanpa meninggalkan cincin tepi hijau) ke `SM_Archipelago_Terrain` serta memadukan palet rumput savana kering (`#6E6B2A / #9A8D3C / #B9A754 / #997B4C`), batu/lumut mesa hangat (`#9B8669 / #6E7033`), dan cekungan tanah pecah (`#C8AF82 / #654A2B`) lalu me-bake ulang `T_Archipelago_BC` (`8192×8192`) dan `T_Archipelago_R/N` (`4096×4096`).
- **Verifikasi Akhir (`2,540,524` B, `324` objek total):** Terrain (`24,190` verts / `48,376` tris, `0` boundary/non-manifold, `0.0%` UV overlap), air (`176` tris), dan 4 grup batu lulus `qa_check.py` bersama terrain (`pass=True`, `errors=[]`, `floating=[]`); ke-10 templat `SM_Rumah_01..05` dan `SM_Pohon_01..05` lulus `qa_check.py` (`pass=True`, `errors=[]`, `warnings=[]`); `scene_audit.py` `ok=True` (`missing=[]`, `orphans=0`); *grounding* BVH rumah `dz ∈ [-0.0151, -0.0150] m` & pohon `dz ∈ [-0.2005, -0.0250] m`; seluruh render [`island_final_topdown.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_topdown.png), [`island_final_perspective.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_perspective.png), [`island_final_village_b2_forest.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_village_b2_forest.png), [`island_final_village_b3.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_village_b3.png) dan tampilan Material live Blender (`look`) telah diperiksa.

## 2026-11-14 — Kontrak Integrasi UI Hybrid (`Live + Placeholder + Dormant`), Aturan Sinkronisasi Progress, & Prompt Implementasi Laptop B ([[kontrak_integrasi_ui_dan_progress]])
- **Sinkronisasi Konteks Nyata Laptop B:** Membaca `brain/laptop-b/{Tasks,Devlog,progress,konteks,prd}.md` untuk menyelaraskan implementasi UI dengan kondisi testing mekanisme aktif di `Test_Dragon_Flight.unity` (`PythonProcessManager.cs`, `UDPPoseReceiver.cs` `5052/5053/5054`, `DragonFlightController.cs`, `DefaultFlightInputMapper.cs`, `DragonWingAnimator.cs`).
- **Arsitektur 3-Kategori UI ([[kontrak_integrasi_ui_dan_progress]]):**
  - **Live Wired:** Navbar Lobby (`MainMenu_Lobby.unity`), Stage 01 `PLAY` & Calibration `READY TO FLY`, Kontrol Kamera 5 State + Dropdown Solid Opaque `#FBF6EB`, Kalibrasi UDP `5054`, `RenderTexture` Live Dragon Preview, 14 kartu pose lokomosi, serta HUD Telemetri, PIP `5053`, Pause, & Slider `Turn Sensitivity`.
  - **Placeholder Fungsional:** Kartu `Stage 02/03`, Slider `BGM/SFX`, HUD atas timer & global vitality `0%`, serta kartu pose ke-15 (`VillageActions`).
  - **Dormant Prefabs (`SetActive(false)`):** `UI_HUD_Waypoint_Chevron`, `SCREEN 03 VillageChallengeUI`, dan `SCREEN 04 FinishModalUI` (tetap dirakit lengkap menjadi prefab + public API & `[ContextMenu]` preview Editor tanpa mengganggu gameplay testing).
- **Protokol Sinkronisasi Progress Masa Depan (`Feature-to-UI Sync`):** Mengunci aturan agar setiap penambahan pulau 3D, `VillageZoneController`/tantangan desa, `GameManager` timer/bintang, perubahan gestur, atau `AudioManager` di Laptop B langsung membuka/menyesuaikan slot placeholder & dormant UI terkait dan melaporkannya di `brain/laptop-b/`.

## 2026-11-14 — Ekspor Lengkap Aset UI Siap Unity 6 (`UI_Unity_Export/`: `134` Komponen PNG + `7` Artboard `1920×1080`) & [[panduan_aset_ui_unity]]
- **Folder Ekspor Proyek ([`UI_Unity_Export/`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/)):**
  - [`UI_Unity_Export/Components/`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/Components/): **`134` file PNG (`1x`, *textless*)** hasil ekspor modular dari Page `03 - Unity Export Kit` (`UI AR Dragon.figcraft`) ke dalam `20` subfolder berstruktur (`01_Panels/` s.d. `07_Biomes/`).
  - [`UI_Unity_Export/Designs/`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/Designs/): **`7` file PNG layar penuh `1920×1080`** hasil ekspor Page `Design` (`00_MainMenu_LevelSelection.png` s.d. `06_CameraControl_5VisualStates.png`) sebagai cetak biru visual perakitan UI.
- **Audit Dimensi Piksel & Panduan Implementasi Unity 6 ([[panduan_aset_ui_unity]]):**
  - Menghitung dimensi aktual, margin transparan ekspor `clip: false` (`0–14 px` + *hard cast shadow*), dan batas aman *9-Slice* berurutan **Unity Sprite Editor Inspector (`L, T, R, B`)** *(serta mengingatkan perbedaan urutan C# `Sprite.border` `(L, B, R, T)`)* untuk seluruh aset `Sliced`, horizontal *3-slice* (`T=0, B=0` pada banner stepped & speech bubble), dan `Filled` (`Vitality Fill`, progress bar biru).
  - Mencatat pengecualian ukuran aktual vs nama layer (`UI_HUD_Vitality_Track_40x480` = `40×484`, `UI_Box_Locked_Center_96x80` = `96×84`, `UI_StarBox_Card_Slot_80x76` = `80×80`, `UI_StarBox_Victory_Slot_100x90` = `100×94`, `UI_Pose_14_PropellerDash` = `66×64`), batasan tinggi tombol/box bertakik kiri `=`, penggunaan viewport berlubang `alpha=0` di atas `RawImage`/`RenderTexture`, aturan `#FFFFFF alpha = 1.0f` pada *Linear Color Space*, serta pemetaan komponen & *TextMeshProUGUI* ke skrip `DragonWingAR.*`.

## 2026-11-14 — Ekspansi 5 Variasi Rumah & 5 Variasi Pohon + Baking Tekstur PBR ([`rumah.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/rumah.blend), [`pohon.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/pohon.blend), [`Pulau_Archipelago.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/Pulau_Archipelago.blend))
- **Backup Aset Awal:** Salinan mentah `rumah_original.blend` dan `pohon_original.blend` diamankan di `scratch/backups/`.
- **Ekspansi & Perbaikan Rumah ([`assets/rumah.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/rumah.blend), `154,917` B, 5 varian):**
  - `SM_Rumah_01_Cottage` (`242` verts, `195` faces, `368` tris, `1:1` `[5.2, 6.2, 5.85]` m / `1:6` `[0.8667, 1.0333, 0.975]` m, material `M_Rumah_01_Cottage`).
  - `SM_Rumah_02_TwinWindow` (`370` verts, `291` faces, `560` tris, `1:1` `[5.1, 7.24, 5.89]` m / `1:6` `[0.85, 1.2067, 0.9817]` m, memperbaiki material dinding/atap yang tertukar dan jendela berlubang gelap, `M_Rumah_02_TwinWindow`).
  - `SM_Rumah_03_TimberGable` (`426` verts, `333` faces, `644` tris, `1:1` `[6.28, 6.6, 6.36]` m / `1:6` `[1.0467, 1.1, 1.06]` m, *half-timbered gable*, atap biru *slate*, dan kanopi teras depan, `M_Rumah_03_TimberGable`).
  - `SM_Rumah_04_LShape` (`412` verts, `328` faces, `632` tris, `1:1` `[7.23, 7.02, 6.37]` m / `1:6` `[1.205, 1.17, 1.0617]` m, denah huruf L 6-verteks menyatu + lis ek, `M_Rumah_04_LShape`).
  - `SM_Rumah_05_TwoStory` (`520` verts, `406` faces, `788` tris, `1:1` `[5.6, 6.2, 7.72]` m / `1:6` `[0.9333, 1.0333, 1.2867]` m, lantai atas menjorok dengan *corbel* dan perbaikan *dormer* depan, `M_Rumah_05_TwoStory`).
- **Ekspansi & Perbaikan Pohon ([`assets/pohon.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/pohon.blend), `161,741` B, 5 varian):**
  - `SM_Pohon_01_Pine` (`106` verts, `142` faces, `184` tris, `1:1` `[3.4533, 3.4895, 5.8639]` m / `1:6` `[0.5755, 0.5816, 0.9773]` m, memperbaiki daun yang sebelumnya cokelat seluruhnya, `M_Pohon_01_Pine`).
  - `SM_Pohon_02_Oak` (`326` verts, `550` faces, `600` tris, `1:1` `[6.7857, 6.1797, 9.2167]` m / `1:6` `[1.1309, 1.03, 1.5361]` m, `M_Pohon_02_Oak`).
  - `SM_Pohon_03_Cedar` (`142` verts, `190` faces, `248` tris, `1:1` `[3.7417, 3.9222, 9.2069]` m / `1:6` `[0.6236, 0.6537, 1.5345]` m, `M_Pohon_03_Cedar`).
  - `SM_Pohon_04_MeadowOak` (`382` verts, `648` faces, `704` tris, `1:1` `[7.0411, 6.1293, 7.6405]` m / `1:6` `[1.1735, 1.0216, 1.2734]` m, `M_Pohon_04_MeadowOak`).
  - `SM_Pohon_05_Birch` (`232` verts, `404` faces, `432` tris, `1:1` `[4.8207, 4.2932, 8.6982]` m / `1:6` `[0.8035, 0.7155, 1.4497]` m, batang kembar putih pucat berbercak lentisel, `M_Pohon_05_Birch`).
- **Baking 30 Map Tekstur PBR (`T_Rumah_01..05_{BC,R,N}.png` & `T_Pohon_01..05_{BC,R,N}.png`):** Disimpan di `assets/upgrade model pulau/textures/` (`1024×1024` Base Color `sRGB` + `512×512` Roughness & Normal `Non-Color`), diverifikasi bebas overlap dan di luar batas UV (`uv_oob_pct: 0.0`, `uv_zero_area_faces: 0`, `uv_overlap_pct: 0.0`).
- **Integrasi & QA Scene Utama ([`Pulau_Archipelago.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/Pulau_Archipelago.blend), `2,455,290` B):**
  - Penempatan skala `1:6` dengan `BVHTree` grounding: **`27` rumah** di `COL_Village_Houses` (`B1: 9`, `B2: 9`, `B3: 9`; `SM_Rumah_01_Cottage: 9`, `SM_Rumah_02_TwinWindow: 6`, `SM_Rumah_03_TimberGable: 6`, `SM_Rumah_04_LShape: 3`, `SM_Rumah_05_TwoStory: 3`; `min_dz = max_dz = -0.015 m`) dan **`156` pohon** di `COL_Foliage_Trees` (`B2: 70`, `B1: 40`, `B3: 28`, `CENTER: 18`; `SM_Pohon_01_Pine: 31`, `SM_Pohon_02_Oak: 29`, `SM_Pohon_03_Cedar: 31`, `SM_Pohon_04_MeadowOak: 25`, `SM_Pohon_05_Birch: 40`; `min_dz = -0.1999 m`, `max_dz = -0.025 m`).
  - Seluruh 10 varian lulus `qa_check` (`pass: true`, `errors: []`, `warnings: []`, `ngons: 0`, `boundary_edges: 0`, `nonmanifold_edges: 0`, `duplicate_verts: 0`, `inside_out_islands: 0`) dan `scene_audit` lulus (`ok: true`, `objects: 206`, `missing: []`, `orphans: 0`). Render pratinjau tersimpan di `assets/upgrade model pulau/previews/island_final_*.png`.

## 2026-11-14 — Penggantian Background Lobi Kepulauan Baru ([`BG_Island_Aerial_View.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/BG_Island_Aerial_View.jpg) & [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft))
- **Pembaruan Aset:** Mengganti background lama (*Aerial Island View* sebelumnya) dengan ilustrasi baru **kepulauan kaldera 4 pulau** (`1024×571`, `99,297` B) di [`Referensi UI/BG_Island_Aerial_View.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/BG_Island_Aerial_View.jpg).
- **Cakupan di [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) (`1,167,234` B):** Mengganti kelima instance `BG Aerial Island View` via `image.replace` (`scale_mode: fill`, `1920×1080`) di artboard `Main Menu` (`667`), `Calibration` (`668`), `Settings` (`669`), `Camera Control 5 States` (`877`) pada page `Design`, serta aset ekspor `07_Backgrounds/UI_Bg_Lobby_AerialIsland` (`1813`) pada page `03 - Unity Export Kit`. Diverifikasi lewat screenshot seluruh 5 node + uji ekspor `UI_Bg_Lobby_AerialIsland.png`.

## 2026-11-14 — Perbaikan Kliping Tag `STAGE 01–03` pada Page `Design` ([`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft))
- **Masalah:** Pada artboard `Main Menu & Level Selection - Design (1920x1080)` (`id: 263`), kontainer kartu `Card 01 - Smog Valley (Active)` (`274`), `Card 02 - Wildfire (Unlocked)` (`289`), dan `Card 03 - Drought (Locked)` (`304`) memakai `clip: true`, sehingga `Ribbon 01/02/03` (`STAGE 01–03`, `y = -18 px`) terpotong bagian atasnya di `y = 0` (dan *drop shadow* bawah `7 px` kartu ikut terpotong).
- **Perbaikan & Verifikasi:** Menyetel `clip: false` pada node `274`, `289`, dan `304` (`UI AR Dragon.figcraft` tersimpan `1,067,670` B). Hasil screenshot `263` dan `[275, 290, 305]` terverifikasi menampilkan ketiga tag `STAGE 01–03` beserta outline, *sheen* atas, teks terpusat, serta bayangan bawah kartu secara utuh.

## 2026-11-14 — Pembuatan Page `03 - Unity Export Kit` (Pemisahan `134` Aset UI Siap Ekspor ke Unity 6, [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft))
- **Tujuan:** Menyiapkan seluruh komponen antarmuka `Design` (`v1.6`) dalam bentuk potongan (*sliced*) modular, bersih dari teks *hardcoded* (diganti `TextMeshProUGUI` di runtime), bertag ekspor `PNG 1x` (`<Folder>/<AssetName>`), siap diekspor massal via FigCraft `file.exportLayers` untuk diimplementasikan ke **Unity 6 (`uGUI` / `CanvasScaler 1920×1080`)** tanpa mengubah isi page `Wireframe` (`id: 1`, `7` node) maupun `Design` (`id: 262`, `7` node).
- **Struktur 8 Section di Page `03 - Unity Export Kit` (`id: 1260`, lebar `4000 px`, tersimpan `1,067,674` B):**
  - **Sec 01 (`1263`, `11` aset — `01_Panels/`):** Panel perkamen `Stepped Rabbeted`, plakat pose/stat, baris taupe borderless, kotak terkunci charcoal, overlay popup solid opaque `#FBF6EB`, dan overlay scrim.
  - **Sec 02 (`1264`, `20` aset — `02_Ornaments/Banners/Tags/Callouts/Chips/`):** Ornamen *Baroque Scrollwork*, *Corner Fan*, *Diamond Fleuron*, *Socket Medallion*, 3 banner *stepped* blank + stamp merah defeat blank, 3 tag stage, 2 callout gelembung emas (dengan koin & polos), chip bar, dan header bar.
  - **Sec 03 (`1265`, `17` aset — `03_Buttons/Chrome/Navbar/`):** Tombol taktil 3D bevel, tombol mode kamera, top chrome `ESC`, ikon `CloseX`/`Pause`, rel navbar, serta tab default & active gold.
  - **Sec 04 (`1266`, `36` aset — `04_Controls/Dropdown/CameraStates/Viewports/`):** Slider track/fill/knob, komponen dropdown solid 100% opaque, 4 badge & 3 tombol status kamera, ikon status/spinner/alert, **viewport kalibrasi & bezel PIP berlubang (`center alpha = 0`)**, viewfinder brackets, serta panel gelap kalibrasi.
  - **Sec 05 (`1267`, `17` aset — `05_HUD/Challenge/Rewards/`):** Kapsul HUD, `Vitality_Track_40x480` + `Fill_34x474` + `Ticks_Overlay` terpisah, waypoint & check dot, ilustrasi `Dragon_WaterBreath` terisolasi (`269×170`), frame gelap, progress bar air biru, badge aktif blank, kotak bintang, dan ikon gembok.
  - **Sec 06 (`1268`, `25` aset — `06_Icons/Poses/`):** 9 ikon vektor UI + slot medali kosong `58×58` + **15 ikon pose kalibrasi (`01_GroundedIdle` – `15_VillageActions`, termasuk `08_Glide_ActiveGold`)** lengkap dengan label & catatan *binding* Unity `PoseCard`.
  - **Sec 07 (`1269`, `8` aset — `07_Biomes/Backgrounds/`):** 3 ilustrasi biome **`Raw`** (`380×210`), 3 **`Framed`** (`392×222`, **Biome 03 Drought bersih tanpa `Locked Scrim`**), `UI_Biome_Frame_Border_9Slice` berlubang (`392×222`), dan **`UI_Bg_Lobby_AerialIsland` (`1920×1080` `1:1`)**.
  - **Sec 08 (`1270`, `0` ekspor — Merah `DO NOT EXPORT`):** Referensi placeholder yang tidak boleh diekspor sebagai gambar statis (`Skeleton UDP 5053`, `Dragon 3D RenderTexture`, `Webcam PIP MJPEG`, panduan lapisan `TextMeshPro`, dan daftar eksklusi 5 item).
- **Verifikasi End-to-End:**
  - Inspeksi screenshot menyeluruh pada Page `1260` + ke-8 section individual (perbaikan lebar banner header `4000 px`, `Hdr Sub` resize vertikal, caption `S2`/`S6`, serta tinggi kotak catatan Sec 06).
  - Ekspor uji `file.exportLayers` ke scratch berhasil memproduksi **`134` file PNG** terorganisir otomatis sesuai subfolder dengan `skipped: 0`, terverifikasi kanal alpha berlubang (`center=(0,0,0,0)`) pada frame viewport/biome, `UI_Bg_Lobby_AerialIsland.png` `1920×1080`, dan bebas konten Section 08.
  - Panduan impor & katalog disinkronkan ke [[rencana_desain_ui]] (§5) dan [[Tasks]].

## 2026-11-14 — Pemasangan Model Rumah (`26` Unit) & Pohon (`128` Unit) Awal dari `assets/` ke Kepulauan (`Pulau_Archipelago.blend`)
- **Ekstraksi & Normalisasi Template (`assets/rumah.blend` & `assets/pohon.blend`, skala `1:6`):**
  - Memisahkan 4 koleksi tumpang-tindih bawaan (`'Rumah 1 jendela '`, `'Rumah 2 jendela'`, `'Pohon1'`, `'Pohon2'`) menjadi 4 data-block mesh kanonik ber-origin tepat di tengah alas bawah `Z = 0.0`, `scale = (1.0, 1.0, 1.0)` (diskalakan `WORLD_SCALE = 1/6` menyamai terrain `400 × 400 m` & naga `0.50 m`):
    - **`SM_Rumah_1Jendela`:** `0.8572 × 0.9664 × 0.9665 m` (`200` verts, `340` tris, 5 material).
    - **`SM_Rumah_2Jendela`:** `0.8402 × 1.1882 × 0.9649 m` (`204` verts, `348` tris, 6 material).
    - **`SM_Pohon_01`:** `0.5353 × 0.5353 × 0.9399 m` (`88` verts, `172` tris, 1 material terpakai).
    - **`SM_Pohon_02`:** `0.9974 × 1.1452 × 1.6078 m` (`450` verts, `800` tris, 2 material, menggabungkan relasi parent-child `Tree Low Poly` + `Icosphere.010`).
  - Memfilter keluar objek lampu bawaan (`Light`, `Spot`, `Point`), slot `None` tak terpakai, material duplikat, serta image eksternal yatim (`ImphenziaPalette01-256-Gradient.png`).
- **Penempatan Desa & Vegetasi Ground-Checked (`COL_Village_Houses` & `COL_Foliage_Trees`):**
  - Menggunakan `BVHTree` raycast ke `SM_Archipelago_Terrain` + sampel warna albedo baked `T_Archipelago_BC` (`8192×8192`) + jarak minimum ke 4 `SM_Rocks_*`, sehingga seluruh rumah & pohon bebas melayang maupun bertumpuk batu, air danau, area bekas bakar Biome 2, cekungan kering Biome 3, tebing curam, serta bibir lumpur pantai:
    - **`COL_Village_Houses` (`26` rumah, `dz = -0.015 m`):** **Biome 1 (*Smog Valley*)** `9` rumah (`(-94, 46)`), **Biome 2 (*Wildfire Woods*)** `9` rumah (`(112, -26)`), **Biome 3 (*Drought Plateau*)** `8` rumah (`(6, -108)`), menghadap ke jalur/alun-alun desa masing-masing.
    - **`COL_Foliage_Trees` (`128` pohon: `23× SM_Pohon_01`, `105× SM_Pohon_02`, `dz ∈ [-0.196, -0.025] m`):** **Biome 2** `58` pohon (paling rimbun), **Biome 1** `34` pohon, **Biome 3** `26` pohon, serta **Center** apron bawah `10` pohon (dataran spawn naga atas `r < 34 m` dibiarkan bersih).
- **Audit, QA, & Catatan Kekurangan Model Aset Bawaan:**
  - `scene_audit.py`: **`ok=True`**, `objects=177`, `missing=[]`, `packed=0`, `absolute_paths=[]`, **`orphans=0`** (instanced `multi_user_mesh` & yaw `rotated` pada `154` rumah/pohon memang disengaja untuk optimasi instancing).
  - `qa_check.py` per varian (`profile="game"`): `SM_Pohon_02` lulus **`pass=True`, `errors=[]`** (peringatan UV tumpuk daun `99.39%` bawaan). Tiga varian lain sengaja dipasang apa adanya terlebih dahulu dengan temuan bawaan dari `rumah.blend`/`pohon.blend` untuk disempurnakan di iterasi berikutnya:
    1. Variasi baru tersedia 2 jenis rumah & 2 jenis pohon.
    2. `SM_Rumah_2Jendela`: material dinding & atap tertukar/belum lengkap (`Cube` memakai `Mat_Roof_Terracotta`, `roof` masih `Material` abu-abu default, serta lubang jendela kanan kosong gelap).
    3. `SM_Pohon_01`: seluruh `82` face masih memakai `Mat_Trunk_Wood` cokelat polos (`Mat_Leaves_Green` belum ter-assign).
    4. Belum memiliki tekstur image UV/PBR khusus bangunan/kulit kayu (`103`/`104`/`82` *zero-area UV faces*, plus `20` & `15` pasang tri coplanar bingkai/dinding pada rumah).
  - Pratinjau diperbarui ([`island_final_topdown.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_topdown.png), [`island_final_perspective.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_perspective.png)) dan render close-up baru ([`island_final_village_b1.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_village_b1.png), [`island_final_village_b2_forest.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_village_b2_forest.png), [`island_final_village_b3.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_village_b3.png)) + viewport `look` telah diperiksa.

## 2026-11-14 — Kepulauan Terhubung di Level Rendah Bawah Air, Pembulatan Tepi Pantai, & Transisi Rumput-ke-Tanah Antar-Gap (`SM_Archipelago_Terrain`)
- **Penyatuan Geometri Level Rendah & Tepi Membulat (`SM_Archipelago_Terrain`):**
  - Menggabungkan ke-4 pulau (`Island_Center`, `Island_Biome1_Smog`, `Island_Biome2_Wildfire`, `Island_Biome3_Drought`) menjadi **1 mesh *watertight* terpadu (`24,190` verts, `47,885` faces, `48,376` tris, `0` boundary edges, `0` non-manifold edges, `0` n-gons)** berukuran `346.22 × 355.27 × 40.35 m` (`z = [-2.85, 37.50] m`), dengan bidang UV top-down bersih (`uv_overlap = 0.0%`, `uv_coverage = 57.2%`).
  - Kontur terluar dibentuk dari *signed-distance field* terhaluskan Gaussian (`sigma` via 3–4 pass) + *bridge field* `min(s0, 0.5*(s0+s1) - 1.6)` pada ambang `7.2 m` -> *Marching Squares* + `2` pass *Chaikin smoothing* + resampling merata (`2.55 m`, `491` vertex tepi), menghilangkan sudut tajam pada seluruh tanjung & teluk serta membentuk kepulauan bersambung di level rendah bawah air (sembari menyisakan teluk tenggara terbuka di `(87.0, -54.5)` antara Biome 2 & Biome 3).
  - *Triangulation adaptif* memakai `delaunay_2d_cdt` dengan resolusi rapat (`1.45 m`) di dataran puncak naga & seluruh zona tepi/celah penghubung, `2.25 m` pada pegunungan & danau Biome 1, serta `3.25 m` di dataran rendah/seabed luar.
  - Seluruh bahu pantai kini melengkung landai dari dataran rumput atas (`~+2.1–2.4 m`) melewati `Z = 0.0 m` ke dasar celah bawah air terhubung (**`Gap_C_B1_mid = -1.501 m`**, **`Gap_C_B2_mid = -1.434 m`**, **`Gap_C_B3_mid = -1.297 m`**, **`Bay_B1_B2_mid = -1.805 m`**, **`Bay_B1_B3_mid = -1.702 m`**) dengan *Shade Smooth* penuh hingga bibir terluar (`SLAB_BOTTOM_Z = -2.85 m`). Bidang laut tidak ditambahkan sesuai instruksi agar sambungan rendah & transisi celah terlihat jelas sebelum ditutup air di Unity.
- **Shader Dual-Atribut & Tekstur PBR Terpadu (`M_Archipelago_Terrain` & `18` File Tekstur Aktif):**
  - Menggunakan 2 atribut warna per-corner (`Col_BiomeMask` & `Col_BiomeAccent`) sehingga satu shader authoring menghasilkan **transisi organik berlapis Rumput -> Tanah Tepi/Bahu Pantai (`soil_coast` ber-noise `fbm2`) -> Tanah Lumpur Lembap Dasar Celah (`mud_channel`)** di setiap gap pulau, sekaligus menjaga gunung lebih hijau *batu+rumput*, 1 danau Biome 1 tepian tanah (`SM_Biome1_Water_01`, `Z = +2.35 m`, kedalaman `~1.39 m`), zona bekas kebakaran Biome 2, 2 cekungan lempung retak kering Biome 3, dan puncak datar hijau Central Mountain (`27.500 m`, puncak rim `37.50 m`).
  - Di-*bake* Cycles `CPU` (`samples=1`, `margin=32`) ke **`T_Archipelago_BC.png` (`8192×8192`, `50,351,267` B)**, **`T_Archipelago_R.png` (`4096×4096`, `5,592,290` B)**, dan **`T_Archipelago_N.png` (`4096×4096`, `25,344,511` B)** + **`SM_Biome1_Water_01`** (`512×512`) + **`4` cluster batu `SM_Rocks_*`** (`1024×1024`) hasil raycast ke `SM_Archipelago_Terrain`.
- **Naga & Verifikasi QA/Audit Menyeluruh:**
  - `ARM_Dragon` + `13` mesh `SM_Dragon_*` diposisikan ulang tepat di puncak datar Central (`(0.0, 2.5, 27.5000)`, panjang terukur `0.5000 m` = `3.00 m` @ `1:6`).
  - `qa_check.py` (`profile="game"`, `max_tris=300000`, `uv=True`) pada `SM_Archipelago_Terrain`, `SM_Biome1_Water_01`, dan 4 `SM_Rocks_*`: **`pass=True`, `errors=[]`**, `zfighting=[]`, `floating=[]`, `uv_overlap_pct=0.0%` pada semua 6 mesh (hanya warning `lowest point z=-2.85` alas bawah). Check terpisah `SM_Dragon_*` (`eps=0.0002`): zero `zfighting`/`floating`, `unweighted_verts=0`, dengan `inside_out_islands` bawaan asli `remodel naga v3/Dragon_Model.blend`.
  - `scene_audit.py`: **`ok=True`**, `objects=23`, `missing=[]`, `absolute_paths=[]`, `orphans=0` (terdapat 5 catatan warisan `origin_far` submesh kepala/leher ber-parent `ARM_Dragon`). Viewport disimpan dalam mode `MATERIAL` (`TEXTURE`) pada [`Pulau_Archipelago.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/Pulau_Archipelago.blend) (`2,309,102` B) & seluruh render ([`island_final_topdown.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_topdown.png), [`island_final_perspective.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_perspective.png), [`island_final_gap_closeup.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_gap_closeup.png), [`island_final_center_dragon.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_center_dragon.png), serta `look` `material` & `wireframe`) telah diperiksa visual.

## 2026-11-14 — Revisi Biome 1 (1 Danau Tepi Tanah), Upgrade 27 Peta Tekstur PBR Seluruh Pulau, & Penempatan Model Naga Central (`Pulau_Archipelago.blend`)
- **Biome 1 (`SM_Island_Biome1_Smog` & `SM_Biome1_Water_01`):**
  - Menghapus kedua danau kecil (`SM_Biome1_Water_02` & `SM_Biome1_Water_03`) sehingga hanya tersisa **1 danau utama** (`SM_Biome1_Water_01`, `176` tris, tekstur riak air `T_Biome1_Water_{BC,R,N}.png` `512×512`).
  - Mengubah garis tepi danau dari pasir menjadi **tanah & lumpur basah organik** (`#422F1E`–`#876542`–`#9E7E5B`) dengan gangguan bentuk tepian *fBm* agar bebas cincin pasir/lingkaran kaku.
- **Upgrade Tekstur PBR Seluruh Pulau & Pegunungan Lebih Hijau (`assets/upgrade model pulau/textures/` — `27` file PNG):**
  - Membake material prosedural menjadi **1 material PBR berbasis Image Texture murni per mesh** (`M_Terrain_*`, `M_Biome1_Water`, `M_Props_Rocks_*`) via Cycles `CPU` (`samples=2`, margin `32 px`) lengkap dengan peta `Base Color` (`4096×4096` pulau, sRGB), `Roughness` (`2048×2048`, Non-Color), dan `Normal` (`2048×2048` OpenGL tangent, Non-Color), serta `1024×1024` untuk `SM_Rocks_*`, serta mengatur `VIEW_3D` pada mode `MATERIAL` preview dengan path relatif penuh.
  - **Rumput (*Grass*):** Kombinasi noise multi-skala (`0.055`, `0.36`, `5.2`, `18.0`) menghasilkan variasi padang rumput hingga detail helai rumput halus di jarak pandang dekat (*close-up* dataran spawn naga).
  - **Gunung Lebih Hijau (`batu + rumput gunung`):** Menggantikan pola retak *Voronoi* heksagonal pada batu gunung & `SM_Rocks_*` dengan stratifikasi batuan *domain-warped* (`_warp_vec` + `Wave` + `Noise Crag`) dan jalur rumput/lumut gunung (`n_gully`), sambil menurunkan ambang lereng batu sehingga punggung dan lembah gunung di seluruh pulau tetap didominasi hijau subur (`Center` `rock_slope_start_nz=0.69`, `Biome1` `0.73`, `Biome2` `0.81`, `Biome3` `0.74`).
  - **Tanah & Aksen Bioma:** Tepian lembap danau & tapak tanah kaki bukit/desa organik (`tanah`), area gosong & abu arang terdistorsi pada Biome 2, serta 2 cekungan lempung kering retak terdistorsi di atas tanah kering SE pada Biome 3.
- **Model Naga & Peninggian Dataran Tengah Gunung Central (`remodel naga v3/Dragon_Model.blend`):**
  - Menaikkan elevasi dataran tengah puncak Central Mountain (`SM_Island_Center`) dari `12.50 m` menjadi **`27.50 m`** (berada di ~73% ketinggian gunung dengan 4 puncak rim hingga **`37.50 m`** — setara `165 m` & `225 m` dunia di Unity) serta me-bake ulang peta tekstur `T_Island_Center_{BC,R,N}.png` & `T_Rocks_Center_*` agar sisi tebing baru tetap hijau-berbatu tanpa noda batu pada tepi dataran rumput atas.
  - Mengimpor `ARM_Dragon` (`47` bone) beserta `13` mesh `SM_Dragon_*` ke koleksi `COL_Dragon` dan membersihkan `3` slot material tak terpakai (`Tail`, `Spikes_Details`, `HindLegs`).
  - Menskalakan panjang asli naga **`2.8763 m`** ke panjang **`0.5000 m`** agar tepat sebanding **`3.00 m`** pada dunia skala `1:6` (kanvas `400 × 400 m` Blender -> `2400 × 2400 m` Unity `Scale = (6,6,6)`), lalu melakukan *apply rotation & scale* (`scale=(1,1,1)`) dan menempatkannya di dataran tengah atas Central Mountain via *raycast* (`location=(0.0, 2.5, 27.5000)`, `aabb=[0.6217, 0.4677, 0.2207] m`, `min_z=27.5000 m`).
- **Verifikasi QA, Audit, & Pratinjau:**
  - `qa_check.py` (`profile="game"`): Ke-4 grup pulau + air + batu (`Center`, `Biome1`, `Biome2`, `Biome3`) seluruhnya lulus **`pass=True`**, **`errors=[]`**, `zfighting=[]`, `floating=[]`, `uv_overlap=0.0%`, `ngons=0` (satu-satunya warning hanyalah alas tepi pulau di `z = -1.5 m`).
  - Pemeriksaan terpisah pada `13` mesh `SM_Dragon_*` (`eps=0.0002`): bebas z-fighting (`zfighting=[]`) dan melayang (`floating=[]`), seluruh vertex berbobot rig (`unweighted_verts=0`, `verts_over_4_influences=0`), dengan `inside_out_islands` pada beberapa bagian bawaan asli `remodel naga v3/Dragon_Model.blend`.
  - `scene_audit.py`: `ok=True`, `objects=26`, `missing=[]`, `absolute_paths=[]`, `orphans=0` (catatan `origin_far` pada 5 submesh kepala/leher ber-parent `ARM_Dragon` dipertahankan agar bind-pose rig tetap utuh).
  - [`Pulau_Archipelago.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/Pulau_Archipelago.blend) (`1,440,380` B) & seluruh 7 render verifikasi ([`island_final_topdown.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_topdown.png), [`island_final_perspective.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_perspective.png), [`island_final_center_dragon.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_center_dragon.png), serta `island_final_3d_*.png`) telah diperiksa secara visual.

## 2026-11-14 — Arsip 5 Referensi Konsep 3D & Pembangunan Model Terrain 3D Final Seluruh Kepulauan (`Pulau_Archipelago.blend`)
- **Pengarsipan Referensi (`assets/upgrade model pulau/references/`):**
  - Menyimpan 4 konsep pulau 3D yang disetujui pengguna beserta acuan gaya visual diorama ke folder proyek:
    - [`Ref_Concept_3D_Center.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/references/Ref_Concept_3D_Center.jpg) (`70,593` B)
    - [`Ref_Concept_3D_Biome1_Smog.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/references/Ref_Concept_3D_Biome1_Smog.jpg) (`69,716` B)
    - [`Ref_Concept_3D_Biome2_Wildfire.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/references/Ref_Concept_3D_Biome2_Wildfire.jpg) (`52,388` B)
    - [`Ref_Concept_3D_Biome3_Drought.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/references/Ref_Concept_3D_Biome3_Drought.jpg) (`74,737` B)
    - [`Ref_Style_Terrain_Diorama.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/references/Ref_Style_Terrain_Diorama.jpg) (`71,661` B)
- **Implementasi Terrain 3D Penuh di [`Pulau_Archipelago.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/Pulau_Archipelago.blend) (`801,772` bytes, kanvas `400 × 400 m` rasio `1:6`):**
  - Garis pantai luar *smooth-minimalist* pada sumbu `XY` dari fase flat tetap dipertahankan 100% utuh, seluruh top terrain & tebing tepi melengkung menerapkan **Shade Smooth** (dengan tepi rim pantai `sharp_edge`), dan alas bawah disusun dengan metode *boundary-only flat slab cap* pada `Z = -1.50 m` agar topologi *watertight* namun hemat poligon (**total terrain mesh `25,044` tris**, aman di bawah anggaran `40,000` tris di [[GDD_Pulau]]).
  - **Revisi Bersih (Tanpa Marker) & Peninggian Central Island:** Seluruh koleksi `COL_Layout_Markers` beserta 4 disc marker warna (`Marker_Spawn_Center`, `Marker_Desa1..3`) dan materialnya dihapus bersih agar scene siap dipakai langsung, sedangkan `Ref_Dragon_Scale_5m` dipindahkan ke `COL_Reference` dalam kondisi tersembunyi (`hide_viewport=True`, `hide_render=True`).
  - **Central Island (`SM_Island_Center` — `3,349` verts / `6,627` faces / `6,694` tris, `z = [-1.50, 36.50] m`):** 4 puncak kaldera batu menjulang tinggi hingga **`36.50 m`** (tertinggi di kepulauan, `+14.0 m` di atas puncak tertinggi biome luar) mengelilingi dataran summon rumput hijau datar (`r = 13.5 m`, `z = 12.50 m`), lengkap dengan `SM_Rocks_Center` (`540` tris, `27` batu). *(Catatan Unity: pada `Scale = (6,6,6)`, puncak tertinggi setara `~219 m`; jika naga boleh terbang melampaui pucuk puncak, naikkan `maxAltitude` dari `160f` ke sekitar `250f`.)*
  - **Biome 1 Smog Valley (`SM_Island_Biome1_Smog` — `2,945` verts / `5,804` faces / `5,886` tris, `z = [-1.50, 22.18] m`):** 3 cekungan danau (`SM_Biome1_Water_01..03`, masing-masing `176` tris, garis pantai pasir lokal tanpa merayap ke tebing), punggung pegunungan gelap W/NW/N + bukit pemisah tengah + puncak timur, dataran pad desa barat daya yang bersih dari marker, dan `SM_Rocks_Biome1_Smog` (`220` tris).
  - **Biome 2 Wildfire Woods (`SM_Island_Biome2_Wildfire` — `2,749` verts / `5,417` faces / `5,494` tris, `z = [-1.50, 22.50] m`):** Dominan hijau segar dengan punggung gunung utara-tengah bebas segmentasi berulang + tebing gelap lokal, area tanah gosong & pinggiran tanah hangat timur, dataran pad desa timur yang bersih dari marker, dan `SM_Rocks_Biome2_Wildfire` (`240` tris).
  - **Biome 3 Drought Plateau (`SM_Island_Biome3_Drought` — `3,487` verts / `6,877` faces / `6,970` tris, `z = [-1.50, 22.50] m`):** Dominan hijau segar dengan masif barat + puncak selatan tinggi, mesa ngarai bertingkat datar hijau-batu di sisi NE/timur, area tanah kering lokal SE dengan **2 cekungan pasir kering surut** bersih tanpa air, dataran pad desa selatan-tengah yang bersih dari marker, dan `SM_Rocks_Biome3_Drought` (`260` tris).
  - Seluruh batu lepas (`SM_Rocks_*`) ditempatkan via *vertical `BVHTree` raycast* ke segitiga terrain sebenarnya (`hit_normal.z >= 0.88`) dan pivot-nya dipusatkan ke titik tengah bounding box sehingga bebas batuan melayang, bebas batu terbenam penuh, dan bebas peringatan *origin_far*.
- **Verifikasi QA & Pratinjau:**
  - `qa_check.py` (`profile="game"`): Ke-4 grup pulau (`Center`, `Biome1`, `Biome2`, `Biome3`) seluruhnya **`ok=True, pass=True`**, **`errors=[]`**, `zfighting=[]`, `floating=[]`, `enclosed=[]`, `0` N-gons/boundary/non-manifold, dan `uv_overlap_pct = 0.0%` di semua mesh tanpa slot material tak terpakai.
  - `scene_audit.py`: **`ok=True`**, `objects=14`, `issues={}`, `orphans=0`, `images missing=[]`, `absolute_paths=[]`.
  - Render pratinjau final tersimpan di [`island_final_3d_center.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_3d_center.png), [`island_final_3d_biome1_smog.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_3d_biome1_smog.png), [`island_final_3d_biome2_wildfire.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_3d_biome2_wildfire.png), [`island_final_3d_biome3_drought.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_3d_biome3_drought.png), [`island_final_topdown.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_topdown.png), dan [`island_final_perspective.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/island_final_perspective.png), serta diperiksa via `look` solid & wireframe.

## 2026-11-14 — Penyempurnaan Kriya Visual UI Mendekati 3 Referensi (`Design` v1.6)
- **Tujuan:** Menyelaraskan detail komponen seluruh 7 artboard (`SCREEN 00–06`) di page `Design` (`id: 262`) pada [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) (`859905` bytes) agar lebih mendekati tiga gambar referensi RPG kartu perkamen sambil tetap mematuhi aturan outline netral seragam (`v1.5`) & ruang blank `>85%` di `SCREEN 02`.
- **Peningkatan Komponen per Area:**
  - **Kartu Utama, Spanduk & Ornamen (`SCREEN 00–06`):** Menambah bibir 3D batu gelap (`#4A4238` `[0, 7]`), menebalkan inner stepped/scallop trim (`#A39884`/`#827766`), memasang 4 ornamen sudut **Corner Fan-Medallion (`24×24 px`)** pada kartu utama `SCREEN 00, 03, 04, 05`, mengubah seluruh banner judul (`SCREEN 00, 01, 03, 04, 05, 06`) menjadi **Stepped Banner** seragam, menurunkan banner `SCREEN 03` (`y=155`) menumpuk batas atas kartu, menyempurnakan **Baroque Scrollwork Crest** simetris di `SCREEN 03–05` dengan jarak napas rapi di bawah navbar `SCREEN 05`, dan menambah sheen & pemusatan vertikal tag `STAGE 01–03`.
  - **Hirarki Kartu Referensi (`SCREEN 00, 03, 04, 05, 06`):** Mengubah baris info/track (`Sub Track`, `Lean Tag`, `Val Track`, `Notes 1–5`, `Proof Rows`) menjadi gaya **borderless recessed warm-taupe** (`#C8BFA8`/`#D5CCBA`, `strokes: []`) bermedali kiri tumpang-tindih (`#38312A`), menambah pill label gelap bawah (`STAR 1..3`, `STAR 01..03`, `0 / 1 ★`) di kotak bintang & merapikan posisi bintang, menempatkan tag speech-bubble emas `#E2A43B` (*coin medallion + corner hatch ticks*) tepat di atas tombol `PLAY`, `SELECT`, dan `NEXT LEVEL`, serta mengonversi slot & tombol terkunci `Card 03` ke *charcoal slate* `#2B2E31`/`#2E3236`.
  - **`SCREEN 01` & `SCREEN 02`:** Memperbaiki kontras teks tombol `RECALIBRATE (C)` ke putih `#FFFFFF` lengkap dengan sheen & double left notch dengan jarak bersih, menambah bibir 3D `[0, 4]` & `[0, 3]` pada seluruh 15 kartu pose, 15 medali ikon, strip kamera, dan seluruh kapsul/track HUD `SCREEN 02`.
- **Verifikasi & Simpan:** Seluruh 7 artboard (`263, 314, 348, 365, 377, 400, 876`) diverifikasi ulang via screenshot render FigCraft dan disinkronkan ke [[rencana_desain_ui]] (`v1.6`).

## 2026-11-14 — Pengembalian Biome 1 ke Flat Layout, Shade Smooth, & Render 4 Foto Referensi 3D Individual + Prompt
- **Keputusan Desain:**
  - Atas arahan pengguna, **Biome 1 (`SM_Island_Biome1_Smog`) dikembalikan ke bentuk datar (*flat*)** seperti ketiga pulau lainnya (seluruh pulau kembali pada `Z = 0.000 m` hingga `-1.500 m`, koleksi `COL_Biome1_Water` serta relief migrasi `Island_v02` dilepas dari scene utama).
  - Sebelum memodelkan ulang kontur elevasi, gunung, dan danau tiap wilayah, disiapkan **1 foto 3D sudut *3/4 bird's-eye* terisolasi per pulau** (tanpa pulau tetangga dan tanpa lingkaran marker agar AI image generator fokus 100% pada siluet garis pantai masing-masing) disertai **4 prompt generasi gambar 3D** sesuai spesifikasi [[konteks]] Bagian 2.3 & [[GDD_Pulau]].
- **Implementasi di [`Pulau_Archipelago.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/Pulau_Archipelago.blend) (`230,048` bytes):**
  - Keempat pulau datar (`SM_Island_Center` `574v/1077f`, `SM_Island_Biome1_Smog` `658v/1230f`, `SM_Island_Biome2_Wildfire` `620v/1159f`, `SM_Island_Biome3_Drought` `836v/1575f`) & 4 marker (`COL_Layout_Markers`) seluruhnya menerapkan **Shade Smooth** pada permukaan top & dinding pantai melengkung dengan `sharp_edge` pada bibir pantai 90° dan alas bawah (`qa_check.py`: keempatnya `pass=True`, `errors=[]`, `uv_overlap=0.0%`; `scene_audit.py`: `ok=True`, `objects=12`, `issues={}`, `orphans=0`).
  - **4 Foto Referensi Pulau Datar Terisolasi (`1280 × 1280 px`, sudut 3/4 SSW -> NNE):**
    - Central Island: [`ref_flat_3d_center.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/ref_flat_3d_center.png) (`1,460,074` B)
    - Biome 1 (Smog Valley): [`ref_flat_3d_biome1_smog.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/ref_flat_3d_biome1_smog.png) (`1,448,905` B)
    - Biome 2 (Wildfire Woods): [`ref_flat_3d_biome2_wildfire.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/ref_flat_3d_biome2_wildfire.png) (`1,474,458` B)
    - Biome 3 (Drought Plateau): [`ref_flat_3d_biome3_drought.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/ref_flat_3d_biome3_drought.png) (`1,408,313` B)
  - Ikhtisar archipelago datar diperbarui di [`flat_topdown.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/flat_topdown.png) (`1,513,346` B) & [`flat_perspective.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/flat_perspective.png) (`1,266,514` B).
- **Revisi 4 Prompt Konsep 3D (Gaya Sederhana ala Image 2 / `Referensi Pulau` — Seluruh Pulau Dominan Hijau):**
  - Menghapus referensi visual *Genshin Impact*, undakan kue melingkar, semak/pohon padat, dan air laut di luar tepi pulau agar hasil generator menyerupai diorama blok terrain *matte* sederhana di latar studio abu-abu polos (*Image 2*: rumput hijau olive *matte* dominan di **keempat** pulau termasuk **Biome 2 & Biome 3**, punggung gunung batu abu-taupe lebih besar/banyak/tegas, serta hanya bercak tanah gosong/tanah kering lokal secukupnya tanpa menghilangkan dominasi hijau alami), tetap mengunci siluet garis pantai 100% dari masing-masing `ref_flat_3d_*.png`.

## 2026-11-14 — Migrasi Kontur Ketinggian, 3 Danau, & Gunung `Island_v02.blend` ke Biome 1 (`SM_Island_Biome1_Smog`)
- **Keputusan Desain & Teknik Pemetaan (*1:1 Square -> Organic Coastline*):**
  - Sumber [`Island_v02.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/Island_v02.blend) (`800 × 800 m` grid `1:1`, `18.817` verts / `35.905` faces, elevasi hingga `118.90 m`) dipindahkan **hanya** ke Biome 1 (`SM_Island_Biome1_Smog`) dengan **garis pantai luar *smooth-minimalist* `XY` Biome 1 dipertahankan 100% utuh** (`189.81 × 174.14 m` Blender); pulau `Center`, `Biome 2`, dan `Biome 3` tetap datar. Batu lepas `Island_Rocks` tidak ikut dimigrasi.
  - Menggunakan **Fong Elliptical Grid Mapping** ($C^\infty$ tanpa lipatan diagonal) dengan rotasi **`-65°`** dan ekspansi domain radial **`B1_MAP_EXPAND = 1.22`** agar ketiga danau dan punggung gunung luar dari `Island_v02` tertarik masuk aman ke dalam pulau (`jarak pantai >= 7.5 m`), dipadukan dengan taper pantai halus (`B1_COAST_BLEND = 6.8 m`) agar pantai tetap landai berumput (`Z = 0.0 m`) kecuali tebing gunung curam (`> 40°`).
  - Faktor elevasi `B1_Z_SCALE = 0.185` + datum lembah `B1_Z_LIFT = 1.85 m` menghasilkan puncak tertinggi **`20.42 m`** di Blender (**`~122.5 m`** saat di Unity `Scale = (6, 6, 6)`), aman di bawah batas `maxAltitude = 160 m`.
- **Implementasi & Hasil di [`Pulau_Archipelago.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/Pulau_Archipelago.blend) (`505,063` bytes):**
  - **`SM_Island_Biome1_Smog` (`5.698` verts, `11.310` faces / `11.392` tris, `dim = 189.81 × 174.14 × 21.92 m`, `Z = [-1.500, 20.423] m`):** Teras bertingkat hijau (`M_Flat_Biome1_Smog`), tebing & punggung batu (`M_Biome1_Rock`), dinding kawah/puncak gelap (`M_Biome1_RockDark`), tepi danau tanah hangat (`M_Biome1_LakeShore`), serta tebing luar (`M_Flat_CoastEdge`).
  - **Shading (*Shade Smooth + Sharp Perimeter Rims*):** Seluruh permukaan top terrain & sisi dinding pantai melengkung di-set *Shade Smooth* (`SM_Island_Biome1_Smog` `5.696/11.310` face top+side halus; `SM_Island_Center` `572/1077`, `SM_Island_Biome2_Wildfire` `618/1159`, `SM_Island_Biome3_Drought` `834/1575`; `SM_Biome1_Water_01..03` `96/144`; `Marker_Desa1_Smog` `72/72`), dengan atribut `sharp_edge` dikunci pada bibir pantai 90° (`top <-> side`), alas bawah (`side <-> bot`), dan sudut tebing `> 45°` sehingga visual *low-poly* faset menghilang tanpa kebocoran gradasi normal gelap di tepian datar.
  - **Koleksi `COL_Biome1_Water` (3 danau bertopologi *watertight wafer* bersudut bersih, masing-masing `98` verts / `144` faces / `192` tris, tebal `0.14 m`, `uv_overlap_pct = 0.0%`):**
    - `SM_Biome1_Water_01` (`37.04 × 23.51 m`, `loc = (-59.57, 128.91, 1.63)`)
    - `SM_Biome1_Water_02` (`13.81 × 17.04 m`, danau kawah berbatu gelap, `loc = (-76.63, 47.49, 1.46)`)
    - `SM_Biome1_Water_03` (`14.98 × 30.47 m`, `loc = (-116.90, 65.07, 1.58)`)
  - **`Marker_Desa1_Smog`:** Otomatis diposisikan di teras rumput datar tengah pulau pada `(-86.33, 74.00, 6.22)` (`z_base = 6.07 m`).
  - Ketiga pulau lainnya tetap datar identik (`SM_Island_Center`: `144.24 × 145.68 × 1.50 m`, `SM_Island_Biome2_Wildfire`: `134.37 × 191.81 × 1.50 m`, `SM_Island_Biome3_Drought`: `225.58 × 158.88 × 1.50 m`).
- **Verifikasi QA & Visual:**
  - `qa_check.py` (`profile="game"`): `SM_Island_Biome1_Smog` + `SM_Biome1_Water_01..03` lulus penuh (`pass=True`, `errors=[]`, `zfighting=[]`, `floating=[]`, `0` N-gons, `0` boundary/non-manifold edges, `0` zero-area UVs, `0.0%` UV overlap); ketiga pulau datar juga `pass=True`, `errors=[]`.
  - `scene_audit.py`: `ok=True`, `objects=17`, `issues={}`, `orphans=0`, `missing=[]`, `absolute_paths=[]`.
  - Render pratinjau tersimpan di [`biome1_migrated_topdown.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/biome1_migrated_topdown.png) (`1,136,616` B), [`biome1_migrated_persp.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/biome1_migrated_persp.png) (`1,398,356` B), [`flat_topdown.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/flat_topdown.png) (`1,613,862` B), dan [`flat_perspective.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/flat_perspective.png) (`1,308,559` B), serta diverifikasi wireframe & solid shading via `look` MCP.

## 2026-11-14 — Pembuatan & Penyempurnaan Fase 1 Flat Layout Pulau *Smooth Minimalist* (`Pulau_Archipelago.blend`, Skala `400 × 400 m`)
- **Keputusan Pipeline, Skala, & Gaya Garis Pantai:**
  - Pembuatan remodel pulau dikerjakan **langsung di Blender (`5.2.3 LTS`)** tanpa perantara Roblox Studio.
  - Dimulai bertahap dari **Fase 1: Flat Layout** — menampilkan bentuk garis pantai, lebar selat pemisah, dan tata letak keempat daratan secara datar (`Z = 0.0 m` sampai `-1.50 m`) **tanpa** mesh laut, perbedaan level ketinggian (*level ground*), gunung, maupun danau.
  - **Kanvas Blender `400 × 400 m` (Rasio `1:6`):** Berdasarkan [`Peta_Denah_Resmi_Genshin_Style.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/Peta_Denah_Resmi_Genshin_Style.jpg) (`2400 × 2400 m` termasuk laut luar), skala di Blender ditetapkan **`400 × 400 m`** agar viewport ringan dan seluruh kepulauan tampak utuh di kamera/viewport. Saat diimpor ke Unity nanti, atur **`Scale = (6, 6, 6)`** untuk mengembalikan ukuran dunia asli.
  - **Refinement Garis Pantai (*Smooth Minimalist*):** Kontur awal *trace* (`approxPolyDP 2.3 px`) terlalu bergerigi/semi-realis dengan banyak lekukan mikro, sehingga diperhalus per pulau menggunakan *morphological close/open* (`29 px` / `15 px`), *cyclic Gaussian arc-length smoothing* (`sigma = 20.0 px`), dan resampling seragam `18.0 px` (`~7.0 m` per segmen di ruang Blender `400 m`) — menghilangkan gerigi tajam sambil tetap mempertahankan siluet teluk, tanjung, dan selat khas masing-masing pulau agar tidak terlalu kaku/minimalis (`Center 67v`, `Biome 1 82v`, `Biome 2 77v`, `Biome 3 93v`).
- **Implementasi di Blender MCP (`Pulau_Archipelago.blend`, `228,091` bytes):**
  - **Koleksi `COL_Island_Flat`** (4 mesh daratan datar, triangulasi seragam *Constrained Delaunay* + tepi tebing `1.50 m` bermaterial `M_Flat_CoastEdge`, skala `(1, 1, 1)`, origin `(0, 0, 0)`, bounding box gabungan daratan `332.49 × 341.38 × 1.50 m` pada `X=[-164.69, 167.80]`, `Y=[-173.22, 168.16]`):
    - `SM_Island_Center`: `144.24 × 145.68 × 1.50 m` (`574` verts, `1077` faces / `1144` tris, `M_Flat_Center`)
    - `SM_Island_Biome1_Smog`: `189.81 × 174.14 × 1.50 m` (`658` verts, `1230` faces / `1312` tris, `M_Flat_Biome1_Smog`)
    - `SM_Island_Biome2_Wildfire`: `134.37 × 191.81 × 1.50 m` (`620` verts, `1159` faces / `1236` tris, `M_Flat_Biome2_Wildfire`)
    - `SM_Island_Biome3_Drought`: `225.58 × 158.88 × 1.50 m` (`836` verts, `1575` faces / `1668` tris, `M_Flat_Biome3_Drought`)
  - **Koleksi `COL_Layout_Markers`:** Kandidat penanda datar `Marker_Spawn_Center` `(0.00, 0.00)` (jarak pantai `52.90 m`), `Marker_Desa1_Smog` `(-68.33, 70.00)` (jarak pantai `11.13 m`), `Marker_Desa2_Wildfire` `(98.33, 25.00)` (`23.31 m`), `Marker_Desa3_Drought` `(15.00, -96.67)` (`29.28 m`), serta `Ref_Dragon_Scale_5m` (proksi naga skala `1:6`, `0.60 × 0.59 × 0.26 m` menghadap Desa 1).
  - **Koleksi `COL_Reference` & `COL_Cameras_Lights`:** `Ref_Blueprint_400m` (`400 × 400 m`, disembunyikan di viewport & render, path relatif `//Peta_Denah_Resmi_Genshin_Style.jpg`), `SUN_Key`, `CAM_TopDown` (`ORTHO`, `ortho_scale 390 m`), dan `CAM_Perspective` (`PERSP`, `(-255, -330, 255)`, `clip_end 2000 m`).
- **Verifikasi QA & Visual:**
  - `qa_check.py` (`profile="game"`, per pulau): keempat pulau `pass=True`, `errors=[]`, `0` N-gons, `0` boundary/non-manifold edges, `0` zero-area UVs, dan `uv_overlap_pct = 0.0%` di **semua 4 pulau** (peringatan `ground_z = -1.5` disengaja karena datum atas di `Z = 0.0`).
  - `scene_audit.py`: `ok=True`, `issues={}`, `orphans=0`, `absolute_paths=[]`, `missing=[]`.
  - Render pratinjau tersimpan di `assets/upgrade model pulau/previews/flat_topdown.png` (`1,513,346` bytes) dan `flat_perspective.png` (`1,267,029` bytes), serta diverifikasi via `look` MCP (`wireframe` & `material`).

## 2026-11-14 — Penyeragaman Warna Outline Netral Seluruh UI (`Design` v1.5)
- **Konsistensi Outline Seluruh Artboard (`SCREEN 00–06`):** Menyeragamkan seluruh warna outline kontainer UI di [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) (`791985` bytes) agar tidak memakai outline berwarna-warni (merah, biru/cyan, hijau, oranye/kuning).
- **Aturan Token Outline Baru:**
  - Kontainer kartu, panel kalibrasi, dropdown kamera, viewport preview/PIP, badge status kamera navbar, dan kartu state di `SCREEN 06` diseragamkan ke tinta utama `#332C25`.
  - `Inner Rim` tombol tutup/jeda (`ESC` / `Close`) pada seluruh layar diubah dari `#C45148` menjadi `#A89E8B`; tombol nonaktif `Matikan` memakai `#9D9384`; dan badge di dalam viewport gelap `#1A2125` memakai `#4B565D`.
  - Pembeda 5 state kamera (`STANDBY`, `MEMUAT YOLO`, `LIVE`, `ERROR`) tetap jelas lewat *fill* badge status, dot indikator, teks panduan, dan tombol mode aktif tanpa mewarnai bingkai luar.
- **Verifikasi Visual & Dokumentasi:** Seluruh 6 artboard terdampak (`263`, `314`, `348`, `377`, `400`, `876`) telah diverifikasi lewat render ulang FigCraft (`SAVE` sukses) dan disinkronkan ke [[rencana_desain_ui]] (`v1.5`).

## 2026-11-14 — Perbaikan Geometri Sudut Kartu (*Stepped Rabbeted Plaques + Scalloped Inner Frame*, `Design` v1.4)
- **Umpan Balik Desain:** Menggantikan seluruh sudut membulat (`cornerRadius: 4..10 px`) dan *corner bracket* mengambang pada page `Design` (`id: 262`) di [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) agar sesuai penuh dengan siluet sudut kartu perkamen pada 3 referensi UI terbaru.
- **Implementasi Geometri Vektor (`Design` id `262`, 7 Artboard):**
  - **Kartu Utama & Panel Kalibrasi (`SCREEN 00, 01, 03, 04, 05`):** Dikonversi menjadi siluet vektor **Stepped Rabbeted Plaque (`a = 14, b = 7`)** bertakik 2 undakan pada 4 sudutnya (`strokeAlign: inside`, `strokeJoin: miter`), dipasangi dua bingkai dalam kontinu (*Stepped Outer Frame* `inset 10 px, #B2A895` + *Concave Scalloped Arch Frame* `inset 15 px, arch r = 18, #8D8371`, `fill: none`).
  - **Sub-Panel & Kartu Mikro:** `Victory Stat Container Box` (`560×278`), `Camera Input & AI YOLO Module` (`640×342`), dan ketujuh kartu spec `SCREEN 06` (`884..890`) memakai **Stepped Plaque (`a = 10, b = 5`)** + inner stepped trim `a = 8, b = 4`; seluruh **15 kartu pose `SCREEN 01`** (`340×122`, termasuk `Card 08 Active` dengan double stepped border `#DF9E38` + `#332C25`), 4 baris slider `SCREEN 05`, 3 baris stat `SCREEN 04`, dan plakat gestur `SCREEN 03` dikonversi ke **Stepped Plaque (`a = 8, b = 4` / `a = 6, b = 3`)**.
  - **Kontrol & Elemen Kompak (`SCREEN 00–06`):** Seluruh tombol aksi, badge, tab navbar, dropdown, viewport preview, box bintang, padlock, dan nilai stat diset seragam ke sudut tajam `r = 2 px` (`r = 3 px` untuk container navbar/HUD kapsul `SCREEN 02`, `r = 1 px` untuk crimson inner rim `ESC`/`Lock Inner Rim`), disertai pembaruan legenda token di `SCREEN 06` (`1019`).
- **Verifikasi & Artefak:** Seluruh 7 artboard diverifikasi ulang via screenshot FigCraft (`steps/954`–`957`, `966`–`967`, `977`), dokumen disinkronkan ke [[rencana_desain_ui]] (`v1.4`), dan [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) tersimpan bersih (`792157` bytes).

## 2026-11-14 — Penyempurnaan Gaya Visual *Fantasy Parchment-and-Ink Tabletop UI* Seluruh 7 Artboard (`Design` v1.3)
- **Tujuan:** Menyelaraskan seluruh 7 artboard di halaman `Design` (`id: 262`) pada [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) agar mendekati kualitas kriya tiga referensi RPG kartu perkamen terbaru (`r = 4..10 px` chamfered plaques, bingkai tinta `#332C25` + hard cast shadow nol-blur, spanduk heksagonal runcing berornamen diamond *fleuron*, tag speech-bubble emas `#DF9E38`, rel tab bookmark `#CFC6B4` + `#E5AD44`, panel stat resessif `#DDD5C5`/`#C7BDAA`, dan tombol taktil `#5C8D46` dengan highlight *sheen* & takik ganda kiri).
- **Perbaikan Teknis & Visual per Artboard (`Design` id `262`):**
  - **Fix Artifact & Z-Order Global:** Membersihkan default fill `#D9D9D9` (`fill: "none"`) pada seluruh vektor terbuka (*stepped corners*, *X stroke*, *pause bars*, *vitality ticks*, *dragon wing ribs*), mengganti transparansi `#FBF6EAE6` pada `Dragon Telemetry Bar` (`687`) menjadi solid 100% opaque `#F2EFE7`, serta memisahkan `Card Surface BG` ke `index: 0` sehingga seluruh spanduk/ribbon bertumpuk (`01–03`, `CAMERA MIRROR · TRACKING`, `LIVE DRAGON PREVIEW`, `STAGE COMPLETE`, `SETTINGS & AUDIO CONTROLS`) menindih bersih garis batas atas kartu tanpa garis potong.
  - **`SCREEN 00` (`id: 263`):** Kartu biome `r = 10` + stepped corners, banner nama level runcing dengan *fleuron*, strip subtitle `#C7BDAA`, tag speech-bubble emas `90s`/`150s`, tombol `PLAY`/`SELECT` chamfered `r = 6`, box `LOCKED`, serta tombol `ESC · QUIT` krem-merah `#F3EFE6`/`#D15046`.
  - **`SCREEN 01` (`id: 314`):** Kedua panel kalibrasi memakai overlapping header plaque heksagonal sehingga status `LIVE · JENDELA PREVIEW` + `Matikan` tidak berdesakan dengan judul, ke-15 kartu pose & medallion diseragamkan ke `r = 6..8`, pil bawah `ARM GATE RULE` & `SEATED & SAFETY` serta CTA `READY TO FLY` beralih ke `r = 6`.
  - **`SCREEN 02` (`id: 348`) & `SCREEN 03` (`id: 365`):** Tetap mempertahankan latar putih bersih `#FFFFFF` (`> 85%` ruang gameplay terbuka di `SCREEN 02`), penyeragaman kapsul timer/waypoint/telemetri/`PIP Camera Control Header`/`Webcam PIP Monitor` ke `r = 6`, ditambah baroque *scrollwork flourish* & spanduk heksagonal `VILLAGE 2: WILDFIRE WOODS` + plakat instruksi resessif `#DDD5C5` pada `SCREEN 03`.
  - **`SCREEN 04` (`id: 377`) & `SCREEN 05` (`id: 400`):** Baroque scrollwork crest di atas kartu modal, spanduk heksagonal bertumpuk `STAGE COMPLETE` / `SETTINGS & AUDIO CONTROLS`, kotak stat resessif `#DDD5C5` + pill nilai `#C7BDAA`, 4 baris slider `Master/BGM/SFX/Sensitivity` yang diselaraskan presisi antara bar fill dan knob, modul kamera standby `r = 6`, serta `APPLY & CLOSE` / `NEXT LEVEL` / `REPLAY` `r = 6`.
  - **`SCREEN 06` (`id: 876`):** Banner spesifikasi heksagonal ber-*fleuron*, tombol `ESC · BACK` dengan rim crimson & X vektor putih bersih, ke-5 kartu state kamera + dropdown popup solid `#FBF6EB` + token badge navbar dinamis diseragamkan ke `r = 4..8`, plus pembaruan legenda palet token `v1.3`.
- **Verifikasi & Artefak:** Seluruh 7 artboard diinspeksi lewat tangkapan layar render FigCraft, file disimpan (`UI AR Dragon.figcraft`, `730169` bytes), dan spesifikasi disinkronkan di [[rencana_desain_ui]] (`v1.3`).

## 2026-11-14 — Pembaruan Desain UI Kontrol Kamera & Aktivasi Dual-Mode YOLO (FigCraft + Spec v1.2)
- **Konteks Integrasi (`laptop-b`):** Menyelaraskan spesifikasi UI dan artboard FigCraft dengan sistem kontrol kamera manual (`autoLaunchPython = false`, dropdown unselected default `"Pilih Input Kamera... ▼"`, tombol `Scan` kamera DirectShow, pemisahan mode `Latar Belakang` / `Jendela Preview`, tombol `Matikan`, serta popup dropdown 100% solid `alpha = 1.0f`).
- **Pembaruan [[rencana_desain_ui]] (`v1.2`):**
  - **SCREEN 00 (Lobby/Missions):** Badge navbar kanan atas dinamis (`● STANDBY · PILIH KAMERA` vs `● CAM READY · LATAR BELAKANG` / `● CAM READY · JENDELA PREVIEW`), tombol `ESC · QUIT` konsisten di kanan atas, serta rujukan 3 ilustrasi biome (`UI_Biome_01_Smog_Valley.jpg`, `UI_Biome_02_Wildfire.jpg`, `UI_Biome_03_Drought.jpg`).
  - **SCREEN 01 (Calibration):** Penambahan status pill (`● LIVE · JENDELA PREVIEW`) + tombol `Matikan` + baris selector (`[6] OBS Virtual Camera ▼`, `Scan`, `Latar Belakang`, `● Jendela Preview`) pada panel kiri kamera mirror.
  - **SCREEN 02 (In-Game Flight HUD):** Penambahan `PIP Camera Control Header` solid `#FBF6EB` (`420×112`) tepat di atas `Webcam PIP Monitor` 16:9 (`420×236`), tetap menjaga `> 85%` ruang pandang gameplay bebas halangan.
  - **SCREEN 05 (Settings Modal):** Perluasan container menjadi 4 slider + modul `Camera Input & AI YOLO Module (Standby)` dengan default `"Pilih Input Kamera... ▼"`, `Scan`, tombol mode disabled-until-selected, dan tombol `Matikan`.
  - **SCREEN 06 (Spec Sheet Baru — `1920×1080` di `x: 0, y: 2400`):** Dokumentasi visual **5 State Panel Kamera** (`STANDBY Belum Pilih`, `STANDBY Terpilih`, `MEMUAT YOLO`, `LIVE`, `ERROR`), simulasi **popup dropdown 100% solid `#FBF6EB`** anti tembus teks, serta **3 varian badge navbar `SCREEN 00`**.
- **Artefak Tersimpan:** [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) (`673172` bytes — page `Wireframe` & `Design`) dan [`rencana_desain_ui.md`](file:///e:/Semester%205/Game/AR%20Dragon/brain/laptop-a/rencana_desain_ui.md).

## 2026-11-14 — Pemasangan Ilustrasi Masalah Biome pada 3 Kartu Level Main Menu (`Design`)
- **Aksi:**
  - Menyimpan 3 aset ilustrasi raster `16:9` *cel-shaded low-poly* hasil generasi prompt dari `referensi/user upload` ke `Referensi UI/`:
    - `Referensi UI/UI_Biome_01_Smog_Valley.jpg` (`144802` bytes) — lembah hijau & danau, rumah desa berdinding krem/atap terakota, serta cerobong batu mengepulkan asap kabut abu-abu gelap.
    - `Referensi UI/UI_Biome_02_Wildfire.jpg` (`141454` bytes) — perbukitan pinus & desa berhadapan dengan tanah hangus hitam dan kobaran api merah-oranye.
    - `Referensi UI/UI_Biome_03_Drought.jpg` (`156588` bytes) — dataran tanah retak kering, danau surut, pepohonan kering tanpa daun, dan desa kecil di tengah kekeringan.
  - Mengganti seluruh placeholder vektor di halaman `Design` -> `SCREEN 00 - Main Menu & Level Selection` (`id: 263`) pada kotak karya `380 × 210 px` (`r = 12`, `clip: true`, bingkai tinta `#2D2926` / `#4A443C`) masing-masing menjadi `Biome Art Box - Smog Valley` (`277`), `Biome Art Box - Wildfire` (`292`), dan `Biome Art Box - Drought` (`307`), dengan mempertahankan layer `Locked Scrim` (`467`) di atas gambar *Drought* agar status terkunci tetap jelas secara visual.
  - Diverifikasi melalui tangkapan layar kanvas FigCraft (`1600×900`) dan disimpan ke `E:\Semester 5\Game\AR Dragon\UI AR Dragon.figcraft` (`603592` bytes).

## 2026-11-14 — Perbaikan Halaman `Calibration` (Page `Design`) Selaras Mekanisme Nyata Laptop B (Ekspansi 15 Kartu Pose)
- **Konteks Audit (`brain/laptop-b/`):** Halaman Calibration pada page `Design` (`SCREEN 01`, `id: 314`) sebelumnya masih memakai tutorial lama `Pose Y (Climb) / Pose T (Cruise) / Pose A (Dive)`, kemudian setelah diperbarui menjadi 4 kartu tetap belum mencakup seluruh gestur nyata di [[laptop-b/konteks]] (v6.0 & v6.1) dan [[laptop-b/prd]] (termasuk *Grounded Idle* berdiri tegak diam di tempat, *Grounded Walk*, *Grounded Look Up*, *Takeoff*, *Fly Up*, *Hover*, *Gentle Descent*, *Pose Y*, *Pose W*, *Tuck Dive*, *Lean & Arm Tilt*, *Propeller Dash*, dan gestur 3 desa).
- **Perbaikan pada `UI AR Dragon.figcraft` (`id: 314`) & [[rencana_desain_ui]] (Bagian 2.2):**
  - **Panel Atas Kompak (`860 × 410 px` pada `y = 114`) untuk Ruang Grid Penuh:** Menata *Camera Mirror* (`532 × 294` + sidebar HUD kalibrasi di kanan dalam viewport gelap) dan *Live Dragon Preview* (skala proporsional `0.62×` di pedestal + bar telemetri atas), tanpa memotong tombol pemicu UDP `5054` (`RECALIBRATE (C)`, `RESET DEFAULT`) dan pil status `GROUND: WALK 3.5 m/s · LEAN: 0°`.
  - **Matriks Lengkap 15 Kartu Pose & Mekanik (Grid `3 × 5`, `340 × 122 px` per kartu):**
    - **Baris 1 (Ground, Transisi, & Kemudi):** `GROUNDED IDLE` (berdiri/duduk tegak lurus tangan rapat di sisi -> diam total `0 m/s`), `GROUNDED WALK` (*arms away* `3.5 m/s`), `GROUNDED LOOK UP` (angkat dada/tilt tanpa kepak -> mendongak `0 m/s`), `TAKEOFF` (kepakan kuat di darat `>=0.25s`), dan `LEAN & ARM TILT` (kemudi 50/50 torso + lengan di darat/udara, dukung mode duduk).
    - **Baris 2 (Penerbangan Udara Inti):** `FLAP TO CLIMB` (`12–19.5 m/s`), `FLY UP` (angkat dada + kepak `+19°`), `GLIDE (T-POSE)` `[ACTIVE]` (`14 m/s` + *Swoop Climb* otomatis paska-dive), `HOVER MODE` (lengan atas rapat + kepak lengan bawah `0 m/s`), dan `GENTLE DESCENT` (lengan atas rapat diam `8.5 m/s`, sink `-1.6 m/s`).
    - **Baris 3 (Turun Presisi, Tukik, Boost, & Desa):** `FLY DOWN (POSE Y)` (*arms away + forearms up*, maju `12 m/s`, `-5.5 m/s`), `FLY DOWN IDLE (POSE W)` (*arms tucked + forearms up*, vertikal `0 m/s`, `-3.2 m/s`), `TUCK TO DIVE` (tangan merapat di dada `28 m/s`), `PROPELLER DASH` (putar kedua lengan melingkar `26 m/s`), dan `VILLAGE ACTIONS` (Smog: *Flap*, Fire: *Open mouth / Clasp chest*, Drought: *Hands high overhead*).
  - **Bar Aturan Inti & CTA Bawah:** 2 pil ringkas di kiri `READY TO FLY`: `ARM GATE RULE` (ringkasan logika terpadu *Tucked* vs *Away*) dan `SEATED & SAFETY` (dukungan *Virtual Hips* mode duduk & perilaku aman *Tracking Loss*).
  - Disimpan ke `E:\Semester 5\Game\AR Dragon\UI AR Dragon.figcraft` (`161309` bytes).

## 2026-11-14 — Implementasi Desain Visual 6 Layar UI pada Page `Design` di FigCraft
- **Aksi:**
  - Menduplikasi page `Wireframe` (`id: 1`) menjadi page baru bernama `Design` (`id: 262`) pada dokumen `UI AR Dragon` (`E:\Semester 5\Game\AR Dragon\UI AR Dragon.figcraft`) dengan mempertahankan posisi & proporsi layout wireframe 100%.
  - Menerapkan bahasa visual dari 3 referensi RPG *parchment-stone* user: backdrop `BG_Island_Aerial_View.jpg` khusus untuk 3 layar menu (`Main Menu`, `Calibration`, `Settings`) dengan header teks tinta gelap `#2D2926`, serta latar belakang blank putih bersih (`#FFFFFF`) pada 3 layar *in-game* (`In-Game Flight HUD`, `Village Challenge Overlay`, `Finish UI Victory Modal`) karena di Unity akan menampilkan viewport game secara langsung.
  - Komponen UI memakai kartu krem parchment (`#EFE9DD`) berbingkai tinta gelap `#2D2926`, *double-line inner border* + ornamen sudut vektor, banner judul berornamen diamond filigree, tab navbar aktif bernuansa emas (`#E0A23B`), CTA primer hijau 3D-bevel (`#5E8E49`) dengan notch kiri, tombol sekunder `#E5DBC7`, tombol `ESC` krem-merah konsisten di kanan atas, kotak bintang gradien vektor, gembok batu gelap di level terkunci, slider vektor dengan ikon medallion, serta ilustrasi vektor 2D untuk 3 biome, skeleton kalibrasi, pratinjau naga, PIP monitor, dan *water breath challenge*.
  - Menyimpan hasil ke `E:\Semester 5\Game\AR Dragon\UI AR Dragon.figcraft`.

## 2026-11-13 — Klarifikasi Status Final Remodel Model Naga (`remodel naga v3/Dragon_Model.blend`)
- **Aksi:** Mengonfirmasi bahwa remodel model naga 3D telah selesai pada `remodel naga v3/Dragon_Model.blend` ([[Pembuatan Model Naga 3D v3]]), sehingga item tugas remodel lama (`assets/Naga V2 Tes 2.blend`) dihapus dari daftar fokus aktif di [[Tasks]].

## 2026-11-13 — Simplifikasi Nama File Memori & Update Aturan Penamaan
- **Aksi:** Menyederhanakan seluruh 12 nama file di `brain/laptop-a/memories/` menjadi judul ringkas (3–6 kata) agar mudah dibaca di Obsidian, memperbarui seluruh `[[wikilinks]]` di [[Devlog]] dan [[Tasks]], serta menyempurnakan aturan penamaan pada command `"jadikan sesi ini memori"` di [[00 - Overview]].

## 2026-11-13 — Penambahan Command Bersama `"jadikan sesi ini memori"`
- **Aksi:** Menambahkan spesifikasi command `"jadikan sesi ini memori"` di [[00 - Overview]] (dan rujukannya di `AGENTS.md` Laptop A) agar baik Laptop A maupun Laptop B otomatis mengenali perintah tersebut, menyimpan catatan sesi deskriptif di `memories/` masing-masing, memperbarui `Devlog.md`/`Tasks.md`, serta melakukan `commit` dan `push` ke `origin/main`.

## 2026-11-13 — Dokumentasi Sesi Remodel Naga 3D v3 ([[Pembuatan Model Naga 3D v3]])
- **Aksi:** Menyusun catatan memori terstruktur [[Pembuatan Model Naga 3D v3]] yang merekam seluruh iterasi pembuatan `remodel naga v3/Dragon_Model.blend` dari 0, transfer kepala v1/v2 (`0.685×`), perbaikan sayap, transisi $C^2$ body–ekor (`24` vertex/cincin), *caudal leaf fin*, gem dada *pear brilliant-cut* (`+21.5°` X-rot), orientasi spike kepala/punggung, penghapusan 2 spike leher atas, serta hasil verifikasi `qa_check.py`.

## 2026-11-13 — Dokumentasi Sesi Perancangan UI, Wireframe FigCraft, & Denah Pulau 3 Biome ([[Desain UI dan Denah Pulau 3 Biome]])
- **Aksi:** Menyusun catatan memori terstruktur [[Desain UI dan Denah Pulau 3 Biome]] yang merangkum penggabungan `konteks.md`/`prd.md` v4.0, penguncian aset `Referensi UI/` (8 file: Lobby, Kalibrasi, Flight HUD, Background, serta Wireframe Finish & Village Action), penguncian cetak biru peta 1:1 `assets/upgrade model pulau/Peta_Denah_Resmi_Genshin_Style.jpg` (*Tight-Knit Caldera Archipelago* $1200 \times 1200\text{ m}$), serta penyusunan 1 prompt wireframe tunggal FigCraft untuk 5 layar tersisa.

## 2026-11-13 — Dokumentasi Sesi Eksperimen Daratan Pulau di Roblox Studio ([[Eksperimen Terrain Pulau Roblox]])
- **Aksi:** Menyusun catatan memori terstruktur [[Eksperimen Terrain Pulau Roblox]] yang merekam iterasi pembuatan peta pulau `1600 × 1600` studs di Roblox Studio (mulai dari 9 formasi plateau blueprint, part & `WedgePart`/`CornerWedgePart` berorientasi diagonal, hingga Smooth Terrain prosedural `WriteVoxels` 16 chunk bebas *seam* tanpa bukit/danau) serta pembersihan total (*clean slate*) di akhir sesi untuk diskusi ulang dari awal.

## 2026-11-13 — Dokumentasi Sesi Blender MCP Pembuatan Daratan 800×800m ([[Generasi Daratan Blender Tahap 1]])
- **Aksi:** Mengarsipkan seluruh hasil sesi 2026-11-08 ke [[Generasi Daratan Blender Tahap 1]] (verifikasi koneksi Blender MCP `5.2.3 LTS`, analisis 3 referensi terrain, identifikasi 3 kontradiksi spesifikasi, serta eksekusi Tahap 1 `island_gen.py` menjadi `Island_v01.blend` [`HEIGHT_MULT = 1.0`] dan `Island_v02.blend` [`HEIGHT_MULT = 3.0`] yang keduanya lolos 14/14 cek `validate()`).

## 2026-11-13 — Konfigurasi & Integrasi MCP Server FigCraft ([[Integrasi MCP FigCraft]])
- **Aksi:** Mengonfigurasi server MCP `figcraft` (`E:\ArtCraft\Apps\FigCraft\FigCraft\figcraft-cli.exe` dengan argumen `["mcp"]`) di `C:\Users\nmast\.gemini\config\mcp_config.json`, mengekspor `instructions.md` serta 19 skema JSON tool ke `C:\Users\nmast\.gemini\antigravity\mcp\figcraft\`, dan mendokumentasikan detailnya di [[Integrasi MCP FigCraft]].

## 2026-11-13 — Iterasi Kaki, Eksperimen & Revert Body/Sayap Naga V2, dan Eksekusi `generate_dragon.py` ([[Iterasi Naga v2 dan Eksekusi Generator]])
- **Aksi:** Menyusun catatan memori terstruktur [[Iterasi Naga v2 dan Eksekusi Generator]] yang merekam perbaikan mesh `kaki` & `kaki2` pada `assets/Naga V2 Tes 2.blend`, pembuatan prompt Hermes (analisis referensi dengan `antigravity`/`gemini flash` + pemodelan Blender dengan `opus 4.6`), eksperimen dan *revert* bersih pada *body* (`Naga V2 Tes 2_backup_before_body.blend`) serta sayap (`Naga V2 Tes 2_backup_before_wings.blend`), hingga perbaikan `SyntaxError` dan eksekusi `generate_dragon.py` ke `assets/AncientDewWaterDragon_Generated.blend`.

## 2026-11-13 — Setup FigCraft MCP Live Control & Wireframe Kit 6 Layar ([[Wireframe 6 Layar UI di FigCraft]])
- **Aksi:** Mengonfigurasi FigCraft MCP (`--attach 9877`, shortcut `FigCraft (MCP Control)`), membangun 6 artboard wireframe 16:9 (`Main Menu & Level Selection`, `Calibration`, `In-Game Flight HUD`, `Village Challenge Overlay`, `Finish Victory Modal`, `Settings Modal`) dalam grid 2×3 secara live via socket kontrol, serta menyeragamkan seluruh tombol `ESC` ke sudut kanan atas ([[Wireframe 6 Layar UI di FigCraft]]).

## 2026-11-13 — Pengecekan Blender MCP & Eksekusi `generate_dragon.py` ([[Perbaikan dan Eksekusi generate_dragon]])
- **Aksi:** Memverifikasi koneksi Blender MCP (`Blender 5.2.3 LTS`, Addon `v1.8`), memperbaiki *SyntaxError* (*unterminated string literal* pada fungsi `export_standalone_obj()`) di `generate_dragon.py`, serta mengeksekusi skrip di Blender hingga menghasilkan 51 mesh komponen naga dalam koleksi `AncientDewWaterDragon` ([[Perbaikan dan Eksekusi generate_dragon]]).

## 2026-11-13 — Dokumentasi Sesi Setup Output Remodel Naga v2 ([[Setup Output Remodel Naga v2]])
- **Aksi:** Menyusun catatan memori terstruktur [[Setup Output Remodel Naga v2]] yang merekam pembuatan struktur direktori `remodel naga v2/` (`Dragon_Model.blend`, `renders/` 5 sudut pandang, `previews/` workbench & contact sheet, `textures/README.txt`), perbaikan orientasi kamera `side`/`back`, serta verifikasi reopen headless Blender 5.2.3 LTS tanpa file tercecer atau path absolut.

## 2026-11-13 — Dokumentasi Sesi Setup Blender MCP, Skill Modeling, & Konsep Pulau Sederhana ([[Setup Blender MCP dan Konsep Pulau]])
- **Aksi:** Menyusun catatan memori terstruktur [[Setup Blender MCP dan Konsep Pulau]] yang merekam konfigurasi `mcp-for-blender` (Blender 5.2.3 LTS, port 9876), instalasi 28 skill `EL4CTEO/blender-skills` di `.agents/skills/`, iterasi awal aset `Assets/pulau_sky_guardian.blend`/`.fbx`, serta keputusan penyederhanaan pulau (fokus pada elemen tantangan: sungai, hutan, cerobong asap, lahan tandus `#8B5A2B`, tanpa air terjun/pulau satelit) beserta prompt referensi gambar 3D-nya.

## 2026-11-13 — Dokumentasi Sesi Blockout Roblox Studio & Penataan Folder Proyek
- **Aksi:** Mencatat seluruh progres eksplorasi *blockout* pulau melayang (*Sky Guardian Island*) di Roblox Studio serta penataan struktur folder proyek (`assets/` dan `misc/`) ke dalam [[Blockout Pulau Roblox dan Penataan Folder]].

## 2026-11-13 — Penamaan Ulang Catatan Sesi ke Format Deskriptif
- **Aksi:** Mengganti nama `Memory1.md` menjadi [[Harmonisasi List Asset dan Time Schedule]] di `brain/laptop-a/memories/` agar seluruh catatan sesi langsung menggambarkan isi pembahasannya tanpa awalan `Memory*`.

## 2026-11-13 — Dokumentasi Sesi List Asset & Jadwal TS ([[Harmonisasi List Asset dan Time Schedule]])
- **Aksi:** Menyusun catatan memori terstruktur [[Harmonisasi List Asset dan Time Schedule]] yang mendokumentasikan perbaikan workbook `TS & List Asset.xlsx` (penyelarasan ke Versi 2 yang ramping sesuai TS, penguncian jadwal VFX Particles pekan 10–11, penataan arsip detail aset di sheet pendukung, dan penyusunan prompt Blender MCP untuk model pulau).

## 2026-11-13 — Pembersihan Dokumen Lama (`brain/laptop-a/`)
- **Aksi:** Menghapus 3 dokumen `.md` usang/sementara dari `brain/laptop-a/`:
  - `prompt_naga.md` (remodel naga v3 sudah selesai di `Dragon_Model.blend`).
  - `prompt_v2.md` (prompt daratan lama 800×800m sudah tidak dipakai).
  - `gdd_peta_pulau.md` (dihapus untuk dibuat ulang sesuai konsep baru Kepulauan 3 Biome $1200 \times 1200\text{ m}$).
- **Dokumen Aktif Tersisa:** [[00 - Overview]], [[konteks]], [[prd]], [[rencana_desain_ui]], [[Tasks]], dan [[Devlog]].

## 2026-11-13 — Setup Obsidian Brain & Migrasi Dokumen Desain
- **Aksi:** Menginisialisasi Obsidian vault terpisah di `brain/` yang terhubung ke remote `ARDragon-Brain` dan terisolasi dari repo utama melalui `.gitignore`.
- **Migrasi:** Memindahkan dokumen desain dari root ke `brain/laptop-a/` dengan format YAML frontmatter & `[[wikilinks]]` (termasuk [[konteks]], [[prd]], [[rencana_desain_ui]], serta file lama `gdd_peta_pulau.md`, `prompt_naga.md`, dan `prompt_v2.md` yang kemudian dibersihkan).
- **Aturan Kerja:** Menetapkan pemisahan tulis (*anti-collision*) antara `laptop-a/` (desain & arsitektur) dan `laptop-b/` (eksekusi & pengembangan program) di `AGENTS.md`.
- **Status Project Saat Ini:** Fokus aktif roadmap berada pada **Sesi GUI** (implementasi di Unity sesuai [[rencana_desain_ui]]), disusul **Sesi Model Pulau** (remodel kepulauan 3 biome).
