---
title: "Iterasi Naga v2 dan Eksekusi Generator"
type: session-memory
tags:
  - session-memory
  - laptop-a
  - blender
  - 3d-modeling
  - dragon-model
  - hermes-prompt
date: 2026-11-13
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
---

# Iterasi Naga v2 dan Eksekusi Generator

## 1. Tujuan & Konteks Sesi
Sesi ini berfokus pada iterasi lanjutan model 3D **Ancient Dew Water Dragon** pada file `assets/Naga V2 Tes 2.blend` dan pembandingan dengan skrip generator prosedural `generate_dragon.py` di Blender:
- Mengembalikan model ke kondisi **versi 2** (`Naga V2 Tes 2.blend`) sebagai baseline kerja.
- Menganalisis gambar referensi dan memperbaiki mesh bagian kaki (`kaki` & `kaki2`), sementara tungkai (`Tangan*`, `Kaki*`) yang sudah disetujui tetap dipertahankan.
- Menyusun prompt arahan kerja untuk **Hermes** dengan pembagian model multi-provider (analisis gambar referensi dan pengerjaan 3D di Blender MCP).
- Melakukan eksperimen remodel *body* dan sayap bergaya *stylized* (mengacu pada `dragon_gen.py` dan gambar referensi sayap), lalu melakukan *revert* penuh ketika hasilnya dinilai belum sesuai referensi.
- Menyusun prompt serta mengeksekusi `generate_dragon.py` di Blender untuk meninjau hasil generasi naga low-poly secara utuh.

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Kebijakan Rollback Berbasis Backup:** Setiap eksperimen berisiko pada `Naga V2 Tes 2.blend` didahului pembuatan file backup eksplisit (`Naga V2 Tes 2_backup_before_body.blend` dan `Naga V2 Tes 2_backup_before_wings.blend`) agar model dapat dikembalikan ke kondisi bersih (`32` objek) tanpa merusak bagian tungkai (`Tangan*`, `Kaki*`) yang sudah disetujui.
2. **Pembagian Peran Model pada Prompt Hermes:**
   - **Provider `antigravity` — Model `gemini flash`:** Ditugaskan untuk membaca dan menganalisis gambar referensi 2D secara visual.
   - **Model `opus 4.6`:** Ditugaskan untuk eksekusi pemodelan 3D di Blender setelah memverifikasi lokasi folder, membaca section *body*, dan mengecek koneksi Blender MCP.
3. **Pemisahan File Hasil `generate_dragon.py`:** Hasil eksekusi `generate_dragon.py` di Blender disimpan terpisah ke `assets/AncientDewWaterDragon_Generated.blend`, sedangkan scene kerja utama dikembalikan ke `assets/Naga V2 Tes 2.blend`.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
1. **Perbaikan Kaki & Eksperimen Body (`Naga V2 Tes 2.blend`):**
   - Melakukan analisis referensi dan perbaikan mesh `kaki` & `kaki2`.
   - Membuat backup `assets/Naga V2 Tes 2_backup_before_body.blend` sebelum percobaan remodel *body*.
   - Setelah percobaan remodel *body* dinilai belum mirip referensi, perubahan dibatalkan (*reverse change*) dari `Naga V2 Tes 2_backup_before_body.blend`, dimuat ulang di Blender (`32` objek), disimpan ke `Naga V2 Tes 2.blend`, dan diverifikasi melalui render `reverted_full_front.png` serta `reverted_full_34.png`.
2. **Eksperimen Sayap & Revert (`Naga V2 Tes 2.blend`):**
   - Membuat backup `assets/Naga V2 Tes 2_backup_before_wings.blend`.
   - Mengambil referensi struktur dari `dragon_gen.py` (`WING`, `path_tube`, `prism`, `wing_spec`, `COLORS`) serta gambar `Referensi Naga V2 (Wing).jpg` dan `Referensi Nagar V2.jpeg`.
   - Membuat prototipe skrip sayap di folder scratch (`scratch/build_stylized_wings.py` dan `scratch/test_stylized_wings.py`).
   - Sesuai permintaan *revert change*, scene dikembalikan ke `Naga V2 Tes 2_backup_before_wings.blend` dan diverifikasi melalui `reverted_wings_front.png` serta `reverted_wings_34.png`.
3. **Perbaikan & Eksekusi `generate_dragon.py`:**
   - Memperbaiki `SyntaxError` (raw newlines di dalam string literal `mf.write`/`of.write` pada fungsi `export_standalone_obj()` di baris 414–429 dan 535–553) menggunakan `scratch/fix_generate_dragon.py`.
   - Memverifikasi sintaks dengan `python -m py_compile` (exit code `0`) dan menguji ekspor standalone OBJ/MTL (`ancient_dew_water_dragon.obj` dan `.mtl`).
   - Mengeksekusi `build_in_blender()` di Blender hingga terbentuk koleksi `AncientDewWaterDragon` berisi **51 objek** beserta `Dragon_Armature` dan **6 pose animasi** (`GLIDE`, `TURN`, `WING_FLAP_UP`, `WING_FLAP_DOWN`, `SPRAY_WATER`, `DEW_RAIN`).
   - Menyimpan hasil ke `assets/AncientDewWaterDragon_Generated.blend`, merender preview `generated_dragon_34.png`, lalu memuat ulang `assets/Naga V2 Tes 2.blend`.

---

## 4. Detail Penting yang Harus Diingat
- **Struktur Rig `Naga V2 Tes 2.blend`:** Menggunakan *direct Bone Parenting* (`parent_type = 'BONE'`) ke `Armature` dengan **36 bone**. Penamaan bone sayap asimetris secara label (`Sayap L` mengarah ke `Sayap2 R`, `Sayap L.001` mengarah ke `Sayap2 L`), pusat simetri naga berada di sekitar `X ≈ -1.8485`, dan naga menghadap ke arah `-Y`.
- **Bagian yang Sudah Disetujui:** Mesh `Tangan*` dan `Kaki*` pada `Naga V2 Tes 2.blend` berstatus *frozen/approved* dan harus dijaga agar tidak ikut berubah saat mengedit *body* atau sayap.
- **Referensi Warna & Struktur Sayap (`dragon_gen.py` & `Referensi Nagar V2.jpeg`):** Sayap menggunakan struktur **3-strut** dengan warna membran cyan `#8FD9E6` dan tulang sayap `#1F6F8C`.
- **Artefak Backup & Hasil:**
  - `assets/Naga V2 Tes 2.blend` (kondisi bersih setelah revert)
  - `assets/Naga V2 Tes 2_backup_before_body.blend`
  - `assets/Naga V2 Tes 2_backup_before_wings.blend`
  - `assets/AncientDewWaterDragon_Generated.blend`

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] Menentukan arah akhir desain *body* dan sayap `Naga V2 Tes 2.blend` (apakah melanjutkan remodel bertahap menggunakan prompt Hermes + referensi 2D, atau mengadopsi komponen dari `AncientDewWaterDragon_Generated.blend`).
- [ ] Jika memakai referensi dari `generate_dragon.py`, menyelaraskan skala, orientasi (`-Y`), dan *bone parenting* terhadap `Armature` 36 bone di `Naga V2 Tes 2.blend`.
- [ ] Melanjutkan sinkronisasi hasil akhir model naga ke pipeline Unity 6 (`Laptop B`) setelah desain *body* dan sayap disetujui.

---

**Navigasi Obsidian:** [[00 - Overview]] · [[konteks]] · [[prd]] · [[rencana_desain_ui]] · [[Tasks]] · [[Devlog]]
