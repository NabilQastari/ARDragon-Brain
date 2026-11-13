---
title: "Generasi Prosedural Daratan Low-Poly 800x800m di Blender MCP (Tahap 1)"
type: session-memory
tags:
  - session-memory
  - laptop-a
  - blender-mcp
  - terrain
  - level-design
date: 2026-11-08
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
---

# Generasi Prosedural Daratan Low-Poly 800x800m di Blender MCP (Tahap 1)

> **Catatan Konteks Waktu:** Sesi eksekusi dilakukan pada **2026-11-08** menggunakan dokumen baseline saat itu (`gdd_peta_pulau.md` v1.1, `konteks.md` v3.0, `prd.md` v3.0, `prompt_v2.md`, dan `island_gen.py`), sebelum pembaruan arsitektur menuju peta kepulauan 3 biome $1200 \times 1200\text{ m}$ pada 2026-11-13.

## 1. Tujuan & Konteks Sesi
- Memverifikasi koneksi live ke **Blender MCP** dan memeriksa kondisi awal scene Blender.
- Menganalisis 3 gambar referensi terrain *low-poly* (tampak isometrik *stepped plateau*, tampak atas/*top-down*, dan tampak isometrik pegunungan alami).
- Membaca seluruh dokumen desain dan skrip generator di root folder (`gdd_peta_pulau.md`, `konteks.md`, `prd.md`, `prompt_v2.md`, `island_gen.py`).
- Mengeksekusi **Langkah 0** dan **Tahap 1** dari `prompt_v2.md` untuk menghasilkan dua variasi model daratan $800 \times 800\text{ m}$ (`HEIGHT_MULT = 1.0` dan `3.0`), memvalidasi 14 kriteria teknis, serta merender pratinjau perbandingan.

---

## 2. Keputusan Desain / Arsitektur / Teknis
- **Acuan Bentuk Utama:** Mengikuti spesifikasi `gdd_peta_pulau.md` v1.1 dan `island_gen.py` (puncak bukit berbatu alami seperti referensi Gambar 3, bukan *stepped gray slab* pada Gambar 1).
- **Penyederhanaan Fitur Lanskap:** Hanya membangun **3 danau besar** (`L1`, `L2`, `L3`) dan **7 bukit besar** (`H1a`, `H1b`, `H1c`, `H2`, `H3a`, `H5`, `H6`) dengan `N_FILLER = 0`. Kolam satelit kecil dan bukit pengisi acak sengaja ditiadakan demi keterbacaan peta (*readability*).
- **Identifikasi 3 Kontradiksi Sebelum Eksekusi:**
  1. **Tinggi Bukit (Visual vs Gameplay):** GDD membatasi tinggi maks $12\text{ m}$ (`HEIGHT_MULT = 1.0`) agar aman untuk terbang naga pada `fixedY = 15 m`, sedangkan referensi visual setara $\pm 36\text{--}40\text{ m}$ (`HEIGHT_MULT = 3.0`). Diputuskan untuk membangun **kedua variasi** agar pengguna dapat memilih di akhir Tahap 1.
  2. **Jumlah Fitur:** Referensi gambar menampilkan kolam satelit dan bukit kecil tambahan, sedangkan GDD v1.1 & `island_gen.py` mengunci tepat 3 danau dan 7 bukit besar.
  3. **Gaya Puncak Bukit:** Gambar 1 berupa *plateau* datar abu-abu, sedangkan Gambar 3, GDD, dan skrip menggunakan puncak berbatu alami dengan material gelap (`M_RockDark`) khusus di `H1b` dan `H2`.
- **Aturan Keselamatan MCP:** Tidak memanggil `bpy.ops.wm.read_factory_settings` agar koneksi Blender MCP tidak terputus; pembersihan scene hanya melalui `stage_scene()`.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Verifikasi Koneksi Blender MCP:**
  - `get_addon_status`: Terhubung & *up-to-date* (Addon v`1.8`, Protokol v`15`, Blender `5.2.3 LTS`).
  - `get_scene_info`: Scene awal standar (`Cube`, `Light`, `Camera`).
- **Eksekusi `island_gen.py` — Variasi 1 (`HEIGHT_MULT = 1.0`, `HMAX = 12.0 m`):**
  - Aset tersimpan: `E:/Semester 5/Game/AR Dragon/Island_v01.blend`.
  - Pratinjau tersimpan: `preview_v01_iso.png` (`Cam_Iso`) dan `preview_v01_top.png` (`Cam_Top`).
  - Output generator: `34.848` segitiga atas (`35.905` total poligon terrain), `3` danau, `42` batu.
  - Hasil `validate()` (**14/14 PASS**): dimensi `800 x 800 m`, puncak `11.98 m` ($\le 12\text{ m}$), dasar `-20.00 m`, segitiga terrain `37.486` ($\le 40.000$), total scene `38.326` ($\le 80.000$), `0` non-manifold edge, luas danau `6.9%`, jarak antar-pad `290 m` ($\ge 250\text{ m}$), pad ke tepi `140 m` ($\ge 100\text{ m}$), selisih tinggi semua pad `0.00 m`.
- **Eksekusi `island_gen.py` — Variasi 2 (`HEIGHT_MULT = 3.0`, `HMAX = 36.0 m`):**
  - Aset tersimpan: `E:/Semester 5/Game/AR Dragon/Island_v02.blend`.
  - Pratinjau tersimpan: `preview_v02_iso.png` (`Cam_Iso`) dan `preview_v02_top.png` (`Cam_Top`).
  - Hasil `validate()` (**14/14 PASS**): puncak `35.95 m` ($\le 36\text{ m}$), seluruh metrik geometri, pad, dan danau lainnya lulus identik dengan Variasi 1.
- **Perbandingan Visual:** Variasi `1.0` terlihat sangat landai dari `Cam_Iso`, sedangkan Variasi `3.0` menampilkan siluet pegunungan dan kontras puncak berbatu gelap yang jauh lebih mendekati referensi Gambar 3.

---

## 4. Detail Penting yang Harus Diingat
- **Cache Singleton pada `island_gen.py`:** Saat mengubah `HEIGHT_MULT` di tengah sesi Blender yang sama, wajib memanggil `_ISL.clear()` dan menghitung ulang `HMAX = 12.0 * HEIGHT_MULT`; jika tidak, `pad_target` dan `water_z` masih memakai cache dari nilai `HEIGHT_MULT` sebelumnya.
- **Konfigurasi Eksperimen Tahap 1:**
  - `SEED = 42`, grid `NX = 132, NY = 132` ($\approx 6\text{ m}$ per sel), `JITTER = 0.36`, `SLAB = 20.0`, `LIP = 1.2`, `LAKE_SCALE = 1.18`.
  - Kandidat koordinat Pad Desa (`PAD_CORE = 25.0 m`): `P1` Desa 1 Polusi `(200, -80)` (jarak danau `146 m`), `P2` Desa 2 Hutan Kebakaran `(-10, 133)` (jarak danau `73 m`), `P3` Desa 3 Kekeringan `(-260, -13)` (jarak danau `158 m`).
- **Konsekuensi Unity jika Memilih `HEIGHT_MULT = 3.0`:** Ketinggian terbang `fixedY` di Unity (`15 m`) harus dinaikkan ke minimal `45–50 m` agar naga tidak menembus puncak `H1b` dan `H2` (`35.95 m`).

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] Menjawab 3 pertanyaan konfirmasi akhir Tahap 1: pilihan nilai `HEIGHT_MULT` (`1.0` vs `3.0` vs nilai lain), evaluasi posisi danau/bukit/batu, dan persetujuan lanjut ke Tahap 2 (*poles visual & ekspor FBX*).
- [ ] **Catatan Arsitektur Terbaru (Laptop A):** Karena per 2026-11-13 konsep pulau bergeser ke model baru **Kepulauan 3 Biome $1200 \times 1200\text{ m}$** (*Tight-Knit Caldera Archipelago*), putuskan apakah `Island_v01.blend` / `Island_v02.blend` ($800 \times 800\text{ m}$) hanya dijadikan arsip/referensi awal atau tetap dilanjutkan ke Tahap 2–3.

---

**Navigasi Obsidian:** [[00 - Overview]] · [[konteks]] · [[prd]] · [[rencana_desain_ui]] · [[Tasks]] · [[Devlog]]
