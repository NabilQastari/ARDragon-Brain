---
title: Fanamir: The Sky Guardian — Project Overview
tags:
  - overview
  - fanamir
  - ar-dragon
  - game-design
  - architecture
status: active
updated: 2026-11-19
---

# 🐉 Fanamir: The Sky Guardian — Overview

> **Visi Utama:** Game *PC Exergaming* 3D semi-open-world berbasis Computer Vision (webcam standar tanpa sensor khusus) di mana pemain mengendalikan **Fanamir**, sang Naga Penjaga Langit (*Ancient Dew Water Dragon*), untuk menjelajahi kepulauan dan memulihkan 3 desa dari bencana lingkungan melalui gestur tubuh interaktif.
> *(Nama proyek lama: `AR Dragon: Sky Guardian`; namespace C# internal `DragonWingAR.*` dan folder teknis tetap dipertahankan demi kompatibilitas).*

---

## 🎯 Ringkasan Teknis & Stack

- **Engine Game:** Unity 6 (`6000.3.7f1`) — Universal Render Pipeline (URP), namespace C# `DragonWingAR.*`.
- **Computer Vision:** Python 3.10+ (`ARDragonSkyGuardian_CV/`) dengan PyTorch CUDA, `YOLOv8n-pose`, One-Euro Filter, dan head-crop mouth tracker.
- **Komunikasi Dual-Port UDP:**
  - `127.0.0.1:5052` — Telemetri pose tubuh 13-float CSV ($\ge 30\text{ FPS}$).
  - `127.0.0.1:5053` — Streaming video webcam PIP JPEG ($320 \times 180$).
- **Pipeline Aset 3D:** Blender 5.1 / 5.2 CLI + MCP (`Dragon_Model.blend`, remodel pulau 3 biome skala $1200 \times 1200\text{ m}$).

---

## 🗺️ Core Gameplay & Misi

1. **Navigasi Penerbangan 3D (Gestur Alfabet Y-T-A):**
   - **Pose Y:** Naik menanjak ($\uparrow$).
   - **Pose T:** Meluncur lurus mendatar ($\rightarrow$).
   - **Pose A:** Menukik tajam ($\downarrow$ + *Dive Boost*).
   - **Kemiringan Bahu/Tangan:** Belok kanan/kiri (*Yaw Turn* & *Roll Banking*).
2. **Pemulihan 3 Desa (Auto-Hover + Gestur Khusus):**
   - **Desa 1 (Smog Valley — Polusi Udara):** Kepakkan kedua tangan (*Flapping*).
   - **Desa 2 (Wildfire Woods — Kebakaran Hutan):** Buka mulut lebar atau tangkupkan tangan di dada (*Water Breath*).
   - **Desa 3 (Drought Plateau — Kekeringan Tanah):** Angkat kedua tangan tinggi di atas kepala (*Dew Rain*).
3. **Sistem Level:** Stage 1 (5 Menit / 300s), Stage 2 (10 Menit / 600s), Stage 3 (15 Menit / 900s), dan Free Play (∞) dengan evaluasi 1–3 bintang.

---

## 🧠 Peta Pengetahuan Vault (`brain/`)

### Dokumen Desain & Arsitektur ([[laptop-a/Tasks|Laptop A]])

#### 📌 Dokumen Inti & Pelacakan
- [[laptop-a/konteks]] — *Single Source of Truth* arsitektur teknis Unity, UDP, dan gestur.
- [[laptop-a/prd]] — *Product Requirements Document* & roadmap sesi pengembangan.
- [[laptop-a/Tasks]] & [[laptop-a/Devlog]] — Pelacakan tugas desain/arsitektur & catatan perkembangan Laptop A.

#### 🎨 UI & UX (`laptop-a/ui-ux/`)
- [[laptop-a/ui-ux/rencana_desain_ui|rencana_desain_ui]] — Spesifikasi UI Boot Flow (*Splash* & *Start Screen*), Lobby, Kalibrasi, HUD, & Finish Modal (*Fantasy Parchment-and-Ink Tabletop Card UI*).
- [[laptop-a/ui-ux/panduan_aset_ui_unity|panduan_aset_ui_unity]] — Panduan impor Unity 6, nilai *9-Slice* (`L, T, R, B`), & perakitan layar untuk **`140` PNG komponen + `11` PNG desain referensi `1920×1080`** di [`UI_Unity_Export/`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/).
- [[laptop-a/ui-ux/kontrak_integrasi_ui_dan_progress|kontrak_integrasi_ui_dan_progress]] — Matriks *Live vs Placeholder vs Dormant UI*, protokol sinkronisasi otomatis *Progress ↔ UI*, & prompt implementasi UI untuk Laptop B.
- [[laptop-a/ui-ux/planning_perbaikan_ui_ingame_dan_event_desa|planning_perbaikan_ui_ingame_dan_event_desa]] — Perencanaan perbaikan UI in-game: mode isolasi total saat textbox narasi (hanya textbox besar + quest objective) & redesain Side-Docked Challenge HUD di samping layar (agar naga di tengah 100% bebas terlihat).

#### 🎮 Gameplay & Mekanik (`laptop-a/gameplay-mechanics/`)
- [[laptop-a/gameplay-mechanics/spesifikasi_kecepatan_momentum_v5_dan_kontrol|spesifikasi_kecepatan_momentum_v5_dan_kontrol]] — Spesifikasi kecepatan & sistem momentum v5.0 ($16\text{--}150\text{ m/s}$), kemudi halus dengan deadzone $\pm 0.08$ & slider Settings, aturan escape desa (3x kepak Pyrewood/Solaria & Tuck Dive Zephyria), serta pendaratan mulus.
- [[laptop-a/gameplay-mechanics/spesifikasi_gameplay_boundary_dan_dual_input_mode|spesifikasi_gameplay_boundary_dan_dual_input_mode]] — Spesifikasi aturan batas laut ($>1100\text{ m}$ wind wall), pantulan tebing ($50\%$ momentum penalty), alur 2-tahap Pyrewood, evaluasi positif time-up, dan dual input mode (CV vs Keyboard).
- [[laptop-a/gameplay-mechanics/planning_post_animasi_vfx_ui_dan_kontrol|planning_post_animasi_vfx_ui_dan_kontrol]] — Perencanaan terpadu pasca-animasi: perbaikan & socketing VFX rig naga, pemolesan animasi/juice UI (spring, ghost bar, pop bintang), dan penyempurnaan mekanisme kontrol/ergonomi exergaming.
- [[laptop-a/gameplay-mechanics/mekanisme_tantangan_desa|mekanisme_tantangan_desa]] — Rancangan gameplay tantangan 3 desa (Zephyria, Pyrewood, Solaria), state mesin narasi vs tantangan, dan mode *Free Play*.
- [[laptop-a/gameplay-mechanics/planning_momentum_dan_revisi_kontrol|planning_momentum_dan_revisi_kontrol]] — Perancangan sistem momentum kontinu ($0–100\%$), Y-Glide, Steep Climb, Hover Flutter vs Slow Glide, dan spesifikasi paket delta ekspor Laptop B.
- [[laptop-a/gameplay-mechanics/arsitektur_sesi_5_gameloop_dan_scoring|arsitektur_sesi_5_gameloop_dan_scoring]] — Arsitektur pengontrol game loop (`GameManager.cs`), timer countdown, formula skor bintang berbasis progres global (33.4%, 66.7%, 100%), dan progresi level.
- [[laptop-a/gameplay-mechanics/prompt_eksekusi_sesi_5_gameloop_dan_scoring_laptop_b|prompt_eksekusi_sesi_5_gameloop_dan_scoring_laptop_b]] — Master prompt dan panduan eksekusi Sesi 5 Dasar bagi Laptop B (pembakuan bintang progres global, countdown 3s, TimeUp evaluation, dan progresi gembok level).
- [[laptop-a/gameplay-mechanics/planning_kamera_dinamis_dan_navigasi_pause|planning_kamera_dinamis_dan_navigasi_pause]] — Perancangan sistem kamera dinamis 5-state, aspect ratio lock, dan navigasi in-game pause ke kalibrasi.
- [[laptop-a/gameplay-mechanics/arsitektur_sistem_audio|arsitektur_sistem_audio]] — Perancangan komprehensif sistem audio Fanamir: BGM Dual-Style (Ghibli/Genshin Fantasy vs Cinematic Tribal), sistem cross-fade dinamis 2-channel, bus AudioMixer, dan katalog 18 SFX WAV prosedural.

#### 👁️ Computer Vision & Kontrol (`laptop-a/cv-control/`)
- [[laptop-a/cv-control/panduan_detail_pose_dan_sync_laptop_b|panduan_detail_pose_dan_sync_laptop_b]] — Panduan detail 17 pose kontrol & sinkronisasi implementasi Laptop B (Python CV + Unity).
- [[laptop-a/cv-control/analisis_dan_solusi_jitter_glitch_cv|analisis_dan_solusi_jitter_glitch_cv]] — Analisis akar penyebab getaran (jitter) & lompatan liar (glitch) kerangka CV, 4 lapisan stabilisasi, dan perancangan arsitektur hibrida YOLOv8 + MediaPipe Pose.
- [[laptop-a/cv-control/prompt_eksekusi_hybrid_cv_dan_timer_laptop_b|prompt_eksekusi_hybrid_cv_dan_timer_laptop_b]] — Master prompt instruksi siap salin bagi Laptop B untuk mengimplementasikan arsitektur hibrida YOLO + MediaPipe Pose Landmarker pada Crop ROI pemain dan perpanjangan timer stage 5/10/15 menit di Unity.
- [[laptop-a/cv-control/planning_audit_pose_dan_clean_cv_preview|planning_audit_pose_dan_clean_cv_preview]] — Pembersihan preview CV (FPS only) & protokol audit 15 pose berbasis screenshot.
- [[laptop-a/cv-control/planning_revisi_titik_wajah_dan_pov|planning_revisi_titik_wajah_dan_pov]] — Perencanaan revisi 3 titik wajah (hidung, dagu, mulut) & penyesuaian pose vertikal fly-up.
- [[laptop-a/cv-control/prompt_visual_pose_cv_kerangka|prompt_visual_pose_cv_kerangka]] — Prompt generator visual pose titik & kerangka CV (piktogram siluet resmi).

#### ✨ Visual & VFX (`laptop-a/visual-vfx/`)
- [[laptop-a/visual-vfx/spesifikasi_revisi_skybox_stylized|spesifikasi_revisi_skybox_stylized]] — Spesifikasi revisi skybox fantasi *stylized* (Ghibli/Genshin 4-stop sky gradient, awan kumulus + cirrus prosedural, piringan matahari + halo, awan 3D parallax `PF_StylizedClouds`, & pemasangan permanen di `Lighting Settings`).
- [[laptop-a/visual-vfx/spesifikasi_revisi_vfx_pilar_1|spesifikasi_revisi_vfx_pilar_1]] — Spesifikasi overhaul Pilar 1 (5 VFX reworked: Zephyria angin nyata, Lake aura uap, Solaria aura bervariasi + hujan lebih lama, Propeller banyak garis angin, Wingtip pita halus; + 3 VFX baru: Mouth Intake, Chest Gem Glow, Pyrewood Wildfire).
- [[laptop-a/visual-vfx/spesifikasi_dynamic_wings_dan_head_tracking|spesifikasi_dynamic_wings_dan_head_tracking]] — Spesifikasi final sistem sayap dinamis Dual-Track (Jalur A 1:1 vs Jalur B Locked + Wing Overdrive Steer), pelacakan kepala 3-sumbu (leher 25/35/40% + camera peek 10%), dan transfer trotting/propeller dari Three.js Studio.
- [[laptop-a/visual-vfx/rencana_skill_vfx_effekseer_laptop_b|rencana_skill_vfx_effekseer_laptop_b]] — Panduan & rencana instalasi skill `kanaworksai-hue/effekseer-vfx-skill` (Effekseer Unity 6 URP) untuk pembuatan VFX pemulihan 3 desa & penerbangan Fanamir di Laptop B.
- [[laptop-a/visual-vfx/planning_vfx_fov_skybox_dan_animasi|planning_vfx_fov_skybox_dan_animasi]] — Perencanaan terpadu sistem VFX Effekseer, Dynamic FOV ($60^\circ–85^\circ$), Skybox adaptif 3 Biome, & blueprint animasi Fanamir.
- [[laptop-a/visual-vfx/prompt_eksekusi_vfx_skybox_dan_fov_laptop_b|prompt_eksekusi_vfx_skybox_dan_fov_laptop_b]] — Master prompt dan panduan eksekusi teknis bagi Laptop B untuk mengimplementasikan Paket Visual Sesi 4B (VFX Effekseer, Dynamic FOV 60°–85°, & Skybox Dinamis 3 Biome).
- [[laptop-a/visual-vfx/arsitektur_animasi_naga_fanamir|arsitektur_animasi_naga_fanamir]] — Arsitektur sistem animasi tubuh prosedural naga (`DragonBodyAnimator.cs`: artikulasi rahang buka mulut `Bone_Jaw`, liukan ekor dinamis `Tail Sway`, lengkungan punggung, dan lipatan kaki cakar aerodinamis).
- [[laptop-a/visual-vfx/prompt_eksekusi_animasi_naga_laptop_b|prompt_eksekusi_animasi_naga_laptop_b]] — Master prompt dan panduan eksekusi implementasi `DragonBodyAnimator.cs` bagi Laptop B.
- [[laptop-a/visual-vfx/planning_perbaikan_animasi_dan_pose|planning_perbaikan_animasi_dan_pose]] — Cetak biru komprehensif perbaikan animasi & pose: siklus jalan 4 kaki di tanah, elevasi dada & buoyant hover, kepala dinamis CV 3-sumbu, dual-track sayap bebas 1:1 vs pose terkunci, kepakan bertenaga keras Propeller Dash, dan anatomi kaki terbang aerodinamis.
- [[laptop-a/visual-vfx/prompt_eksekusi_animasi_tahap_1_laptop_b|prompt_eksekusi_animasi_tahap_1_laptop_b]] — Master prompt dan panduan eksekusi teknis Tahap 1 bagi Laptop B: anatomi kaki terbang aerodinamis sesuai referensi resmi & elevasi dada +16° saat hover di `DragonBodyAnimator.cs`.
- [[laptop-a/visual-vfx/evaluasi_dan_revisi_pose_tahap_1|evaluasi_dan_revisi_pose_tahap_1]] — Evaluasi kritis & panduan perbaikan anomali visual pose Tahap 1: pelurusan kaki belakang sejajar ekor (anti-remuk) dan elevasi sikap tubuh hover (+18°..+22°) dengan kaki melayang santai.
- [[laptop-a/visual-vfx/studio_animasi_threejs_fanamir|studio_animasi_threejs_fanamir]] — Dokumentasi aplikasi web interaktif Three.js WebGL Fanamir 3D Animation & Pose Studio (`dragon_anim_studio/`) untuk preview visual $360^\circ$ dan generator kode C# Unity.

#### 🤝 Handoff & Eksekusi Laptop B (`laptop-a/handoff-laptop-b/`)
- [[laptop-a/handoff-laptop-b/laptop B Patch 1 planning|laptop B Patch 1 planning]] — Rencana detail perbaikan patch 1 untuk Laptop B (CV, dynamic camera, smooth UI, Sesi 4 & 5).
- [[laptop-a/handoff-laptop-b/checklist_finalisasi_pra_sesi_4|checklist_finalisasi_pra_sesi_4]] — Checklist finalisasi & prasyarat pra-Sesi 4 (mekanisme tantangan desa & kontrol CV).
- [[laptop-a/handoff-laptop-b/planning_optimasi_token_laptop_b|planning_optimasi_token_laptop_b]] — Cetak biru & panduan konfigurasi optimasi token (*The Optimal Lean Stack*: `.antigravityignore`, RTK CLI compressor, Ponytail-lite rules) untuk Laptop B.
- [[laptop-a/handoff-laptop-b/planning_graphify_laptop_b|planning_graphify_laptop_b]] — Panduan implementasi & eksekusi Graphify AST (`graphifyy`) terintegrasi Obsidian (`code-graph/`) untuk navigasi kode C#/Python dan penghematan token di Laptop B.
- [[laptop-a/handoff-laptop-b/prompt_eksekusi_graphify_laptop_b|prompt_eksekusi_graphify_laptop_b]] — Master prompt instruksi siap salin bagi Laptop B untuk menginstal `graphifyy`, mengekstrak AST C# & Python, dan memverifikasi integrasi graf di Obsidian.
- [[laptop-a/handoff-laptop-b/panduan_screenshot_mandiri_dan_laporan|panduan_screenshot_mandiri_dan_laporan]] — Panduan bagi Laptop B untuk melakukan *screenshot* mandiri otomatis (Unity C#, Python CV, OS-level) dan menampungnya di `docs/figures/raw_screenshots/` sebagai bahan pembaruan Laporan Capstone.
- [[laptop-a/handoff-laptop-b/prompt_eksekusi_sistem_audio_laptop_b|prompt_eksekusi_sistem_audio_laptop_b]] — Master prompt instruksi siap salin bagi Laptop B untuk mengimpor aset audio (8 BGM + 22 SFX), menyusun AudioMixer, mengimplementasikan `AudioManager.cs`, dan menghapus penanda `// TODO [SYNC-AUDIO]`.
- [[laptop-a/handoff-laptop-b/prompt_eksekusi_animasi_final_laptop_b|prompt_eksekusi_animasi_final_laptop_b]] — Master prompt instruksi siap salin bagi Laptop B untuk mengimplementasikan Dual-Track Dynamic Wings, pelacakan kepala 3-sumbu, kamera melirik, dan siklus trotting kaki di Unity C# & Python CV.
- [[laptop-a/handoff-laptop-b/prompt_eksekusi_revisi_vfx_pilar_1_laptop_b|prompt_eksekusi_revisi_vfx_pilar_1_laptop_b]] — Master prompt eksekusi Pilar 1 + paket `Export_VFX_Revisi_LaptopB.zip` (8 skrip `.py` + `briefs`, koreksi socket `Neck_01`/`Head`/`Wing_Finger_Outer` + `TrailRenderer`, wildfire Biome 2, & hujan Solaria `14s`) untuk Laptop B.
- [[laptop-a/handoff-laptop-b/prompt_eksekusi_revisi_skybox_laptop_b|prompt_eksekusi_revisi_skybox_laptop_b]] — Master prompt eksekusi revisi skybox fantasi *stylized* (`DynamicBiomeSkybox.shader` berawan kumulus Ghibli/Genshin, palet 7 state `BiomeAtmosphereController.cs`, `PF_StylizedClouds` 3D parallax, & pemasangan di `Lighting Settings`) untuk Laptop B.
- [[laptop-a/handoff-laptop-b/prompt_eksekusi_integrasi_skybox_dan_awan_eksternal_laptop_b|prompt_eksekusi_integrasi_skybox_dan_awan_eksternal_laptop_b]] — Master prompt integrasi aset eksternal pilihan user (**AllSky Free**, **Fantasy Skybox FREE**, & awan 3D **Quaternius Modular Platformer Pack**) ke `BiomeAtmosphereController.cs`, `PF_StylizedClouds`, & `Lighting Settings` di Laptop B.
- [[laptop-a/handoff-laptop-b/prompt_eksekusi_model_dan_animasi_20_pose_laptop_b|prompt_eksekusi_model_dan_animasi_20_pose_laptop_b]] — Master prompt & paket ZIP `Export_DragonModel_Anim20_LaptopB.zip` (model `Dragon_Model.fbx` 47 tulang bebas *weight bleed/stretching* & dorsal kepala mulus + helper `FromStudioEulerXYZ` & referensi C# seluruh 20 pose `v15` termasuk `#15B-1 Water Siphon`) untuk eksekusi di Laptop B.

### Eksekusi & Pengembangan Program ([[laptop-b/Tasks|Laptop B]])
- [[laptop-b/Tasks]] & [[laptop-b/Devlog]] — Pelacakan tugas implementasi kode, integrasi Unity/Python, & log pengujian Laptop B.

---

## 🤝 Aturan Kolaborasi Dual-Laptop
- **Laptop A (`brain/laptop-a/`):** Fokus desain, ide, GDD, UI/UX, aset konsep, dan arsitektur.
- **Laptop B (`brain/laptop-b/`):** Fokus eksekusi kode Unity C#, Python CV, build, dan debugging.
- Setiap laptop hanya menulis di foldernya masing-masing dan membaca folder rekan secara *read-only* untuk sinkronisasi konteks.

---

## ⚡ Command Khusus Bersama: `"jadikan sesi ini memori"`

Jika pengguna mengetik **`jadikan sesi ini memori`** di sesi chat mana pun (baik di **Laptop A** maupun **Laptop B**), AI Agent **WAJIB** menjalankan alur berikut:

1. **Tentukan Identitas & Folder Target:**
   - Cek `AGENTS.md` di root project untuk menentukan identitas laptop aktif:
     - **Laptop A** $\rightarrow$ tulis hanya di `brain/laptop-a/` (`owner: Laptop A`, tag `laptop-a`).
     - **Laptop B** $\rightarrow$ tulis hanya di `brain/laptop-b/` (`owner: Laptop B`, tag `laptop-b`).
   - **Dilarang keras** mengedit atau menghapus file di folder laptop lain.
2. **Sinkronisasi Awal:** Jalankan `git -C brain pull` (jika ada koneksi internet).
3. **Penamaan File Ringkas & Deskriptif:**
   - Simpan di `brain/<laptop-aktif>/memories/<Nama Ringkas Sesi>.md` (buat folder `memories/` jika belum ada).
   - Gunakan nama file yang **ringkas, padat, dan langsung menggambarkan topik utama sesi** (idealnya **3–6 kata** / maks ~50 karakter, misal `Pembuatan Model Naga 3D v3.md` atau `Integrasi MCP FigCraft.md`).
   - Tanpa awalan `Memory*`, hindari koma atau daftar detail panjang di nama file (rincian teknis ditaruh di isi catatan), bebas karakter terlarang Windows `\ / : * ? " < > |`, dan tidak menimpa file yang sudah ada.
4. **Format Catatan Obsidian:**
   - Gunakan **YAML frontmatter** (`title`, `type: session-memory`, `tags`, `date`, `owner`, `related`) dan `[[wikilinks]]`.
   - Susun isi ke dalam 5 bagian wajib berdasarkan fakta yang benar-benar terjadi di sesi tersebut (tanpa mengarang):
     1. `## 1. Tujuan & Konteks Sesi`
     2. `## 2. Keputusan Desain / Arsitektur / Teknis`
     3. `## 3. Ringkasan Eksekusi & Perubahan File/Aset`
     4. `## 4. Detail Penting yang Harus Diingat` (angka, koordinat, aturan, nama simbol/skrip, batasan, preferensi user)
     5. `## 5. Tugas Tersisa & Langkah Selanjutnya`
   - Sertakan footer navigasi `[[00 - Overview]]`, `[[Tasks]]`, `[[Devlog]]`, serta catatan terkait di folder laptop masing-masing.
5. **Update Log & Push Git:**
   - Tambahkan entri ringkas di `brain/<laptop-aktif>/Devlog.md` yang menautkan `[[<Nama Deskriptif Sesi>]]`, serta perbarui `brain/<laptop-aktif>/Tasks.md` bila ada tugas yang selesai atau baru muncul.
   - Jalankan `git -C brain add`, `commit` dengan format `docs(<laptop-aktif>): add session note - <topik singkat>`, dan `push` ke `origin main`, lalu laporkan nama file yang dibuat serta status push Git-nya.
