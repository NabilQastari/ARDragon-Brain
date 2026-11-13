---
title: Laptop A — Tasks (Desain, Ide, & Arsitektur)
tags:
  - tasks
  - laptop-a
  - design
  - architecture
owner: Laptop A
updated: 2026-11-13
---

# 📋 Tasks — Laptop A (Desain, Ide, & Arsitektur)

> Ruangan khusus untuk melacak pekerjaan desain, GDD, UI/UX, konsep aset 3D, dan arsitektur sistem di **Laptop A**. Lihat konteks eksekusi program di [[laptop-b/Tasks]] dan [[laptop-b/Devlog]].

## 🔥 Fokus Aktif
- [ ] Susun prompt desain visual UI di FigCraft berdasarkan gambar referensi user setelah peninjauan layout wireframe selesai ([[Desain UI dan Denah Pulau 3 Biome]]).
- [ ] Simpan dokumen wireframe aktif FigCraft (`Untitled`) ke file `.figcraft` / ekspor PNG per artboard di `Referensi UI/` ([[Wireframe 6 Layar UI di FigCraft]]).
- [ ] Finalisasi arah desain & remodel *body* serta sayap `assets/Naga V2 Tes 2.blend` (mempertahankan `Tangan*`/`Kaki*` yang sudah disetujui dan kompatibilitas 36-bone `Armature`).
- [ ] Review final seluruh spesifikasi [[rencana_desain_ui]] (*Missions*, *Calibration*, *Settings*, *In-Game HUD*, *Finish Modal*) agar siap diimplementasikan oleh Laptop B.
- [ ] Buat ulang GDD Peta Pulau versi baru (**Sesi Model Pulau**: *Tight-Knit Caldera Archipelago* $1200 \times 1200\text{ m}$ — Center Dragon Spawn Peak, Smog Valley, Wildfire Woods, Drought Plateau) mengacu pada [[konteks]] dan denah `Peta_Denah_Resmi_Genshin_Style.jpg`.
- [ ] Penyelarasan koordinat pad desa terbaru pada denah Genshin-style terhadap [[prd]] Sesi 4.

## ✅ Selesai
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
