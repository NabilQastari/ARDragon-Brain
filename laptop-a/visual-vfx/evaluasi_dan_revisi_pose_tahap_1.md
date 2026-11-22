---
title: Evaluasi Kritis & Panduan 2 Referensi Resmi Pose Naga Fanamir (Hover & Flight)
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

# 🐉 Evaluasi Kritis & Panduan 2 Lembar Rujukan Resmi: Hover vs Flight

> **Dokumen Resmi Rujukan Desain:** Pengguna telah menyerahkan **2 lembar rujukan desain anatomi resmi** Fanamir yang menjadi panduan mutlak bagi seluruh pose kaki, kemiringan tubuh, dan siluet naga di Unity:
> 1. 🖼️ **Hovering:** [`Referensi/Ref_Dragon_Hover_Leg_Anatomy.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi/Ref_Dragon_Hover_Leg_Anatomy.jpg) — *DRAGON HOVER POSES (STATIONARY FLIGHT)*
> 2. 🖼️ **Flight / Glide:** [`Referensi/Ref_Dragon_Flight_Leg_Anatomy.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi/Ref_Dragon_Flight_Leg_Anatomy.jpg) — *DRAGON FLIGHT POSES (LOCOMOTION)*

---

## 🖼️ 1. Dua Lembar Rujukan Resmi

### Rujukan A: Pose Hovering (Stationary Flight)
![Dragon Hover Poses](file:///e:/Semester%205/Game/AR%20Dragon/Referensi/Ref_Dragon_Hover_Leg_Anatomy.jpg)

### Rujukan B: Pose Flight (Locomotion / Fast Flight / Glide)
![Dragon Flight Poses](file:///e:/Semester%205/Game/AR%20Dragon/Referensi/Ref_Dragon_Flight_Leg_Anatomy.jpg)

---

## 🔍 2. Perbandingan Anatomi Detail: Hover vs Flight

```text
┌────────────────────────────────────────────────────────────────────────────────────────┐
│              MATRIKS PERBANDINGAN ANATOMI RESMI: HOVER VS FLIGHT                       │
├──────────────────────────┬───────────────────────────────┬─────────────────────────────┤
│ FITUR TUBUH              │ 1. HOVER (STATIONARY FLIGHT)  │ 2. FLIGHT (GLIDE / DIVE)    │
├──────────────────────────┼───────────────────────────────┼─────────────────────────────┤
│ KEMIRINGAN BADAN (BODY)  │ Upright Hovering Posture      │ Sleek Horizontal Streamlined│
│                          │ Tegap condong naik +28°..+35° │ Datar sejajar arah laju (0°)│
├──────────────────────────┼───────────────────────────────┼─────────────────────────────┤
│ KAKI DEPAN (FORELEGS)    │ Relaxed, loosely bent beneath │ Tucked backward against     │
│                          │ chest, siku ~80°-90°, cakar   │ body, folded neatly under   │
│                          │ menggantung santai ke bawah   │ chest rata di bawah dada    │
├──────────────────────────┼───────────────────────────────┼─────────────────────────────┤
│ KAKI BELAKANG (HINDLEGS) │ Dangling naturally downward,  │ Extended backward,          │
│                          │ lutut tertekuk santai 35°-45°,│ streamlined along tail,     │
│                          │ melayang bebas di bawah badan │ lurus sejajar ekor          │
├──────────────────────────┼───────────────────────────────┼─────────────────────────────┤
│ EKOR (TAIL)              │ Menjuntai ke belakang bawah & │ Lurus mendatar menjadi satu │
│                          │ ujung ekor melengkung naik    │ garis aerodinamis bersama   │
│                          │ (counter-balance weight)      │ kedua kaki belakang         │
├──────────────────────────┼───────────────────────────────┼─────────────────────────────┤
│ SAYAP (WINGS)            │ Broad flapping motion, sayap  │ Bentangan aerodinamis       │
│                          │ mekar tinggi menciduk udara   │ horizontal membelah angin   │
└──────────────────────────┴───────────────────────────────┴─────────────────────────────┘
```

---

## 🛠️ 3. Instruksi Eksekusi Presisi di Laptop B

### 3.1 Mode Hover (`IsHovering` / Kecepatan $< 4\text{ m/s}$ di Udara)
Berdasarkan `Ref_Dragon_Hover_Leg_Anatomy.jpg`:
1. **Sudut Kemiringan Badan (*Body Angle*):**
   * Terapkan kemiringan pitch **$+28^\circ \sim +35^\circ$** pada orientasi naga (`Visual_Dragon` atau rotasi kumulatif `Spine_01..02`).
   * Permata dada (*Chest Gem*) membusung anggun ke arah depan-bawah.
2. **Kaki Depan (*Forelegs*):**
   * Lengan atas turun sedikit ke depan. Siku menekuk $\approx 80^\circ \sim 90^\circ$.
   * Lengan bawah dan telapak cakar menggantung santai ke bawah-depan (*dangling loosely, slightly forward*).
3. **Kaki Belakang (*Hind Legs*):**
   * Paha mengarah ke bawah-depan, lutut tertekuk santai $\approx 35^\circ \sim 45^\circ$.
   * Kaki menggantung bebas secara alami di bawah badan (*hanging naturally below body*), **TIDAK berdiri kaku $90^\circ$ menjejak lantai**.
4. **Ekor (*Tail*):**
   * Pangkal ekor turun mengikuti kemiringan panggul, lalu ujung ekor (`Tail_04..05`) sedikit melengkung anggun ke atas sebagai penyeimbang bobot (*counter-balance curve*).

---

### 3.2 Mode Flight (`Glide`, `Tuck Dive`, Kecepatan $\ge 12\text{ m/s}$)
Berdasarkan `Ref_Dragon_Flight_Leg_Anatomy.jpg`:
1. **Sudut Kemiringan Badan:**
   * Datar horizontal sejajar vektor kecepatan terbang (*streamlined aerodynamic flight posture*).
2. **Kaki Depan (*Forelegs*):**
   * Melipat rapi ke arah belakang menempel di bawah rongga dada (*folded neatly under chest*).
3. **Kaki Belakang (*Hind Legs*):**
   * **WAJIB LURUS KE BELAKANG** (*extended backward, streamlined along tail*).
   * Paha lurus ke belakang, lutut hampir lurus ($0^\circ \sim 15^\circ$), cakar menunjuk ke belakang di sepanjang pangkal ekor, menghasilkan siluet mirip tombak/burung pemangsa.

---

*Navigasi:* [[00 - Overview]] | [[Tasks]] | [[Devlog]] | [[prompt_eksekusi_animasi_tahap_1_laptop_b]]
