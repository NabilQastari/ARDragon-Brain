---
title: Prompt Generator Visual Pose Titik & Kerangka CV Minimalis (Sesuai Referensi Biometrik)
type: prompt-guide
tags:
  - prompt
  - pose-guide
  - computer-vision
  - skeleton
  - keypoints
  - minimalist
  - biometric
  - laptop-a
date: 2026-11-17
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[planning_audit_pose_dan_clean_cv_preview]]"
---

# 🎯 Katalog Prompt Visual Pose CV — Edisi Minimalis (Biometric Skeleton UI)

> **Spesifikasi Visual Sesuai Referensi:**
> 1. **Siluet Manekin Transparan:** Manekin tubuh manusia 3D abu-abu gelap semi-transparan dengan *rim light* tepi yang sangat halus dan elegan.
> 2. **Garis Kerangka Tipis Bersih (*Wireframe Skeleton*):** Garis vektor tipis bercahaya yang menghubungkan sendi tubuh tanpa efek heboh atau ornamen fantasi.
> 3. **Titik Sendi Dual-Tone (*Keypoints*):**
>    - Sisi Kanan Tubuh: Titik bulat menyala warna **Kuning / Emas Amber** (`#FFB300`).
>    - Sisi Kiri Tubuh: Titik bulat menyala warna **Cyan / Teal** (`#00F5FF`).
>    - Wajah & Poros Tengah: Titik Cyan pada Hidung, Mulut, Dagu, dan Leher.
> 4. **Latar Belakang Minimalis:** Dark charcoal / matte slate square viewport dengan garis grid kotak tipis (*subtle blueprint grid*) dan aksen sudut bidik (*corner brackets*), bersih dari teks acak, bebas partikel magis/asap/api.

---

## 🎨 Master Prompt Formula (Untuk Generator Gambar)

```text
Minimalist Computer Vision motion capture visualization, dark biometric UI interface. A semi-transparent dark matte grey human mannequin silhouette with soft glowing edge rim lighting, centered inside a dark slate square viewport frame with faint blueprint grid lines and subtle corner bracket reticles. Clean glowing wireframe skeleton lines connecting small glowing circular joint nodes: warm amber dots on one side of the body and cyan dots on the other side, with facial keypoints on nose, mouth, and chin. [BODY POSE SPECIFICATION]. Perfectly clean, high precision, minimalist UI diagram, zero clutter, no text, no fantasy magic effects, no particles, studio vector 3D render --ar 1:1 --v 6.0 --style raw
```

---

## 📂 Segmen 1: Grounded Poses (Pose Landasan Darat)

### #01 · Grounded Idle
* **Postur Tubuh:** Berdiri tegak lurus rileks. Kedua lengan lurus merapat di samping paha/torso. Kepala dan dagu tegak lurus netral ke depan.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture visualization, dark biometric UI interface. A semi-transparent dark matte grey human mannequin silhouette with soft glowing edge rim lighting, centered inside a dark slate viewport with faint blueprint grid lines and corner reticle brackets. Clean glowing wireframe skeleton lines connecting small glowing circular joint nodes: warm amber dots on the right side and cyan dots on the left side, facial keypoints on nose, mouth, and chin. The figure is standing upright and completely relaxed, arms resting straight and tucked tight against the sides of the torso, head looking directly forward. Ultra clean, high precision, minimalist telemetry UI, no text, no extra particles --ar 1:1 --v 6.0 --style raw
```

---

### #02 · Grounded Walk
* **Postur Tubuh:** Berdiri santai dengan kedua lengan sedikit terbuka menjauh dari rusuk/paha (*arms away*, bukaan rendah). Kepala menghadap depan.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture visualization, dark biometric UI interface. A semi-transparent dark matte grey human mannequin silhouette with soft glowing edge rim lighting, centered inside a dark slate viewport with faint blueprint grid lines and corner reticle brackets. Clean glowing wireframe skeleton lines connecting small glowing circular joint nodes: warm amber dots on the right side and cyan dots on the left side, facial keypoints on nose, mouth, and chin. The figure is in a walking stance, arms held slightly outward away from the hips in a relaxed low A-pose, showing clear separation between the wrists and the torso. Ultra clean, high precision, minimalist telemetry UI, no text, no extra particles --ar 1:1 --v 6.0 --style raw
```

---

### #03 · Grounded Look Up (Pitch POV)
* **Postur Tubuh:** Berdiri tegak santai dengan dada dan bahu tetap rata rileks. Dagu dan hidung mendongak ke atas menatap langit (titik dagu dan hidung terangkat ke atas melebihi garis horizontal).
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture visualization, dark biometric UI interface. A semi-transparent dark matte grey human mannequin silhouette with soft glowing edge rim lighting, centered inside a dark slate viewport with faint blueprint grid lines and corner reticle brackets. Clean glowing wireframe skeleton lines connecting small glowing circular joint nodes: warm amber dots on the right side and cyan dots on the left side. Torso and shoulders remain flat and relaxed, while the head, chin, and nose are distinctly tilted upward looking towards the ceiling. Elevated cyan keypoints on nose, mouth, and chin. Ultra clean, high precision, minimalist telemetry UI, no text, no extra particles --ar 1:1 --v 6.0 --style raw
```

---

## 📂 Segmen 2: Locomotion & Penerbangan Inti (Dasar Terbang)

### #04 · Takeoff (Lepas Landas)
* **Postur Tubuh:** Dagu sedikit mendongak ke atas, kedua lengan ditekuk dan diayunkan mengepak ke bawah melewati garis bahu secara tegas.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture visualization, dark biometric UI interface. A semi-transparent dark matte grey human mannequin silhouette with soft glowing edge rim lighting, centered inside a dark slate viewport with faint blueprint grid lines and corner reticle brackets. Clean glowing wireframe skeleton lines connecting small glowing circular joint nodes: warm amber dots on the right side and cyan dots on the left side. Chin and head are tilted slightly upward, arms are posed in a decisive downward wing-stroke with elbows and wrists driving downwards past the shoulder level. Ultra clean, high precision, minimalist telemetry UI, no text, no extra particles --ar 1:1 --v 6.0 --style raw
```

---

### #05 · Lean & Arm Tilt (Kemudi Belok Kiri / Kanan)
* **Postur Tubuh:** Torso miring diagonal ke satu sisi, garis bahu dan kedua lengan miring membentuk sudut kemiringan tajam (satu tangan lebih tinggi dari yang lain). Dagu tetap lurus ke depan.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture visualization, dark biometric UI interface. A semi-transparent dark matte grey human mannequin silhouette with soft glowing edge rim lighting, centered inside a dark slate viewport with faint blueprint grid lines and corner reticle brackets. Clean glowing wireframe skeleton lines connecting small glowing circular joint nodes: warm amber dots on the right side and cyan dots on the left side. The upper torso is tilted diagonally to one side in a banking roll posture, with one arm elevated and the other lowered forming a clear diagonal shoulder and arm axis, while the head remains level facing forward. Ultra clean, high precision, minimalist telemetry UI, no text, no extra particles --ar 1:1 --v 6.0 --style raw
```

---

### #06 · Flap to Climb (Mengepak Naik Bertahap)
* **Postur Tubuh:** Kedua lengan terentang lebar ke samping di atas garis bahu dengan siku sedikit melengkung dalam pose kepakan sayap aktif, dagu sedikit terangkat.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture visualization, dark biometric UI interface. A semi-transparent dark matte grey human mannequin silhouette with soft glowing edge rim lighting, centered inside a dark slate viewport with faint blueprint grid lines and corner reticle brackets. Clean glowing wireframe skeleton lines connecting small glowing circular joint nodes: warm amber dots on the right side and cyan dots on the left side. Both arms are spread wide horizontally and elevated slightly above shoulder height in an active wing flap posture, elbows slightly curved, chin slightly raised. Ultra clean, high precision, minimalist telemetry UI, no text, no extra particles --ar 1:1 --v 6.0 --style raw
```

---

### #07 · Fly Up / Y-Pose (Menanjak Curam ke Langit)
* **Postur Tubuh:** Kedua lengan diangkat lurus tinggi ke atas membentuk huruf "Y" simetris di atas kepala, dagu dan hidung mendongak tajam ke atas menatap langit.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture visualization, dark biometric UI interface. A semi-transparent dark matte grey human mannequin silhouette with soft glowing edge rim lighting, centered inside a dark slate viewport with faint blueprint grid lines and corner reticle brackets. Clean glowing wireframe skeleton lines connecting small glowing circular joint nodes: warm amber dots on the right side and cyan dots on the left side. Both arms are extended high overhead forming a crisp symmetrical 'Y' shape, head, chin, and nose are tilted sharply upward looking high above. Ultra clean, high precision, minimalist telemetry UI, no text, no extra particles --ar 1:1 --v 6.0 --style raw
```

---

### #08 · Glide / T-Pose (Meluncur Datar Sejajar)
* **Postur Tubuh:** Kedua lengan terentang lurus horizontal sejajar garis bahu membentuk huruf "T" sempurna. Garis kerangka lengan lurus tanpa lengkungan, kepala netral.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture visualization, dark biometric UI interface. A semi-transparent dark matte grey human mannequin silhouette with soft glowing edge rim lighting, centered inside a dark slate viewport with faint blueprint grid lines and corner reticle brackets. Clean glowing wireframe skeleton lines connecting small glowing circular joint nodes: warm amber dots on the right side and cyan dots on the left side. Arms are spread straight horizontally at shoulder height in a perfect 'T-Pose', forming a flat horizontal line from wrist to wrist, head facing directly forward in neutral posture. Ultra clean, high precision, minimalist telemetry UI, no text, no extra particles --ar 1:1 --v 6.0 --style raw
```

---

## 📂 Segmen 3: Maneuver Khusus (Turun, Melayang, Menukik, & Dash)

### #09 · Hover Mode (Melayang Diam di Tempat)
* **Postur Tubuh:** Lengan atas merapat ke rusuk tubuh, siku ditekuk 90°, kedua tangan berada santai di depan dada. Kepala lurus menghadap depan.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture visualization, dark biometric UI interface. A semi-transparent dark matte grey human mannequin silhouette with soft glowing edge rim lighting, centered inside a dark slate viewport with faint blueprint grid lines and corner reticle brackets. Clean glowing wireframe skeleton lines connecting small glowing circular joint nodes: warm amber dots on the right side and cyan dots on the left side. Upper arms are tucked firmly against the ribs, elbows bent at 90 degrees with hands held quietly in front of the chest, head facing forward in neutral alignment. Ultra clean, high precision, minimalist telemetry UI, no text, no extra particles --ar 1:1 --v 6.0 --style raw
```

---

### #10 · Gentle Descent (Turun Meluncur Terkontrol)
* **Postur Tubuh:** Lengan atas merapat ke tubuh, lengan bawah mengarah santai ke depan-bawah, dagu dan hidung ditundukkan sedikit ke bawah.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture visualization, dark biometric UI interface. A semi-transparent dark matte grey human mannequin silhouette with soft glowing edge rim lighting, centered inside a dark slate viewport with faint blueprint grid lines and corner reticle brackets. Clean glowing wireframe skeleton lines connecting small glowing circular joint nodes: warm amber dots on the right side and cyan dots on the left side. Arms are held close to the torso with forearms angled downwards and forwards at a gentle slope, head and chin are slightly dipped downward in a controlled descent angle. Ultra clean, high precision, minimalist telemetry UI, no text, no extra particles --ar 1:1 --v 6.0 --style raw
```

---

### #11 · Fly Down / Y-Dive (Menukik Cepat Diagonal)
* **Postur Tubuh:** Lengan atas melebar menjauh dari tubuh, siku ditekuk diagonal ke atas membentuk huruf "Y", dagu dan kepala menunduk jelas ke bawah.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture visualization, dark biometric UI interface. A semi-transparent dark matte grey human mannequin silhouette with soft glowing edge rim lighting, centered inside a dark slate viewport with faint blueprint grid lines and corner reticle brackets. Clean glowing wireframe skeleton lines connecting small glowing circular joint nodes: warm amber dots on the right side and cyan dots on the left side. Upper arms are flared wide away from the ribs with forearms angled upward diagonally in an angular 'Y' posture, head and chin are tucked downward in a dive posture. Ultra clean, high precision, minimalist telemetry UI, no text, no extra particles --ar 1:1 --v 6.0 --style raw
```

---

### #12 · Fly Down Idle / W-Descent (Turun Vertikal di Tempat)
* **Postur Tubuh:** Lengan atas merapat ke sisi tubuh, kedua lengan bawah ditekuk tegak ke atas di samping telinga membentuk huruf "W" simetris, dagu menunduk ke bawah.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture visualization, dark biometric UI interface. A semi-transparent dark matte grey human mannequin silhouette with soft glowing edge rim lighting, centered inside a dark slate viewport with faint blueprint grid lines and corner reticle brackets. Clean glowing wireframe skeleton lines connecting small glowing circular joint nodes: warm amber dots on the right side and cyan dots on the left side. Upper arms are held tight against the torso while forearms are bent vertically upward beside the head forming a symmetrical 'W' shape, head and chin bowed downward. Ultra clean, high precision, minimalist telemetry UI, no text, no extra particles --ar 1:1 --v 6.0 --style raw
```

---

### #13 · Tuck to Dive (Menukik Cepat -45°)
* **Postur Tubuh:** Kedua tangan dirapatkan erat di tengah dada atas, siku merapat kuat ke pinggang membentuk kuncupan tubuh kompak, kepala dan dagu menunduk tajam.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture visualization, dark biometric UI interface. A semi-transparent dark matte grey human mannequin silhouette with soft glowing edge rim lighting, centered inside a dark slate viewport with faint blueprint grid lines and corner reticle brackets. Clean glowing wireframe skeleton lines connecting small glowing circular joint nodes: warm amber dots on the right side and cyan dots on the left side. Hands are pulled tightly against the upper chest with elbows tucked hard against the ribs in a streamlined aerodynamic tuck posture, head and chin tucked sharply downward. Ultra clean, high precision, minimalist telemetry UI, no text, no extra particles --ar 1:1 --v 6.0 --style raw
```

---

### #14 · Propeller Dash (Burst Speed Melipat Angin)
* **Postur Tubuh:** Kedua pergelangan tangan berada tepat di depan dada dalam pose memutar horizontal melingkar, kepala lurus menghadap depan.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture visualization, dark biometric UI interface. A semi-transparent dark matte grey human mannequin silhouette with soft glowing edge rim lighting, centered inside a dark slate viewport with faint blueprint grid lines and corner reticle brackets. Clean glowing wireframe skeleton lines connecting small glowing circular joint nodes: warm amber dots on the right side and cyan dots on the left side. Both hands and forearms are positioned in front of the chest, wrists aligned as if in a circular revolving propeller motion, head centered and facing forward. Ultra clean, high precision, minimalist telemetry UI, no text, no extra particles --ar 1:1 --v 6.0 --style raw
```

---

## 📂 Segmen 4: Village Actions (Aksi Khusus Pemulihan 3 Biome Desa)

### #15A · Zephyria — Wind Flap (Pembersihan Polusi Kabut)
* **Postur Tubuh:** Kedua lengan terentang lebar di samping tubuh dalam posisi kepakan sayap bawah (menuju ke arah paha), kepala lurus ke depan.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture visualization, dark biometric UI interface. A semi-transparent dark matte grey human mannequin silhouette with soft glowing edge rim lighting, centered inside a dark slate viewport with faint blueprint grid lines and corner reticle brackets. Clean glowing wireframe skeleton lines connecting small glowing circular joint nodes: warm amber dots on the right side and cyan dots on the left side. Both arms are extended in a wide rhythmic downward wing-flap motion beside the torso, head and chin centered and facing straight forward. Ultra clean, high precision, minimalist telemetry UI, no text, no extra particles --ar 1:1 --v 6.0 --style raw
```

---

### #15B · Pyrewood — Open Mouth Water Breath (Semburan Air Purba)
* **Postur Tubuh:** Tampilan potret setengah badan menghadap tepat ke kamera dengan **mulut terbuka lebar** (*Mouth Aspect Ratio > 0.45*). Titik deteksi wajah cyan di hidung, kedua ujung bibir atas/bawah, dan dagu menunjukkan bukaan rahang yang jelas. Tangan rileks di samping dada.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture visualization, dark biometric UI interface. A semi-transparent dark matte grey human mannequin silhouette with soft glowing edge rim lighting, upper body portrait centered inside a dark slate viewport with faint blueprint grid lines and corner reticle brackets. Clean glowing wireframe skeleton lines on shoulders and arms. Facial keypoint tracking dots in glowing cyan distinctly outlining the nose, upper lip, separated lower lip, and dropped chin showing an open mouth posture. Ultra clean, high precision, minimalist telemetry UI, no text, no water VFX, no extra particles --ar 1:1 --v 6.0 --style raw
```

---

### #15C · Solaria — Raise Both Hands Dew Rain (Pemanggilan Hujan Embun Suci)
* **Postur Tubuh:** Kedua lengan lurus tegak ke langit di atas kepala dengan telapak tangan terbuka menengadah ke atas. Dagu sedikit terangkat ke atas.
* **Prompt AI:**
```text
Minimalist Computer Vision motion capture visualization, dark biometric UI interface. A semi-transparent dark matte grey human mannequin silhouette with soft glowing edge rim lighting, centered inside a dark slate viewport with faint blueprint grid lines and corner reticle brackets. Clean glowing wireframe skeleton lines connecting small glowing circular joint nodes: warm amber dots on the right side and cyan dots on the left side. Both arms are held completely vertical straight up above the head, wrists reaching high with open palms, head and chin tilted slightly upward. Ultra clean, high precision, minimalist telemetry UI, no text, no rain VFX, no extra particles --ar 1:1 --v 6.0 --style raw
```
