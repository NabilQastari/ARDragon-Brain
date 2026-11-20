---
title: Dynamic Wings Movement & Kontrol Input Kamera
type: session-memory
tags:
  - laptop-b
  - wing-animation
  - camera-control
  - unity6
  - python-cv
  - fbx-rigging
  - ar-dragon
date: 2026-11-21
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[laptop-a/konteks]]"
  - "[[laptop-a/rencana_desain_ui]]"
  - "[[Mekanik Sky Peck dan Auto-Launch Python]]"
  - "[[Implementasi UI Hybrid AR Dragon]]"
---

# 🪽 Dynamic Wings Movement & Kontrol Input Kamera

> **Konteks Kronologis:** Sesi ini berlangsung pada **2026-11-14 s.d. 2026-11-15** dan diarsipkan ke memori pada **2026-11-21**. Fitur-fitur lanjutan di repository (UI Hybrid lengkap, Sesi 4, Sesi 4B VFX, dan Momentum) dikembangkan pada sesi terpisah setelah pekerjaan inti ini.

---

## 1. Tujuan & Konteks Sesi
1. **Kontrol Input Kamera & Dual Mode Aktivasi YOLO:** Memastikan kamera CV tidak otomatis menyala saat game dijalankan, menambahkan pemilih perangkat kamera (*dropdown*) agar tidak terkunci ke kamera laptop bawaan, menyediakan dua mode aktivasi (`Latar Belakang` khusus feed PIP in-game vs `Jendela Preview` eksternal + PIP), serta menyusun teks penjelasan fitur baru ini sebagai bahan pembaruan desain UI di Laptop A.
2. **Implementasi & Perbaikan Dynamic Wings Movement Naga 3D:** Menghidupkan animasi sayap prosedural berbasis rig asli `Dragon_Model.blend` (`Dragon_Model.fbx` 47 tulang) pada 12 tulang sayap (`Wing_Shoulder`, `Wing_Forearm`, `4 Wing_Finger` kiri/kanan) lewat `DragonWingAnimator.cs`, kemudian memperbaiki cacat *rest-pose front lift* di mana bagian depan sayap masih mempertahankan pose awal impor yang terangkat curam selama animasi berjalan.

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Manual-Start Camera Selection & Dual Launch Mode:**
   - Menonaktifkan *auto-launch* saat startup (`autoLaunchPython = false`, `selectedCameraIndex = -1`, status awal `STANDBY`) di `PythonProcessManager.cs`, `UDPPoseReceiver.cs`, dan `Test_Dragon_Flight.unity`.
   - Menggunakan enumerasi perangkat kamera DirectShow cepat berbasis `ctypes` COM `ICreateDevEnum` (`enumerate_dshow_cameras()` / `--list-json`) agar nama & indeks kamera selaras antara Windows, Python OpenCV (`--cam` / `--cam-name`), dan dropdown Unity UI (`SolidCameraDropdown`).
   - Memisahkan `CameraLaunchMode`: `BackgroundInGameOnly` (`--headless`, hemat/rapi dengan stream PIP via UDP `5053`) vs `ExternalPreviewWindow` (membuka jendela OpenCV terpisah selain stream PIP), dilengkapi tombol `Scan` dan `Matikan` serta reset seluruh state pose di `UDPPoseReceiver` ketika stream terputus (`!_isConnected`).
2. **Hybrid Procedural Wing Engine Hanya pada 12 Tulang Sayap:**
   - Mempertahankan `assets/blender/Dragon_Model.blend` tetap utuh (*untouched*), dan mengekspor ulang `Dragon_Model.fbx` dengan visibilitas armature `ARM_Dragon` aktif di memori agar ke-13 `SkinnedMeshRenderer` terhubung ke 47 tulang di Unity.
   - `DragonWingAnimator.cs` (`[DefaultExecutionOrder(50)]`, namespace `DragonWingAR.Controllers`) hanya menggerakkan 12 tulang sayap, meninggalkan 35 tulang tubuh lainnya pada pose istirahat, dan memadukan state fisika `DragonFlightController` + telemetri CV per-sisi (`IPoseProvider`), sekaligus mendukung `SetPreviewState` / `ClearPreviewState` untuk `LiveDragonPreviewController`.
3. **Rebasing Basis Airfoil Horizontal Zero-Twist pada Pose Istirahat:**
   - **Akar masalah yang ditemukan saat perbaikan lanjutan:** Pose impor `Dragon_Model.fbx` mengangkat bagian depan sayap curam (`Wing_Shoulder.L` `+12.9°`, `Wing_Forearm.L` naik `+35.5°` ke wrist `y = 0.554` di atas `Head` `y = 0.341`), sedangkan 4 jari menukik tajam `-19°..-55°` (`boneY.y = -0.33..-0.82`). Karena `CreateBoneRig()` menyimpan `RestLocalRotation` apa adanya dan `ComputeWingTargets()` masih memberi bias positif `PitchUp` di banyak regime, sayap tetap terlihat mengangkat bagian depannya.
   - **Solusi bersih:** Menetralkan orientasi tulang sayap di `BuildWingSide()` / `OrientWingBoneNeutral()` (`Shoulder` $\rightarrow$ `Forearm` $\rightarrow$ `Fingers` secara berurutan agar tanpa *cross-axis skew* dan *zero-twist* menggunakan cross `Vector3.up`) **sebelum** merekam `RestLocalRotation` serta `AxisUp`, `AxisForward`, dan `AxisPitchUp`.
   - Memproyeksikan fleksi vertikal jari ke komponen lateral (`AxisForward`) dan belakang (`AxisPitchUp`) via `FingerLateralFlapWeights` dan `FingerBackPitchWeights`, serta membalik sweep pelipatan jari dalam (`FingerFoldSweepDeg`) agar tidak berputar melintasi tulang punggung.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset

### A. Kontrol Kamera & Python CV
- **`ARDragonSkyGuardian_CV/camera_stream.py` & `check_camera.py`:** Menambahkan `enumerate_dshow_cameras()`, `resolve_camera_index()`, dan flag `--list-json`.
- **`ARDragonSkyGuardian_CV/active_cv_sender.py`:** Menambahkan dukungan `--cam-name` serta deteksi penutupan jendela OpenCV `[X]` secara bersih.
- **`AR Dragon Sky Guardian/Assets/_Scripts/CV_Receiver/PythonProcessManager.cs`:** Menambahkan `CameraLaunchMode` (`None`, `BackgroundInGameOnly`, `ExternalPreviewWindow`), `RefreshCameraListAsync()`, `SelectCamera()`, `LaunchBackgroundMode()`, `LaunchExternalPreviewMode()`, `PYTHONIOENCODING=utf-8`, dan guard race-condition `ReferenceEquals` pada lifecycle proses anak.
- **`AR Dragon Sky Guardian/Assets/_Scripts/CV_Receiver/UDPPoseReceiver.cs`:** Menonaktifkan `autoLaunchPython` dan mereset penuh flag pose/flap/dash ketika `!_isConnected`.
- **`AR Dragon Sky Guardian/Assets/_Scripts/UI/InGameFlightHUD.cs` & `Test_Dragon_Flight.unity`:** Menambahkan UI pemilih input kamera di kanan atas (dropdown opaque `alpha = 1.0f`, `Scan`, `Matikan`, `Latar Belakang`, `Jendela Preview`), serta menyerahkan ringkasan teks fitur ke user untuk pembaruan UI di Laptop A.

### B. Rigging FBX & Dynamic Wings Movement (`DragonWingAnimator.cs`)
- **`AR Dragon Sky Guardian/Assets/Assets 3D/Dragon_Model.fbx`:** Ekspor ulang dari Blender `5.1.2` dengan armature 47 tulang `ARM_Dragon` (`13` `SkinnedMeshRenderer`, orientasi `+Z` depan, skala `1.25`).
- **`AR Dragon Sky Guardian/Assets/_Scenes/Test_Dragon_Flight.unity` & `Assets/Editor/DragonFlightTestSceneBuilder.cs`:** Pembaruan prefab `/Dragon_Player/Visual_Dragon/Dragon_Model` dan pemasangan komponen `DragonWingAnimator`.
- **`AR Dragon Sky Guardian/Assets/_Scripts/Controllers/DragonWingAnimator.cs`:**
  - Penetralan airfoil horizontal di [`BuildWingSide`](file:///d:/Fauzih/Kuliah/SEMESTER%20V/Proyek%20Multimedia%20dan%20Game/AR%20Dragon/AR%20Dragon%20Sky%20Guardian/Assets/_Scripts/Controllers/DragonWingAnimator.cs#L284-L311) & [`OrientWingBoneNeutral`](file:///d:/Fauzih/Kuliah/SEMESTER%20V/Proyek%20Multimedia%20dan%20Game/AR%20Dragon/AR%20Dragon%20Sky%20Guardian/Assets/_Scripts/Controllers/DragonWingAnimator.cs#L318-L331) memakai konstanta `NeutralShoulderRightDir`, `NeutralForearmRightDir`, dan `NeutralFingerRightDirs` + `MirrorDirX()`.
  - Penghapusan bias positif `PitchUp` pada `GroundedIdle` (`-4f`/`-2f`), `GroundedWalk` (`-3f`/`-2f`), `Takeoff`/`FlyUp`/`Flapping` (`stroke * 6f`, `forearmWave * 3f`), `Hover` (`3f + stroke * 6f`), `TuckDive` (sweep dikalibrasi menjadi `32f..48f` / `10f..16f`), `PropellerDash`, `FlyDownY` (`6f`/`3f`), `FlyDownW` (`3f`), `GentleDescent` (`4f`/`2f`), dan `Glide` (`-1f`/`0f`).
  - Pembaruan [`ApplyWingSide`](file:///d:/Fauzih/Kuliah/SEMESTER%20V/Proyek%20Multimedia%20dan%20Game/AR%20Dragon/AR%20Dragon%20Sky%20Guardian/Assets/_Scripts/Controllers/DragonWingAnimator.cs#L710-L744) dengan `FingerFoldSweepDeg = { 18f, -10f, -40f, -64f }`, `FingerFanDipDeg = { 0f, 1.5f, 2.5f, 2f }`, `FingerLateralFlapWeights = { 0.98f, 0.78f, 0.36f, 0.0f }`, dan `FingerBackPitchWeights = { 0.18f, 0.62f, 0.93f, 1.0f }`.

### C. Hasil Verifikasi Utama
- **Kontrol Kamera (`Test_Dragon_Flight.unity`):** Dropdown menampilkan 7 kamera DirectShow `[0..6]`, mode `Latar Belakang` berjalan dengan PIP in-game tanpa window eksternal, `Jendela Preview` membuka window OpenCV (`MainWindowHandle` non-zero), tombol `Matikan` & keluar Play Mode membersihkan proses Python (`0` error/warning).
- **Perbaikan Sayap (`MainMenu_Lobby.unity` & `Test_Dragon_Flight.unity`):**
  - Rekompilasi Unity bersih (`compilationFailed: false`, `errors: []`, `consoleErrors: 0`).
  - Saat `CacheWingRig()` inisialisasi di `MainMenu_Lobby.unity`, batas membran sayap langsung sejajar (`Y[-0.182, 0.092]`, `FrontY = +0.018`, `BackY = -0.024`); `GLIDE (T-POSE)`, `TUCK TO DIVE` (bebas tumpuk di punggung), dan `FLAP TO CLIMB` terverifikasi via `capture_game_view`.
  - Play Mode UDP `5052` di `Test_Dragon_Flight.unity` terverifikasi untuk `GroundedIdle` (`FrontY = +0.032`, `BackY = +0.014`, `Y[-0.169, 0.100]`), `Flapping` (`FrontY = -0.258`, `BackY = -0.413`), `GentleDescent` (`FrontY = +0.211`, `BackY = +0.323`), serta `cv_asym` (`consoleErrors: 0`, `consoleWarnings: 0`, `isDirty: false`).

---

## 4. Detail Penting yang Harus Diingat
- **Dua SkinnedMeshRenderer Sayap & Preview Edit-Mode:** `Dragon_Model` memiliki **dua** mesh sayap terpisah (`SM_Dragon_WingBones` untuk tulang/cakar dan `SM_Dragon_WingMembranes` untuk selaput). Saat menginspeksi pose tulang lewat `script_eval` di Edit Mode, kedua `SkinnedMeshRenderer` perlu `updateWhenOffscreen = true` dan `forceMatrixRecalculationPerRender = true` + `EditorApplication.QueuePlayerLoopUpdate()` agar render Scene View tidak menampilkan frame lama yang tidak sinkron antar kedua mesh.
- **Konvensi Sumbu Bone Blender $\rightarrow$ Unity & Tanda Rotasi `DragonWingAnimator`:**
  - Di rest pose `Dragon_Model.fbx`, setiap tulang sayap memiliki `bone.up` (`+Y`) ke arah panjang tulang, `bone.right` (`+X`) horizontal sejajar bidang sayap (`y == 0`), dan `bone.forward` (`+Z`) mengarah ke atas sebagai normal permukaan sayap.
  - Dalam koordinat `Visual_Dragon` (+Z depan, +Y atas, -X kiri): nilai `+ShoulderPitchUp` memutar **tepi depan (`+Z`) naik** dan **tepi belakang (`-Z`) turun**, sedangkan `+FingerFoldSweepDeg` pada jari dalam yang sudah mengarah `-Z` (sekitar `-93°`) akan memutar jari menyeberang ke atas tulang punggung bila bahu + lengan ikut menyapu ke belakang—karena itu jari dalam menggunakan offset negatif (`-64f`) agar sejajar sisi badan.
- **Urutan Inisialisasi `OrientWingBoneNeutral`:** Selalu orientasikan tulang **berurutan dari induk ke anak** (`Wing_Shoulder` $\rightarrow$ `Wing_Forearm` $\rightarrow$ `Wing_Finger_*`) sebelum memanggil `CreateBoneRig()`, agar `Quaternion.Inverse(bone.rotation) * visualRoot.rotation` bebas miring silang (*cross-axis skew*).
- **Jangan Ubah File Blender Mentah:** `assets/blender/Dragon_Model.blend` tidak dimodifikasi; perbaikan dilakukan lewat ekspor armature (`Dragon_Model.fbx`) dan penetralan runtime di `DragonWingAnimator.cs`.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
*(Catatan: Sesi-sesi lain pasca 2026-11-15 telah melanjutkan implementasi UI Hybrid v1.8, Sesi 4, Sesi 4B VFX/FOV/Skybox, serta Momentum v4.0 pada `DragonWingAnimator.cs` & scene `MainIsland_Gameplay.unity`.)*
- Selesaikan item terbuka di [[Tasks]]:
  1. **Uji Play Mode Webcam Langsung (`Fly Up`, `Hover`, `Gentle Descent`):** Uji kenyamanan gestur dan transisi fisika secara real-time di depan kamera oleh pemain.
  2. **Texturing & Ekspor `rumah.blend`:** Terapkan tekstur *stylized matte* di Hermes Desktop / Blender dan ekspor `.fbx` siap URP.
  3. **Sesi 5 & Feature-to-UI Sync (`GameManager` & `AudioManager`):** Implementasikan `GameManager.cs` & `AudioManager`, selesaikan sinkronisasi `InGameFlightHUD`, `FinishModalUI`, `MissionsPanelUI`, serta `DragonGameSettings`, lalu buat build Windows standalone.

---

**Navigasi:** [[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[Implementasi UI Hybrid AR Dragon]] · [[Mekanik Sky Peck dan Auto-Launch Python]]
