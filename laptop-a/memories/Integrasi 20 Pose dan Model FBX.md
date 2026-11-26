---
title: Integrasi 20 Pose dan Model FBX
type: session-memory
tags:
  - session-memory
  - laptop-a
  - animation
  - 3d-model
  - threejs
  - fbx
  - blender
  - unity
  - handoff
date: 2026-11-26
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[handoff-laptop-b/prompt_eksekusi_model_dan_animasi_20_pose_laptop_b]]"
  - "[[visual-vfx/studio_animasi_threejs_fanamir]]"
  - "[[visual-vfx/planning_perbaikan_animasi_dan_pose]]"
  - "[[visual-vfx/spesifikasi_dynamic_wings_dan_head_tracking]]"
---

# 🐉 Integrasi 20 Pose dan Model FBX

## 1. Tujuan & Konteks Sesi
- **Penyempurnaan Gerak & Pose Fanamir di Three.js Studio:**
  - Merespons permintaan pengguna untuk menyempurnakan kepakan sayap `#14 Propeller Dash` (rotasi/twist sayap menyerupai renang gaya kupu-kupu / *butterfly stroke*, serta kepala dan leher lebih maju ke depan).
  - Merevisi pose desa `#15A Zephyria` (kepala sedikit menunduk `-14°`, kaki depan sedikit melipat `36°/44°/-58°`).
  - Merevisi pose desa `#15C Solaria` (kaki depan melipat simetris ke dalam `-26°/84°/-22°` menyentuh/mengapit kristal permata dada).
  - Menambahkan pose baru `#15B-1 Pyrewood · Water Siphon (Drink)` (postur *Low Descent* `-10°`, leher dan kepala menunduk ke bawah seperti menyeruput air danau `-56°/-24°/+38°` + *sipJaw*, sayap berkepak aktif `1.3 Hz`).
- **Perbaikan Tekstur & Mesh Kepala Naga (`texfix`):**
  - Mengoreksi garis dorsal (*dorsal stripe*) pada atas kepala `SM_Dragon_Head` di `assets/Dragon_Model.blend` yang sebelumnya jagged/asimetris menjadi mulus simetris sempurna (`0` poligon dorsal asimetris).
- **Handoff Model 3D & 20 Pose ke Laptop B:**
  - Mengekspor ulang model FBX bersih `Dragon_Model.fbx` (47 tulang lengkap termasuk `Root`, 13 `SkinnedMeshRenderer`, scale 1.0, forward `-Z`, up `Y`).
  - Menyusun helper matematis konversi sudut Three.js ke Unity `FromStudioEulerXYZ(x, y, z)` (`0.00e+00` error).
  - Memaketkan seluruh artefak ke dalam `assets/Export_DragonModel_Anim20_LaptopB.zip` (1.82 MB) dan menyusun Master Prompt siap salin untuk Laptop B.
  - Memberikan rekomendasi model AI bagi Laptop B (`Hydrogen High`).

---

## 2. Keputusan Desain / Arsitektur / Teknis

1. **Kinematika & Artikulasi Pose Three.js Studio (`v14` & `v15`):**
   - **Propeller Dash (`#14`):**
     - Leher dan kepala dimajukan (`neckPitch = -12°`, `headPitch = +8°`, `neckForwardOffset = +0.18m`).
     - Kepakan sayap diperkaya rotasi roll & yaw asimetris (`wingRollOffset`, `wingYawOffset`) dengan kurva dorongan kubik yang meniru gaya renang kupu-kupu (*butterfly stroke*), di mana sayap meraup udara ke depan-luar lalu mendorong kuat ke belakang-bawah.
   - **Desa Zephyria (`#15A`):**
     - Sikap kepala menunduk khidmat (`headPitch = -14°`, `neck01Pitch = -8°`).
     - Kaki depan sedikit melipat santai ke belakang tubuh (`ForeLeg_L/R` pitch `36°`, Shin `44°`, Metatarsus `-58°`).
   - **Desa Solaria (`#15C`):**
     - Kedua kaki depan menekuk simetris ke atas-dalam mendekati dada (`ForeLeg_L/R` pitch `-26°`, roll `84°`, yaw `-22°`) sehingga telapak/cakar naga mengapit kristal permata dada (*Chest Gem*).
   - **Pose Baru Desa Pyrewood (`#15B-1 Water Siphon / Drink`):**
     - Dirancang khusus untuk fase menyerap energi air di danau sebelum memadamkan api hutan.
     - Postur badan menunduk rendah (*Low Descent* body pitch `-10°`, kaki belakang terentang stabil, kaki depan sedikit menekuk).
     - Leher & kepala melengkung curam ke bawah (`neck01Pitch = -24°`, `neck02Pitch = -32°`, `headPitch = +38°` relatif terhadap leher) membentuk siluet kepala mencelup ke air, dengan artikulasi rahang bawah membuka-tutup halus (*sipJaw* `0°` s.d. `-15°`).
     - Sayap tetap berkepak aktif (`1.3 Hz`) menjaga daya apung naga di atas permukaan danau.

2. **Perbaikan Poligon & Material Dorsal Stripe Kepala (`SM_Dragon_Head`):**
   - Dari inspeksi screenshot tangkapan layar pengguna (`media_1795585896552_3671656e.png`), ditemukan patahan asimetris pada pita dorsal naga di ubun-ubun kepala.
   - Audit poligon mesh di Blender 5.2.3 mengungkap `polygons[80]` dan `polygons[83]` tidak sengaja ter-assign ke material badan `0` (`M_Dragon_Body_Teal`), sementara pasangan simetrisnya di sisi kanan menggunakan material dorsal `1` (`M_Dragon_Dorsal_DarkTeal`). Serta `polygons[16]` di sisi kiri ter-assign ke dorsal padahal pasangannya badan.
   - Dilakukan reklasifikasi material via Python bpy:
     ```python
     poly_80.material_index = 1  # Dark Teal
     poly_83.material_index = 1  # Dark Teal
     poly_16.material_index = 0  # Teal Body
     ```
   - Hasil audit: `0` poligon dorsal asimetris di seluruh Head, Neck, Torso, dan Tail. Garis dorsal kini lurus, simetris, dan menyatu mulus ke leher.

3. **Formulasi Matematis Konversi Rotasi Three.js ke Unity C#:**
   - Three.js menggunakan sistem koordinat *Right-Handed* dengan urutan Euler *Intrinsic* `XYZ` (+X = kiri naga, +Y = atas, +Z = belakang).
   - Unity menggunakan sistem koordinat *Left-Handed* dengan urutan bawaan `Quaternion.Euler` bernilai `ZXY` (+X = kanan naga, +Y = atas, +Z = depan).
   - Agar seluruh 20 pose dari Three.js Studio dapat diterapkan dengan presisi absolut ke tulang rig Unity tanpa deviasi rotasi sekecil apa pun, diformulasikan helper konversi matriks:
     ```csharp
     public static Quaternion FromStudioEulerXYZ(float xDeg, float yDeg, float zDeg)
     {
         return Quaternion.AngleAxis(xDeg, Vector3.right)
              * Quaternion.AngleAxis(-yDeg, Vector3.up)
              * Quaternion.AngleAxis(-zDeg, Vector3.forward);
     }
     ```
   - Formula ini terbukti menghasilkan deviasi error matematis `0.00e+00` pada seluruh 47 sendi rig naga.

4. **Rekomendasi Model AI untuk Eksekusi Laptop B:**
   - Rekomendasi: **`Hydrogen High`** (lebih diunggulkan dibanding *Orchestrator Neon High* atau *xHigh*).
   - Alasan arsitektural:
     1. Seluruh kode rotasi 20 pose, helper konversi, dan script copy batch sudah 100% matang dan tercakup dalam paket handoff tanpa perlu perancangan ulang.
     2. Unity Editor berjalan dalam satu thread utama (satu AppDomain & satu Play Mode session), sehingga eksekusi sequential deterministik oleh model tunggal yang sangat teliti (*Hydrogen High*) jauh lebih stabil, bebas tabrakan skrip, dan hemat token dibanding multi-agent orchestrator.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset

1. **Aplikasi Three.js WebGL Studio (`dragon_anim_studio/`):**
   - [dragon_anim_studio/index.html](file:///e:/Semester%205/Game/AR%20Dragon/dragon_anim_studio/index.html):
     - Memperbarui opsi pose selector menjadi 20 pose resmi (`#01`–`#15C` + `#15B-1`).
     - Memperbarui cache buster `studio.js?v=20261125_v15` dan `dragon_model.glb?v=20261125_texfix`.
   - [dragon_anim_studio/studio.js](file:///e:/Semester%205/Game/AR%20Dragon/dragon_anim_studio/studio.js):
     - Menambahkan konstanta `POSE_PYREWOOD_WATER_SIPHON = 'pyrewood_water_siphon'`.
     - Mengimplementasikan kinematika `#14 Propeller Dash`, `#15A Zephyria`, `#15C Solaria`, dan `#15B-1 Water Siphon`.
     - Memperbarui `generateUnityCSharpSnippet()` untuk mengekspor seluruh 20 pose ke blok C# Unity.
     - Teruji via `node --check studio.js` (`0` error) dan verifikasi visual browser subagent (`final_step_366.png`, `final_step_412.png`).
   - [dragon_anim_studio/dragon_model.glb](file:///e:/Semester%205/Game/AR%20Dragon/dragon_anim_studio/dragon_model.glb):
     - Diekspor ulang dengan perbaikan dorsal stripe kepala.

2. **Model Blender & Aset Unity FBX:**
   - [assets/Dragon_Model.blend](file:///e:/Semester%205/Game/AR%20Dragon/assets/Dragon_Model.blend):
     - Memperbaiki poligon material dorsal `SM_Dragon_Head` dan menyimpan file `.blend`.
   - [Assets/Assets 3D/Dragon_Model.fbx](file:///e:/Semester%205/Game/AR%20Dragon/Assets/Assets%203D/Dragon_Model.fbx):
     - Mengekspor model FBX bersih (`2.41 MB`, 47 tulang termasuk `Root`, 13 SkinnedMesh, scale 1.0, forward `-Z`, up `Y`).

3. **Paket Standalone & Handoff Laptop B:**
   - [Fanamir_AnimStudio_Standalone.zip](file:///e:/Semester%205/Game/AR%20Dragon/Fanamir_AnimStudio_Standalone.zip):
     - Dikemas ulang dengan aset studio `v15` terbaru (`13.89 MB`).
   - `assets/Export_DragonModel_Anim20_LaptopB/`:
     - Berisi `Dragon_Model.fbx`, `UNITY_20_POSES_REFERENCE.cs.txt` (105 KB, referensi lengkap C# 20 pose), `copy_dragon_to_unity.bat`, `README_LAPTOP_B.md`, dan folder referensi studio Three.js.
   - [assets/Export_DragonModel_Anim20_LaptopB.zip](file:///e:/Semester%205/Game/AR%20Dragon/assets/Export_DragonModel_Anim20_LaptopB.zip):
     - Arsip ZIP siap kirim ke Laptop B (`1.82 MB`).
   - [brain/laptop-a/handoff-laptop-b/prompt_eksekusi_model_dan_animasi_20_pose_laptop_b.md](file:///e:/Semester%205/Game/AR%20Dragon/brain/laptop-a/handoff-laptop-b/prompt_eksekusi_model_dan_animasi_20_pose_laptop_b.md):
     - Master prompt eksekusi lengkap 5 langkah untuk Laptop B.

---

## 4. Detail Penting yang Harus Diingat

1. **Daftar 20 Pose Resmi Fanamir:**
   - Kelompok 1 (Grounded): `#01 Rest / T-Pose`, `#02 Trotting Gait (Ground Walk)`.
   - Kelompok 2 (Core Flight): `#03 Neutral Gliding`, `#04 Steep Climb`, `#05 Shallow Climb`, `#06 Level Cruising`, `#07 Gentle Descend (Y-Glide)`, `#08 Steep Dive (Tuck Dive)`.
   - Kelompok 3 (Special Maneuvers & Hover): `#09A Buoyant Hover`, `#09B Flapping Wings (High Lift)`, `#10 Low-A Descent`, `#11 Banked Turn Left`, `#12 Banked Turn Right`, `#13 Barrel Roll`, `#14 Propeller Dash (Speed Boost)`.
   - Kelompok 4 (Village Actions): `#15A Zephyria · Fog Dispersion`, `#15B Pyrewood · Water Breath Flame Extinguish`, `#15B-1 Pyrewood · Water Siphon (Drink)`, `#15C Solaria · Dew Rain Prayer`, `#16 Victory Roar`.

2. **Rotasi Inti Pose Revisi Sesi Ini:**
   - **Zephyria (`#15A`):** `Head` pitch `-14°`, `ForeLeg_L/R` pitch `36°`, Shin `44°`, Metatarsus `-58°`.
   - **Solaria (`#15C`):** `ForeLeg_L/R` pitch `-26°`, roll `84°`, yaw `-22°` (tangan mendekap permata dada).
   - **Water Siphon (`#15B-1`):** `Root` pitch `-10°`, `Neck_01` pitch `-24°`, `Neck_02` pitch `-32°`, `Head` pitch `+38°`, rahang bawah `sipJaw` `0°` s.d. `-15°`, sayap berkepak frekuensi `1.3 Hz`.

3. **Aturan Rig FBX Unity:**
   - Tulang `Root` **wajib ada** di hierarki `ARM_Dragon` di Unity Inspector.
   - Paha belakang (`Thigh_Hind_L/R`) memiliki koreksi rotasi intrinsik `rz = ±90°` pada rig Blender agar lutut menekuk ke depan secara anatomis.

---

## 5. Tugas Tersisa & Langkah Selanjutnya

1. **Eksekusi di Laptop B:**
   - Mengirimkan / mengekstrak `Export_DragonModel_Anim20_LaptopB.zip` ke workspace Laptop B.
   - Menjalankan master prompt [prompt_eksekusi_model_dan_animasi_20_pose_laptop_b.md](file:///e:/Semester%205/Game/AR%20Dragon/brain/laptop-a/handoff-laptop-b/prompt_eksekusi_model_dan_animasi_20_pose_laptop_b.md) dengan model AI **`Hydrogen High`**.
   - Memperbarui `DragonBodyAnimator.cs` di Unity dengan helper `FromStudioEulerXYZ` dan 20 pose terverifikasi.
2. **Pilar Pasca-Animasi (Laptop A):**
   - Merumuskan spesifikasi detail Pilar 2 (Animasi UI elastis, ghost progress bar, unroll textbox) dan Pilar 3 (Penyempurnaan kendali instan downstroke sayap & mitigasi kelelahan fisik) di `planning_post_animasi_vfx_ui_dan_kontrol.md`.
3. **Penyelesaian Bab Pengujian Laporan Capstone:**
   - Melengkapi Subbab 4.6 (Pengujian Fungsional Game Loop), 4.7 (Kinerja & Performa), 4.8 (Evaluasi Kebugaran Fisik), dan Bab 5 (Kesimpulan & Saran).

---

[[00 - Overview]] | [[laptop-a/Tasks|Tasks]] | [[laptop-a/Devlog|Devlog]] | [[laptop-a/handoff-laptop-b/prompt_eksekusi_model_dan_animasi_20_pose_laptop_b|Prompt Eksekusi Model & 20 Pose Laptop B]]
