---
title: Laptop B — Patch 1 Planning
type: planning
tags:
  - planning
  - laptop-a
  - laptop-b
  - patch-1
  - cv-control
  - ui-polish
  - sesi-4
  - sesi-5
date: 2026-11-15
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[laptop-b/Tasks]]"
  - "[[laptop-b/Devlog]]"
  - "[[kontrak_integrasi_ui_dan_progress]]"
---

# 🛠️ Laptop B — Patch 1 Planning

> Daftar perbaikan, penyempurnaan pengalaman pengguna, dan target eksekusi lanjutan untuk **Laptop B** pada proyek **`Fanamir: The Sky Guardian`**.

---

## 📋 Daftar Perbaikan & Pembaruan

### 1. Improve CV & Control (Kontrol Lebih Smooth)
- Menyempurnakan responsivitas dan kehalusan transisi antar-gestur pada pipeline Computer Vision (`active_cv_sender.py`, `motion_recognizer.py`, `pose_stabilizer.py`) serta pemetaan fisika penerbangan di Unity (`UDPPoseReceiver.cs`, `DefaultFlightInputMapper.cs`, `DragonFlightController.cs`).
- Fokus: mengurangi jitter/patah-patah saat peralihan kecepatan, kemudi belok (*lean / arm tilt*), dan transisi elevasi sehingga pengalaman terbang **Fanamir** terasa lebih natural dan nyaman.

### 2. Rasio Kamera Dinamis & Auto-Default / Last-Selected Camera
- **Rasio Kamera Dinamis / Selectable:** Menyesuaikan tampilan viewport & PIP kamera agar mendukung perbedaan rasio aspek dari berbagai perangkat input kamera (misalnya `16:9`, `4:3`, dsb.) secara dinamis atau melalui opsi *selectable* (detail teknis dibahas pada sesi di Laptop B).
- **Camera Default / Save Last Selected:** Menyediakan pemilihan kamera default (kamera bawaan laptop) dan/atau menyimpan pilihan kamera terakhir (*last selected camera* via `PlayerPrefs` / config) sehingga pemain tidak perlu memilih ulang kamera dari nol setiap kali memulai permainan.

### 3. Improve Animasi UI (Lebih Smooth)
- Memperhalus seluruh transisi dan animasi antarmuka (transisi tab Lobby, modal pop-up, *fade/scale* panel, *pose card* MRU, *Quest Narrative Box*, dan elemen HUD) agar terasa lebih responsif dan poles.

### 4. Uji Play Mode Webcam Nyata (`MainIsland_Gameplay.unity`)
- Melakukan pengujian langsung dengan webcam fisik di scene **`MainIsland_Gameplay.unity`** untuk memverifikasi:
  - Kenyamanan dan akurasi gestur **`Fly Up`**, **`Hover`**, **`Gentle Descent`**, serta manuver belok/menukik.
  - Pengalaman jelajah kepulauan 3 biome skala `2400×2400 m` (jarak pandang, ketinggian puncak Center `~165 m` / `maxAltitude = 260f`, dan performa).

### 5. Sesi 4 — `VillageZoneController.cs`, VFX Effekseer, & `Feature-to-UI Sync`
- **Pemasangan Skill VFX di Laptop B ([[rencana_skill_vfx_effekseer_laptop_b]]):**
  - Menginstal paket *Agent Skills* **`kanaworksai-hue/effekseer-vfx-skill`** (`effekseer-vfx` + `vfx-review`) via `npx skills add kanaworksai-hue/effekseer-vfx-skill` atau ke `.agents/skills/`.
  - Mengintegrasikan paket URP `EffekseerForUnity` (`https://github.com/effekseer/EffekseerForUnity.git#package`) dan menambahkan `EffekseerRendererFeature` pada Forward Renderer URP Unity 6.
  - Memproduksi dan mengintegrasikan 3 efek utama pemulihan desa (*Cleansing Wind Gust*, *WaterBreath Stream*, *Radiant Dew Rain*) serta VFX pendukung penerbangan Fanamir (*wingtip trails*, *dive wind cone*, *dash sonic ring*).
- Mengimplementasikan **`VillageZoneController.cs`**, **`Session4_VillageBuilder.cs`**, mekanisme *Auto-Hover* / *Idle Fly*, serta sistem VFX pemulihan 3 desa (*Smog Valley / Zephyria*, *Wildfire Woods / Pyrewood*, *Drought Plateau / Solaria*) mengacu pada [[mekanisme_tantangan_desa]].
- Menjalankan **Feature-to-UI Sync** penuh untuk menghidupkan elemen UI dormant:
  - `PF_QuestNarrativeBox` (`QuestNarrativeUI.ShowNarrativeSequence` — Auto-Play $\rightarrow$ update objective `ACTIVE TASK`).
  - `InGameFlightHUD` (`ResetQuestObjectiveToTravel`, `SetWaypointTarget`, `ClearWaypoint`).
  - `PF_VillageChallengeOverlay` (`VillageChallengeUI.ShowChallenge`, `UpdateProgress`, `HideChallenge`).
  - `CalibrationPanelUI.SetVillageActionHighlight` (**Pose 15**).

### 6. Sesi 5 — `GameManager.cs`, `AudioManager.cs`, `Feature-to-UI Sync`, & Build `.exe`
- Mengimplementasikan **`GameManager.cs`** (alur Stage 01–03, timer mundur, perhitungan 1–3 bintang, unlock Stage 02/03) dan **`AudioManager.cs`** (BGM & SFX).
- Menjalankan **Feature-to-UI Sync** penuh pada:
  - `InGameFlightHUD` (`SetMissionTimer`, `SetVillageCount`, `SetGlobalVitality`).
  - `FinishModalUI` (`ShowVictory`, `ShowDefeat`).
  - `MissionsPanelUI.SetStageProgress`.
  - `DragonGameSettings.OnBgmVolumeChanged` & `OnSfxVolumeChanged` (membersihkan penanda `// TODO [SYNC-GAMEPLAY]` & `// TODO [SYNC-AUDIO]`).
- Membuat build Windows standalone **`Fanamir_The_Sky_Guardian.exe`** beserta launcher **`start_game.bat`**.

---

[[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[laptop-b/Tasks]]
