---
title: Laptop B — Devlog (Eksekusi & Pengembangan Program)
tags:
  - devlog
  - laptop-b
owner: Laptop B
updated: 2026-11-13
---

# 🛠️ Devlog — Laptop B

> Catatan kronologis pengembangan kode, debugging, pengujian Unity/Python, dan hasil build dari **Laptop B**. Kembali ke [[00 - Overview]].

---

## 2026-11-13 — Implementasi Perbaikan Kontrol Prioritas 1 (Takeoff, Landing, FlyUp Threshold, Config CV & Grounded Look Up)
- **Aksi:**
  - **Unity (`DragonFlightController.cs` & `Test_Dragon_Flight.unity`):**
    - Menghapus *hard-snap* `safePos.y = landedSurfaceY + minGroundClearance` (`3.5 m`) yang menyebabkan naga teleport `+2.65 m` pada frame pertama `Takeoff`, serta menjaga agar `groundAvoidance` tidak mendistorsi kurva naiknya `_isTakingOff`.
    - Mencegah `FlyUp` membatalkan `_isTakingOff` sebelum naga mencapai ketinggian target (`_takeoffTargetY - 0.35f`).
    - Mengizinkan `TuckDive` yang mencapai permukaan tanah untuk memicu transisi mendarat (`TriggerLanding()`) alih-alih tertahan meluncur di atas tanah.
    - Memisahkan `Grounded Look Up` dari `PitchInput` (tangan terangkat) sehingga di darat naga hanya mendongak berdasarkan `BodyPitch`.
    - Menonaktifkan `enableKeyboardFallback` pada `UDPPoseReceiver` di `/Dragon_Player` (`Test_Dragon_Flight.unity`) sesuai keputusan fokus murni kontrol webcam/CV.
  - **Python CV (`player_calibrator.py`, `motion_recognizer.py`, `cv_locomotion_config.json`, `active_cv_sender.py`):**
    - Mengubah `get_default_profile()` menjadi `is_calibrated=False` dan memperbaiki rumus adaptif `FlyUpClassifier` saat terkalibrasi (`min(1.80, max(1.68, neutral_ratio + 0.08))`) sehingga ambang `Fly Up` kembali responsif di `1.74` (default) dan `<= 1.80` (calibrated), bukan `1.9075`.
    - Menghubungkan pembacaan `_CV_CONFIG` (`cv_locomotion_config.json`) ke `TuckDiveClassifier`, `FlyUpClassifier`, `UpperArmLocomotionGate`, `GentleDescentClassifier`, dan `FlyDownClassifier`.
    - Memperkaya sinyal `body_pitch` di `active_cv_sender.py` dengan menggabungkan *head tilt* (`head_signal`) dan busung dada (`chest_elevation`) tanpa terkontaminasi posisi pergelangan tangan.
  - **Verifikasi:** Kompilasi Unity 6 URP `0 errors` (`compilationFailed: false`) dan suite validasi Python CV Phase 2, 4, 5 serta unit test `FlyUpClassifier` lulus 100%.

## 2026-11-13 — Ganti Pulau dengan Grass Baseplate & Audit Kendala Mekanisme Kontrol
- **Aksi:**
  - Menghapus `/World_Island` dari hierarki `Test_Dragon_Flight.unity` dan menggantinya dengan `/Grass_Baseplate` (`Cube` ukuran `2000 x 2 x 2000` di `(0, -1, 0)` dengan material `Assets/Materials/Mat_Grass_Baseplate.mat`, shader `Universal Render Pipeline/Lit`), serta memindahkan `/Dragon_Player` ke `(0, 0.85, 0)` dan menyimpan scene.
  - Melakukan audit penuh terhadap seluruh mekanisme kontrol (Python CV, protokol UDP 41-float, `UDPPoseReceiver`, `DefaultFlightInputMapper`, `DragonFlightController`, `InGameFlightHUD`, dan suite tes CV).
  - Menemukan 15 kendala utama (termasuk bug takeoff teleport `+2.65 m`, `FlyUp` threshold default naik ke `1.9075`, dead config `cv_locomotion_config.json`, double smoothing/steering `^1.8225`, split `UpperArmLocomotionGate`, `DefaultFlightInputMapper` belum terpasang di scene, celah keyboard fallback `A`/`Dash`/`FlyDownY/W`/harus menahan `T`, serta kegagalan pada `test_additional_gestures.py` Skenario 3 dan `test_e2e_flight_physics.py` Test 5).

## 2026-11-13 — Penyederhanaan Nama File Memori (Laptop B)
- **Aksi:** Menyederhanakan seluruh nama file di `brain/laptop-b/memories/` menjadi judul ringkas (3–6 kata) sesuai standar baru di [[00 - Overview]] serta memperbarui seluruh `[[wikilinks]]` di `Devlog.md` dan `Tasks.md`.

## 2026-11-13 — Upgrade On-Ground Mechanics, Arm-Proximity Locomotion Gate, Takeoff, Fly Up/Down & Verifikasi 16 Skenario
- **Aksi:**
  - Menyelesaikan pembaruan dokumen perencanaan (`prd.md` & `konteks.md`) serta implementasi penuh **Fase A–D** pada Python CV (`pose_stabilizer.py`, `motion_recognizer.py`, `udp_sender.py`, `active_cv_sender.py`, `cv_locomotion_config.json`) dan Unity (`DragonFlightController.cs`, `DefaultFlightInputMapper.cs`, `UDPPoseReceiver.cs`, `IFlightInputProvider.cs`, `IPoseProvider.cs`, `DragonLocomotionConfig.cs`, `InGameFlightHUD.cs`).
  - Memverifikasi seluruh **16 skenario pengujian** di Unity Play Mode via UDP `5052` dengan `compilationFailed: false` dan `consoleErrors: 0`.
  - Catatan lengkap: [[On-Ground Mechanics dan Uji 16 Skenario]].

## 2026-11-13 — Setup OmniRoute, Migrasi Endpoint Hermes, & Launcher `Run Hermes Desktop.bat`
- **Aksi:**
  - Menyusun prompt autonomous setup OmniRoute untuk Hermes Agent, panduan mematikan server 9Router (port `20128`), solusi error `better-sqlite3` (`NODE_MODULE_VERSION 127` vs `137`) pada OmniRoute `v3.8.48`, serta command migrasi endpoint Hermes ke `http://localhost:20128/v1`.
  - Membuat file `Run Hermes Desktop.bat` di root project untuk menjalankan OmniRoute (jika port `20128` belum aktif) dan meluncurkan Hermes Desktop secara otomatis.
  - Catatan lengkap: [[Setup OmniRoute dan Launcher Hermes Desktop]].

## 2026-11-13 — Mekanik Sky Peck, Auto-Launch Python Process Manager, & Update PRD/Konteks v6.0
- **Catatan Sesi Lengkap:** [[Mekanik Sky Peck dan Auto-Launch Python]]
- **Ringkasan Eksekusi:**
  - Menerapkan mekanik penerbangan ala **Sky Peck** di `DragonFlightController.cs`, `UDPPoseReceiver.cs`, `InGameFlightHUD.cs`, dan `active_cv_sender.py` (*Flap to Climb* decoupled tanpa *pitch nodding*, *Lean to Bank/Turn* 50% arm tilt + 50% torso lean, *Tuck to Dive* hingga $28\text{ m/s}$, *Swoop Climb*, *Glide*, serta *Propeller Dash* hingga $26\text{ m/s}$).
  - Menambahkan `PythonProcessManager.cs` pada `Dragon_Player` di `Test_Dragon_Flight.unity` agar `active_cv_sender.py` otomatis berjalan saat Unity Play Mode dimulai dan otomatis berhenti bersih (`taskkill /F /T`) saat keluar Play Mode.
  - Menambahkan arsitektur input modular (`IFlightInputProvider.cs`, `DefaultFlightInputMapper.cs`) dan memperluas protokol UDP 5052 hingga 39-float.
  - Memverifikasi kompilasi bebas error (`0 errors`) dan pengujian live Unity Play Mode (termasuk PIP webcam stream port 5053 dan terminasi bersih PID Python).
  - Memutakhirkan `konteks.md` & `prd.md` ke **v6.0** dan push ke repo project (`origin/master` commit `6f03436`, `2b4833b`, `62c7e90`).

## 2026-11-13 — Analisis Aset `rumah.blend` & Prompt Tekstur Hermes Desktop (Laptop B)
- **Ringkasan:** [[Analisis dan Tekstur Rumah Blend]]
- **Aksi:**
  - Menganalisis struktur proyek dan menginspeksi `rumah.blend`, `Nagcu.blend`, `pulau.blend`, dan `pohon.blend` via Blender 5.1 CLI.
  - Menyusun prompt eksekusi untuk **Hermes Desktop** pada path `Assets AR Dragon Sky Guardian` dengan gaya *stylized low-poly matte* ala `Nagcu.blend`, menggunakan palet warna rumah alami (terakota `#B84A39`, plester krem `#E8DFCE`, kayu `#5C381E`, batu `#54575E`).

## 2026-11-13 — Rework Computer Vision Motion Control & Tambahan Kontrol Terbang (Fly Up, Hover, Gentle Descent)
- **Catatan Memori:** [[Rework CV dan Kontrol Terbang Baru]]
- **Aksi & Hasil:**
  - Mengimplementasikan deteksi & fisika **Fly Up** (chest raised + flapping), **Hover Mode** (upper arms tucked + forearm flapping), dan **Gentle Descent** (tucked T-pose tanpa flapping) dengan strict priority arbitration di `motion_recognizer.py` dan `DragonFlightController.cs`.
  - Memperluas protokol UDP CSV ke 39 token (`34..38`), memperbarui `IPoseProvider.cs`, `UDPPoseReceiver.cs` (termasuk keyboard fallback `H`, `G`, `U`), `IFlightInputProvider.cs`, `DefaultFlightInputMapper.cs`, dan `InGameFlightHUD.cs`.
  - Validasi lulus 100%: `test_e2e_flight_physics.py` (6/6), `test_additional_gestures.py` (10/10), `test_phase5_validation.py` (7/7), `test_phase6_final_session.py` (7/7), dan kompilasi Unity 6 URP 0 error.

## 2026-11-13 — Setup Obsidian Brain & Migrasi Catatan Lokal (Laptop B)
- **Aksi:**
  - Menambahkan `brain/` ke `.gitignore` root project dan meng-clone `ARDragon-Brain.git` ke `brain/`.
  - Memindahkan seluruh file `.md` lokal dari root project (`GDD_Pulau.md`, `konteks.md`, `prd.md`, `progress.md`) ke `brain/laptop-b/`.
  - Membuat `AGENTS.md` di root project untuk aturan kolaborasi dual-laptop.

## 2026-11-13 — Inisialisasi Folder Brain Laptop B
- Folder `brain/laptop-b/` dibuat untuk memisahkan log eksekusi kode & implementasi teknis dari catatan desain di `brain/laptop-a/`.
- Baseline yang sudah terverifikasi sebelum sinkronisasi vault:
  - Python CV Dual-Port UDP (`5052` pose CSV, `5053` JPEG PIP) aktif.
  - Sistem terbang 3D Pose Y-T-A dan model naga `Dragon_Model.blend` aktif di Unity 6 (`6000.3.7f1`).
  - Tugas berikutnya menanti eksekusi **Sesi GUI** berdasarkan [[laptop-a/rencana_desain_ui]].
