---
title: Prompt Generator Visual Pose Titik & Kerangka CV — Piktogram Siluet Resmi (Segmen 1-4)
type: prompt-guide
tags:
  - prompt
  - pose-guide
  - computer-vision
  - pictogram
  - skeleton
  - keypoints
  - minimalist
  - laptop-a
date: 2026-11-17
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[planning_audit_pose_dan_clean_cv_preview]]"
---

# 🎯 Katalog Prompt Visual Pose CV — Piktogram Siluet Resmi (Segmen 1–4)

> **Spesifikasi Formula Visual Sukses (Terverifikasi dari Hasil Segmen 1):**
> 1. **Siluet Piktogram 2D Solid:** Tubuh hitam/charcoal solid (*matte*) dengan bentuk torso kapsul membulat, lengan & kaki tebal membulat, dan kepala lingkaran.
> 2. **Garis Kerangka Kawat (*Wireframe Skeleton*):** Garis vektor tipis bercahaya yang menghubungkan sendi tubuh.
> 3. **Titik Sendi Bulat Dual-Tone (*Keypoints*):**
>    - Sisi Kanan Tubuh: Kuning / Amber Gold (`#FFB300`).
>    - Sisi Kiri Tubuh: Cyan / Teal (`#00F5FF`).
>    - Wajah: Titik Cyan menonjol/terang pada Hidung, Mulut, dan Dagu.
> 4. **Latar Belakang:** Square viewport abu-abu gelap dengan garis *blueprint grid* tipis dan aksen siku bidik (*corner brackets*), bersih tanpa efek asap/api/partikel dan tanpa teks.

---

## 📂 Segmen 1: Grounded Poses (Pose Landasan Darat) — [STATUS: SELESAI & DISIMPAN]

*Aset tersimpan di:* [`assets/poses_cv_guide/`](file:///e:/Semester%205/Game/AR%20Dragon/assets/poses_cv_guide/) & [`Referensi/Referensi Pose CV/`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi/Referensi%20Pose%20CV/)

### #01 · Grounded Idle
* **File Tersimpan:** `Pose_01_GroundedIdle.jpg`
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, flat 2D human pictogram silhouette with smooth rounded limbs, circular head, and capsule torso. Dark matte charcoal silhouette centered in a dark slate viewport with subtle blueprint grid lines and corner brackets. Overlaid with glowing wireframe skeleton lines and bright circular joint dots: warm amber dots on the right limbs, cyan dots on the left limbs. High-contrast prominent glowing cyan facial keypoint dots distinctly visible on the circular head marking the nose, mouth, and chin facing forward. The pictogram is in a strict Grounded Idle I-Pose, standing rigidly upright on flat ground with both arms pinned completely straight and tight against the sides of the torso, both legs standing straight and stationary side-by-side. Bold definitive pictogram posture, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

### #02 · Grounded Walk (Stationary T-Pose di Darat)
* **File Tersimpan:** `Pose_02_GroundedWalk.jpg`
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, flat 2D human pictogram silhouette with smooth rounded limbs, circular head, and capsule torso. Dark matte charcoal silhouette centered in a dark slate viewport with subtle blueprint grid lines and corner brackets. Overlaid with glowing wireframe skeleton lines and bright circular joint dots: warm amber dots on the right limbs, cyan dots on the left limbs. High-contrast prominent glowing cyan facial keypoint dots distinctly visible on the circular head marking the nose, mouth, and chin. The pictogram is in a rigid stationary Grounded T-Pose: both arms are stretched completely straight horizontally at 180 degrees shoulder height, while both legs are standing completely straight and stationary side-by-side on the flat floor, feet flat, perfectly still upright legs, strictly no walking motion, no leg bending, no stepping. Bold definitive pictogram posture, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

### #03 · Grounded Look Up (Tampak Depan Penuh + Panah Elevasi)
* **File Tersimpan:** `Pose_03_GroundedLookUp.jpg`
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, flat 2D human pictogram silhouette with smooth rounded limbs, circular head, and capsule torso. Dark matte charcoal silhouette centered in a dark slate viewport with subtle blueprint grid lines and corner brackets. Overlaid with glowing wireframe skeleton lines and bright circular joint dots: warm amber dots on the right limbs, cyan dots on the left limbs. STRICT FRONT-FACING CAMERA VIEW, perfectly symmetrical front perspective, facing directly forward towards the camera, absolutely no side profile, no three-quarter view. The pictogram is standing upright and stationary with arms straight down at the sides and legs together. To indicate Looking Up: the circular head has its glowing cyan facial keypoint dots (nose, mouth, chin) elevated high towards the upper part of the face, accompanied by a clean glowing cyan vertical upward arrow (↑) beside the head indicating head pitch up. Bold definitive pictogram posture, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

## 📂 Segmen 2: Locomotion & Penerbangan Inti (Dasar Terbang) — [STATUS: SELESAI & DISIMPAN]

*Aset tersimpan di:* [`assets/poses_cv_guide/`](file:///e:/Semester%205/Game/AR%20Dragon/assets/poses_cv_guide/) & [`Referensi/Referensi Pose CV/`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi/Referensi%20Pose%20CV/)

### #04 · Takeoff (Lepas Landas — Tangan Mengepak Sayap)
* **File Tersimpan:** `Pose_04_Takeoff.jpg`
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, flat 2D human pictogram silhouette with smooth rounded limbs, circular head, and capsule torso. Dark matte charcoal silhouette centered in a dark slate viewport with subtle blueprint grid lines and corner brackets. Overlaid with glowing wireframe skeleton lines and bright circular joint dots: warm amber on the right, cyan on the left. High-contrast prominent glowing cyan facial keypoint dots on the upward-tilted head. The pictogram is performing a Takeoff wing flap: lower body is strictly stationary with both legs completely straight, vertical, and motionless side-by-side on the floor with zero leg bending. The upper body features a dynamic wing-flapping motion blur effect showing multi-exposure ghosted arms (6 arms total: primary horizontal arms with faint semi-transparent ghosted arms above and below) capturing an active flapping motion streak. Bold definitive pictogram posture, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

### #05 · Lean & Arm Tilt (Kemudi Belok Kiri / Kanan)
* **File Tersimpan:** `Pose_05_LeanArmTilt.jpg`
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, flat 2D human pictogram silhouette with smooth rounded limbs, circular head, and capsule torso. Dark matte charcoal silhouette centered in a dark slate viewport with subtle blueprint grid lines and corner brackets. Overlaid with glowing wireframe skeleton lines and bright circular joint dots: warm amber on the right, cyan on the left. High-contrast prominent glowing cyan facial keypoint dots on the centered level head. The entire upper torso and straight arm bar are sharply tilted at a 40-degree diagonal banking angle (one arm pointing high, one arm pointing low). Crucially, the lower body remains completely upright, straight, and motionless: both legs are perfectly vertical side-by-side with zero knee bending, no lunging, no squatting, feet flat on the floor. Bold definitive pictogram posture, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

### #06 · Flap to Climb (Mengepak Naik Bertahap)
* **File Tersimpan:** `Pose_06_FlapToClimb.jpg`
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, flat 2D human pictogram silhouette with smooth rounded limbs, circular head, and capsule torso. Dark matte charcoal silhouette centered in a dark slate viewport with subtle blueprint grid lines and corner brackets. Overlaid with glowing wireframe skeleton lines and bright circular joint dots: warm amber on the right, cyan on the left. High-contrast prominent glowing cyan facial dots on the slightly raised head. The pictogram is floating in mid-air with both legs held perfectly straight, parallel, and motionless vertically. The arms display a rhythmic climbing wing-flap with semi-transparent motion blur ghosting arms trailing above and below the main arm stroke (multi-arm motion blur effect), showing active flight flapping. Bold definitive pictogram posture, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

### #07 · Fly Up / Y-Pose (Menanjak Curam ke Langit)
* **File Tersimpan:** `Pose_07_FlyUp.jpg`
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, flat 2D human pictogram silhouette with smooth rounded limbs, circular head, and capsule torso. Dark matte charcoal silhouette centered in a dark slate viewport with subtle blueprint grid lines and corner brackets. Overlaid with glowing wireframe skeleton lines and bright circular joint dots: warm amber on the right, cyan on the left. High-contrast prominent glowing cyan facial keypoint dots on the sharply upward-tilted head. The pictogram is in a rigid, static wide 'Y-Pose': both arms raised high overhead completely straight in a bold symmetrical 'Y', head looking directly skyward. Both legs remain completely straight, vertical, and motionless side-by-side with zero bending. Bold definitive pictogram posture, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

### #08 · Glide / T-Pose (Meluncur Datar Sejajar)
* **File Tersimpan:** `Pose_08_Glide.jpg`
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, flat 2D human pictogram silhouette with smooth rounded limbs, circular head, and capsule torso. Dark matte charcoal silhouette centered in a dark slate viewport with subtle blueprint grid lines and corner brackets. Overlaid with glowing wireframe skeleton lines and bright circular joint dots: warm amber on the right, cyan on the left. High-contrast prominent glowing cyan facial keypoint dots on the centered level head. The pictogram is in a pure static mid-air Glide T-Pose: both arms stretched completely straight horizontally at 180 degrees shoulder height in a flat level bar. Both legs are completely straight, vertical, and motionless side-by-side with zero bending. Bold definitive pictogram posture, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

## 📂 Segmen 3: Maneuver Khusus (Turun, Melayang, Menukik, & Dash)

### #09 · Hover Mode (Melayang Diam di Tempat)
* **Bentuk Pose:** **L-Arm Pose (Siku 90°)**. Lengan atas merapat ke torso, siku ditekuk 90° ke depan dada seperti isyarat menahan/berhenti, titik muka netral ke depan.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, flat 2D human pictogram silhouette with smooth rounded limbs, circular head, and capsule torso. Dark matte charcoal silhouette centered in a dark slate viewport with subtle blueprint grid lines and corner brackets. Overlaid with glowing wireframe skeleton lines and bright circular joint dots: warm amber dots on the right limbs, cyan dots on the left limbs. High-contrast prominent glowing cyan facial keypoints on the forward-facing head. Upper arms are clamped tightly to the torso with both elbows bent sharply at a 90-degree angle, forearms extending straight forward horizontally in front of the chest in a clean stop stance, straight legs. Bold definitive pictogram posture, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

### #10 · Gentle Descent (Turun Meluncur Terkontrol)
* **Bentuk Pose:** **Low-A Pose**. Kedua lengan lurus mengarah ke samping bawah diagonal (sudut 45°), kepala dan titik muka menunduk sedikit ke bawah.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, flat 2D human pictogram silhouette with smooth rounded limbs, circular head, and capsule torso. Dark matte charcoal silhouette centered in a dark slate viewport with subtle blueprint grid lines and corner brackets. Overlaid with glowing wireframe skeleton lines and bright circular joint dots: warm amber dots on the right limbs, cyan dots on the left limbs. Prominent glowing cyan facial keypoints on the downward-dipped head. Both arms are held completely straight pointing diagonally downwards at a 45-degree angle in a distinct Low-A posture, straight legs. Bold definitive pictogram posture, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

### #11 · Fly Down / Y-Dive (Menukik Cepat Diagonal)
* **Bentuk Pose:** Lengan atas melebar ke samping, kedua lengan bawah ditekuk tajam ke atas diagonal membentuk sudut Y tertekuk, kepala dan titik muka menunduk tajam.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, flat 2D human pictogram silhouette with smooth rounded limbs, circular head, and capsule torso. Dark matte charcoal silhouette centered in a dark slate viewport with subtle blueprint grid lines and corner brackets. Overlaid with glowing wireframe skeleton lines and bright circular joint dots: warm amber on the right, cyan on the left. Prominent glowing cyan facial keypoints on the head bowed downward in a dive posture. Upper arms are flared wide horizontally while forearms are bent sharply upward at 45 degrees forming a wide angular Y-shape, straight legs. Bold definitive pictogram posture, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

### #12 · Fly Down Idle / W-Descent (Turun Vertikal di Tempat)
* **Bentuk Pose:** **Full W-Pose**. Siku ditarik ke bawah merapat di rusuk, kedua lengan bawah tegak lurus ke atas di samping telinga membentuk huruf "W" simetris, kepala dan titik muka menunduk ke bawah.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, flat 2D human pictogram silhouette with smooth rounded limbs, circular head, and capsule torso. Dark matte charcoal silhouette centered in a dark slate viewport with subtle blueprint grid lines and corner brackets. Overlaid with glowing wireframe skeleton lines and bright circular joint dots: warm amber on the right, cyan on the left. Prominent glowing cyan facial keypoints on the downward-bowed head. The pictogram is in a clean symmetrical 'W-Pose', elbows held low against the sides with both forearms bent straight vertically upward beside the head forming an undeniable 'W' shape, straight legs. Bold definitive pictogram posture, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

### #13 · Tuck to Dive (Menukik Cepat -45°)
* **Bentuk Pose:** **Full Aerodynamic Tuck**. Kedua tangan menyilang rapat di dada atas, siku merapat kuat ke pinggang membentuk kuncupan tubuh kompak, kepala menunduk ke dada.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, flat 2D human pictogram silhouette with smooth rounded limbs, circular head, and capsule torso. Dark matte charcoal silhouette centered in a dark slate viewport with subtle blueprint grid lines and corner brackets. Overlaid with glowing wireframe skeleton lines and bright circular joint dots: warm amber on the right, cyan on the left. Prominent glowing cyan facial keypoints on the deeply tucked head. The pictogram is curled into a tight aerodynamic tuck: both hands and wrists locked firmly against the center of the upper chest, elbows pinned tight to the ribs, straight legs. Bold definitive pictogram posture, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

### #14 · Propeller Dash (Burst Speed Melipat Angin)
* **Bentuk Pose:** Kedua lengan bawah bersilangan tepat di depan dada dalam pose baling-baling memutar (*windmill propeller formation*), titik muka lurus ke depan.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, flat 2D human pictogram silhouette with smooth rounded limbs, circular head, and capsule torso. Dark matte charcoal silhouette centered in a dark slate viewport with subtle blueprint grid lines and corner brackets. Overlaid with glowing wireframe skeleton lines and bright circular joint dots: warm amber on the right, cyan on the left. High-contrast prominent glowing cyan facial keypoints on the centered head. Both forearms are crossed directly in front of the chest with wrists forming an angular windmill propeller formation, straight legs. Bold definitive pictogram posture, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

## 📂 Segmen 4: Village Actions (Aksi Khusus Pemulihan 3 Biome Desa)

### #15A · Zephyria — Wind Flap (Pembersihan Polusi Kabut)
* **Bentuk Pose:** Gerakan kepak sayap penuh menyapu ke bawah sampai samping paha dalam ayunan lebar, titik muka lurus ke depan.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, flat 2D human pictogram silhouette with smooth rounded limbs, circular head, and capsule torso. Dark matte charcoal silhouette centered in a dark slate viewport with subtle blueprint grid lines and corner brackets. Overlaid with glowing wireframe skeleton lines and bright circular joint dots: warm amber on the right, cyan on the left. High-contrast prominent glowing cyan facial keypoints on the forward head. Both arms are held in a wide full-range downward flap stroke sweeping down alongside the thighs, straight legs. Bold definitive pictogram posture, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

### #15B · Pyrewood — Open Mouth Water Breath (Semburan Air Purba)
* **Bentuk Pose:** Potret setengah badan piktogram dengan **mulut terbuka lebar yang sangat kentara**. Titik pelacak cyan di hidung, bibir atas, bibir bawah, dan dagu yang turun terpisah sangat jelas di atas kepala lingkaran.
```text
Minimalist Computer Vision motion capture diagram, flat 2D human pictogram silhouette with smooth rounded limbs, circular head, and capsule torso. Upper torso and head portrait of a sleek dark charcoal pictogram, centered in a dark slate viewport with subtle blueprint grid lines and corner brackets. Glowing skeleton lines on shoulders. The circular head features an unmistakably wide open dropped jaw, with highly prominent, bright glowing cyan facial keypoint dots clearly marking the nose, separated upper lip, lower lip, and dropped chin. Bold definitive pictogram posture, zero clutter, no text, no water effects --ar 1:1 --v 6.0 --style raw
```

---

### #15C · Solaria — Raise Both Hands Dew Rain (Pemanggilan Hujan Embun Suci)
* **Bentuk Pose:** **Double I-Pose Vertikal ke Langit**. Kedua lengan diangkat lurus sejajar tegak lurus ke atas kepala setinggi mungkin, kepala mendongak dengan titik muka menengadah ke langit.
```text
Minimalist Computer Vision motion capture diagram, flat 2D human pictogram silhouette with smooth rounded limbs, circular head, and capsule torso. Dark matte charcoal silhouette centered in a dark slate viewport with subtle blueprint grid lines and corner brackets. Overlaid with glowing wireframe skeleton lines and bright circular joint dots: warm amber on the right, cyan on the left. Highly prominent, bright glowing cyan facial keypoint dots on the upward-tilted head. Both arms are stretched completely straight vertically upwards above the head in parallel lines reaching for the sky, straight legs. Bold definitive pictogram posture, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```
