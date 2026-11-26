---
title: Perencanaan dan Master Prompt Graphify Laptop B
type: session-memory
tags:
  - session-memory
  - laptop-a
  - laptop-b
  - graphify
  - obsidian
  - tooling
  - code-graph
  - token-optimization
date: 2026-11-26
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[planning_graphify_laptop_b]]"
  - "[[prompt_eksekusi_graphify_laptop_b]]"
  - "[[planning_optimasi_token_laptop_b]]"
---

# 🧠 Perencanaan dan Master Prompt Graphify Laptop B

> **Ringkasan Eksekutif:** Sesi ini berfokus pada diskusi arsitektur, penyusunan cetak biru perencanaan, serta pembuatan master prompt instruksi siap pakai untuk menginstal dan mengintegrasikan **Graphify** (`graphifyy`) dengan **Obsidian Vault (`brain/`)** di lingkungan kerja **Laptop B**. Integrasi ini bertujuan memetakan kode C# Unity 6 (`Assets/_Scripts/`) dan modul Python CV (`ARDragonSkyGuardian_CV/`) menggunakan parser AST lokal (Tree-sitter), memangkas *re-orientation token tax* AI Agent Laptop B hingga lebih dari 80%, serta menyediakan navigasi graf visual hidup di Obsidian.

---

## 1. Tujuan & Konteks Sesi

- **Konteks Masalah di Laptop B:**
  - AI Agent di Laptop B sering kali harus memindai (*grep*, *glob*, atau membaca ratusan baris skrip) setiap kali berganti sesi atau mengeksekusi integrasi lintas modul (misal: aliran data `body_pitch` dari Python UDP 5052 ke `UDPPoseReceiver.cs`, `DefaultFlightInputMapper.cs`, `DragonFlightController.cs`, dan `InGameFlightHUD.cs`).
  - Pemindaian mentah berulang ini membuang ribuan hingga belasan ribu token per sesi (*re-orientation tax*) dan membebani riwayat percakapan.
- **Target Utama Sesi:**
  1. Mendiskusikan kelayakan, manfaat, batasan, dan arsitektur integrasi **Graphify** (`graphifyy`) dengan Obsidian Vault `brain/` khusus untuk kebutuhan Laptop B.
  2. Menyusun cetak biru perencanaan resmi di `brain/laptop-a/handoff-laptop-b/planning_graphify_laptop_b.md`.
  3. Menerbitkan master prompt instruksi langkah demi langkah siap salin bagi agen Laptop B di `brain/laptop-a/handoff-laptop-b/prompt_eksekusi_graphify_laptop_b.md`.
  4. Menyelaraskan aturan penempatan berkas dengan prinsip anti-collision dual-laptop dan isolasi anti-clutter.

---

## 2. Keputusan Desain / Arsitektur / Teknis

### A. Parser AST Lokal & Offline (Tree-sitter)
- **Keputusan:** Menggunakan perintah `graphify` dengan flag `--code-only`.
- **Rasional:** Memanfaatkan parser AST Tree-sitter lokal (mendukung C# dan Python) yang 100% deterministik dan berjalan tanpa memerlukan panggilan API LLM eksternal. Hal ini menjamin proses ekstraksi berjalan instan (hitungan detik) dan bebas biaya token LLM tambahan.

### B. Isolasi Output di Vault `brain/` (Anti-Collision & Zero-Clutter)
- **Keputusan:** Seluruh keluaran Graphify Laptop B wajib ditempatkan di subfolder terisolasi: `brain/laptop-b/code-graph/`.
- **Rasional:**
  - Mematuhi aturan `AGENTS.md` (Laptop A hanya menulis di `laptop-a/`, Laptop B hanya menulis di `laptop-b/`).
  - Mencegah ledakan file (*file clutter*) di root vault `brain/` agar sinkronisasi Git dual-laptop tetap ringan dan cepat.

### C. Pembatasan Target Pemindaian (Scan Scope Guardrail)
- **Keputusan:** Pemindaian secara eksplisit **HANYA** menargetkan dua folder kode sumber:
  1. `Assets/_Scripts/` (logika game C# Unity)
  2. `ARDragonSkyGuardian_CV/` (pipeline pelacakan pose & UDP Python)
- **Rasional:** Dilarang keras menjalankan `graphify .` pada root workspace karena akan memindai folder biner raksasa Unity (`Library/`, `Temp/`, `Packages/`, `.vs/`) dan ribuan berkas `.meta`, yang dapat menyebabkan *memory crash* atau ribuan catatan sampah.

### D. Pemanfaatan Plugin Obsidian Eksisting
- **Keputusan:** Memanfaatkan plugin **Juggl** dan **Extended Graph** yang sudah terpasang di `brain/.obsidian/plugins/` untuk visualisasi interaktif di Obsidian dengan memfilter path: `path:"laptop-b/code-graph"`.

### E. Alur Kueri Terfokus untuk AI Agent Laptop B
- **Keputusan:** AI Agent Laptop B diarahkan menggunakan CLI kueri terfokus sebelum menyentuh file mentah:
  - `graphify explain "<NodeName>"` (~50–100 token output) daripada membaca file controller 700+ baris (~4.000 token).
  - `graphify path "<NodeA>" "<NodeB>"` untuk melacak keterkaitan antar-komponen.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset

1. **Penyusunan Cetak Biru Perencanaan ([[planning_graphify_laptop_b]]):**
   - Berkas: [`brain/laptop-a/handoff-laptop-b/planning_graphify_laptop_b.md`](file:///e:/Semester%205/Game/AR%20Dragon/brain/laptop-a/handoff-laptop-b/planning_graphify_laptop_b.md).
   - Berisi latar belakang, arsitektur isolasi direktori, panduan instalasi CLI, strategi kueri AI Agent, protokol pemeliharaan graf pasca-refaktor, dan checklist verifikasi sukses.
2. **Penerbitan Master Prompt Eksekusi Siap Salin ([[prompt_eksekusi_graphify_laptop_b]]):**
   - Berkas: [`brain/laptop-a/handoff-laptop-b/prompt_eksekusi_graphify_laptop_b.md`](file:///e:/Semester%205/Game/AR%20Dragon/brain/laptop-a/handoff-laptop-b/prompt_eksekusi_graphify_laptop_b.md).
   - Berisi blok teks terstruktur 5 langkah yang siap disalin oleh pengguna ke terminal/chat AI Agent di Laptop B (instalasi `graphifyy`, pemindaian terarah, pembuatan `index.md`, pengujian kueri CLI, dan sinkronisasi Git via `rtk`).
3. **Penyelarasan Indeks & Log Vault:**
   - Memperbarui [`brain/00 - Overview.md`](file:///e:/Semester%205/Game/AR%20Dragon/brain/00%20-%20Overview.md#L98) dengan mendaftarkan dokumen perencanaan dan master prompt baru.
   - Memperbarui [`brain/laptop-a/Tasks.md`](file:///e:/Semester%205/Game/AR%20Dragon/brain/laptop-a/Tasks.md) mencatat penyelesaian perancangan Graphify.
   - Memperbarui [`brain/laptop-a/Devlog.md`](file:///e:/Semester%205/Game/AR%20Dragon/brain/laptop-a/Devlog.md) dengan entri kronologis implementasi.

---

## 4. Detail Penting yang Harus Diingat

- **Nama Paket PyPI:** Nama paket di PyPI adalah **`graphifyy`** (dua huruf 'y'), sedangkan perintah terminal yang dipanggil adalah **`graphify`**.
- **Opsi Ekstra MCP:** Dapat diinstal via `pip install "graphifyy[mcp]"` untuk mendukung kueri via Model Context Protocol.
- **Perintah Ekstraksi Standar:**
  ```powershell
  graphify Assets/_Scripts ARDragonSkyGuardian_CV --code-only --obsidian --out brain/laptop-b/code-graph
  ```
- **Pengecualian File Besar di Git:** Jika berkas visualizer `brain/laptop-b/code-graph/graph.html` berukuran >5 MB, disarankan ditambahkan ke `brain/.gitignore` lokal agar tidak membebani repository Git, sementara `graph.json`, `GRAPH_REPORT.md`, dan `index.md` tetap di-commit.
- **Integrasi dengan RTK di Laptop B:** Setiap operasi Git di Laptop B wajib diawali dengan `rtk` (misal: `rtk git -C brain add laptop-b/`) sesuai aturan efisiensi token di `AGENTS.md`.

---

## 5. Tugas Tersisa & Langkah Selanjutnya

- [ ] **Eksekusi di Laptop B:** Pengguna menyalin master prompt dari `prompt_eksekusi_graphify_laptop_b.md` ke AI Agent Laptop B setelah menjalankan `git -C brain pull`.
- [ ] **Verifikasi Output di Laptop B:** Memastikan file `graph.json`, `GRAPH_REPORT.md`, dan `index.md` terbentuk di `brain/laptop-b/code-graph/`.
- [ ] **Pengujian Kueri AI:** Menguji perintah `graphify explain "DragonFlightController"` dan `graphify path "UDPPoseReceiver" "DragonWingAnimator"` di terminal Laptop B.
- [ ] **Verifikasi Obsidian:** Membuka Graph View / Juggl di Obsidian Laptop B untuk mengecek visualisasi relasi skrip C# dan Python.
- [ ] **Lanjut ke Agenda Desain Laptop A:** Melanjutkan penyusunan spesifikasi paska-animasi (Pilar 2 Animasi UI / Juice & Pilar 3 Mekanisme Kontrol) serta penyelesaian bab pengujian Laporan Capstone.

---

**Navigasi Dokumen Terkait:**  
[[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[planning_graphify_laptop_b]] · [[prompt_eksekusi_graphify_laptop_b]] · [[planning_optimasi_token_laptop_b]]
