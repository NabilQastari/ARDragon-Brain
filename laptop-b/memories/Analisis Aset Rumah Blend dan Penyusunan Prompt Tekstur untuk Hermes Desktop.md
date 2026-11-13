---
title: "Analisis Aset rumah.blend dan Penyusunan Prompt Tekstur untuk Hermes Desktop"
type: session-memory
tags:
  - session-memory
  - laptop-b
  - assets-3d
  - blender
  - texturing
  - rumah-blend
date: 2026-11-13
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
---

# Analisis Aset `rumah.blend` dan Penyusunan Prompt Tekstur untuk Hermes Desktop

> **Catatan Konteks Waktu:** Sesi analisis aset dan penyusunan prompt berlangsung pada `2026-11-02`, kemudian dirangkum ke dalam memori Obsidian Brain Laptop B pada `2026-11-13`.

## 1. Tujuan & Konteks Sesi
- **Target:** Menganalisis seluruh isi workspace proyek *AR Dragon Sky Guardian* (khususnya folder aset 3D Blender) dan menyusun prompt texturing yang akurat untuk `Assets AR Dragon Sky Guardian/rumah.blend` (PIC: Fauzih, progres awal di `TS & List Asset.xlsx`: 60%).
- **Kebutuhan Lanjutan User:**
  1. Awalnya meminta prompt tekstur untuk `rumah.blend` secara umum berdasarkan konteks proyek.
  2. Kemudian meminta tekstur disesuaikan agar serasi dengan aset utama `Nagcu.blend`.
  3. Terakhir mengarahkan pembuatan prompt siap pakai untuk **Hermes Desktop** dengan path `D:\Fauzih\Kuliah\SEMESTER V\Proyek Multimedia dan Game\AR Dragon\Assets AR Dragon Sky Guardian`, dengan syarat **warna rumah tetap alami seperti rumah sungguhan** (bukan warna biru/emas naga), namun **gaya/tema teksturingnya mengikuti `Nagcu.blend`**.

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Prinsip 1 Model Dasar untuk 3 Desa (Anti-Bug):**
   - Sesuai `konteks.md` dan `TS & List Asset.xlsx`, `rumah.blend` berfungsi sebagai satu model dasar bangunan desa yang dipakai ulang pada 3 zona desa (`Village_1_Pollution`, `Village_2_Wildfire`, `Village_3_Drought`). Perubahan visual tercemar vs pulih dikendalikan lewat shader/material dan *Particle System* C# di Unity.
2. **Pemisahan Warna vs Gaya Teksturing (`rumah.blend` vs `Nagcu.blend`):**
   - **Gaya/Tema Teksturing (mengikuti `Nagcu.blend`):** *Stylized low-poly cartoon RPG*, permukaan *matte* bersih (`Roughness` tinggi `0.7–1.0`, `Metallic = 0.0` kecuali aksen logam gagang pintu), warna solid/hand-painted bersih tanpa *noise* atau kotoran fotorealistis.
   - **Skema Warna Rumah (alami seperti rumah sungguhan, tidak mengikuti warna biru/emas naga):**
     - **Atap (`roof` / `Cube.003`):** Genteng tanah liat terakota klasik (`#B84A39`).
     - **Dinding Utama (`Cube` / `Cube.002`):** Plester dinding krem/putih hangat (`#E8DFCE`).
     - **Pintu & Kusen Jendela (`pintu`, `jendela`):** Kayu cokelat natural/oak (`#5C381E`).
     - **Cerobong Asap (`Cube.001` / `Cube.004`):** Batu bata/batu alam abu-abu (`#54575E`).
     - **Gagang Pintu (`Cylinder` / `Cylinder.001`):** Kuningan/besi tempa antik (`#8C734B`, `Roughness 0.4`, `Metallic 0.6`).
     - **Kaca Jendela:** Kaca biru langit lembut (`#8CB5C9`, `Roughness 0.15`).
3. **Target Eksekusi via Hermes Desktop:**
   - Prompt diformulasikan agar Hermes Desktop dapat menjalankan skrip Python Blender (*headless* via `blender.exe`), membuat backup `rumah_backup.blend`, memasang material *Principled BSDF* ke koleksi `Rumah 1 jendela` dan `Rumah 2 jendela`, atau menghasilkan UV Texture Atlas `2048x2048` PNG untuk Unity URP.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
> **Catatan:** Pada sesi ini tidak dilakukan modifikasi langsung terhadap file `.blend` maupun kode Unity/Python; seluruh pekerjaan berupa inspeksi file proyek dan penyusunan prompt.

- **Inspeksi Dokumen & Workspace:**
  - Membaca `konteks.md`, `prd.md`, `progress.md`, `Run Hermes Desktop.bat`, serta mengekstrak isi `TS & List Asset.xlsx` (sheet `List Asset` dan `TS`) dan `Dokumentasi Projek.docx` (berisi 20 gambar media tanpa teks XML utama).
- **Inspeksi Headless Blender (`C:\Program Files\Blender Foundation\Blender 5.1\blender.exe`):**
  - **`rumah.blend`:** Terdiri dari 2 koleksi (`Rumah 1 jendela` dan `Rumah 2 jendela`). Semua mesh sudah memiliki `UVMap`, namun baru memakai material abu-abu default (`Material`, `Material.001` dengan `BaseColor (0.8, 0.8, 0.8, 1.0)`).
  - **`Nagcu.blend`:** Menggunakan `Material.004` (Cobalt Blue `RGB (0.0, 0.0805, 0.8000)`, `Roughness 1.0`, `Metallic 0.0`), `Material.005` (Warm Golden Amber `RGB (0.8000, 0.6968, 0.3697)`, `Roughness 1.0`, `Metallic 0.0`), `Material.006` (White `0.8, 0.8, 0.8`, `Roughness 1.0`), serta node tekstur eksternal `sayap.png` dan `Screenshot 2026-09-27 133330.png` (tidak di-pack).
  - **`pulau.blend`:** Memiliki 5 material (`Mat_CliffRock`, `Mat_DirtPath`, `Mat_GrassTop`, `Mat_LakeWater`, `Mat_VillagePlaza_Drought`).
  - **`pohon.blend`:** Memiliki 3 material (`folhas`, `Material.001`, `tronco normal`).

---

## 4. Detail Penting yang Harus Diingat
- **Daftar Objek & Dimensi di `rumah.blend`:**
  - Koleksi `Rumah 1 jendela`: `Cube.002` (dinding, dim `4.64 x 5.8 x 5.1`), `Cube.003` (atap, dim `5.14 x 5.79 x 2.16`), `Cube.004` (cerobong, dim `0.99 x 0.99 x 2.72`), `pintu.001`, `Cylinder.001` (gagang pintu), `jendela.001`.
  - Koleksi `Rumah 2 jendela`: `Cube` (dinding, dim `4.48 x 6.71 x 5.21`), `roof` (atap, dim `5.04 x 7.13 x 2.24`), `Cube.001` (cerobong, dim `0.99 x 0.99 x 2.72`), `pintu`, `Cylinder` (gagang pintu), `jendela`, `Light`.
- **Path Aset Blender:** `D:\Fauzih\Kuliah\SEMESTER V\Proyek Multimedia dan Game\AR Dragon\Assets AR Dragon Sky Guardian`
- **Lokasi Blender Lokal:** `C:\Program Files\Blender Foundation\Blender 5.1\blender.exe`
- **Catatan `pulau.blend`:** Saat dibuka di Blender 5.1.2 muncul peringatan `WARNING File written by newer Blender binary (502.45), expect loss of data!`.
- **Preferensi User:** Rumah harus memakai warna realistis/alami rumah sungguhan, tidak boleh meniru warna biru/kuning naga, namun *treatment* teksturnya harus menyatu dengan gaya kartun low-poly *matte* milik `Nagcu.blend`.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] Jalankan prompt final di **Hermes Desktop** untuk menerapkan material/tekstur ke `rumah.blend` (beserta backup `rumah_backup.blend`).
- [ ] Verifikasi hasil visual `rumah.blend` di Blender setelah material/tekstur diterapkan oleh Hermes.
- [ ] Ekspor `rumah.blend` ke format `.fbx` siap Unity URP dan lanjutkan integrasi pada tahap **Sesi 3 / Sesi 4** di [[Tasks]].

---
**Navigasi Obsidian:** [[00 - Overview]] · [[konteks]] · [[prd]] · [[progress]] · [[GDD_Pulau]] · [[Tasks]] · [[Devlog]]
