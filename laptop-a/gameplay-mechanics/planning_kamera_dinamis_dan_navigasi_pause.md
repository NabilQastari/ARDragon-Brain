---
title: Planning Teknis — Sistem Kamera Dinamis & Navigasi In-Game ke Kalibrasi (Fokus 1)
type: planning
tags:
  - planning
  - laptop-a
  - laptop-b
  - camera
  - aspect-ratio
  - pause-modal
  - calibration
date: 2026-11-16
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[laptop B Patch 1 planning]]"
  - "[[kontrak_integrasi_ui_dan_progress]]"
  - "[[laptop-b/Tasks]]"
---

# 📷 Planning Teknis — Sistem Kamera Dinamis & Navigasi In-Game ke Kalibrasi (Fokus 1)

> Dokumen spesifikasi teknis dan arsitektur eksekusi untuk **Laptop B** dalam menangani:
> 1. Rasio aspek kamera dinamis (termasuk kamera vertikal `9:16` seperti DroidCam / Iriun).
> 2. Perapian HUD in-game (menghilangkan dropdown kamera langsung saat terbang).
> 3. Alur transisi dari gameplay ke Kalibrasi melalui Pause Modal (`ESC`) tanpa harus keluar ke Lobby.
> 4. Auto-persistence kamera default / pilihan terakhir via `PlayerPrefs`.

---

## 1. Arsitektur & Spesifikasi Komponen

### A. Rasio Aspek Kamera Dinamis (Horizontal 16:9, 4:3, & Vertikal 9:16 / 3:4)

#### Masalah Saat Ini:
- GameObject `RawImage` untuk webcam di **Calibration Viewport** (`SCREEN 01`) dan **In-Game PIP** (`SCREEN 02`) memiliki ukuran piksel tetap (persegi panjang horizontal).
- Jika input kamera berasal dari ponsel (misal Iriun / DroidCam / Camo) dengan rasio vertikal (`9:16` atau `3:4`), tekstur kamera akan ditarik paksa secara horizontal (*stretched/gepeng*). Hal ini membuat cermin pemain terlihat aneh dan visual tidak profesional.

#### Solusi Implementasi di Unity:
1. **Pemasangan `AspectRatioFitter`:**
   - Pada GameObject `RawImage` (misal `SkeletonMirrorImage` di Kalibrasi dan `PipCameraImage` di HUD), tambahkan komponen:
     `UnityEngine.UI.AspectRatioFitter`
   - Mode yang digunakan: **`AspectRatioFitter.AspectMode.FitInParent`** (memastikan seluruh frame kamera terlihat utuh dengan batas pillarbox/letterbox rapi di dalam bingkai plakat perkamen, tanpa terdistorsi keluar bingkai).
2. **Sinkronisasi Rasio Dinamis di C#:**
   - Saat frame pertama dari `WebCamTexture` atau `Texture2D` (UDP 5053) diterima:
     ```csharp
     public void UpdateCameraAspectRatio(int width, int height)
     {
         if (height <= 0 || _aspectRatioFitter == null) return;
         
         float ratio = (float)width / height;
         _aspectRatioFitter.aspectRatio = ratio;
     }
     ```
3. **Verifikasi Pipeline Python (`camera_stream.py` & `active_cv_sender.py`):**
   - Pastikan resolusi asli kamera dibaca via `cap.get(cv2.CAP_PROP_FRAME_WIDTH)` & `FRAME_HEIGHT`.
   - Normalisasi titik keypoint tubuh COCO (`x / w`, `y / h`) tetap berada dalam rentang `0.0 – 1.0` yang proporsional terhadap dimensi aslinya.
   - Inferensi YOLOv8n-pose harus mempertahankan rasio dengan padding letterbox standar bawaan Ultralytics tanpa distorsi geometris.

---

### B. Perapian HUD In-Game & Alur Transisi via Pause Modal

#### 1. Pembersihan Dropdown Kamera dari In-Game HUD (`SCREEN 02`):
- **Alasan:** Mengganti input kamera saat Fanamir sedang terbang aktif di kepulauan sangat rawan menyebabkan drop frame, stuttering fisika penerbangan, serta merusak imersi pemain.
- **Tindakan:**
  - Pada klaster kanan bawah HUD (`PIP_CameraControlsCard` di `InGameFlightHUD.cs`):
    - **Sembunyikan / Nonaktifkan:** Dropdown pemilih kamera, tombol `Scan`, dan tombol `Matikan`.
    - **Pertahankan:** `PIP_CameraPanel` (preview webcam kecil berbingkai `ViewfinderBrackets`) dan pill status kecil (`CV: LIVE` dot hijau / `CV: OFF` dot merah).

#### 2. Alur Transisi Pause Menu ke Kalibrasi (`In-Game -> ESC -> Calibration -> Resume`):
- **Diagram Alur Navigasi:**
  ```text
  [Gameplay Penerbangan Aktif]
              │
              ▼ (Tekan tombol ESC / Tombol Pause di HUD)
  [PF_PauseModal Aktif (Time.timeScale = 0)]
        ├─ Tombol "RESUME" ─────────────► Kembali ke Penerbangan (Unpause)
        ├─ Tombol "CALIBRATION & CAMERA" ─► Buka Overlay Kalibrasi Live
        ├─ Tombol "SETTINGS" ───────────► Buka Modal Pengaturan Volume & Teks
        └─ Tombol "BACK TO LOBBY" ──────► Keluar ke MainMenu_Lobby (Scene 0)
  ```

- **Detail Perubahan di `PauseModalUI.cs` & `PF_PauseModal.prefab`:**
  - Tambahkan tombol baru: **`Btn_Calibration`** berlabel **`CALIBRATION & CAMERA`** di antara tombol `RESUME` dan `SETTINGS`.
  - Gunakan style perkamen yang konsisten (`UI_Btn_TopChrome_Cream_9Slice`).
  - Event `OnClick`:
    ```csharp
    public void OnClickCalibration()
    {
        // 1. Sembunyikan Pause Modal sementara (alpha = 0 atau SetActive false)
        // 2. Tampilkan panel Kalibrasi in-game (bisa instance prefab PF_CalibrationPanel atau overlay)
        // 3. Set mode Kalibrasi menjadi "InGamePauseMode = true"
    }
    ```

- **Detail Penyesuaian di `CalibrationPanelUI.cs`:**
  - Saat dibuka dalam mode in-game pause:
    - Tombol pojok kiri/bawah yang biasanya bertuliskan **`BACK TO LOBBY`** secara dinamis berganti label menjadi **`BACK TO PAUSE`** (atau `RETURN`).
    - Event tombol tersebut: Menutup panel Kalibrasi dan mengaktifkan kembali `PF_PauseModal`.
    - Pemain kemudian bisa langsung menekan **`RESUME`** untuk lanjut terbang seketika dengan konfigurasi kamera baru!

---

### C. Kamera Default & Auto-Persistence (`PlayerPrefs`)

- **Kunci Penyimpanan:** `"DragonWingAR_LastCameraDevice"` (string nama perangkat) dan `"DragonWingAR_LastCameraIndex"` (integer).
- **Logika Inisialisasi:**
  ```csharp
  public string GetSavedOrDefaultCamera()
  {
      string savedDevice = PlayerPrefs.GetString("DragonWingAR_LastCameraDevice", "");
      var availableDevices = WebCamTexture.devices;
      
      if (availableDevices == null || availableDevices.Length == 0)
          return null;

      // Cek apakah perangkat yang tersimpan masih terpasang di sistem
      foreach (var dev in availableDevices)
      {
          if (dev.name == savedDevice)
              return dev.name;
      }

      // Fallback: gunakan perangkat indeks 0 (webcam bawaan laptop)
      return availableDevices[0].name;
  }
  ```
- **Logika Penyimpanan:**
  - Setiap kali pemain memilih kamera di dropdown halaman Kalibrasi, simpan nama perangkat tersebut ke `PlayerPrefs.SetString(...)` dan panggil `PlayerPrefs.Save()`.
- **Hasil:** Pemain hanya perlu memilih kamera sekali. Sesi-sesi berikutnya akan langsung otomatis menggunakan kamera tersebut saat game dimulai.

---

## 2. Checklist File yang Diperbarui di Laptop B

| File Script / Prefab | Target Perubahan |
| :--- | :--- |
| `InGameFlightHUD.cs` | Sembunyikan dropdown kamera & tombol Scan dari HUD; pasang `AspectRatioFitter` pada PIP webcam. |
| `CalibrationPanelUI.cs` | Pasang `AspectRatioFitter` pada `SkeletonMirrorImage`; dukung mode `InGamePauseMode` (tombol *Back to Pause* vs *Back to Lobby*); implementasikan auto-select kamera tersimpan. |
| `PauseModalUI.cs` | Tambahkan tombol `CALIBRATION & CAMERA`; sediakan referensi/pemanggilan ke overlay Kalibrasi. |
| `PF_PauseModal.prefab` | Penataan tombol `CALIBRATION & CAMERA` dengan visual perkamen RPG yang rapi. |
| `PythonProcessManager.cs` / `UDPPoseReceiver.cs` | Sinkronisasi resolusi kamera aktual dan parameter `--cam` / `--cam-name` dengan `PlayerPrefs`. |

---

## 3. Langkah Pengujian di Laptop B (Play Mode Verification)

1. **Uji Kamera Bawaan Laptop (16:9 / 4:3):**
   - Buka scene `MainMenu_Lobby.unity` $\rightarrow$ masuk Kalibrasi $\rightarrow$ pastikan gambar proporsional.
   - Masuk ke gameplay (`MainIsland_Gameplay.unity`) $\rightarrow$ pastikan PIP di pojok kanan bawah tampil proporsional tanpa tombol dropdown.
2. **Uji Kamera Vertikal (9:16 dari HP via DroidCam / Iriun):**
   - Hubungkan HP $\rightarrow$ buka Kalibrasi $\rightarrow$ pilih kamera HP.
   - Pastikan tampilan tidak terdistorsi (terdapat pillarbox hitam/transparan di sisi kiri-kanan secara simetris).
   - Pastikan skeleton tracking dan 15 pose tetap terdeteksi normal.
3. **Uji Alur Pause $\rightarrow$ Kalibrasi $\rightarrow$ Resume:**
   - Di tengah penerbangan, tekan `ESC`.
   - Klik `CALIBRATION & CAMERA` $\rightarrow$ Kalibrasi terbuka, naga tetap pause di posisi langit.
   - Ubah kamera atau klik `RECALIBRATE` $\rightarrow$ klik `BACK TO PAUSE` $\rightarrow$ klik `RESUME`.
   - Pastikan penerbangan lanjut lancar tanpa glitch atau reset posisi.
4. **Uji Restart Permainan:**
   - Hentikan Play Mode lalu Play kembali. Pastikan kamera yang dipilih sebelumnya langsung aktif secara otomatis tanpa harus dipilih ulang.
