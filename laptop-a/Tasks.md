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
- [ ] Review final seluruh spesifikasi [[rencana_desain_ui]] (*Missions*, *Calibration*, *Settings*, *In-Game HUD*, *Finish Modal*) agar siap diimplementasikan oleh Laptop B.
- [ ] Buat ulang GDD Peta Pulau versi baru (**Sesi Model Pulau**: *Tight-Knit Caldera Archipelago* $1200 \times 1200\text{ m}$ — Center Dragon Spawn Peak, Smog Valley, Wildfire Woods, Drought Plateau) mengacu pada [[konteks]] dan denah `Peta_Denah_Resmi_Genshin_Style.jpg`.
- [ ] Penyelarasan koordinat pad desa terbaru pada denah Genshin-style terhadap [[prd]] Sesi 4.

## ✅ Selesai
- [x] Pengecekan Blender MCP (`Blender 5.2.3 LTS`, Addon `v1.8`), perbaikan *SyntaxError* pada `generate_dragon.py`, dan eksekusi generator naga low-poly `AncientDewWaterDragon` (51 mesh) di Blender ([[Pengecekan Blender MCP dan Perbaikan serta Eksekusi generate_dragon.py]]).
- [x] Setup koneksi Blender MCP (port `9876`, Blender 5.2.3 LTS) & instalasi 28 skill `EL4CTEO/blender-skills` di `.agents/skills/` ([[Setup Blender MCP, Instalasi Blender Skills, dan Penyederhanaan Konsep Pulau]]).
- [x] Penataan struktur folder proyek (`assets/` untuk model/referensi, `misc/` untuk skrip sementara/Luau/Excel) dan dokumentasi sesi eksplorasi *blockout* Roblox Studio ([[Eksplorasi Blockout Pulau Sky Guardian di Roblox Studio dan Penataan Folder Assets Misc]]).
- [x] Sinkronisasi dan perbaikan sheet `List Asset` (Versi 2 aset utama & arsip detail) pada `TS & List Asset.xlsx` sesuai Time Schedule ([[Perbaikan Harmonisasi List Asset Excel terhadap Time Schedule dan Prompt Pulau Melayang]]).
- [x] Finalisasi [[konteks]] v4.0 (Single Source of Truth sistem terbang Y-T-A, Dual-Port UDP 5052/5053, dan kontrol keyboard zero-conflict).
- [x] Finalisasi [[prd]] v4.0 (Roadmap Tahap 0 – Sesi 5).
- [x] Kunci desain UI (*Design Approved*) di [[rencana_desain_ui]] beserta aset referensi di `Referensi UI/`.
- [x] Setup vault Obsidian terpisah (`brain/`) dan bersihkan dokumen lama yang sudah tidak dipakai (`gdd_peta_pulau.md`, `prompt_naga.md`, `prompt_v2.md`).

## 💡 Backlog Ide & Arsitektur
- [ ] Desain detail *Village Action Challenge Overlay* (*Auto-Hover* prompt) & transisi VFX pemulihan 3 desa.
- [ ] Rancangan audio & feedback SFX (kepakan sayap, dive wind, semburan air, lonceng kemenangan).
