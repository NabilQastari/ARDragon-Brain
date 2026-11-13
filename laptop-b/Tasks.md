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
- [ ] **Uji Play Mode Webcam (Fly Up, Hover, Gentle Descent):** Verifikasi kenyamanan gestur dan transisi fisika secara langsung dengan pemain di Unity Editor sebelum lanjut ke UI lobby.
- [ ] **Texturing & Ekspor `rumah.blend`:** Jalankan prompt tekstur alami bergaya *stylized matte* (`Nagcu.blend`) di Hermes Desktop, verifikasi material pada koleksi `Rumah 1 jendela` & `Rumah 2 jendela`, lalu ekspor `.fbx` siap Unity URP (lihat [[Analisis dan Tekstur Rumah Blend]]).
- [ ] **Sesi GUI:** Implementasi Canvas Menu Lobby (`Panel_Missions`, `Panel_Calibration` + RenderTexture Live Dragon, `Panel_Settings`), `InGameFlightHUD`, serta Victory/Defeat Modal sesuai [[laptop-a/rencana_desain_ui]].
- [ ] **Sesi Model Pulau:** Integrasi & setup FBX kepulauan 3 biome baru di Unity 6 URP setelah pemodelan Blender selesai.
- [ ] **Sesi 4:** Implementasi `Session4_VillageBuilder.cs`, `VillageZoneController.cs`, Auto-Hover, dan VFX 3 desa sesuai [[laptop-a/prd]].
- [ ] **Sesi 5:** `GameManager.cs` (Level 1–3, timer, bintang 1–3), audio/SFX, dan build Windows standalone + `start_game.bat`.

## ✅ Selesai (v6.0 — Sky Peck & Auto-Launch Python)
- [x] **Rework CV & Tambahan Kontrol Terbang (Fly Up, Hover, Gentle Descent):** Selesai diintegrasikan pada Python CV (`pose_stabilizer.py`, `motion_recognizer.py`, `udp_sender.py`, `active_cv_sender.py`) & Unity (`IPoseProvider.cs`, `UDPPoseReceiver.cs`, `IFlightInputProvider.cs`, `DefaultFlightInputMapper.cs`, `DragonFlightController.cs`, `InGameFlightHUD.cs`) dengan verifikasi 10 skenario gestur & 6 suite fisika E2E. Lihat [[Rework CV dan Kontrol Terbang Baru]].
- [x] **Mekanik Penerbangan Sky Peck:** Implementasi *Flap to Climb* (decoupled tanpa pitch nodding), *Lean to Bank/Turn* (50% arm tilt + 50% torso lean), *Tuck to Dive* ($28\text{ m/s}$), *Propeller Dash* ($26\text{ m/s}$), *Swoop Climb*, dan *Glide* di `DragonFlightController.cs`, `UDPPoseReceiver.cs`, `active_cv_sender.py`, & `test_send_dummy_udp.py`.
- [x] **Auto-Launch Python Process Manager:** `PythonProcessManager.cs` terpasang di `Dragon_Player` (`Test_Dragon_Flight.unity`), otomatis menjalankan `active_cv_sender.py` saat Play Mode dan menghentikan process tree bersih saat keluar Play Mode.
- [x] **Arsitektur Input Modular & Telemetri 39-Float:** `IFlightInputProvider.cs`, `DefaultFlightInputMapper.cs`, `udp_sender.py` (39-float UDP 5052 + PIP 5053), dan indikator `Python CV : AKTIF [AUTO-SYNC]` di `InGameFlightHUD.cs`.
- [x] **Update Dokumentasi v6.0:** Pemutakhiran `konteks.md` & `prd.md` root project (commit `62c7e90`).
- [x] **On-Ground Mechanics & Arm-Proximity Locomotion Upgrade (v6.1, Fase A–D):** Spawn grounded diam, Arm-Proximity Gate (*tucked/away* + hysteresis), Ground Walk/Turn/LookUp, Takeoff, Air Gentle Descent, Hover, Glide (momentum decay), Fly Up, Fly Down (Pose Y & W), config terpusat (`DragonLocomotionConfig` & `cv_locomotion_config.json`), debug overlay HUD, serta verifikasi 16 skenario Play Mode.
- [x] **Setup Obsidian Brain (Laptop B):** Isolasi `brain/` di `.gitignore`, clone `ARDragon-Brain`, buat `AGENTS.md`, dan migrasi 4 catatan `.md` lokal (`GDD_Pulau.md`, `konteks.md`, `prd.md`, `progress.md`) ke `brain/laptop-b/`.
- [x] **Tahap 0 & Sesi 1:** `active_cv_sender.py` (YOLOv8n-pose CUDA, One-Euro filter, Dual UDP 5052 & 5053).
- [x] **Sesi 2:** `UDPPoseReceiver.cs` & `DragonFlightController.cs` (Navigasi 3D Y-T-A, dive boost, raycast ground clearance, keyboard zero-conflict).
- [x] **Sesi 3:** Integrasi awal naga `Dragon_Model.blend`, pulau 800x800m, dan `InGameFlightHUD.cs` awal.
