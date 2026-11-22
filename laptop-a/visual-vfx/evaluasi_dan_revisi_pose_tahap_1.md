---
title: Evaluasi & Perbaikan Kritis Pose Tahap 1 (Anatomi Kaki & Sikap Hover)
tags:
  - evaluation
  - bugfix
  - animation
  - dragon-rig
  - hover-pose
  - flight-legs
  - visual-vfx
  - laptop-a
owner: Laptop A
updated: 2026-11-23
status: active
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[visual-vfx/planning_perbaikan_animasi_dan_pose]]"
  - "[[visual-vfx/prompt_eksekusi_animasi_tahap_1_laptop_b]]"
---

# 🔍 Evaluasi Kritis & Perbaikan Pose Tahap 1

> **Konteks:** Hasil pengujian Play Mode Tahap 1 di Laptop B menunjukkan dua anomali visual yang sangat mencolok pada tangkapan layar pengguna:
> 1. **Pose Terbang (`GLIDE`):** Kaki naga tampak remuk, menciut ke dalam badan, dan tidak proporsional.
> 2. **Pose Hover (`HOVER`):** Badan naga tampak sama sekali tidak berubah (datar horizontal $0^\circ$), dan keempat kakinya berdiri tegak lurus $90^\circ$ ke bawah persis seperti naga berdiri di atas lantai/meja.

---

## 📸 1. Bedah Masalah Visual Hasil Pengujian

```text
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                        BEDAH ANOMALI VISUAL TAHAP 1 DI LAPTOP B                        │
├────────────────────────────────────────────┬───────────────────────────────────────────┤
│ 1. GLIDE / FLIGHT TUCK (ANOMALI KAKI)      │ 2. HOVER (BADAN DATAR & KAKI MEJA)        │
│ • Kaki belakang terlipat remuk ke dalam    │ • Badan naga 100% horizontal datar (0°)   │
│   perut (accordion fold / mesh clipping).  │ • Elevasi dada tidak terlihat sama sekali.│
│ • Kaki belakang tidak lurus sejajar ekor.  │ • 4 kaki tegak lurus 90° ke bawah menjejak│
│ • Kaki depan tampak seperti benjolan kaku. │   lantai gaib (ground standing di udara). │
└────────────────────────────────────────────┴───────────────────────────────────────────┘
```

### 1.1 Masalah Pose Terbang (Gambar 1 — `GLIDE T-POSE`, $17.5\text{ m/s}$)
* **Temuan:** Di `laptop-b/Devlog.md`, rotasi yang diterapkan adalah:
  `HindTuckPitchDeg = { -88f, 32f, -52f, -65f }`
* **Akar Masalah:**
  * Sudut-sudut negatif ekstrim ini memutar sendi paha, lutut, dan cakar ke arah yang saling berlawanan di sumbu lokal yang salah, sehingga kaki belakang terlipat rapat menembus daging perut (*mesh penetration*) dan terlihat buntung/terpotong.
  * Padahal referensi gambar resmi (`Ref_Dragon_Flight_Leg_Anatomy.jpg`) menginstruksikan:
    **"HIND LEGS: Extended backward, streamlined along tail"**
    $\rightarrow$ Kaki belakang harus **LURUS MEMANJANG KE BELAKANG**, sejajar dan merapat di sepanjang pangkal ekor layaknya postur elang yang menukik cepat.

### 1.2 Masalah Pose Hover (Gambar 2 — `HOVER #09A`, $0.0\text{ m/s}$)
* **Temuan:**
  * Badan naga tampak $100\%$ datar horizontal. Tidak ada tanda-tanda dada terangkat $+16^\circ$.
  * Keempat kaki berdiri tegak lurus kaku $90^\circ$ ke bawah seperti meja.
* **Akar Masalah:**
  1. Rotasi $+8^\circ$ pada `Spine_01` dan `Spine_02` kemungkinan diterapkan pada sumbu lokal yang salah di rig FBX (misal roll atau yaw, bukan pitch visual), atau tertimpa oleh rotasi bind-pose.
  2. Yang terpenting: `DragonFlightController` menahan pitch badan naga (`Visual_Dragon`) di $0^\circ$ (horizontal). Tanpa adanya rotasi kemiringan tubuh secara menyeluruh (*body attitude pitch* $+18^\circ \sim +22^\circ$), naga akan selalu terlihat datar.
  3. Pose kaki saat hover diatur ke sudut rileks yang hampir identik dengan pose berdiri di tanah (*ground stance*), sehingga di udara naga tampak seperti sedang berdiri di atas lantai kaca tak terlihat.

---

## 🛠️ 2. Petunjuk Perbaikan Teknis untuk Laptop B

### A. Perbaikan Kaki Terbang (*Flight Tuck* — Streamlined Rearward)
Tujuan: Kaki belakang memanjang lurus ke belakang sejajar ekor; kaki depan melipat rata di bawah rongga dada.
1. **Kaki Belakang (`_backLegs`):**
   * **Paha Belakang (`Thigh_Hind`):** Putar ke arah belakang sejajar sumbu horizontal tubuh (tulang paha mengarah ke `-Z` visual / searah pangkal ekor).
   * **Lutut / Tulang Kering (`Shin_Hind`):** Jangan ditekuk tajam! Pertahankan sendi lutut hampir lurus ($\approx 0^\circ \sim 15^\circ$ lekukan alami).
   * **Metatarsus & Cakar (`Metatarsus_Hind`, `Foot_Hind`):** Luruskan searah dengan tulang kering, cakar merapat menunjuk lurus ke belakang di samping ekor.
   * *Verifikasi Visual:* Dari samping, kaki belakang membentuk satu garis aerodinamis yang menyatu dengan siluet ekor naga.
2. **Kaki Depan (`_frontLegs`):**
   * **Paha Depan (`UpperArm_Front`):** Tarik ke belakang merapat di samping tulang rusuk.
   * **Lengan Bawah (`Forearm_Front`):** Tekuk ke belakang di bawah dada.
   * **Telapak Cakar (`Paw_Front`):** Rapatkan cakar rata di bawah perut/dada (*flat against chest*), jangan mencuat keluar.

---

### B. Perbaikan Pose Hover (*Elevated Buoyant Hover Posture*)
Tujuan: Naga terlihat gagah melayang di udara dengan dada terangkat tegap dan kaki menggantung santai.
1. **Elevasi Tubuh Menyeluruh (*Body Attitude Pitch*):**
   * Saat `flightController.IsHovering` atau kecepatan horizontal $< 4\text{ m/s}$ di udara:
     * Tubuh naga (`Visual_Dragon` atau rotasi dasar tubuh) harus memiliki kemiringan pitch naik sebesar **$+18^\circ \sim +22^\circ$**.
     * Ini membuat naga condong tegap menentang gravitasi, dan permata dada (*Chest Gem*) memancar jelas ke arah depan-bawah.
2. **Lengkungan Leher & Kepala:**
   * Leher (`Neck_01..02`) mengimbangi kemiringan tubuh dengan menekuk lembut ke depan sehingga kepala naga tetap memandang lurus mendatar ke depan (*horizontal eye-line*).
3. **Kaki Menjuntai Santai (*Air Hover Dangle* — Bukan Berdiri di Lantai!):**
   * Kaki depan: Tertekuk rileks ke belakang bawah ($\sim 40^\circ$), cakar menggantung lembut.
   * Kaki belakang: Tertekuk santai ke bawah-belakang ($\sim 35^\circ$), sendi lutut ditekuk lembut, cakar tidak kaku menjejak lantai.
   * Keempat kaki tampak melayang santai tertiup angin ke bawah, mempertegas status bahwa naga sedang berada di udara (*airborne*).

---

*Navigasi:* [[00 - Overview]] | [[Tasks]] | [[Devlog]] | [[prompt_eksekusi_animasi_tahap_1_laptop_b]]
