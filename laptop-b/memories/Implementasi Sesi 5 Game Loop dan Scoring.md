---
title: "Implementasi Sesi 5 Game Loop dan Scoring"
type: session-memory
tags:
  - session-memory
  - laptop-b
  - session-5
  - game-manager
  - scoring
  - progression
  - feature-to-ui-sync
date: 2026-11-21
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[laptop-a/arsitektur_sesi_5_gameloop_dan_scoring]]"
  - "[[laptop-a/prompt_eksekusi_sesi_5_gameloop_dan_scoring_laptop_b]]"
  - "[[laptop-a/mekanisme_tantangan_desa]]"
  - "[[laptop-a/panduan_screenshot_mandiri_dan_laporan]]"
  - "[[Implementasi Sesi 4 Tantangan Desa]]"
  - "[[Implementasi Sesi 4B VFX dan Atmosfer]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
---

# Implementasi Sesi 5 Game Loop dan Scoring

## 1. Tujuan & Konteks Sesi
- Mengimplementasikan **Sesi 5 Dasar: Pengontrol Game Loop (`GameManager.cs`), Timer Countdown Awal (`3.. 2.. 1.. TERBANG!`), Pembakuan Formula 3 Bintang Berbasis Progres Global (`33.4% / 66.7% / 100%`), Evaluasi `TimeUp`, & Progresi Gembok Level + Free Play** sesuai `[[laptop-a/arsitektur_sesi_5_gameloop_dan_scoring]]` dan `[[laptop-a/prompt_eksekusi_sesi_5_gameloop_dan_scoring_laptop_b]]`.
- Menyelaraskan **Feature-to-UI Sync** pada `SCREEN 00 (MissionsPanelUI)`, `SCREEN 02 (InGameFlightHUD)`, dan `SCREEN 04 (FinishModalUI)`, serta mengambil tangkapan layar mandiri untuk Laporan Capstone.

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Formula Bintang Global Vitality & Persistensi `PlayerPrefs` (`DragonGameSettings.cs`):**
   - `CalculateStars(float globalVitality01)` dibakukan murni dari **Progres Vitalitas Global** (bukan sisa waktu):
     - `>= 1.000f (100%)` $\rightarrow$ **`3` Bintang**
     - `>= 0.667f (66.7%)` $\rightarrow$ **`2` Bintang**
     - `>= 0.334f (33.4%)` $\rightarrow$ **`1` Bintang**
     - `< 0.334f` $\rightarrow$ **`0` Bintang (Gagal)**
   - Mematikan `UnlockAllStagesForTesting = false` untuk progresi normal dan menyimpan ke `PlayerPrefs` (`Fanamir_Stage01..03_Unlocked`, `Fanamir_FreePlay_Unlocked`, `Fanamir_Stage01..03_BestStars`, `Fanamir_Stage01..03_BestTime`). Meraih $\ge 1$ bintang pada Stage $N$ otomatis membuka Stage $N+1$ (dan menyelesaikan Stage 3 dengan $\ge 1$ bintang membuka **Free Play Mode** `StageIndex = 4`).
2. **State Machine `GameManager.cs`, Countdown 3 Detik, & Jeda Narasi (`GameManager.cs`, `DragonFlightController.cs`, `VillageZoneController.cs`):**
   - Siklus `GameState`: `LevelInit` $\rightarrow$ `CountdownStart` (`3.. 2.. 1.. TERBANG!`, `DragonFlightController.ControlsLocked = true`) $\rightarrow$ `Playing` $\leftrightarrow$ `Paused` $\rightarrow$ `TimeUp` / `Victory`.
   - Batas waktu per stage: **Stage 1 (`120s` / `02:00`)**, **Stage 2 (`240s` / `04:00`)**, **Stage 3 (`360s` / `06:00`)**, dan **Free Play (`Stage 4`, tanpa timer / eksplorasi bebas 3 desa)**.
   - Timer misi otomatis **PAUSE** selama dialog narasi `PF_QuestNarrativeBox` (`SCREEN 08`) tampil dan **RESUME** setelah narasi selesai.
   - `VillageZoneController.ComputeGlobalVitality()` menghitung progres global secara kontinu (termasuk kontribusi parsial desa yang sedang dikerjakan), sehingga pada Stage 2 (1 desa pulih = `50%` = `1` Bintang, desa kedua $+16.7\%$ = `66.7%` = `2` Bintang) dan Stage 3 (`1/2/3` desa = `33.3%/66.7%/100%`).
   - Saat waktu habis (`TimeUp 00:00`), pemain dengan progres global $\ge 33.4\%$ ($\ge 1$ bintang) tetap dinyatakan **LULUS (`isStagePassed = true`)** dan membuka stage berikutnya; hanya jika $< 33.4\%$ (`0` bintang) tampil **WAKTU HABIS (Gagal)**.
3. **Sinkronisasi UI (`FinishModalUI.cs`, `MissionsPanelUI.cs`, `InGameFlightHUD.cs`):**
   - `FinishModalUI.cs` (`SCREEN 04`): animasi pengisian bintang sekuensial `0.40s` per bintang dengan `WaitForSecondsRealtime` (`Time.timeScale == 0f`), statistik `Waktu Bermain (MM:SS)`, `Desa Pulih (X / N)`, `VITALITAS GLOBAL (%)`, serta tombol `LEVEL BERIKUTNYA` / `MODE FREE PLAY` (pasca Stage 3), `COBA LAGI`, dan `KEMBALI KE LOBBY`.
   - `MissionsPanelUI.cs` (`SCREEN 00`): menampilkan bintang terbaik (`BestStars`), rekor waktu (`BEST MM:SS`), gembok dinamis Stage 02/03, serta tombol `Btn_FreePlay` (`FREE PLAY · LOCKED` $\rightarrow$ `FREE PLAY · OPEN`).
   - `InGameFlightHUD.cs` (`SCREEN 02`): menambahkan `CountdownOverlay` (`SetCountdownDisplay`), indikator `FREE PLAY` pada `MissionCapsule`, serta menghapus seluruh `// TODO [SYNC-GAMEPLAY]`.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Unity Gameplay, UI & Editor (`AR Dragon Sky Guardian/`):**
  - `Assets/_Scripts/Gameplay/GameManager.cs` *(Baru)*, `Assets/_Scripts/Gameplay/VillageZoneController.cs`, `Assets/_Scripts/Controllers/DragonFlightController.cs`.
  - `Assets/_Scripts/UI/DragonGameSettings.cs`, `FinishModalUI.cs`, `MissionsPanelUI.cs`, `InGameFlightHUD.cs`, `CalibrationPanelUI.cs`.
  - `Assets/Editor/UISceneAndPrefabBuilder.cs`, `Assets/Editor/Session4_VillageBuilder.cs`, `Assets/Editor/Session5GameLoopVerificationRunner.cs` *(Baru)*.
  - Prefab & Scene diperbarui: `PF_FinishModal.prefab`, `MainMenu_Lobby.unity`, `MainIsland_Gameplay.unity`, `Test_Dragon_Flight.unity`.
- **Hasil Verifikasi Play Mode (`Unity 6000.3.7f1`) & Screenshot Mandiri:**
  - `Session5GameLoopVerificationRunner.RunPlayModeVerificationAsync()`: **`ALL SESSION 5 GAME LOOP, COUNTDOWN, NARRATIVE FREEZE, GLOBAL VITALITY STARS, UNLOCK & FINISH MODAL CHECKS PASSED (100%)`** (`0` console errors).
  - Non-regresi `MomentumFlightVerificationRunner` & `Session4VerificationRunner` di `MainIsland_Gameplay.unity`, serta verifikasi `MainMenu_Lobby.unity`: **`100% PASS`** (`0` errors, `0` warnings).
  - Screenshot mandiri: `docs/figures/raw_screenshots/SS_Sesi5_FinishModal_Victory.png` & `docs/figures/raw_screenshots/SS_Sesi5_Lobby_StageProgress.png`.

---

## 4. Detail Penting yang Harus Diingat
- **Aturan Kelulusan `TimeUp`:** Kelulusan level ditentukan oleh `earnedStars >= 1` (`globalVitality >= 0.334f`), **bukan** semata-mata semua desa harus `100%` sebelum timer habis.
- **Animasi UI Saat `Time.timeScale == 0f`:** Seluruh coroutine pada `FinishModalUI` wajib menggunakan `WaitForSecondsRealtime` / `Time.unscaledDeltaTime` karena game di-pause (`Time.timeScale = 0f`) ketika modal selesai tampil.
- **Mode Free Play (`StageIndex = 4`):** Mengaktifkan ke-3 desa tanpa batas waktu countdown dan terbuka otomatis di `MissionsPanelUI` begitu pemain meraih $\ge 1$ bintang di Stage 03.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] **Sesi 5 Lanjutan (`AudioManager` & Build Windows Standalone):** Implementasi sistem BGM/SFX (`AudioManager` / `AudioMixer`, subscribe ke `DragonGameSettings.OnBgmVolumeChanged` & `OnSfxVolumeChanged`, hapus `// TODO [SYNC-AUDIO]`), serta paket build `.exe` Windows standalone + `start_game.bat`.

---

**Navigasi Obsidian:** [[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[Implementasi Sesi 4B VFX dan Atmosfer]] · [[laptop-a/arsitektur_sesi_5_gameloop_dan_scoring]] · [[laptop-a/prompt_eksekusi_sesi_5_gameloop_dan_scoring_laptop_b]]
