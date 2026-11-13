---
title: "Wireframe 6 Layar UI di FigCraft"
type: session-memory
tags:
  - session-memory
  - laptop-a
  - figcraft
  - ui-ux
  - wireframe
  - mcp
date: 2026-11-13
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[rencana_desain_ui]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
---

# Wireframe 6 Layar UI di FigCraft

## 1. Tujuan & Konteks Sesi
- Mengecek koneksi dan kemampuan MCP server **FigCraft** (`E:\ArtCraft\Apps\FigCraft\FigCraft\figcraft-cli.exe`).
- Mengatasi masalah sinkronisasi di mana perintah MCP sebelumnya berjalan secara *headless* di memori dan tidak tampil pada jendela aplikasi GUI `figcraft.exe` yang sedang dibuka pengguna.
- Meningkatkan kecepatan pembuatan desain sekaligus menampilkan proses perakitan UI secara **live & bertahap** (*step-by-step*) di dalam jendela FigCraft.
- Menyusun *wireframe kit* 2D bergaya **Pure Minimalist Grayscale Wireframe** (16:9 `1920×1080` per layar, monokrom, tanpa warna/tekstur dekoratif) untuk seluruh alur UI *AR Dragon: Sky Guardian* mengacu pada [[rencana_desain_ui]], serta menyeragamkan posisi tombol `ESC` di sudut kanan atas.

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Mode Koneksi FigCraft Live (`--control` / `--attach 9877`):**
   - `figcraft-cli.exe mcp` tanpa argumen hanya membuka sesi *headless* terpisah.
   - Diputuskan mengubah konfigurasi MCP agar menggunakan `--attach 9877` dan menjalankan `figcraft.exe --control 9877` supaya seluruh perubahan MCP langsung ter-render secara *real-time* di jendela aplikasi GUI FigCraft (port `9876` tetap untuk Blender MCP).
2. **Strategi Eksekusi Live & Cepat:**
   - Ditemukan bahwa socket kontrol `127.0.0.1:9877` menerima line-delimited JSON-RPC (`method: "engine.execute"`, mendukung `node.create`, `command.batch`, `edit.delete`, `view.zoomSelection`, `view.zoomFit`).
   - Untuk memenuhi preferensi pengguna agar pengerjaan terlihat bertahap (*tidak langsung jadi sekaligus*) namun tetap cepat, digunakan dua pendekatan skrip Python: *incremental builder* dengan jeda `0.32s–0.38s` per elemen dan *per-screen batch* (`command.batch` dengan `$ref`) berjeda `0.4s` antar-layar.
   - Seluruh node teks wajib diberi `textAutoResize: "widthAndHeight"` agar teks tidak otomatis terpotong/wrapping pada lebar default `100px`.
3. **Standar Layout Wireframe & Navigasi `ESC`:**
   - Seluruh layar disusun dalam grid **2×3** (`1920×1080` per artboard, background `#ECECEC` / `#DFDFDF`, outline `#222222`–`#888888`).
   - **Standarisasi UX:** Seluruh tombol `ESC` (`[ ESC · QUIT ]`, `[ ESC · BACK ]`, `[ ESC · PAUSE ]`, `[ ESC · LOBBY ]`) dipindahkan dan diseragamkan ke **pojok kanan atas** (`y: 44` / `40`, ukuran `160×42`, `cornerRadius: 21`).

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
1. **Konfigurasi & Launcher Sistem (Di Luar Repo `brain/`):**
   - `C:\Users\nmast\.gemini\config\mcp_config.json` — Menambahkan `"--attach", "9877"` pada argumen server MCP `figcraft`.
   - `C:\Users\nmast\Desktop\FigCraft-Control.bat` & `C:\Users\nmast\Desktop\FigCraft (MCP Control).lnk` — Dibuat sebagai launcher sekali klik untuk menjalankan `figcraft.exe --control 9877` (mengatasi kendala sintaks `&` PowerShell di CMD).
2. **Skrip & Artefak Sesi (`C:\Users\nmast\.gemini\antigravity\brain\381c65d6-8254-466f-940b-1a5cd3e6d3ff\`):**
   - `scratch/live_builder.py`, `scratch/build_victory_wireframe.py`, `scratch/build_title_screen_live.py`, dan `scratch/build_level_selection_wireframe.py` — Skrip uji coba & perakitan bertahap.
   - `scratch/build_complete_wireframe_kit.py` — Skrip pembangun 5 layar wireframe kit secara berurutan.
   - `live_progress.md`, `live_progress_missions.png`, `wireframe_kit_report.md`, dan `wireframe_kit_suite.png` — Artefak pratinjau screenshot dari jendela FigCraft.
3. **Hasil Kanvas FigCraft (`Untitled`, `Page 1` — 6 Artboards 16:9 dalam Grid 2×3):**
   - **Baris Atas (`y: 0`):**
     - `Main Menu & Level Selection - Wireframe (1920x1080)` (`x: 0`, id `99`): Header `[ AR DRAGON · SKY GUARDIAN ]`, navbar kapsul 3 tab (`CALIBRATION`, `MISSIONS` aktif, `SETTINGS`), badge `[ ● CAM READY ]` (`x: 1500, y: 44`), `[ ESC · QUIT ]` (`x: 1690, y: 44`), serta 3 kartu level (`[ 01 ] SMOG VALLEY` 90s 3★ `[ PLAY ]`, `[ 02 ] WILDFIRE` 150s 2★1☆ `[ SELECT ]`, `[ 03 ] DROUGHT` `🔒 [ LOCKED ]`).
     - `SCREEN 01 - Calibration & Motion Tutorial (1920x1080)` (`x: 2100`, id `150`): Tab `CALIBRATION` aktif, `[ ESC · BACK ]` (`x: 1680, y: 44`), Dual-Panel (`[ CAMERA MIRROR · PLAYER TRACKING ]` stickman + `[ TRACKING: READY (1.8m) ]` dan `[ LIVE DRAGON PREVIEW · SPREAD-WING POSE ]` + `[ STATUS: CRUISE FLIGHT ]`), 3 kartu gestur (`POSE Y`, `POSE T` aktif, `POSE A`), dan `[ READY TO FLY ]`.
     - `SCREEN 02 - In-Game Flight HUD (1920x1080)` (`x: 4200`, id `184`): Kapsul `[ ⏱ 02:15 · VILLAGES: 1 / 3 ]`, `[ ESC · PAUSE ]` (`x: 1720, y: 40`), vertical progress bar kiri `[ 33% ]`, waypoint `[ ▲ WILDFIRE · 140m ]`, cluster kanan bawah (`[ ALT: 42m | SPD: 12.0m/s | POSE T ]` + PIP webcam `400×225` `[ ● CAM READY ]`).
   - **Baris Bawah (`y: 1200`):**
     - `SCREEN 03 - Village Challenge Overlay (1920x1080)` (`x: 0`, id `201`): Overlay `#DFDFDF`, banner `[ VILLAGE 2: WILDFIRE WOODS ]`, kartu gestur `[ GESTURE: OPEN MOUTH TO BREATHE WATER ]`, progress bar `[ 70% RESTORED ]`, badge `[ STATUS: ACTION ACTIVE ]`.
     - `SCREEN 04 - Finish UI Victory Modal (1920x1080)` (`x: 2100`, id `213`): Modal terpusat `680×800`, `[ STAGE COMPLETE ]`, 3 bintang `[ ★ ] [ ★ ] [ ★ ]`, 3 baris statistik (`Time Remaining: 00:46`, `Villages Restored: 3 / 3`, `Total Score: 9450`), tombol `[ NEXT LEVEL ]` & `[ REPLAY ]`, serta `[ ESC · LOBBY ]` (`x: 1680, y: 44`).
     - `SCREEN 05 - Settings & Audio Modal (1920x1080)` (`x: 4200`, id `236`): Tab `SETTINGS` aktif, `[ ESC · BACK ]` (`x: 1680, y: 44`), kartu `760×840` berisi `[ AUDIO & CONTROLS SPECIFICATION ]`, 4 slider (*Master 80%*, *BGM 70%*, *SFX 90%*, *Turn Sensitivity 1.5x*), dropdown `[ Camera Device: USB HD Webcam (Default) ▼ ]`, dan `[ APPLY & CLOSE ]`.
   - **Verifikasi:** Seluruh 6 artboard telah diverifikasi via `inspect_document` dan `screenshot {window: true}` dengan skala `zoomFit` (`~0.1425`) tanpa error.

---

## 4. Detail Penting yang Harus Diingat
- **Syarat Menjalankan FigCraft Live:** Pengguna harus membuka aplikasi melalui shortcut Desktop **`FigCraft (MCP Control)`** (atau perintah CMD `"E:\ArtCraft\Apps\FigCraft\FigCraft\figcraft.exe" --control 9877` tanpa awalan `&` PowerShell) sebelum memakai MCP `figcraft`.
- **Status Penyimpanan Dokumen FigCraft:** Hingga akhir sesi, dokumen di FigCraft masih berstatus `Untitled` (`path: null`, `dirty: true`) di memori aplikasi yang terbuka dan belum disimpan ke file `.figcraft` permanen.
- **Preferensi Pengguna:** Pengguna ingin proses pembuatan desain di FigCraft terlihat berjalan bertahap (*live build*) di dalam aplikasi, serta tombol `ESC` selalu konsisten berada di kanan atas.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] **Simpan Dokumen Wireframe FigCraft:** Simpan dokumen aktif di FigCraft menjadi file `.figcraft` di dalam proyek (mis. `Referensi UI/ARDragon_Wireframe_Kit.figcraft`) atau ekspor PNG per artboard agar tidak hilang saat aplikasi ditutup.
- [ ] **Lanjut ke Tahap Visual / Implementasi Unity:** Lanjutkan review spesifikasi [[rencana_desain_ui]] (atau konversi wireframe ke versi *Full Color Cartoon Fantasy* jika diperlukan) sebelum implementasi Canvas oleh Laptop B.

---

**Navigasi Obsidian:** [[00 - Overview]] · [[konteks]] · [[prd]] · [[rencana_desain_ui]] · [[Tasks]] · [[Devlog]]
