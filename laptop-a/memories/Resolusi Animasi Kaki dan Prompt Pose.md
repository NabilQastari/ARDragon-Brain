---
title: Resolusi Animasi Kaki dan Prompt Pose
type: session-memory
tags:
  - session-memory
  - laptop-a
  - animation
  - threejs
  - blender
  - dragon-rig
  - prompt-engineering
date: 2026-11-24
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[visual-vfx/evaluasi_dan_revisi_pose_tahap_1]]"
  - "[[visual-vfx/prompt_eksekusi_animasi_tahap_1_laptop_b]]"
  - "[[visual-vfx/studio_animasi_threejs_fanamir]]"
---

# 📝 Resolusi Animasi Kaki Naga & Paket Prompt Referensi Pose

## 1. Tujuan & Konteks Sesi
- Mengatasi bug pose kaki aneh/remuk dan badan menukik pada Three.js Animation Studio (`dragon_anim_studio/`) serta kode ekspor Unity C# (`DragonBodyAnimator.cs`) agar cocok dengan 2 lembar rujukan resmi pengguna (`Referensi/Ref_Dragon_Hover_Leg_Anatomy.jpg` & `Referensi/Ref_Dragon_Flight_Leg_Anatomy.jpg`).
- Menyusun paket lengkap prompt generator gambar (multi-angle + variasi frame animasi) untuk seluruh pose Fanamir menggunakan pendekatan modular agar pengguna dapat menghasilkan referensi visual lanjutan dengan melampirkan gambar model 3D Fanamir.
- Menetapkan transisi alur kerja baru di akhir sesi: implementasi animasi ditunda sementara untuk memprioritaskan desain sistem Audio, sembari menyiapkan sesi khusus untuk setup Graphify pada proyek Three.js.

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Resolusi Matematis Rig `ARM_Dragon` (Blender LTS 5.2.3 CLI & Three.js):**
   - **Inversi Pitch Badan:** Pada konvensi glTF/Three.js, rotasi `+X` memiringkan moncong ke atas (*pitch up*). Penggunaan `-bodyPitch` sebelumnya membenamkan kepala naga $-30^\circ$ ke tanah dan membalik elevasi dada. Diubah menjadi `rootPitch = bodyPitch` positif.
   - **Sumbu Lokal Paha Non-Uniform (`Thigh_Hind`):** Sumbu lokal X `Thigh_Hind.L` berorientasi diagonal $\approx 50^\circ$ `(-0.64, -0.77, 0.00)`. Untuk meluruskan kaki belakang sejajar ekor (*Streamlined Flight*), wajib menerapkan *intrinsic roll* **$rz = +90^\circ$ (Kiri) / $-90^\circ$ (Kanan)** agar artikulasi kaki berada di bidang sagital.
   - **Sumbu Lokal Tulang Kering vs Telapak:** `Shin_Hind.L` (`+0.99` X) dan `Metatarsus_Hind.L` (`-1.00` X) bertolak belakang $180^\circ$, sehingga membutuhkan tanda rotasi yang dikalibrasi terpisah agar tidak menekuk berlawanan arah.
2. **Arsitektur Animasi Modular (*Layered System*):**
   - Pengguna menegaskan bahwa **`#03 Look Up` bukan pose statis terpisah**, melainkan lapisan **Universal Dynamic Head & Neck Movement** (3 sumbu CV) yang bekerja di atas pose apa pun.
   - Sebagian besar pose berbagi **4 Sikap Dasar Badan & Kaki Universal**:
     1. *Grounded Stance* (`#01 Idle`, `#02 Walk`).
     2. *Hover Stance* (`#09A`, `#15A`, `#15B`, `#15C`, `#04` transisi).
     3. *Streamlined Flight Stance* (`#08`, `#06`, `#06B`, `#07`, `#05`, `#11`, `#13`, `#14`).
     4. *Landing / Descent Prep Stance* (`#10`, `#12` variasi vertikal).
3. **Strategi Alur Kerja Lanjutan:**
   - **Prioritas Utama Berikutnya:** Implementasi animasi diundur sementara, alur kerja langsung melompat ke perancangan **Sistem Audio (BGM & SFX)**.
   - **Alur Paralel Three.js:** Proyek Three.js (`dragon_anim_studio/`) akan dipasangi **Graphify** terlebih dahulu sebelum melanjutkan perancangan animasi berikutnya.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **`dragon_anim_studio/studio.js`:** Memperbarui solver prosedural kaki, pitch badan, kurva dada, counter-pitch kepala, serta fungsi `generateUnityCSharpSnippet()` agar kode ekspor C# Unity menggunakan sudut Euler terverifikasi (sintaks JS diverifikasi `node -c` exit code `0`).
- **`dragon_anim_studio/index.html` & `Fanamir_AnimStudio_Standalone/app/`:** Memperbarui cache buster ke `studio.js?v=20261124_v3` dan menyinkronkan file aplikasi mandiri.
- **`Fanamir_AnimStudio_Standalone.zip`:** Dikompresi ulang menjadi paket mandiri terbaru (`13,882,816` bytes).
- **`brain/laptop-a/visual-vfx/prompt_eksekusi_animasi_tahap_1_laptop_b.md`:** Menambahkan tabel angka rotasi Euler terverifikasi untuk Mode Hover dan Mode Flight.
- **Paket Prompt Image Generator (4 Sesi Lengkap di Chat):** Menyusun seluruh prompt siap salin beserta panduan lampiran referensi untuk:
  - *Sesi 1 (Grounded + Universal):* `#01 Idle`, `#02 Walk (4-Frame Trot)`, Universal Dynamic Head/Neck.
  - *Sesi 2 (Core Flight & Climb):* `#04 Takeoff (4-Frame)`, `#05 Banking Turn`, `#06 Flap to Climb (4-Frame)`, `#06B Steep Climb (3-Frame)`, `#07 Y-Glide`, `#08 Glide Front/Top`.
  - *Sesi 3 (Special Maneuvers & Hover):* `#09A Hover Flutter (4-Frame)`, `#09B Slow Glide`, `#10 Gentle Descent (Landing Prep)`, `#11 Y-Dive`, `#12 W-Pose`, `#13 Tuck Dive`, `#14 Propeller Dash (4-Frame Power Flap)`.
  - *Sesi 4 (Village Actions):* `#15A Zephyria (4-Frame Storm Sweep)`, `#15B Pyrewood (Jaw -30° Water Breath)`, `#15C Solaria (Double-I "11" Ritual)`.

---

## 4. Detail Penting yang Harus Diingat
- **Sudut Terverifikasi Hover Mode (`Ref_Dragon_Hover_Leg_Anatomy.jpg`):**
  - `bodyPitch`: $+28^\circ \sim +35^\circ$, `Spine_01`: $+6.3^\circ$, `Spine_02`: $+7.7^\circ$, `Neck_01`: $-11.4^\circ$, `Neck_02`: $-10.0^\circ$, `Head`: $-7.1^\circ$.
  - Forelegs: `Shoulder` `-5°`, `UpperArm` `+45° (rz ∓10°)`, `Forearm` `-65° (rz ±6°)`, `Paw` `+25°`.
  - Hindlegs: `Thigh` `+18° (rz ∓6°)`, `Shin` `+38°`, `Metatarsus` `-30°`, `Foot` `-10°`.
- **Sudut Terverifikasi Flight Mode (`Ref_Dragon_Flight_Leg_Anatomy.jpg`):**
  - `bodyPitch`: $0^\circ$.
  - Forelegs: `Shoulder` `+15° (rz ∓5°)`, `UpperArm` `+65° (rz ∓14°)`, `Forearm` `+105° (rz ±10°)`, `Paw` `-40°`.
  - Hindlegs: `Thigh` `-32° (rz ±90°)`, `Shin` `-50° (rz ∓20°)`, `Metatarsus` `-60° (rz ∓10°)`, `Foot` `-65°`.
- **Server Preview Lokal:** `python -m http.server 8088 --directory dragon_anim_studio` (aktif di `http://localhost:8088`).

---

## 5. Tugas Tersisa & Langkah Selanjutnya
1. **Sesi Baru 1 (Prioritas Alur Kerja -> Audio):** Membahas penyesuaian roadmap (menunda implementasi animasi sementara) dan merancang penuh arsitektur **Audio, BGM, & SFX** untuk `AudioManager.cs` (Lobby, Jelajah, Krisis 3 Desa, Victory, UI, dan SFX aksi naga).
2. **Sesi Baru 2 (Three.js + Graphify):** Men-setup **Graphify** pada proyek Three.js (`dragon_anim_studio/` / `Fanamir_AnimStudio_Standalone/`), lalu melanjutkan perancangan & evaluasi animasi Three.js berdasarkan hasil generate gambar referensi multi-angle/frame.

---
*Navigasi:* [[00 - Overview]] | [[Tasks]] | [[Devlog]] | [[visual-vfx/evaluasi_dan_revisi_pose_tahap_1]] | [[visual-vfx/studio_animasi_threejs_fanamir]]
