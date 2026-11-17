---
title: Prompt Generator Visual Pose Titik & Kerangka CV (15 Pose Fanamir)
type: prompt-guide
tags:
  - prompt
  - pose-guide
  - computer-vision
  - skeleton
  - keypoints
  - laptop-a
  - visual-assets
date: 2026-11-17
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[planning_audit_pose_dan_clean_cv_preview]]"
  - "[[planning_revisi_titik_wajah_dan_pov]]"
---

# 🤖 Katalog Prompt Visual Pose — Titik & Kerangka CV (YOLOv8-Pose / MediaPipe Style)

> Dokumen acuan prompt generator gambar (Midjourney v6, DALL-E 3, Stable Diffusion XL, Flux, Leonardo AI) untuk menghasilkan visual panduan pose berformat **Visual Titik (*Keypoints*) + Kerangka (*Skeleton Wireframe*) Computer Vision**.
>
> Visual ini dirancang khusus untuk merepresentasikan skema kontrol ergonomis terbaru **Fanamir: The Sky Guardian (v2.0)**:
> 1. Elevasi vertikal dikendalikan murni oleh kepala (Hidung & Dagu) — dada tetap rileks.
> 2. Kemudi horizontal dikendalikan murni oleh kemiringan badan & lengan (*Lean & Arm Tilt*).
> 3. Semburan air purba dikendalikan oleh bukaan mulut (*Open Mouth*).

---

## 🎨 Master Style Directive (Gaya Visual Utama)

Untuk menjaga konsistensi di semua pose, gunakan formula dasar berikut:

* **Subjek:** Siluet figur manusia modern/atletis minimalis berwarna netral (matte dark slate atau translusen holografik halus) dengan orientasi menghadap kamera (*front view* atau *three-quarter front view*).
* **Overlay CV Keypoints:** Lingkaran titik sendi menyala tegas bergradasi warna:
  * **Titik Wajah:** Cyan cerah (`#00F5FF`) pada Hidung (*Nose*), Dagu (*Chin*), dan Mulut (*Mouth*).
  * **Titik Lengan/Bahu:** Emas ambar hangat (`#FFB300`) atau neon teal pada Bahu, Siku, dan Pergelangan Tangan.
  * **Titik Torso/Pinggul:** Biru kobalt menyala pada Tulang Belakang (*Spine*) dan Pinggul (*Hips*).
* **Overlay CV Skeleton:** Garis-garis vektor tipis bercahaya (*glowing vector wireframe lines*) yang menghubungkan titik-titik sendi membentuk diagram anatomi kerangka gerak Computer Vision yang presisi.
* **Latar Belakang:** Latar belakang gelap teknologis minimalis (*clean dark tech backdrop*, vignetting halus, tanpa teks berantakan, tanpa watermark).
* **Rasio Aspek:** Square `1:1` (cocok untuk kartu ikon UI) atau `4:3` / `16:9` untuk lembar panduan.

---

## 📂 Segmen 1: Grounded Poses (Pose Darat & Kalibrasi Dasar)

### #01 · Grounded Idle
* **Kategori:** Darat · 0 m/s · Diam Netral
* **Geometri CV:** Figur berdiri atau duduk tegak rileks. Kedua lengan rapat di samping paha/rusuk (*tucked*). Dagu dan hidung berada pada garis horizontal netral (`Head Pitch = 0.0`).
* **Prompt AI:**
```text
A clean minimalist Computer Vision motion capture visualization of a human figure in Grounded Idle pose, front view, standing upright and relaxed. Arms are straight and tucked closely to the sides of the torso. Crisp glowing circular keypoint dots at nose, chin, mouth, shoulders, elbows, wrists, and hips. Thin glowing neon cyan and amber wireframe skeleton lines connecting the joints. Modern UI tech diagram aesthetic, dark slate background with subtle studio vignette, high contrast, clean vector style, no messy text, professional game UI icon --ar 1:1 --v 6.0 --style raw
```

---

### #02 · Grounded Walk
* **Kategori:** Darat · 3.5 m/s · Jalan Kaki
* **Geometri CV:** Figur berdiri santai dengan kedua lengan terbuka melebar ke samping bawah menjauh dari rusuk (*arms away*). Titik pergelangan tangan terpisah dari pinggul. Kepala netral menghadap depan.
* **Prompt AI:**
```text
A minimalist Computer Vision pose estimation illustration of a human figure in Grounded Walk pose, front view. Arms are spread slightly outward away from the ribs in a relaxed low A-pose. Glowing neon joint nodes at nose, chin, shoulders, elbows, wrists, and hips. Bright luminous vector skeleton lines linking the upper body limbs, showing arm separation from the torso. Subtle motion directional arrows along the limbs. Clean dark navy backdrop, tech HUD infographic aesthetic, clean vector rendering --ar 1:1 --v 6.0 --style raw
```

---

### #03 · Grounded Look Up (Pitch POV)
* **Kategori:** Darat · 0 m/s · Pandangan Mendongak
* **Geometri CV:** Figur berdiri santai dengan dada dan bahu tetap rata rileks (tidak membusung). Dagu dan hidung terangkat mendongak ke atas menatap langit. Titik dagu berada jelas di atas garis bahu dengan panah elevasi vertikal kecil.
* **Prompt AI:**
```text
A minimalist Computer Vision motion capture diagram of a human figure performing Grounded Look Up pose, front three-quarter view. The body and shoulders remain completely relaxed and neutral, while the chin and nose are tilted upward looking towards the sky. Glowing cyan keypoint dots on the lifted chin and nose with an upward elevation vector arrow. Glowing amber keypoint nodes at relaxed shoulders, elbows, and wrists. Thin luminous wireframe skeleton lines. Clean dark technical background, high readability, UI pose card illustration --ar 1:1 --v 6.0 --style raw
```

---

## 📂 Segmen 2: Locomotion & Penerbangan Inti (Dasar Terbang)

### #04 · Takeoff (Lepas Landas)
* **Kategori:** Transisi Darat-ke-Udara · Dorongan Kuat
* **Geometri CV:** Figur dengan dagu sedikit mendongak ke atas, kedua lengan digerakkan mengayun ke bawah melewati garis bahu secara tegas. Titik pergelangan tangan berada di bawah siku dengan garis lengkung trajektori kepakan ke bawah (*downward flap arc*).
* **Prompt AI:**
```text
A Computer Vision pose detection illustration of a human figure executing a powerful Takeoff motion, front view. Chin is tilted slightly upward, arms are shown midway through a strong downward wing flap stroke with wrists thrusting downwards past the shoulder plane. Glowing cyan nodes on nose and chin, glowing gold keypoint dots on shoulders, elbows, and hands. Luminous trajectory curved arc vectors indicating downward arm motion. Dark high-tech background, clean motion capture overlay aesthetic --ar 1:1 --v 6.0 --style raw
```

---

### #05 · Lean & Arm Tilt (Kemudi Belok Kiri / Kanan)
* **Kategori:** Kemudi Belok Horizontal · Yaw & Roll
* **Geometri CV:** Torso figur miring secara diagonal (misal miring 25°–35° ke kanan), atau lengan miring seperti kemudi sayap (satu pergelangan tangan jauh lebih tinggi dari yang lain). Posisi kepala dan dagu tetap lurus netral.
* **Prompt AI:**
```text
A clean minimalist Computer Vision skeletal diagram of a human figure performing Lean and Arm Tilt banking maneuver, front view. The upper body torso is tilted diagonally to the right side, with the left arm elevated and the right arm lowered, forming a banked flight posture. Head and chin remain level and facing forward. Glowing cyan keypoint dots at nose and chin, vivid neon amber joint dots at shoulders, elbows, and wrists showing the tilted angle. Glowing wireframe skeleton line connecting the inclined shoulders. Dark tech slate background, precision HUD UI icon --ar 1:1 --v 6.0 --style raw
```

---

### #06 · Flap to Climb (Mengepak untuk Menanjak)
* **Kategori:** Udara · 12–19.5 m/s · Menanjak Bertahap
* **Geometri CV:** Figur dengan kedua lengan terentang lebar di samping tubuh dalam siklus kepakan ritmis (lengan terangkat di atas bahu lalu mengayun turun). Dagu sedikit terangkat ke atas.
* **Prompt AI:**
```text
A dynamic Computer Vision motion analysis illustration of a human figure performing rhythmic Flap to Climb wing flapping, front view. Both arms are fully extended sideways with elbows slightly curved, displaying an active vertical flapping motion cycle. Chin is tilted slightly up. Glowing neon nodes at nose, chin, shoulders, elbows, and wrists, connected by luminous cyan wireframe skeleton lines. Semi-transparent ghosting trail arcs showing the upward and downward wing stroke path. Dark charcoal background, clean sports science / game HUD aesthetic --ar 1:1 --v 6.0 --style raw
```

---

### #07 · Fly Up / Y-Pose (Menanjak Curam ke Langit)
* **Kategori:** Udara · Menanjak Curam
* **Geometri CV:** Figur mengangkat kedua tangan lurus tinggi ke atas membentuk huruf "Y" sempurna di atas kepala. Dagu dan hidung mendongak tajam ke atas menatap langit. Dada rileks.
* **Prompt AI:**
```text
A Computer Vision pose estimation illustration of a human figure in Fly Up Y-Pose, front view. Both arms are raised high overhead forming a wide 'Y' shape, hands open towards the sky. The head and chin are distinctly tilted upward looking high above. Glowing cyan keypoints on nose and chin with an upward vertical vector arrow. Glowing amber keypoint circles on elevated wrists, elbows, and shoulders connected by crisp glowing wireframe skeleton bones. Minimal dark background, clean UI instruction card style --ar 1:1 --v 6.0 --style raw
```

---

### #08 · Glide / T-Pose (Meluncur Datar Sejajar)
* **Kategori:** Udara · 14 m/s · Meluncur Datar Stabil
* **Geometri CV:** Kedua lengan terentang lurus horizontal sejajar dengan garis bahu membentuk huruf "T" sempurna. Kepala dan dagu lurus menghadap ke depan. Garis kerangka lengan lurus horizontal tanpa lengkungan.
* **Prompt AI:**
```text
A sleek minimalist Computer Vision skeleton visualization of a human figure in perfect Glide T-Pose, front view. Arms are spread perfectly straight horizontally at shoulder height, forming a flawless 'T' shape. Head and chin are centered and facing straight ahead in neutral alignment. Brilliant glowing gold and cyan joint keypoints at nose, chin, shoulders, elbows, and wrists. Perfectly straight horizontal glowing wireframe lines connecting the arm bones. Dark tech minimalist background, crisp vector UI graphic --ar 1:1 --v 6.0 --style raw
```

---

## 📂 Segmen 3: Maneuver Khusus (Turun, Melayang, Menukik, & Dash)

### #09 · Hover Mode (Melayang Diam di Tempat)
* **Kategori:** Udara · 0 m/s · Melayang Diam
* **Geometri CV:** Lengan atas merapat ke rusuk tubuh, siku ditekuk 90°–120°, kedua telapak tangan berada santai di depan dada. Kepala netral menghadap lurus ke depan.
* **Prompt AI:**
```text
A Computer Vision pose detection diagram of a human figure in Hover Mode, front view. Upper arms are tucked closely against the sides of the chest, elbows bent at approximately 90 degrees with open palms held calmly in front of the chest. Head is straight and level. Glowing cyan keypoint nodes on nose and chin, bright glowing amber nodes at shoulders, bent elbows, and wrists in front of torso. Fine glowing skeleton connector lines. Clean dark background, futuristic minimalist telemetry HUD style --ar 1:1 --v 6.0 --style raw
```

---

### #10 · Gentle Descent (Turun Meluncur Terkontrol)
* **Kategori:** Udara · 8.5 m/s · Turun Halus
* **Geometri CV:** Lengan atas merapat ke badan, lengan bawah mengarah santai ke depan-bawah. Dagu dan hidung ditundukkan sedikit ke bawah.
* **Prompt AI:**
```text
A minimalist Computer Vision pose overlay illustration of a human figure in Gentle Descent pose, front three-quarter view. Arms are held close to the torso with forearms angled gently downwards and forwards. Head and chin are slightly dipped downward in a subtle pitch down posture. Glowing cyan keypoint dots on downward-tilted nose and chin with a soft downward trajectory arrow. Glowing amber nodes on shoulders, elbows, and lower wrists. Dark tech backdrop, sleek motion analysis aesthetic --ar 1:1 --v 6.0 --style raw
```

---

### #11 · Fly Down / Y-Dive (Menukik Cepat Diagonal)
* **Kategori:** Udara · 12 m/s · Menukik Tajam
* **Geometri CV:** Lengan atas melebar menjauh dari tubuh, siku ditekuk mengarah ke atas diagonal membentuk huruf "Y" tertekuk, dagu dan kepala menunduk jelas ke bawah.
* **Prompt AI:**
```text
A Computer Vision skeleton diagram of a human figure in Fly Down Y-Pose dive, front view. Upper arms are flared outward away from the body with forearms bent upwards diagonally in a wide angular 'Y' posture, while the head and chin are bowed downward in a strong dive posture. Glowing cyan keypoints on the lowered chin and nose, bright glowing orange joint dots on shoulders, bent elbows, and raised wrists. Crisp glowing wireframe skeleton overlay, clean dark tech background --ar 1:1 --v 6.0 --style raw
```

---

### #12 · Fly Down Idle / W-Descent (Turun Vertikal di Tempat)
* **Kategori:** Udara · 0 m/s · Turun Vertikal
* **Geometri CV:** Lengan atas menempel di rusuk, kedua lengan bawah ditekuk tegak ke atas di samping kepala membentuk huruf "W" simetris. Dagu dan kepala menunduk ke bawah.
* **Prompt AI:**
```text
A Computer Vision motion tracking illustration of a human figure in Fly Down Idle W-Pose, front view. Upper arms are tucked close to the ribs, with both forearms bent vertically upward beside the head forming a symmetrical 'W' shape. Chin and head are tilted downward. Glowing cyan nodes at nose and chin, vivid glowing amber keypoints at shoulders, lower elbows, and upright wrists. Thin luminous wireframe skeleton lines, dark slate tech background, clean graphic design icon --ar 1:1 --v 6.0 --style raw
```

---

### #13 · Tuck to Dive (Menukik Cepat -45°)
* **Kategori:** Udara · Kecepatan Ekstrem 28 m/s · Menukik Tajam
* **Geometri CV:** Kedua tangan dan kepalan tangan dirapatkan erat di tengah dada atas, siku merapat ke pinggang membentuk kuncupan aerodinamis kompak. Dagu dan kepala menunduk tajam ke bawah.
* **Prompt AI:**
```text
A high-contrast Computer Vision pose estimation illustration of a human figure in Tuck to Dive aerodynamic posture, front view. Hands and fists are clenched tightly against the upper chest with elbows tucked firmly against the ribs in a compact streamlined dive tuck. Head and chin are tucked sharply downward. Glowing cyan keypoints on lowered chin and nose, intense glowing gold nodes on shoulders, tightly tucked elbows, and wrists centered on chest. Dynamic downward speed motion lines around the silhouette, dark tech background, punchy game HUD art --ar 1:1 --v 6.0 --style raw
```

---

### #14 · Propeller Dash (Burst Speed 26 m/s)
* **Kategori:** Udara · Kecepatan Semburan Kilat
* **Geometri CV:** Kedua tangan berputar melingkar di depan dada secara dinamis. Kepala dan dagu tegak lurus ke depan. Garis lingkaran trajektori rotasi bercahaya mengitari kedua pergelangan tangan.
* **Prompt AI:**
```text
A dynamic Computer Vision motion capture visualization of a human figure executing the Propeller Dash gesture, front view. Both forearms and hands are spinning continuously in a rapid circular propeller motion in front of the chest. Head is straight and focused forward. Glowing cyan keypoints on nose and chin, glowing amber nodes on shoulders and elbows, with glowing circular motion streak rings around the rotating wrists. Sonic burst wave rings emanating outward, dark tech backdrop, high readability vector UI style --ar 1:1 --v 6.0 --style raw
```

---

## 📂 Segmen 4: Village Actions (Aksi Khusus Pemulihan 3 Biome Desa)

### #15A · Zephyria — Wind Flap (Pembersihan Polusi Kabut)
* **Kategori:** Desa 1 (Smog Valley) · Pemulihan Kabut
* **Geometri CV:** Figur mengepakkan kedua tangan ke samping secara ritmis dan kuat dari posisi bahu ke paha untuk menghasilkan hembusan angin. Kepala lurus menghadap depan.
* **Prompt AI:**
```text
A Computer Vision motion tracking diagram of a human figure performing the Zephyria Wind Flap ritual gesture, front view. Both arms are actively flapping down and up in powerful wide rhythmic wing strokes to generate wind gusts. Head and chin are level and forward. Glowing cyan keypoints on nose and chin, bright glowing emerald green and amber nodes on shoulders, elbows, and wrists, with expanding cyan wind draft wave vectors around the figure. Clean dark slate background, fantasy exergaming UI card style --ar 1:1 --v 6.0 --style raw
```

---

### #15B · Pyrewood — Open Mouth Water Breath (Semburan Air Purba)
* **Kategori:** Desa 2 (Wildfire Woods) · Pemadam Kebakaran
* **Geometri CV:** Figur menghadap tepat ke arah webcam dengan mulut terbuka lebar (*Mouth Aspect Ratio > 0.45*). Titik bibir atas dan bawah terpisah jelas dengan lingkaran deteksi bukaan mulut (*mouth tracking bounding box*). Tangan santai atau ditangkupkan.
* **Prompt AI:**
```text
A specialized Computer Vision facial and body tracking illustration of a human figure in Open Mouth Water Breath gesture, front close-up portrait view. The mouth is opened wide in an active breath-casting stance. Glowing cyan keypoint dots distinctly highlighting the nose, upper lip, lower lip, and chin, with a high-tech circular facial tracking reticle around the open mouth showing mouth aperture tracking. Glowing amber nodes on shoulders and chest. A clean stylized glowing blue water stream effect emerging forward. Minimal dark background, precision CV HUD art --ar 1:1 --v 6.0 --style raw
```

---

### #15C · Solaria — Raise Both Hands Dew Rain (Pemanggilan Hujan Embun Suci)
* **Kategori:** Desa 3 (Drought Plateau) · Pemulihan Kekeringan
* **Geometri CV:** Kedua tangan terangkat lurus tegak ke langit di atas kepala dengan telapak tangan terbuka menengadah. Dagu dan hidung sedikit mendongak ke atas menatap langit.
* **Prompt AI:**
```text
A majestic Computer Vision motion estimation illustration of a human figure performing the Solaria Dew Rain gesture, front view. Both arms are held completely straight high above the head, palms open and reaching toward the sky in a blessing ritual. Head and chin are tilted slightly upward. Glowing cyan keypoints at nose and chin, brilliant radiant gold and purple keypoints at shoulders, elbows, and high-reaching wrists, with fine glowing skeleton lines. Stylized falling dew drop particles above. Dark elegant background, game UI instruction card --ar 1:1 --v 6.0 --style raw
```

---

## 💡 Tips & Rekomendasi Penggunaan Prompt

1. **Midjourney v6:**
   * Gunakan akhiran `--ar 1:1` untuk ikon/kartu pose, atau `--ar 16:9` untuk banner horizontal.
   * Parameter `--style raw --v 6.0` disarankan agar model tidak menambahkan ornamen fantasi berlebihan dan tetap setia pada estetika Computer Vision HUD.
2. **DALL-E 3 / ChatGPT:**
   * Tambahkan instruksi awal: *"Create a clean 2D vector style game UI icon featuring Computer Vision keypoints and skeleton lines..."*
3. **Stable Diffusion XL / Flux:**
   * Masukkan kata kunci negatif (*negative prompt*): `photorealistic human, messy text, watermarks, deformed fingers, extra limbs, cluttered background, blurry, low resolution`.
