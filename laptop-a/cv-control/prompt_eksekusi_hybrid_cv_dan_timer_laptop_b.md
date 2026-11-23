---
title: "Master Prompt Eksekusi: Arsitektur Hibrida YOLO + MediaPipe & Perpanjangan Timer Stage (Laptop B)"
tags:
  - handoff
  - prompt-eksekusi
  - laptop-b
  - hybrid-cv
  - mediapipe
  - yolo
  - game-loop
status: ready-for-laptop-b
owner: Laptop A
updated: 2026-11-24
related:
  - "[[00 - Overview]]"
  - "[[analisis_dan_solusi_jitter_glitch_cv]]"
  - "[[arsitektur_sesi_5_gameloop_dan_scoring]]"
  - "[[panduan_detail_pose_dan_sync_laptop_b]]"
---

# 🚀 Master Prompt Eksekusi: Arsitektur Hibrida YOLO + MediaPipe & Perpanjangan Timer Stage
## Handoff Resmi dari Laptop A ke Laptop B

> **Panduan bagi Pengguna:** Salin seluruh blok markdown di bawah garis horizontal ini dan berikan langsung ke AI Agent di **Laptop B** untuk dieksekusi secara terpadu.

---

```markdown
Tugas: Implementasikan Perpanjangan Timer Misi Stage (5/10/15 Menit) di Unity 6 dan Bangun Arsitektur Hibrida Computer Vision (YOLOv8 sebagai Otak Makro + MediaPipe sebagai Presisi Mikro Sendi) di Python sesuai dokumen desain resmi `[[laptop-a/cv-control/analisis_dan_solusi_jitter_glitch_cv]]` dan `[[laptop-a/gameplay-mechanics/arsitektur_sesi_5_gameloop_dan_scoring]]`.

---

### 1. BACA ATURAN & DOKUMEN RUJUKAN
1. Jalankan `git -C brain pull`.
2. Baca dokumen rujukan dari Laptop A:
   - `brain/laptop-a/cv-control/analisis_dan_solusi_jitter_glitch_cv.md` (Analisis 5 akar penyebab jitter/glitch & arsitektur hibrida 4 lapis).
   - `brain/laptop-a/gameplay-mechanics/arsitektur_sesi_5_gameloop_dan_scoring.md` (Konfigurasi timer 300s/600s/900s & aturan jeda narasi).
3. Pahami kode eksisting di `AR Dragon Sky Guardian/` dan `ARDragonSkyGuardian_CV/`:
   - Unity: `GameManager.cs`, `DragonGameSettings.cs`, `MissionsPanelUI.cs`, `InGameFlightHUD.cs`.
   - Python: `active_cv_sender.py`, `pose_stabilizer.py`, `motion_recognizer.py`.
   - Perhatikan bahwa `mediapipe==1.1.1` dan file model `pose_landmarker_lite.task` SUDAH TERSEDIA di virtual environment `venv/`.

---

### 2. EKSEKUSI BAGIAN 1: PERPANJANG DURASI TIMER STAGE (UNITY 6 C#)

1. **Perbarui Nilai Default Durasi Timer (`GameManager.cs` / `DragonGameSettings.cs`):**
   Ubah durasi batas waktu stage menjadi:
   - **Stage 1 (Zephyria):** `300f` detik ($5\text{ Menit}$).
   - **Stage 2 (Zephyria + Pyrewood):** `600f` detik ($10\text{ Menit}$).
   - **Stage 3 (Seluruh 3 Desa):** `900f` detik ($15\text{ Menit}$).
   - **Free Play:** Tetap `0f` (tanpa batas waktu / $\infty$).

2. **Perbarui Tampilan Kartu di Lobby (`MissionsPanelUI.cs` / `SCREEN 00`):**
   - Update label tag durasi pada kartu level Lobby:
     - Kartu Stage 01: `"5 MIN"` / `"300s"`
     - Kartu Stage 02: `"10 MIN"` / `"600s"`
     - Kartu Stage 03: `"15 MIN"` / `"900s"`

3. **Verifikasi Play Mode Unity:**
   - Jalankan `MainIsland_Gameplay.unity` di Stage 1: Pastikan Kapsul Timer (`MissionCapsule`) di HUD atas memulai hitungan mundur dari `05:00`.
   - Pastikan jeda waktu (*freeze*) tetap berfungsi saat naga masuk ke silinder dialog tetua desa (`PF_QuestNarrativeBox` aktif).

---

### 3. EKSEKUSI BAGIAN 2: ARSITEKTUR HIBRIDA YOLO + MEDIAPIPE (PYTHON CV)

Tujuan utama: Menghilangkan 100% getaran (*jitter*) saat diam dan mencegah pergelangan tangan melompat (*glitch*) ke latar belakang, dengan memadukan **YOLOv8** (pelacakan orang makro & orientasi kemudi tubuh) dengan **MediaPipe Pose** (presisi sub-piksel sendi tangan 3D).

1. **Buat Modul Pelacak Hibrida `ARDragonSkyGuardian_CV/hybrid_pose_tracker.py`:**
   Implementasikan kelas `HybridPoseTracker` yang mengawinkan YOLOv8 dan MediaPipe Pose Landmarker pada Crop ROI pemain:

   ```python
   # ARDragonSkyGuardian_CV/hybrid_pose_tracker.py
   import os
   import cv2
   import numpy as np
   import mediapipe as mp

   class HybridPoseTracker:
       """
       Arsitektur Hibrida:
       - YOLOv8: Deteksi orang makro, Primary Player Auto-Lock Bounding Box, & Lean Steering.
       - MediaPipe: Presisi mikro 33 landmark 3D (anti-jitter, anti-glitch ke background).
       """
       def __init__(self, model_task_path="pose_landmarker_lite.task"):
           self.use_mediapipe = False
           self.mp_landmarker = None
           
           # Inisialisasi MediaPipe Tasks Pose Landmarker jika file .task ada
           if os.path.exists(model_task_path):
               try:
                   from mediapipe.tasks import python
                   from mediapipe.tasks.python import vision
                   base_options = python.BaseOptions(model_asset_path=model_task_path)
                   options = vision.PoseLandmarkerOptions(
                       base_options=base_options,
                       output_segmentation_masks=False,
                       running_mode=vision.RunningMode.IMAGE
                   )
                   self.mp_landmarker = vision.PoseLandmarker.create_from_options(options)
                   self.use_mediapipe = True
                   print(f"[HybridPoseTracker] MediaPipe Pose Landmarker aktif via {model_task_path}")
               except Exception as e:
                   print(f"[HybridPoseTracker] Gagal inisialisasi Tasks API ({e}), mencoba mp.solutions.pose...")
           
           # Fallback ke mp.solutions.pose standar jika Tasks API belum terkonfigurasi
           if not self.use_mediapipe:
               try:
                   self.mp_pose_solutions = mp.solutions.pose.Pose(
                       static_image_mode=False,
                       model_complexity=0,
                       min_detection_confidence=0.45,
                       min_tracking_confidence=0.50
                   )
                   self.use_mediapipe = True
                   print("[HybridPoseTracker] MediaPipe Pose aktif via mp.solutions.pose (Complexity 0)")
               except Exception as e:
                   print(f"[HybridPoseTracker] Peringatan: MediaPipe tidak dapat dimuat ({e}). Fallback ke YOLO murni.")
                   self.use_mediapipe = False

       def refine_keypoints_with_mediapipe(self, frame_bgr, yolo_kpts, yolo_bbox):
           """
           yolo_kpts: array shape (17, 3) [x, y, conf] dalam koordinat piksel frame.
           yolo_bbox: [x1, y1, x2, y2] bounding box pemain dari YOLO.
           Mengembalikan: kpts yang dihaluskan (17, 3) di mana siku & pergelangan tangan
                          digantikan oleh landmark presisi sub-piksel MediaPipe.
           """
           if not self.use_mediapipe or yolo_bbox is None:
               return yolo_kpts

           h_frame, w_frame = frame_bgr.shape[:2]
           x1, y1, x2, y2 = yolo_bbox
           
           # Berikan margin aman (+12%) agar rentangan tangan tidak terpotong
           pad_w = int((x2 - x1) * 0.12)
           pad_h = int((y2 - y1) * 0.12)
           crop_x1 = max(0, x1 - pad_w)
           crop_y1 = max(0, y1 - pad_h)
           crop_x2 = min(w_frame, x2 + pad_w)
           crop_y2 = min(h_frame, y2 + pad_h)

           crop_w = crop_x2 - crop_x1
           crop_h = crop_y2 - crop_y1
           if crop_w < 60 or crop_h < 60:
               return yolo_kpts

           crop_bgr = frame_bgr[crop_y1:crop_y2, crop_x1:crop_x2]
           crop_rgb = cv2.cvtColor(crop_bgr, cv2.COLOR_BGR2RGB)

           mp_kpts = None
           # 1. Jalankan inferensi MediaPipe pada crop kecil (~360x480 px -> super cepat ~8 ms)
           if hasattr(self, 'mp_landmarker') and self.mp_landmarker is not None:
               mp_image = mp.Image(image_format=mp.ImageFormat.SRGB, data=crop_rgb)
               result = self.mp_landmarker.detect(mp_image)
               if result.pose_landmarks and len(result.pose_landmarks) > 0:
                   mp_kpts = result.pose_landmarks[0]
           elif hasattr(self, 'mp_pose_solutions') and self.mp_pose_solutions is not None:
               result = self.mp_pose_solutions.process(crop_rgb)
               if result.pose_landmarks:
                   mp_kpts = result.pose_landmarks.landmark

           if mp_kpts is None:
               return yolo_kpts

           refined = np.copy(yolo_kpts)

           # Pemetaan Indeks MediaPipe (33) -> COCO YOLO (17):
           # MP 11 (L Shoulder) -> COCO 5
           # MP 12 (R Shoulder) -> COCO 6
           # MP 13 (L Elbow)    -> COCO 7
           # MP 14 (R Elbow)    -> COCO 8
           # MP 15 (L Wrist)    -> COCO 9
           # MP 16 (R Wrist)    -> COCO 10
           pairs = [
               (13, 7),  # Left Elbow
               (14, 8),  # Right Elbow
               (15, 9),  # Left Wrist
               (16, 10), # Right Wrist
           ]

           for mp_idx, coco_idx in pairs:
               pt = mp_kpts[mp_idx]
               vis = getattr(pt, 'visibility', 0.9)
               conf = getattr(pt, 'presence', vis)
               
               if vis > 0.35:
                   # Transformasikan kembali koordinat normalisasi crop ke koordinat global frame
                   gx = crop_x1 + pt.x * crop_w
                   gy = crop_y1 + pt.y * crop_h
                   
                   # Simpan landmark presisi MediaPipe
                   refined[coco_idx, 0] = gx
                   refined[coco_idx, 1] = gy
                   refined[coco_idx, 2] = max(refined[coco_idx, 2], vis)

           return refined
   ```

2. **Integrasikan ke `active_cv_sender.py`:**
   - Inisialisasi `self.hybrid_tracker = HybridPoseTracker("pose_landmarker_lite.task")` di dalam loop utama `active_cv_sender.py`.
   - Setelah YOLOv8 mendeteksi bounding box pemain utama (`player_bbox`) dan `raw_kpts`, panggil:
     `kpts = self.hybrid_tracker.refine_keypoints_with_mediapipe(frame, raw_kpts, player_bbox)`
   - Koordinat `kpts` yang sudah disempurnakan oleh MediaPipe kemudian diteruskan ke `pose_stabilizer.py` dan `motion_recognizer.py`.

3. **Penyetelan One-Euro Filter & Kinematic Clamping di `pose_stabilizer.py`:**
   - **Tuning One-Euro:**
     - `min_cutoff = 0.40 Hz` (meredam 95% getaran saat diam).
     - `beta = 0.12` (responsif mengikuti kepakan sayap tanpa lonjakan *spike snap*).
   - **Kinematic Bone Clamping:**
     - Jarak siku ke pergelangan dibatasi maksimal $1.10 \times \text{panjang lengan bawah}$. Jika melebihi batas, koordinat pergelangan tangan otomatis ditarik kembali ke radius bola siku.

---

### 4. PENGUJIAN & VERIFIKASI PLAY MODE

1. **Verifikasi Suite Python CV:**
   - Jalankan `ARDragonSkyGuardian_CV\venv\Scripts\python.exe test_all_17_poses_alignment.py`.
   - Pastikan seluruh suite pengujian 17 pose tetap **100% PASS**.
2. **Verifikasi Visual Live Preview Webcam:**
   - Jalankan `active_cv_sender.py` dengan jendela preview.
   - Berdiri diam di posisi T-Pose (`Glide`): Verifikasi bahwa kerangka tangan diam kokoh (*rock-solid*), bebas dari getaran (*jitter*).
   - Kepakkan tangan cepat dan lakukan putaran *Propeller Dash*: Verifikasi bahwa pergelangan tangan tidak melompat ke latar belakang dinding/lemari.
3. **Verifikasi Game Loop Unity:**
   - Jalankan `MainMenu_Lobby.unity` $\rightarrow$ pilih Stage 1 $\rightarrow$ pastikan timer HUD berawal dari `05:00`.
   - Uji Stage 2 $\rightarrow$ pastikan timer berawal dari `10:00`.
   - Uji dialog tetua desa $\rightarrow$ pastikan timer terhenti sejenak (*freeze*) selama narasi berlangsung.
4. **Catat devlog di `brain/laptop-b/Devlog.md`, centang tugas di `brain/laptop-b/Tasks.md`, lalu `commit` dan `push` Git.**
```

---
