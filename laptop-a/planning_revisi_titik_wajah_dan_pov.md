---
title: Planning Teknis — Revisi Titik Wajah (Hidung, Dagu, Mulut) & Pemisahan Kontrol POV
type: planning
tags:
  - planning
  - laptop-a
  - laptop-b
  - face-tracking
  - pov-camera
  - mouth-tracker
  - cv-control
date: 2026-11-16
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[laptop B Patch 1 planning]]"
  - "[[planning_kamera_dinamis_dan_navigasi_pause]]"
  - "[[mekanisme_tantangan_desa]]"
---

# 👤 Planning Teknis — Revisi Titik Wajah & Pemisahan Kontrol POV

> Dokumen spesifikasi revisi sistem pelacakan wajah (*Face Keypoints Tracking*) untuk **Laptop B**.
> 
> **Poin Utama Revisi:**
> 1. Mengunci **3 Titik Wajah Inti:** **Hidung (`Nose`)**, **Dagu (`Chin`)**, dan **Mulut (`Mouth`)**.
> 2. **Hidung & Dagu $\rightarrow$ Menggerakkan POV (Pandangan Kepala/Kamera):** Saat dagu dan hidung diangkat sedikit, Fanamir mendongak dan kamera menoleh ke atas untuk melihat pemandangan langit/kepulauan, **TIDAK mengubah atau membelokkan arah lintasan terbang naga**.
> 3. **Mulut $\rightarrow$ Aksi Semburan Air:** Bukaan mulut murni digunakan untuk menyemburkan air purba (*Water Breath*) pada pemadaman api di Desa Pyrewood.

---

## 1. Latar Belakang & Masalah Sebelumnya

* **Masalah Sebelumnya di Laptop B:**
  - Sebelumnya, sinyal kepala (*head_signal*) dari hidung dicampur ke dalam `body_pitch` pada `DragonFlightController.cs`.
  - Akibatnya, ketika pemain hanya ingin mendongakkan kepala sedikit untuk melihat pemandangan ke atas, badan naga justru ikut menanjak naik (*pitch up / climb*), sehingga arah terbang naga terganggu.
  - Selain itu, COCO 17-keypoint standar pada YOLOv8-pose hanya mendeteksi hidung, mata, dan telinga tanpa titik dagu eksplisit.
* **Perbaikan yang Ditargetkan:**
  - **Pemisahan Mutlak (*Decoupling*):** Arah terbang naga dikontrol oleh gestur penerbangan (*Flap to Climb, Fly Up Pose Y, Glide T-Pose, Tuck to Dive*), sedangkan gerakan **Hidung & Dagu hanya mengontrol orientasi kepala naga (POV Look-At) dan kamera**.
  - **3 Titik Wajah Spesifik:**
    1. **Hidung (`Nose`)**
    2. **Dagu (`Chin`)**
    3. **Mulut (`Mouth`)**

---

## 2. Spesifikasi 3 Titik Wajah & Fungsinya

```
           [ Mata / Dahi ]
                 │
           (1) HIDUNG (Nose)  ───┐
                 │                ├─► Sudut Pitch POV (Naga mendongak/melihat atas,
           (3) MULUT  (Mouth) ──┐ │   TIDAK membelokkan arah terbang naga!)
                 │              │ └──►
           (2) DAGU   (Chin)  ──┘
                                │
                                └─► Bukaan Mulut (Buka Mulut = Semburan Air Purba)
```

| Titik Wajah | Lokasi Anatomis | Fungsi Gameplay di Fanamir |
| :--- | :--- | :--- |
| **1. Hidung (`Nose`)** | Ujung tengah hidung | Bersama Dagu, mengukur sudut elevasi kepala (*Head Pitch Angle*). |
| **2. Dagu (`Chin`)** | Ujung bawah dagu | Menjadi jangkar dasar orientasi wajah vertikal. |
| **3. Mulut (`Mouth`)** | Bibir atas & bibir bawah | Mengukur rasio bukaan mulut vertikal (`0.0 = Tutup`, `1.0 = Buka Lebar`) untuk memicu semburan air di Desa Pyrewood. |

---

## 3. Logika Pemisahan: POV vs Arah Lintasan Terbang

### A. Kontrol Pandangan Kepala & Kamera (POV Look-Around)
* **Kalkulasi di Python (`active_cv_sender.py`):**
  - Hitung vektor vertikal antara hidung dan dagu:
    $$\Delta Y_{\text{face}} = Y_{\text{chin}} - Y_{\text{nose}}$$
  - Dibandingkan terhadap baseline postur netral yang terkalibrasi (`neutral_face_height`):
    - Jika pemain mengangkat dagu ke atas: jarak relatif atau posisi hidung naik terhadap garis bahu/dagu menghasilkan nilai `HEAD_PITCH_POV` ($0.0 \sim +1.0$).
    - Jika kepala posisi normal: `HEAD_PITCH_POV = 0.0`.
* **Penerapan di Unity (`DragonHeadLookController.cs`):**
  - Pasang komponen pengendali tulang kepala di `Visual_Dragon`:
    ```csharp
    // Di eksekusi pada LateUpdate() setelah animasi sayap selesai
    void LateUpdate()
    {
        float targetHeadPitch = udpReceiver.HeadPitchPOV * maxHeadTiltAngle; // misal max 30 derajat
        _currentHeadPitch = Mathf.Lerp(_currentHeadPitch, targetHeadPitch, Time.deltaTime * headSmoothSpeed);
        
        // Putar hanya tulang leher dan kepala naga ke atas
        headBone.localRotation *= Quaternion.Euler(_currentHeadPitch, 0f, 0f);
        
        // Gerakkan sedikit pitch kamera follow untuk memperluas pandangan ke langit
        cameraFollow.SetLookOffsetPitch(_currentHeadPitch * cameraPitchInfluence); // misal 0.35x
    }
    ```
  - **HASIL:** Fanamir menengok dan melihat ke atas secara alami, kamera ikut memperluas pandangan ke langit atas, namun **kecepatan maju dan ketinggian terbang naga tetap stabil mendatar (arah terbang TIDAK berubah)**.

### B. Aksi Menyemburkan Air (Mulut Buka)
* **Kalkulasi Bukaan Mulut:**
  - Jarak celah bibir vertikal:
    $$\text{MouthOpenRatio} = \frac{\text{Lip}_{\text{lower}}.y - \text{Lip}_{\text{upper}}.y}{\Delta Y_{\text{face}}}$$
  - Dinormalisasi ke skala `0.0` s/d `1.0`.
* **Penerapan di Unity:**
  - Jika berada di radius kebakaran hutan Desa Pyrewood dan memiliki cadangan air:
    - `MouthOpen > 0.45` $\rightarrow$ memicu partikel semburan air purba `VFX_Dragon_WaterBreath_Stream` dari mulut Fanamir.
    - `MouthOpen <= 0.45` $\rightarrow$ semburan air berhenti.

---

## 4. Arsitektur Pipeline Python CV di Laptop B

Agar pendeteksian Hidung, Dagu, dan Mulut sangat ringan (< 2 ms) tanpa memperlambat inferensi tubuh YOLOv8n:

1. **YOLOv8n-pose Primer:**
   - Mendeteksi tubuh pemain secara penuh (bahu, siku, pergelangan, pinggul) dan memberikan posisi kasar hidung (`Nose`).
2. **Head ROI Cropping (~$180 \times 180\text{ px}$):**
   - Ambil area kotak kepala di sekeliling titik hidung.
3. **Face Landmark Sub-Module (Hidung, Dagu, Mulut):**
   - Di dalam kotak crop kecil, sistem mendeteksi:
     - Titik Hidung (`Nose Tip`)
     - Titik Dagu (`Chin Tip`)
     - Titik Bibir Atas & Bawah (`Upper & Lower Lip`)
   - Dapat memanfaatkan model landmark wajah ringan (seperti MediaPipe FaceMesh khusus ROI kepala, atau landmark geometri kontur bibir/dagu OpenCV).
4. **Protokol UDP 5052 Baru:**
   - Masukkan token `HEAD_PITCH_POV` (token float sudut pandang) dan pertahankan `MOUTH_OPEN` pada token 12.

---

## 5. Ringkasan Pengujian di Play Mode

1. **Uji Angkat Dagu Saat Terbang Mendatar (`Glide` / `Flap`):**
   - Pemain meluncur datar membentuk T-Pose.
   - Angkat dagu dan hidung sedikit ke atas.
   - **Verifikasi:** Kepala naga mendongak melihat ke atas, pemandangan langit terlihat lebih luas di layar kamera, **namun naga tetap terbang meluncur lurus mendatar tanpa menanjak**.
2. **Uji Buka Mulut di Hutan Terbakar (Desa Pyrewood):**
   - Pemain masuk zona hutan terbakar.
   - Buka mulut lebar-lebar $\rightarrow$ air menyembur deras.
   - Tutup mulut $\rightarrow$ semburan air padam seketika.
3. **Uji Pose Mendongak di Darat (`#03 Grounded Look Up`):**
   - Naga diam di tanah, pemain mendongak ke atas $\rightarrow$ Fanamir mendongakkan kepala ke langit sambil tetap diam di tempat.
