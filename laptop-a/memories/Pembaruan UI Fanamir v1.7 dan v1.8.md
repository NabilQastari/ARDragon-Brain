---
title: Pembaruan UI Fanamir v1.7 dan v1.8
type: session-memory
tags:
  - session-memory
  - laptop-a
  - ui-ux
  - figcraft
  - unity-ui
  - fanamir
date: 2026-11-18
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[rencana_desain_ui]]"
  - "[[panduan_aset_ui_unity]]"
  - "[[kontrak_integrasi_ui_dan_progress]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[laptop-b/Devlog]]"
---

# 📝 Memori Sesi — Pembaruan UI `Fanamir` (`v1.6.1` $\rightarrow$ `v1.8`)

## 1. Tujuan & Konteks Sesi
Sesi ini berfokus pada penyempurnaan sistem antarmuka pengguna (**UI/UX**) dan kesiapan aset ekspor Unity 6 di **Laptop A** untuk game **`Fanamir: The Sky Guardian`** (yang berjalan beriringan dengan implementasi paralel di **Laptop B**):
1. Memperbarui background Lobby (`BG_Island_Aerial_View.jpg`), membangun pipeline ekspor UI Unity (`UI_Unity_Export/`), serta merancang **kontrak integrasi UI Hybrid** (`Live` + `Placeholder` + `Dormant`) agar UI bisa diimplementasikan di Laptop B selagi mekanisme terbang masih diuji.
2. Merevisi tab **Calibration (`SCREEN 01`)** menjadi `4` mini card pose dinamis beserta modal **`SCREEN 07 - Pose Guide Overlay`** (`v1.6.1`).
3. Menerapkan pembaruan **`v1.7`**: branding resmi **`Fanamir: The Sky Guardian`** / **`Fanamir`**, panel **`Quest Objective`** (`SCREEN 02`), **Quest Narrative Text Box Auto-Play** *hands-free* tanpa tombol Continue (`SCREEN 08`), dan slider ke-5 **`Text Auto-Next`** (`SCREEN 05`).
4. Merancang alur boot **`v1.8`**: **`Splash Screen (SCREEN 09)` $\rightarrow$ `Start Screen (SCREEN 10 · Press Any Button to Start)` $\rightarrow$ `Lobby`**.

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Branding Publik vs Namespace Teknis:**
   - Seluruh teks publik di UI (`Lobby Top Bar`, header `LIVE FANAMIR PREVIEW`, deskripsi pose `#01–#03`) memakai **`Fanamir: The Sky Guardian`** / **`Fanamir`**.
   - Nama teknis internal (**`DragonWingAR.*`**, scene `Test_Dragon_Flight.unity`, dan nama aset `UI_Illust_Dragon_WaterBreath`) **tetap dipertahankan** agar tidak merusak binding yang sudah berjalan di Laptop B.
2. **Kalibrasi `1×4` Dinamis & Pose Guide Overlay (`v1.6.1`):**
   - Halaman kalibrasi tidak lagi memakai grid `3×5` yang padat; panel tracking & preview naga diperbesar (`860×540 px`), di bawahnya hanya tampil **maksimal 4 mini card (`1×4`)** berbasis antrean MRU pose terakhir (default launch `[#08 Glide, #06 Flap To Climb, #04 Takeoff, #13 Tuck To Dive]`), disertai tombol **`OPEN POSE GUIDE [15 POSES]`** untuk membuka katalog penuh di `SCREEN 07`.
3. **Narasi Desa Auto-Play *Hands-Free* (`v1.7`):**
   - Karena kedua tangan pemain sibuk melakukan gestur pose di depan webcam, **tombol Continue pada Quest Narrative Box (`SCREEN 08`) dihapus**.
   - Text box berjalan otomatis dengan countdown widget **`AUTO-PLAY · HANDS-FREE`** (`NEXT IN 3.2s / 5.0s` + progress bar hijau), intervalnya dikontrol oleh slider ke-5 **`Text Auto-Next`** di `SCREEN 05` (`2.0s–10.0s`, default `5.0s`, `PlayerPrefs`), lalu menutup sendiri dan otomatis mengubah `Quest Objective Panel` dari **`TRAVEL`** ke **`ACTIVE TASK`**.
4. **Alur Boot `Splash -> Start Screen -> Lobby` Tanpa Tambahan Sprite Baru (`v1.8`):**
   - `SCREEN 09 (Splash Screen)` memakai latar perkamen krem solid `#F2EFE7` dengan bingkai *stepped*, *Baroque Scrollwork Crest*, dan plakat logo **`FANAMIR: THE SKY GUARDIAN`** (~`2.5s`, skippable setelah `0.5s`).
   - `SCREEN 10 (Start Screen)` berbagi background `UI_Bg_Lobby_AerialIsland` dengan `MainMenu_Lobby.unity`, menempatkan plakat judul di langit atas agar kepulauan di tengah layar terbuka lebar, tombol `ESC · QUIT` di kanan atas, serta kapsul **`PRESS ANY BUTTON TO START`** di bawah.
   - Arsitektur Unity yang direkomendasikan memakai **`BootFlowController.cs`** di dalam `MainMenu_Lobby.unity` (Build Index `0`) dengan flag statis **`SkipBootScreensOnce`** saat kembali dari `Test_Dragon_Flight.unity` (`Pause` / `Finish Modal`) supaya pemain tidak dipaksa menonton Splash Screen berulang kali.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
1. **Aset FigCraft & Ekspor Komponen (`v1.6` – `v1.7`):**
   - Memperbarui [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) (`1,306,400` B) dengan `5` layer background kepulauan baru, revisi `SCREEN 01`, modal `SCREEN 07`, pembaruan branding `Fanamir`, `Quest Objective Panel` (`SCREEN 02`), slider ke-5 `Text Auto-Next` (`SCREEN 05`), dan overlay `SCREEN 08`.
   - Mengekspor **`140` PNG komponen *textless* (`clip: false`)** ke [`UI_Unity_Export/Components/`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/Components/), termasuk `6` komponen baru `v1.7`: `UI_Panel_QuestObjective_9Slice.png` (`240×135`, `2,018` B), `UI_Panel_NarrativeBox_9Slice.png` (`304×200`, `2,287` B), `UI_Banner_SpeakerTag_Blank.png` (`240×42`, `483` B), `UI_Icon_QuestScroll_Gold.png` (`48×48`, `697` B), `UI_Icon_AutoPlay_Timer.png` (`48×48`, `1,209` B), dan `UI_Icon_PromptNext_Arrow.png` (`48×48`, `869` B).
2. **Desain Referensi `v1.8` (`0` Komponen PNG Baru Dibutuhkan):**
   - Merakit [`09_SplashScreen.png`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/Designs/09_SplashScreen.png) (`1920×1080`, `54,750` B) dan [`10_StartScreen.png`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/Designs/10_StartScreen.png) (`1920×1080`, `1,349,407` B) langsung dari sprite komponen eksisting dengan kalkulasi 9-Slice / 3-Slice H 1:1 dan font `Fredoka`, sehingga total [`UI_Unity_Export/Designs/`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/Designs/) kini berisi **`11` PNG `1920×1080`**.
3. **Dokumentasi Obsidian `brain/` (`Laptop A`):**
   - Memperbarui [[00 - Overview]], [[rencana_desain_ui]] (`v1.8`), [[panduan_aset_ui_unity]], [[kontrak_integrasi_ui_dan_progress]] (menambahkan **§5 Prompt Patch `v1.7`** & **§6 Prompt Patch `v1.8`** untuk Laptop B), [[Tasks]], dan [[Devlog]] (commit `188ffa4` & `294db55`).
   - Menurut `brain/laptop-b/Devlog.md`, Laptop B telah berhasil mengimplementasikan **`v1.6.1`** dan **`v1.7`** (`140` PNG, `0` error/warning).

---

## 4. Detail Penting yang Harus Diingat
- **Kendala MCP `figcraft` Pasca *Server Restart*:** Saat pengerjaan `v1.8` (`SCREEN 09–10`), server MCP `figcraft` terputus dari sesi chat setelah *server restart* (hanya `Roblox_Studio`, `blender`, dan `photocraft` yang terpasang). Karena itu, `09_SplashScreen.png` dan `10_StartScreen.png` dibuat langsung dari `140` sprite hasil ekspor FigCraft tanpa memodifikasi [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) secara manual.
- **Konvensi Border Unity C# vs Inspector:** Tabel di [[panduan_aset_ui_unity]] memakai urutan Editor **`L, T, R, B`**, sedangkan `Sprite.border` C# (`Vector4`) memakai **`(L, B, R, T)`**.
- **Aturan 3-Slice Horizontal:** Kunci tinggi `UI_Banner_SpeakerTag_Blank` (`42 px`) dan `UI_Banner_Stepped_Modal_Blank` (`77 px`) agar telinga *stepped* dan bayangan bawah tidak melar vertikal.
- **Anti-Collision `brain/`:** Folder `brain/laptop-b/` memiliki perubahan unstaged bawaan (`memories/Rework CV dan Kontrol Terbang Baru.md`); jangan pernah men-stage/mengedit file apa pun di `brain/laptop-b/`.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
1. **Laptop B — Eksekusi UI `v1.8`:** Kirimkan [`09_SplashScreen.png`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/Designs/09_SplashScreen.png) & [`10_StartScreen.png`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/Designs/10_StartScreen.png) bersama prompt `§6` di [[kontrak_integrasi_ui_dan_progress]] untuk membangun `BootFlowController.cs` di `MainMenu_Lobby.unity`.
2. **Opsional — Sinkronisasi Artboard `.figcraft`:** Tambahkan artboard `SCREEN 09` & `SCREEN 10` ke [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) di sesi terpisah saat `figcraft` MCP aktif bila ingin menjaga sumber vektor tetap lengkap.

---
**Navigasi Obsidian:** [[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[rencana_desain_ui]] · [[panduan_aset_ui_unity]] · [[kontrak_integrasi_ui_dan_progress]]
