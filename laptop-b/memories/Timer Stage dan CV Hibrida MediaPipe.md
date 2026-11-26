---
title: "Timer Stage dan CV Hibrida MediaPipe"
type: session-memory
tags:
  - session-memory
  - laptop-b
  - hybrid-cv
  - mediapipe
  - yolov8
  - pose-stabilizer
  - game-loop
date: 2026-11-24
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[Implementasi Sesi 5 Game Loop dan Scoring]]"
  - "[[Penyelarasan 17 Pose dan Propeller Dash]]"
  - "[[laptop-a/cv-control/analisis_dan_solusi_jitter_glitch_cv]]"
  - "[[laptop-a/gameplay-mechanics/arsitektur_sesi_5_gameloop_dan_scoring]]"
---

# Timer Stage dan CV Hibrida MediaPipe

## 1. Tujuan & Konteks Sesi
- Melaksanakan handoff Laptop A (`[[laptop-a/cv-control/analisis_dan_solusi_jitter_glitch_cv]]` & `[[laptop-a/gameplay-mechanics/arsitektur_sesi_5_gameloop_dan_scoring]]`) pada dua sisi sekaligus:
  1. Memperpanjang durasi timer misi stage di Unity 6 menjadi **5 / 10 / 15 menit** (`300s / 600s / 900s`) beserta pembaruan label kartu Lobby (`SCREEN 00`).
  2. Membangun arsitektur hibrida Computer Vision di Python (**YOLOv8n-pose** sebagai otak makro bounding box & baseline tubuh + **MediaPipe Pose Landmarker** pada Crop ROI pemain untuk presisi mikro sendi siku/pergelangan), disertai penyetelan ulang One-Euro Filter dan *Kinematic Forearm Clamping* untuk meredam jitter saat diam maupun glitch lompatan ke latar belakang.

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Perpanjangan Durasi Timer Stage (`Unity 6 C#`):**
   - `GameManager.GetStageTimeLimit(stageIndex)` dan default field `stageTimeLimitSeconds` / `timeRemaining` diperbarui menjadi Stage 1 = `300f`, Stage 2 = `600f`, Stage 3 = `900f`, dan Free Play = `0f` ($\infty$), dengan aturan *Narrative Time-Freeze* saat `PF_QuestNarrativeBox` aktif tetap terjaga.
   - `MissionsPanelUI.cs` & `UISceneAndPrefabBuilder.cs` diperbarui agar label kartu menampilkan `"1 VILLAGE · 5 MIN"`, `"2 VILLAGES · 10 MIN"`, dan `"3 VILLAGES · 15 MIN"` saat belum ada rekor waktu (`best <= 0f`), serta `"{limitSec}s · BEST MM:SS"` (`300s`/`600s`/`900s`) saat rekor tersimpan di `PlayerPrefs`.
2. **Arsitektur Hibrida YOLOv8 + MediaPipe Pose (`ARDragonSkyGuardian_CV/`):**
   - Pengecekan langsung di `venv/` menunjukkan `mediapipe==1.1.1` tidak menyediakan `mp.solutions` (`hasattr(mp, "solutions") == False`), sedangkan `mediapipe.tasks.python.vision.PoseLandmarker` dan model `pose_landmarker_lite.task` tersedia penuh. Oleh karena itu `HybridPoseTracker` menggunakan Tasks API (`RunningMode.IMAGE`) sebagai jalur utama (dengan resolusi path relatif terhadap `__file__`) dan fallback kondisional ke `mp.solutions.pose` hanya bila atribut `solutions` ada.
   - `refine_keypoints_with_mediapipe(frame_bgr, yolo_kpts, yolo_bbox)` memotong ROI pemain berdasarkan `boxes[best_idx]` dengan padding `+12%` (dilewati bila `crop_w < 60` atau `crop_h < 60`), menjalankan inferensi MediaPipe pada ROI crop, lalu memetakan landmark `MP 13..16` (siku & pergelangan) ke indeks COCO `7..10` jika `visibility > 0.35`. Karena keduanya memakai konvensi anatomis citra yang sama pasca `cv2.flip(frame, 1)`, pemetaan 1:1 selaras dengan penukaran mirror `IDX_RIGHT_*` / `IDX_LEFT_*` di `active_cv_sender.py`.
3. **Penyetelan Stabilisasi 4 Lapis (`pose_stabilizer.py`):**
   - **One-Euro Filter:** Torso/kepala (`ls`, `rs`, `lh`, `rh`, `nose`) = `0.30 Hz / beta 0.03`, siku (`le`, `re`) = `0.40 Hz / beta 0.06`, pergelangan (`lw`, `rw`) = `0.40 Hz / beta 0.12`.
   - **Confidence Gating:** `CONFIDENCE_MIN = 0.28`, `BLUR_WRIST_CONF_MIN = 0.12` (hanya diizinkan jika jarak pergelangan ke siku berada di dalam `max_forearm`), `BLUR_ELBOW_CONF_MIN = 0.16`.
   - **Kinematic Forearm Clamping:** Jarak pergelangan ke siku dibatasi `MAX_FOREARM_CLAMP_MULT = 1.10` kali `ref_forearm_length` (`DEFAULT_FOREARM_RATIO = 0.72` dari lebar bahu `sw`, beradaptasi lembut via EMA pada rentang `0.55..0.90 * sw` saat `conf >= 0.50`) pada **seluruh tingkat confidence**.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Unity C# & Scene (`AR Dragon Sky Guardian/`):**
  - Edit `Assets/_Scripts/Gameplay/GameManager.cs` (`300f/600f/900f`), `Assets/_Scripts/Gameplay/VillageZoneController.cs` (`_stageTimeLimitSeconds = 300f`).
  - Edit `Assets/_Scripts/UI/MissionsPanelUI.cs` & `Assets/Editor/UISceneAndPrefabBuilder.cs` (`5 MIN / 10 MIN / 15 MIN` & `300/600/900`).
  - Perbarui nilai serialized dan simpan `Assets/_Scenes/MainIsland_Gameplay.unity` (`limit=300, rem=300`) serta `Assets/_Scenes/MainMenu_Lobby.unity`.
- **Python CV (`ARDragonSkyGuardian_CV/`):**
  - Buat `hybrid_pose_tracker.py` (`HybridPoseTracker`).
  - Edit `active_cv_sender.py` (inisialisasi `HybridPoseTracker`, ekstraksi `player_bbox` dari `boxes[best_idx]`, pemanggilan `refine_keypoints_with_mediapipe`, serta `hybrid_tracker.close()` di `finally`).
  - Edit `pose_stabilizer.py` (tuning One-Euro, `CONFIDENCE_MIN = 0.28`, `BLUR_WRIST_CONF_MIN = 0.12`, dan Kinematic Forearm Clamping `1.10 * ref_forearm_length`).
  - Sesuaikan `test_all_17_poses_alignment.py` (`time.sleep(0.032)` $\to$ `0.025` pada loop foreshortening `#14`) dan `test_phase5_validation.py` (`clamped_y > 0.42` $\to$ `> 0.41`).
- **Verifikasi yang Dijalankan:**
  - **Python CV (`venv\Scripts\python.exe`):** `test_all_17_poses_alignment.py`, `test_momentum_and_control_revision.py`, `test_additional_gestures.py` (`10/10`), `test_e2e_flight_physics.py` (`6/6`), `test_phase1_validation.py` (`8/8`), `test_phase5_validation.py` (`7/7`), dan `test_tahap1_face_tracking.py` (`7/7`) seluruhnya **100% PASS**; smoke test `HybridPoseTracker` (`use_mp: True`) & clamp check `RobustKeypointTracker` (`0.1400 <= 0.1580`) lulus; serta uji startup `active_cv_sender.py --headless --cam 0` pada `USB2.0 HD UVC WebCam (1280x720)` + CUDA YOLOv8n-pose + `HybridPoseTracker` berjalan bersih.
  - **Unity Play Mode (`6000.3.7f1`):** `MainIsland_Gameplay.unity` terverifikasi `05:00 · VILLAGES 0/1`, `10:00 · VILLAGES 0/2`, `15:00 · VILLAGES 0/3`; `Session5GameLoopVerificationRunner.RunVerificationMenu()` **100% PASS**; dan `MainMenu_Lobby.unity` menampilkan `300s`/`600s`/`900s` dengan `0` console errors & `0` console warnings.

---

## 4. Detail Penting yang Harus Diingat
- Di `ARDragonSkyGuardian_CV/venv/` (`mediapipe==1.1.1`), modul `mp.solutions` tidak ada; selalu gunakan `mediapipe.tasks.python.vision.PoseLandmarker` dengan `pose_landmarker_lite.task` (atau guard `hasattr(mp, "solutions")` bila menulis fallback).
- Pada Windows, `time.sleep(0.032)` aktualnya dapat tertahan hingga `~42.8 ms` mengikuti tick timer OS; pengujian kecepatan sudut berbasis `time.perf_counter()` seperti `#14 Propeller Dash` (`min_angular_velocity = 2.85 rad/s`) sensitif terhadap selisih tick tersebut sehingga `time.sleep(0.025)` menjaga step simulasi di kisaran `~31 ms`.
- Penyetelan `min_cutoff = 0.40 Hz, beta = 0.12` pada `lw`/`rw` membuat lonjakan 1 frame teredam kuat (`0.40 -> 0.415` untuk step `0.08`), sehingga ambang uji 1-frame cepat berada di `> 0.41`.
- **Konteks Kronologis Vault:** Pekerjaan sesi ini menjadi dasar bagi penyempurnaan kontrol lanjutan (perluasan crop atas `HybridPoseTracker` `+28%` dan pemisahan `#07` vs `#11`) serta sesi Audio, Animasi Final, Gameplay Boundary, dan Skybox Eksternal yang tercatat pada entri-entri berikutnya di `[[Devlog]]`.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- Uji visual langsung oleh pengguna di depan webcam pada jendela preview `active_cv_sender.py` (T-Pose `Glide` diam dan putaran cepat `Propeller Dash`).
- Melanjutkan backlog aktif di `[[Tasks]]`: **Sesi 5 Lanjutan — Build Windows Standalone & Launcher `start_game.bat`**.

---
**Navigasi Obsidian:** [[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[Implementasi Sesi 5 Game Loop dan Scoring]] · [[Penyelarasan 17 Pose dan Propeller Dash]]
