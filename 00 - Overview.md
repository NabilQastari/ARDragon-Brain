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
