---
title: AR Dragon Sky Guardian — Project Overview
tags:
  - overview
  - ar-dragon
  - game-design
  - architecture
status: active
updated: 2026-11-13
---

# 🐉 AR Dragon: Sky Guardian — Overview

> **Visi Utama:** Game *PC Exergaming* 3D semi-open-world berbasis Computer Vision (webcam standar tanpa sensor khusus) di mana pemain mengendalikan Naga Penjaga Langit (*Ancient Dew Water Dragon*) untuk menjelajahi kepulauan dan memulihkan 3 desa dari bencana lingkungan melalui gestur tubuh interaktif.

---

## 🎯 Ringkasan Teknis & Stack

- **Engine Game:** Unity 6 (`6000.3.7f1`) — Universal Render Pipeline (URP), namespace C# `DragonWingAR.*`.
- **Computer Vision:** Python 3.10+ (`ARDragonSkyGuardian_CV/`) dengan PyTorch CUDA, `YOLOv8n-pose`, One-Euro Filter, dan head-crop mouth tracker.
- **Komunikasi Dual-Port UDP:**
  - `127.0.0.1:5052` — Telemetri pose tubuh 13-float CSV ($\ge 30\text{ FPS}$).
  - `127.0.0.1:5053` — Streaming video webcam PIP JPEG ($320 \times 180$).
- **Pipeline Aset 3D:** Blender 5.1 / 5.2 CLI + MCP (`Dragon_Model.blend`, remodel pulau 3 biome skala $1200 \times 1200\text{ m}$).

---

## 🗺️ Core Gameplay & Misi

1. **Navigasi Penerbangan 3D (Gestur Alfabet Y-T-A):**
   - **Pose Y:** Naik menanjak ($\uparrow$).
   - **Pose T:** Meluncur lurus mendatar ($\rightarrow$).
   - **Pose A:** Menukik tajam ($\downarrow$ + *Dive Boost*).
   - **Kemiringan Bahu/Tangan:** Belok kanan/kiri (*Yaw Turn* & *Roll Banking*).
2. **Pemulihan 3 Desa (Auto-Hover + Gestur Khusus):**
   - **Desa 1 (Smog Valley — Polusi Udara):** Kepakkan kedua tangan (*Flapping*).
   - **Desa 2 (Wildfire Woods — Kebakaran Hutan):** Buka mulut lebar atau tangkupkan tangan di dada (*Water Breath*).
   - **Desa 3 (Drought Plateau — Kekeringan Tanah):** Angkat kedua tangan tinggi di atas kepala (*Dew Rain*).
3. **Sistem Level:** Level 1 (90s), Level 2 (150s), Level 3 (210s) dengan evaluasi 1–3 bintang.

---

## 🧠 Peta Pengetahuan Vault (`brain/`)

### Dokumen Desain & Arsitektur ([[laptop-a/Tasks|Laptop A]])
- [[laptop-a/konteks]] — *Single Source of Truth* arsitektur teknis Unity, UDP, dan gestur.
- [[laptop-a/prd]] — *Product Requirements Document* & roadmap sesi pengembangan.
- [[laptop-a/rencana_desain_ui]] — Spesifikasi UI Lobby, Kalibrasi, HUD, & Finish Modal (*Clean Minimalist 2D Vector Cartoon Fantasy*).
- [[laptop-a/Tasks]] & [[laptop-a/Devlog]] — Pelacakan tugas desain/arsitektur & catatan perkembangan Laptop A.

### Eksekusi & Pengembangan Program ([[laptop-b/Tasks|Laptop B]])
- [[laptop-b/Tasks]] & [[laptop-b/Devlog]] — Pelacakan tugas implementasi kode, integrasi Unity/Python, & log pengujian Laptop B.

---

## 🤝 Aturan Kolaborasi Dual-Laptop
- **Laptop A (`brain/laptop-a/`):** Fokus desain, ide, GDD, UI/UX, aset konsep, dan arsitektur.
- **Laptop B (`brain/laptop-b/`):** Fokus eksekusi kode Unity C#, Python CV, build, dan debugging.
- Setiap laptop hanya menulis di foldernya masing-masing dan membaca folder rekan secara *read-only* untuk sinkronisasi konteks.

---

## ⚡ Command Khusus Bersama: `"jadikan sesi ini memori"`

Jika pengguna mengetik **`jadikan sesi ini memori`** di sesi chat mana pun (baik di **Laptop A** maupun **Laptop B**), AI Agent **WAJIB** menjalankan alur berikut:

1. **Tentukan Identitas & Folder Target:**
   - Cek `AGENTS.md` di root project untuk menentukan identitas laptop aktif:
     - **Laptop A** $\rightarrow$ tulis hanya di `brain/laptop-a/` (`owner: Laptop A`, tag `laptop-a`).
     - **Laptop B** $\rightarrow$ tulis hanya di `brain/laptop-b/` (`owner: Laptop B`, tag `laptop-b`).
   - **Dilarang keras** mengedit atau menghapus file di folder laptop lain.
2. **Sinkronisasi Awal:** Jalankan `git -C brain pull` (jika ada koneksi internet).
3. **Penamaan File Deskriptif:**
   - Simpan di `brain/<laptop-aktif>/memories/<Nama Deskriptif Sesi>.md` (buat folder `memories/` jika belum ada).
   - Gunakan nama file yang **spesifik dan langsung menggambarkan seluruh pembahasan/pekerjaan di sesi tersebut** (boleh panjang, tanpa awalan `Memory*`, bebas karakter terlarang Windows `\ / : * ? " < > |`, dan tidak menimpa file yang sudah ada).
4. **Format Catatan Obsidian:**
   - Gunakan **YAML frontmatter** (`title`, `type: session-memory`, `tags`, `date`, `owner`, `related`) dan `[[wikilinks]]`.
   - Susun isi ke dalam 5 bagian wajib berdasarkan fakta yang benar-benar terjadi di sesi tersebut (tanpa mengarang):
     1. `## 1. Tujuan & Konteks Sesi`
     2. `## 2. Keputusan Desain / Arsitektur / Teknis`
     3. `## 3. Ringkasan Eksekusi & Perubahan File/Aset`
     4. `## 4. Detail Penting yang Harus Diingat` (angka, koordinat, aturan, nama simbol/skrip, batasan, preferensi user)
     5. `## 5. Tugas Tersisa & Langkah Selanjutnya`
   - Sertakan footer navigasi `[[00 - Overview]]`, `[[Tasks]]`, `[[Devlog]]`, serta catatan terkait di folder laptop masing-masing.
5. **Update Log & Push Git:**
   - Tambahkan entri ringkas di `brain/<laptop-aktif>/Devlog.md` yang menautkan `[[<Nama Deskriptif Sesi>]]`, serta perbarui `brain/<laptop-aktif>/Tasks.md` bila ada tugas yang selesai atau baru muncul.
   - Jalankan `git -C brain add`, `commit` dengan format `docs(<laptop-aktif>): add session note - <topik singkat>`, dan `push` ke `origin main`, lalu laporkan nama file yang dibuat serta status push Git-nya.
