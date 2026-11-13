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

## 2026-11-13 — Setup Obsidian Brain & Migrasi Dokumen Desain
- **Aksi:** Menginisialisasi Obsidian vault terpisah di `brain/` yang terhubung ke remote `ARDragon-Brain` dan terisolasi dari repo utama melalui `.gitignore`.
- **Migrasi:** Memindahkan seluruh dokumen desain dan prompt dari root ke `brain/laptop-a/` dengan format YAML frontmatter & `[[wikilinks]]`:
  - [[konteks]]
  - [[prd]]
  - [[gdd_peta_pulau]]
  - [[rencana_desain_ui]]
  - [[prompt_naga]]
  - [[prompt_v2]]
- **Aturan Kerja:** Menetapkan pemisahan tulis (*anti-collision*) antara `laptop-a/` (desain & arsitektur) dan `laptop-b/` (eksekusi & pengembangan program) di `AGENTS.md`.
- **Status Project Saat Ini:** Fokus aktif roadmap berada pada **Sesi GUI** (implementasi di Unity sesuai [[rencana_desain_ui]]), disusul **Sesi Model Pulau** (remodel kepulauan 3 biome).
