---
title: "Setup Obsidian Brain Laptop B"
type: session-memory
tags:
  - session-memory
  - laptop-b
  - obsidian-brain
  - git-workflow
date: 2026-11-21
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
---

# Setup Obsidian Brain Laptop B

## 1. Tujuan & Konteks Sesi
Menyiapkan dan menyinkronkan sistem **Obsidian Brain** di workspace **Laptop B** (`AR Dragon`) agar terhubung ke repository terpisah `https://github.com/NabilQastari/ARDragon-Brain.git`, mengisolasi repo utama dari repo `brain/`, memindahkan catatan `.md` lokal ke `brain/laptop-b/`, serta menyederhanakan penamaan file memori sesuai protokol terbaru di [[00 - Overview]].

## 2. Keputusan Desain / Arsitektur / Teknis
- **Isolasi Dual-Repo:** Folder `brain/` di-ignore di `.gitignore` root project utama sehingga commit kode Unity/Python tidak tercampur dengan catatan Obsidian.
- **Anti-Collision Dual-Laptop:** Laptop B hanya menulis di `brain/laptop-b/` dan membaca `brain/laptop-a/` secara *read-only*, sesuai aturan di `AGENTS.md`.
- **Standar Penamaan Memori:** Nama file di `brain/laptop-b/memories/` disederhanakan menjadi 3–6 kata (maks ~50 karakter) tanpa koma atau rincian panjang, dengan `[[wikilinks]]` yang sinkron di `Devlog.md` dan `Tasks.md`.

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **`.gitignore` (root project):** Menambahkan `brain/` di bagian akhir file.
- **Clone Repo Brain:** Meng-clone `https://github.com/NabilQastari/ARDragon-Brain.git` ke `brain/` dan memverifikasi `brain/.gitignore` mengabaikan `.obsidian/`.
- **Migrasi `.md` Lokal ke `brain/laptop-b/`:** Memindahkan 4 file catatan dari root project:
  - `GDD_Pulau.md` → `brain/laptop-b/GDD_Pulau.md`
  - `konteks.md` → `brain/laptop-b/konteks.md`
  - `prd.md` → `brain/laptop-b/prd.md`
  - `progress.md` → `brain/laptop-b/progress.md`
- **`AGENTS.md` (root project):** Membuat file aturan kolaborasi Laptop B termasuk rujukan command `"jadikan sesi ini memori"`.
- **Penyederhanaan Nama File Memori (`brain/laptop-b/memories/`):**
  - `Analisis dan Tekstur Rumah Blend.md`
  - `Mekanik Sky Peck dan Auto-Launch Python.md`
  - `On-Ground Mechanics dan Uji 16 Skenario.md`
  - `Rework CV dan Kontrol Terbang Baru.md`
  - `Setup OmniRoute dan Launcher Hermes Desktop.md`
- **Pembaruan Log & Push Git:** Memperbarui `brain/laptop-b/Tasks.md` dan `brain/laptop-b/Devlog.md`, mengatasi error push 403 awal dengan izin kolaborator `fauziiih`, rebase dari `origin main`, lalu push commit `81975d1` dan `83b3f2c`.

## 4. Detail Penting yang Harus Diingat
- **Remote Brain:** `https://github.com/NabilQastari/ARDragon-Brain.git` (branch `main`).
- **Akun Push Laptop B:** `fauziiih` (membutuhkan akses *Write* / *Collaborator* di repo `ARDragon-Brain`).
- **Pengecualian Pencarian `.md`:** Folder `ARDragonSkyGuardian_CV/venv/` memakai nama `venv` (bukan `.venv`), sehingga pengecualian pencarian `.md` di Windows perlu mengecualikan `venv/` juga.
- **Obsidian Vault:** Buka folder `brain/` di Obsidian lewat **Open folder as vault**.

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] Lanjutkan antrean eksekusi program di [[Tasks]] (Uji Play Mode Webcam, Texturing & Ekspor `rumah.blend`, **Sesi GUI** sesuai [[laptop-a/rencana_desain_ui]], Sesi Model Pulau, Sesi 4, dan Sesi 5).

---
**Navigasi Obsidian:** [[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[konteks]] · [[prd]] · [[progress]] · [[GDD_Pulau]]
