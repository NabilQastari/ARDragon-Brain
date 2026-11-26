---
title: "Stabilisasi Kontrol Lintas Scene dan Dash"
type: session-memory
tags:
  - session-memory
  - laptop-b
  - udp-5052
  - scene-transition
  - propeller-dash
  - effekseer-vfx
  - calibration-gate
date: 2026-11-26
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[laptop-b/Tasks]]"
  - "[[laptop-b/Devlog]]"
  - "[[Penyelarasan 17 Pose dan Propeller Dash]]"
  - "[[Implementasi Sesi 4B VFX dan Atmosfer]]"
---

# Stabilisasi Kontrol Lintas Scene dan Dash

## 1. Tujuan & Konteks Sesi
- Mengatur alur mulai game dari scene menu (`MainMenu_Lobby.unity` pada Build Index `0`), memperbaiki kontrol in-game & fallback keyboard, serta mengunci peluncuran stage sebelum kalibrasi kamera selesai.
- Menuntaskan bug kontrol naga yang hilang saat transisi `MainMenu_Lobby` $\rightarrow$ `MainIsland_Gameplay` $\rightarrow$ Calibration serta memperbaiki bug kilau VFX berlebih ("cahaya mengkilat") dan duplikasi socket saat naga take off.
- Menyetel ulang sensitivitas `#14 Propeller Dash` pada Python CV (`ARDragonSkyGuardian_CV/`) agar tidak mudah terpicu secara tidak sengaja oleh gerakan 1 lengan, kepakan 1D, atau ayunan lambat.

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Persistent Shared Socket UDP `5052` & Session Host (`UDPPoseReceiver.cs`, `DragonGameSettings.cs`):**
   - Mengubah socket listener port `5052` dari siklus bind/close per scene (`ReuseAddress = true`) menjadi **persistent shared singleton socket** (`_sharedUdpClient`, `_sharedReceiveThread`, `_activeReceiver`, `_sharedSocketLock`, `ExclusiveAddressUse = true`).
   - `ReleasePortForSceneTransition()` / `OnDisable()` / `OnDestroy()` hanya melepaskan `_activeReceiver` agar socket `5052` tetap terbuka lintas scene dan mencegah terbentuknya socket ganda yang mencuri paket; pembersihan penuh berjalan pada `[RuntimeInitializeOnLoadMethod(RuntimeInitializeLoadType.SubsystemRegistration)]`, `EditorApplication.playModeStateChanged` (`ExitingPlayMode`), dan `OnApplicationQuit`.
   - Memanggil `EnsurePersistentSessionHost()` di `DragonGameSettings.EnsureInit()` (`BeforeSceneLoad`) dan menghapus komponen `PythonProcessManager` lokal dari `LobbyControllers` di `MainMenu_Lobby.unity`.
2. **Urutan Scene Menu, Fallback Keyboard, & Gerbang Kalibrasi (`GameManager.cs`, `DefaultFlightInputMapper.cs`, `MissionsPanelUI.cs`, `CalibrationPanelUI.cs`, `LobbyNavigationManager.cs`):**
   - Menetapkan `MainMenu_Lobby.unity` sebagai Build Index `0` (`AudioListener` pada `Main Camera`), menjaga `enableKeyboardFallback = true`, menambahkan `IsKeyboardOverrideActive` di `UDPPoseReceiver.cs`, menangani `SIO_UDP_CONNRESET`, serta mereset `GameManager.Instance` via `SubsystemRegistration` dengan `Time.timeScale = 1f` di `BeginStage()`.
   - Memblokir tombol Stage `01..03`, Free Play, dan `LaunchFlightScene()` ketika `DragonGameSettings.IsCameraCalibrated == false`, otomatis berpindah ke tab Kalibrasi dan menyinkronkan label UI melalui event statis `DragonGameSettings.OnCalibrationStatusChanged`.
3. **Eliminasi Kilau VFX Takeoff & Warning RenderGraph (`EffekseerSettings.asset`, `Session4_VillageBuilder.cs`, `DragonVfxController.cs`, `VFX_Flight_*.py`):**
   - Mematikan `enableDepth` (`1 -> 0`) di `EffekseerSettings.asset` karena `enableURPRasterPass: 0` memicu warning `CommandBuffer: temporary render texture not found (SetGlobalTexture)` pada URP RenderGraph Unity 6.
   - Menghapus 2 duplikat `VFX_Sockets` di bawah `Visual_Dragon` pada `MainIsland_Gameplay.unity` (`18 -> 8` emitter) dan memperbaiki pencariannya di `Session4_VillageBuilder.cs`.
   - Menahan `VFX_Flight_WingtipTrails` selama fase `IsTakingOff` di `DragonVfxController.cs`, serta menurunkan ukuran dan emissive pada `VFX_Flight_WingtipTrails.py` dan `VFX_Flight_SonicDashShockwave.py`.
4. **Penyetelan Bilateral `#14 Propeller Dash` (`motion_recognizer.py`, `pose_stabilizer.py`, `cv_locomotion_config.json`):**
   - Mengganti logika `max(omega)` / `or` dengan validasi **bilateral kedua lengan** (`avg_omega`, `min(omega_l, omega_r)`, `avg_accum`, `min_single_accum`, `both_2d_loops` untuk entry, serta `radius_ok` di kedua sisi).
   - Memperketat spread horizontal lengan atas, ukuran & aspek orbit 2D, konfirmasi `min_entry_frames = 2`, peluruhan saat diam/reversal, `grace_hold_sec` (`0.42s -> 0.18s`), serta pemicu `recover_motion_blurred_wrists()`.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Unity C#, Editor, VFX & Scene (`AR Dragon Sky Guardian/`, `vfx/src/`):**
  - `UDPPoseReceiver.cs`, `DragonGameSettings.cs`, `DragonWingAnimator.cs`, `DragonBodyAnimator.cs`, `DragonVfxController.cs`, `DefaultFlightInputMapper.cs`, `GameManager.cs`, `InGameFlightHUD.cs`, `MissionsPanelUI.cs`, `CalibrationPanelUI.cs`, `LobbyNavigationManager.cs`.
  - `UISceneAndPrefabBuilder.cs`, `Session4_VillageBuilder.cs`, `MainMenu_Lobby.unity`, `MainIsland_Gameplay.unity`, `Test_Dragon_Flight.unity`.
  - `Assets/Resources/EffekseerSettings.asset`, `vfx/src/VFX_Flight_WingtipTrails.py`, `vfx/src/VFX_Flight_SonicDashShockwave.py` (kompilasi `vfx lint` & `build` -> `.efkefc` di `Assets/VFX/Effekseer/`).
- **Python CV (`ARDragonSkyGuardian_CV/`):**
  - `cv_locomotion_config.json`, `motion_recognizer.py`, `pose_stabilizer.py`, `test_all_17_poses_alignment.py`.
- **Verifikasi yang Diamati:**
  - Transisi penuh webcam real (`active_cv_sender.py`, `PID 14492`): `MainMenu_Lobby` $\rightarrow$ `MainIsland_Gameplay` (`UDP: conn=True pose=True calib=True state=5`, `VID: conn=True`) $\rightarrow$ `Pause -> Calibration` $\rightarrow$ `BackToLobby -> Calibration`, dengan `netstat` tepat **1** socket `5052` dan **0** error/warning.
  - Simulasi Takeoff + Turn VFX: `[DuringTakeoff] Grounded=False IsTakingOff=True Wingtip=False` $\rightarrow$ `[PostTakeoff] Grounded=False IsTakingOff=False Speed=19.50 Wingtip=True` (`0` error/warning); `DragonAnimationVerificationRunner` (`100% PASS`) & `MomentumFlightVerificationRunner` (`100% PASS`).
  - Python CV (`ARDragonSkyGuardian_CV/venv/Scripts/python.exe`): `test_all_17_poses_alignment.py` (termasuk uji penolakan 1 lengan, flap 1D + sway, putaran lambat `<2 rad/s`, & ayunan Low-A), `test_momentum_and_control_revision.py`, `test_additional_gestures.py` (`10/10`), & `test_e2e_flight_physics.py` (`6/6`) lulus **100%**.

---

## 4. Detail Penting yang Harus Diingat
- **Konfigurasi `propeller_dash` di `cv_locomotion_config.json`:** `min_rotation_accum_rad: 2.05`, `min_side_rotation_accum_rad: 1.90`, `min_angular_velocity: 2.85`, `min_side_angular_velocity: 1.65`, `min_orbit_radius_sw: 0.135`, `min_angle_radius_sw: 0.085`, `spread_horiz_enter: 0.40`, `spread_horiz_exit: 0.26`, `spread_vert_enter: 0.46`, `spread_vert_exit: 0.62`, `min_2d_span_sw: 0.13`, `min_2d_aspect: 0.28`, `min_entry_frames: 2`, `grace_hold_sec: 0.18`.
- **Blur Recovery Guard (`pose_stabilizer.py`):** `recover_motion_blurred_wrists()` hanya aktif saat `elbow_span_sw >= 1.45`, `c_e >= 0.15`, `(c_w < 0.28 or dist_we_sw < 0.14)`, dan `diff >= 18.0`.
- **VFX & Effekseer URP:** Seluruh `VFX_Sockets` harus tetap `1` instance (`8` emitter) di bawah `/Dragon_Player/Visual_Dragon`, dan `enableDepth` pada `EffekseerSettings.asset` dibiarkan `0` selama `enableURPRasterPass` = `0`.
- **Jangan duplikasi `PythonProcessManager`:** Hanya `[DragonSessionHost]` (`DontDestroyOnLoad`) yang boleh memegang `PythonProcessManager` & `UDPVideoStreamReceiver`.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] Uji coba manual langsung dengan webcam di Unity Play Mode untuk memvalidasi kenyamanan rasa pemicu `#14 Propeller Dash` pasca penyetelan ulang sensitivitas.
- [ ] Lanjutkan agenda pengembangan aktif di `[[laptop-b/Tasks]]`.

---

**Navigasi Obsidian:** [[00 - Overview]] · [[laptop-b/konteks|konteks]] · [[laptop-b/prd|prd]] · [[laptop-b/GDD_Pulau|GDD_Pulau]] · [[laptop-b/Tasks|Tasks]] · [[laptop-b/Devlog|Devlog]]
