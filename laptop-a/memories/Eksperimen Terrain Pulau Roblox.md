---
title: "Eksperimen Terrain Pulau Roblox"
type: session-memory
tags:
  - session-memory
  - laptop-a
  - roblox-studio
  - terrain
  - island-design
  - level-design
date: 2026-11-13
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[prd]]"
  - "[[konteks]]"
---

# Eksperimen Terrain Pulau Roblox

## 1. Tujuan & Konteks Sesi
Sesi ini berfokus pada eksplorasi bertahap (*step-by-step*) pembuatan ulang peta pulau **AR Dragon: Sky Guardian** di **Roblox Studio** (`AR Dragon Map`, placeId `76777908576082`, Studio ID `5ada63e9-78fd-49e5-9cd9-075e20b6ffa2`) melalui Roblox Studio MCP (`execute_luau`, `screen_capture`, `search_game_tree`), dengan mengacu pada [[prd]], `prompt.md`, serta referensi visual top-down dan isometrik dari pengguna.

Setelah sempat mereset dari awal dan membahas singkat opsi kontrol jarak jauh serta screenshot latar belakang, diskusi dikembalikan ke pembangunan pulau secara bertahap (1 per 1): dimulai dari base daratan, percobaan pondasi bukit, pengembalian fokus ke kontur daratan ber-level tanpa bukit dan danau, hingga eksplorasi Roblox Smooth Terrain prosedural sebelum akhirnya di-reset kembali ke kondisi kosong (*clean slate*).

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Ukuran & Orientasi Pulau 1:1:**
   - Skala pulau ditetapkan menjadi **1600 × 1600 studs** (persegi 1:1, rentang koordinat `X: [-800, 800]`, `Z: [-800, 800]`), tebal slab bawah 60 studs (`Base_Soil_Slab`).
   - Daratan utama dibuat *axis-aligned* (rotasi dasar `0°`); kesan miring pada gambar referensi diakui murni berasal dari sudut pandang kamera (POV) isometrik.
2. **Evaluasi Gaya Part vs. Smooth Terrain:**
   - Pendekatan Part murni (*blocky* sumbu 0°/90°) dinilai terlalu kaku seperti kotak bertumpuk. Penambahan orientasi diagonal (`28°–45°`) dan `CornerWedgePart` membantu memecah bentuk kotak, namun pengguna tetap ingin mengevaluasi versi **Roblox Smooth Terrain** (`workspace.Terrain`) lengkap dengan tekstur material dan elevasi.
   - Pada Smooth Terrain, pendekatan `FillBlock` terpisah menghasilkan tampilan pad persegi yang kaku dan tidak organik, sedangkan pendekatan prosedural **`WriteVoxels` berbasis *continuous heightmap*** (4×4 chunk, bebas *seam*) mampu menghasilkan aliran kontur alami yang jauh lebih halus sesuai formula di `prompt.md`.
3. **Pembatasan Tahap (Build 1 per 1):**
   - Pondasi bukit ditunda dan dihapus agar bentuk dasar pulau (*base island landmass* tanpa bukit dan tanpa danau) benar-benar matang terlebih dahulu.
4. **Reset Akhir Sesi:**
   - Pada akhir sesi, pengguna memutuskan untuk menghapus seluruh hasil percobaan di Roblox Studio agar dapat memulai ulang dari awal dengan arahan yang lebih tepat.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
Seluruh eksekusi dilakukan secara prosedural di **Roblox Studio** (tidak ada perubahan kode Unity/Blender permanen; koneksi Blender MCP sempat dicek dan berstatus *Not connected to Blender*):

1. **Iterasi Base Bukit Blueprint (`02_Hills_V2` → `02_Plateaus_Blueprint_Accurate`):**
   - Menghapus `02_Hill_Footprints_And_Foundations` lama.
   - Sempat membangun `02_Hills_V2` (formasi rim keliling + `Densify_Rim`, 137+27 part), namun ditolak karena membentuk cincin melingkar di tepi pulau dan 4 kotak di tengah.
   - Di-rebuild menjadi `02_Plateaus_Blueprint_Accurate` (65 instances, gaya `SmoothPlastic` `#9E9E9E` atas & `#7A7672` tebing) yang memetakan **9 formasi plateau terpisah** (`Plateau_01_NW_Top` s/d `Plateau_09_SE_Massif`) + `North_Dirt_Mound` sesuai denah top-down referensi dengan padang tengah terbuka.
2. **Iterasi Daratan Pulau Part & Wedge (`01_Island_Terrain_Levels`):**
   - Sesuai arahan pengguna untuk menunda bukit, seluruh `02_Plateaus_Blueprint_Accurate` dihapus.
   - Membangun `01_Island_Terrain_Levels` (51 part & wedge) berisi padang tengah (`Y=10`), pematang diagonal (`Y=16`), dataran tinggi utara (`Y=22–32`), teras barat/timur (`Y=16–20`), dataran selatan (`Y=7`), dan cekungan danau pasir (`Y=3`).
   - Di-rebuild menjadi **74 instances** dengan orientasi diagonal (`28°`, `32°`, `35°`, `45°`, `-25°`, `-40°`, `-45°`, `135°`, `212°`, `225°`, `315°`) serta `CornerWedgePart` untuk memecah bentuk *blocky*.
3. **Iterasi Roblox Smooth Terrain (`workspace.Terrain`):**
   - Mencadangkan `workspace.AR_Dragon_Map` (79 objek) ke `ServerStorage.AR_Dragon_Map_Parts_Backup`.
   - Membangun versi Smooth Terrain lengkap dengan `Grass` (`Decoration = true`), `Sand`, `Water` (`#5B8FB0`, transparansi `0.35`, gelombang `0.05`), `Rock`/`Basalt`, dan `Mud`/`Ground`.
   - Menyederhanakan ulang menjadi pulau tanpa bukit dan tanpa danau, lalu menggantinya dengan **Continuous Heightmap `WriteVoxels`** 16 chunk (400×400 studs per chunk, resolusi 4 studs, waktu eksekusi **2,856 detik**).
4. **Pembersihan Total (Clean Slate):**
   - Menjalankan `workspace.Terrain:Clear()` serta menghapus `AR_Dragon_Map` dan backup di `ServerStorage`. Verifikasi akhir memastikan `Workspace` hanya berisi `Terrain` (kosong) dan `Camera`.

---

## 4. Detail Penting yang Harus Diingat
- **Batas & Perilaku API Roblox Studio Terrain:**
  - `Region3` untuk `ReadVoxels` pada volume `1600 × 150 × 1600` melebihi batas dan memicu error `Region is too large`; ukuran aman yang teruji cepat untuk `WriteVoxels` adalah chunk **400 × 400 studs** (`100 × 100` kolom voxel resolusi 4).
  - Untuk menghindari *seam* (celah 1 voxel) antar-chunk pada `WriteVoxels`, batas chunk harus disejajarkan tepat ke pusat voxel kelipatan 4: `[startK * 4 - 2, (startK + 99) * 4 + 2]`.
  - Parameter wajib Roblox Studio MCP: `execute_luau` membutuhkan `datamodel_type` (`"Edit"`/`"Client"`/`"Server"`) dan `studio_id`; `screen_capture` membutuhkan `camera_position`, `look_at_position` (array 3 elemen), dan `capture_id`.
- **Formula Heightmap Kontinu yang Teruji (`H(x, z)`):**
  - `H_BASE = 8` studs, `BOTTOM_Y = -34`, `TOP_Y = 54`.
  - `edge_rise`: sisi utara (`-Z`) dan timur laut (`+X, -Z`) naik halus hingga `+22` s/d `+34` studs dengan variasi gelombang `3 * math.sin(x / 130) * uN`.
  - `diagonal_ridge`: garis normal `0.848 * x + 0.530 * z + 80` menggunakan kurva *smoothstep* naik `+10` studs.
  - `flatness`: area tengah sekitar `(0, 0)` dibuat paling datar (`rCenter < 0.15`), sementara tepian luar memiliki noise halus.
- **Preferensi Visual Pengguna:**
  - Sangat sensitif terhadap bentuk kotak (*blocky*) dan penempatan fitur yang tidak organik; lebih menginginkan siluet alami yang mengikuti aliran kontur referensi sebelum menambahkan detail bukit atau danau.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] Diskusikan ulang dengan pengguna pendekatan dasar pembuatan pulau dari nol di Roblox Studio (atau sinkronisasi dengan pipeline Blender `1200 × 1200 m` pada [[Tasks]] Laptop A) sebelum membangun ulang aset apa pun.
- [ ] Konfirmasi apakah peta di Roblox Studio ini merupakan prototipe blokout terpisah atau perlu diselaraskan dengan GDD Kepulauan 3 Biome (*Tight-Knit Caldera Archipelago*) di [[konteks]] dan [[prd]].

**Navigasi Obsidian:** [[00 - Overview]] · [[konteks]] · [[prd]] · [[rencana_desain_ui]] · [[Tasks]] · [[Devlog]]
