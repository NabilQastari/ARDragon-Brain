---
title: "Model Kepulauan 3D, Rumah, dan Pohon"
type: session-memory
tags:
  - session-memory
  - laptop-a
  - blender
  - 3d-model
  - archipelago
  - textures
date: 2026-11-18
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
---

# Model Kepulauan 3D, Rumah, dan Pohon

> **Catatan Konteks Waktu:** Seluruh pemodelan, teksturing PBR, penataan rumah/pohon, dan verifikasi di Blender dieksekusi pada **2026-11-14 s/d 2026-11-15**, lalu diarsipkan menjadi catatan memori sesi pada **2026-11-18**.

## 1. Tujuan & Konteks Sesi
- Menguji coba penempatan lalu menyempurnakan model terrain 3D kepulauan pada [`Pulau_Archipelago.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/Pulau_Archipelago.blend) (kanvas `400 × 400 m` skala `1:6` dari ukuran dunia `2400 × 2400 m` untuk impor Unity `Scale = (6, 6, 6)`), termasuk meninggikan dataran tengah gunung Central dan menyederhanakan danau Biome 1 menjadi 1 danau bertepian tanah.
- Menyambungkan keempat daratan kepulauan di level rendah bawah air dalam 1 mesh terpadu (`SM_Archipelago_Terrain`), membulatkan garis pantai, serta membangun transisi rumput-ke-tanah organik di sepanjang garis pantai dan celah penghubung.
- Memasukkan lalu memperluas variasi model rumah (`5` varian) dan pohon (`12` varian dengan variasi tinggi bertingkat `3.0–10.0 m` serta tekstur PBR lengkap), menata ulang area *Wildfire* dan *Drought*, serta menghapus model naga statis di atas gunung Central.
- Menjaga sinkronisasi dokumentasi Laptop A di [[Tasks]] dan [[Devlog]] serta repositori Git [`brain/`](file:///e:/Semester%205/Game/AR%20Dragon/brain/).

## 2. Keputusan Desain / Arsitektur / Teknis
- **Elevasi Gunung Central & Penghapusan Naga Statis:**
  - Dataran tengah puncak gunung Central dinaikkan dari `12.50 m` menjadi **`27.50 m`** (dikelilingi 4 puncak kaldera hingga **`37.50 m`**, setara `165 m` dan puncak `225 m` saat di Unity `Scale = (6, 6, 6)`).
  - Model naga dari [`remodel naga v3/Dragon_Model.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/remodel%20naga%20v3/Dragon_Model.blend) (`ARM_Dragon`, skala panjang `0.5000 m`) sempat diimpor ke `(0.0, 2.5, 27.50)` untuk verifikasi skala, namun **akhirnya dihapus bersih** karena naga pemain dikendalikan dan dimuat terpisah di runtime Unity (cadangan adegan bernaga disimpan ke `scratch/backups/Pulau_Archipelago_with_dragon.blend`).
- **Kepulauan Terhubung di Level Rendah (`SM_Archipelago_Terrain`) & 1 Danau Biome 1:**
  - Menghapus `SM_Biome1_Water_02` & `SM_Biome1_Water_03` sehingga hanya tersisa **1 danau utama** (`SM_Biome1_Water_01`, `176` tris, `Z = +2.35 m`, kedalaman `~1.39 m`) bertepian tanah dan lumpur lembap (tanpa pasir).
  - Menyatukan ke-4 massa pulau menjadi **1 mesh *watertight*** (`SM_Archipelago_Terrain`, `24,190` verts / `48,376` tris, `0` boundary/non-manifold/n-gons, `SLAB_BOTTOM_Z = -2.85 m`) dengan tepi pantai diperhalus (*Gaussian + Marching Squares + Chaikin*, *Shade Smooth* penuh hingga bibir dasar) serta celah bawah air terhubung kontinyu (**`Gap C-B1 = -1.501 m`**, **`Gap C-B2 = -1.434 m`**, **`Gap C-B3 = -1.297 m`**, **`Bay B1-B2 = -1.805 m`**, **`Bay B1-B3 = -1.702 m`**) tanpa menambahkan bidang laut agar elevasi dasar tetap terlihat sebelum air dipasang di Unity.
  - Shader *bake* menggunakan atribut corner `Col_BiomeMask`, `Col_BiomeAccent`, dan `Col_DroughtArid` untuk transisi berlapis Rumput -> Tanah Tepi (`soil_coast`) -> Lumpur Dasar Celah (`mud_channel`) serta pegunungan yang lebih hijau (*batu domain-warped + rumput gunung*).
- **Perbaikan & Ekspansi Aset Rumah (`01–05`) & Pohon (`01–12`):**
  - Seluruh templat kanonik dinormalisasi ber-origin di pusat alas bawah `Z = 0.0`, skala `(1, 1, 1)`, dan `1` material PBR murni per objek (`1024×1024 BC sRGB` + `512×512 R/N Non-Color`).
  - Memperbaiki *material swap* & lubang jendela gelap pada `Rumah 2 jendela`, mengganti tajuk cokelat `Pohon1` menjadi hijau konifer, serta menambah varian rumah (`03` TimberGable, `04` LShape, `05` TwoStory) dan varian pohon (`03–05` Cedar/MeadowOak/Birch hijau sehat; `06–08` DeadDry/DroughtScrub/DryPine kering; `09–10` HalfBurntPine/HalfBurntOak sebagian gosong; `11–12` BurntLeafless/BurntCharredSpire hangus hitam-arang tanpa daun).
- **Penataan Biome 2 (*Wildfire*) & Biome 3 (*Drought*):**
  - **Biome 2 (*Wildfire*):** Memindahkan desa dari sudut selatan ke tengah dataran di **`(123.0, 1.0)`** tepat di sebelah barat daya area tanah *burned* `(133.0, 24.0)`, menyingkirkan batu dari inti area terbakar, dan menanam **`125` pohon hutan** di atas & tepi area *burned* memadukan pohon hangus tanpa daun (`11–12`), setengah gosong (`09–10`), serta pohon hijau sehat (`01–05`).
  - **Biome 3 (*Drought*):** Memindahkan kedua cekungan danau kering ke samping timur desa `(6.0, -108.0)` di **`(27.5, -116.0)`** dan **`(41.5, -106.0)`**, menambahkan palet tanah/rumput kering `Col_DroughtArid` hingga ke tepi pantai bebas garis hijau, serta mengganti seluruh **`48` pohon Drought** menjadi varian kering `06–08`.
- **Variasi Tinggi Pohon Bertingkat (`3.0 m – 10.0 m`):**
  - Kanonik varian di `pohon.blend` dirancang mencakup tinggi alami `3.81–10.00 m` (`1:1`), lalu skrip penempatan memilih **`15` *tier* tinggi per `0.5 m` (`3.0, 3.5, 4.0, ..., 10.0 m`)** dengan penskalaan seragam per *instance* (`scale ∈ [0.62, 1.38]`) dan radius tabrakan yang ikut diskalakan.

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Pustaka Rumah (`assets/rumah.blend` saat sesi, kini di [`assets/misc/rumah.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/misc/rumah.blend), `154,917` B):**
  - `5` varian mesh: `SM_Rumah_01_Cottage` (`368` tris), `SM_Rumah_02_TwinWindow` (`560` tris), `SM_Rumah_03_TimberGable` (`644` tris), `SM_Rumah_04_LShape` (`632` tris), dan `SM_Rumah_05_TwoStory` (`788` tris).
- **Pustaka Pohon (`assets/pohon.blend` saat sesi, kini di [`assets/misc/pohon.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/misc/pohon.blend), `239,285` B):**
  - `12` varian mesh: `SM_Pohon_01_Pine` (`184` tris), `SM_Pohon_02_Oak` (`600` tris), `SM_Pohon_03_Cedar` (`248` tris), `SM_Pohon_04_MeadowOak` (`704` tris), `SM_Pohon_05_Birch` (`432` tris), `SM_Pohon_06_DeadDry` (`408` tris), `SM_Pohon_07_DroughtScrub` (`488` tris), `SM_Pohon_08_DryPine` (`256` tris), `SM_Pohon_09_HalfBurntPine` (`304` tris), `SM_Pohon_10_HalfBurntOak` (`696` tris), `SM_Pohon_11_BurntLeafless` (`344` tris), dan `SM_Pohon_12_BurntCharredSpire` (`344` tris).
- **Scene Kepulauan Utama ([`assets/upgrade model pulau/Pulau_Archipelago.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/Pulau_Archipelago.blend), `2,077,219` B; salinan identik juga tersedia di [`assets/Pulau_Archipelago.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/Pulau_Archipelago.blend)):**
  - Total **`352` objek**: terrain `SM_Archipelago_Terrain`, air `SM_Biome1_Water_01`, `4` grup batu `SM_Rocks_*`, **`27` rumah (`COL_Village_Houses`: `B1: 9`, `B2: 9`, `B3: 9`)**, **`316` pohon (`COL_Foliage_Trees`: `B2: 210`, `B3: 48`, `B1: 40`, `CENTER: 18`)**, kamera, dan lampu — dengan **`0` objek/mesh/armature/material/koleksi naga tersisa**.
  - Komposisi `316` pohon per varian: `Pine: 34`, `Oak: 47`, `Cedar: 43`, `MeadowOak: 30`, `Birch: 55`, `DeadDry: 15`, `DroughtScrub: 14`, `DryPine: 19`, `HalfBurntPine: 18`, `HalfBurntOak: 15`, `BurntLeafless: 15`, `BurntCharredSpire: 11`. Seluruh `15` bin tinggi terisi (`3.0m: 16` s/d `10.0m: 20`).
- **Tekstur PBR ([`assets/upgrade model pulau/textures/`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/textures/), `81` PNG total termasuk set per-pulau awal):**
  - Peta aktif utama: `T_Archipelago_{BC,R,N}.png` (`8192×8192` BC + `4096×4096` R/N), `T_Biome1_Water_{BC,R,N}.png`, `4` set `T_Rocks_*_{BC,R,N}.png`, `5` set `T_Rumah_01..05_{BC,R,N}.png`, dan `12` set `T_Pohon_01..12_{BC,R,N}.png`.
- **Verifikasi QA, Audit, Grounding, & Render:**
  - `qa_check.py` (`profile="game"`): ke-`17` templat aset (`SM_Rumah_01..05` + `SM_Pohon_01..12`) lulus **`pass = True` (`errors = []`, `warnings = []`, `ground_z = 0.0`)**; grup environment lulus **`pass = True` (`errors = []`)**.
  - `scene_audit.py`: **`ok = True`**, `objects = 352`, `images.missing = []`, `packed = 0`, `absolute_paths = []`, `orphans = 0`, seluruh `VIEW_3D` bershading `MATERIAL`.
  - *Grounding BVH*: `COL_Village_Houses` (`27` unit, `missing_hits = 0`, `z_diff ∈ [-0.0151, -0.0150] m`), `COL_Foliage_Trees` (`316` unit, `missing_hits = 0`, `z_diff ∈ [-0.1963, -0.0158] m`, tinggi bbox `1:1` `2.9998–10.0003 m`).
  - Pratinjau diperiksa pada `island_final_{topdown,perspective,village_b1,village_b2_forest,village_b3}.png` di [`assets/upgrade model pulau/previews/`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/).

## 4. Detail Penting yang Harus Diingat
- **Rasio Skala & Ketinggian Unity:** File `.blend` pulau menggunakan skala **`1:6`** (`WORLD_SCALE = 1/6`). Saat diimpor ke Unity dengan `Scale = (6, 6, 6)`, dataran spawn Center `27.50 m` menjadi `~165 m` dan puncak gunung tertinggi `37.50 m` menjadi `~225 m` (di scene Unity Laptop B memakai `maxAltitude = 260f`).
- **Koordinat Pusat Wilayah Penting (Ruang Blender `1:6`):**
  - Central Spawn Peak: `(0.0, 2.5, 27.50)` (`r = 13.5 m`).
  - Village Biome 1 (*Smog*): `(-94.0, 46.0)`.
  - Village Biome 2 (*Wildfire*): `(123.0, 1.0)`; inti hutan terbakar: `(133.0, 24.0)`.
  - Village Biome 3 (*Drought*): `(6.0, -108.0)`; cekungan danau kering: `(27.5, -116.0)` dan `(41.5, -106.0)`.
- **Instancing & Headless Blender:** `unapplied_scale` pada instance pohon serta `rotated` & `multi_user_mesh` pada rumah/pohon sengaja dipertahankan agar hemat memori dan bervariasi tinggi; jangan menjalankan *apply scale* per instance tanpa melepaskan instancing. Eksekusi Blender batch memakai `D:\blender\blender.exe -b --factory-startup --python-exit-code 1` (Blender `5.2.3 LTS`).
- **Lokasi File Setelah Reorganisasi & Git:** `rumah.blend` & `pohon.blend` yang awalnya berada di `assets/` kini tersimpan di `assets/misc/`. Hanya [`brain/`](file:///e:/Semester%205/Game/AR%20Dragon/brain/) yang memiliki repositori Git (`ARDragon-Brain.git`, `main`); jangan memodifikasi file di `brain/laptop-b/`.

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] **Laptop A ([[Tasks]]):** Susun ulang GDD Peta Pulau versi baru (*Tight-Knit Caldera Archipelago*) dan selaraskan dokumentasi koordinat desa serta spawn Center terhadap [[prd]] Sesi 4.
- [ ] **Integrasi Gameplay Lanjutan (Laptop B):** Ekspor `Pulau_Archipelago.fbx` (`349` mesh) dan perakitan scene `MainIsland_Gameplay.unity` sudah diselesaikan Laptop B ([[laptop-b/Tasks]]); tindak lanjut berikutnya berfokus pada `VillageZoneController.cs`, efek VFX Effekseer pemulihan 3 desa (*Wildfire*/*Smog*/*Drought*), serta `GameManager` & `AudioManager`.

**Navigasi Obsidian:** [[00 - Overview]] · [[konteks]] · [[prd]] · [[GDD_Pulau|GDD Pulau Laptop B]] · [[Tasks]] · [[Devlog]]
