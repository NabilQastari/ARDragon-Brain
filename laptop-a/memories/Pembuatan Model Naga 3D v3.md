---
title: "Pembuatan Model Naga 3D v3"
type: session-memory
tags:
  - session-memory
  - laptop-a
  - blender
  - 3d-model
  - dragon-v3
  - rigging
date: 2026-11-13
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
---

# Pembuatan Model Naga 3D v3

## 1. Tujuan & Konteks Sesi
- Menganalisis perbedaan model naga pada folder `remodel naga v1` dan `remodel naga v2`, lalu membangun ulang versi baru (**remodel naga v3**) dari 0 di `remodel naga v3/Dragon_Model.blend` berdasarkan referensi visual yang disetujui.
- Melakukan iterasi revisi bertahap berdasarkan umpan balik visual pengguna hingga seluruh bagian tubuh, kepala, sayap, ekor, gem dada, spike, dan rigging siap untuk integrasi game.
- Menjaga folder `remodel naga v1` dan `remodel naga v2` tetap utuh tanpa modifikasi.

## 2. Keputusan Desain / Arsitektur / Teknis
- **Desain Kepala Menggunakan Basis v1/v2:** Pengguna lebih memilih proporsi kepala dari model v1/v2 daripada v3 awal. Oleh karena itu, objek `SM_Dragon_Head`, `SM_Dragon_Jaw`, `SM_Dragon_Eyes`, dan `SM_Dragon_Horns` ditransfer dari `remodel naga v1/Dragon_Model.blend` dengan skala `0.685×` agar selaras dengan leher v3.
- **Pembersihan Ornamen & Celah Sayap:** Ornamen *shoulder pads* hijau zaitun dihapus total, dan seluruh celah pada `SM_Dragon_WingMembranes` ditutup agar membran sayap menyatu rapi dengan tulang sayap (`SM_Dragon_WingBones`).
- **Kontinuitas Geometri $C^2$ Body–Ekor (`24` Vertex per Cincin):**
  - `SM_Dragon_Torso` (Rings 9–17) dan `SM_Dragon_Tail` (`y = 0.0850 .. 1.9850`) disatukan menggunakan fungsi profil kontinu `eval_body_tail_profile(y)` dengan jumlah segmen seragam **24 vertex per cincin** (`N_RING = 24`).
  - Titik sambung di `Y = +0.0850` memiliki koordinat dan kemiringan (*slope* `drx/dY`, `dz_top/dY`, `dz_bot/dY`) yang identik, serta *edge smoothing* penutup internal (`v[433]` dan `front_cap`) dipisahkan (`e.smooth = False`) agar tidak menarik *normal* kulit luar.
- **Desain Ujung Ekor (*Caudal Leaf Fin*):** Menghapus *collar ring* pangkal daun ekor dan bercak putih di ekor, lalu membangun *caudal leaf fin* dengan *webbing* sejajar diagonal (`M_Dragon_Tail_Fin`) dan 2 pasang tulang/urat diagonal meruncing (`M_Dragon_Dorsal_DarkTeal`).
- **Desain Gem Dada (*Pear/Teardrop Brilliant-Cut*):**
  - Bentuk diubah menjadi kristal *pear/teardrop* 32 vertex (12 *girdle*, 12 *crown*, 6 *table*, *table center*, *pavilion*) dengan shader multi-tone `M_Dragon_Chest_Crystal`.
  - Rotasi X dikoreksi dari `-18°` menjadi `+21.5°` agar sejajar dengan kemiringan dada, ukuran penampang diperkecil `~32%` (`rx = 0.038, rz_top = 0.056, rz_bot = 0.044`), dan ketebalan *crown/table* dibuat lebih menonjol secara 3D (`y_local = -0.017 .. -0.029`).
- **Orientasi Spike & Pembersihan Geometri Internal:**
  - Ketiga spike kepala (`SM_Dragon_Horns`) diputar mengikuti *surface normal* dahi & mahkota.
  - Spike punggung dan ekor (`SM_Dragon_Spikes_Details`) ditempatkan langsung di permukaan luar melalui BVH raycast agar tidak *clipping*, *enclosed internal islands* dihapus, dan 2 spike teratas di belakang kepala/leher atas (`z_avg = 0.8853` dan `0.9600`) dihapus sesuai permintaan pengguna.

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Model Utama:** `remodel naga v3/Dragon_Model.blend` (Blender `5.2.3 LTS`) berisi **13 mesh** (`SM_Dragon_Head`, `SM_Dragon_Jaw`, `SM_Dragon_Eyes`, `SM_Dragon_Horns`, `SM_Dragon_Neck`, `SM_Dragon_Torso`, `SM_Dragon_Tail`, `SM_Dragon_Spikes_Details`, `SM_Dragon_FrontLegs`, `SM_Dragon_HindLegs`, `SM_Dragon_Claws`, `SM_Dragon_WingBones`, `SM_Dragon_WingMembranes`) dan **1 armature** `ARM_Dragon` (`Root` + 46 deform bones).
- **Perbaikan Material & Stripe:**
  - `SM_Dragon_Head`: *forehead stripe* `M_Dragon_Dorsal_DarkTeal` disimetriskan (`2` kolom `-X` dan `2` kolom `+X`), lubang hidung di-*mirror*, *rear cap* diubah menjadi *quad* vertikal selebar `±0.0465`.
  - `SM_Dragon_Torso` & `SM_Dragon_Tail`: 4 kolom atas (`k = 22, 23, 0, 1`) memakai `M_Dragon_Dorsal_DarkTeal`, 4 kolom bawah (`k = 10, 11, 12, 13`) memakai `M_Dragon_Belly_Cream` dengan pita garis horizontal `M_Dragon_Belly_Line` penuh 4 kolom tiap 3 strip (`i % 3 == 0`, `i <= 18`).
  - `SM_Dragon_HindLegs`: pangkal paha dalam dirapikan ke dalam panggul (`x = ±0.085`, `z <= 0.595`, `ring0` di `|x| <= 0.112`) dan bercak krem asimetris di paha dalam dibersihkan.
- **Render Preview di `remodel naga v3/`:**
  - Standar: `Dragon_Preview_Front.png`, `Dragon_Preview_Side.png`, `Dragon_Preview_Top.png`, `Dragon_Preview_Back.png`, `Dragon_Preview_Perspective.png`.
  - Close-up verifikasi revisi: `Dragon_Preview_Tail_Fin_Top.png`, `Dragon_Preview_Tail_3Quarter.png`, `Dragon_Preview_Chest_Gem_Closeup.png`, `Dragon_Preview_Gem_Side_Fix.png`, `Dragon_Preview_Body_Tail_Side_Fix.png`, `Dragon_Preview_Body_Tail_Top_Fix.png`, `Dragon_Preview_Body_Tail_Belly_Fix.png`, `Dragon_Preview_Head_Spikes_Fix.png`, `Dragon_Preview_Neck_Body_Spikes_Fix.png`.
- **Hasil Verifikasi (`qa_check.py` & `rig_check.py`):**
  - `qa_check.py` (setelah revisi spike & gem): `{"ok": true, "pass": true, "errors": [], "zfighting": [], "floating": [], "enclosed": []}` dengan 5 *warning* minor (unused material slots pada `SM_Dragon_Tail`, `SM_Dragon_Spikes_Details`, `SM_Dragon_HindLegs`, serta `back_to_back` pada `SM_Dragon_WingMembranes` 128 pairs dan `SM_Dragon_Head<->SM_Dragon_Jaw` 78 pairs).
  - `rig_check.py` (dijalankan dengan `-- --armature ARM_Dragon` sebelum revisi terakhir): sempat gagal dengan `{"ok": false, "error": "TypeError: bpy_prop_collection[slice]: slice steps not supported"}` pada skrip pemeriksa.

## 4. Detail Penting yang Harus Diingat
- **Koordinat & Parameter Penting di `Dragon_Model.blend`:**
  - Sambungan Torso–Tail berada di `Y = +0.0850` (`N_RING = 24`), dengan `SM_Dragon_Torso` Rings 9–17 menempati `Y = -0.2410 .. +0.0850` dan `SM_Dragon_Tail` menempati `Y = +0.0850 .. +1.9850` (*caudal fin* mulai `Y = 1.4500`).
  - Dada untuk penempatan gem berada pada rentang midline `(-0.5145, 0.5278)` hingga `(-0.5734, 0.6831)`, dengan pusat gem di `(0.0, -0.5485, 0.6080)` dan rotasi `+21.5°` pada sumbu X.
  - Tiga spike kepala (`SM_Dragon_Horns`) berada di `y = -0.6945, -0.6260, -0.5520` dengan kemiringan normal `+48.8°`, `+10.0°`, dan `-32.0°`.
- **Catatan Penghapusan Terakhir:** Setelah *run* `qa_check.py` terakhir, 2 spike leher atas/belakang kepala (`z_avg = 0.8853` dan `0.9600`) dihapus dari `SM_Dragon_Spikes_Details` beserta pembaruan *skin weights* dan render ulang `Dragon_Preview_Neck_Body_Spikes_Fix.png`, `Dragon_Preview_Side.png`, `Dragon_Preview_Back.png`, dan `Dragon_Preview_Perspective.png`.
- **Aturan Kamera:** Kamera review `CAM_Review_*` harus tetap `hide_viewport = True` setelah rendering.

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] Jalankan ulang `qa_check.py` dan perbaiki/jalankan `rig_check.py` pada kondisi final setelah penghapusan 2 spike leher atas, lalu bersihkan 3 unused material slots jika diperlukan sebelum ekspor FBX/GLB.
- [ ] Ekspor `remodel naga v3/Dragon_Model.blend` ke format target Unity (`FBX`) saat integrasi di Laptop B, dan pastikan deformasi pose *rig* `ARM_Dragon` berjalan mulus.
- [ ] Lanjutkan fokus aktif Laptop A di [[Tasks]] (review final [[rencana_desain_ui]] & GDD Kepulauan 3 Biome $1200 \times 1200\text{ m}$).

**Navigasi Obsidian:** [[00 - Overview]] · [[konteks]] · [[prd]] · [[rencana_desain_ui]] · [[Tasks]] · [[Devlog]]
