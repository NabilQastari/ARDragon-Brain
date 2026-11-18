---
title: "Panduan Setup Brain Antigravity Obsidian"
type: session-memory
tags:
  - session-memory
  - laptop-a
  - obsidian
  - brain
  - antigravity
  - workflow
  - documentation
date: 2026-11-18
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
---

# Panduan Setup Brain Antigravity Obsidian

## 1. Tujuan & Konteks Sesi
- Menyusun panduan menyeluruh dan mendalam mengenai arsitektur sistem **Brain Antigravity + Obsidian** berdasarkan pengalaman riil proyek AR Dragon / Fanamir.
- Mengemas panduan ke dalam file template mandiri bernama [`prompt-setup-brain.md`](file:///e:/Semester%205/Game/AR%20Dragon/prompt-setup-brain.md) di root workspace.
- Memastikan dokumen tersebut dapat langsung di-copy-paste ke sesi chat Antigravity di proyek baru mana pun agar AI Agent di proyek tersebut bertindak sebagai *interactive setup wizard* yang memandu pengguna hingga sistem Brain siap 100%.
- Mengonfirmasi fleksibilitas arsitektur Brain sebagai solusi universal yang kompatibel untuk proyek game, web/mobile app, AI/ML research, penulisan tesis/buku, maupun solo developer.

## 2. Keputusan Desain / Arsitektur / Teknis
- **Pemisahan Sub-Repo Git Mandiri (`brain/`):**
  - Folder `brain/` berada di dalam root workspace, namun dikelola sebagai Git repository independen terpisah dari repositori kode/aset utama.
  - File `.gitignore` di root proyek utama wajib menambahkan baris `brain/` agar commit dokumentasi tidak mengotori riwayat commit kode.
  - File `brain/.gitignore` wajib menambahkan `.obsidian/` agar konfigurasi lokal, workspace layout, dan cache personal pengguna tidak memicu merge conflict antar-perangkat.
- **Arsitektur Anti-Collision Multi-Device (0% Merge Conflict):**
  - Pembagian folder eksklusif per perangkat (misal: `brain/laptop-a/` dan `brain/laptop-b/`, atau `brain/core/` untuk single device).
  - Setiap perangkat hanya boleh menulis di foldernya sendiri dan membaca folder rekan secara *read-only*.
  - File root bersama seperti `00 - Overview.md` hanya dimodifikasi pada saat pergeseran besar arsitektur atau konsensus bersama.
- **Otomasi SOP via `AGENTS.md`:**
  - File `AGENTS.md` di root workspace mendikte perilaku otomatis Antigravity:
    - *Startup Hook:* Wajib menjalankan `git -C brain pull`, lalu membaca `00 - Overview.md` serta folder aktif dan folder partner.
    - *Handover Hook:* Wajib memperbarui `Tasks.md` dan `Devlog.md` di akhir tugas.
    - *Memory Trigger:* Menjalankan protokol khusus saat pengguna mengetik `"jadikan sesi ini memori"`.
- **Standarisasi Format Memori Sesi:**
  - Format wajib 5 bagian: (1) Tujuan & Konteks Sesi, (2) Keputusan Desain/Arsitektur/Teknis, (3) Ringkasan Eksekusi & Perubahan File/Aset, (4) Detail Penting yang Harus Diingat, dan (5) Tugas Tersisa & Langkah Selanjutnya.
  - Dilengkapi YAML frontmatter dan `[[wikilinks]]` untuk terintegrasi dengan graf pengetahuan Obsidian.
- **Universalitas Domain:**
  - Sistem tidak bergantung pada game engine atau tech stack tertentu karena menggunakan format terbuka Markdown murni (`.md`) dan Git standar.

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Pembuatan File Panduan & Prompt Wizard:**
  - Membuat [`prompt-setup-brain.md`](file:///e:/Semester%205/Game/AR%20Dragon/prompt-setup-brain.md) di root workspace dengan instruksi pembuka `<SYSTEM_ONBOARDING_INSTRUCTION>`.
  - Berisi penjelasan arsitektur lengkap, alasan efektivitas, panduan instalasi Obsidian, pembuatan private Git repo, topologi device, inisialisasi Git sub-repo, template file starter (`00 - Overview.md`, `Tasks.md`, `Devlog.md`, `konteks.md`, `prd.md`), template `AGENTS.md` (Laptop A, Laptop B, Single Device), setting Obsidian, protokol `"jadikan sesi ini memori"`, hingga alur onboarding device kedua.
- **Pembaruan Dokumen Brain Laptop A:**
  - Memperbarui [[Tasks]] dengan menandai selesainya penyusunan panduan setup Brain dan prompt onboarding.
  - Memperbarui [[Devlog]] dengan catatan kronologis penciptaan `prompt-setup-brain.md` dan verifikasi integrasi Git sub-repo.

## 4. Detail Penting yang Harus Diingat
- **Perintah Git Sub-Repo:** Semua perintah git untuk vault harus menggunakan flag `-C brain`, contoh: `git -C brain pull`, `git -C brain add .`, `git -C brain commit -m "..."`, dan `git -C brain push origin main`.
- **Konvensi Commit:** Format pesan commit memori dan devlog menggunakan konvensi `docs(<device-aktif>): add session note - <topik singkat>`.
- **Aturan Nama File Memori:** Ringkas (3–6 kata, maks ~50 karakter), tanpa awalan `Memory*`, tanpa karakter terlarang Windows `\ / : * ? " < > |`, dan tidak menimpa file yang sudah ada.
- **Pemisahan Cache Obsidian:** Folder `.obsidian/` tidak boleh di-track oleh Git agar plugin dan pengaturan tampilan lokal tidak bentrok saat sinkronisasi silang.

## 5. Tugas Tersisa & Langkah Selanjutnya
- Menyimpan file `prompt-setup-brain.md` sebagai template portabel yang siap dipakai untuk inisialisasi proyek baru kapan saja.
- Melanjutkan fokus aktif Laptop A terkait review implementasi dinamis kamera dan penyesuaian kontrol titik wajah oleh Laptop B untuk persiapan Sesi 4.

---
Kembali ke: [[00 - Overview]] | [[Tasks]] | [[Devlog]]
