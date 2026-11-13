---
title: "Setup Blender MCP, Instalasi Blender Skills, dan Penyederhanaan Konsep Pulau"
type: session-memory
tags:
  - session-memory
  - laptop-a
  - blender-mcp
  - blender-skills
  - 3d-island
  - prompt-engineering
date: 2026-11-13
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[konteks]]"
  - "[[prd]]"
---

# Setup Blender MCP, Instalasi Blender Skills, dan Penyederhanaan Konsep Pulau

## 1. Tujuan & Konteks Sesi
- Membaca spesifikasi model pulau pada `prd.md` dan `konteks.md` (saat sesi berlangsung masih mengacu pada spesifikasi awal v3.0 di root workspace).
- Menghubungkan Antigravity dengan **Blender 5.2.3 LTS** melalui **Blender MCP (`mcp-for-blender`)** agar agen dapat membuat dan memvalidasi aset 3D pulau secara langsung di viewport Blender.
- Mencari dan memasang paket *agent skills* Blender 5.2 dari GitHub untuk meningkatkan kualitas pemodelan, *QA check*, dan ekspor ke Unity.
- Mencari referensi visual, melakukan iterasi pemodelan awal pulau di Blender, lalu menyederhanakan konsep desain pulau agar fokus pada elemen tantangan permainan serta menyusun prompt referensi gambar 3D.

---

## 2. Keputusan Desain / Arsitektur / Teknis
- **Integrasi Blender MCP Lokal:** Menggunakan executable virtualenv lokal (`D:\Tools\mcp4blender-venv\Scripts\mcp-for-blender.exe`) pada `BLENDER_HOST=localhost` dan `BLENDER_PORT=9876` (tanpa bergantung pada `uvx` yang belum tersedia di `PATH`) dengan `DISABLE_TELEMETRY=true`.
- **Standarisasi Pipeline Blender 5.2 via `EL4CTEO/blender-skills`:** Memasang 28 skill modular khusus Blender 5.2 ke `.agents/skills/` agar penulisan skrip `bpy`, *hard-surface bevel*, *environment kits*, *asset QA*, dan ekspor FBX mengikuti standar API 5.2.
- **Penyederhanaan Konsep Pulau (*Challenge-Focused*):**
  - Setelah iterasi model pulau lengkap (yang menyertakan air terjun tepi jurang, pulau satelit berkristal, dan awan), diputuskan bahwa untuk tahap awal pulau dibuat **simpel, bersih, dan fungsional**.
  - Elemen kosmetik berlebih (**air terjun** dan **pulau satelit**) dihilangkan dari kebutuhan utama.
  - Elemen yang dipertahankan hanyalah yang berhubungan langsung dengan tantangan: **1 aliran sungai utama** (batas alami & sumber air), **kawasan pemukiman cerobong asap** (Desa 1), **kawasan hutan rimbun** (Desa 2), **lahan tanah tandus `#8B5A2B`** (Desa 3), dan **jalan setapak penghubung**.
- **Catatan Evolusi Project:** Spesifikasi koordinat dan skala dalam sesi ini berasal dari dokumen v3.0 sebelum penyusunan ulang arah arsitektur v4.0 (*Tight-Knit Caldera Archipelago* $1200 \times 1200\text{ m}$ di [[00 - Overview]] dan [[Tasks]]).

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
1. **Konfigurasi Global MCP (`C:\Users\nmast\.gemini\config\mcp_config.json`):**
   - Menambahkan entri server `"blender"` yang menunjuk ke `D:\Tools\mcp4blender-venv\Scripts\mcp-for-blender.exe` (port `9876`).
   - Terverifikasi terhubung live dengan **Blender 5.2.3 LTS** (Addon v1.8, Protocol v15) melalui `get_addon_status` dan `get_scene_info`.
2. **Instalasi Skill Blender di Workspace (`.agents/skills/`):**
   - Mengkloning `https://github.com/EL4CTEO/blender-skills.git` dan mengekstrak 28 folder skill ke `e:\Semester 5\Game\AR Dragon\.agents\skills\` (antara lain `blender-environment-kits`, `blender-stylized-npr`, `blender-hardsurface-modeling`, `blender-asset-qa`, `blender-game-optimization`, `blender-export-pipelines`, `blender-core`, dll.).
3. **Pembuatan & Ekspor Aset Pulau di Blender (`Assets/`):**
   - Membersihkan scene awal Blender (`0 objects`), lalu membangun dan me-*rebuild* model `Island_Root` beserta 3 desa, sungai, jembatan, air terjun (sebelum keputusan penyederhanaan), dan awan.
   - Menjalankan `qa_check.py` pada `Island_Terrain` dengan hasil **`QA Check on Island_Terrain: True`**.
   - Menyimpan file sumber `Assets/pulau_sky_guardian.blend` dan mengekspor `Assets/pulau_sky_guardian.fbx` (orientasi `-Z Forward`, `Y Up`, `apply_unit_scale=True`).
4. **Pembaruan Dokumentasi (`konteks.md` di root saat sesi berlangsung):**
   - Menambahkan **Poin 4: Desain Model Pulau Sederhana (Fokus Elemen Tantangan)** pada Bagian 2 `konteks.md` (menghapus air terjun/pulau satelit, fokus pada sungai, hutan, cerobong asap, dan lahan kering `#8B5A2B`).
5. **Penyusunan Prompt Referensi Gambar 3D:**
   - Menyusun prompt utama dan versi ringkas untuk *image generator* (Midjourney / DALL-E 3 / Stable Diffusion) dengan gaya *isometric low-poly stylized diorama*, tanpa air terjun dan tanpa pulau satelit.

---

## 4. Detail Penting yang Harus Diingat
- **Lokasi & Versi Blender:** `D:\blender\blender.exe` (versi **5.2.3 LTS**); addon `blender_mcp.py` berada di `C:\Users\nmast\AppData\Roaming\Blender Foundation\Blender\5.2\scripts\addons\blender_mcp.py`.
- **Cara Mengaktifkan Koneksi Blender MCP:** Buka Blender $\rightarrow$ tekan `N` di 3D Viewport $\rightarrow$ tab `MCP for Blender` $\rightarrow$ klik `Start MCP Server` (port `9876`).
- **Library Aset Bawaan Addon:** Opsi `Poly Haven`, `Sketchfab`, dan `Poly Pizza` pada panel `MCP for Blender` dalam kondisi nonaktif (*unchecked*) secara default; perlu dicentang manual di sidebar Blender jika ingin memakai `search_assets` / `import_asset`.
- **Koordinat Acuan v3.0 yang Dipakai Saat Pembuatan `pulau_sky_guardian.fbx`:**
  - `Island_Root`: `(0, 0, 0)` | Skala `(1, 1, 1)`
  - `Village_1_Pollution`: `(-35, 2, -10)` (Unity) / `(-35, -10, 2)` (Blender)
  - `Village_2_Wildfire`: `(40, 2, 20)` (Unity) / `(40, 20, 2)` (Blender)
  - `Village_3_Drought`: `(0, 2, 65)` (Unity) / `(0, 65, 2)` (Blender), material tanah kering `Mat_Drought_Ground` (`#8B5A2B`).

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] **Laptop A:** Menghasilkan gambar referensi dari prompt pulau sederhana yang telah dibuat, lalu menyelaraskannya dengan arah baru *Tight-Knit Caldera Archipelago* ($1200 \times 1200\text{ m}$) pada GDD Peta Pulau di [[Tasks]].
- [ ] **Laptop A / Pemodelan Blender:** Membuat ulang (*blockout* & *modeling*) pulau versi simpel sesuai gambar referensi dan koordinat pad desa terbaru pada [[konteks]] v4.0 (tanpa air terjun/dekorasi berlebih).
- [ ] **Laptop B:** Menunggu aset FBX pulau final hasil remodel sebelum masuk ke integrasi Scene Unity (`Sesi Model Pulau` dan `Sesi 4` di [[laptop-b/Tasks]]).

---

**Navigasi Obsidian:** [[00 - Overview]] · [[konteks]] · [[prd]] · [[rencana_desain_ui]] · [[Tasks]] · [[Devlog]]
