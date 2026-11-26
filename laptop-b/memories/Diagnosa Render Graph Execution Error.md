---
title: "Diagnosa Render Graph Execution Error"
type: session-memory
tags:
  - session-memory
  - laptop-b
  - unity-editor
  - render-graph
  - effekseer
  - troubleshooting
date: 2026-11-26
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[Revisi VFX Skybox dan Bouncy Landing]]"
---

# Diagnosa Render Graph Execution Error

## 1. Tujuan & Konteks Sesi
- Pengguna melaporkan error berulang di Console Unity Editor (`6000.3.7f1`) dari tangkapan layar:
  ```text
  [15:31:03] [UDPPoseReceiver] Berhasil mendengarkan port UDP 5052.
  [15:31:14] Render Graph Execution error
  UnityEngine.GUIUtility:ProcessEvent (int,intptr,bool&)
  [15:31:14] ArgumentNullException: Value cannot be null.
  Parameter name: value
  ```
- Menganalisis akar penyebab exception pada Render Graph URP Unity 6, memastikan integritas project game, dan mendokumentasikan penjelasannya ke memori Obsidian Brain Laptop B tanpa mengubah file di proyek game (sesuai instruksi: *"cuman buat memory jangan sentuh project game"*).

---

## 2. Keputusan Desain / Arsitektur / Teknis

### A. Akar Masalah (Root Cause Analysis)
1. **Asal Eksekusi (`GUIUtility:ProcessEvent`):**
   - Error terjadi saat event input / repaint GUI Editor (interaksi mouse pada Scene View atau Game View) memicu rendering frame URP Render Graph.
2. **Karakteristik Unity 6 URP Render Graph:**
   - Unity 6 (`6000.3.7f1`) mengaktifkan Render Graph secara default.
   - Di dalam Render Graph, setiap pass wajib mendeklarasikan attachment secara eksplisit melalui `RasterRenderPass` atau `UnsafePass`.
3. **Mekanisme Renderer Feature Effekseer (`EffekseerURPRenderPassFeature.cs`):**
   - Pada konfigurasi default di `Assets/Resources/EffekseerSettings.asset`, properti `enableURPRasterPass` bernilai `0`.
   - Ketika `enableURPRasterPass == 0`, method `RecordRenderGraph()` jatuh ke blok `renderGraph.AddUnsafePass`:
     ```csharp
     passData.prop.colorTargetIdentifier = (RenderTargetIdentifier)passData.colorTexture;
     CoreUtils.SetRenderTarget(cmd, color);
     ```
   - Di Unity 6, saat event repaint GUI tertentu pada Scene View, `passData.colorTexture` (`TextureHandle`) yang di-cast secara eksplisit ke `RenderTargetIdentifier` dapat bernilai kosong (`null`), sehingga konstruktor internal `RenderTargetIdentifier` melempar:
     `ArgumentNullException: Value cannot be null. Parameter name: value`.

### B. Solusi yang Direkomendasikan
- **Mengaktifkan `enableURPRasterPass: 1` di `EffekseerSettings`:**
  - `EffekseerSystem.CanUseURPRasterPass` di proyek ini bernilai `true` (karena `enableDistortion = 0`, `enableDepth = 0`, dan `RendererType = Unity`).
  - Ketika `enableURPRasterPass: 1`, Effekseer menggunakan `renderGraph.AddRasterRenderPass`:
    ```csharp
    builder.SetRenderAttachment(passData.colorTexture, 0, AccessFlags.ReadWrite);
    builder.SetRenderFunc(static (PassData passData, RasterGraphContext context) => ExecuteRasterRenderGraphPass(passData, context));
    ```
  - Mode ini tidak melakukan konversi `TextureHandle` ke `RenderTargetIdentifier` dan tidak memanggil `CoreUtils.SetRenderTarget` secara manual, sehingga Render Graph Unity 6 berjalan bersih tanpa memicu `ArgumentNullException` pada `GUIUtility:ProcessEvent`.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- Sesuai instruksi tegas pengguna (*"cuman buat memory jangan sentuh project game"*), **tidak ada perubahan yang diaplikasikan pada file workspace proyek game** (`AR Dragon Sky Guardian/` dan `ARDragonSkyGuardian_CV/` tetap utuh 100%).
- Penanganan difokuskan secara eksklusif pada dokumentasi analisis teknis dan arsip memori di `brain/laptop-b/`.

---

## 4. Detail Penting yang Harus Diingat
- **Status Proyek:** Error ini tidak memengaruhi kompilasi C# (`compilationFailed: false`), listener UDP (`port 5052`), maupun logika fisika/animasi gameplay.
- **Langkah User di Editor (Jika Ingin Membersihkan Warning):**
  1. Di Unity Editor, pilih asset `Assets/Resources/EffekseerSettings.asset` di Project Window.
  2. Di Inspector, centang opsi **Enable URP Raster Pass**.
  3. Klik tombol **Clear** di Console.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- Tetap pantau kestabilan frame rate pada Scene View dan Game View.
- Lanjutkan persiapan build Windows Standalone pada tahapan berikutnya.

---
**Navigasi Obsidian:** [[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[Revisi VFX Skybox dan Bouncy Landing]]
