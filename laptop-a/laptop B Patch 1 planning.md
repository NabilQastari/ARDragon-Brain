---
title: Laptop B — Patch 1 Planning & Checklist Pra-Sesi 4
type: planning
tags:
  - planning
  - laptop-a
  - laptop-b
  - patch-1
  - cv-control
  - camera-aspect-ratio
  - ui-polish
  - sesi-4
date: 2026-11-16
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[laptop-b/Tasks]]"
  - "[[laptop-b/Devlog]]"
  - "[[mekanisme_tantangan_desa]]"
  - "[[kontrak_integrasi_ui_dan_progress]]"
  - "[[rencana_skill_vfx_effekseer_laptop_b]]"
---

# 🛠️ Laptop B — Patch 1 Planning & Checklist Pra-Sesi 4

> Panduan kerja terfokus untuk **Laptop B** sebelum memulai implementasi mekanisme tantangan desa (Sesi 4). Dokumen ini memprioritaskan 2 fokus utama: **(1) Penanganan Kamera Dinamis & Navigasi In-Game ke Kalibrasi**, dan **(2) Validasi 15 Pose & Finalisasi Kontrol Terbang Fanamir**, serta merangkum checklist perbaikan pra-tantangan desa.

---

## 🎯 FOKUS 1: Sistem Kamera Dinamis & Alur Navigasi In-Game

### 1. Rasio Kamera Dinamis (Horizontal 16:9, 4:3, & Vertikal 9:16)
* **Tantangan:** Perangkat kamera pemain bervariasi:
  - Webcam bawaan laptop (kebanyakan `16:9` atau `4:3`).
  - Webcam eksternal USB (`16:9` `1080p` / `720p`).
  - Kamera ponsel via aplikasi streaming seperti Iriun / DroidCam / Camo yang sering kali berorientasi **Vertikal (`9:16` / `3:4`)**.
* **Solusi di Python (`camera_stream.py` & `active_cv_sender.py`):**
  - Baca resolusi aktual input frame kamera (`actual_w = cap.get(cv2.CAP_PROP_FRAME_WIDTH)`, `actual_h = cap.get(cv2.CAP_PROP_FRAME_HEIGHT)`).
  - Normalisasi koordinat keypoint tubuh COCO harus berbasis dimensi aktual tanpa di-*stretch* paksa.
  - Untuk frame vertikal (`h > w`): pastikan YOLOv8n-pose melakukan padding *letterbox* simetris saat inferensi sehingga proporsi jarak bahu-pinggul dan elevasi tangan tidak mengalami distorsi vertikal.
* **Solusi di Unity (`InGameFlightHUD.cs`, `CalibrationPanelUI.cs`, & `RawImage` Viewport):**
  - Pasang komponen **`UnityEngine.UI.AspectRatioFitter`** pada GameObject `RawImage` webcam:
    - Mode: `AspectRatioFitter.AspectMode.FitInParent` (atau `EnvelopeParent` pada panel cermin berbingkai).
    - Nilai `aspectRatio` di-update dinamis saat tekstur webcam diterima:
      ```csharp
      if (webcamTexture != null && webcamTexture.height > 0)
      {
          aspectRatioFitter.aspectRatio = (float)webcamTexture.width / webcamTexture.height;
      }
      ```
  - Hasil: Kamera vertikal akan tampil rapi dengan pillarbox (bebas distorsi gepeng/tertarik), dan deteksi pose tetap akurat 100%.

### 2. Perbaikan Pemilihan Kamera di In-Game (Tidak Langsung di HUD)
* **Masalah:** Mengganti input kamera secara langsung dari HUD saat naga sedang terbang aktif (`MainIsland_Gameplay.unity`) sangat mengganggu konsentrasi pemain dan rawan menyebabkan stutter/freeze pada streaming UDP.
* **Perbaikan Alur:**
  - **Sembunyikan Dropdown Kamera dari HUD In-Game:**
    - Panel kanan bawah in-game HUD (`PIP_CameraControlsCard`) hanya menampilkan layar **PIP Viewport kecil** + status dot hijau (`CV: LIVE`) tanpa dropdown pemilih kamera dan tanpa tombol `Scan/Matikan`.
  - **Alur Ganti Kamera via Pause Menu (Tanpa Back to Lobby):**
    - Saat in-game, pemain menekan tombol **`ESC`** (atau tombol pause di HUD).
    - Muncul modal **`PF_PauseModal` (`SCREEN 06`)** dan permainan masuk kondisi **PAUSE** (`Time.timeScale = 0`).
    - Pada `PauseModalUI`, sediakan tombol:
      1. **`RESUME`** $\rightarrow$ Kembali terbang langsung di posisi naga saat itu.
      2. **`CALIBRATION & CAMERA`** $\rightarrow$ Membuka panel overlay Kalibrasi (`SCREEN 01`) secara langsung di atas scene in-game (tanpa me-reload scene / tanpa keluar ke Lobby). Di panel ini pemain bisa:
         - Memilih perangkat kamera (Webcam Laptop vs External/Mobile Cam).
         - Menguji preview cermin skeleton & 3D Fanamir.
         - Melakukan re-kalibrasi (`RECALIBRATE [C]`).
         - Tekan tombol `BACK TO PAUSE` / `ESC` untuk kembali ke Pause Modal, lalu `RESUME` ke penerbangan.
      3. **`SETTINGS`** $\rightarrow$ Membuka modal pengaturan volume BGM/SFX & slider `Text Auto-Next`.
      4. **`BACK TO LOBBY`** $\rightarrow$ Keluar ke menu utama.

### 3. Kamera Default & Penyimpanan Pilihan Terakhir (*Persistence*)
* Simpan nama perangkat kamera terakhir yang berhasil dipilih pemain ke `PlayerPrefs`:
  ```csharp
  PlayerPrefs.SetString("DragonWingAR_LastCameraDevice", selectedDeviceName);
  PlayerPrefs.Save();
  ```
* Saat game pertama kali dibuka:
  - Jika ada `LastCameraDevice` dan perangkat tersebut terdeteksi di sistem, otomatis pilih perangkat tersebut tanpa mengharuskan pemain memilih ulang.
  - Jika belum ada, otomatis pilih kamera default indeks `0` (webcam bawaan laptop).

---

## 🦅 FOKUS 2: Validasi 15 Pose & Finalisasi Kontrol Terbang Fanamir

### 1. Pengujian 15 Pose di Play Mode Webcam Nyata (`MainIsland_Gameplay.unity`)
Lakukan pengetesan bertahap di hadapan webcam nyata untuk memvalidasi responsivitas seluruh 15 pose:

| Kategori | ID & Nama Pose | Kriteria Validasi di Play Mode |
| :--- | :--- | :--- |
| **Grounded** | `#01 Grounded Idle` | Berdiri/duduk santai, sayap naga terlipat di samping tubuh, naga diam di tanah. |
| | `#02 Grounded Walk` | Langkah pelan di tempat, naga berjalan di landasan. |
| | `#03 Grounded Look Up` | Mendongak ke atas, leher naga mengarah ke langit. |
| **Locomotion** | `#04 Takeoff` | Ayunan kepak pertama saat di tanah, naga lepas landas ke udara tanpa snap teleportasi. |
| | `#05 Lean & Arm Tilt` | Miringkan satu lengan lebih tinggi dari yang lain (atau miringkan badan), naga berbelok halus (*banking roll* $\pm 35^\circ$). |
| | `#06 Flap to Climb` | Kepakan sayap ritmik di udara, naga menanjak bertahap (*lift power* stabil tanpa lonjakan tajam). |
| | `#07 Fly Up (Pose Y)` | Angkat kedua tangan ke atas membentuk Y, naga menanjak vertikal cepat ($\uparrow +10\text{ m/s}$). |
| | `#08 Glide (T-Pose)` | Rentangkan kedua tangan mendatar membentuk T, naga meluncur stabil mendatar ($12–15\text{ m/s}$). |
| **Maneuver** | `#09 Hover` | Tangan ditekuk rileks di samping dada, naga berhenti meluncur dan melayang di tempat (*bobbing* halus $\pm 0.4\text{ m}$). |
| | `#10 Gentle Descent` | Kedua lengan lurus ke depan bawah, naga turun perlahan secara terkontrol. |
| | `#11 Fly Down Pose Y` | Alternatif pose turun cepat. |
| | `#12 Fly Down Pose W` | Alternatif pose turun sayap ditekuk W. |
| | `#13 Tuck to Dive` | Kedua tangan merapat rapat ke dada/paha, naga menukik tajam ke bawah (*Dive Boost* hingga $24–28\text{ m/s}$). |
| | `#14 Propeller Dash` | Putaran tangan cepat di depan tubuh, dorongan kecepatan maju instan (*Dash*). |
| **Village Action** | `#15 Village Action` | *Flap* (Desa Zephyria), *Open Mouth* (Desa Pyrewood), *Raise Both Hands* (Desa Solaria). |

### 2. Finalisasi Kontrol & Bug Fixing yang Ditargetkan
1. **Deadzone & Kemudi Belok (*Steering Sensitivity*):**
   - Pastikan ada sedikit deadzone tengah ($\pm 2.5^\circ$) agar naga tidak bergetar/goyah saat pemain hanya ingin terbang lurus dalam pose *Glide*.
   - Kurva belok terasa nyaman baik untuk pemain yang berdiri maupun duduk di kursi.
2. **Hysteresis Flap vs Glide:**
   - Cegah pembacaan *Flap* palsu saat pemain hanya sedikit menggerakkan tangan saat *Glide*.
   - Deteksi kepakan sayap harus tegas membaca fase *downstroke* dan *upstroke*.
3. **Sinkronisasi Animasi Sayap (`DragonWingAnimator.cs`):**
   - Pastikan 12 tulang sayap merespons mulus tanpa patah saat berpindah dari *Flap* ke *Glide*, atau dari *Glide* ke *Tuck to Dive*.
4. **Batas Ketinggian (*Ceiling & Floor*):**
   - Verifikasi naga dapat melewati puncak tertinggi kepulauan Center Peak (`Y ~ 165.85m`) dengan nyaman di bawah batas `maxAltitude = 260f`.

---

## 📋 Checklist Tambahan Pra-Sesi 4 (Sebelum Implementasi Tantangan Desa)

Sebelum masuk ke penulisan skrip `VillageZoneController.cs` dan `Session4_VillageBuilder.cs`, selesaikan checklist berikut:

- [ ] **1. Integrasi Alur Pause Modal ke Kalibrasi:**
  - Tambahkan tombol `CALIBRATION` pada `PF_PauseModal.prefab` & `PauseModalUI.cs`.
  - Hubungkan pemanggilan `LobbyNavigationManager` atau overlay kalibrasi langsung tanpa transisi scene.
- [ ] **2. AspectRatioFitter pada Seluruh RawImage Kamera:**
  - Pasang `AspectRatioFitter` di `RawImage` Kalibrasi (`SCREEN 01`) dan PIP HUD (`SCREEN 02`).
  - Uji dengan kamera vertikal (misal webcam HP / DroidCam).
- [ ] **3. Pembersihan Dropdown Kamera dari InGame HUD:**
  - Sembunyikan dropdown kamera dari HUD saat Play Mode gameplay.
- [ ] **4. Penyimpanan Last Selected Camera di PlayerPrefs:**
  - Skrip `PythonProcessManager.cs` / `UDPPoseReceiver.cs` membaca dan menyimpan nama kamera terakhir.
- [ ] **5. Verifikasi Titik Koordinat 3 Desa di `MainIsland_Gameplay.unity`:**
  - Periksa apakah titik marker berikut berada pas di atas permukaan tanah:
    - `Village_B1_SmogValley`: `(-562.40, 14.10, 274.30)` (Desa Zephyria)
    - `Village_B2_WildfireWoods`: `(739.60, 12.90, 7.80)` (Desa Pyrewood)
    - `Village_B3_DroughtPlateau`: `(39.40, 14.70, -647.60)` (Desa Solaria)
    - `Danau Biome 1 (Water Source)`: danau di sebelah Desa Zephyria untuk isi air.
    - `Hutan Terbakar (Burned Forest)`: hutan pinus di samping Desa Pyrewood.
- [ ] **6. Instalasi Skill & Plugin Effekseer VFX di Laptop B:**
  - Instal skill `kanaworksai-hue/effekseer-vfx-skill` mengacu pada [[rencana_skill_vfx_effekseer_laptop_b]].
  - Impor plugin `EffekseerForUnity` ke Unity 6 URP dan daftarkan `EffekseerRendererFeature`.
- [ ] **7. Verifikasi Navigasi HUD Waypoint Compass:**
  - Pastikan kompas di langit atas (`SCREEN 02`) dapat menunjuk arah marker desa dengan benar menggunakan formula sudut `Vector3.SignedAngle` terhadap naga.

---

[[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[mekanisme_tantangan_desa]] · [[laptop-b/Tasks]]
