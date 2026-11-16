---
title: Planning Teknis — Revisi Titik Wajah (Hidung, Dagu, Mulut) & Kontrol POV Fly Up
type: planning
tags:
  - planning
  - laptop-a
  - laptop-b
  - face-tracking
  - pov-camera
  - fly-up
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

# 👤 Planning Teknis — Revisi Titik Wajah & Kontrol POV Fly Up

> Dokumen spesifikasi revisi sistem pelacakan wajah (*Face Keypoints Tracking*) untuk **Laptop B**.
> 
> **Poin Utama Revisi:**
> 1. Mengunci **3 Titik Wajah Inti:** **Hidung (`Nose`)**, **Dagu (`Chin`)**, dan **Mulut (`Mouth`)**.
> 2. **Hidung & Dagu $\rightarrow$ Menggerakkan POV (Pandangan Kepala/Kamera) dengan Tujuan Utama untuk FLY UP (Terbang Menanjak Naik):**
>    - Saat dagu dan hidung diangkat ke atas, kepala Fanamir mendongak, kamera menatap ke langit, dan naga **terbang menanjak ke atas (*Fly Up*)**.
>    - Gerakan kepala ini **BUKAN untuk membelokkan arah terbang horizontal (belok kiri/kanan)**. Kemudi belok kiri/kanan murni dikendalikan oleh kemiringan badan dan lengan (*Lean & Arm Tilt*).
> 3. **Mulut $\rightarrow$ Aksi Semburan Air Purba:** Bukaan mulut murni digunakan untuk menyemburkan air (*Water Breath*) pada pemadaman api di Desa Pyrewood.

---

## 1. Arsitektur & Spesifikasi 3 Titik Wajah

```
           [ Mata / Dahi ]
                 │
           (1) HIDUNG (Nose)  ───┐
                 │                ├─► POV Look-Up & Pemicu Utama FLY UP
           (3) MULUT  (Mouth) ──┐ │   (Elevasi vertikal naik, BUKAN belok kiri/kanan!)
                 │              │ └──►
           (2) DAGU   (Chin)  ──┘
                                │
                                └─► Bukaan Mulut (Buka Mulut = Semburan Air Purba)
```

| Titik Wajah | Lokasi Anatomis | Fungsi Gameplay di Fanamir |
| :--- | :--- | :--- |
| **1. Hidung (`Nose`)** | Ujung tengah hidung | Bersama Dagu, mendeteksi sudut elevasi kepala (*Head Pitch Angle*). |
| **2. Dagu (`Chin`)** | Ujung bawah dagu | Menjadi jangkar dasar orientasi wajah vertikal. |
| **3. Mulut (`Mouth`)** | Bibir atas & bibir bawah | Mengukur bukaan mulut vertikal (`0.0 = Tutup`, `1.0 = Buka Lebar`) untuk memicu semburan air di Desa Pyrewood. |

---

## 2. Logika Kontrol: POV Fly Up vs Kemudi Belok

### A. Kontrol Vertikal: Angkat Hidung & Dagu $\rightarrow$ POV Menatap Langit & Fly Up
* **Kalkulasi di Python (`active_cv_sender.py`):**
  - Hitung vektor vertikal antara hidung dan dagu:
    $$\Delta Y_{\text{face}} = Y_{\text{chin}} - Y_{\text{nose}}$$
  - Ketika pemain mengangkat kepala/dagu ke atas:
    - Nilai `head_pitch` dihitung dari pergeseran posisi hidung dan dagu terhadap garis bahu netral.
    - Menghasilkan nilai normalisasi `HEAD_PITCH_ELEVATION` ($0.0 \sim +1.0$).
* **Penerapan di Unity (`DragonFlightController.cs` & `DragonHeadLookController.cs`):**
  1. **Visual Kepala & Kamera (POV Look-At):**
     - Leher dan kepala naga mendongak ke atas mengikuti sudut `HEAD_PITCH_ELEVATION` (hingga $+30^\circ$).
     - Kamera follow sedikit bergeser ke atas memberikan sudut pandang sinematik menatap langit.
  2. **Dinamika Terbang Menanjak (Fly Up):**
     - Sinyal elevasi kepala ini menggerakkan daya angkat vertikal Fanamir:
       ```csharp
       if (headPitchElevation > 0.15f) // deadzone kecil
       {
           float climbLift = headPitchElevation * flyUpLiftPower;
           verticalVelocity += climbLift * Time.deltaTime;
           dragonPitch = Mathf.Lerp(dragonPitch, headPitchElevation * maxClimbPitch, Time.deltaTime * pitchSmoothSpeed);
       }
       ```
     - **HASIL:** Fanamir mendongak dan langsung menanjak terbang ke atas (**Fly Up**) secara anggun mengikuti arah pandangan pemain!

### B. Kemudi Belok Arah Terbang (Yaw / Roll Kiri-Kanan) Murni dari Badan/Tangan
* **Aturan Keras (*Rule of Decoupling*):**
  - Gerakan mengangkat hidung & dagu **SAMA SEKALI TIDAK mempengaruhi kemudi belok kiri/kanan**.
  - Kemudi belok horizontal (Yaw & Roll) tetap **100% dikendalikan oleh `Lean & Arm Tilt`** (condong badan atau miring tangan).
  - Ini mencegah naga berbelok oleng tanpa sengaja saat pemain hanya ingin terbang menanjak ke atas.

### C. Aksi Semburan Air (Mulut)
* **Kalkulasi Bukaan Mulut:**
  - Jarak celah bibir vertikal dinormalisasi terhadap jarak hidung-dagu $\rightarrow$ `MouthOpen` ($0.0 \sim 1.0$).
* **Penerapan di Unity:**
  - Saat di radius kebakaran hutan Desa Pyrewood dan memiliki air:
    - `MouthOpen > 0.45` $\rightarrow$ semburan air purba aktif (`VFX_Dragon_WaterBreath_Stream`).
    - `MouthOpen <= 0.45` $\rightarrow$ semburan air padam.

---

## 3. Implementasi Teknis di Laptop B

1. **Python CV (`active_cv_sender.py` & Head ROI):**
   - Ambil bounding box kepala dari YOLOv8n-pose.
   - Deteksi 3 titik: Hidung, Dagu, dan Bibir Atas/Bawah.
   - Kirimkan telemetri melalui UDP 5052:
     - `HEAD_PITCH_ELEVATION` (sinyal elevasi kepala untuk POV & Fly Up)
     - `MOUTH_OPEN` (rasio bukaan mulut)
     - *Catatan:* Jangan campurkan sinyal kepala ini ke sumbu kemudi belok (steering input)!
2. **Unity Controller (`DragonFlightController.cs`):**
   - Hubungkan `HEAD_PITCH_ELEVATION` ke sistem daya angkat naga (**Fly Up**) dan rotasi visual kepala/kamera.
   - Sumbu kemudi belok horizontal (`turnInput`) tetap murni membaca `arm_tilt` / `torso_lean`.

---

## 4. Pengujian di Play Mode

1. **Uji Fly Up via Angkat Dagu:**
   - Terbang meluncur datar (`#08 Glide`).
   - Angkat hidung dan dagu ke atas.
   - **Verifikasi:** Kepala naga mendongak menatap langit, kamera follow terangkat ke atas, dan Fanamir menanjak naik ke atas (**Fly Up**) tanpa belok miring ke kiri atau kanan.
2. **Uji Belok Sambil Fly Up:**
   - Angkat dagu (naga menanjak) sambil memiringkan badan ke kanan $\rightarrow$ naga menanjak spiral ke kanan secara terkontrol.
3. **Uji Semburan Air:**
   - Buka mulut lebar-lebar $\rightarrow$ Fanamir menyemburkan air purba.
