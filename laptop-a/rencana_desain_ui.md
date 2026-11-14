---
title: "Rencana Desain & Implementasi UI (v1.5)"
tags:
  - ui-ux
  - design
  - laptop-a
  - unity-ui
status: approved
owner: Laptop A
updated: 2026-11-14
related:
  - "[[00 - Overview]]"
  - "[[konteks]]"
  - "[[prd]]"
---

# 🎨 DOKUMEN RENCANA DESAIN & IMPLEMENTASI UI (v1.5)
## AR DRAGON: SKY GUARDIAN

> **Status Desain:** Dikunci & Disetujui (*Design Approved* — Seluruh 6 Layar Utama + Spec 5 State Kamera tersedia di [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) pada page `Wireframe` dan `Design`, dengan penyempurnaan visual *Fantasy Parchment-and-Ink Tabletop UI* + outline tinta netral seragam `v1.5` pada seluruh 7 artboard page `Design`).
> **Aset Rujukan:** Tersedia di folder [`Referensi UI/`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/).
> **Prinsip Utama:** *Fantasy Parchment-and-Ink Tabletop Card UI* — Kertas perkamen solid (`#F2EFE7` / `#FBF6EB`, **100% opaque / `alpha = 1.0f`, zero transparency / zero glassmorphism**), sudut **stepped rabbeted plaques** (*vector staircase corner cutouts*) dengan bingkai ganda arsitektural kontinu (*stepped + concave scalloped inner frame*) & kontrol tajam `r = 1–3 px`, *outlined* tinta arsitektural `#332C25` (`2.5–3 px`) dengan *hard cast shadow* nol-blur (`radius: 0`), spanduk heksagonal runcing berornamen diamond *fleuron* bertumpuk batas atas kartu, tag speech-bubble emas `#DF9E38`, palet tombol 3D bevel (`#5C8D46` / `#3D642D` dengan *top sheen* `#7FB667` & takik ganda kiri, sekunder `#E6DFD2` / `#BDB3A0`, dan tombol `ESC` krem-merah `#F3EFE6` / `#D15046`), serta latar belakang blank putih `#FFFFFF` khusus layar in-game agar langsung menampakkan viewport Unity (`> 85%` ruang bebas di `SCREEN 02`).
> **Catatan Placeholder Runtime:** Visual vektor naga 3D & kerangka *stick-figure* di artboard FigCraft berstatus *placeholder visual* yang akan digantikan oleh kamera/model realtime (`RenderTexture` + `RawImage` & overlay kerangka pose) saat implementasi di Unity.

### 🧱 Token Gaya Visual Final (Page `Design` v1.5)
- **Permukaan & Bingkai:** Latar kartu `#F2EFE7`, kartu fokus/aksi `#ECE6DA`, panel tauperesessif `#DDD5C5`, strip nilai/kode `#C7BDAA`, popup dropdown solid `#FBF6EB`, outline tinta utama `#332C25` (`2.5–3 px`, `strokeAlign: inside`, `miter`), dan *hard shadow* `#29231E` tanpa blur (`offset [0, 3]` hingga `[0, 8]`, `radius: 0`).
- **Konsistensi Warna Outline Netral (v1.5):** Seluruh kontainer UI (kartu utama, sub-panel, kartu pose, badge status kamera navbar, dropdown, viewport monitor/PIP, kotak stat, dan kartu state `SCREEN 06`) menggunakan **outline tinta seragam `#332C25`** — tanpa outline berwarna merah, biru/cyan, hijau, atau oranye/kuning. Variasi netral terbatas pada `#A89E8B` (*inner rim* tombol `ESC`/tutup/jeda), `#9D9384` (tombol nonaktif `Matikan`), `#4B565D` (badge di dalam viewport gelap `#1A2125`), serta `#B2A895` / `#8D8371` untuk garis bingkai dalam perkamen. State kamera tetap dibedakan lewat *fill* badge, dot indikator, teks panduan, dan tombol mode aktif.
- **Geometri Sudut Kartu & Kontrol (Sesuai Referensi):**
  - **Kartu Utama & Panel Kalibrasi (`460×680`, `860×404`, `720×652`, `680×792`, `760×874`):** Siluet vektor **Stepped Rabbeted Plaque (`a = 14, b = 7`)** bertakik 2 undakan di keempat sudut, dipasangi dua lapisan *continuous inner border*: outer stepped frame (`inset = 10`, `a = 12, b = 6`, `#B2A895`) + inner concave scalloped arch frame (`inset = 15`, arch `r = 18`, `#8D8371`, `fill: none`).
  - **Sub-Panel Menengah (`Victory Stat Box` `560×278`, `Cam Module` `640×342`, 7 kartu state `SCREEN 06`):** Siluet vektor **Stepped Plaque (`a = 10, b = 5`)** + inner stepped trim `a = 8, b = 4`.
  - **Kartu Pose (`15×` `340×122`), Baris Slider (`640×70`), Baris Victory (`520×66`), Plakat Gestur (`600×54`):** Siluet vektor **Stepped Plaque (`a = 8, b = 4` / `a = 6, b = 3`)** (`Card 08 Active` memakai double stepped border emas `#DF9E38` + tinta `#332C25`).
  - **Kontrol, Tombol, Tab, Dropdown, Medallion, Box Bintang & Gembok:** Sudut mikro-tegas **`r = 2 px`** (`r = 3 px` untuk container navbar/HUD kapsul `SCREEN 02`, `r = 1 px` untuk *inner rim* netral `#A89E8B` pada tombol `ESC`/tutup), menggantikan seluruh sudut kapsul membulat `r = 6..10 px`.
- **Hierarki Komponen Khas:** Spanduk judul heksagonal runcing + 4-petal *fleuron* (plus *scrollwork flourish* baroque pada `SCREEN 03–05`), navbar rail `#CFC6B4` dengan tab aktif emas `#E5AD44` + bibir `#B37C1E`, dan speech-bubble emas `#DF9E38` untuk durasi & status aktif.

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
| [`UI_Biome_01_Smog_Valley.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/UI_Biome_01_Smog_Valley.jpg) | Ilustrasi 16:9 kartu Level 01 **Smog Valley** (lembah pinus hijau, danau, desa atap terakota, cerobong asap abu-abu gelap). |
| [`UI_Biome_02_Wildfire.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/UI_Biome_02_Wildfire.jpg) | Ilustrasi 16:9 kartu Level 02 **Wildfire** (lereng desa berdampingan dengan tanah hangus ember & kobaran api hutan). |
| [`UI_Biome_03_Drought.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/UI_Biome_03_Drought.jpg) | Ilustrasi 16:9 kartu Level 03 **Drought** (tanah retak gersang, danau surut, pohon mati, desa kecil kekeringan). |

---

## 🏛️ 2. SPESIFIKASI HALAMAN MENU UTAMA (LOBBY)

Sistem Menu Utama menggunakan **3 Tab Navigasi Terpusat (*Segmented Capsule Pill Navbar*)** di bagian atas layar:
* **Kiri:** `CALIBRATION` (Ikon tangan)
* **Tengah:** `MISSIONS` (Pita teal aktif / tab default)
* **Kanan:** `SETTINGS` (Ikon gear)

---

### 2.1 Tab 1: [ MISSIONS ] / SCREEN 00 (Lobby & Pemilihan Level)
*Rujukan Visual:* [`UI_Missions_Screen.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/UI_Missions_Screen.jpg) · *Artboard FigCraft:* `SCREEN 00 - Main Menu / Missions (1920x1080)` (`Design` id `263`, `Wireframe` id `99`)

```
+---------------------------------------------------------------------------------------------------+
| AR DRAGON · SKY GUARDIAN   [ MISSIONS (Aktif) ] [ CALIBRATION ] [ SETTINGS ]  [● STANDBY] [ESC·QUIT]|
+---------------------------------------------------------------------------------------------------+
|                                                                                                   |
|      +---------------------+   +---------------------+   +---------------------+                  |
|      |       PITA 01       |   |       PITA 02       |   |       PITA 03       |                  |
|      |  [Img: Smog Valley] |   |   [Img: Wildfire]   |   |   [Img: Drought]    |                  |
|      |     SMOG VALLEY     |   |      WILDFIRE       |   |       DROUGHT       |                  |
|      |         90s         |   |        150s         |   |                     |                  |
|      |        ⭐⭐⭐        |   |        ⭐⭐☆         |   |        [🔒]         |                  |
|      |      [ PLAY ]       |   |     [ SELECT ]      |   |                     |                  |
|      +---------------------+   +---------------------+   +---------------------+                  |
+---------------------------------------------------------------------------------------------------+
```

#### Komponen UI:
1. **Header Kiri Atas & Tab Navigasi:** Teks judul bersih `AR DRAGON · SKY GUARDIAN` (Font: *Geometric Sans-Serif Bold*, warna: `#2D2926` Dark Ink) diikuti tab pill `[ MISSIONS ]`, `[ CALIBRATION ]`, dan `[ SETTINGS ]`.
2. **Navbar Kanan Atas (`Badge Camera Dynamic Status` + `ESC · QUIT`):**
   * **Tombol `ESC · QUIT`:** Konsisten menempel di sudut **kanan atas** semua layar dengan kontras tinggi (`#FBF6EB`, outline luar seragam `#332C25`, *inner rim* netral `#A89E8B`, serta ikon/pil coral di bagian dalam).
   * **Badge Status Kamera Dinamis (`Badge Camera Dynamic Status`):** Tepat di sebelah kiri `ESC · QUIT`, pill 100% solid `#FBF6EB` ber-outline tinta seragam `#332C25` yang menampilkan status real-time `PythonProcessManager` lewat warna *fill* badge/dot:
     * **Default Standby:** Badge & dot kuning/amber `● STANDBY · PILIH KAMERA` (outline tetap `#332C25`).
     * **Saat YOLO Aktif (Mode Headless):** Badge & dot hijau emerald `● CAM READY · LATAR BELAKANG` (outline tetap `#332C25`).
     * **Saat YOLO Aktif (Mode Eksternal + PIP):** Badge & dot hijau emerald `● CAM READY · JENDELA PREVIEW` (outline tetap `#332C25`).
     * *(Rujukan lengkap ketiga varian badge navbar tersedia di `SCREEN 06`, bottom-right).*
3. **Kartu Level 1 (Smog Valley - Aktif):**
   * Background: Kertas krem solid 100% opaque (`#FBF6EB`) dengan border tinta `#2D2926` & sudut melengkung (*border-radius 16px*).
   * Ilustrasi Biome: [`UI_Biome_01_Smog_Valley.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/UI_Biome_01_Smog_Valley.jpg) (desa hijau dengan cerobong asap abu-abu).
   * Ribbon Lipat Atas: Warna Teal (`#1ABC9C` / `#16A085`) dengan teks `"01"`.
   * Judul: `"SMOG VALLEY"`, subtext `"90s"`. Rating: 3 Bintang emas solid (⭐⭐⭐).
   * Tombol Aksi: Coral-red pill button (`#E74C3C` / `#D63031`) dengan teks putih tebal `"PLAY"`.
4. **Kartu Level 2 (Wildfire Woods - Terbuka):**
   * Ilustrasi Biome: [`UI_Biome_02_Wildfire.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/UI_Biome_02_Wildfire.jpg) (hutan terbakar di tepi desa).
   * Ribbon Lipat Atas: Warna Oranye (`#E67E22`), judul `"WILDFIRE"`, subtext `"150s"`, rating ⭐⭐☆, tombol beige `"SELECT"` (`#E0D5BE`).
5. **Kartu Level 3 (Drought Plateau - Terkunci):**
   * Ilustrasi Biome: [`UI_Biome_03_Drought.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/UI_Biome_03_Drought.jpg) (danau kering pecah-pecah dengan efek desaturasi/terkunci).
   * Ribbon Lipat Atas: Warna Abu-abu netral (`#95A5A6`), judul `"DROUGHT"`, ikon gembok solid `[🔒]` di tengah (syarat buka: min. 1 bintang di Level 2), tanpa tombol aksi.

---

### 2.2 Tab 2: [ CALIBRATION ] (Kalibrasi Biometrik & Live Naga — 15 Kartu Pose)
*Rujukan Desain Terbaru:* Page `Design` -> `SCREEN 01 - Calibration & Motion Tutorial - Design` (`UI AR Dragon.figcraft`) — Disinkronkan dengan seluruh mekanisme nyata *Sky Peck + On-Ground Arm-Proximity Locomotion (v6.1)*, *Village Actions*, dan kalibrasi UDP `5054` (Laptop B).

```
+----------------------------------------------------------------------------------------------------------+
| AR DRAGON · SKY GUARDIAN         [ CALIBRATION (Aktif) ]   [ MISSIONS ]   [ SETTINGS ]      [ESC · BACK] |
+----------------------------------------------------------------------------------------------------------+
|  +------------------------------------------------+  +------------------------------------------------+  |
|  | CAMERA MIRROR · BIOMETRIC CALIBRATION          |  | LIVE DRAGON PREVIEW · GLIDE (T-POSE)           |  |
|  | [Skeleton Upper-Body+Hips] | [CALIB 5/5]       |  | [STATE: GLIDE · SPD:14 · PITCH:-4 · SWOOP]     |  |
|  | (NOSE,LS/RS,LE/RE,LW/RW,   | [ARM GATE · CONF] |  |        (Model 3D Naga Merespons Live          |  |
|  |  LH/RH Virtual Hips Ready) | [BASELINE 100%]   |  |          via RenderTexture di Pedestal)        |  |
|  | [TRACKING: ACTIVE]   [RECALIBRATE(C)] [RESET]  |  | [STATUS: GLIDE]       [GROUND: WALK · LEAN:0°] |  |
|  +------------------------------------------------+  +------------------------------------------------+  |
|                                                                                                          |
|  [GROUNDED IDLE]  [GROUNDED WALK]   [GROUND LOOK UP]   [TAKEOFF]          [LEAN & ARM TILT]              |
|  [FLAP TO CLIMB]  [FLY UP]          [GLIDE (ACTIVE)]   [HOVER MODE]       [GENTLE DESCENT]               |
|  [FLY DOWN (Y)]   [FLY DOWN (W)]    [TUCK TO DIVE]     [PROPELLER DASH]   [VILLAGE ACTIONS]              |
|                                                                                                          |
|  [ARM GATE RULE: Tucked vs Away]    [SEATED & SAFETY: Virtual Hips & Tracking Loss]      [READY TO FLY]  |
+----------------------------------------------------------------------------------------------------------+
```

#### Komponen UI:
1. **Panel Kiri (`CAMERA MIRROR · BIOMETRIC CALIBRATION`, `860 × 410 px`):**
   * **Header Status & Tombol `Matikan` (Baris Atas Panel):** Di sebelah kanan judul terpasang pil status dinamis (mis. emerald **`● LIVE · JENDELA PREVIEW`**) dan tombol merah taktil **`Matikan`** (`#CF5B52`) yang tampil saat kamera `MEMUAT YOLO` atau `LIVE` untuk kembali ke status `STANDBY`.
   * **Strip Pemilih Kamera & Dual Mode YOLO (`804 × 40 px`, `#FBF6EB` solid opaque):** Terletak tepat di atas viewport cermin kamera:
     - **Dropdown Input Kamera:** Memilih perangkat terdeteksi (mis. `[6] OBS Virtual Camera ▼` atau default unselected `"Pilih Input Kamera... ▼"`) dengan popup daftar **100% solid opaque** (`#FBF6EB`).
     - **Tombol `Scan`:** Memindai ulang daftar kamera DirectShow secara asinkron jika webcam baru dihubungkan.
     - **Dua Tombol Mode YOLO:** **`Latar Belakang`** (*Headless / PIP Only*) & **`Jendela Preview`** (*External OpenCV Window + PIP*). Tombol aktif diberi penanda titik `●` + aksen hijau emerald dan pemain dapat berpindah langsung antar-mode.
   * **Area Kiri Viewport (`248 px` tinggi, bingkai tinta seragam `#332C25`):** Mirror kamera / radar skeleton **Upper-Body + Hips** (`NOSE, LS, RS, LE, RE, LW, RW, LH, RH` — mendukung mode duduk lewat *virtual hip synthesis*).
   * **Sidebar HUD Kanan (100% solid opaque `#1A2125` di dalam viewport):** Badge `CALIB STATE 5/5 · PROFILE SAVED`, badge `ARM GATE: AWAY · CONF 94%`, kartu progress bar **Neutral Posture Baseline** (`0–100%`), dan kotak instruksi kalibrasi 3 detik.
   * **Bar Status & Trigger Bawah (UDP Port `5054`):** Badge hijau `TRACKING: ACTIVE (CUDA)`, tombol amber `RECALIBRATE (C)` (`CALIBRATE` / `CANCEL`), dan tombol netral `RESET DEFAULT` (`CALIBRATE_RESET`).
2. **Panel Kanan (`LIVE DRAGON PREVIEW · GLIDE (T-POSE)`, `860 × 410 px`):**
   * Menampilkan model naga 3D (`Dragon_Model.blend`) via `RenderTexture` di atas pedestal yang merespons gestur secara langsung.
   * Pil Telemetri Atas: Menampilkan status live (`STATE`, `SPD`, `PITCH`, `SINK`, `SWOOP: READY`).
   * Bar Bawah: Pil teal `STATUS: GLIDE (ARMS AWAY)` (berubah sesuai state aktif) + pil netral `GROUND: WALK 3.5 m/s · LEAN: 0°`.
3. **Matriks 15 Kartu Panduan Pose & Mekanik (`3 Baris × 5 Kolom`, `340 × 122 px`):**
   * **Baris 1 (Ground, Transisi, & Kemudi):**
     - **`GROUNDED IDLE`** (`GROUND · 0 m/s · STAND STRAIGHT`): Berdiri/duduk tegak lurus dengan lengan rapat di sisi badan (`tucked`) -> naga diam sepenuhnya di tempat di darat.
     - **`GROUNDED WALK`** (`GROUND · 3.5 m/s · ARMS AWAY`): Rentangkan lengan atas menjauh (`T-pose` darat) -> berjalan maju `3.5 m/s`.
     - **`GROUNDED LOOK UP`** (`GROUND · 0 m/s · +15°..25° PITCH`): Angkat dada / *torso tilt* ke atas tanpa kepakan -> naga mendongak ke langit tanpa terbang.
     - **`TAKEOFF`** (`TRANSITION · >=0.25s · +7m LIFT`): Di darat, kepakkan kedua lengan kuat (`>=0.25s`) -> melesat lepas landas ke udara.
     - **`LEAN & ARM TILT`** (`STEERING · GROUND & AIR · SEATED OK`): Condongkan badan (`50%`) atau miringkan tinggi lengan (`50%`) untuk berputar di tempat, berbelok saat jalan, atau *bank-turn* saat terbang.
   * **Baris 2 (Penerbangan Inti):**
     - **`FLAP TO CLIMB`** (`AIR · 12–19.5 m/s · SMOOTH CLIMB`): Kepakkan kedua lengan penuh secara ritmis melewati bahu untuk menanjak stabil.
     - **`FLY UP`** (`AIR · +19° PITCH · STEEP CLIMB`): Angkat dada / *torso tilt* ke atas sambil mengepak aktif untuk menanjak curam ke langit tinggi.
     - **`GLIDE (T-POSE)`** `[ACTIVE]` (`AIR · 14 m/s · SWOOP READY`): Rentangkan lengan mendatar sejajar bahu (`T-pose` tanpa kepak) untuk meluncur santai; memicu *Swoop Climb* otomatis paska-*dive*.
     - **`HOVER MODE`** (`AIR · 0 m/s · STATIONARY`): Rapatkan lengan atas ke rusuk & kepakkan lengan bawah secara ritmis -> melayang diam di tempat di udara.
     - **`GENTLE DESCENT`** (`AIR · 8.5 m/s · -1.6 m/s SINK`): Rapatkan lengan atas ke badan & tahan lengan bawah rileks tanpa kepak -> turun perlahan menuju pendaratan mulus.
   * **Baris 3 (Turun Presisi, Tukik, Boost, & Desa):**
     - **`FLY DOWN (POSE Y)`** (`AIR · 12 m/s FWD · -5.5 m/s SINK`): Lengan atas menjauh (`Away`) + lengan bawah mengarah ke atas membentuk huruf **`Y`** -> turun menukik pelan sambil tetap maju.
     - **`FLY DOWN IDLE (POSE W)`** (`AIR · 0 m/s · -3.2 m/s SINK`): Lengan atas rapat ke badan (`Tucked`) + lengan bawah mengarah ke atas membentuk huruf **`W`** -> turun vertikal perlahan di tempat.
     - **`TUCK TO DIVE`** (`AIR · -45° PITCH · UP TO 28 m/s`): Tangan dirapatkan/mengepal di dada atas dengan siku mengarah ke bawah -> menukik curam berkecepatan tinggi.
     - **`PROPELLER DASH`** (`AIR BOOST · 26 m/s BURST`): Putar kedua lengan melingkar secara terus-menerus -> dorongan kecepatan instan di udara.
     - **`VILLAGE ACTIONS`** (`AUTO-HOVER · 3 BIOME GESTURES`): Smog: kepak sayap · Fire: buka mulut lebar / tangkup tangan di dada · Drought: angkat kedua tangan tinggi di atas kepala.
4. **Bar Aturan Inti & CTA Bawah:**
   * **2 Pil Kiri:** `ARM GATE RULE` (ringkasan perbedaan perilaku *Tucked* vs *Away*) & `SEATED & SAFETY` (dukungan *Virtual Hips* mode duduk & perilaku aman *Tracking Loss* di darat/udara).
   * **Pojok Kanan Atas:** Tombol `ESC · BACK` konsisten kembali ke tab Missions.
   * **Pojok Kanan Bawah:** Tombol hijau 3D-bevel `READY TO FLY` langsung memulai penerbangan.

---

### 2.3 Tab 3: [ SETTINGS ] / SCREEN 05 (Pengaturan Audio, Sensitivitas & Kamera)
*Artboard FigCraft:* `SCREEN 05 - Settings Modal (1920x1080)` (`Design` id `400`, `Wireframe` id `236`) · Menggunakan kartu kertas krem solid 100% opaque (`#FBF6EB`) dengan tombol `ESC · BACK` tetap di kanan atas:
1. **4 Slider Pengaturan Inti (`Master Volume`, `BGM Volume`, `SFX Volume`, `Turn Sensitivity` $1.0\times$ s/d $3.0\times$).**
2. **Modul `Camera Input & AI YOLO Module`:**
   * **Header Modul:** Menampilkan badge status aktif (`● KAMERA STANDBY` secara default; `autoLaunchPython = false` agar webcam laptop tidak terkunci otomatis saat boot) dan tombol merah taktil **`Matikan`** di kanan header (nonaktif saat `STANDBY`, menyala aktif saat `MEMUAT YOLO` atau `LIVE`).
   * **Dropdown Input Kamera + Tombol `Scan`:** Dropdown dimulai dengan placeholder unselected `"Pilih Input Kamera... ▼"`. Tombol **`Scan`** di kanan dropdown memindai ulang perangkat kamera (`[0] USB2.0 HD UVC WebCam`, `[6] OBS Virtual Camera`, dll.).
   * **Daftar Popup Dropdown 100% Solid Opaque (`#FBF6EB`, `alpha = 1.0f`):** Latar belakang menu popup wajib solid penuh agar daftar perangkat terbaca tajam tanpa tembus teks modul di belakangnya.
   * **Dua Tombol Aktivasi YOLO (Tepat di Bawah Dropdown):**
     * **`Latar Belakang`** *(Headless / In-Game PIP Only)*: Menjalankan `YOLOv8n-pose` di balik layar tanpa jendela desktop terpisah; video + skeleton hanya tampil di preview PIP/Kalibrasi dalam game.
     * **`Jendela Preview`** *(Eksternal OpenCV + PIP)*: Menjalankan `YOLOv8n-pose` sekaligus membuka jendela preview OpenCV terpisah di desktop, sementara feed PIP game tetap aktif.
     * Kedua tombol **terkunci/disabled** selama dropdown belum memilih kamera, **menyala aktif** begitu perangkat dipilih, dan tombol mode yang berjalan ditandai `●` serta dapat langsung dialihkan antar-mode.
   * **Kotak Panduan State:** Area preview/status bawah menampilkan pesan panduan sesuai state aktif (default: *"Pilih input kamera di drop-down atas"*).
3. **Tombol Konfirmasi:** `APPLY & CLOSE` di dasar kartu kontainer modal.

---

### 2.4 Referensi 5 State Visual Panel Kamera & Popup Dropdown Solid (`SCREEN 06`)
*Artboard FigCraft:* `SCREEN 06 - Camera States & Opaque Dropdown Spec (1920x1080)` (`Design` id `876`, `Wireframe` id `1022` pada `x: 0, y: 2400`)

Seluruh panel kontrol kamera pada **SCREEN 01 (Calibration)**, **SCREEN 02 (In-Game PIP)**, dan **SCREEN 05 (Settings)** memakai mesin state tunggal dengan 5 representasi visual baku berikut:

| State | Outline & Warna Badge Status | Header Kanan | Kondisi Tombol Mode (`Latar Belakang` / `Jendela Preview`) | Teks Panduan & Preview Area 16:9 |
| :--- | :--- | :--- | :--- | :--- |
| **1. `STANDBY (Belum Pilih Kamera)`** | Outline `#332C25` · Badge & dot Amber (`● KAMERA STANDBY`) | `Matikan` nonaktif (outline `#9D9384`) | **Disabled / Terkunci** (redup, belum bisa diklik) | Tampilan wireframe/standby dengan teks: *"Pilih input kamera di drop-down atas"* |
| **2. `STANDBY (Kamera Terpilih)`** | Outline `#332C25` · Badge & dot Amber (`● KAMERA STANDBY`) | `Matikan` nonaktif (outline `#9D9384`) | **Enabled / Siap** setelah dropdown terisi (mis. `[6] OBS Virtual Camera`) | Pratinjau standby siap jalan dengan teks: *"Klik Latar Belakang / Jendela Preview"* |
| **3. `MEMUAT YOLO`** | Outline `#332C25` · Badge & dot Cyan (`● MEMUAT YOLO...`) | **`Matikan` Aktif** (Fill Coral, outline `#332C25`) | Mode terpilih terkunci menunggu inisialisasi | Teks proses: *"Memuat YOLOv8n-pose & membuka kamera..."* |
| **4. `LIVE (Aktif)`** | Outline `#332C25` · Badge & dot Emerald (`● LIVE · LATAR BELAKANG` / `JENDELA PREVIEW`) | **`Matikan` Aktif** (Fill Coral, outline `#332C25`) | Tombol mode berjalan diberi penanda aktif `●`; **bisa langsung klik mode lain** untuk switch | Live feed webcam 16:9 + overlay skeleton & keypoint joint pose aktif |
| **5. `ERROR (Gagal Dibuka)`** | Outline `#332C25` · Badge & dot Coral (`● KAMERA GAGAL DIBUKA`) | `Matikan` reset / opsional | Kembali siap pilih kamera lain | Teks peringatan kontras tinggi: *"Kamera gagal dibuka! Pilih kamera lain."* |

#### Aturan Khusus Dropdown & Navbar Dinamis di `SCREEN 06`:
1. **Popup Daftar Kamera 100% Opaque (`#FBF6EB`, `alpha = 1.0f`):** Mengingat *Linear Color Space* Unity kerap membuat UI semi-transparan tampak pudar dan tembus teks di belakangnya, popup dropdown kamera (`[0] USB2.0 HD UVC WebCam`, `[6] OBS Virtual Camera`, dst.) wajib memakai background krem `#FBF6EB` opaque penuh dengan border `#2D2926` 2px dan penanda baris terpilih.
2. **3 Varian Badge Navbar `SCREEN 00`:** Ditampilkan di pojok kanan bawah `SCREEN 06` (`● STANDBY · PILIH KAMERA`, `● CAM READY · LATAR BELAKANG`, dan `● CAM READY · JENDELA PREVIEW`).

---

## 🎮 3. SPESIFIKASI IN-GAME GUI & FINISH MODAL

Menggunakan bahasa visual kartun minimalis solid yang sama:

### 3.1 In-Game Flight HUD / SCREEN 02 (Resmi — Compact Space-Saving Design)
*Rujukan Visual:* [`UI_InGame_Flight_HUD.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/UI_InGame_Flight_HUD.jpg) · *Artboard FigCraft:* `SCREEN 02 - In-Game Flight HUD (1920x1080)` (`Design` id `348`, `Wireframe` id `184`) · *Latar belakang gameplay dibuat kosong/blank transparan karena langsung menumpuk di atas kamera game 3D.*

```
+---------------------------------------------------------------------------------------------------+
|                             [ 02:15  ·  VILLAGES: 1/3 ]                            [ ESC · PAUSE ]|
|                                                                                                   |
| [33% 🍃]                                                                                          |
| +---+                                                                                             |
| |===|                                         ▲                                                   |
| |===|                                [ WILDFIRE · 140m ]                                          |
| |   |                                                     +-------------------------------------+ |
| |   |                                                     | ▲ 42m  12.0m/s            [ POSE T ]  | |
| |   |                                                     +-------------------------------------+ |
| |   |                                                     | ● LIVE · LATAR BELAKANG   [Matikan] | |
| |   |                                                     | [0] USB2.0 HD UVC WebCam ▼  [Scan]  | |
| |   |                                                     | [● Latar Belakang][Jendela Preview] | |
| +---+                                                     +-------------------------------------+ |
|                                                           |  LIVE · PIP 30 FPS                  | |
|                                                           |        (Skeleton Overlay 16:9)      | |
|                                                           +-------------------------------------+ |
+---------------------------------------------------------------------------------------------------+
```

#### Komponen In-Game HUD:
1. **Tengah Atas (Timer & Objektif Desa Mungil):**
   * Kapsul krem solid `#FBF6EB` ramping tanpa pita: ikon jam pasir + digital countdown `02:15`, pemisah titik, dan status `VILLAGES: 1 / 3` dengan tanda centang hijau.
2. **Kanan Atas (Tombol Pause):**
   * Tombol pil `ESC · PAUSE` solid `#FBF6EB` tetap konsisten di sudut kanan atas.
3. **Sisi Kiri Layar (Global Vitality Progress Bar Vertikal):**
   * Bar meteran vertikal ramping mengisi dari bawah ke atas warna hijau emerald mewakili energi pemulihan pulau, dengan lencana persentase `33%` di puncak.
4. **Klaster Kanan Bawah (Telemetri Naga + Header Kontrol Kamera + Monitor PIP 16:9):**
   * **Dock Telemetri (`y: 632`):** Kapsul ramping paling atas menampilkan ketinggian `▲ 42m`, kecepatan angin `12.0m/s`, dan badge cyan `POSE T` (`POSE Y` menanjak / `POSE A` menukik).
   * **Header Kontrol Kamera PIP (`PIP Camera Control Header`, `420×112` tepat di atas kotak PIP):** Kartu krem solid `#FBF6EB` tanpa transparansi berisi:
     * Baris status: badge state (`● LIVE · LATAR BELAKANG`, dll.) + tombol merah taktil **`Matikan`** di kanan.
     * Baris pemilih input: dropdown kamera (`Pilih Input Kamera... ▼` / `[0] USB2.0 HD UVC WebCam ▼`, popup solid 100% opaque `#FBF6EB`) + tombol **`Scan`**.
     * Baris aktivasi YOLO: tombol **`Latar Belakang`** dan **`Jendela Preview`** (terkunci sebelum pilih kamera; langsung switch mode saat aktif).
   * **Monitor Kamera PIP (`420×236`, rasio 16:9):** Outline tinta seragam `#332C25` pada seluruh state (state `LIVE`, `STANDBY`, `MEMUAT YOLO`, dan `ERROR` ditunjukkan oleh isi badge status, dot indikator, tombol mode aktif, serta pesan panduan di dalam panel — tanpa outline berwarna), menampilkan live feed webcam + skeleton pose atau pesan panduan state terkait.
5. **Tengah Layar (Subtle Waypoint Compass Pointer):**
   * Panah chevron kartun halus mengarah ke desa terdekat: `▲ WILDFIRE · 140m`.
6. **Kelegaan Layar:** Dengan memadatkan kontrol kamera tepat di atas kotak PIP sudut kanan bawah, lebih dari **85% area tengah dan kiri layar tetap lapang** (~88% viewport terbuka).

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

1. **Aset Grafis & Material Solid Opaque (`#FBF6EB`):**
   * Menggunakan [`UI_Bg_Lobby.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/UI_Bg_Lobby.jpg) sebagai latar belakang menu lobby, sedangkan canvas **In-Game Flight HUD** dibiarkan kosong/transparan agar pemandangan pulau 3D tampil langsung.
   * Semua panel, kartu, dan terutama **popup daftar dropdown kamera** di Unity *Linear Color Space* wajib dikonfigurasi dengan **opasitas solid penuh (`alpha = 1.0f`, warna `#FBF6EB`)** tanpa glassmorphism/transparansi agar tidak tembus teks di belakangnya.
2. **Kontrol Kamera & Dual-Mode AI YOLO (`PythonProcessManager.cs` & `InGameFlightHUD.cs`):**
   * Default startup menggunakan `autoLaunchPython = false` sehingga kamera berada dalam status `STANDBY` dan tidak mengunci webcam bawaan laptop.
   * **Scan & Pilih Kamera:** Memanggil `RefreshCameraListAsync()` untuk memindai daftar kamera DirectShow (`enumerate_dshow_cameras()` / `--list-json`) lalu mengunci target dengan `SelectCamera()` (`--cam-name`).
   * **Dua Mode Peluncuran (`CameraLaunchMode`):** `LaunchBackgroundMode()` menyalakan YOLO headless (`--headless`, hanya feed PIP in-game), sedangkan `LaunchExternalPreviewMode()` membuka jendela OpenCV terpisah + feed PIP in-game. Perpindahan langsung antar mode didukung sebelum maupun saat `LIVE`, dan tombol `Matikan` mengembalikan proses ke `STANDBY`.
   * State mesin (`STANDBY Belum Pilih`, `STANDBY Terpilih`, `MEMUAT YOLO`, `LIVE`, `ERROR`) tersinkronisasi antara badge navbar **SCREEN 00**, panel kiri **SCREEN 01**, header PIP **SCREEN 02**, dan modal **SCREEN 05**.
3. **Sistem Tab Navigasi (`LobbyNavigationManager.cs`):**
   * Mengatur aktif/nonaktif panel `Panel_Missions`, `Panel_Calibration`, dan `Panel_Settings` secara instan atau dengan transisi geser mulus (*smooth horizontal slide*).
4. **Integrasi Live Dragon Preview di Kalibrasi:**
   * Menggunakan **RenderTexture** sekunder dengan kamera khusus yang menyorot model `Dragon_Model.blend` di scene terisolasi. Parameter pose dari `UDPPoseReceiver.cs` langsung menggerakkan tulang sayap dan badan naga di pratinjau tersebut.
5. **Font & Tipografi:**
   * Menggunakan **TextMeshPro (TMP)** dengan font bergaya *clean geometric rounded sans-serif* (seperti *Nunito*, *Quicksand*, atau *Fredoka One*) dengan warna tinta `#2D2926`.


---
**Navigasi Obsidian:** [[00 - Overview]] · [[konteks]] · [[prd]] · [[Tasks]] · [[Devlog]]
