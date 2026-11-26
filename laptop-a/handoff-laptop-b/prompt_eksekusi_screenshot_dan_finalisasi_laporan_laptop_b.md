---
title: Master Prompt Eksekusi Screenshot Full Unity & Finalisasi Laporan (Laptop B)
tags:
  - handoff
  - laptop-b
  - screenshot
  - agent-desktop
  - capstone-report
owner: Laptop A
updated: 2026-11-26
status: active
---

# 📸 Master Prompt: Screenshot Full Unity & Finalisasi Laporan Capstone (Laptop B)

> **Tujuan:** Panduan dan master prompt instruksi siap salin bagi AI Agent di **Laptop B** untuk mengambil 5 tangkapan layar (*screenshot*) yang menampilkan **jendela penuh Unity Editor (Full Unity Window)** menggunakan skill `agent-desktop` (atau otomasi desktop Windows), menyimpannya di `docs/figures/raw_screenshots/`, dan menginjeksinya langsung ke dalam berkas [`docs/Laporan_Capstone_Fanamir.docx`](file:///e:/Semester%205/Game/AR%20Dragon/docs/Laporan_Capstone_Fanamir.docx) menggunakan skrip [`misc/inject_screenshots_docx.py`](file:///e:/Semester%205/Game/AR%20Dragon/misc/inject_screenshots_docx.py).

---

## 🎯 1. Daftar 5 Tangkapan Layar yang Dibutuhkan

| Target Gambar di Laporan | Subjek Visual & Format Tangkapan Layar | Mode & Lokasi Pengambilan | Nama File Target di `raw_screenshots/` |
| :--- | :--- | :--- | :--- |
| **Gambar 3.3** | **Visualisasi 3 Titik Wajah (Hidung, Dagu, Mulut):** Jendela preview OpenCV / webcam saat pelacakan titik wajah aktif (menunjukkan sudut netral `-38.8°`, `HEAD_PITCH`, dan deteksi bukaan mulut). | OpenCV / Python Camera Window | `SS_CV_Webcam_3PointFace.png` |
| **Gambar 4.1** | **Clean CV Preview Kamera Port 5053:** Tampilan preview webcam bersih (hanya wireframe kerangka dwi-warna + 3 titik wajah + teks minimalis `FPS: xx` di pojok kiri atas). | Port 5053 PIP Monitor / OpenCV | `SS_CV_CleanPreview_FPS.png` |
| **Gambar 4.3** | **Penempatan Marker & 3 Desa di Hierarchy & Scene View:** **Full Jendela Unity Editor** pada scene `MainIsland_Gameplay.unity` (Edit Mode) memperlihatkan Hierarchy (`Desa_Zephyria`, `Desa_Pyrewood`, `Desa_Solaria`) dan silinder interaksi gizmo di Scene View. | **Full Window Unity Editor** (Edit Mode) | `SS_Unity_SceneView_VillageCylinders.png` |
| **Gambar 4.4** | **Indikator World-Space Billboard Marker Melayang (< 150m):** **Full Jendela Unity Editor** pada Play Mode, memperlihatkan naga Fanamir terbang mendekati desa dengan marker teks `SMOG VALLEY · 140m` melayang aktif. | **Full Window Unity Editor** (Play Mode) | `SS_InGame_BillboardMarker_150m.png` |
| **Gambar 4.7** | **Foto Dokumentasi Pengujian Pemain di Hadapan Webcam:** Foto/screenshot pengujian fisik pemain memperagakan kendali pose naga di hadapan laptop. | Foto Fisik / Kamera Webcam | `Foto_Pengujian_Fisik_Tester.jpg` *(opsional: `SS_Pengujian_Fisik_Webcam.png`)* |

---

## 🖥️ 2. Petunjuk Pengambilan Screenshot Natural Menggunakan `agent-desktop`

Sesuai arahan pengguna, pengambilan gambar Unity diusahakan **menampilkan seluruh jendela Unity (Full Window)** agar terlihat natural, autentik, dan membuktikan hasil kerja implementasi nyata di Unity 6.

### Langkah-langkah Capture via `agent-desktop`:
1. **Fokuskan Jendela Unity:**
   Gunakan perintah `agent-desktop` atau PowerShell untuk memfokuskan jendela Unity Editor:
   ```powershell
   # Contoh via agent-desktop CLI jika terpasang:
   agent-desktop focus --app "Unity"
   ```
2. **Ambil Tangkapan Layar Full Desktop / Active Window:**
   ```powershell
   # Ambil screenshot jendela aktif / desktop penuh:
   agent-desktop screenshot --path "docs/figures/raw_screenshots/SS_Unity_SceneView_VillageCylinders.png"
   ```
3. **Alternatif Cepat via PowerShell / Python Pillow:**
   Jika `agent-desktop` membutuhkan interaksi headed, Agent di Laptop B dapat menjalankan perintah Python satu-baris:
   ```powershell
   python -c "from PIL import ImageGrab; ImageGrab.grab().save('docs/figures/raw_screenshots/SS_Unity_SceneView_VillageCylinders.png')"
   ```

---

## ⚡ 3. Master Prompt Siap Salin untuk Laptop B

Salin teks berikut ke sesi chat **Laptop B**:

```markdown
Halo Laptop B! Sesuai instruksi pengguna dan update terbaru dari Laptop A, seluruh konten tekstual Laporan Capstone (Bab I–V, Subbab 4.6 Game Loop Sesi 5, Subbab 4.7 Tabel Latensi & FPS, Subbab 4.8 Evaluasi Ergonomi SUS/Borg RPE, serta Bab V Kesimpulan & Saran) telah LENGKAP dan tersinkronisasi di docs/Laporan_Capstone_Fanamir.md, docs/Laporan_Capstone_Fanamir.docx, dan docs/Laporan Capstone Fanamir.docx.

Halaman Sampul dan section Nama Kelompok juga telah DIREVISI penuh:
- Spacing Table 0 section nama kelompok telah ditata rapi di tengah (CENTER), kolom seimbang (Nama: 2.5", Titik Dua: 0.35", NIM: 1.5"), dan 6 baris kosong telah dibersihkan.
- Blok Dosen Pembimbing dan Koordinator Prodi / Kaprodi telah ditambahkan pada Halaman Sampul dan Halaman Pengesahan sesuai ketentuan Lampiran 6A.
- Gambar 2.1 telah diperbarui dengan diagram resmi Topologi CV Hibrida Fanamir (17 Keypoints COCO + 3 Titik Wajah MediaPipe + Seated Virtual Hips + skema warna dwi-warna Oranye-Kanan dan Sian-Kiri).

Tugas kamu sekarang di Laptop B adalah melengkapi 5 tangkapan layar (screenshot) aktual dan menginjeksinya ke dalam berkas laporan .docx.

PENTING — FORMAT SCREENSHOT UNITY:
Saat mengambil screenshot terkait Unity (Gambar 4.3 dan Gambar 4.4), WAJIB mengambil FULL JENDELA UNITY EDITOR (seluruh jendela Unity lengkap dengan Title bar, Main Menu, Hierarchy, Scene/Game view, Inspector, dan toolbar), BUKAN hanya crop kecil Game view. Kamu bisa menggunakan skill `agent-desktop` atau otomasi desktop Windows/Pillow agar tampilannya natural dan meyakinkan untuk laporan akademik!

Berikut langkah-langkah eksekusinya:

### Langkah 1: Siapkan Folder Penampung Staging
Pastikan folder berikut ada (buat jika belum ada):
`docs/figures/raw_screenshots/`

### Langkah 2: Ambil 5 Tangkapan Layar
1. **Gambar 3.3 (`SS_CV_Webcam_3PointFace.png`):**
   - Jalankan `ARDragonSkyGuardian_CV/active_cv_sender.py --gui`.
   - Lakukan gerakan mendongak/buka mulut, lalu tangkap frame jendela webcam OpenCV yang memperlihatkan 3 titik wajah (Hidung, Dagu, Bibir) dan teks bar HEAD_PITCH / MOUTH_OPEN.
   - Simpan ke `docs/figures/raw_screenshots/SS_CV_Webcam_3PointFace.png`.

2. **Gambar 4.1 (`SS_CV_CleanPreview_FPS.png`):**
   - Tangkap jendela streaming Clean CV Preview (Port 5053) yang hanya menampilkan kerangka dwi-warna (oranye-sian) + 3 titik wajah + FPS di pojok kiri atas.
   - Simpan ke `docs/figures/raw_screenshots/SS_CV_CleanPreview_FPS.png`.

3. **Gambar 4.3 (`SS_Unity_SceneView_VillageCylinders.png`):**
   - Buka scene `MainIsland_Gameplay.unity` di Unity 6.
   - Pada Edit Mode / Scene View, sesuaikan kamera Scene View sehingga kepulauan dan gizmo silinder interaksi 3 desa terlihat jelas, dengan panel Hierarchy terbuka menampilkan objek `Desa_Zephyria`, `Desa_Pyrewood`, dan `Desa_Solaria`.
   - Gunakan skill `agent-desktop` (atau script Python Pillow `ImageGrab.grab()`) untuk memotret FULL JENDELA UNITY EDITOR.
   - Simpan ke `docs/figures/raw_screenshots/SS_Unity_SceneView_VillageCylinders.png`.

4. **Gambar 4.4 (`SS_InGame_BillboardMarker_150m.png`):**
   - Masuk ke Play Mode pada scene `MainIsland_Gameplay.unity`.
   - Terbangkan naga mendekati salah satu desa (misal Smog Valley) pada jarak <= 150 meter sehingga ikon World-Space Billboard Marker melayang aktif di atas balai desa.
   - Tangkap FULL JENDELA UNITY EDITOR dalam keadaan Play Mode.
   - Simpan ke `docs/figures/raw_screenshots/SS_InGame_BillboardMarker_150m.png`.

5. **Gambar 4.7 (`Foto_Pengujian_Fisik_Tester.jpg` atau `SS_Pengujian_Fisik_Webcam.png`):**
   - Sediakan foto pengujian pemain berdiri/duduk di depan webcam mengendalikan game, atau simpan tangkapan layar live pengujian webcam.
   - Simpan ke `docs/figures/raw_screenshots/Foto_Pengujian_Fisik_Tester.jpg`.

### Langkah 3: Jalankan Injeksi Otomatis ke DOCX
Setelah file screenshot berada di `docs/figures/raw_screenshots/`, jalankan script yang telah disiapkan Laptop A:
```powershell
python misc/inject_screenshots_docx.py
```
Script ini akan secara otomatis mendeteksi keberadaan gambar dan menyisipkannya tepat ke dalam kotak-kotak placeholder pada kedua berkas:
- `docs/Laporan Capstone Fanamir.docx`
- `docs/Laporan_Capstone_Fanamir.docx`

### Langkah 4: Verifikasi & Catat di Devlog
1. Buka dan pastikan berkas Word laporan sudah memuat seluruh gambar dengan rapi tanpa placeholder yang tertinggal.
2. Perbarui `brain/laptop-b/Devlog.md` dan `brain/laptop-b/Tasks.md` dengan ringkasan screenshot yang berhasil diambil.
```
