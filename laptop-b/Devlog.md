---
title: Laptop B — Devlog (Eksekusi & Pengembangan Program)
tags:
  - devlog
  - laptop-b
owner: Laptop B
updated: 2026-11-13
---

# 🛠️ Devlog — Laptop B

> Catatan kronologis pengembangan kode, debugging, pengujian Unity/Python, dan hasil build dari **Laptop B**. Kembali ke [[00 - Overview]].

---

## 2026-11-13 — Setup Obsidian Brain & Migrasi Catatan Lokal (Laptop B)
- **Aksi:**
  - Menambahkan `brain/` ke `.gitignore` root project dan meng-clone `ARDragon-Brain.git` ke `brain/`.
  - Memindahkan seluruh file `.md` lokal dari root project (`GDD_Pulau.md`, `konteks.md`, `prd.md`, `progress.md`) ke `brain/laptop-b/`.
  - Membuat `AGENTS.md` di root project untuk aturan kolaborasi dual-laptop.

## 2026-11-13 — Inisialisasi Folder Brain Laptop B
- Folder `brain/laptop-b/` dibuat untuk memisahkan log eksekusi kode & implementasi teknis dari catatan desain di `brain/laptop-a/`.
- Baseline yang sudah terverifikasi sebelum sinkronisasi vault:
  - Python CV Dual-Port UDP (`5052` pose CSV, `5053` JPEG PIP) aktif.
  - Sistem terbang 3D Pose Y-T-A dan model naga `Dragon_Model.blend` aktif di Unity 6 (`6000.3.7f1`).
  - Tugas berikutnya menanti eksekusi **Sesi GUI** berdasarkan [[laptop-a/rencana_desain_ui]].
