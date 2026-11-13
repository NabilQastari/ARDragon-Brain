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

## 2026-11-13 — Penamaan Ulang Catatan Sesi ke Format Deskriptif
- **Aksi:** Mengganti nama `Memory1.md` menjadi [[Perbaikan Harmonisasi List Asset Excel terhadap Time Schedule dan Prompt Pulau Melayang]] di `brain/laptop-a/memories/` agar seluruh catatan sesi langsung menggambarkan isi pembahasannya tanpa awalan `Memory*`.

## 2026-11-13 — Dokumentasi Sesi List Asset & Jadwal TS ([[Perbaikan Harmonisasi List Asset Excel terhadap Time Schedule dan Prompt Pulau Melayang]])
- **Aksi:** Menyusun catatan memori terstruktur [[Perbaikan Harmonisasi List Asset Excel terhadap Time Schedule dan Prompt Pulau Melayang]] yang mendokumentasikan perbaikan workbook `TS & List Asset.xlsx` (penyelarasan ke Versi 2 yang ramping sesuai TS, penguncian jadwal VFX Particles pekan 10–11, penataan arsip detail aset di sheet pendukung, dan penyusunan prompt Blender MCP untuk model pulau).

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
