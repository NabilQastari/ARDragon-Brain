---
title: "Perbaikan UI Settings dan Pose Guide"
type: session-memory
tags:
  - session-memory
  - laptop-b
  - unity-ui
  - settings
  - pose-guide
  - calibration
date: 2026-11-26
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Implementasi UI Hybrid AR Dragon]]"
  - "[[Pembaruan UI Fanamir dan Boot Flow]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[laptop-a/ui-ux/rencana_desain_ui]]"
---

# Perbaikan UI Settings dan Pose Guide

## 1. Tujuan & Konteks Sesi
- Melakukan perapihan dan perbaikan tata letak UI **Laptop B** pada panel **Settings** (`PF_SettingsPanel.prefab`), modal **Pose Guide** (`PF_PoseGuideOverlay.prefab`), dan kartu pose **Calibration** (`PF_CalibrationPanel.prefab`):
  1. Menghapus section kalibrasi/input kamera (`CAMERA INPUT & AI YOLO`) dari layar Settings karena kontrol kamera sudah terpusat di tab Calibration & overlay Pause Calibration.
  2. Memberikan *gap* yang proporsional pada tombol aksi bawah `APPLY & CLOSE` (Settings) dan `CLOSE POSE GUIDE` (Pose Guide) agar tidak berdempetan dengan bingkai dalam kartu perkamen.
  3. Mengatasi masalah teks, ikon, dan pill `SLOT 1 · ACTIVE` / `ACTIVE` yang berdempetan/menabrak bingkai tebal kartu pose di `PoseGuideOverlay` maupun `Calibration`.

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Penghapusan Section Kamera di Settings & Routing Navbar (`SettingsPanelUI.cs`, `LobbyNavigationManager.cs`, `UISceneAndPrefabBuilder.cs`):**
   - Menghapus `CameraModulePlaque` (`CAMERA INPUT & AI YOLO`, instance `PF_CameraControlModule_Full`, `CameraHintPanel`) dan binding `cameraModule` di `SettingsPanelUI.cs` serta `BuildSettingsPanelPrefab()`.
   - Mengarahkan klik `navCamBadgeButton` di navbar tab Missions (`LobbyNavigationManager.cs`) dari `LobbyTab.Settings` ke `LobbyTab.Calibration`, serta memperbarui teks state error menjadi `"CAM ERROR · BUKA CALIBRATION"`.
2. **Penyelarasan Tinggi Kartu & Gap Tombol Tutup (`SettingsCard` & `ModalCard`):**
   - **`SettingsCard` (`PF_SettingsPanel.prefab`):** Diperpendek dari `960×720 px` menjadi `960×520 px` (`y = -10`) untuk membungkus 5 baris slider dengan `Btn_ApplyAndClose` pada `y = 42` dari tepi bawah kartu (jarak `36 px` di bawah slider ke-5 & bersih di atas bingkai dalam bawah `UI_Panel_Parchment_Full_9Slice`).
   - **`ModalCard` (`PF_PoseGuideOverlay.prefab`):** Diperpendek dari `1848×890 px` menjadi `1848×854 px` (`y = -18`) dan posisi `Btn_ClosePoseGuide` (`CLOSE POSE GUIDE`) dinaikkan dari `y = 36` menjadi `y = 48` dari tepi bawah kartu (`32 px` di bawah `KeyRulesStrip`).
3. **Diagnosis & Perbaikan Margin Dalam Kartu Pose `ACTIVE`:**
   - Inspeksi piksel sprite `UI_Plaque_PoseCard_Active_9Slice.png` (`208×143`, `BorderLTRB(27, 27, 27, 32)`) menunjukkan area `0..13 px` transparan, garis bingkai tinta hitam berada pada piksel **`14..18 px`**, dan takik sudut mencapai **`~23 px`** (sedangkan `UI_Plaque_PoseCard_Default_9Slice.png` `200×133` memiliki garis dalam pada `15..16 px` dan `18..19 px` bawah).
   - Margin lama (`16–18 px`) menyebabkan ikon, pill, dan tepi kanan deskripsi menumpuk di atas bingkai hitam. Seluruh elemen dalam kartu diperlebar ke **`26 px` (kiri, atas, kanan)** dan **`24 px` (bawah)** pada `PoseGuideCard_01..15` dan `RecentPoseSlot_1..4`, serta `PoseGuideOverlayUI.RefreshAllCards()` disinkronkan (`offMax.x = showPill ? -96f : -26f`).

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Kode C# Runtime & Builder (`AR Dragon Sky Guardian/Assets/`):**
  - `_Scripts/UI/SettingsPanelUI.cs`: Hapus `using DragonWingAR.CV;`, field `cameraModule`, dan `cameraModule.SetPoseReceiver(...)` di `OnEnable()`.
  - `_Scripts/UI/LobbyNavigationManager.cs`: Arahkan `navCamBadgeButton` ke `LobbyTab.Calibration` dan perbarui teks error `"CAM ERROR · BUKA CALIBRATION"`.
  - `_Scripts/UI/PoseGuideOverlayUI.cs`: Ubah batas kanan dinamis `titleLabel` di `RefreshAllCards()` menjadi `showPill ? -96f : -26f`.
  - `Editor/UISceneAndPrefabBuilder.cs`: Perbarui `BuildAll()`, `BuildSettingsPanelPrefab()`, `BuildPoseGuideOverlayPrefab()`, dan `BuildCalibrationPanelPrefab()`.
- **Prefab UI yang Dibangun Ulang (`Assets/Prefabs/UI/`):**
  - `PF_SettingsPanel.prefab` (`SettingsCard` `960×520`, `Btn_ApplyAndClose` `y = 42`, tanpa `CameraModulePlaque`).
  - `PF_PoseGuideOverlay.prefab` (`ModalCard` `1848×854`, `Btn_ClosePoseGuide` `y = 48`, 15 kartu `PoseGuideCard_01..15` bermargin `26/24 px`).
  - `PF_CalibrationPanel.prefab` (4 kartu `RecentPoseSlot_1..4` bermargin `26/24 px`).
- **Verifikasi Play Mode (`Unity 6000.3.7f1`):**
  - `MainMenu_Lobby.unity`: Tab Settings (`960×520`, `camPlaque=False`, `APPLY & CLOSE` -> `Missions`, `NavCamBadge` -> `Calibration`), Tab Calibration (`RecentPoseSlot_1` `SLOT 1 · ACTIVE` bersih), & Pose Guide (`ModalCard 1848×854`, `#08 GLIDE (T-POSE) ACTIVE` & 14 kartu lain bersih, `closedByFooter=True, closedByEsc=True`) — `0` console errors, `0` console warnings.
  - `MainIsland_Gameplay.unity`: Sub-panel `Pause -> Settings` terbuka dan tertutup normal oleh `APPLY & CLOSE` (`0` console errors, `0` console warnings).

---

## 4. Detail Penting yang Harus Diingat
- Pada sprite `UI_Plaque_PoseCard_Active_9Slice.png` (`208×143`, `BorderLTRB(27,27,27,32)`), bingkai tinta hitam berada hingga piksel `18` (sudut bertakik `~23 px`). Gunakan margin isi kartu minimal **`26 px`** (kiri/atas/kanan) dan **`24 px`** (bawah) agar elemen tidak menyentuh bingkai.
- Pada sprite `UI_Panel_Parchment_Full_9Slice.png`, tombol di tepi bawah kartu (`Btn_ApplyAndClose`, `Btn_ClosePoseGuide`) memerlukan offset bawah minimal **`42–48 px`** agar bebas dari garis bingkai dalam dan ornamen sudut.
- **Catatan Kronologis Vault:** Setelah pekerjaan UI ini (`2026-11-25`), sesi Laptop B lainnya pada `2026-11-26` menambahkan opsi `Row_InputControlMode` dan dual BGM dropdown (`SettingsCard` `960×656` / `960×716`) pada `PF_SettingsPanel.prefab` (lihat `[[Devlog]]`).

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- Tugas aktif Laptop B terbaru merujuk `[[Tasks]]` (saat ini tinggal **Build Windows Standalone & Launcher `start_game.bat`**, uji live webcam pengguna, serta finalisasi laporan/screenshot jika diperlukan).

---
**Navigasi Obsidian:** [[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[Implementasi UI Hybrid AR Dragon]] · [[Pembaruan UI Fanamir dan Boot Flow]]
