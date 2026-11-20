---
title: Panduan Screenshot Mandiri & Manajemen Aset Laporan (Laptop B)
tags:
  - guidelines
  - screenshot
  - capstone-report
  - laptop-b
  - automation
owner: Laptop A
updated: 2026-11-19
status: active
---

# 📸 Panduan Screenshot Mandiri & Manajemen Aset Laporan untuk Laptop B

> **Tujuan:** Panduan resmi bagi asisten AI di **Laptop B** untuk melakukan tangkapan layar (*screenshot*) secara mandiri/otomatis di setiap akhir sesi pengujian atau implementasi fitur, serta menampungnya di folder terstruktur sebagai bahan pembaruan Laporan Capstone Project ([`Laporan_Capstone_Fanamir.docx`](file:///e:/Semester%205/Game/AR%20Dragon/docs/Laporan_Capstone_Fanamir.docx)).

---

## 📁 1. Struktur Folder Penampung (*Staging*)

Seluruh screenshot yang diambil oleh Laptop B disimpan ke folder penampung berikut:
```
docs/figures/raw_screenshots/
```
*(Jika folder belum ada, buat folder tersebut terlebih dahulu).*

### Format Penamaan File:
Gunakan format deskriptif berbasis sesi dan fitur:
- `SS_Sesi4_Zephyria_InGame.png`
- `SS_Sesi4_Pyrewood_WaterBreath.png`
- `SS_Sesi4_BillboardMarker_150m.png`
- `SS_CV_CleanPreview_FPS.png`
- `SS_CV_Webcam_3PointFace.png`
- `SS_Unity_SceneView_VillageCylinders.png`

---

## 🛠️ 2. Metode Pengambilan Screenshot Mandiri oleh Agent di Laptop B

Agent di Laptop B dapat memilih metode otomatis berikut sesuai kebutuhan:

### A. Screenshot In-Game / Gameplay dari Unity (Otomatis & Resolusi Penuh)
Saat menjalankan pengujian via *Play Mode* atau *Test Runner*, gunakan fungsi bawaan Unity C#:
```csharp
// Contoh pemanggilan di skrip pengujian atau helper C#
string folderPath = System.IO.Path.Combine(Application.dataPath, "../docs/figures/raw_screenshots");
if (!System.IO.Directory.Exists(folderPath)) System.IO.Directory.CreateDirectory(folderPath);

string filePath = System.IO.Path.Combine(folderPath, "SS_Sesi4_Zephyria_InGame.png");
ScreenCapture.CaptureScreenshot(filePath);
Debug.Log($"[Screenshot] Saved: {filePath}");
```
*Kelebihan:* Menghasilkan gambar jernih tanpa window bar, langsung dari render kamera Unity.

---

### B. Screenshot Tampilan CV & Webcam Preview (Dari Python OpenCV)
Untuk memotret hasil tracking webcam atau tampilan *Clean CV Preview*:
Tambahkan perintah `cv2.imwrite` pada saat frame stabil atau dengan tombol pintasan di script Python (`ARDragonSkyGuardian_CV`):
```python
import os
import cv2

output_dir = os.path.abspath("../docs/figures/raw_screenshots")
os.makedirs(output_dir, exist_ok=True)

# Simpan frame webcam tracking / clean preview
cv2.imwrite(os.path.join(output_dir, "SS_CV_Webcam_3PointFace.png"), annotated_frame)
cv2.imwrite(os.path.join(output_dir, "SS_CV_CleanPreview_FPS.png"), clean_frame)
print("[CV Screenshot] Saved snapshot successfully!")
```

---

### C. Screenshot Desktop / Unity Editor Window via Terminal (OS-Level)
Agent di Laptop B dapat menjalankan perintah satu baris PowerShell / Python untuk memotret layar kerja atau Unity Scene View:
```powershell
# Menggunakan Python Pillow
python -c "from PIL import ImageGrab; ImageGrab.grab().save('docs/figures/raw_screenshots/SS_Unity_SceneView_VillageCylinders.png')"
```

---

## 🎯 3. Daftar 5 Gambar Placeholder yang Dibutuhkan untuk Laporan

Dalam draf laporan [Laporan_Capstone_Fanamir.docx](file:///e:/Semester%205/Game/AR%20Dragon/docs/Laporan_Capstone_Fanamir.docx), saat ini terdapat **5 kotak placeholder** yang menunggu tangkapan layar langsung dari Laptop B:

| Target Gambar di Laporan | Deskripsi Visual yang Dibutuhkan | Metode Capture yang Disarankan | Rekomendasi Nama File di `raw_screenshots/` |
|---|---|---|---|
| **Gambar 3.3** | Visualisasi pelacakan 3 titik wajah (*Hidung, Dagu, Mulut*) pada webcam pemain saat mendongak/menunduk | Script Python OpenCV (`cv2.imwrite`) | `SS_CV_Webcam_3PointFace.png` |
| **Gambar 4.1** | Tampilan *Clean CV Preview* webcam (hanya ada overlay FPS di pojok kiri atas, rasio 320×240/320×180) | Script Python OpenCV atau jendela PIP Unity | `SS_CV_CleanPreview_FPS.png` |
| **Gambar 4.3** | Tampilan *Scene View* Unity 6 yang memperlihatkan sebaran silinder interaksi 3 desa di kepulauan | PowerShell / Python `ImageGrab` saat Unity Editor aktif di Scene View | `SS_Unity_SceneView_VillageCylinders.png` |
| **Gambar 4.4** | Tampilan in-game naga terbang mendekati desa dengan *World-Space Billboard Marker* melayang aktif ($\le 150\text{ m}$) | Unity C# `ScreenCapture.CaptureScreenshot` | `SS_InGame_BillboardMarker_150m.png` |
| **Gambar 4.7** | Foto dokumentasi pengujian fisik (pemain berdiri di depan webcam laptop mengendalikan naga) | Foto manual via kamera ponsel pengguna | `Foto_Pengujian_Fisik_Tester.jpg` |

---

## 🔄 4. SOP Pasca-Sesi untuk Laptop B

Setiap kali menyelesaikan sebuah sesi pengembangan atau pengujian fitur:
1. **Ambil Tangkapan Layar:** Jalankan salah satu metode screenshot di atas untuk merekam fitur/keadaan gameplay terbaru.
2. **Simpan ke Staging:** Pastikan file tersimpan di `docs/figures/raw_screenshots/`.
3. **Catat di Devlog:** Tuliskan path file screenshot yang baru diambil ke dalam `brain/laptop-b/Devlog.md`.
4. **Sinkronisasi:** File screenshot siap disematkan ke dalam laporan `.docx` pada sesi pembaruan laporan berikutnya!
