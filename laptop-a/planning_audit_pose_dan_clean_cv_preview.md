---
title: Planning Teknis — Pembersihan Preview CV & Protokol Audit 15 Pose Berbasis Screenshot
type: planning
tags:
  - planning
  - laptop-a
  - laptop-b
  - cv-preview
  - pose-audit
  - bugfix-control
  - gesture-tuning
date: 2026-11-17
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[laptop B Patch 1 planning]]"
  - "[[planning_revisi_titik_wajah_dan_pov]]"
---

# 🧹 Planning Teknis — Pembersihan Preview CV & Protokol Audit 15 Pose Berbasis Screenshot

> Dokumen spesifikasi lanjutan untuk **Laptop B** dalam rangka menyempurnakan keakuratan mekanisme kontrol sebelum masuk ke Sesi 4.
> 
> **2 Poin Utama Sesi Ini:**
> 1. **Pembersihan Preview CV:** Mengosongkan seluruh indikator teks pada jendela preview webcam OpenCV/PIP; cukup tampilkan visual titik (*keypoints*) dan garis kerangka (*skeleton*) saja agar bersih dan tidak menghalangi tubuh pemain.
> 2. **Protokol Audit & Tuning Pose Berbasis Screenshot:** Pemain akan menguji pose di depan webcam dan mengambil tangkapan layar (*screenshot*) untuk setiap pose yang bermasalah / gagal terdeteksi, sebagai acuan langsung bagi Laptop B dalam merevisi formula/threshold classifier.

---

## 1. Pembersihan Preview CV (Clean Skeleton-Only View)

### Masalah Saat Ini:
* Pada jendela preview OpenCV (`active_cv_sender.py`) dan feed PIP di Unity, terdapat panel teks telemetri (misalnya kotak HUD hitam, teks FPS, pembacaan float `HEAD_PITCH`, angka `MOUTH_OPEN`, sudut lean, dan teks state).
* Teks-teks ini sering kali menutupi area kepala, dada, atau pergelangan tangan pemain, sehingga menyulitkan pemain dan pengembang untuk melihat apakah posisi fisik tubuh sudah sesuai dengan panduan visual.

### Tindakan Implementasi di Laptop B (`active_cv_sender.py`):
1. **Nonaktifkan / Hapus Gambar Kotak & Teks Telemetri:**
   - Hapus pemanggilan `cv2.putText()` dan `cv2.rectangle()` untuk panel HUD teks telemetri pada frame yang dirender/dikirim ke port 5053.
   - Sembunyikan teks FPS, nama status, dan nilai numerik dari atas kanvas video.
2. **Pertahankan Murni Visual Titik & Kerangka (*Clean Skeleton Overlay*):**
   - **Garis Tulang (Skeleton Lines):** Garis bahu, lengan atas, lengan bawah, serta torso.
   - **Titik Sendi Tubuh (Keypoints):** Lingkaran kecil pada bahu, siku, pergelangan tangan, dan pinggul.
   - **Titik Wajah Baru:** Titik Hidung (`Nose`), Dagu (`Chin`), dan Mulut (`Mouth`).
3. **Hasil:**
   - Tampilan video webcam menjadi sangat bersih (*clean & minimal*).
   - Pemain dapat melihat dengan jelas postur tubuhnya sendiri tanpa gangguan tulisan yang menumpuk di layar.

---

## 2. Protokol Audit & Penyelarasan Pose Berbasis Screenshot

Untuk memastikan seluruh 15 pose bekerja 100% akurat dan nyaman sesuai [Panduan 15 Pose Fanamir](file:///e:/Semester%205/Game/AR%20Dragon/Panduan_15_Pose_Fanamir.pdf), alur audit empiris dilakukan dengan langkah-langkah berikut:

```text
[ Pemain Menguji Pose di Webcam ]
              │
              ▼
    Apakah Pose Berhasil
    Terdeteksi dengan Akurat?
         ├─── YA ───► Lanjut ke pose berikutnya
         │
         └─── TIDAK (Gagal / Tidak Akurat / Lambat)
              │
              ▼
[ Ambil Screenshot Webcam (Per Pose yang Bermasalah) ]
              │  - Simpan ke folder assets/screenshots/audit_poses/
              │  - Catat nama pose dan perilaku di layar
              ▼
[ Evaluasi & Tuning di Laptop B ]
              │  - Periksa posisi keypoint & relasi geometri pada screenshot
              │  - Sesuaikan threshold di motion_recognizer.py / active_cv_sender.py
              │  - Sesuaikan smoothing / deadzone di DragonFlightController.cs
              ▼
[ Uji Ulang & Lolos Verifikasi ]
```

### Panduan Pengambilan Screenshot oleh Pemain:
1. Posisikan tubuh sesuai petunjuk di [Panduan 15 Pose](file:///e:/Semester%205/Game/AR%20Dragon/Panduan_15_Pose_Fanamir.pdf).
2. Jika suatu pose **tidak terdeteksi**, **salah terbaca** (misal: ingin `Hover` tapi terbaca `Gentle Descent`), atau **terasa terlalu sulit/berat**:
   - Ambil screenshot layar (menampilkan video webcam dengan titik/kerangka yang aktif).
   - Simpan screenshot dengan format penamaan yang jelas, misalnya:
     - `FAIL_Pose03_GroundedLookUp.png`
     - `FAIL_Pose07_FlyUp.png`
     - `FAIL_Pose09_Hover.png`
     - `FAIL_Pose13_TuckDive.png`
     - `FAIL_Pose15_MouthOpen.png`

### Area Tuning di Laptop B Berdasarkan Hasil Screenshot:

| Gejala Masalah pada Screenshot | Modul yang Disesuaikan | Parameter yang Dituning |
| :--- | :--- | :--- |
| Dagu sudah diangkat tapi Fanamir belum mendongak / belum `Fly Up` | `mouth_tracker.py` & `active_cv_sender.py` | Turunkan ambang deadzone `HEAD_PITCH` dari `±2.2°` ke `±1.5°` atau sesuaikan `DEFAULT_NEUTRAL_ELEVATION_DEG`. |
| Mulut sudah dibuka tapi `MouthOpen` tidak mencapai `0.45` | `mouth_tracker.py` | Turunkan ambang aktivasi semprotan dari `0.45` ke `0.35` atau perbesar faktor skala `MAR` (Mouth Aspect Ratio). |
| Kepak sayap (`Flap`) terasa berat atau telat terpicu | `motion_recognizer.py` | Sesuaikan `FlapStrokeClassifier` (kecepatan transisi downstroke/upstroke & hysteresis). |
| Naga terasa oleng saat meluncur lurus (`Glide`) | `DragonFlightController.cs` | Perlebar sedikit deadzone kemudi belok (`turnDeadzone = 3.0°`) agar meluncur lurus lebih tenang. |
| Pose `Hover` tertukar dengan `Gentle Descent` | `motion_recognizer.py` | Perjelas batas sudut siku (`mean_elbow_angle`) dan ketinggian pergelangan tangan (`flap_height`). |

---

## 3. Checklist Tindakan untuk Laptop B

- [ ] **Langkah 1: Bersihkan Preview CV:**
  - Buka `active_cv_sender.py`.
  - Matikan/komentari blok rendering teks telemetri OpenCV (hanya pertahankan `draw_skeleton()` dan `draw_keypoints()`).
- [ ] **Langkah 2: Siapkan Folder Tangkapan Layar:**
  - Pastikan folder tujuan screenshot siap (misal `assets/screenshots/audit_poses/`).
- [ ] **Langkah 3: Penerimaan Screenshot & Tuning Bertahap:**
  - Terima tangkapan layar dari pengujian pemain.
  - Periksa geometri keypoint yang tertera di screenshot dan sesuaikan formula threshold terkait.
  - Verifikasi ulang hingga seluruh 15 pose terkonfirmasi akurat dan responsif.
