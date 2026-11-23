---
title: Setup Graphify Code Graph Laptop B
type: session-memory
tags:
  - session-memory
  - graphify
  - code-graph
  - obsidian
  - laptop-b
  - token-optimization
date: 2026-11-24
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[laptop-b/code-graph/index]]"
  - "[[laptop-a/handoff-laptop-b/planning_graphify_laptop_b]]"
---

# 🕸️ Setup Graphify Code Graph Laptop B

## 1. Tujuan & Konteks Sesi
Memasang dan mengintegrasikan **Graphify** (`graphifyy`) dengan **Obsidian Vault (`brain/`)** di workspace **Laptop B** untuk memetakan relasi kode C# Unity 6 (`AR Dragon Sky Guardian/Assets/_Scripts/`) dan Python Computer Vision (`ARDragonSkyGuardian_CV/`) ke dalam *Code Knowledge Graph* berbasis AST lokal (Tree-sitter), guna memangkas *re-orientation token tax* AI Agent.

## 2. Keputusan Desain / Arsitektur / Teknis
- **Penyesuaian CLI `graphifyy v0.9.110`:** Pada versi `0.9.110`, subcommand yang tersedia adalah `graphify extract <path> --code-only --out <dir>` (menerima satu `<path>` per pemanggilan) diikuti `graphify merge-graphs` dan `graphify cluster-only --no-label`.
- **Target Scan Terarah:** Mengekstrak dua direktori kode saja (`AR Dragon Sky Guardian/Assets/_Scripts` dan `ARDragonSkyGuardian_CV`) agar folder biner Unity (`Library/`, `Temp/`, `Packages/`, `.meta`) serta `venv/` Python tidak ikut terpindai.
- **100% Lokal & Deterministik:** Flag `--code-only` dan `--no-label` dipakai agar ekstraksi AST dan klasterisasi Leiden berjalan offline tanpa memanggil LLM API.
- **Isolasi Output & Git:** Seluruh artefak ditempatkan di `brain/laptop-b/code-graph/`. Karena ukuran `graph.html` hanya `1.50 MB` (`< 5 MB`), seluruh berkas di `code-graph/` dapat di-commit langsung tanpa perlu pengecualian `.gitignore`.

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Instalasi:** `graphifyy 0.9.110` terpasang pada `C:\laragon\bin\python\python-3.10\python.exe`.
- **Ekstraksi & Penggabungan:**
  - C# (`AR Dragon Sky Guardian/Assets/_Scripts`): `33` file kode $\rightarrow$ `937` node, `2146` edge, `39` komunitas.
  - Python (`ARDragonSkyGuardian_CV`): `27` file kode $\rightarrow$ `397` node, `1247` edge, `17` komunitas.
  - Gabungan (`graph.json`): `1334` node, `3393` edge, `56` komunitas.
- **File Dibuat/Diperbarui:**
  - `brain/laptop-b/code-graph/index.md` — Portal navigasi Obsidian.
  - `brain/laptop-b/code-graph/GRAPH_REPORT.md` (`12,959` bytes) — Laporan kluster & dependensi.
  - `brain/laptop-b/code-graph/graph.json` (`1,832,894` bytes) — Basis data graf untuk kueri CLI.
  - `brain/laptop-b/code-graph/graph.html` (`1,569,254` bytes) — Visualizer interaktif browser.
  - `brain/laptop-b/code-graph/.graphify_analysis.json` (`109,889` bytes).
  - `brain/laptop-b/Tasks.md` & `brain/laptop-b/Devlog.md`.

## 4. Detail Penting yang Harus Diingat
- **Path Unity C#:** Lokasi skrip Unity berada di `AR Dragon Sky Guardian/Assets/_Scripts` (bukan `Assets/_Scripts` di root).
- **Kueri Terverifikasi:**
  - `graphify explain "DragonFlightController" --graph brain/laptop-b/code-graph/graph.json` $\rightarrow$ `Community 8`, `Degree: 88`.
  - `graphify path "UDPPoseReceiver" "DragonWingAnimator" --undirected --graph brain/laptop-b/code-graph/graph.json` $\rightarrow$ `2 hops` via `DragonWingAnimator.ResolveDependencies()`. (Catatan: arah panah adalah `DragonWingAnimator.ResolveDependencies() --> UDPPoseReceiver`, sehingga perlu `--undirected` jika query dimulai dari `UDPPoseReceiver`).
  - **Top 5 God Nodes:** `UDPPoseReceiver` (`99`), `DragonFlightController` (`88`), `VillageZoneController` (`73`), `InGameFlightHUD` (`63`), `IPoseProvider` (`63`).

## 5. Tugas Tersisa & Langkah Selanjutnya
- Gunakan kueri `graphify explain`, `graphify path --undirected`, `graphify query`, dan `graphify god-nodes` sebelum membaca skrip mentah saat mengerjakan Sesi 5 Lanjutan (`AudioManager` & Build Windows Standalone).
- Perbarui graf secara berkala setelah refaktor besar dengan mengekstrak ulang dua direktori target dan menggabungkannya kembali ke `brain/laptop-b/code-graph/graph.json`.

---
Navigasi: [[00 - Overview]] | [[Tasks]] | [[Devlog]] | [[laptop-b/code-graph/index]]
