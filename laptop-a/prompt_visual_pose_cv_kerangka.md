---
title: Prompt Generator Visual Pose Titik & Kerangka CV — Semi-Stickman Anatomis (To The Point)
type: prompt-guide
tags:
  - prompt
  - pose-guide
  - computer-vision
  - stickman
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

# 🎯 Katalog Prompt Visual Pose CV — Edisi Semi-Stickman Anatomis (To The Point)

> **Spesifikasi Karakter & Gaya Visual:**
> 1. **Semi-Stickman Proporsi Manusia:** Figur stickman modern ramping dengan proporsi tubuh manusia yang akurat (panjang torso, lengan, tungkai realistis; kepala bulat sederhana; tanpa baju/kulit realistis).
> 2. **Bentuk Pose "To The Point":** Setiap gestur digambarkan secara tegas, ekstrem, dan tanpa ragu (misal: Ground Walk langsung **Full T-Pose** di darat, W-Pose langsung **Full W**, Y-Pose langsung **Full Y**).
> 3. **Titik Sendi Dual-Tone (*Keypoints*):**
>    - Sisi Kanan Tubuh: Kuning / Amber Gold (`#FFB300`).
>    - Sisi Kiri Tubuh: Cyan / Teal (`#00F5FF`).
>    - Wajah: Titik Cyan di Hidung, Mulut, dan Dagu.
> 4. **Latar Belakang Minimalis:** Square viewport abu-abu gelap dengan garis *blueprint grid* tipis dan aksen siku bidik (*corner brackets*), bersih tanpa efek asap/api/partikel dan tanpa teks.

---

## 🎨 Master Prompt Formula

```text
Minimalist Computer Vision motion capture diagram, semi-stickman figure with realistic human anatomical proportions. Sleek geometric stick figure with slender dark limbs, centered in a dark slate viewport with faint blueprint grid and corner brackets. Glowing skeleton lines and bright circular joint dots: warm amber on the right side and cyan on the left side, with cyan facial dots. [EXACT POSE HERE]. Pure minimalist stick-figure silhouette, high-contrast, bold and definitive posture, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

## 📂 Segmen 1: Grounded Poses (Pose Landasan Darat)

### #01 · Grounded Idle
* **Karakter Pose:** Berdiri tegak lurus sempurna (**I-Pose**). Kedua lengan lurus menempel rapat di samping paha/badan. Kepala lurus ke depan.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, semi-stickman figure with realistic human anatomical proportions. Sleek geometric stick figure with slender dark limbs, centered in a dark slate viewport with faint blueprint grid and corner brackets. Glowing skeleton lines and bright circular joint dots: warm amber on the right side and cyan on the left side, with cyan facial dots. The stickman is in a strict Grounded Idle I-Pose, standing rigidly upright on flat ground with both arms pinned completely straight and tight against the sides of the torso, head looking directly forward. Bold definitive silhouette, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

### #02 · Grounded Walk
* **Karakter Pose:** **Full T-Pose di Darat**. Kaki menapak di tanah, kedua lengan terentang lurus 180° mendatar sejajar bahu (pose tegas to the point).
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, semi-stickman figure with realistic human anatomical proportions. Sleek geometric stick figure with slender dark limbs, centered in a dark slate viewport with faint blueprint grid and corner brackets. Glowing skeleton lines and bright circular joint dots: warm amber on the right side and cyan on the left side, with cyan facial dots. The stickman is performing Grounded Walk in a full rigid T-Pose while standing on the ground: feet planted on the floor, both arms stretched completely straight horizontally at 180 degrees shoulder height. Bold definitive silhouette, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

### #03 · Grounded Look Up (Pitch POV)
* **Karakter Pose:** Badan berdiri tegak santai dengan dada/bahu datar. Kepala dan dagu mendongak tegak lurus ke atas menatap langit.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, semi-stickman figure with realistic human anatomical proportions. Sleek geometric stick figure with slender dark limbs, centered in a dark slate viewport with faint blueprint grid and corner brackets. Glowing skeleton lines and bright circular joint dots: warm amber on the right side and cyan on the left side. The body and shoulders remain strictly upright and neutral, while the head, chin, and neck are sharply tilted straight upward looking directly at the sky. Elevated cyan keypoints on nose and chin. Bold definitive silhouette, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

## 📂 Segmen 2: Locomotion & Penerbangan Inti (Dasar Terbang)

### #04 · Takeoff (Lepas Landas — Tangan Mengepak)
* **Karakter Pose:** **Pose Tangan Mengepak (Wing Flap Takeoff)**. Kaki di darat siap lepas landas, dagu mendongak ke atas, kedua lengan terentang lebar ke samping dengan siku ditekuk membentuk busur sayap mengepak yang tegas (`\_ o _/`) layaknya sayap naga yang sedang mengepak kuat untuk lepas landas ke udara.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, semi-stickman figure with realistic human anatomical proportions. Sleek geometric stick figure with slender dark limbs, centered in a dark slate viewport with faint blueprint grid and corner brackets. Glowing skeleton lines and bright circular joint dots: warm amber on the right side and cyan on the left side. The stickman is performing a dynamic Takeoff wing flap: standing grounded with chin tilted upward, both arms spread wide outward in a bold wing-flapping posture with elbows bent and forearms angled upward like flapping dragon wings. Bold definitive silhouette, clear wing-flapping arm posture, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

### #05 · Lean & Arm Tilt (Kemudi Belok Kiri / Kanan)
* **Karakter Pose:** Kemiringan belok 35°–45°. Garis kedua lengan dan bahu membentuk satu garis diagonal miring lurus yang tajam (satu tangan tinggi, satu tangan rendah), kepala tetap lurus ke depan.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, semi-stickman figure with realistic human anatomical proportions. Sleek geometric stick figure with slender dark limbs, centered in a dark slate viewport with faint blueprint grid and corner brackets. Glowing skeleton lines and bright circular joint dots: warm amber on the right side and cyan on the left side. The entire torso and straight arm bar are sharply tilted at a 40-degree banking angle, one arm reaching high and the other arm low forming a steep diagonal line, while the head remains perfectly level and facing forward. Bold definitive silhouette, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

### #06 · Flap to Climb (Mengepak Naik Bertahap)
* **Karakter Pose:** Puncak kepakan sayap ke atas. Kedua lengan terentang lebar dan melengkung tinggi di atas garis bahu seperti sayap mengembang, dagu terangkat.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, semi-stickman figure with realistic human anatomical proportions. Sleek geometric stick figure with slender dark limbs, centered in a dark slate viewport with faint blueprint grid and corner brackets. Glowing skeleton lines and bright circular joint dots: warm amber on the right side and cyan on the left side. Both arms are held wide and arched high above shoulder level in a bold upward wing-flap stance, elbows curved upward, chin tilted slightly up. Bold definitive silhouette, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

### #07 · Fly Up / Y-Pose (Menanjak Curam ke Langit)
* **Karakter Pose:** **Full Y-Pose**. Kedua lengan lurus terentang tinggi ke atas membentuk huruf "Y" tegas dan simetris, dagu dan kepala mendongak tajam ke atas.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, semi-stickman figure with realistic human anatomical proportions. Sleek geometric stick figure with slender dark limbs, centered in a dark slate viewport with faint blueprint grid and corner brackets. Glowing skeleton lines and bright circular joint dots: warm amber on the right side and cyan on the left side. The stickman is in an unmistakable wide 'Y-Pose', both arms raised high overhead completely straight in a bold symmetrical 'Y', head and chin tilted sharply upward to the sky. Bold definitive silhouette, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

### #08 · Glide / T-Pose (Meluncur Datar Sejajar)
* **Karakter Pose:** **Full T-Pose di Udara**. Kedua lengan lurus sempurna 180° horizontal sejajar garis bahu, melayang mulus di udara, kepala lurus ke depan.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, semi-stickman figure with realistic human anatomical proportions. Sleek geometric stick figure with slender dark limbs, centered in a dark slate viewport with faint blueprint grid and corner brackets. Glowing skeleton lines and bright circular joint dots: warm amber on the right side and cyan on the left side. The stickman is in a pure mid-air Glide T-Pose, floating with both arms stretched completely straight horizontally at 180 degrees shoulder height forming a flat level bar, head centered and level. Bold definitive silhouette, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

## 📂 Segmen 3: Maneuver Khusus (Turun, Melayang, Menukik, & Dash)

### #09 · Hover Mode (Melayang Diam di Tempat)
* **Karakter Pose:** Siku ditekuk 90° tegas merapat di rusuk, kedua telapak tangan/lengan bawah lurus mendatar mengarah ke depan dada (**L-Arm Pose**), kepala netral.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, semi-stickman figure with realistic human anatomical proportions. Sleek geometric stick figure with slender dark limbs, centered in a dark slate viewport with faint blueprint grid and corner brackets. Glowing skeleton lines and bright circular joint dots: warm amber on the right side and cyan on the left side. Upper arms are clamped tightly to the torso with both elbows bent sharply at a 90-degree angle, forearms extending straight forward horizontally in front of the chest in a clean stop stance, head facing forward. Bold definitive silhouette, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

### #10 · Gentle Descent (Turun Meluncur Terkontrol)
* **Karakter Pose:** **Low-A Pose**. Kedua lengan lurus mengarah ke samping bawah diagonal (sudut 45°), kepala dan dagu menunduk ke bawah.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, semi-stickman figure with realistic human anatomical proportions. Sleek geometric stick figure with slender dark limbs, centered in a dark slate viewport with faint blueprint grid and corner brackets. Glowing skeleton lines and bright circular joint dots: warm amber on the right side and cyan on the left side. Both arms are held completely straight pointing diagonally downwards at a 45-degree angle in a distinct Low-A posture, head and chin dipped clearly downward. Bold definitive silhouette, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

### #11 · Fly Down / Y-Dive (Menukik Cepat Diagonal)
* **Karakter Pose:** Lengan atas melebar ke samping, kedua lengan bawah ditekuk tajam ke atas membentuk sudut Y tertekuk, kepala menunduk tajam.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, semi-stickman figure with realistic human anatomical proportions. Sleek geometric stick figure with slender dark limbs, centered in a dark slate viewport with faint blueprint grid and corner brackets. Glowing skeleton lines and bright circular joint dots: warm amber on the right side and cyan on the left side. Upper arms are flared wide horizontally while forearms are bent sharply upward at 45 degrees forming a wide angular Y-shape, head and chin bowed firmly downward in a dive posture. Bold definitive silhouette, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

### #12 · Fly Down Idle / W-Descent (Turun Vertikal di Tempat)
* **Karakter Pose:** **Full W-Pose**. Siku ditarik ke bawah merapat di rusuk, kedua lengan bawah tegak lurus ke atas di samping telinga membentuk huruf "W" simetris sempurna, dagu menunduk ke bawah.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, semi-stickman figure with realistic human anatomical proportions. Sleek geometric stick figure with slender dark limbs, centered in a dark slate viewport with faint blueprint grid and corner brackets. Glowing skeleton lines and bright circular joint dots: warm amber on the right side and cyan on the left side. The stickman is in a clean symmetrical 'W-Pose', elbows held low against the sides with both forearms bent straight vertically upward beside the head forming an undeniable 'W' shape, head and chin bowed downward. Bold definitive silhouette, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

### #13 · Tuck to Dive (Menukik Cepat -45°)
* **Karakter Pose:** **Full Tuck / Kuncup Aerodinamis**. Kedua tangan menyilang rapat di dada atas, siku merapat kuat ke pinggang, kepala menunduk ke dada.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, semi-stickman figure with realistic human anatomical proportions. Sleek geometric stick figure with slender dark limbs, centered in a dark slate viewport with faint blueprint grid and corner brackets. Glowing skeleton lines and bright circular joint dots: warm amber on the right side and cyan on the left side. The stickman is curled into a tight aerodynamic tuck: both hands and wrists locked firmly against the center of the upper chest, elbows pinned tight to the ribs, head and chin tucked deeply down. Bold definitive silhouette, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

### #14 · Propeller Dash (Burst Speed Melipat Angin)
* **Karakter Pose:** Kedua tangan dan pergelangan tangan bersilangan tegak di depan dada dalam pose baling-baling memutar (propeller formation), kepala lurus ke depan.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, semi-stickman figure with realistic human anatomical proportions. Sleek geometric stick figure with slender dark limbs, centered in a dark slate viewport with faint blueprint grid and corner brackets. Glowing skeleton lines and bright circular joint dots: warm amber on the right side and cyan on the left side. Both forearms are crossed directly in front of the chest with wrists forming an angular windmill propeller formation, head centered and facing forward. Bold definitive silhouette, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

## 📂 Segmen 4: Village Actions (Aksi Khusus Pemulihan 3 Biome Desa)

### #15A · Zephyria — Wind Flap (Pembersihan Polusi Kabut)
* **Karakter Pose:** Gerakan kepak sayap penuh ke bawah sampai samping paha dalam ayunan lebar, kepala lurus ke depan.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, semi-stickman figure with realistic human anatomical proportions. Sleek geometric stick figure with slender dark limbs, centered in a dark slate viewport with faint blueprint grid and corner brackets. Glowing skeleton lines and bright circular joint dots: warm amber on the right side and cyan on the left side. Both arms are held in a wide full-range downward flap stroke sweeping down alongside the thighs, head and chin centered and facing straight forward. Bold definitive silhouette, zero clutter, no text, clean UI vector icon --ar 1:1 --v 6.0 --style raw
```

---

### #15B · Pyrewood — Open Mouth Water Breath (Semburan Air Purba)
* **Karakter Pose:** Potret kepala dan torso semi-stickman dengan **mulut terbuka lebar yang sangat kentara** (*jaw dropped wide open*). Titik pelacak cyan di bibir atas dan bibir bawah terpisah jauh (tanpa visual semprotan air yang mengaburkan wajah).
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, semi-stickman figure with realistic human anatomical proportions. Upper torso and head portrait of a sleek geometric stick figure, centered in a dark slate viewport with faint blueprint grid and corner brackets. Glowing skeleton lines on shoulders. The circular stick head features an unmistakably wide open dropped jaw, with glowing cyan keypoint dots distinctly placed on the nose, separated upper lip, lower lip, and dropped chin. Bold definitive silhouette, zero clutter, no text, no water effects --ar 1:1 --v 6.0 --style raw
```

---

### #15C · Solaria — Raise Both Hands Dew Rain (Pemanggilan Hujan Embun Suci)
* **Karakter Pose:** **Double I-Pose Vertikal ke Langit**. Kedua lengan diangkat lurus sejajar tegak lurus ke atas kepala setinggi mungkin, dagu mendongak ke atas.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture diagram, semi-stickman figure with realistic human anatomical proportions. Sleek geometric stick figure with slender dark limbs, centered in a dark slate viewport with faint blueprint grid and corner brackets. Glowing skeleton lines and bright circular joint dots: warm amber on the right side and cyan on the left side. Both arms are stretched completely straight vertically upwards above the head in parallel lines reaching for the sky, head and chin tilted upward. Bold definitive silhouette, zero clutter, no text, no rain effects --ar 1:1 --v 6.0 --style raw
```
