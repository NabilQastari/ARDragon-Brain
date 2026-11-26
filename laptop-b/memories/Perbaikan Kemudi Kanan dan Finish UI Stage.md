---
title: "Perbaikan Kemudi Kanan dan Finish UI Stage"
type: session-memory
tags:
  - session-memory
  - laptop-b
  - cv-steering
  - calibration
  - stage-division
  - finish-ui
date: 2026-11-26
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Implementasi Sesi 4 Tantangan Desa]]"
  - "[[Implementasi Sesi 5 Game Loop dan Scoring]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[laptop-a/gameplay-mechanics/mekanisme_tantangan_desa]]"
---

# Perbaikan Kemudi Kanan dan Finish UI Stage

## 1. Tujuan & Konteks Sesi
- Mengaudit & menyelesaikan tindak lanjut pasca-implementasi **Sesi 4** (`[[Implementasi Sesi 4 Tantangan Desa]]`) dan sinkronisasi catatan `brain/`:
  1. Memperbaiki bug **kontrol kemudi naga stuck belok kanan terus** saat menggunakan modul Computer Vision.
  2. Memperbaiki masalah **3 tantangan masih tergabung di Level 1** (menegakkan pembagian `Lvl 1 = 1 desa`, `Lvl 2 = 2 desa`, `Lvl 3 = 3 desa`).
  3. Menghubungkan alur penyelesaian seluruh misi stage agar memunculkan **Finish UI (`PF_FinishModal` / `FinishModalUI`)** secara otomatis beserta transisi `NEXT LEVEL`, `REPLAY`, dan skor bintang.

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Penyebab & Solusi Bug Stuck Belok Kanan (`ARDragonSkyGuardian_CV/`):**
   - **Akar Masalah:** Berkas runtime `ARDragonSkyGuardian_CV/player_profile.json` tersimpan dengan nilai korup/ekstrem (`shoulder_width = 0.044`, `torso_length = 0.085`, `neutral_lean_bias_deg = -22.856°`). Karena `AdaptiveThresholdEngine.compute_adaptive_steering()` menghitung `corrected_angle = raw_lean_angle_deg - self.neutral_bias`, postur tegak (`0°`) berubah menjadi `+22.856°` dan mengirim `steering_input = +1.0` (`LEAN_RIGHT`) terus-menerus (bahkan saat condong kiri `-15°` tetap `+0.30` kanan).
   - **Guard Berlapis di `player_calibrator.py`:** Menambahkan `AdaptiveThresholdEngine.is_profile_valid()` (`shoulder_width >= 0.07`, `torso_length >= 0.11`, `abs(neutral_lean_bias_deg) <= 12.0°`), clamp runtime `neutral_bias` pada `[-8.0°, +8.0°]`, penolakan sampel netral tidak valid di `_finalize_calibration()`, serta penolakan profil korup di `load_profile()` (otomatis jatuh ke `get_default_profile()`).
   - **Guard Oklusi Pergelangan & Fallback Pinggul:** Di `motion_recognizer.py` (`GestureStateMachine.update()`), `arm_tilt_norm` hanya dicampurkan jika kedua pergelangan tangan memiliki confidence `>= 0.20` (mencegah fallback pergelangan occluded memicu belok sepihak). Di `active_cv_sender.py`, penyebut horizontal *shoulder roll* pada fallback pinggul duduk distabilkan (`>= 0.05 * aspect_scale_x`) dan `seated_lean_deg = 0.0` bila bahu bersilang (`raw_rs_x - raw_ls_x < 0.04`).
2. **Pembagian Stage 1 / 2 / 3 & Reset Sesi Play Mode (`DragonGameSettings.cs`, `CalibrationPanelUI.cs`, `VillageZoneController.cs`):**
   - **Akar Masalah:** `DragonGameSettings.GetActiveStageIndexOrDefault(fallback = 3)` dan `VillageZoneController.defaultStageIfUnset = 3` (termasuk serialisasi di `MainIsland_Gameplay.unity` & `Session4_VillageBuilder.cs`) membuat sesi Play langsung maupun peluncuran lewat `CalibrationPanelUI` tombol `READY TO FLY` (`SelectedStageIndex == 0`) memuat Stage 3 (ketiga desa sekaligus). Selain itu `SelectedStageIndex` tidak direset saat memulai Play Mode baru sehingga nilai `3` bertahan di memori statis.
   - **Solusi:** Mengubah fallback `GetActiveStageIndexOrDefault(1)`, `defaultStageIfUnset = 1`, mengisi `SelectedStageIndex = 1` jika belum dipilih di `CalibrationPanelUI.OnReadyToFlyClicked()`, dan menambahkan `ResetStaticSessionState()` bertanda `[RuntimeInitializeOnLoadMethod(RuntimeInitializeLoadType.SubsystemRegistration)]` di `DragonGameSettings.cs`.
3. **Integrasi Finish UI & Alur Akhir Stage (`VillageZoneController.cs`, `FinishModalUI.cs`, `MissionsPanelUI.cs`):**
   - Ketika `ClearedVillageCount >= ActiveVillageCount` pada `CompleteVillageRestoration()`, naga dilepas dengan *Launch Thrust* lalu `PF_FinishModal` (`FinishModalUI.ShowVictory`) dipanggil otomatis setelah jeda selebrasi `victoryCelebrateDelay = 2.5s` (menjaga kelulusan uji terbang pasca-Zephyria `1.6s` sebelum modal membuka & men-set `Time.timeScale = 0f`).
   - Memperbarui `FinishModalUI.cs` (`hasNextLevel` menyembunyikan `Btn_NextLevel` di Stage 3, `HandleNextLevelClicked()` melanjutkan `Stage 1 -> Stage 2 -> Stage 3`, serta pemulihan `Time.timeScale = 1f` pada `HideModal()`/`OnDestroy()`/`HandleRetryClicked()`/`HandleBackToLobbyClicked()`), serta menampilkan bintang tersimpan + info durasi di `MissionsPanelUI.cs`.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Python CV (`ARDragonSkyGuardian_CV/`):**
  - Hapus berkas runtime korup `player_profile.json`.
  - Edit `player_calibrator.py`, `motion_recognizer.py`, `active_cv_sender.py`.
- **Unity C# & Scene (`AR Dragon Sky Guardian/`):**
  - Edit `Assets/_Scripts/UI/DragonGameSettings.cs`, `CalibrationPanelUI.cs`, `MissionsPanelUI.cs`, `FinishModalUI.cs`.
  - Edit `Assets/_Scripts/Gameplay/VillageZoneController.cs`.
  - Edit `Assets/Editor/Session4_VillageBuilder.cs` & `Assets/Editor/Session4VerificationRunner.cs`, lalu jalankan menu `DragonWingAR/Session 4/Build Village Zones and Markers` untuk menyimpan `Assets/_Scenes/MainIsland_Gameplay.unity` (`defaultStageIfUnset: 1`, `finishModal` terhubung).
- **Verifikasi yang Dijalankan:**
  - **Python CV:** Uji regresi profil korup & oklusi pergelangan (`REGRESSION_CHECKS_PASS`) + 6 suite validasi (`test_phase4_validation.py`, `test_phase5_validation.py`, `test_phase6_final_session.py`, `test_all_17_poses_alignment.py`, `test_momentum_and_control_revision.py`, `test_tahap1_face_tracking.py`) **100% PASS**.
  - **Unity Play Mode (`6000.3.7f1`):** `InitialBoot: stage=1, count=1`, `Session4VerificationRunner` (`100% PASS` termasuk `PF_FinishModal` tampil otomatis usai Stage 1 dan jumlah desa Stage `1/2/3`), `MomentumFlightVerificationRunner` (`100% PASS`), `FinishModalUI` `Btn_NextLevel` (`Stage 1 -> Stage 2`, `Btn_NextLevel` nonaktif di Stage 3), dan `MainMenu_Lobby.unity` Stage 02 (`LoadedStage=2, Villages=2`) dengan `0` compilation errors.

---

## 4. Detail Penting yang Harus Diingat
- Jangan biarkan `ARDragonSkyGuardian_CV/player_profile.json` hasil kalibrasi menyimpang (`shoulder_width < 0.07`, `torso_length < 0.11`, atau `|neutral_lean_bias_deg| > 12°`) lolos dari `AdaptiveThresholdEngine.is_profile_valid()`, karena bias netral langsung menggeser titik nol kemudi di setiap frame UDP `5052`.
- `SubsystemRegistration` pada `DragonGameSettings.ResetStaticSessionState()` berjalan hanya saat Play Mode / aplikasi dimulai (bukan saat `LoadScene`), sehingga aman mereset state antarsesi Editor sambil mempertahankan `SelectedStageIndex` saat transisi `MainMenu_Lobby` $\leftrightarrow$ `MainIsland_Gameplay` atau `NEXT LEVEL`.
- **Konteks Kronologis Vault:** Timer awal pada patch ini (`120s/240s/360s`) dan logika finish di `VillageZoneController` kemudian diperluas pada sesi-sesi berikutnya di `Devlog.md` (`GameManager.cs`, timer `300s/600s/900s`, Sesi 4B VFX, Animasi Naga, Audio, & Skybox — lihat `[[Implementasi Sesi 5 Game Loop dan Scoring]]`).

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- Tugas aktif Laptop B terbaru merujuk `[[Tasks]]` (saat ini tinggal **Sesi 5 Lanjutan — Build Windows Standalone & Launcher `start_game.bat`**, uji live webcam pengguna, serta pengambilan screenshot laporan bila masih diperlukan).

---
**Navigasi Obsidian:** [[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[Implementasi Sesi 4 Tantangan Desa]] · [[Implementasi Sesi 5 Game Loop dan Scoring]]
