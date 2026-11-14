---
title: Laptop A — Tasks (Desain, Ide, & Arsitektur)
tags:
  - tasks
  - laptop-a
  - design
  - architecture
owner: Laptop A
updated: 2026-11-14
---

# 📋 Tasks — Laptop A (Desain, Ide, & Arsitektur)

> Ruangan khusus untuk melacak pekerjaan desain, GDD, UI/UX, konsep aset 3D, dan arsitektur sistem di **Laptop A**. Lihat konteks eksekusi program di [[laptop-b/Tasks]] dan [[laptop-b/Devlog]].

## 🔥 Fokus Aktif
- [ ] Generate **4 gambar referensi konsep 3D per pulau** menggunakan 4 foto pulau datar terisolasi (`ref_flat_3d_center.png`, `ref_flat_3d_biome1_smog.png`, `ref_flat_3d_biome2_wildfire.png`, `ref_flat_3d_biome3_drought.png`) + 4 prompt desain (*Central Peak*, *Smog Valley*, *Wildfire Woods*, *Drought Plateau*) sebagai acuan resmi *sculpting*/elevasi di Blender tahap berikutnya.
- [ ] Review halaman `Design` di `UI AR Dragon.figcraft` & ekspor PNG per artboard ke `Referensi UI/` jika sudah disetujui final.
- [ ] Review final sisa spesifikasi [[rencana_desain_ui]] (*Missions*, *Settings*, *In-Game HUD*, *Finish Modal*) agar siap diimplementasikan oleh Laptop B pada **Sesi GUI**.
- [ ] Buat ulang GDD Peta Pulau versi baru (**Sesi Model Pulau**: *Tight-Knit Caldera Archipelago* — Center Dragon Spawn Peak, Smog Valley, Wildfire Woods, Drought Plateau) mengacu pada [[konteks]], denah `Peta_Denah_Resmi_Genshin_Style.jpg`, dan skala final yang disetujui.
- [ ] Penyelarasan koordinat pad desa terbaru pada denah Genshin-style terhadap [[prd]] Sesi 4.

## ✅ Selesai
- [x] **Penyempurnaan detail kriya visual seluruh 7 artboard UI page `Design` mendekati 3 referensi RPG kartu perkamen (`v1.6`)** di [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) (`859905` bytes) & [[rencana_desain_ui]]: 4 *Corner Fan-Medallion* sudut kartu, banner judul *Stepped Plaque* seragam + *Baroque Scrollwork Crest*, tag `STAGE 01–03` terpusat bersheen, baris stat *borderless recessed taupe* + medali kiri, pill bawah `STAR 1..3` / `0 / 1 ★`, speech-bubble callout emas (`90s`, `150s`, `+3 STARS`) berkoin & *hatch ticks*, slot/tombol `LOCKED` *charcoal slate*, fix kontras tombol putih `RECALIBRATE (C)` + notch, serta kedalaman bayangan 3D hard shadow konsisten pada 15 kartu pose, HUD `SCREEN 02`, & spec `SCREEN 06` (dengan aturan outline tinta netral seragam `#332C25` tetap terjaga).
- [x] **Pengembalian Biome 1 ke Flat Layout + Shade Smooth Seluruh Kepulauan + 4 Render Referensi 3D Individual** di [`Pulau_Archipelago.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/Pulau_Archipelago.blend) (`230,048` B): Biome 1 (`SM_Island_Biome1_Smog`, `658` verts / `1,230` faces) dikembalikan datar (`Z = [-1.500, 0.000] m`, relief & air `Island_v02` dibersihkan); seluruh 4 pulau datar (`Center`, `Biome1_Smog`, `Biome2_Wildfire`, `Biome3_Drought`) + marker memakai *Shade Smooth* dengan `sharp_edge` di bibir tebing pantai & alas bawah (`pass=True`, `0 errors`, `0.0%` UV overlap, `scene_audit ok=True`); merender 4 foto sudut 3/4 terisolasi per pulau (`ref_flat_3d_*.png`, `1280 × 1280` tanpa tetangga/marker) beserta 4 prompt *concept-art* untuk menghasilkan referensi pulau 3D masing-masing wilayah.
- [x] ~~Migrasi Kontur `Island_v02.blend` ke Biome 1~~ *(arsip eksperimen di skrip scratch; dikembalikan ke kondisi datar atas permintaan user untuk pembuatan referensi 3D per pulau terlebih dahulu)*.
- [x] Pembuatan & penyempurnaan **Fase 1 Flat Layout Pulau (`400 × 400 m`, skala `1:6`, *Smooth Minimalist* garis pantai)** langsung di Blender 5.2.3 LTS (`assets/upgrade model pulau/Pulau_Archipelago.blend` — 4 mesh daratan datar tanpa laut, level ground, gunung, dan danau: `SM_Island_Center`, `SM_Island_Biome1_Smog`, `SM_Island_Biome2_Wildfire`, `SM_Island_Biome3_Drought`, plus `COL_Layout_Markers` kandidat Spawn/3 Desa & `Ref_Dragon_Scale_5m`), lulus `qa_check.py` per pulau (`0 errors`, `0 ngons`, `0 boundary/non-manifold`, `0.0%` UV overlap pada semua 4 pulau) & `scene_audit.py` bersih, dengan render pratinjau `flat_topdown.png` dan `flat_perspective.png`.
- [x] Penyeragaman warna outline seluruh kontainer UI pada 7 artboard page `Design` (`SCREEN 00–06`) ke tinta netral konsisten (`#332C25` utama, `#A89E8B` inner rim tombol `ESC`/`Close`, `#9D9384` nonaktif, `#4B565D` badge viewport gelap) tanpa outline berwarna merah/biru/hijau/kuning, diverifikasi via render ulang & disimpan di `UI AR Dragon.figcraft` (`791985` bytes) serta diperbarui di [[rencana_desain_ui]] (`v1.5`).
- [x] Perbaikan geometri sudut & border-radius seluruh 7 artboard page `Design` (`SCREEN 00–06`) menjadi **Stepped Rabbeted Plaques (`a=14,b=7` utama, `a=10,b=5` sub-panel, `a=8/6` pose & row)** dengan double inner border *stepped + concave scalloped arch* (`fill: none`) dan sudut tajam seragam `r = 1..3 px` pada kontrol/tombol/badge/slot sesuai 3 referensi UI, tersimpan di `UI AR Dragon.figcraft` (`792157` bytes) & terdokumentasi di [[rencana_desain_ui]] (`v1.4`).
- [x] Penyempurnaan gaya visual *Fantasy Parchment-and-Ink Tabletop Card UI* (`v1.3`) pada seluruh 7 artboard page `Design` (`SCREEN 00–06`) di `UI AR Dragon.figcraft` (spanduk heksagonal runcing ber-*fleuron*, baroque flourish, `r = 4..10` chamfered cards/buttons, tombol taktil `#5C8D46` bersheen & notch ganda, speech-bubble tags `#DF9E38`, slider presisi, fix `fill: "none"` seluruh vektor terbuka & z-order kartu agar bebas garis potong), tersimpan `730169` bytes & tercatat di [[rencana_desain_ui]] `v1.3`.
- [x] Pembaruan spesifikasi `rencana_desain_ui.md` (`v1.2`) & 2 halaman FigCraft (`Wireframe` + `Design`) di `UI AR Dragon.figcraft` untuk kontrol kamera & YOLO dual-mode (`SCREEN 00`, `01`, `02`, `05`, plus `SCREEN 06` berisi 5 state visual kamera, popup dropdown solid `#FBF6EB` 100% opaque, dan 3 varian badge navbar dinamis).
- [x] Pemasangan 3 ilustrasi masalah biome asli (*Smog Valley*, *Wildfire*, *Drought*) ke kotak gambar `380 × 210 px` pada 3 kartu level *Main Menu* (`id: 263` -> `277`, `292`, `307`) di page `Design` `UI AR Dragon.figcraft` (dengan `Locked Scrim` tetap aktif pada *Drought*) & arsip file ke `Referensi UI/UI_Biome_*.jpg`.
- [x] Perbaikan halaman `Calibration` (`SCREEN 01`) pada page `Design` di `UI AR Dragon.figcraft` & [[rencana_desain_ui]] Bagian 2.2 agar selaras 100% dengan seluruh pose & mekanisme nyata Laptop B: ekspansi menjadi matriks **15 kartu pose** (grid `3×5` mencakup *Grounded Idle* tegak lurus `0 m/s`, *Grounded Walk*, *Grounded Look Up*, *Takeoff*, *Lean & Arm Tilt*, *Flap to Climb*, *Fly Up*, *Glide*, *Hover*, *Gentle Descent*, *Fly Down Pose Y/W*, *Tuck Dive*, *Propeller Dash*, & *Village Actions*), kalibrasi biometrik State `0–5` + tombol UDP `5054` `RECALIBRATE (C)`/`RESET DEFAULT`, serta pil `ARM GATE RULE` & `SEATED & SAFETY`.
- [x] Duplikasi page `Wireframe` menjadi page `Design` di `UI AR Dragon.figcraft` serta implementasi penuh desain visual RPG parchment-stone (6 layar 16:9 mengikuti 100% layout wireframe).
- [x] Penggabungan `konteks.md`/`prd.md` v4.0, pengumpulan 8 aset referensi UI di `Referensi UI/`, penguncian cetak biru peta 1:1 `assets/upgrade model pulau/Peta_Denah_Resmi_Genshin_Style.jpg` (*Tight-Knit Caldera Archipelago* $1200 \times 1200\text{ m}$), dan penyusunan 1 prompt wireframe tunggal FigCraft untuk 5 layar tersisa ([[Desain UI dan Denah Pulau 3 Biome]]).
- [x] Setup FigCraft MCP live control (`--control 9877` / `--attach 9877`), pembuatan 6 artboard wireframe UI/UX monokrom 16:9 (`Main Menu`, `Calibration`, `Flight HUD`, `Village Challenge`, `Finish Modal`, `Settings Modal`), dan penyeragaman tombol `ESC` di kanan atas ([[Wireframe 6 Layar UI di FigCraft]]).
- [x] Pembuatan dan iterasi revisi model naga 3D **v3** di `remodel naga v3/Dragon_Model.blend` (kepala v1/v2 `0.685×`, transisi $C^2$ body–ekor `24`-vert, gem dada *pear brilliant-cut*, orientasi spike, & `qa_check.py` lulus) ([[Pembuatan Model Naga 3D v3]]).
- [x] Konfigurasi & integrasi MCP server FigCraft (`figcraft-cli.exe mcp`, `v0.18.0`, 19 tools) ke Antigravity ([[Integrasi MCP FigCraft]]).
- [x] Pengecekan Blender MCP (`Blender 5.2.3 LTS`, Addon `v1.8`), perbaikan *SyntaxError* pada `generate_dragon.py`, dan eksekusi generator naga low-poly `AncientDewWaterDragon` (51 mesh) di Blender ([[Perbaikan dan Eksekusi generate_dragon]]).
- [x] Setup koneksi Blender MCP (port `9876`, Blender 5.2.3 LTS) & instalasi 28 skill `EL4CTEO/blender-skills` di `.agents/skills/` ([[Setup Blender MCP dan Konsep Pulau]]).
- [x] Penataan struktur folder proyek (`assets/` untuk model/referensi, `misc/` untuk skrip sementara/Luau/Excel) dan dokumentasi sesi eksplorasi *blockout* Roblox Studio ([[Blockout Pulau Roblox dan Penataan Folder]]).
- [x] Sinkronisasi dan perbaikan sheet `List Asset` (Versi 2 aset utama & arsip detail) pada `TS & List Asset.xlsx` sesuai Time Schedule ([[Harmonisasi List Asset dan Time Schedule]]).
- [x] Finalisasi [[konteks]] v4.0 (Single Source of Truth sistem terbang Y-T-A, Dual-Port UDP 5052/5053, dan kontrol keyboard zero-conflict).
- [x] Finalisasi [[prd]] v4.0 (Roadmap Tahap 0 – Sesi 5).
- [x] Kunci desain UI (*Design Approved*) di [[rencana_desain_ui]] beserta aset referensi di `Referensi UI/`.
- [x] Setup vault Obsidian terpisah (`brain/`) dan bersihkan dokumen lama yang sudah tidak dipakai (`gdd_peta_pulau.md`, `prompt_naga.md`, `prompt_v2.md`).

## 💡 Backlog Ide & Arsitektur
- [ ] Desain detail *Village Action Challenge Overlay* (*Auto-Hover* prompt) & transisi VFX pemulihan 3 desa.
- [ ] Rancangan audio & feedback SFX (kepakan sayap, dive wind, semburan air, lonceng kemenangan).
