---
title: "Setup Struktur Output Remodel Naga v2 dan Verifikasi Aset Blender MCP"
type: session-memory
tags:
  - session-memory
  - laptop-a
  - blender-mcp
  - dragon-model
  - asset-pipeline
date: 2026-11-10
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
---

# Setup Struktur Output Remodel Naga v2 dan Verifikasi Aset Blender MCP

> **Catatan Konteks Waktu:** Sesi eksekusi dilakukan pada **2026-11-10** (sebelum pembersihan `prompt_naga.md` dan penyelesaian remodel naga v3 pada **2026-11-13**).

## 1. Tujuan & Konteks Sesi
- Menyiapkan direktori output baru `remodel naga v2` di root project (`E:\Semester 5\Game\AR Dragon`) sesuai standar **6.4 Asset Output Directory Management**.
- Memastikan seluruh file `.blend`, render `.png` 5 sudut pandang, pratinjau tambahan, dan aset/dokumen tekstur tersimpan rapi di dalam `remodel naga v2` dengan path relatif tanpa meninggalkan file tercecer di folder lain (`Desktop`, `Downloads`, `%TEMP%`, atau direktori default Blender).
- Memverifikasi bahwa `Dragon_Model.blend` dapat dibuka ulang secara independen tanpa tekstur eksternal yang hilang.

---

## 2. Keputusan Desain / Arsitektur / Teknis
- **Basis Model Awal:** Karena scene Blender MCP aktif dalam keadaan kosong (`0` objek), disepakati membuka model terbaru yang tersedia saat itu, yaitu `remodel naga v1/Dragon_Model.blend`, lalu langsung menyimpannya ke `remodel naga v2/Dragon_Model.blend` dengan `relative_remap=True` tanpa mengubah atau menimpa isi `remodel naga v1`.
- **Preservasi Geometri & Material:** Tidak ada perubahan geometri maupun material pada tahap setup folder ini; seluruh `13` mesh (`SM_Dragon_*`) dan `13` material prosedural (`M_Dragon_*`) dipertahankan identik dengan baseline v1.
- **Kebijakan Tekstur Prosedural:** Seluruh material naga bersifat prosedural (`Principled BSDF`, dengan node `Voronoi` bump pada `M_Dragon_Body_Teal` dan `M_Dragon_Dorsal_DarkTeal`) dan tidak memakai tekstur gambar eksternal sesuai aturan `prompt_naga.md`. Folder `textures/` tetap dibuat dan diisi `README.txt` sebagai dokumentasi aturan path relatif (`//textures/...`) bila di kemudian hari ada tekstur hasil *bake*.
- **Penambahan Kamera Review:** Ditambahkan `5` kamera ortografik/perspektif (`CAM_Review_Front`, `CAM_Review_Side`, `CAM_Review_Back`, `CAM_Review_Top`, `CAM_Review_Perspective`) ke koleksi `COL_Dragon_Lighting_Cam` tanpa mengganggu `CAM_Dragon_Hero`, serta memindahkan `scene.render.filepath` dari path v1 ke path relatif `//renders/`.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Struktur Direktori & File yang Dibuat di `E:\Semester 5\Game\AR Dragon\remodel naga v2\`:**
  - `Dragon_Model.blend` (`447,633 B`) & `Dragon_Model.blend1` (`447,928 B`)
  - `renders/` (`1600x1200` PNG, engine `BLENDER_EEVEE`, `Khronos PBR Neutral`):
    - `dragon_front.png` (`1,899,421 B`)
    - `dragon_side.png` (`1,866,995 B`)
    - `dragon_back.png` (`1,882,151 B`)
    - `dragon_top.png` (`1,925,957 B`)
    - `dragon_perspective.png` (`1,932,045 B`)
  - `previews/` (`BLENDER_WORKBENCH` & contact sheet):
    - `dragon_contact_sheet.png` (`735,941 B`) — gabungan 5 sudut render + 1 pratinjau clay
    - `dragon_preview_clay_perspective.png` (`1,231,009 B`)
    - `dragon_preview_clay_side.png` (`1,218,894 B`)
    - `dragon_preview_matcap_front.png` (`1,239,499 B`)
  - `textures/README.txt` (`519 B`)
- **Perbaikan Framing Kamera:** Pada pemeriksaan visual render awal, kamera `side` dan `back` sempat miring/terbalik; orientasi diperbaiki menggunakan `to_track_quat('-Z', 'Y')` lalu dirender ulang dan diverifikasi lewat `dragon_contact_sheet.png`.
- **Verifikasi Headless Independen (`D:\blender\blender.exe -b ... --factory-startup --python-exit-code 1`):**
  - `Dragon_Model.blend` berhasil dibuka ulang (`exit=0`) dengan `22` objek, `13` mesh, `13` material, `19,628` segitiga, dan `6` kamera (`CAM_Dragon_Hero` + `5` `CAM_Review_*`).
  - `RENDER_OUT`: `//renders\` $\rightarrow$ `E:\Semester 5\Game\AR Dragon\remodel naga v2\renders\`.
  - `EXTERNAL_PATHS_ABSOLUTE`: `[]` dan `MISSING_FILES`: `[]`.
  - Pemeriksaan file tercecer memastikan tidak ada file baru di luar `remodel naga v2` (termasuk root project, `Desktop`, `Downloads`, dan `%TEMP%`).

---

## 4. Detail Penting yang Harus Diingat
- **Spesifikasi Scene `Dragon_Model.blend` (Baseline v2):**
  - Versi Blender: `5.2.3 LTS` (`D:\blender\blender.exe`).
  - Bounding box mesh: `[-2.485, -1.586, 0.0]` hingga `[2.485, 2.511, 1.848]`.
  - Total segitiga saat ini: **`19,628` tris** (melebihi batas `<= 8,000` tris pada aturan `prompt_naga.md` saat itu sehingga dicatat untuk ditangani saat remodel).
  - Daftar mesh (`13`): `SM_Dragon_Claws`, `SM_Dragon_Eyes`, `SM_Dragon_FrontLegs`, `SM_Dragon_Head`, `SM_Dragon_HindLegs`, `SM_Dragon_Horns`, `SM_Dragon_Jaw`, `SM_Dragon_Neck`, `SM_Dragon_Spikes_Details`, `SM_Dragon_Tail`, `SM_Dragon_Torso`, `SM_Dragon_WingBones`, `SM_Dragon_WingMembranes`.
  - Daftar material (`13`): `M_Dragon_Belly_Cream`, `M_Dragon_Belly_Groove`, `M_Dragon_Body_Teal`, `M_Dragon_Chest_Crystal`, `M_Dragon_Dorsal_DarkTeal`, `M_Dragon_Eye_Highlight`, `M_Dragon_Eye_Iris`, `M_Dragon_Eye_Pupil`, `M_Dragon_Fin_MidTeal`, `M_Dragon_Horn_Claw`, `M_Dragon_Marking_Aqua`, `M_Dragon_Shoulder_Moss`, `M_Dragon_Wing_Membrane`.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] **Laptop A / Pipeline Model Naga:** Langkah remodel `prompt_naga.md` (Langkah 0 dan Task 1–5) serta reduksi anggaran segitiga dari `19,628` tris ke target game-ready belum dijalankan pada folder `remodel naga v2` di akhir sesi ini *(catatan: per 2026-11-13, remodel naga v3 pada `Dragon_Model.blend` telah dinyatakan selesai di `Devlog.md`)*.

---
**Navigasi Obsidian:** [[00 - Overview]] · [[konteks]] · [[prd]] · [[rencana_desain_ui]] · [[Tasks]] · [[Devlog]]
