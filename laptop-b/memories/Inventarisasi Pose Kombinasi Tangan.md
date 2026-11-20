---
title: "Inventarisasi Pose Kombinasi Tangan"
type: session-memory
tags:
  - session-memory
  - laptop-b
  - computer-vision
  - gesture
  - flight-physics
date: 2026-11-21
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[konteks]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[On-Ground Mechanics dan Uji 16 Skenario]]"
  - "[[Rework CV dan Kontrol Terbang Baru]]"
---

# Inventarisasi Pose Kombinasi Tangan

## 1. Tujuan & Konteks Sesi
Sesi ini bertujuan menjawab pertanyaan pengguna mengenai jumlah total kombinasi pose tangan yang terdokumentasi di `brain/` berdasarkan mekanisme kontrol **AR Dragon: Sky Guardian** saat ini (arsitektur **v6.0 Sky Peck** + **v6.1 On-Ground Mechanics & Arm-Proximity Locomotion**).

---

## 2. Keputusan Desain / Arsitektur / Teknis
- Tidak ada perubahan kode maupun arsitektur pada sesi ini; seluruh hasil merupakan audit dan konsolidasi dari dokumen rujukan di `brain/laptop-b/` (`[[konteks]]`, `[[Devlog]]`, `[[On-Ground Mechanics dan Uji 16 Skenario]]`, dan `[[Rework CV dan Kontrol Terbang Baru]]`).
- Klasifikasi pose tangan dipisahkan menjadi tiga kelompok agar tidak terjadi kerancuan hitung antara state darat, udara, aksi desa, dan modifier kemudi:
  1. **10 kombinasi pose/gerakan tangan inti** untuk lokomosi darat & terbang (yang memetakan **13 state kontrol**: 12 state terpadu v6.1 + `Propeller Dash`).
  2. **2 pose tangan khusus aksi desa** (Desa 2 dan Desa 3; Desa 1 menggunakan kepakan biasa).
  3. **1 modifier kemudi arah** (*Arm Tilt* kiri/kanan yang digabungkan dengan *Torso Lean*).

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- Membaca dan mengaudit dokumentasi kontrol terkini di `brain/00 - Overview.md`, `brain/laptop-a/konteks.md`, `brain/laptop-b/konteks.md`, `brain/laptop-b/Devlog.md`, `brain/laptop-b/Tasks.md`, `brain/laptop-b/progress.md`, serta catatan memori `[[Rework CV dan Kontrol Terbang Baru]]` dan `[[On-Ground Mechanics dan Uji 16 Skenario]]`.
- Menyusun ringkasan total **12 pose tangan diskrit** (10 lokomosi/terbang + 2 aksi desa) beserta **1 modifier kemudi** untuk pengguna.
- Menyimpan catatan sesi ini ke `brain/laptop-b/memories/Inventarisasi Pose Kombinasi Tangan.md` dan memperbarui `[[Devlog]]`.

---

## 4. Detail Penting yang Harus Diingat
- **10 Kombinasi Pose Lokomosi & Terbang (→ 13 State Kontrol):**
  1. Lengan atas *Tucked* tanpa kepak → **Grounded Idle** (`0 m/s` di darat).
  2. Lengan atas *Away* (T-pose tanpa kepak) → **Grounded Walk** (`3.5 m/s` di darat) / **Glide** (`14 m/s` di udara, termasuk transisi *Swoop Climb* pasca-dive).
  3. Lengan atas *Tucked* + lengan bawah terentang (*Tucked T-Pose*) tanpa kepak → **Gentle Descent** (`8.5–9 m/s`).
  4. Lengan atas *Tucked* + siku ditekuk & kepak lengan bawah (*forearm flap*) → **Hover** (`0 m/s`).
  5. Lengan atas *Away* + kepakan kedua tangan penuh → **Takeoff** (transisi darat → udara) / **Forward Flight (Flap to Climb)**.
  6. Dada terangkat / torso tilt atas + kepakan aktif → **Fly Up** (catatan: di darat tanpa kepakan menjadi state ke-13 **Grounded Look Up** via *BodyPitch*).
  7. Lengan atas *Away* + lengan bawah mengarah ke atas (**Pose Y**) → **Fly Down (Pose Y)**.
  8. Lengan atas *Tucked* + lengan bawah mengarah ke atas (**Pose W**) → **Fly Down Idle (Pose W)**.
  9. Kedua tangan rapat di dada/sternum, siku ke bawah (**Tuck Dive**) → **Dive** (hingga `28 m/s`).
  10. Kedua lengan diputar orbital kontinu → **Propeller Dash** (hingga `26 m/s`).
- **2 Pose Khusus Aksi Desa:**
  - **Desa 2 (Kebakaran Hutan):** Tangkupkan kedua tangan di dada *(atau buka mulut lebar)* untuk *Water Breath*.
  - **Desa 3 (Kekeringan Tanah):** Angkat kedua tangan tinggi di atas kepala (*Hands High Up*) untuk *Dew Rain*.
  - *(Desa 1 Polusi Udara memakai pose kepakan tangan biasa.)*
- **1 Modifier Kemudi:** *Arm Tilt* (selisih tinggi tangan kiri vs kanan) + *Torso Lean* untuk belok kiri/kanan (**Lean to Bank & Turn**).

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] Melanjutkan antrean aktif di [[Tasks]] (uji Play Mode webcam untuk kenyamanan *Fly Up*, *Hover*, dan *Gentle Descent*, texturing `rumah.blend`, serta Sesi GUI).

---

**Navigasi Obsidian:** [[00 - Overview]] · [[konteks]] · [[prd]] · [[progress]] · [[Tasks]] · [[Devlog]]
