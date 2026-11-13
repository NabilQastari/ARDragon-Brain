---
title: "Eksplorasi Blockout Pulau Sky Guardian di Roblox Studio dan Penataan Folder Assets Misc"
type: session-memory
tags:
  - session-memory
  - laptop-a
  - roblox-studio
  - blender
  - blockout
  - pulau
  - folder-organization
date: 2026-11-13
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
---

# Eksplorasi Blockout Pulau Sky Guardian di Roblox Studio dan Penataan Folder Assets Misc

## 1. Tujuan & Konteks Sesi
- **Konteks Waktu:** Sesi chat ini berlangsung pada **2026-11-08** (didokumentasikan ke Obsidian Brain pada **2026-11-13**) saat eksplorasi *blockout* peta pulau masih difokuskan pada **Roblox Studio** sebelum transisi roadmap ke Unity 6 (`6000.3.7f1`).
- **Target Utama:**
  1. Memahami konsep *blockout* pulau dan merevisi `gdd_peta_pulau.md` menjadi ukuran **800 × 800 m** dengan **3 danau utama** (menghilangkan danau kecil sesuai gambar referensi `Referensi Pulau - Slab Isometrik.jpg` dan `Referensi Pulau - Topdown.jpg`).
  2. Membangun *blockout* pulau di **Roblox Studio** (`AR Dragon Map`) dengan mempertahankan 3 pulau kecil satelit dan mengganti pulau utama menggunakan model Blender kedua (`assets/pulau_sky_guardian.blend`).
  3. Menguji **Pilihan 2** (pahat Terrain Voxel via Luau) serta menyiapkan **Pilihan 1** (impor langsung 3D Model `.fbx`).
  4. Merapikan seluruh file di root folder proyek ke dalam `assets/` dan folder baru `misc/`.

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Revisi Dimensi & Danau pada `gdd_peta_pulau.md`:**
   - Skala peta direvisi dari rancangan awal menjadi **800 × 800 m**.
   - Jumlah danau tidak harus 8 dan danau kecil dihapus; disederhanakan menjadi **3 danau utama** sesuai referensi visual. *(Catatan: pada pembaruan vault 2026-11-13, rancangan peta utama berevolusi lebih lanjut ke Unity 6 dengan konsep Kepulauan 3 Biome $1200 \times 1200\text{ m}$).*
2. **Penggunaan `assets/pulau_sky_guardian.blend` sebagai Sumber Geometri:**
   - Percobaan *blockout* prosedural awal dengan `FillBall` ditolak user karena bentuknya terlalu bulat/abstrak dan jauh dari referensi.
   - Diputuskan mengambil geometri langsung dari file Blender kedua (`assets/pulau_sky_guardian.blend`) yang memuat pulau utama (`Island_Terrain`), 3 pulau kecil satelit (`Satellite_Islet_1`, `2`, `3`), danau gunung (`Mountain_Lake`), sungai (`River_Surface`), jalan tanah (`Island_Dirt_Roads`), serta layout 3 desa.
3. **Evaluasi Pilihan 2 (Voxel Terrain via Luau) vs Pilihan 1 (Impor 3D FBX):**
   - **Pilihan 2** dieksekusi dengan *BVHTree raycasting* di Blender menjadi 4.584 sel voxel (`Rock`: 32, `Grass`: 4.348, `Ground`: 115, `Water`: 89). Hasil render top-down & isometrik di Roblox Studio menunjukkan 2 celah garis vertikal kosong karena `Grass chunk 2`, `3`, dan `7` (750 blok) terlewat saat eksekusi bertahap.
   - User memutuskan beralih mencoba **Pilihan 1** (impor langsung 3D Model `.fbx`). `workspace.Terrain` dikosongkan (`Terrain:Clear()`), dan disiapkan ekspor FBX skala 10× (`assets/pulau_sky_guardian_10x.fbx`) agar sesuai skala penerbangan naga (~1334 studs).
4. **Penataan Struktur Direktori Proyek:**
   - Memisahkan seluruh aset visual/3D ke folder `assets/` dan memindahkan seluruh skrip pembantu, potongan Luau sementara, serta workbook Excel ke folder baru `misc/` agar root proyek tetap bersih.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Dokumen & Gambar Referensi:**
  - Memperbarui `gdd_peta_pulau.md` (800 × 800 m, 3 danau utama) dan menyimpan `Referensi Pulau - Slab Isometrik.jpg` serta `Referensi Pulau - Topdown.jpg` ke proyek (kini di `assets/`).
- **Uji Coba Voxel Terrain Roblox Studio (Pilihan 2):**
  - Menjalankan `build_sky_guardian_roblox.py` via Blender 5.2.3 LTS headless untuk menghasilkan `sculpt_sky_guardian.luau` dan memecahnya ke `run_1.luau`–`run_6.luau`.
  - Mengeksekusi `run_3.luau`–`run_6.luau` ke Roblox Studio (`studio_id: 437566eb-4aeb-4621-9a4b-4eb852e35ea7`) dan mengambil tangkapan layar verifikasi (`SkyGuardian_Roblox_Topdown.jpg`, `SkyGuardian_Roblox_Isometric.jpg`).
  - Memeriksa `get_console_output` Roblox Studio dan mengidentifikasi `Grass chunk 2`, `3`, dan `7` belum tereksekusi; sempat menyiapkan `fix_missing_chunks.luau` sebelum user beralih ke Pilihan 1.
- **Persiapan Impor 3D FBX (Pilihan 1):**
  - Menjalankan `workspace.Terrain:Clear()` di Roblox Studio (terverifikasi kosong melalui `screen_capture` `CurrentStudioView`).
  - Mengekspor `assets/pulau_sky_guardian_10x.fbx` (629.372 bytes, 99 objek termasuk `Island_Terrain`, `Satellite_Islet_1..3`, `Mountain_Lake`, `River_Surface`, `Waterfall_Veil`, 3 desa, dan awan) dengan `global_scale=10.0`.
  - Menguji upload via `open_cloud_invoke` (`POST /assets/v1/assets`) — gagal karena folder `E:\Semester 5\Game\AR Dragon\assets\` belum disetujui di *Assistant Settings -> MCP Permissions*. Membuka Windows File Explorer menyorot `pulau_sky_guardian_10x.fbx` agar user dapat melakukan *drag & drop* langsung ke viewport Roblox Studio.
- **Pembersihan & Organisasi Folder (`assets/` & `misc/`):**
  - Membuat folder `misc/` dan memindahkan skrip Python (`build_*.py`, `split_*.py`, `scratch_*.py`, `organize_project.py`), potongan Luau (`compact_blockout.luau`, `generate_ref_blockout.luau`, `part_*.luau`, `sg_part_*.luau`, `step_*.luau`), gambar uji (`crop_topleft.jpg`, `Restored_Terrain_View.jpg`, `test_render_blend.jpg0001.jpg`), dan file Excel (`TS & List Asset*.xlsx`) ke `misc/`.
  - Memindahkan file `.blend` (`Island_StageA_v1/v2/v3.blend*`), gambar referensi (`Referensi Pulau*`), serta hasil render/capture (`Blockout_*.jpg`, `preview_sky_guardian.jpg`, `SkyGuardian_Roblox_*.jpg`) ke `assets/`.
  - Menghapus skrip inspeksi/batch sementara (`check_*.py`, `inspect_*.py`, `count_run_blocks.py`, `make_fix.py`, `prepare_runs.py`, `export_10x.py`, `fix_missing_chunks.luau`, `run_*.luau`, `sculpt_sky_guardian.luau`).

---

## 4. Detail Penting yang Harus Diingat
- **Lokasi Blender CLI:** Blender 5.2.3 LTS berada di `D:\blender\blender.exe` (tidak terdaftar di `PATH` sistem dan memerlukan eksekusi di luar sandbox).
- **Dimensi & Pemetaan `pulau_sky_guardian`:**
  - `Island_Terrain` asli di Blender: `133.38 × 121.13 × 52.64 m` (2.742 vertices, 2.456 polygons).
  - Pemetaan skala voxel ke Roblox Studio: $S_{XZ} = 10.0\text{ studs/m}$, $S_Y = 1.8\text{ studs/m}$ (puncak $\approx 22.7\text{ studs} \le 24\text{ studs}$ agar penerbangan naga di $Y = 30\text{ studs}$ / $15\text{ m}$ tidak menabrak bukit), dengan transformasi $X_{\text{roblox}} = (x - 1.3) \times 10.0$, $Z_{\text{roblox}} = -(y - 25.0) \times 10.0$, $Y_{\text{roblox}} = z \times 1.8$.
- **Batasan Teknis Roblox Studio MCP:**
  - `terrain:FillBall` gagal (`Extents are too large`) untuk radius $> 256\text{ studs}$ (perlu dibatasi $\le 80\text{ studs}$ atau memakai `FillBlock`).
  - `execute_luau` berisiko memutus koneksi/timeout jika payload terlalu besar; harus dipecah per batch ($\le 250$ blok, `task.wait()` setiap 80 blok).
  - `open_cloud_invoke` untuk upload file lokal memerlukan penambahan direktori target pada *Assistant Settings -> MCP Permissions* di Roblox Studio.
- **Lokasi File Excel:** `TS & List Asset.xlsx`, `TS & List Asset v2.xlsx`, dan `TS & List Asset_backup.xlsx` dipindahkan ke `misc/` pada akhir sesi ini.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] *(Opsional Roblox Studio)* Jika ingin melanjutkan uji coba **Pilihan 1**, lakukan *drag & drop* `assets/pulau_sky_guardian_10x.fbx` ke viewport Roblox Studio (atau setujui folder `assets/` di *MCP Permissions*), lalu kunci `Anchored = true` pada seluruh `MeshPart`.
- [ ] *(Alur Aktif Project — [[Tasks]])* Lanjutkan **Sesi Model Pulau** untuk pipeline utama Unity 6 ($1200 \times 1200\text{ m}$ *Tight-Knit Caldera Archipelago*) sesuai [[00 - Overview]] dan [[konteks]].
- [ ] Pastikan penempatan `TS & List Asset*.xlsx` di `misc/` tetap mudah ditemukan atau disinkronkan kembali bila diperlukan untuk pembaruan jadwal di Laptop A/B.

---
**Navigasi Obsidian:** [[00 - Overview]] · [[konteks]] · [[prd]] · [[rencana_desain_ui]] · [[Tasks]] · [[Devlog]]
