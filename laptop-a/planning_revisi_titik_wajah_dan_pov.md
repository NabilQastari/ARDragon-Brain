---
title: Planning Teknis — Revisi Titik Wajah (Hidung, Dagu, Mulut) & Penyesuaian Pose Vertikal
type: planning
tags:
  - planning
  - laptop-a
  - laptop-b
  - face-tracking
  - pov-camera
  - fly-up
  - pitch-control
  - mouth-tracker
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

# 👤 Planning Teknis — Revisi Titik Wajah & Penyesuaian Pose Vertikal

> Dokumen acuan kerja bertahap untuk **Laptop B**:
> 1. **Tahap 1:** Revisi titik deteksi wajah (Hidung, Dagu, Mulut) pada pipeline Python CV & UDP.
> 2. **Tahap 2:** Penyesuaian pose-pose terkait (`#03 Grounded Look Up`, `#07 Fly Up`, dan Pitch Down) agar **murni membaca Hidung & Dagu tanpa perlu mengangkat dada (*no chest elevation needed*)**.

---

## 📌 Alur Pengerjaan Bertahap di Laptop B

```text
[ TAHAP 1: REVISI TITIK WAJAH ]
      │  - Deteksi Hidung (Nose), Dagu (Chin), Mulut (Mouth) di Python CV
      │  - Hitung sinyal pitch vertikal: Naik (+) & Turun (-) dari Hidung & Dagu
      │  - Hitung bukaan mulut MOUTH_OPEN (0.0 s/d 1.0)
      │  - Uji kestabilan titik di live webcam window
      ▼
[ TAHAP 2: PENYESUAIAN BEBERAPA POSE ]
         - Sesuaikan #03 Grounded Look Up (Cukup angkat sedikit dagu, dada rileks)
         - Sesuaikan #07 Fly Up (Dagu/Hidung naik -> Fanamir mendongak & terbang naik)
         - Sesuaikan Pitch Down (Dagu/Hidung turun -> Fanamir menunduk & meluncur turun)
         - Kunci Tegasan: Kepala TIDAK membelokkan arah horizontal (Belok kiri/kanan murni Lean & Arm Tilt)
```

---

## 1. Spesifikasi 3 Titik Wajah (Tahap 1)

```
           [ Mata / Dahi ]
                 │
           (1) HIDUNG (Nose)  ───┐
                 │                ├─► Sinyal Pitch Vertikal (Naik & Turun)
           (3) MULUT  (Mouth) ──┐ │   (Fly Up, Pitch Down, Grounded Look Up)
                 │              │ └──►
           (2) DAGU   (Chin)  ──┘
                                │
                                └─► Bukaan Mulut (Buka Mulut = Semburan Air Purba)
```

| Titik Wajah | Bagian Anatomis | Fungsi Gameplay di Fanamir |
| :--- | :--- | :--- |
| **1. Hidung (`Nose`)** | Ujung tengah hidung | Bersama Dagu, mengukur elevasi pitch kepala (*Head Pitch*). |
| **2. Dagu (`Chin`)** | Ujung bawah dagu | Menjadi jangkar orientasi kepala vertikal. |
| **3. Mulut (`Mouth`)** | Bibir atas & bibir bawah | Mengukur bukaan mulut vertikal (`0.0 = Tutup`, `1.0 = Buka Lebar`) untuk semburan air di Desa Pyrewood. |

---

## 2. Penyesuaian Pose & Sumbu Kontrol (Tahap 2)

### A. Penyesuaian Pose `#03 · Grounded Look Up`
* **Sebelumnya:** Pemain harus membusungkan/mengangkat dada ke atas (*chest elevation*).
* **Penyesuaian Baru:**
  - **Dada tidak perlu diangkat/dibusungkan!**
  - Cukup **angkat sedikit dagu ke atas**.
  - Begitu dagu dan hidung naik sedikit di atas ambang netral, kepala dan leher Fanamir mendongak menatap langit ($+15^\circ \sim +25^\circ$), kaki naga tetap diam di tanah.

### B. Penyesuaian Kontrol Terbang Vertikal: Naik & Turun
* **Sumbu Pitch Vertikal Murni dari Kepala:**
  - **Terbang Menanjak Naik (`FLY UP`):**
    - Angkat dagu dan hidung ke atas $\rightarrow$ Fanamir mendongak menatap langit (kamera follow terangkat) dan naga **terbang menanjak naik ke atas (*Fly Up*)** secara proporsional.
    - **Dada tidak perlu dinaikkan!** Cukup gerakkan kepala.
  - **Terbang Meluncur Datar (`GLIDE`):**
    - Posisi dagu dan hidung lurus netral $\rightarrow$ Fanamir meluncur datar stabil.
  - **Terbang Menukik / Turun Perlahan (`PITCH DOWN`):**
    - Turunkan/tundukkan dagu sedikit ke bawah $\rightarrow$ Fanamir menunduk dan meluncur turun secara terkontrol.

### C. Tegasan `!= Belok Arah Terbang`
* **Aturan Mutlak:**
  - Gerakan kepala (Hidung & Dagu) **HANYA untuk sumbu Pitch vertikal (naik dan turun)**.
  - Gerakan kepala **SAMA SEKALI TIDAK membelokkan arah horizontal naga (tidak memicu belok kiri atau kanan)**.
  - Kemudi belok kiri/kanan (Yaw & Roll) **100% tetap dikendalikan oleh kemiringan badan dan tangan (`#05 Lean & Arm Tilt`)**.
  - Ini menjamin saat pemain mendongak untuk menanjak (*Fly Up*), naga melesat lurus naik tanpa oleng atau belok sendiri.

### D. Aksi Semburan Air (Mulut)
* Membuka mulut lebar di depan kamera (**`MouthOpen > 0.45`**) saat berada di zona kebakaran hutan Desa Pyrewood memicu semburan air purba (*Water Breath Stream*).

---

## 3. Checklist Eksekusi Teknis di Laptop B

### Tahap 1: Eksekusi Revisi Titik Wajah (Python CV)
- [ ] Implementasikan deteksi 3 titik (Hidung, Dagu, Mulut) di `active_cv_sender.py` / `MouthTracker`.
- [ ] Hitung nilai normalisasi `HEAD_PITCH` (rentang `-1.0` s/d `+1.0`, di mana `0.0` = netral, `+` = mendongak/naik, `-` = menunduk/turun).
- [ ] Hitung rasio bukaan mulut `MOUTH_OPEN` (`0.0` s/d `1.0`).
- [ ] Uji preview visual titik di jendela OpenCV/PIP: pastikan hidung, dagu, dan mulut terdeteksi stabil tanpa jitter.

### Tahap 2: Penyesuaian Pose & Controller (Unity C#)
- [ ] Hapus ketergantungan `chest_elevation` dari pembacaan pitch di `DragonFlightController.cs`.
- [ ] Hubungkan `HEAD_PITCH` dari UDP 5052 ke:
  1. Rotasi visual tulang leher/kepala naga (POV look-at).
  2. Daya angkat vertikal naga (`Fly Up` saat positif, `Descent` saat negatif).
- [ ] Pastikan sumbu `steeringInput` (kemudi belok kiri-kanan) murni berasal dari `arm_tilt` / `torso_lean` tanpa tercemar `HEAD_PITCH`.
- [ ] Uji Play Mode:
  - Di darat: Angkat dagu sedikit $\rightarrow$ naga mendongak (`#03 Grounded Look Up`).
  - Di udara: Angkat dagu $\rightarrow$ naga mendongak & terbang naik (`Fly Up`).
  - Di udara: Tundukkan dagu sedikit $\rightarrow$ naga meluncur turun.
  - Sambil mendongak, miringkan badan $\rightarrow$ naga menanjak sambil berbelok mulus.
