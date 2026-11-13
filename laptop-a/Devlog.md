---
title: Laptop A — Devlog (Desain, Ide, & Arsitektur)
tags:
  - devlog
  - laptop-a
owner: Laptop A
updated: 2026-11-13
---

# 📝 Devlog — Laptop A

> Catatan kronologis keputusan desain, ide, arsitektur, dan pembaruan dokumentasi dari **Laptop A**. Kembali ke [[00 - Overview]].

---

## 2026-11-13 — Klarifikasi Status Final Remodel Model Naga (`remodel naga v3/Dragon_Model.blend`)
- **Aksi:** Mengonfirmasi bahwa remodel model naga 3D telah selesai pada `remodel naga v3/Dragon_Model.blend` ([[Pembuatan Model Naga 3D v3]]), sehingga item tugas remodel lama (`assets/Naga V2 Tes 2.blend`) dihapus dari daftar fokus aktif di [[Tasks]].

## 2026-11-13 — Simplifikasi Nama File Memori & Update Aturan Penamaan
- **Aksi:** Menyederhanakan seluruh 12 nama file di `brain/laptop-a/memories/` menjadi judul ringkas (3–6 kata) agar mudah dibaca di Obsidian, memperbarui seluruh `[[wikilinks]]` di [[Devlog]] dan [[Tasks]], serta menyempurnakan aturan penamaan pada command `"jadikan sesi ini memori"` di [[00 - Overview]].

## 2026-11-13 — Penambahan Command Bersama `"jadikan sesi ini memori"`
- **Aksi:** Menambahkan spesifikasi command `"jadikan sesi ini memori"` di [[00 - Overview]] (dan rujukannya di `AGENTS.md` Laptop A) agar baik Laptop A maupun Laptop B otomatis mengenali perintah tersebut, menyimpan catatan sesi deskriptif di `memories/` masing-masing, memperbarui `Devlog.md`/`Tasks.md`, serta melakukan `commit` dan `push` ke `origin/main`.

## 2026-11-13 — Dokumentasi Sesi Remodel Naga 3D v3 ([[Pembuatan Model Naga 3D v3]])
- **Aksi:** Menyusun catatan memori terstruktur [[Pembuatan Model Naga 3D v3]] yang merekam seluruh iterasi pembuatan `remodel naga v3/Dragon_Model.blend` dari 0, transfer kepala v1/v2 (`0.685×`), perbaikan sayap, transisi $C^2$ body–ekor (`24` vertex/cincin), *caudal leaf fin*, gem dada *pear brilliant-cut* (`+21.5°` X-rot), orientasi spike kepala/punggung, penghapusan 2 spike leher atas, serta hasil verifikasi `qa_check.py`.

## 2026-11-13 — Dokumentasi Sesi Perancangan UI, Wireframe FigCraft, & Denah Pulau 3 Biome ([[Desain UI dan Denah Pulau 3 Biome]])
- **Aksi:** Menyusun catatan memori terstruktur [[Desain UI dan Denah Pulau 3 Biome]] yang merangkum penggabungan `konteks.md`/`prd.md` v4.0, penguncian aset `Referensi UI/` (8 file: Lobby, Kalibrasi, Flight HUD, Background, serta Wireframe Finish & Village Action), penguncian cetak biru peta 1:1 `assets/upgrade model pulau/Peta_Denah_Resmi_Genshin_Style.jpg` (*Tight-Knit Caldera Archipelago* $1200 \times 1200\text{ m}$), serta penyusunan 1 prompt wireframe tunggal FigCraft untuk 5 layar tersisa.

## 2026-11-13 — Dokumentasi Sesi Eksperimen Daratan Pulau di Roblox Studio ([[Eksperimen Terrain Pulau Roblox]])
- **Aksi:** Menyusun catatan memori terstruktur [[Eksperimen Terrain Pulau Roblox]] yang merekam iterasi pembuatan peta pulau `1600 × 1600` studs di Roblox Studio (mulai dari 9 formasi plateau blueprint, part & `WedgePart`/`CornerWedgePart` berorientasi diagonal, hingga Smooth Terrain prosedural `WriteVoxels` 16 chunk bebas *seam* tanpa bukit/danau) serta pembersihan total (*clean slate*) di akhir sesi untuk diskusi ulang dari awal.

## 2026-11-13 — Dokumentasi Sesi Blender MCP Pembuatan Daratan 800×800m ([[Generasi Daratan Blender Tahap 1]])
- **Aksi:** Mengarsipkan seluruh hasil sesi 2026-11-08 ke [[Generasi Daratan Blender Tahap 1]] (verifikasi koneksi Blender MCP `5.2.3 LTS`, analisis 3 referensi terrain, identifikasi 3 kontradiksi spesifikasi, serta eksekusi Tahap 1 `island_gen.py` menjadi `Island_v01.blend` [`HEIGHT_MULT = 1.0`] dan `Island_v02.blend` [`HEIGHT_MULT = 3.0`] yang keduanya lolos 14/14 cek `validate()`).

## 2026-11-13 — Konfigurasi & Integrasi MCP Server FigCraft ([[Integrasi MCP FigCraft]])
- **Aksi:** Mengonfigurasi server MCP `figcraft` (`E:\ArtCraft\Apps\FigCraft\FigCraft\figcraft-cli.exe` dengan argumen `["mcp"]`) di `C:\Users\nmast\.gemini\config\mcp_config.json`, mengekspor `instructions.md` serta 19 skema JSON tool ke `C:\Users\nmast\.gemini\antigravity\mcp\figcraft\`, dan mendokumentasikan detailnya di [[Integrasi MCP FigCraft]].

## 2026-11-13 — Iterasi Kaki, Eksperimen & Revert Body/Sayap Naga V2, dan Eksekusi `generate_dragon.py` ([[Iterasi Naga v2 dan Eksekusi Generator]])
- **Aksi:** Menyusun catatan memori terstruktur [[Iterasi Naga v2 dan Eksekusi Generator]] yang merekam perbaikan mesh `kaki` & `kaki2` pada `assets/Naga V2 Tes 2.blend`, pembuatan prompt Hermes (analisis referensi dengan `antigravity`/`gemini flash` + pemodelan Blender dengan `opus 4.6`), eksperimen dan *revert* bersih pada *body* (`Naga V2 Tes 2_backup_before_body.blend`) serta sayap (`Naga V2 Tes 2_backup_before_wings.blend`), hingga perbaikan `SyntaxError` dan eksekusi `generate_dragon.py` ke `assets/AncientDewWaterDragon_Generated.blend`.

## 2026-11-13 — Setup FigCraft MCP Live Control & Wireframe Kit 6 Layar ([[Wireframe 6 Layar UI di FigCraft]])
- **Aksi:** Mengonfigurasi FigCraft MCP (`--attach 9877`, shortcut `FigCraft (MCP Control)`), membangun 6 artboard wireframe 16:9 (`Main Menu & Level Selection`, `Calibration`, `In-Game Flight HUD`, `Village Challenge Overlay`, `Finish Victory Modal`, `Settings Modal`) dalam grid 2×3 secara live via socket kontrol, serta menyeragamkan seluruh tombol `ESC` ke sudut kanan atas ([[Wireframe 6 Layar UI di FigCraft]]).

## 2026-11-13 — Pengecekan Blender MCP & Eksekusi `generate_dragon.py` ([[Perbaikan dan Eksekusi generate_dragon]])
- **Aksi:** Memverifikasi koneksi Blender MCP (`Blender 5.2.3 LTS`, Addon `v1.8`), memperbaiki *SyntaxError* (*unterminated string literal* pada fungsi `export_standalone_obj()`) di `generate_dragon.py`, serta mengeksekusi skrip di Blender hingga menghasilkan 51 mesh komponen naga dalam koleksi `AncientDewWaterDragon` ([[Perbaikan dan Eksekusi generate_dragon]]).

## 2026-11-13 — Dokumentasi Sesi Setup Output Remodel Naga v2 ([[Setup Output Remodel Naga v2]])
- **Aksi:** Menyusun catatan memori terstruktur [[Setup Output Remodel Naga v2]] yang merekam pembuatan struktur direktori `remodel naga v2/` (`Dragon_Model.blend`, `renders/` 5 sudut pandang, `previews/` workbench & contact sheet, `textures/README.txt`), perbaikan orientasi kamera `side`/`back`, serta verifikasi reopen headless Blender 5.2.3 LTS tanpa file tercecer atau path absolut.

## 2026-11-13 — Dokumentasi Sesi Setup Blender MCP, Skill Modeling, & Konsep Pulau Sederhana ([[Setup Blender MCP dan Konsep Pulau]])
- **Aksi:** Menyusun catatan memori terstruktur [[Setup Blender MCP dan Konsep Pulau]] yang merekam konfigurasi `mcp-for-blender` (Blender 5.2.3 LTS, port 9876), instalasi 28 skill `EL4CTEO/blender-skills` di `.agents/skills/`, iterasi awal aset `Assets/pulau_sky_guardian.blend`/`.fbx`, serta keputusan penyederhanaan pulau (fokus pada elemen tantangan: sungai, hutan, cerobong asap, lahan tandus `#8B5A2B`, tanpa air terjun/pulau satelit) beserta prompt referensi gambar 3D-nya.

## 2026-11-13 — Dokumentasi Sesi Blockout Roblox Studio & Penataan Folder Proyek
- **Aksi:** Mencatat seluruh progres eksplorasi *blockout* pulau melayang (*Sky Guardian Island*) di Roblox Studio serta penataan struktur folder proyek (`assets/` dan `misc/`) ke dalam [[Blockout Pulau Roblox dan Penataan Folder]].

## 2026-11-13 — Penamaan Ulang Catatan Sesi ke Format Deskriptif
- **Aksi:** Mengganti nama `Memory1.md` menjadi [[Harmonisasi List Asset dan Time Schedule]] di `brain/laptop-a/memories/` agar seluruh catatan sesi langsung menggambarkan isi pembahasannya tanpa awalan `Memory*`.

## 2026-11-13 — Dokumentasi Sesi List Asset & Jadwal TS ([[Harmonisasi List Asset dan Time Schedule]])
- **Aksi:** Menyusun catatan memori terstruktur [[Harmonisasi List Asset dan Time Schedule]] yang mendokumentasikan perbaikan workbook `TS & List Asset.xlsx` (penyelarasan ke Versi 2 yang ramping sesuai TS, penguncian jadwal VFX Particles pekan 10–11, penataan arsip detail aset di sheet pendukung, dan penyusunan prompt Blender MCP untuk model pulau).

## 2026-11-13 — Pembersihan Dokumen Lama (`brain/laptop-a/`)
- **Aksi:** Menghapus 3 dokumen `.md` usang/sementara dari `brain/laptop-a/`:
  - `prompt_naga.md` (remodel naga v3 sudah selesai di `Dragon_Model.blend`).
  - `prompt_v2.md` (prompt daratan lama 800×800m sudah tidak dipakai).
  - `gdd_peta_pulau.md` (dihapus untuk dibuat ulang sesuai konsep baru Kepulauan 3 Biome $1200 \times 1200\text{ m}$).
- **Dokumen Aktif Tersisa:** [[00 - Overview]], [[konteks]], [[prd]], [[rencana_desain_ui]], [[Tasks]], dan [[Devlog]].

## 2026-11-13 — Setup Obsidian Brain & Migrasi Dokumen Desain
- **Aksi:** Menginisialisasi Obsidian vault terpisah di `brain/` yang terhubung ke remote `ARDragon-Brain` dan terisolasi dari repo utama melalui `.gitignore`.
- **Migrasi:** Memindahkan dokumen desain dari root ke `brain/laptop-a/` dengan format YAML frontmatter & `[[wikilinks]]` (termasuk [[konteks]], [[prd]], [[rencana_desain_ui]], serta file lama `gdd_peta_pulau.md`, `prompt_naga.md`, dan `prompt_v2.md` yang kemudian dibersihkan).
- **Aturan Kerja:** Menetapkan pemisahan tulis (*anti-collision*) antara `laptop-a/` (desain & arsitektur) dan `laptop-b/` (eksekusi & pengembangan program) di `AGENTS.md`.
- **Status Project Saat Ini:** Fokus aktif roadmap berada pada **Sesi GUI** (implementasi di Unity sesuai [[rencana_desain_ui]]), disusul **Sesi Model Pulau** (remodel kepulauan 3 biome).
