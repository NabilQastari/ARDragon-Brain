---
title: Planning Implementasi & Eksekusi Graphify di Laptop B
type: planning
tags:
  - planning
  - graphify
  - obsidian
  - laptop-a
  - laptop-b
  - tooling
  - token-optimization
  - code-graph
date: 2026-11-22
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[planning_optimasi_token_laptop_b]]"
  - "[[kontrak_integrasi_ui_dan_progress]]"
---

# 🕸️ Planning Implementasi & Eksekusi Graphify di Laptop B

> **Tujuan Dokumen:** Memberikan panduan arsitektur, spesifikasi teknis, dan langkah eksekusi konkret bagi **Laptop B** untuk menginstal, mengonfigurasi, dan mengoperasikan **Graphify** (`graphifyy`) yang terhubung langsung dengan **Obsidian Vault (`brain/`)**. Integrasi ini bertujuan menciptakan peta pengetahuan kode (*Code Knowledge Graph*) otomatis berbasis AST untuk C# (Unity) dan Python (Computer Vision), memangkas *re-orientation token tax* AI Agent secara drastis, serta menyediakan visualisasi arsitektur interaktif di Obsidian.

---

## 📌 1. Latar Belakang & Masalah yang Diselesaikan

Di Laptop B, basis kode proyek terbagi menjadi dua pilar besar:
1. **Unity 6 C# Logic (`Assets/_Scripts/`):** Terdiri dari puluhan controller (`DragonFlightController`, `UDPPoseReceiver`, `DragonWingAnimator`, `InGameFlightHUD`, `BootFlowController`, UI managers, config ScriptableObjects, dll.).
2. **Python Computer Vision Engine (`ARDragonSkyGuardian_CV/`):** Terdiri dari pipeline pelacak pose tubuh dan wajah (`active_cv_sender.py`, `motion_recognizer.py`, `player_calibrator.py`, `pose_stabilizer.py`, socket UDP streaming, dll.).

### Titik Kebocoran Token Saat Ini:
* Setiap kali AI Agent di Laptop B memulai sesi baru atau menangani tugas lintas-sistem (misalnya: menghubungkan sinyal pose baru dari Python ke HUD dan animasi sayap Unity), agen harus memindai (*grep*, *glob*, *read_file*) puluhan berkas skrip mentah.
* Proses re-orientasi ini menghabiskan **ribuan hingga belasan ribu token per sesi**, padahal agen hanya membutuhkan peta dependensi dan alur pemanggilan method/class.

### Solusi Graphify + Obsidian:
* **Graphify** menggunakan parser AST lokal (**Tree-sitter**) untuk membedah relasi kode secara instan dan deterministik (100% offline, tanpa kuota LLM tambahan).
* Graphify mengelompokkan kode menggunakan algoritma **Leiden Clustering**, mendeteksi *God Nodes* (titik tumpu sistem), dan mengekspor hasilnya menjadi berkas Markdown kaya tautan wikilink (`[[...]]`) yang langsung terintegrasi dengan **Obsidian Graph View** dan plugin **Juggl** yang sudah terpasang di vault `brain/`.
* Agen AI Laptop B dapat melakukan kueri cerdas (`graphify query`, `graphify path`, `graphify explain`) secara terfokus sebelum menyentuh kode.

---

## 🏛️ 2. Arsitektur Penempatan di Vault `brain/` (Anti-Collision)

Agar sesuai dengan aturan kolaborasi dual-laptop (`AGENTS.md`) dan tidak mengotori catatan konsep desain, seluruh artefak Graphify diisolasi di dalam folder khusus milik Laptop B:

```text
AR Dragon/
├── Assets/_Scripts/                     <-- Target Scan C#
├── ARDragonSkyGuardian_CV/             <-- Target Scan Python
└── brain/
    ├── .obsidian/                       <-- Konfigurasi & Plugin (Juggl, Extended Graph)
    ├── laptop-a/                        <-- Catatan Laptop A (READ ONLY untuk Laptop B)
    └── laptop-b/
        ├── Devlog.md
        ├── Tasks.md
        ├── konteks.md
        └── code-graph/                  <-- [BARU] FOLDER ISOLASI GRAPHIFY
            ├── index.md                 <-- Portal Navigasi Obsidian
            ├── GRAPH_REPORT.md          <-- Laporan Arsitektur & Kluster Modul
            ├── graph.json               <-- Basis Data Graf (untuk CLI Query & MCP)
            └── graph.html               <-- Visualizer Interaktif Lokal (Browser)
```

> [!IMPORTANT]
> **Aturan Anti-Clutter:** Jangan jalankan Graphify di root folder tanpa pembatasan direktori target! Menjalankan Graphify di root `AR Dragon/` akan memindai folder biner raksasa Unity (`Library/`, `Temp/`, `Packages/`, dan ribuan `.meta`), menyebabkan *out of memory* atau ribuan catatan sampah.

---

## 🚀 3. Panduan Eksekusi Langkah demi Langkah (Laptop B)

### Langkah 1: Instalasi Paket Graphify di Environment Python

Buka terminal PowerShell di Laptop B (pastikan berada di environment Python yang aktif):

```powershell
# Instalasi paket resmi via pip (nama paket PyPI: graphifyy)
pip install graphifyy

# Opsi tambahan jika ingin mengaktifkan server MCP terintegrasi:
pip install "graphifyy[mcp]"
```

*Verifikasi instalasi:*
```powershell
graphify --version
```
*(Pastikan perintah `graphify` sudah dapat dipanggil langsung dari terminal).*

---

### Langkah 2: Ekstraksi Graf Pertama Kali (Initial Targeted Scan)

Jalankan pemindaian lokal terarah hanya pada direktori skrip C# dan modul Python. Gunakan flag `--code-only` agar parser berjalan deterministik menggunakan Tree-sitter lokal tanpa memerlukan API key eksternal:

```powershell
# Jalankan dari root workspace: E:\Semester 5\Game\AR Dragon
graphify Assets/_Scripts ARDragonSkyGuardian_CV --code-only --obsidian --out brain/laptop-b/code-graph
```

**Penjelasan Parameter:**
* `Assets/_Scripts ARDragonSkyGuardian_CV`: Dua folder target utama yang berisi logika C# dan Python.
* `--code-only`: Memaksa parser AST lokal (Tree-sitter), mengecualikan pemindaian teks bebas/dokumen non-kode.
* `--obsidian`: Menghasilkan metadata tautan markdown wikilinks (`[[...]]`) yang kompatibel penuh dengan Obsidian.
* `--out brain/laptop-b/code-graph`: Menempatkan seluruh berkas keluaran secara rapi di dalam ruang kerja Laptop B.

---

### Langkah 3: Membuat Portal Navigasi Obsidian (`index.md`)

Setelah berkas `GRAPH_REPORT.md` dan `graph.json` terbentuk di `brain/laptop-b/code-graph/`, buat file portal sederhana `brain/laptop-b/code-graph/index.md` agar mudah diakses dari navigasi utama Obsidian:

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
updated: 2026-11-22
related:
  - "[[00 - Overview]]"
  - "[[laptop-b/konteks]]"
  - "[[laptop-b/Devlog]]"
---

# 🕸️ Fanamir Code Knowledge Graph

> Peta keterkaitan arsitektur kode C# Unity 6 (`Assets/_Scripts/`) dan Python Computer Vision (`ARDragonSkyGuardian_CV/`) yang diekstraksi menggunakan Graphify AST.

## 📑 Dokumen Utama
- [[GRAPH_REPORT]] — Laporan lengkap analisis kluster (Leiden communities), God Nodes, dan peta dependensi.
- [Visualizer Interaktif (Browser)](file:///./graph.html) — Buka berkas visualisasi interaktif di browser lokal.

## 🧭 Visualisasi di Obsidian
Untuk melihat graf visual di Obsidian:
1. Buka berkas `GRAPH_REPORT.md`.
2. Buka tab **Graph View** atau gunakan plugin **Juggl** / **Extended Graph** yang sudah terpasang.
3. Filter pencarian graf dengan: `path:"laptop-b/code-graph"`.
```

---

### Langkah 4: Operasional Alur Kerja AI Agent Laptop B (CLI Query)

Mulai saat ini, AI Agent di Laptop B tidak perlu lagi membaca puluhan file skrip secara manual untuk mencari relasi kode. Agen dapat memanfaatkan perintah CLI Graphify:

1. **Mencari Jalur Hubungan Antar-Dua Skrip/Komponen:**
   *Contoh kasus:* Ingin tahu bagaimana `UDPPoseReceiver` terhubung ke pergerakan sayap `DragonWingAnimator`:
   ```powershell
   graphify path "UDPPoseReceiver" "DragonWingAnimator" --graph brain/laptop-b/code-graph/graph.json
   ```

2. **Meminta Penjelasan Struktur Komponen Sentral (God Node):**
   *Contoh kasus:* Memahami dependensi `DragonFlightController`:
   ```powershell
   graphify explain "DragonFlightController" --graph brain/laptop-b/code-graph/graph.json
   ```

3. **Kueri Pertanyaan Struktural Cepat:**
   ```powershell
   graphify query "aliran data body_pitch dari UDP hingga rotasi naga" --graph brain/laptop-b/code-graph/graph.json
   ```

> [!TIP]
> **Penghematan Token:** Menjalankan `graphify path` atau `graphify explain` hanya mengembalikan teks ringkas (~50–150 token), dibandingkan membuka file `DragonFlightController.cs` (700+ baris) dan `UDPPoseReceiver.cs` (500+ baris) yang menghabiskan lebih dari **4.000 token**.

---

### Langkah 5: Protokol Pembaruan Graf (Maintenance Lifecycle)

Kapan Laptop B harus memperbarui graf kode?
* **Tidak Perlu Setiap Commit Kecil:** Jangan jalankan pembaruan graf untuk perubahan 1–2 baris kode/perbaikan bug minor.
* **Jalankan Pasca-Refaktor / Penambahan Fitur Besar:** Jalankan pembaruan setelah menyelesaikan sesi besar (misal: Sesi 4 *VillageZoneController*, Sesi 5 *GameManager*):
  ```powershell
  # Pembaruan inkremental (hanya file yang berubah):
  graphify Assets/_Scripts ARDragonSkyGuardian_CV --update --code-only --out brain/laptop-b/code-graph
  ```

---

## 🛡️ 4. Penyelarasan dengan Aturan Efisiensi Token (`AGENTS.md`)

Pastikan aturan di `AGENTS.md` Laptop B tetap dipatuhi:
1. **Gunakan `rtk` untuk Membungkus Perintah Git/Pytest:**
   Saat menambahkan folder baru ke git, gunakan:
   ```powershell
   rtk git -C brain add laptop-b/code-graph/
   rtk git -C brain commit -m "feat(code-graph): setup initial graphify knowledge graph"
   ```
2. **Pengecualian Berkas Besar:**
   Jika berkas `graph.html` terlalu besar (>5 MB) dan tidak perlu di-sync ke remote Git, berkas tersebut dapat ditambahkan ke `brain/.gitignore` lokal:
   ```gitignore
   # Opsional: abaikan visualizer HTML jika terlalu berat
   laptop-b/code-graph/graph.html
   ```
   Tetap simpan `graph.json` dan `GRAPH_REPORT.md` di Git karena keduanya adalah basis data esensial bagi AI Agent dan Obsidian.

---

## ✅ 5. Checklist Verifikasi Sukses (Laptop B)

- [ ] Paket `graphifyy` terinstal dan `graphify --version` berhasil dieksekusi di terminal.
- [ ] Perintah pemindaian terarah berhasil dijalankan tanpa error Tree-sitter.
- [ ] Folder `brain/laptop-b/code-graph/` berisi `graph.json`, `GRAPH_REPORT.md`, dan `graph.html`.
- [ ] Berkas `index.md` dibuat dan dapat diklik melalui Obsidian.
- [ ] Graph View / Juggl di Obsidian mampu merender simpul dan edge antar-skrip C# dan Python.
- [ ] Tes perintah CLI `graphify explain "DragonFlightController"` menghasilkan output struktur yang akurat.
- [ ] `brain/laptop-b/Devlog.md` dan `Tasks.md` diperbarui mencatat setup Graphify, lalu di-commit & push ke repo `brain`.

---

**Navigasi Dokumen Terkait:**  
[[00 - Overview]] · [[planning_optimasi_token_laptop_b]] · [[kontrak_integrasi_ui_dan_progress]] · [[laptop-b/Devlog]] · [[laptop-b/Tasks]]
