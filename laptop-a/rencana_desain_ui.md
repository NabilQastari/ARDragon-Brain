---
title: "Rencana Desain & Implementasi UI (v1.0)"
tags:
  - ui-ux
  - design
  - laptop-a
  - unity-ui
status: approved
owner: Laptop A
updated: 2026-11-13
related:
  - "[[00 - Overview]]"
  - "[[konteks]]"
  - "[[prd]]"
---

# 🎨 DOKUMEN RENCANA DESAIN & IMPLEMENTASI UI (v1.0)
## AR DRAGON: SKY GUARDIAN

> **Status Desain:** Dikunci & Disetujui (*Design Approved*).
> **Aset Rujukan:** Tersedia di folder [`Referensi UI/`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/).
> **Prinsip Utama:** *Clean Minimalist 2D Vector Cartoon Fantasy* — Menggabungkan kehangatan kertas krem, pita kain petualang, dan kayu halus ala Nintendo (*Zelda: Link's Awakening / Animal Crossing*) dengan kontras tinggi (100% solid, zero transparency) demi keterbacaan optimal dari jarak webcam 1.5–2 meter.

---

## 📂 1. KATALOG ASET RUJUKAN VISUAL

Seluruh gambar rujukan telah disimpan di folder `Referensi UI/`:

| Nama File | Fungsi & Deskripsi |
| :--- | :--- |
| [`BG_Island_Aerial_View.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/BG_Island_Aerial_View.jpg) | Background panorama pulau hijau dari ketinggian (*high aerial POV*) menembus awan putih dengan laut biru tenang (16:9 widescreen). |
| [`UI_Missions_Screen.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/UI_Missions_Screen.jpg) | Tampilan antarmuka Tab Utama **Missions** (Level 1, 2, 3) di atas latar putih bersih. |
| [`UI_Calibration_Screen.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/UI_Calibration_Screen.jpg) | Tampilan antarmuka Tab **Calibration** (Dual Preview: Cermin Webcam + Naga 3D Realtime + Panduan Pose Y-T-A). |
| [`UI_InGame_Flight_HUD.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/UI_InGame_Flight_HUD.jpg) | Tampilan antarmuka **In-Game Flight HUD** resmi (Timer top-center, Pause top-right, Progress bar vertikal kiri, Telemetri & Webcam kanan bawah). |
| [`UI_Composite_Mockup.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/UI_Composite_Mockup.jpg) | Pratinjau komposit UI Missions saat di-overlay di atas Background Pulau. |
| [`Wireframe_Finish_Modal.png`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/Wireframe_Finish_Modal.png) | Cetak biru Wireframe resmi **Finish UI / Victory & Results Modal** (16:9 layout blueprint). |
| [`Wireframe_Village_Action.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/Wireframe_Village_Action.jpg) | Cetak biru Wireframe resmi **Village Action Challenge Overlay** (Auto-hover gesture prompt). |
| [`UI_Style_Inspiration_Kit.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/UI_Style_Inspiration_Kit.jpg) | Lembar referensi gaya awal (*Paper, wood, cloth ribbons, and chunky tactile buttons*). |

---

## 🏛️ 2. SPESIFIKASI HALAMAN MENU UTAMA (LOBBY)

Sistem Menu Utama menggunakan **3 Tab Navigasi Terpusat (*Segmented Capsule Pill Navbar*)** di bagian atas layar:
* **Kiri:** `CALIBRATION` (Ikon tangan)
* **Tengah:** `MISSIONS` (Pita teal aktif / tab default)
* **Kanan:** `SETTINGS` (Ikon gear)

---

### 2.1 Tab 1: [ MISSIONS ] (Pemilihan Level)
*Rujukan Visual:* [`UI_Missions_Screen.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/UI_Missions_Screen.jpg)

```
+-----------------------------------------------------------------------------------+
| AR DRAGON · SKY GUARDIAN     [ CALIBRATION ]   [ MISSIONS (Aktif) ]   [ SETTINGS ]|
+-----------------------------------------------------------------------------------+
|                                                                                   |
|      +------------------+   +------------------+   +------------------+           |
|      |      PITA 01     |   |      PITA 02     |   |      PITA 03     |           |
|      |                  |   |                  |   |                  |           |
|      |   SMOG VALLEY    |   |     WILDFIRE     |   |     DROUGHT      |           |
|      |       90s        |   |       150s       |   |                  |           |
|      |      ⭐⭐⭐      |   |      ⭐⭐☆       |   |       [🔒]       |           |
|      |                  |   |                  |   |                  |           |
|      |     [ PLAY ]     |   |    [ SELECT ]    |   |                  |           |
|      +------------------+   +------------------+   +------------------+           |
|                                                                                   |
| [ ESC · QUIT ]                                                                    |
+-----------------------------------------------------------------------------------+
```

#### Komponen UI:
1. **Header Kiri Atas:** Teks judul bersih `AR DRAGON · SKY GUARDIAN` (Font: *Geometric Sans-Serif Bold*, warna: `#2C3E50` Dark Slate).
2. **Kartu Level 1 (Smog Valley - Aktif):**
   * Background: Kertas krem halus solid (`#FBF6EB`) dengan sudut melengkung (*border-radius 16px*).
   * Ribbon Lipat Atas: Warna Teal (`#1ABC9C` / `#16A085`) dengan teks "01".
   * Judul: "SMOG VALLEY", subtext "90s".
   * Rating: 3 Bintang emas solid (⭐⭐⭐).
   * Tombol Aksi: Coral-red pill button (`#E74C3C` / `#D63031`) dengan teks putih tebal "PLAY".
3. **Kartu Level 2 (Wildfire Woods - Terbuka):**
   * Ribbon Lipat Atas: Warna Oranye (`#E67E22`).
   * Judul: "WILDFIRE", subtext "150s".
   * Rating: 2 Bintang emas + 1 outline (⭐⭐☆).
   * Tombol Aksi: Beige pill button (`#E0D5BE`) dengan teks "SELECT".
4. **Kartu Level 3 (Drought Plateau - Terkunci):**
   * Ribbon Lipat Atas: Warna Abu-abu netral (`#95A5A6`).
   * Judul: "DROUGHT" (warna teks sedikit redup).
   * Ikon Tengah: Gembok kartun coklat/emas solid melambangkan level terkunci (syarat buka: min. 1 bintang di Level 2).
   * Tanpa tombol (area kosong yang bersih).
5. **Pojok Kiri Bawah:** Tombol pil kecil `ESC · QUIT`.

---

### 2.2 Tab 2: [ CALIBRATION ] (Kalibrasi Postur & Live Naga)
*Rujukan Visual:* [`UI_Calibration_Screen.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/UI_Calibration_Screen.jpg)

```
+-----------------------------------------------------------------------------------+
| AR DRAGON · SKY GUARDIAN     [ CALIBRATION (Aktif) ]   [ MISSIONS ]   [ SETTINGS ]|
+-----------------------------------------------------------------------------------+
|                                                                                   |
|     +-----------------------------+         +-----------------------------+       |
|     |       PLAYER TRACKING       |         |       DRAGON REACTION       |       |
|     |        CAMERA MIRROR        |   ==>   |     LIVE DRAGON PREVIEW     |       |
|     |  (Siluet Pemain & Skeleton) |         |  (Model 3D Naga Merespons)  |       |
|     |                             |         |                             |       |
|     |   [TRACKING: READY (1.8m)]  |         |   [STATUS: CRUISE FLIGHT]   |       |
|     +-----------------------------+         +-----------------------------+       |
|                                                                                   |
|     =================== INDIKATOR GESTUR TERDETEKSI ===================           |
|        [ POSE Y · CLIMB UP ]   [ POSE T · CRUISE FORWARD ]   [ POSE A · DIVE ]    |
|        Tangan di Atas Bahu      Tangan Sejajar Bahu (Aktif)   Tangan ke Bawah     |
|                                                                                   |
| [ ESC · BACK ]                                                    [ READY TO FLY ]|
+-----------------------------------------------------------------------------------+
```

#### Komponen UI:
1. **Panel Kiri (Player Tracking / Camera Mirror):**
   * Bingkai kartu krem berukuran besar.
   * Menampilkan siluet tubuh dengan titik pelacakan keypoint AI (Bahu, Pergelangan Tangan, Pinggul, Kepala).
   * Badge Status Bawah: Kapsul hijau solid `TRACKING: READY (1.8m)` (berubah kuning/merah jika pemain terlalu dekat/jauh).
2. **Panel Kanan (Dragon Reaction / Live Dragon Preview):**
   * Bingkai kartu krem berukuran identik dengan panel kiri.
   * Menampilkan model naga 3D (`Dragon_Model.blend`) secara live (via RenderTexture) yang meniru gerakan pemain secara *real-time* (mengepak, menukik, atau meluncur).
   * Badge Status Bawah: Kapsul teal solid `STATUS: CRUISE FLIGHT` (berubah dinamis: `CLIMBING`, `DIVING`, `TURNING LEFT/RIGHT`).
3. **Baris Status Gestur (Bawah Tengah):**
   * **Pill 1:** `POSE Y · CLIMB UP` (Ikon tangan terangkat $\uparrow$).
   * **Pill 2:** `POSE T · CRUISE FORWARD` (Ikon tangan merentang $\rightarrow$, memiliki border teal menyala saat aktif).
   * **Pill 3:** `POSE A · DIVE BOOST` (Ikon tangan serong bawah $\downarrow$).
4. **Navigasi Bawah:**
   * Kiri: `ESC · BACK` (Kembali ke tab Missions).
   * Kanan: Tombol Coral-Red tebal `READY TO FLY` (Langsung meluncurkan penerbangan).

---

### 2.3 Tab 3: [ SETTINGS ] (Pengaturan Audio & Sensitivitas)
*(Akan menggunakan format kartu krem dan layout yang serasi)*:
1. **Slider Master Volume, BGM Volume, dan SFX Volume.**
2. **Sensitivitas Kemudi Belok (*Turn Sensitivity Slider*):** Mengatur pengali kepekaan kemiringan tangan ($1.0\times$ s/d $3.0\times$).
3. **Pilihan Kamera Webcam:** Dropdown untuk memilih webcam aktif jika memiliki lebih dari 1 kamera.
4. **Toggle Resolusi / Fullscreen.**

---

## 🎮 3. SPESIFIKASI IN-GAME GUI & FINISH MODAL

Menggunakan bahasa visual kartun minimalis solid yang sama:

### 3.1 In-Game Flight HUD (Resmi — Compact Space-Saving Design)
*Rujukan Visual:* [`UI_InGame_Flight_HUD.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/UI_InGame_Flight_HUD.jpg)

```
+-----------------------------------------------------------------------------------+
|                            [ 02:15  ·  VILLAGES: 1/3 ]            [ ESC · PAUSE ] |
|                            (Pill Bersih Tanpa Pita)              (Sudut Kanan Atas)|
|                                                                                   |
| [BAR PROGRES]                                                                     |
| +----+                                                                            |
| |33% | (Ikon Daun)                                                                |
| |====|                                                                            |
| |====|                                  ▲                                         |
| |    |                           [ WILDFIRE · 140m ]                              |
| |    |                                                                            |
| |    |                                                       +------------------+ |
| |    | (Bar Vertikal                                         | ▲42m 12m/s POSE T| |
| |    |  Sisi Kiri Layar)                                     +------------------+ |
| +----+                                                       | PREVIEW WEBCAM   | |
|                                                              | (● CAM READY)    | |
|                                                              +------------------+ |
+-----------------------------------------------------------------------------------+
```

#### Komponen In-Game HUD:
1. **Tengah Atas (Timer & Objektif Desa Mungil):**
   * Kapsul krem ramping tanpa pita: Ikon jam pasir + digital countdown `02:15`, pemisah titik, dan status `VILLAGES: 1 / 3` dengan tanda centang hijau.
2. **Kanan Atas (Tombol Pause):**
   * Tombol pil kecil `ESC · PAUSE` di sudut kanan atas untuk akses cepat jeda game.
3. **Sisi Kiri Layar (Global Vitality Progress Bar Vertikal):**
   * Bar meteran vertikal ramping dengan ujung rounded. Mengisi dari bawah ke atas dengan warna hijau zamrud (*emerald green*) mewakili energi pemulihan alam pulau.
   * Di puncak bar: Lencana bulat mini dengan persentase `33%` dan ikon daun.
4. **Klaster Kanan Bawah (Telemetri Naga & Kamera PIP):**
   * **Dock Telemetri (Atas):** Kapsul ramping tepat di atas kamera menampilkan ketinggian `▲ 42m`, kecepatan angin `12.0m/s`, dan badge cyan terang `POSE T` (berubah dinamis menjadi `POSE Y` saat menanjak atau `POSE A` saat menukik).
   * **Monitor Kamera PIP (Bawah):** Kotak rasio 16:9 menampilkan feed webcam asli dengan overlay kerangka pose tubuh (*stickman joint dots*) dan indikator `● CAM READY`.
5. **Tengah Layar (Subtle Waypoint Compass Pointer):**
   * Panah chevron kartun halus berwarna teal yang menunjuk arah desa target terdekat: `▲ WILDFIRE · 140m`.
6. **Kelegaan Layar:** Lebih dari **85% area tengah dan kiri layar bebas halangan**, memberikan visibilitas maksimal saat terbang menjelajah pulau.

### 3.2 Mode Auto-Hover Desa (Mission Overlay)
Saat naga masuk ke area desa:
* Banner nama desa muncul di atas: `DESA 2: HUTAN KEBAKARAN`.
* Ikon gestur besar di tengah: Ikon animasi mulut naga membuka / kedua tangan di dada bertuliskan *"Semburkan Air Purba!"*.
* Bar progres lokal desa krem-hijau naik dari $0\% \to 100\%$.

### 3.3 Finish UI (Victory & Defeat Modal)
* **Victory Screen:** Plakat kayu besar dengan 3 bintang emas bersinar (⭐⭐⭐), rincian waktu tempuh, jumlah desa yang dipulihkan, serta tombol tebal `[ LEVEL BERIKUTNYA ]` dan `[ KEMBALI KE LOBBY ]`.
* **Defeat Screen:** Plakat kayu dengan stempel merah "WAKTU HABIS", menampilkan desa yang belum selesai, dan tombol `[ COBA LAGI ]`.

---

## 🛠️ 4. RENCANA IMPLEMENTASI TEKNIS DI UNITY

1. **Aset Grafis (Sprite Slicing):**
   * Menggunakan gambar `BG_Island_Aerial_View.jpg` sebagai latar belakang Canvas 16:9 (`Image` component / RawImage).
   * Mengambil elemen kartu, tombol, ribbon, dan plakat dari `UI_Missions_Screen.jpg` dan `UI_Calibration_Screen.jpg` untuk diubah menjadi **9-Sliced Sprites** transparan.
2. **Sistem Tab Navigasi (`LobbyNavigationManager.cs`):**
   * Mengatur aktif/nonaktif panel `Panel_Missions`, `Panel_Calibration`, dan `Panel_Settings` secara instan atau dengan transisi geser mulus (*smooth horizontal slide*).
3. **Integrasi Live Dragon Preview di Kalibrasi:**
   * Menggunakan **RenderTexture** sekunder dengan kamera khusus yang menyorot model `Dragon_Model.blend` di scene terisolasi.
   * Parameter pose dari `UDPPoseReceiver.cs` langsung menggerakkan tulang sayap dan badan naga di pratinjau tersebut.
4. **Font & Tipografi:**
   * Menggunakan **TextMeshPro (TMP)** dengan font bergaya *clean geometric rounded sans-serif* (seperti *Nunito*, *Quicksand*, atau *Fredoka One*) untuk mencocokkan estetika kartun Nintendo.


---
**Navigasi Obsidian:** [[00 - Overview]] · [[konteks]] · [[prd]] · [[Tasks]] · [[Devlog]]
