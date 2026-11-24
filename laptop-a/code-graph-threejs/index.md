---
title: Three.js Studio Code Knowledge Graph
type: code-graph
tags:
  - code-graph
  - threejs
  - webgl
  - studio
  - animation
  - laptop-a
owner: Laptop A
updated: 2026-11-24
status: active
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[visual-vfx/studio_animasi_threejs_fanamir]]"
---

# 🕸️ Three.js Studio Code Knowledge Graph (`dragon_anim_studio`)

> Peta arsitektur dan relasi pemanggilan fungsi di **Three.js Animation Studio** (`dragon_anim_studio/studio.js`) yang diekstraksi secara offline berbasis AST Tree-sitter menggunakan **Graphify**.

---

## 📑 Dokumen Utama & Navigasi
- [[GRAPH_REPORT]] — Laporan lengkap analisis kluster (Leiden communities), God Nodes, dan relasi pemanggilan fungsi.
- [[graph.canvas|Obsidian Canvas Graf]] — Tampilan visual interaktif simpul dan koneksi fungsi dalam format Obsidian Canvas.
- [Visualizer Interaktif Browser (graph.html)](file:///../../../dragon_anim_studio/graphify-out/graph.html) — Buka visualizer graf interaktif di peramban web lokal.

---

## 🏛️ God Nodes & Inti Abstraksi Studio
1. `[[updateDragonProceduralAnimation()]]` — Solver utama loop animasi naga (dada, leher, sayap, cakar, ekor).
2. `[[init()]]` — Inisialisasi scene Three.js, kamera, renderer, dan event listener UI.
3. `[[animate()]]` — Game loop / render loop via `requestAnimationFrame`.
4. `[[applyPosePreset()]]` — Solver preset pose (`hover`, `flight`, `walk`, `dash`, `dive`, `rest`).
5. `[[loadDragonModel()]]` — GLTFLoader untuk model 3D `dragon_model.glb` dan pemetaan tulang `ARM_Dragon`.
6. `[[generateUnityCSharpSnippet()]]` — Generator otomatis kode ekspor C# Unity (`DragonBodyAnimator.cs`).
7. `[[computeTrottingLegAngles()]]` — Solver kinematika trotting 4 kaki quadrupedal saat berjalan.
8. `[[evaluatePropellerDashStroke()]]` — Solver kepakan frekuensi tinggi Propeller Dash.

---

## 🧭 Cara Melakukan Kueri Cepat (CLI)
Gunakan perintah CLI Graphify untuk navigasi hemat token:
```powershell
# Penjelasan fungsi sentral
graphify explain "updateDragonProceduralAnimation" --graph dragon_anim_studio/graphify-out/graph.json

# Jalur koneksi antar fungsi
graphify path "animate" "computeTrottingLegAngles" --graph dragon_anim_studio/graphify-out/graph.json
```

---
*Navigasi:* [[00 - Overview]] | [[Tasks]] | [[Devlog]] | [[visual-vfx/studio_animasi_threejs_fanamir]]
