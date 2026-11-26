---
title: "Redesain UI InGame dan Stabilisasi Hibrida CV"
type: session-memory
tags:
  - session-memory
  - laptop-a
  - ui-ux
  - in-game-hud
  - village-challenge
  - computer-vision
  - mediapipe
  - yolo
  - game-loop
date: 2026-11-26
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[laptop-a/ui-ux/planning_perbaikan_ui_ingame_dan_event_desa]]"
  - "[[laptop-a/cv-control/analisis_dan_solusi_jitter_glitch_cv]]"
  - "[[laptop-a/cv-control/prompt_eksekusi_hybrid_cv_dan_timer_laptop_b]]"
  - "[[laptop-a/gameplay-mechanics/arsitektur_sesi_5_gameloop_dan_scoring]]"
---

# 🧠 Redesain UI InGame dan Stabilisasi Hibrida CV

## 1. Tujuan & Konteks Sesi
- **Pembersihan Tampilan In-Game saat Dialog Narasi:** Merespons keluhan pengguna mengenai elemen UI penerbangan normal yang masih bertumpuk saat textbox narasi desa muncul; menyusun mode isolasi total (*Full Narrative Isolation Mode*) di mana hanya Textbox Narasi dan Quest Objective yang aktif, serta memperbesar ukuran box ($1440 \times 300\text{ px}$) dan tipografi dialog ($34\text{ pt}$) untuk keterbacaan *exergaming* jarak jauh ($1.5\text{--}2.5\text{ m}$).
- **Pembebasan Pandangan Aksi Naga saat Event Desa:** Menggantikan kartu modal perkamen raksasa $720 \times 670\text{ px}$ di tengah layar (`SCREEN 03` lama) yang menutupi badan naga Fanamir dengan **Side-Docked Challenge HUD** ramping ($400 \times 620\text{ px}$) di sisi kanan layar, membebaskan pandangan visual naga di tengah layar $100\%$ tanpa halangan.
- **Perpanjangan Durasi Timer Misi Stage 1–3:** Menyelaraskan batas waktu bermain menjadi $5\text{ Menit}$ (Stage 1 / 300s), $10\text{ Menit}$ (Stage 2 / 600s), dan $15\text{ Menit}$ (Stage 3 / 900s) demi mengakomodasi ritme kebugaran fisik *exergaming* dan penjelajahan santai kepulauan 3 biome.
- **Resolusi Jitter & Glitch Computer Vision:** Mengaudit masalah getaran (*jitter*) dan lompatan liar sendi (*glitch/snapping*), merumuskan 4 lapisan stabilisasi, serta membakukan arsitektur hibrida **YOLOv8 (Otak Utama Makro)** + **MediaPipe Pose (Presisi Mikro Sendi 3D)** menggunakan dependensi `mediapipe==1.1.1` dan `pose_landmarker_lite.task` yang sudah ada di virtual environment Laptop B.

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Full Narrative Isolation Mode (`InGameFlightHUD.cs`):**
   - Saat dialog narasi desa dibuka (`NarrativeActive`), seluruh widget HUD normal dinonaktifkan total (`SetActive(false)`): `MissionCapsule` (timer), `Btn_EscPause`, `GlobalVitalityBar`, `TelemetryDock`, `MomentumDock`, klaster `PIP_Camera`, `WaypointCompass`, dan *world-space marker billboard*.
   - Hanya **dua elemen** yang boleh tampil: `QuestObjectivePanel` (kiri atas) dan `PF_QuestNarrativeBox` (bawah tengah).
2. **Redesain Textbox Narasi Ekstra Besar (`PF_QuestNarrativeBox.prefab`):**
   - Dimensi diperbesar dari $1236 \times 212\text{ px}$ menjadi **$1440 \times 300\text{ px}$** (anchor *Bottom-Center*, `anchoredPosition = (0, 36)`).
   - Teks dialog dinaikkan drastis dari $22\text{ pt}$ ke **$34\text{ pt}$** (*Nunito ExtraBold*, line spacing $1.2$), Speaker Tag **$28\text{ pt}$**, teks petunjuk aksi **$26\text{ pt}$**, dan bar countdown auto-play dipertebal menjadi $14\text{ px}$.
3. **Redesain Side-Docked Challenge HUD (`PF_VillageChallengeOverlay.prefab`, `SCREEN 03R`):**
   - Menghapus modal tengah $720 \times 670\text{ px}$ agar area tengah layar ($x = 350\dots 1500$, bentang $> 1150\text{ px}$) **100% bebas terbuka** untuk melihat animasi naga mengepak, menyemburkan air, atau ritual embun.
   - Menempatkan panel samping ramping $400 \times 620\text{ px}$ di kanan tengah (*Middle-Right*, `anchoredPosition = (-30, 0)`).
   - Menyediakan komponen: Header Krisis Desa, Kartu Indikator Pose Target (ikon $84\text{ px}$ + teks komando $28\text{ pt}$ + Live Detection Feedback Badge hijau `● GERAKAN TERDETEKSI!` vs amber `○ MENUNGGU GERAKAN`), Progress Bar tebal $48\text{ px}$ no-decay, dan Escape Hint chip.
4. **Perpanjangan Timer Misi (`GameManager.cs` / `MissionsPanelUI.cs`):**
   - Stage 1: `300s` ($5\text{ Menit}$).
   - Stage 2: `600s` ($10\text{ Menit}$).
   - Stage 3: `900s` ($15\text{ Menit}$).
   - Free Play: `0s` / $\infty$ (tanpa batas waktu).
   - Aturan jeda narasi: Timer otomatis pause saat dialog aktif dan resume setelah selesai.
5. **Arsitektur Hibrida YOLOv8 + MediaPipe Pose (`hybrid_pose_tracker.py`):**
   - **YOLOv8:** Berfungsi sebagai *Otak Makro* untuk deteksi orang di seluruh frame, penguncian Bounding Box pemain utama (Primary Player Auto-Lock kebal background clutter), dan estimasi kemudi torso (*Lean Steering*).
   - **MediaPipe Pose Landmarker:** Memproses gambar crop pemain (~360x480 px) secara *zero-delay* ($\sim 8\text{--}10\text{ ms}$) menggunakan `pose_landmarker_lite.task` untuk menghasilkan 33 landmark 3D.
   - Siku dan pergelangan tangan diambil dari MediaPipe (mengeliminasi 100% jitter dan snapping) dengan dukungan kedalaman $Z$ untuk pose melipat di dada (`#13 Tuck Dive`, `#15B Water Breath`).
   - Disertai batasan kinematik panjang tulang (*Kinematic Bone-Length Clamping* jarak siku-wrist max $1.10\times$) dan tuning One-Euro Filter (`min_cutoff=0.40 Hz`, `beta=0.12`).

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Dokumen Perencanaan UI:**
  - `brain/laptop-a/ui-ux/planning_perbaikan_ui_ingame_dan_event_desa.md` *(Baru)*: Spesifikasi detail isolasi UI narasi, perbesaran textbox, dan redesain Side-Docked Challenge HUD.
- **Dokumen Analisis & Arsitektur CV:**
  - `brain/laptop-a/cv-control/analisis_dan_solusi_jitter_glitch_cv.md` *(Baru)*: Analisis 5 faktor akar penyebab jitter/glitch, 4 lapisan stabilisasi, dan perancangan strategi hibrida YOLO + MediaPipe.
- **Master Prompt Eksekusi Siap Salin untuk Laptop B:**
  - `brain/laptop-a/cv-control/prompt_eksekusi_hybrid_cv_dan_timer_laptop_b.md` *(Baru)*: Panduan langkah teknis Unity C# (GameManager, MissionsPanelUI) dan Python (`hybrid_pose_tracker.py`, `active_cv_sender.py`, `pose_stabilizer.py`).
- **Pembaruan Dokumen Inti & Game Loop:**
  - `brain/laptop-a/gameplay-mechanics/arsitektur_sesi_5_gameloop_dan_scoring.md`: Penyelarasan tabel durasi timer Stage 1 = 300s, Stage 2 = 600s, Stage 3 = 900s.
  - `brain/00 - Overview.md`: Penyelarasan durasi stage pada ringkasan sistem level dan penambahan tautan dokumen baru.
  - `brain/laptop-a/Tasks.md` & `brain/laptop-a/Devlog.md`: Pembaruan status pelacakan tugas dan catatan kronologis.
- **Verifikasi Eksekusi Awal di Laptop B:**
  - Laptop B telah mengimplementasikan perbaikan UI in-game (isolasi narasi & Side-Docked Challenge HUD) dan berhasil diverifikasi 100% PASS Play Mode dengan tangkapan layar mandiri (`SS_UI_Narrative_Isolation.png`, `SS_UI_SideDocked_Challenge_Waiting.png`, `SS_UI_SideDocked_Challenge_Flapping.png`).

---

## 4. Detail Penting yang Harus Diingat
- **Dimensi Textbox Narasi Baru:** $1440 \times 300\text{ px}$, anchor *Bottom-Center* `(0.5, 0)`, `anchoredPosition = (0, 36)`.
- **Tipografi Narasi Exergaming:** Font dialog `Nunito-ExtraBold 34 pt` (line spacing $1.2$), Speaker Tag `Fredoka-Bold 28 pt`, Petunjuk Aksi `Fredoka-Bold 26 pt`.
- **Dimensi Side-Docked Challenge HUD:** $400 \times 620\text{ px}$, anchor *Middle-Right* `(1.0, 0.5)`, `anchoredPosition = (-30, 0)`.
- **Area Bebas Naga di Tengah Layar:** Rentang horizontal $x = 350\dots 1500$ ($> 1150\text{ px}$) bersih dari segala UI saat tantangan berlangsung.
- **Konfigurasi Timer Baru:** Stage 1 = `300f` (5m), Stage 2 = `600f` (10m), Stage 3 = `900f` (15m), Free Play = `0f` ($\infty$).
- **Dependensi MediaPipe Eksisting:** File `pose_landmarker_lite.task` dan library `mediapipe==1.1.1` sudah terinstal di `ARDragonSkyGuardian_CV/venv/`.
- **Parameter Stabilisasi CV:**
  - One-Euro Filter: Badan inti (`min_cutoff=0.30, beta=0.03`), Siku (`min_cutoff=0.40, beta=0.06`), Pergelangan tangan (`min_cutoff=0.45, beta=0.12`).
  - Kinematic Bone Clamping: $\text{dist}(\text{elbow}, \text{wrist}) \le 1.10 \times L_{\text{forearm}}$.
  - Confidence Threshold Minimum: $\ge 0.30$ (menolak noise background $< 0.30$).

---

## 5. Tugas Tersisa & Langkah Selanjutnya
1. Eksekusi `ARDragonSkyGuardian_CV/hybrid_pose_tracker.py` di Laptop B sesuai `prompt_eksekusi_hybrid_cv_dan_timer_laptop_b.md`.
2. Verifikasi live preview webcam untuk memastikan tangan tenang (*rock-solid*) pada T-Pose dan bebas dari lompatan liar saat gerakan cepat.
3. Verifikasi hitungan mundur timer $05:00$, $10:00$, dan $15:00$ di Play Mode Unity `MainIsland_Gameplay.unity`.
4. Persiapan bab pengujian dan penyusunan kuesioner kebugaran fisik pada Laporan Capstone.

---

[[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[laptop-a/ui-ux/planning_perbaikan_ui_ingame_dan_event_desa]] · [[laptop-a/cv-control/analisis_dan_solusi_jitter_glitch_cv]] · [[laptop-a/cv-control/prompt_eksekusi_hybrid_cv_dan_timer_laptop_b]]
