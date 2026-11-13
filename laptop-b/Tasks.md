---
title: Laptop B — Tasks (Eksekusi & Pengembangan Program)
tags:
  - tasks
  - laptop-b
  - engineering
owner: Laptop B
updated: 2026-11-13
---

# 💻 Tasks — Laptop B (Eksekusi & Pengembangan Program)

> Ruangan khusus untuk melacak implementasi kode Unity C#, Python CV, integrasi aset 3D, dan build di **Laptop B**. Rujuk desain & arsitektur di [[00 - Overview]], [[laptop-a/prd]], dan [[laptop-a/konteks]].

## 🔥 Antrean Eksekusi (Dari Dokumen Desain Laptop A)
- [ ] **Sesi GUI:** Implementasi Canvas Menu Lobby (`Panel_Missions`, `Panel_Calibration` + RenderTexture Live Dragon, `Panel_Settings`), `InGameFlightHUD`, serta Victory/Defeat Modal sesuai [[laptop-a/rencana_desain_ui]].
- [ ] **Sesi Model Pulau:** Integrasi & setup FBX kepulauan 3 biome baru di Unity 6 URP setelah pemodelan Blender selesai.
- [ ] **Sesi 4:** Implementasi `Session4_VillageBuilder.cs`, `VillageZoneController.cs`, Auto-Hover, dan VFX 3 desa sesuai [[laptop-a/prd]].
- [ ] **Sesi 5:** `GameManager.cs` (Level 1–3, timer, bintang 1–3), audio/SFX, dan build Windows standalone + `start_game.bat`.

## ✅ Selesai (Baseline v4.0)
- [x] **Tahap 0 & Sesi 1:** `active_cv_sender.py` (YOLOv8n-pose CUDA, One-Euro filter, Dual UDP 5052 & 5053).
- [x] **Sesi 2:** `UDPPoseReceiver.cs` & `DragonFlightController.cs` (Navigasi 3D Y-T-A, dive boost, raycast ground clearance, keyboard zero-conflict).
- [x] **Sesi 3:** Integrasi awal naga `Dragon_Model.blend`, pulau 800x800m, dan `InGameFlightHUD.cs` awal.
