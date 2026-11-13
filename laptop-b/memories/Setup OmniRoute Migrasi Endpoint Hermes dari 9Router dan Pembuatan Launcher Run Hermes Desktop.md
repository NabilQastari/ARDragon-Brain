---
title: "Setup OmniRoute, Migrasi Endpoint Hermes dari 9Router, dan Pembuatan Launcher Run Hermes Desktop"
type: session-memory
tags:
  - session-memory
  - laptop-b
  - omniroute
  - hermes-desktop
  - 9router
  - tooling
date: 2026-11-13
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
---

# Setup OmniRoute, Migrasi Endpoint Hermes dari 9Router, dan Pembuatan Launcher Run Hermes Desktop

## 1. Tujuan & Konteks Sesi
Sesi ini berfokus pada persiapan lingkungan *tooling* AI lokal di **Laptop B** untuk menjalankan **Hermes Agent / Hermes Desktop** melalui gateway **OmniRoute** sebagai pengganti **9Router**:
- Menyusun prompt *autonomous* untuk Hermes Agent agar dapat menginstal dan menjalankan OmniRoute hingga dashboard setup provider aktif di `localhost:20128`.
- Menyiapkan perintah untuk menghentikan server 9Router yang sebelumnya berjalan di port `20128`.
- Menangani error modul native `better-sqlite3` saat menjalankan `omniroute` v3.8.48 di Windows.
- Mengalihkan endpoint Hermes dari 9Router ke OmniRoute (`http://localhost:20128/v1`).
- Membuat script launcher Windows `Run Hermes Desktop.bat` untuk menyalakan OmniRoute dan Hermes Desktop dalam satu klik.

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Standardisasi Gateway ke OmniRoute (`localhost:20128`):**
   - OmniRoute (`diegosouzapw/OmniRoute`) dipilih sebagai gateway OpenAI-compatible terpadu pada port `20128` dengan endpoint API `http://localhost:20128/v1` dan dashboard web di `http://localhost:20128`.
   - Karena 9Router dan OmniRoute sama-sama menggunakan port default `20128`, server 9Router harus dihentikan terlebih dahulu sebelum OmniRoute dijalankan.
2. **Penanganan Inkompatibilitas ABI Node.js pada `better-sqlite3`:**
   - Error `NODE_MODULE_VERSION 127` vs `137` pada `omniroute` v3.8.48 terjadi karena binary bawaan dikompilasi untuk Node.js 22 sedangkan sistem memakai Node.js 23/24.
   - Jalur perbaikan utama yang direkomendasikan adalah perintah bawaan `omniroute runtime repair` (tanpa membutuhkan C++ toolchain), dengan fallback `npm rebuild better-sqlite3` di folder `dist`, clean reinstall, atau `nvm use 22`.
3. **Launcher Terpadu (`Run Hermes Desktop.bat`):**
   - Dibuat script `.bat` yang mengecek port `20128` terlebih dahulu agar tidak memicu instance ganda OmniRoute, menjalankan OmniRoute secara *minimized* bila belum aktif, memberi jeda 4 detik, menyetel `OPENAI_BASE_URL`, lalu mendeteksi lokasi executable/shortcut/CLI Hermes Desktop secara otomatis.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **File baru dibuat di root workspace:**
  - `Run Hermes Desktop.bat` (`d:\Fauzih\Kuliah\SEMESTER V\Proyek Multimedia dan Game\AR Dragon\Run Hermes Desktop.bat`, 92 baris):
    1. Cek port `20128` dengan `netstat -ano | findstr :20128`.
    2. Jika belum aktif, menjalankan `start "OmniRoute Service" /min cmd /k "omniroute"` dan menunggu 4 detik (`timeout /t 4 /nobreak >nul`).
    3. Menyetel `set OPENAI_BASE_URL=http://localhost:20128/v1`.
    4. Mencari Hermes Desktop di `%LOCALAPPDATA%\Programs\Hermes\Hermes.exe`, `%LOCALAPPDATA%\hermes-desktop\Hermes Desktop.exe`, `C:\Program Files\Hermes\Hermes.exe`, `C:\Program Files (x86)\Hermes\Hermes.exe`, shortcut Desktop (`Hermes Desktop.lnk` / `Hermes.lnk`), serta fallback CLI `where hermes`.
- **Verifikasi file:** File `Run Hermes Desktop.bat` telah ditulis dan diverifikasi keberadaannya di workspace (belum diuji eksekusi langsung dari sesi ini untuk peluncuran GUI Hermes Desktop).
- **Catatan sistem yang diamati:** Pencarian direktori sistem sebelumnya menemukan folder terkait Hermes di `C:\Users\ASUS\AppData\Roaming\Hermes` (termasuk `Partitions\hermes-embed`), sementara beberapa path sistem di luar workspace terkena pembatasan akses sandbox (`Access is denied`).

---

## 4. Detail Penting yang Harus Diingat
- **Versi & Lokasi OmniRoute yang diamati:**
  - OmniRoute `v3.8.48` terpasang global di `C:\Users\ASUS\AppData\Roaming\npm\node_modules\omniroute`.
  - File `.env` dimuat dari `C:\Users\ASUS\.omniroute\.env` dan `C:\Users\ASUS\AppData\Roaming\npm\node_modules\omniroute\.env`.
- **Port & Endpoint:**
  - Dashboard OmniRoute: `http://localhost:20128`
  - Base URL OpenAI-compatible: `http://localhost:20128/v1`
- **Command Penting:**
  - Matikan 9Router di Windows PowerShell: `Get-NetTCPConnection -LocalPort 20128 -ErrorAction SilentlyContinue | ForEach-Object { Stop-Process -Id $_.OwningProcess -Force }`
  - Perbaikan runtime OmniRoute: `omniroute runtime repair`
  - Ubah endpoint Hermes via CLI: `hermes config set model.base_url http://localhost:20128/v1` (atau edit `%USERPROFILE%\.hermes\config.yaml` / `%USERPROFILE%\.hermes\.env`).
- **Batasan & Konflik Aturan:**
  - Awalnya user menggunakan template prompt Laptop A, namun dikonfirmasi bahwa sesi ini milik **Laptop B** sesuai `AGENTS.md` (dilarang mengedit `brain/laptop-a/`, hanya menulis di `brain/laptop-b/`).

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] Pastikan `omniroute runtime repair` sudah dijalankan jika `omniroute` masih memunculkan error `NODE_MODULE_VERSION 137`.
- [ ] Uji coba double-click `Run Hermes Desktop.bat` di Windows Explorer; jika lokasi `Hermes.exe` berada di path kustom, sesuaikan baris path pada script `.bat`.
- [ ] Lanjutkan antrean pengembangan proyek AR Dragon di Laptop B sesuai `[[Tasks]]` (Sesi GUI, integrasi model pulau 3 biome, Sesi 4, dan Sesi 5).

---
**Navigasi Obsidian:** [[00 - Overview]] · [[konteks]] · [[prd]] · [[GDD_Pulau]] · [[progress]] · [[Tasks]] · [[Devlog]]
