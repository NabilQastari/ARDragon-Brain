---
title: Prompt Eksekusi & Instalasi Graphify di Laptop B
tags:
  - prompt
  - laptop-b
  - graphify
  - obsidian
  - tooling
  - code-graph
  - token-optimization
owner: Laptop A
updated: 2026-11-23
status: ready-for-execution
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[planning_graphify_laptop_b]]"
  - "[[planning_optimasi_token_laptop_b]]"
---

# 🚀 Prompt Eksekusi & Instalasi Graphify untuk Laptop B

> **Petunjuk untuk Pengguna:** Salin (*copy*) seluruh teks di dalam kotak **"MASTER PROMPT EKSEKUSI GRAPHIFY UNTUK LAPTOP B"** di bawah, lalu kirimkan langsung ke AI Agent di **Laptop B** setelah menjalankan `git -C brain pull`.

---

## 📋 Ikhtisar Arsitektur & Target Eksekusi (Laptop B)

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│                    EKSEKUSI GRAPHIFY + OBSIDIAN (LAPTOP B)                   │
├──────────────────────────┬──────────────────────────┬───────────────────────┤
│ 1. TARGET SCAN MINIMALIS │ 2. ISOLASI OUTPUT        │ 3. QUERY & VISUALISASI│
│ • Assets/_Scripts/ (C#)  │ • brain/laptop-b/        │ • CLI explain & path  │
│ • ARDragonSkyGuardian_CV/│   code-graph/            │ • Obsidian Juggl View │
│   (Python CV)            │ • graph.json & report    │ • Token tax drop 90%  │
└──────────────────────────┴──────────────────────────┴───────────────────────┘
```

1. **Instalasi Paket:** Menginstal paket resmi `graphifyy` di environment Python Laptop B.
2. **Ekstraksi AST Terarah:** Memindai logika C# (`Assets/_Scripts/`) dan Python (`ARDragonSkyGuardian_CV/`) tanpa menyentuh folder biner Unity (`Library/`, `Temp/`, `.meta`).
3. **Penyimpanan Terisolasi:** Menyimpan hasil di `brain/laptop-b/code-graph/` sesuai aturan Anti-Collision.
4. **Verifikasi Kueri & Navigasi:** Memastikan kueri CLI berjalan dan portal `index.md` dapat diakses dari Obsidian.

---

## 📋 MASTER PROMPT EKSEKUSI GRAPHIFY UNTUK LAPTOP B (SIAP SALIN)

```markdown
Halo Laptop B! Sesuai arsitektur dan perencanaan terbaru dari Laptop A, kita akan memasang dan mengintegrasikan **Graphify** (`graphifyy`) dengan **Obsidian Vault (`brain/`)** di workspace Laptop B.

Tujuannya adalah memetakan seluruh relasi kode C# Unity dan Python Computer Vision ke dalam knowledge graph berbasis AST (Tree-sitter lokal), memangkas *re-orientation token tax* AI Agent hingga lebih dari 80%, serta memberikan visualisasi graf arsitektur hidup di Obsidian.

Rujuk dokumen perencanaan lengkap:
- `brain/laptop-a/handoff-laptop-b/planning_graphify_laptop_b.md`
- `brain/laptop-a/handoff-laptop-b/planning_optimasi_token_laptop_b.md`
- `brain/00 - Overview.md`

Tolong jalankan langkah-langkah implementasi berikut secara berurutan:

---

### Langkah 1: Instalasi Paket `graphifyy` di Laptop B
Buka terminal PowerShell pada root workspace (`AR Dragon`) dan jalankan:
```powershell
# Instalasi paket resmi via pip
pip install graphifyy

# (Opsional jika ingin mendukung query via Model Context Protocol):
pip install "graphifyy[mcp]"

# Verifikasi instalasi CLI
graphify --version
```
*Pastikan perintah `graphify` dapat dipanggil normal tanpa error.*

---

### Langkah 2: Ekstraksi Graf Terarah (Targeted Scan)
Jalankan pemindaian AST deterministik (100% lokal via Tree-sitter, tanpa LLM API) hanya pada skrip C# dan modul Python. Arahkan output ke subfolder khusus `brain/laptop-b/code-graph/`:
```powershell
# Buat folder penampung jika belum ada
New-Item -ItemType Directory -Force -Path "brain/laptop-b/code-graph"

# Jalankan ekstraksi AST dengan flag --code-only dan format Obsidian
graphify Assets/_Scripts ARDragonSkyGuardian_CV --code-only --obsidian --out brain/laptop-b/code-graph
```

> **PERINGATAN:** Jangan jalankan `graphify .` pada root workspace karena akan memindai folder biner raksasa Unity (`Library/`, `Temp/`, `Packages/`)!

---

### Langkah 3: Buat Portal Navigasi Obsidian (`index.md`)
Buat file `brain/laptop-b/code-graph/index.md` dengan isi sebagai berikut:
```markdown
---
title: Fanamir Code Knowledge Graph
tags:
  - code-graph
  - architecture
  - laptop-b
  - unity
  - python-cv
owner: Laptop B
updated: 2026-11-23
related:
  - "[[00 - Overview]]"
  - "[[laptop-b/konteks]]"
  - "[[laptop-b/Devlog]]"
---

# 🕸️ Fanamir Code Knowledge Graph

> Peta arsitektur dan relasi kode C# Unity 6 (`Assets/_Scripts/`) dan Python CV (`ARDragonSkyGuardian_CV/`) yang diekstrak otomatis via Graphify AST.

## 📑 Dokumen Utama
- [[GRAPH_REPORT]] — Laporan analisis kluster komunitas (Leiden clustering), God Nodes, dan dependensi kode.
- [Visualizer Interaktif Lokal (HTML)](file:///./graph.html) — Pratinjau graf visual di browser.

## 🧭 Panduan Navigasi di Obsidian
1. Buka berkas `GRAPH_REPORT.md` di Obsidian.
2. Buka tab **Graph View** atau plugin **Juggl** / **Extended Graph** yang sudah aktif.
3. Filter pencarian graf dengan: `path:"laptop-b/code-graph"`.
```

---

### Langkah 4: Uji Coba Kueri CLI (Verifikasi AI Workflow)
Lakukan sanity check kueri CLI untuk membuktikan agen AI dapat mencari relasi kode tanpa membaca file mentah ratusan baris:
```powershell
# 1. Cek penjelasan struktur komponen sentral (God Node)
graphify explain "DragonFlightController" --graph brain/laptop-b/code-graph/graph.json

# 2. Cek alur dependensi antara UDP receiver dan penggerak sayap
graphify path "UDPPoseReceiver" "DragonWingAnimator" --graph brain/laptop-b/code-graph/graph.json
```
*Pastikan kueri mengembalikan teks ringkasan simpul dan koneksi.*

---

### Langkah 5: Penyelarasan Git & Devlog Laptop B
1. Periksa ukuran berkas `brain/laptop-b/code-graph/graph.html`. Jika ukurannya sangat besar (>5 MB), tambahkan ke `brain/.gitignore` lokal:
   ```text
   laptop-b/code-graph/graph.html
   ```
   *(File `graph.json`, `GRAPH_REPORT.md`, dan `index.md` wajib di-commit ke git)*.
2. Perbarui `brain/laptop-b/Devlog.md` dan centang tugas di `brain/laptop-b/Tasks.md`.
3. Buat catatan memori sesi di `brain/laptop-b/memories/Setup Graphify Code Graph Laptop B.md`.
4. Stage, commit, dan push perubahan di folder `brain/`:
   ```powershell
   rtk git -C brain add laptop-b/
   rtk git -C brain commit -m "feat(laptop-b): setup and extract graphify code knowledge graph"
   rtk git -C brain push origin main
   ```

Laporkan hasil instalasi, daftar file yang terbentuk di `code-graph/`, dan cuplikan output uji kueri `graphify explain` / `graphify path`. Terima kasih!
```
