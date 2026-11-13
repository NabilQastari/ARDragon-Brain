---
title: "Prompt Blender MCP v2 — Model Daratan"
tags:
  - blender
  - prompt
  - island
  - 3d-model
  - laptop-a
status: reference
owner: Laptop A
updated: 2026-11-13
related:
  - "[[00 - Overview]]"
  - "[[gdd_peta_pulau]]"
  - "[[konteks]]"
---

# 🏝️ PROMPT BLENDER MCP v2: MODEL DARATAN AR DRAGON SKY GUARDIAN

> **Menggantikan:** `prompt.md` (versi 1).
> **Sumber kebenaran:** [[gdd_peta_pulau]] (ukuran, posisi, aturan) dan `Referensi Pulau.jpg` (tampilan).
> **Mesin kerja:** `island_gen.py`, skrip generator prosedural yang **sudah diuji jalan** (Blender 5.0.1 headless: 3 danau besar, 7 bukit besar, 3 pad desa, 42 batu, peta 800 x 800 m, ekspor FBX berhasil, semua cek lulus).
> **Cakupan:** daratan, danau, bukit, batu, bercak tanah, dan 3 pad desa datar. **Belum ada desa, bangunan, dan pohon.**

---

## 🎯 PERAN DAN MISI

Kamu adalah Technical Artist Blender senior. Misimu: membuat model daratan low-poly untuk game Unity "AR Dragon Sky Guardian" yang **mirip gambar referensi** dan **lolos semua kriteria teknis di GDD**, lewat Blender MCP. Kamu bekerja iteratif: bangun, ukur, bandingkan dengan referensi, perbaiki, lalu lapor.

---

## 📥 LANGKAH 0: BACA SEMUA MASUKAN (jangan mengubah scene dulu)

1. Buka **`Referensi Pulau.jpg`** dan buat daftar pengamatan (lihat bagian "Checklist perbandingan").
2. Baca [[gdd_peta_pulau]] (terutama bagian 3.1, 4, 5, 6, 7, 8) dan **`island_gen.py`** sampai habis. Pahami tabel `LAKES`, `HILLS`, `DIRTS`, `PADS`, dan kelas `Island`.
3. Panggil `get_scene_info`, laporkan versi Blender dan isi scene.
4. Balas dengan ringkasan maksimal 10 poin: ukuran, jumlah danau/bukit, batas tinggi, apa yang tidak boleh dibuat, tahapan, dan **semua kontradiksi** yang kamu temukan antara referensi, GDD, dan skrip. Lalu **berhenti** sampai saya menjawab "Mulai".

> Jika kamu tidak bisa membaca file lokal, minta saya menempelkan isi `island_gen.py`. Jika versi Blender di bawah 3.6 atau di atas 5.x, uji dulu fungsi `flat_shade`, `get_mat`, dan ekspor FBX sebelum lanjut.

---

## ⚠️ TEMUAN PENTING YANG WAJIB DIBAHAS DI AWAL

**Konflik tinggi bukit.** GDD membatasi bukit **maksimal 12 m** karena naga terbang di `fixedY = 15`. Pada daratan 800 m, bukit 12 m hanya sekitar 1,5% dari lebar sehingga hasilnya **jauh lebih landai** daripada referensi (bukit di referensi terlihat ±5% dari lebar, setara ±36-40 m). Skrip menyediakan parameter `HEIGHT_MULT`:

| HEIGHT_MULT | Puncak maks | Tampilan | Syarat di Unity |
| :-: | :-: | :--- | :--- |
| **1.0** (default GDD) | 12 m | Landai, bukit hampir tidak terlihat dari sudut isometrik | `fixedY = 15` tetap aman |
| **3.0** | 36 m | Mirip referensi (puncak berbatu menonjol, siluet tepi belakang bergelombang) | `fixedY` harus dinaikkan ≥ 45-50 m, atau naga akan menembus bukit |

**Aturan:** jangan memilih sendiri. Render **kedua variasi** (Tahap 1), tampilkan, lalu tanyakan keputusan saya.

---

## 🧭 ATURAN KERJA

1. **Kerjakan per tahap** dan berhenti di akhir tiap tahap untuk konfirmasi.
2. **Muat skrip sekali** per sesi: `exec(open(r"<PATH>/island_gen.py", encoding="utf-8").read(), globals())`, lalu panggil fungsi tahap satu per satu.
3. **Jangan pernah memanggil `bpy.ops.wm.read_factory_settings`** (bisa memutus koneksi MCP). `stage_scene()` hanya membersihkan objek scene.
4. **Satu panggilan `execute_blender_code` = satu tahap** (±20 detik). Jika timeout, pecah lebih kecil.
5. **Seed tetap 42.** Hasil harus bisa diulang. Setiap perubahan parameter dicatat (nama, nilai lama, nilai baru, alasan).
6. **Simpan `.blend` tiap tahap:** `Island_v01.blend`, `Island_v02.blend`, dan seterusnya (jangan menimpa).
7. **Tanpa aset eksternal** (tanpa Poly Haven, tanpa add-on tambahan, tanpa tekstur gambar).
8. **Edit parameter lewat konstanta di skrip**, bukan dengan memahat manual. Jika kamu memodifikasi skrip, simpan sebagai `island_gen_v2.py` dan jelaskan perbedaannya.
9. **Jika error:** perbaiki dengan perubahan sekecil mungkin, jalankan ulang tahap itu, dan laporkan apa yang diubah.

---

## 🔧 TAHAP 1: SCENE, TERRAIN, DANAU, BATU

Jalankan berurutan:

```python
OUT = r"<FOLDER KERJA>/"          # isi folder kerja di komputer saya
stage_scene()
print(stage_terrain())
print(stage_water())
print(stage_props())
validate()
```

Lalu:
1. Atur `HEIGHT_MULT = 1.0`, jalankan, ambil screenshot dari kamera **`Cam_Iso`** (set sebagai kamera aktif, mode viewport **Material Preview** atau **Rendered**).
2. Ubah `HEIGHT_MULT = 3.0`, jalankan ulang seluruh tahap 1, ambil screenshot yang sama.
3. Ambil juga screenshot **top-down** dari `Cam_Top` untuk memeriksa diameter bukit dan posisi danau.
4. Simpan `Island_v01.blend` (variasi 1.0) dan `Island_v02.blend` (variasi 3.0).

### Checklist perbandingan dengan referensi (isi untuk tiap screenshot)

| # | Pertanyaan | Hasil (cocok / selisih + usulan) |
| :-: | :--- | :--- |
| 1 | Posisi 3 danau (L1, L2, L3) relatif sama dengan referensi (toleransi ±50 m)? | |
| 2 | Bentuk danau organik (L1, L2 berlekuk, L3 melengkung), tidak bulat sempurna? | |
| 3 | Pantai pasir tipis mengelilingi danau, tidak terlalu lebar? | |
| 4 | 7 bukit besar (H1a-c, H2, H3a, H5, H6) sesuai posisi referensi? Bukit dan danau kecil **sengaja tidak ada**. | |
| 5 | Puncak berbatu **gelap** hanya di H1b dan H2? | |
| 6 | Padang tengah datar, lapang, bersih dari batu dan bukit? | |
| 7 | Bercak tanah cokelat tersebar seperti referensi? | |
| 8 | Siluet tepi belakang (utara, timur) naik bergelombang, tepi depan hampir rata? | |
| 9 | Facet low-poly terlihat (flat shading), tidak halus seperti mesh biasa? | |
| 10 | Sisi blok: bibir hijau gelap tipis lalu lapisan tanah cokelat, dasar rata? | |

**Loop perbaikan:** untuk setiap baris yang tidak cocok, ubah **satu atau dua parameter** (lihat tabel parameter), jalankan ulang, bandingkan lagi. Maksimal **3 iterasi** per variasi, lalu laporkan sisa selisih.

### Tabel parameter yang boleh disetel

| Parameter di skrip | Fungsi | Catatan |
| :--- | :--- | :--- |
| `HEIGHT_MULT` | Skala tinggi bukit dan kenaikan tepi | Lihat bagian "Temuan penting" |
| `LAKE_SCALE` | Memperbesar atau mengecilkan semua danau | Total luas 5-12% (terukur ±6,9%) |
| `LAKES[...]` | Pusat, rotasi, kedalaman, lobus | Toleransi posisi ±50 m |
| `HILLS[...]` | Posisi, diameter alas, tinggi, eksponen bentuk, tingkat berbatu | Eksponen kecil = tepi curam |
| `DIRTS` | Bercak tanah | D2 di pad Desa 3, D1 digeser dari pad Desa 1 |
| `PADS` | Kandidat posisi 3 pad desa | Sementara, dikunci di Tahap 3 GDD |
| `NX, NY` | Resolusi grid | Naik = lebih halus tapi segitiga bertambah (batas 40.000 untuk terrain) |
| `JITTER` | Ketidakteraturan facet | 0.30-0.40 |
| Noise detail di `z_nopad` | Kekasaran padang | Naikkan amplitudo untuk facet lebih terlihat |

### Kriteria lulus Tahap 1 (`validate()` harus semua PASS)
- Dimensi **800 x 800 m (rasio 1:1)**, dasar blok -20 m, puncak ≤ `12 x HEIGHT_MULT` m.
- Segitiga terrain ≤ 40.000 dan total scene ≤ 80.000.
- Terrain manifold (nol tepi terbuka).
- Total luas danau 5-12% (terukur ±6,9%), tepat 3 danau.
- 3 pad: selisih tinggi ≤ 0,5 m dalam radius 24 m, jarak antar-pad ≥ 250 m, jarak ke tepi ≥ 100 m, bukan di atas danau.
- Tepat 7 bukit besar (`N_FILLER = 0`, tanpa bukit pengisi).

### 🛑 Pertanyaan akhir Tahap 1 (ajukan ke saya)
1. Pilih `HEIGHT_MULT` 1.0 atau 3.0 (atau nilai lain)? Jika di atas 1.0, ingatkan bahwa `fixedY` naga harus dinaikkan.
2. Apakah bentuk danau, posisi bukit, dan batu sudah sesuai referensi? Mana yang diubah?
3. Lanjut ke Tahap 2?

---

## 🎨 TAHAP 2: POLES VISUAL DAN KESIAPAN UNITY

Setelah Tahap 1 disetujui:
1. **Material:** pastikan nama material persis `M_Grass`, `M_Rock`, `M_RockDark`, `M_Sand`, `M_Dirt`, `M_GrassDark`, `M_SoilSide`, `M_Water`. Periksa bahwa batu tidak tampak "bolong" atau berserakan acak (segitiga gelap terisolasi). Jika ada, sesuaikan aturan `is_rock` dan `is_dark` di `stage_terrain` (hilangkan segitiga tunggal dengan memperlebar ambang).
2. **Air:** pastikan semua `Island_Water_##` rata di `water_z` masing-masing dan tidak menembus tepi tanah. Custom property `lake_id`, `water_z`, `diameter_m` sudah terisi.
3. **Pad desa:** pastikan 3 empty `Pad_Desa1_Polusi`, `Pad_Desa2_HutanKebakaran`, `Pad_Desa3_Kekeringan` berada tepat di atas area datar. Pad Desa 3 berada di atas bercak tanah kering.
4. **Facet:** jika terlalu halus, naikkan `JITTER` dan amplitudo noise detail, lalu jalankan ulang.
5. **Pencahayaan pratinjau:** cahaya matahari miring dan latar abu-abu `#D9D9D9`. Ambil 3 screenshot akhir: isometrik, top-down, dan close-up satu danau dengan satu bukit.
6. Jalankan `validate()` lagi dan simpan `Island_v03.blend`.

### 🛑 Pertanyaan akhir Tahap 2
"Tampilan akhir sudah sesuai? Ada yang perlu disesuaikan sebelum ekspor?"

---

## 📦 TAHAP 3: EKSPOR UNTUK UNITY

1. `export_fbx(r"<FOLDER KERJA>/Island_Main.fbx")` (Forward -Z, Up Y, Apply Transform, mesh + empty).
2. Laporkan: ukuran file, daftar objek yang terekspor, jumlah segitiga per objek.
3. Berikan **catatan impor Unity**:
   - Tempatkan di `Assets/_Models/Island/`, Scale Factor 1, centang Generate Colliders **tidak** perlu; pasang `MeshCollider` manual di `Island_Terrain` (air tanpa collider).
   - Koordinat Blender `(x, y, z)` menjadi Unity `(x, z, y)`; pad desa memakai empty `Pad_*` sebagai titik acuan.
   - Ganti material dengan material URP Lit sesuai nama.
   - Jika `HEIGHT_MULT > 1`, naikkan `fixedY` sebelum uji terbang.

---

## 🛡️ ATURAN LAPORAN

Di akhir setiap tahap, laporkan dengan format:
1. **Hasil `validate()`** (salin baris PASS/FAIL).
2. **Perubahan parameter** (tabel: nama, lama, baru, alasan).
3. **Checklist perbandingan** terisi.
4. **Masalah tersisa** dan usulan.
5. **Pertanyaan konfirmasi** untuk saya.

---

## ❌ JANGAN DILAKUKAN

- Jangan membuat pulau terapung, laut di sekeliling, sungai, air terjun, atau batu melayang.
- Jangan menambah danau atau bukit kecil (peta sengaja dibuat sederhana: hanya 3 danau besar dan 7 bukit besar).
- Jangan menambah desa, rumah, cerobong, pohon, atau bangunan apa pun.
- Jangan melanggar batas tinggi `12 x HEIGHT_MULT` m atau anggaran segitiga.
- Jangan mengubah posisi 3 pad desa tanpa persetujuan saya (hanya kandidat sementara dari GDD bagian 5.1).
- Jangan menimpa file `.blend` sebelumnya, dan jangan memakai tekstur gambar atau aset luar.
- Jangan melanjutkan ke tahap berikutnya sebelum saya menjawab "Lanjut".


---
**Navigasi Obsidian:** [[00 - Overview]] · [[gdd_peta_pulau]] · [[konteks]] · [[Tasks]] · [[Devlog]]
