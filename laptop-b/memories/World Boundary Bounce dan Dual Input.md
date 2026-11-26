---
title: World Boundary Bounce, Misi Pyrewood 2-Tahap, & Dual Input Mode
type: session-memory
tags:
  - laptop-b
  - gameplay-mechanics
  - world-boundary
  - dual-input-mode
  - unity6
  - python-cv
  - ui-ux
date: 2026-11-26
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[laptop-a/gameplay-mechanics/spesifikasi_gameplay_boundary_dan_dual_input_mode]]"
  - "[[laptop-a/visual-vfx/spesifikasi_dynamic_wings_dan_head_tracking]]"
  - "[[Implementasi Sesi 4 Tantangan Desa]]"
  - "[[Implementasi Sesi 5 Game Loop dan Scoring]]"
---

# 🌊 World Boundary Bounce, Misi Pyrewood 2-Tahap, & Dual Input Mode

> **Konteks Kronologis:** Sesi eksekusi di **Laptop B** pada **2026-11-25 s.d. 2026-11-26** untuk merampungkan **Langkah 4–7** dari [[laptop-a/gameplay-mechanics/spesifikasi_gameplay_boundary_dan_dual_input_mode]] (melanjutkan **Langkah 1–3** Dual-Track Dynamic Wings, Head 3-Sumbu, Camera Peek, & Trotting `1.4 Hz` yang selesai dan diverifikasi pada bagian awal rangkaian pekerjaan ini).

---

## 1. Tujuan & Konteks Sesi
1. **World Boundary (`> 1100 m`) & Terrain/Rocks Glancing Bounce (`Langkah 4`):** Mencegah naga keluar batas kepulauan tanpa *invisible wall* keras, serta mengganti kondisi tabrakan tebing/bebatuan menjadi pantulan fisika aman tanpa *crash/reload scene*.
2. **Alur Misi Pyrewood 2-Tahap & Positive Time Up Screen (`Langkah 5`):** Memastikan danau tetap terkunci sebelum narasi diterima, menampilkan dua sub-tugas berjenjang di `QuestObjectivePanel`, serta mengubah layar Time Up agar suportif dengan metrik latihan.
3. **Dual Input Mode `CV (Webcam)` vs `Keyboard` (`Langkah 6`):** Menyediakan opsi pengalihan mode kontrol langsung di Settings (`SCREEN 05`) sehingga pemain dapat bermain penuh dengan Keyboard tanpa terblokir kalibrasi webcam ataupun error socket UDP.
4. **Verifikasi Lintas Suite & Sinkronisasi Brain (`Langkah 7`):** Menjalankan seluruh suite Python CV dan Unity Play Mode verifier di `MainIsland_Gameplay.unity` & `MainMenu_Lobby.unity`, lalu memulihkan scene aktif ke `MainMenu_Lobby.unity`.

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Hybrid Collision Bounce (`OnCollisionEnter` + `SphereCastAll` Sweep) pada Kinematic Rigidbody:**
   - `/Dragon_Player` menggunakan `Rigidbody` dengan `isKinematic = true` dan `useGravity = false` agar `TryGetGroundSurfaceY`, `SnapToGround()`, dan seluruh kontroler berbasis `transform.position` tetap deterministik tanpa *depenetration spike* PhysX.
   - Karena kinematic Rigidbody di Unity PhysX tidak selalu memicu `OnCollisionEnter` terhadap static `MeshCollider`, `DragonFlightController.cs` tetap menyediakan `OnCollisionEnter(Collision)` **dan** menjalankan *forward sweep* `Physics.SphereCastAll` (`TryHandleGlancingCollisionSweep`) tepat sebelum memindahkan `transform.position`, keduanya menuju satu helper `ApplyGlancingBounce(contactNormal, contactPoint)`.
   - Pendaratan terkendali pada permukaan datar (`isAttemptingLanding && hit.normal.y > 0.65f`) dikecualikan dari pantulan agar proses mendarat normal tetap mulus.
2. **Histeresis World Boundary (`1100 m` Masuk / `1050 m` Bebas):**
   - Di `UpdateWorldBoundaryPushback()`, radius horizontal XZ `> 1100f` mengaktifkan `_isBoundaryPushbackActive`, mengunci belok pemain sementara, memutar `Yaw` naga 180° menuju `(0,0,0)` via `Mathf.SmoothDampAngle(..., boundaryTurnSmoothTime = 0.65f)`, menjaga kecepatan jelajah minimal + sudut pitch aman di atas laut, dan menampilkan banner runtime `BoundaryWarningBanner` pada `InGameFlightHUD` tanpa merusak prefab HUD utama. Kunci dilepas otomatis saat radius `<= 1050f`.
3. **Pemisahan `IsCameraCalibrated` vs `IsReadyToLaunchGameplay`:**
   - `DragonGameSettings.IsCameraCalibrated` dipertahankan mencerminkan status kalibrasi CV sesungguhnya, sedangkan UI gerbang peluncuran (`MissionsPanelUI`, `LobbyNavigationManager`, `CalibrationPanelUI`) memeriksa `DragonGameSettings.IsReadyToLaunchGameplay` (`IsKeyboardInputMode || IsCameraCalibrated`). Dengan demikian, beralih kembali dari `Keyboard` ke `CV (Webcam)` tetap menegakkan aturan kalibrasi kamera.
4. **Guard `Application.isPlaying` untuk `DontDestroyOnLoad`:**
   - Ditemukan saat memanggil `Session4VerificationRunner` dari EditMode bahwa `DragonGameSettings.EnsurePersistentSessionHost()` dan `UDPVideoStreamReceiver.EnsureInstance()` dapat terpanggil saat `Application.isPlaying == false` dan memicu `InvalidOperationException` pada `DontDestroyOnLoad`. Keduanya kini dijaga dengan `if (!Application.isPlaying) return ...;` bersama guard di `InGameFlightHUD.ResolveDependencies()`.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **`AR Dragon Sky Guardian/ProjectSettings/TagManager.asset` & `Assets/Editor/IslandArchipelagoSetupUtility.cs`:**
  - Menambahkan layer `Terrain` (index `6`) dan `Rocks` (index `7`), serta metode `SyncTerrainAndRockLayersToPrefabAndScene()` untuk menetapkan layer pada `SM_Archipelago_Terrain` dan `SM_Rocks_*` di `PF_Pulau_Archipelago.prefab` & `MainIsland_Gameplay.unity`.
- **`AR Dragon Sky Guardian/Assets/_Scripts/Controllers/DragonFlightController.cs`:**
  - Menambahkan `worldBoundaryRadius = 1100f`, `worldBoundaryReleaseRadius = 1050f`, `boundaryTurnSmoothTime = 0.65f`, `crashProbeRadius = 1.6f`, `crashMicroShakeDuration = 0.12f`, `IsBoundaryPushbackActive`, `TotalFlapStrokes`, `UpdateWorldBoundaryPushback()`, `TryHandleGlancingCollisionSweep()`, `OnCollisionEnter()`, dan `ApplyGlancingBounce()` (pantulan `+15°..38°`, `-50%` momentum & airspeed, micro-shake `0.12s`, `PlayWingFlap(heavy: true)`, serta `_crashRecoveryGraceTimer = 0.45s`).
- **`AR Dragon Sky Guardian/Assets/_Scripts/UI/InGameFlightHUD.cs` & `Assets/_Scripts/Gameplay/VillageZoneController.cs` & `FinishModalUI.cs`:**
  - Menambahkan `SetBoundaryWarning()` / `UpdateBoundaryWarning()` (`"Angin badai laut terlalu kuat! Kembali ke kepulauan."`), menyembunyikan `pipPanelRoot`, `pipVideoRawImage`, dan `pipSkeletonOverlay` saat `IsKeyboardInputMode` aktif, menjaga `pyrewoodLake` terkunci hingga narasi diterima, menampilkan `"Cadangan Air Purba: SIAP!"` dan sub-tugas `[1]` & `[2]` di `QuestObjectivePanel`, serta memperbarui `FinishModalUI.ShowDefeat()` dengan judul `"WAKTU HABIS! USAHA YANG BAGUS!"` dan metrik durasi latihan + estimasi kepakan (`TotalFlapStrokes`) + vitalitas pulau.
- **`AR Dragon Sky Guardian/Assets/_Scripts/UI/DragonGameSettings.cs`, `SettingsPanelUI.cs`, `MissionsPanelUI.cs`, `LobbyNavigationManager.cs`, `CalibrationPanelUI.cs`, `Assets/Editor/UISceneAndPrefabBuilder.cs`, `UDPPoseReceiver.cs`, `PythonProcessManager.cs`, `UDPVideoStreamReceiver.cs`:**
  - Menambahkan `ControlInputMode` (`CvWebcam = 0`, `Keyboard = 1`) pada `PlayerPrefs` key `"Fanamir_InputControlMode"`, baris `Row_InputControlMode` pada `PF_SettingsPanel.prefab` (`SettingsCard` `960×716 px`, dirakit ulang lewat `UISceneAndPrefabBuilder.BuildAll()`), badge Lobby `"KEYBOARD MODE"`, serta `UDPPoseReceiver.ApplyInputControlMode(bool)` dan guard `PythonProcessManager.LaunchPython()`.

### Hasil Verifikasi Pasca-Edit Terakhir (`100% PASS`)
- **Python CV (`ARDragonSkyGuardian_CV\venv\Scripts\python.exe`):**
  - `test_all_17_poses_alignment.py` (`ALL 17 POSE ALIGNMENT TESTS PASSED 100%!`), `test_momentum_and_control_revision.py` (`100%`), `test_tahap1_face_tracking.py` (`SELURUH 8 PENGUJIAN TAHAP 1 LULUS 100%!`).
- **Unity 6 (`6000.3.7f1`) Play Mode:**
  - `Session4VerificationRunner`, `Session5GameLoopVerificationRunner`, `DragonAnimationVerificationRunner`, `MomentumFlightVerificationRunner`, dan `AudioSystemVerificationRunner` (`37/37` klip) seluruhnya **100% PASS** (catatan: runner gameplay/animasi/momentum dijalankan masing-masing dari sesi Play Mode bersih agar posisi/altitude/TimeScale awal tidak saling terbawa antar-runner).
  - Verifikasi runtime khusus **Langkah 4–6** di `MainIsland_Gameplay.unity` & `MainMenu_Lobby.unity` (lapisan `Terrain`/`Rocks`, boundary `>1100 m` & rilis `<=1050 m`, `ApplyGlancingBounce`, Pyrewood `[1]` & `[2]`, `"WAKTU HABIS! USAHA YANG BAGUS!"`, Settings dropdown `CV` $\leftrightarrow$ `Keyboard`, Lobby `"KEYBOARD MODE"`, & PIP hidden) **100% PASS**, `consoleErrors = 0`, `compilationFailed = false`, dan scene aktif dikembalikan ke `MainMenu_Lobby.unity`.

---

## 4. Detail Penting yang Harus Diingat
- **Kode Kunci `PlayerPrefs` & Layer Unity Baru:**
  - `Fanamir_InputControlMode`: `0` = `CV (Webcam)`, `1` = `Keyboard`. Default: `0`.
  - Layer `6 = Terrain`, Layer `7 = Rocks` di `TagManager.asset`.
- **Perilaku Test Runner di Play Mode Beruntun:**
  - `Session5GameLoopVerificationRunner` mensyaratkan naga dimulai dalam kondisi `Grounded` + `CountdownStart`, sedangkan `DragonAnimationVerificationRunner` mengakhiri uji terbang di ketinggian mendekati `maxAltitude = 260 m`. Jalankan tiap runner dari kondisi awal Play Mode/posisi yang bersih.
- **Splash Screen Lobby Saat Pengujian UI Otomatis:**
  - Saat `MainMenu_Lobby.unity` masuk Play Mode, `BootFlowController` menonaktifkan root Lobby selama fase Splash (`0..3s`). Untuk menguji tab Lobby (`Missions`/`Settings`) lewat skrip otomatis tanpa menunggu fade, panggil `BootFlowController.EnterLobbyImmediate()` terlebih dahulu agar `OnEnable()` panel UI tereksekusi.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
1. **Build Windows Standalone & Launcher:** Merakit build Windows standalone (`MainMenu_Lobby.unity`, `MainIsland_Gameplay.unity`, `Test_Dragon_Flight.unity`) beserta `start_game.bat` di `[[Tasks]]`.
2. **Uji Play Mode Langsung & Aset Sekunder Terbuka:** Uji kenyamanan langsung webcam dengan pemain (`Fly Up`, `Hover`, `Gentle Descent`), texturing & ekspor `rumah.blend`, serta (bila diperlukan di UI Lobby/Gameplay) sinkronisasi status visual lanjutan pasca uji pengguna.

---

**Navigasi:** [[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[laptop-a/gameplay-mechanics/spesifikasi_gameplay_boundary_dan_dual_input_mode]] · [[Implementasi Sesi 4 Tantangan Desa]] · [[Implementasi Sesi 5 Game Loop dan Scoring]]
