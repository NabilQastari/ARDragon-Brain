---
title: "Rencana Desain & Implementasi UI (v1.7)"
tags:
  - ui-ux
  - design
  - laptop-a
  - unity-ui
status: approved
owner: Laptop A
updated: 2026-11-15
related:
  - "[[00 - Overview]]"
  - "[[konteks]]"
  - "[[prd]]"
---

# 🎨 DOKUMEN RENCANA DESAIN & IMPLEMENTASI UI (v1.7)
## FANAMIR: THE SKY GUARDIAN

> **Status Desain:** Dikunci & Disetujui (*Design Approved* — Seluruh layar utama, overlay *Pose Guide*, **Quest Objective + Quest Narrative Box**, & spec 5 State Kamera tersedia di [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) pada page `Wireframe` dan **`9` artboard** page `Design` **`SCREEN 00–08`** dengan gaya *Fantasy Parchment-and-Ink Tabletop* `v1.7`: pembaruan branding **`Fanamir: The Sky Guardian`** / **`Fanamir`**, panel **`Quest Objective`** di kiri atas `SCREEN 02`, serta artboard overlay **`SCREEN 08`** untuk **Quest Narrative Text Box** saat Fanamir masuk area desa dan memainkan *Idle Fly*).
> **Aset Rujukan:** Tersedia di folder [`Referensi UI/`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/).
> **Prinsip Utama:** *Fantasy Parchment-and-Ink Tabletop Card UI* — Kertas perkamen solid (`#F2EFE7` / `#FBF6EB`, **100% opaque / `alpha = 1.0f`, zero transparency / zero glassmorphism**), sudut **stepped rabbeted plaques** (*vector staircase corner cutouts*) dengan bingkai ganda arsitektural kontinu (*stepped + concave scalloped inner frame* + ornamen sudut *Corner Fan-Medallion* `24×24 px`), spanduk judul *Stepped Banner* bertumpuk batas atas kartu lengkap dengan ornamen diamond *fleuron* & *Baroque Scrollwork Crest*, kontrol tajam `r = 1–4 px`, *outlined* tinta netral seragam `#332C25` dengan bibir 3D *hard cast shadow* nol-blur (`radius: 0`), slot info *borderless recessed taupe* (`#C8BFA8`/`#D5CCBA`) bermedali kiri tumpang-tindih, kotak bintang ber-pill gelap bawah (`STAR 1..3`), tag speech-bubble emas `#E2A43B` (*coin medallion + corner hatch ticks*) tepat di atas tombol aksi utama, palet tombol 3D bevel (`#5C8D46` / `#3D642D` bersheen & takik ganda kiri, sekunder `#E6DFD2` / `#BDB3A0`, locked charcoal `#2E3236`, dan tombol `ESC` krem-merah `#F3EFE6` / `#D15046`), serta latar belakang blank putih `#FFFFFF` khusus layar in-game agar langsung menampakkan viewport Unity (`> 85%` ruang bebas di `SCREEN 02`).
> **Catatan Placeholder Runtime:** Visual vektor naga 3D & kerangka *stick-figure* di artboard FigCraft berstatus *placeholder visual* yang akan digantikan oleh kamera/model realtime (`RenderTexture` + `RawImage` & overlay kerangka pose) saat implementasi di Unity.

### 🧱 Token Gaya Visual Final (Page `Design` v1.6)
- **Permukaan & Bingkai:** Latar kartu `#F2EFE7`, kartu fokus/aksi `#F4F1EA`/`#ECE6DA`, sumur stat resessif *borderless* `#D5CCBA` / `#C8BFA8` / `#C2B9A6`, strip note/dropdown solid `#FBF6EB`, outline tinta utama seragam `#332C25` (`2.5–3 px`, `strokeAlign: inside`, `miter`), dan *hard shadow* nol-blur bertingkat (`#4A4238` `[0, 7]` pada kartu utama, `#5C5346` `[0, 4]` pada sub-kartu pose & spanduk, `#332C25` `[0, 2..6]` pada kontrol/HUD/tab/speech tag, `#181A1C` pada slot `LOCKED`).
- **Konsistensi Warna Outline Netral (v1.5–v1.6):** Seluruh kontainer UI (kartu utama, sub-panel, kartu pose, badge status kamera navbar, dropdown, viewport monitor/PIP, kotak stat, slot info, dan kartu state `SCREEN 06`) menggunakan **outline tinta seragam `#332C25`** atau tanpa border untuk sumur stat resessif (`strokes: []`) — tanpa outline berwarna merah, biru/cyan, hijau, atau oranye/kuning. Variasi netral terbatas pada `#A89E8B` (*inner rim* tombol `ESC`/tutup/jeda), `#9D9384` (tombol nonaktif `Matikan`), `#4B565D` (badge di dalam viewport gelap `#1A2125`), serta `#A39884` / `#827766` untuk garis bingkai & ornamen sudut dalam perkamen. State kamera tetap dibedakan lewat *fill* badge, dot indikator, teks panduan, dan tombol mode aktif.
- **Geometri Sudut Kartu & Kontrol (Sesuai Referensi):**
  - **Kartu Utama, Panel Kalibrasi, & Modal (`460×680`, `860×540`, `720×652`, `680×792`, `760×874`, `1824×824`):** Siluet vektor **Stepped Rabbeted Plaque (`a = 14, b = 7`)** bertakik 2 undakan di keempat sudut, dipasangi dua lapisan *continuous inner border*: outer stepped frame (`inset = 10`, `a = 12, b = 6`, `#A39884`) + inner concave scalloped arch frame (`inset = 15`, arch `r = 18`, `#827766`, `fill: none`) + 4 blok ornamen sudut **Corner Fan-Medallion (`24×24 px`)** pada kartu utama `SCREEN 00, 03, 04, 05, 07`.
  - **Spanduk Judul & Ornamen Atas (`SCREEN 00–07`):** Seluruh banner judul memakai siluet **Stepped Plaque (`a = 6..10, b = 3..5`)** dengan inner stepped trim `#A89E8B` + diamond *fleuron* kiri-kanan + bibir 3D `[0, 4]` (banner `SCREEN 03` menumpuk garis atas kartu pada `y = 155`), dilengkapi **Baroque Scrollwork Crest (`280×34 px`)** simetris di atas banner `SCREEN 03, 04, 05, 07` serta tag `STAGE 01–03` bersheen di atas kartu `SCREEN 00`.
  - **Sub-Panel, Slot Borderless & Speech-Bubble Callouts:** `Victory Stat Box` (`560×278`), `Cam Module` (`640×342`, `#ECE6DA`), 7 kartu state `SCREEN 06` (`a = 10, b = 5`), **4 kartu pose dinamis `SCREEN 01`** (`422×166`, `a = 8, b = 4`) + **15 kartu panduan pose `SCREEN 07`** (`340×154`, `a = 8, b = 4`, bibir 3D `[0, 4]`, plus `[0, 3]` pada 15 medali ikon), 4 baris slider `SCREEN 05`, 3 baris stat `SCREEN 04`, dan plakat gestur `SCREEN 03`. Baris info sekunder (`Sub Track` `SCREEN 00`, `Lean Tag` `SCREEN 01`, `Val Track`, `Notes 1–5` & `Proof Rows` `SCREEN 06`) memakai gaya **borderless recessed taupe** + medali bundar `#38312A` menumpang tepi kiri. Tag waktu/reward (`90s`, `150s`, `+3 STARS`) berbentuk **gold speech-bubble** (`#E2A43B`) bermedali koin & *hatch ticks* tepat di atas tombol utama.
  - **Kontrol, Tombol, Tab, Dropdown, Box Bintang & Slot Terkunci:** Sudut mikro-tegas **`r = 2–4 px`** (`r = 1 px` untuk *inner rim* `#A89E8B` tombol `ESC`/tutup). Kotak bintang `SCREEN 00` & `04` dilengkapi pill gelap bawah (`STAR 1..3`, `STAR 01..03`, `0 / 1 ★`); kartu & tombol terkunci (`Card 03`) memakai *charcoal slate* `#2B2E31` / `#2E3236`; dan tombol `RECALIBRATE (C)` `SCREEN 01` diset teks `#FFFFFF` ber-sheen & takik ganda kiri agar kontras tinggi.
- **Hierarki Komponen Khas:** Navbar rail `#CFC6B4` (`[0, 4]` `#332C25`) dengan tab aktif emas `#E5AF46` + bibir `#B37C1E`, pill HUD `SCREEN 02` berbintang bayangan solid tanpa mengganggu `> 85%` viewport gameplay kosong, serta `SCREEN 06` dengan popup dropdown `#FBF6EB` bertingkat bayangan `[0, 6]`.

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
| FANAMIR: THE SKY GUARDIAN    [ MISSIONS (Aktif) ] [ CALIBRATION ] [ SETTINGS ]  [● STANDBY] [ESC·QUIT]|
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
1. **Header Kiri Atas & Tab Navigasi:** Teks judul bersih `FANAMIR: THE SKY GUARDIAN` (Font: *Geometric Sans-Serif Bold*, warna: `#2D2926` Dark Ink) diikuti tab pill `[ MISSIONS ]`, `[ CALIBRATION ]`, dan `[ SETTINGS ]`.
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

### 2.2 Tab 2: [ CALIBRATION ] (Kalibrasi Biometrik & Live Naga — 4 Kartu Riwayat Dinamis + Pose Guide Modal)
*Rujukan Desain Terbaru:* Page `Design` -> `SCREEN 01 - Calibration & Motion Tutorial - Design` (`id: 314`) & `SCREEN 07 - Pose Guide Overlay Modal - Design` (`id: 1893`) pada [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) — Disinkronkan dengan mekanisme *Sky Peck + On-Ground Arm-Proximity Locomotion (v6.1)*, kalibrasi UDP `5054` (Laptop B), serta pratinjau skeleton & naga berukuran besar (`860×540 px`).

```
+----------------------------------------------------------------------------------------------------------+
| FANAMIR: THE SKY GUARDIAN        [ CALIBRATION (Aktif) ]   [ MISSIONS ]   [ SETTINGS ]      [ESC · BACK] |
+----------------------------------------------------------------------------------------------------------+
|  +------------------------------------------------+  +------------------------------------------------+  |
|  | CAMERA MIRROR · TRACKING                       |  | LIVE FANAMIR PREVIEW · GLIDE (T-POSE)          |  |
|  | [Dropdown Cam] [Scan] [Latar Blkg] [Jendela]   |  | [STATE: GLIDE · SPD:14 · PITCH:-4 · SWOOP]     |  |
|  | [Skeleton Upper-Body+Hips  |  CALIB 5/5      ] |  |                                                |  |
|  | [  380 px tinggi — lega!   |  ARM GATE·100%  ] |  |      (Model 3D Naga Merespons Live 438 px      |  |
|  | [  NOSE, LS/RS, LE/RE,     |  HOLD 3s GUIDE  ] |  |        via RenderTexture di Pedestal)          |  |
|  | [TRACKING: ACTIVE]   [RECALIBRATE(C)] [RESET]  |  | [STATUS: GLIDE]       [GROUND: WALK · LEAN:0°] |  |
|  +------------------------------------------------+  +------------------------------------------------+  |
|                                                                                                          |
|  [RECENT POSES · MAX 4]  Default at launch: 4 Main Poses (Glide, Flap, Takeoff, Tuck Dive)...  [POSE GUIDE (15)]
|  +-----------------------+  +-----------------------+  +-----------------------+  +-----------------------+ |
|  | GLIDE (T-POSE)  [ACT] |  | FLAP TO CLIMB  [DEF]  |  | TAKEOFF        [DEF]  |  | TUCK TO DIVE   [DEF]  | |
|  | AIR·14m/s·SWOOP READY |  | AIR·12-19.5m/s·CLIMB  |  | TRANS·>=0.25s·+7m     |  | AIR·-45° PITCH·28m/s  | |
|  +-----------------------+  +-----------------------+  +-----------------------+  +-----------------------+ |
|                                                                                                          |
|  [ARM GATE RULE: Tucked vs Away]   [SEATED & SAFETY: Virtual Hips & Tracking Loss]       [READY TO FLY]  |
+----------------------------------------------------------------------------------------------------------+
```

#### Komponen UI `SCREEN 01`:
1. **Panel Kiri (`CAMERA MIRROR · TRACKING`, `860 × 540 px`, posisi `(80, 114)`):**
   * **Header Status & Tombol `Matikan` (Baris Atas Panel):** Di sebelah kanan judul terpasang pil status dinamis (mis. emerald **`● LIVE · JENDELA PREVIEW`**) dan tombol merah taktil **`Matikan`** (`#CF5B52`) yang tampil saat kamera `MEMUAT YOLO` atau `LIVE` untuk kembali ke status `STANDBY`.
   * **Strip Pemilih Kamera & Dual Mode YOLO (`804 × 40 px`, `#FBF6EB` solid opaque):** Terletak tepat di atas viewport cermin kamera:
     - **Dropdown Input Kamera:** Memilih perangkat terdeteksi (mis. `[6] OBS Virtual Camera ▼` atau default unselected `"Pilih Input Kamera... ▼"`) dengan popup daftar **100% solid opaque** (`#FBF6EB`).
     - **Tombol `Scan`:** Memindai ulang daftar kamera DirectShow secara asinkron jika webcam baru dihubungkan.
     - **Dua Tombol Mode YOLO:** **`Latar Belakang`** (*Headless / PIP Only*) & **`Jendela Preview`** (*External OpenCV Window + PIP*). Tombol aktif diberi penanda titik `●` + aksen hijau emerald dan pemain dapat berpindah langsung antar-mode.
   * **Area Kiri Viewport (`380 px` tinggi, bingkai tinta seragam `#332C25`):** Ruang pengawasan skeleton jauh lebih lapang (+132 px dibanding v1.6 awal), menampilkan mirror kamera / radar skeleton **Upper-Body + Hips** (`NOSE, LS, RS, LE, RE, LW, RW, LH, RH` — mendukung mode duduk lewat *virtual hip synthesis*).
   * **Sidebar HUD Kanan (100% solid opaque `#1A2125` di dalam viewport):** Badge `CALIB STATE 5/5 · PROFILE SAVED`, badge `ARM GATE: AWAY · CONF 94%`, kartu progress bar **Neutral Posture Baseline** (`0–100%`), dan kotak instruksi kalibrasi 3 detik.
   * **Bar Status & Trigger Bawah (UDP Port `5054`):** Badge hijau `TRACKING: ACTIVE (CUDA)`, tombol amber `RECALIBRATE (C)` (`CALIBRATE` / `CANCEL`), dan tombol netral `RESET DEFAULT` (`CALIBRATE_RESET`).
2. **Panel Kanan (`LIVE DRAGON PREVIEW · GLIDE (T-POSE)`, `860 × 540 px`, posisi `(980, 114)`):**
   * Ruang viewport pratinjau naga diperbesar hingga **`438 px` tinggi**, menampilkan model naga 3D (`Dragon_Model.fbx`) via `RenderTexture` di atas pedestal yang merespons gestur secara langsung.
   * Pil Telemetri Atas: Menampilkan status live (`STATE`, `SPD`, `PITCH`, `SINK`, `SWOOP: READY`).
   * Bar Bawah: Pil teal `STATUS: GLIDE (ARMS AWAY)` (berubah sesuai state aktif) + pil netral `GROUND: WALK 3.5 m/s · LEAN: 0°`.
3. **Strip Riwayat Pose & Tombol Pembuka Overlay (`y = 676..678`):**
   * **Kiri (`Pose History Strip`, `1372 × 50 px`):** Pill tag amber **`RECENT POSES · MAX 4`** (`#E2A43B`) diikuti teks penjelas *"Default at launch: 4 Main Poses (Glide, Flap, Takeoff, Tuck Dive) · Slots update dynamically with your last 4 detected poses"*.
   * **Kanan (`Btn Open Pose Guide`, `368 × 54 px`):** Tombol sekunder krem bevel (`UI_Btn_Secondary_Cream_9Slice`) bertuliskan **`OPEN POSE GUIDE`** lengkap dengan pill emas **`15 POSES`** untuk membuka overlay **`SCREEN 07`**.
4. **Baris `1 × 4` Kartu Pose Dinamis (`y = 746`, ukuran `422 × 166 px`, jarak antar-kartu `24 px`):**
   * **Kapasitas Maksimal 4 Kartu:** Hanya menampilkan maksimal **4 mini card pose** berukuran lebih lega & mudah dibaca sekilas dibanding grid 15 kecil.
   * **Kondisi Default Saat Game Baru Dijalankan (*Startup* / Reset):** Secara default mengisi Slot 1–4 dengan **4 pose utama penerbang**:
     1. **Slot 1:** **`#08 · GLIDE (T-POSE)`** (status default / aktif awal: `UI_Plaque_PoseCard_Active_9Slice`, badge `SLOT 1 · ACTIVE`)
     2. **Slot 2:** **`#06 · FLAP TO CLIMB`** (`UI_Plaque_PoseCard_Default_9Slice`, badge `SLOT 2 · DEFAULT`)
     3. **Slot 3:** **`#04 · TAKEOFF`** (`UI_Plaque_PoseCard_Default_9Slice`, badge `SLOT 3 · DEFAULT`)
     4. **Slot 4:** **`#13 · TUCK TO DIVE`** (`UI_Plaque_PoseCard_Default_9Slice`, badge `SLOT 4 · DEFAULT`)
   * **Rotasi Dinamis (*Last-Performed MRU Queue*, Anti-Duplikat):** Setiap kali tracker CV mendeteksi pose baru `P` (dengan debounce stabil `~0.15s` & mengabaikan `None`), `P` dipindahkan/disisipkan ke **Slot 1** (`SLOT 1 · ACTIVE` dengan `UI_Plaque_PoseCard_Active_9Slice`), sementara pose-pose sebelumnya bergeser ke kanan (Slot `1 → 2 → 3 → 4`, item ke-5 terkeluar). Jika `P` sudah berada di Slot `2..4`, posisinya dipromosikan ke Slot `1` tanpa menduplikasi kartu, dan badge Slot `2..4` berubah menjadi `SLOT 2..4 · RECENT`.
5. **Bar Aturan Inti & CTA Bawah (`y = 940..946`):**
   * **2 Pil Kiri:** `ARM GATE RULE` (`720×50`, ringkasan *Tucked* vs *Away*) & `SEATED & SAFETY` (`720×50`, dukungan *Virtual Hips* & perilaku aman *Tracking Loss*).
   * **Pojok Kanan Atas:** Tombol `ESC · BACK` konsisten kembali ke tab Missions.
   * **Pojok Kanan Bawah:** Tombol hijau 3D-bevel `READY TO FLY` (`340×64`) langsung memulai penerbangan.

---

### 2.2.1 Overlay Modal: [ SCREEN 07 · POSE GUIDE MODAL ] (Katalog Lengkap 15 Pose & Penjelasan)
*Artboard FigCraft:* `SCREEN 07 - Pose Guide Overlay Modal - Design (1920x1080)` (`id: 1893`) · Dibuka oleh tombol **`OPEN POSE GUIDE`** pada `SCREEN 01` dan ditutup oleh tombol **`CLOSE POSE GUIDE`**, tombol **`ESC · CLOSE`** di kanan atas, atau tombol keyboard `Escape` (sebelum keluar dari tab Calibration).

1. **Backdrop & Bingkai Modal Utama (`1824 × 824 px`, posisi `(48, 136)`):**
   * Menggunakan *dark scrim* `#1B1815` (`alpha = 0.58`) menutupi background lobby agar fokus tertuju penuh ke panduan pose.
   * Plakat perkamen utama (`UI_Panel_Parchment_Full_9Slice`) bermahkota `UI_Crest_Baroque_Scrollwork` dan banner bertakik `POSE GUIDE · ALL 15 POSES` (`UI_Banner_Stepped_Modal_Blank`).
   * Pojok kanan atas layar menyediakan tombol **`ESC · CLOSE`** (`UI_Btn_TopChrome_Cream_9Slice` + `UI_IconBadge_CloseX_20`), dan bagian bawah modal menyediakan tombol bevel **`CLOSE POSE GUIDE`** (`340×64`).
2. **Strip Legenda Kategori (`y = 186`):**
   * 4 pil kategori warna lembut sesuai kelompok gerak (`01–03 · GROUND`, `04–05 · LAUNCH & STEER`, `06–12 · CORE FLIGHT`, `13–15 · DIVE · DASH · VILLAGE`) beserta pengingat aturan *"Default 4 Main Poses shown at launch (04, 06, 08, 13) · Calibration displays your last 4 detected poses"*.
3. **Grid Lengkap `3 Baris × 5 Kolom` — Semua 15 Pose (`340 × 154 px` per kartu, gap `15 × 18 px`):**
   * Badge **`MAIN`** menandai ke-4 pose utama awal (`#04`, `#06`, `#08`, `#13`); saat kamera live aktif dan overlay sedang dibuka, kartu yang sedang terdeteksi otomatis tersorot emas (`ACTIVE` + `UI_Plaque_PoseCard_Active_9Slice`).
   * **Baris 1 (Ground, Transisi, & Kemudi):**
     - **`#01 · GROUNDED IDLE`** (`GROUND · 0 m/s · STAND STRAIGHT`): Berdiri/duduk tegak lurus dengan lengan rapat di sisi badan (`tucked`) -> naga diam sepenuhnya di tempat di darat.
     - **`#02 · GROUNDED WALK`** (`GROUND · 3.5 m/s · ARMS AWAY`): Rentangkan lengan atas menjauh (`T-pose` darat) -> berjalan maju `3.5 m/s`.
     - **`#03 · GROUNDED LOOK UP`** (`GROUND · 0 m/s · +15°..25° PITCH`): Angkat dada / *torso tilt* ke atas tanpa kepakan -> naga mendongak ke langit tanpa terbang.
     - **`#04 · TAKEOFF`** `[MAIN]` (`TRANSITION · >=0.25s · +7m LIFT`): Di darat, kepakkan kedua lengan kuat (`>=0.25s`) -> melesat lepas landas ke udara.
     - **`#05 · LEAN & ARM TILT`** (`STEERING · GROUND & AIR · SEATED OK`): Condongkan badan (`50%`) atau miringkan tinggi lengan (`50%`) untuk berputar di tempat, berbelok saat jalan, atau *bank-turn* saat terbang.
   * **Baris 2 (Penerbangan Inti):**
     - **`#06 · FLAP TO CLIMB`** `[MAIN]` (`AIR · 12–19.5 m/s · SMOOTH CLIMB`): Kepakkan kedua lengan penuh secara ritmis melewati bahu untuk menanjak stabil.
     - **`#07 · FLY UP`** (`AIR · +19° PITCH · STEEP CLIMB`): Angkat dada / *torso tilt* ke atas sambil mengepak aktif untuk menanjak curam ke langit tinggi.
     - **`#08 · GLIDE (T-POSE)`** `[MAIN / ACTIVE]` (`AIR · 14 m/s · SWOOP READY`): Rentangkan lengan mendatar sejajar bahu (`T-pose` tanpa kepak) untuk meluncur santai; memicu *Swoop Climb* otomatis paska-*dive*.
     - **`#09 · HOVER MODE`** (`AIR · 0 m/s · STATIONARY`): Rapatkan lengan atas ke rusuk & kepakkan lengan bawah secara ritmis -> melayang diam di tempat di udara.
     - **`#10 · GENTLE DESCENT`** (`AIR · 8.5 m/s · -1.6 m/s SINK`): Rapatkan lengan atas ke badan & tahan lengan bawah rileks tanpa kepak -> turun perlahan menuju pendaratan mulus.
   * **Baris 3 (Turun Presisi, Tukik, Boost, & Desa):**
     - **`#11 · FLY DOWN (POSE Y)`** (`AIR · 12 m/s FWD · -5.5 m/s SINK`): Lengan atas menjauh (`Away`) + lengan bawah mengarah ke atas membentuk huruf **`Y`** -> turun menukik pelan sambil tetap maju.
     - **`#12 · FLY DOWN IDLE (POSE W)`** (`AIR · 0 m/s · -3.2 m/s SINK`): Lengan atas rapat ke badan (`Tucked`) + lengan bawah mengarah ke atas membentuk huruf **`W`** -> turun vertikal perlahan di tempat.
     - **`#13 · TUCK TO DIVE`** `[MAIN]` (`AIR · -45° PITCH · UP TO 28 m/s`): Tangan dirapatkan/mengepal di dada atas dengan siku mengarah ke bawah -> menukik curam berkecepatan tinggi.
     - **`#14 · PROPELLER DASH`** (`AIR BOOST · 26 m/s BURST`): Putar kedua lengan melingkar secara terus-menerus -> dorongan kecepatan instan di udara.
     - **`#15 · VILLAGE ACTIONS`** (`AUTO-HOVER · 3 BIOME GESTURES`): Smog: kepak sayap · Fire: buka mulut lebar / tangkup tangan di dada · Drought: angkat kedua tangan tinggi di atas kepala.
4. **Strip Bawah (`KEY POSE RULES`):** Merangkum aturan cepat *Arm Gate*, *Seated Virtual Hips*, dan perilaku aman saat *Tracking Loss*.

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
| [📜 QUEST OBJECTIVE · TRAVEL]                 [ 02:15  ·  VILLAGES: 1/3 ]           [ ESC · PAUSE ]|
| | Fly to the marked village (Smog Valley)                                                         |
+---------------------------------------------------------------------------------------------------+
|                                                                                                   |
| [33% 🍃]                                                                                          |
| +---+                                                                                             |
| |===|                                         ▲                                                   |
| |===|                              [ SMOG VALLEY · 140m ]                                         |
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
0. **Kiri Atas di Atas Vitality Bar (`Quest Objective Panel`, `x: 60, y: 40, 424×112`):**
   * Plakat *Stepped Parchment* (`#F2EFE7` + outline `#332C25` + shadow `[0, 5]`) yang duduk tepat di atas lencana `33%` (`y: 280`, jeda aman `128 px`) dengan soket medali kiri berikon gulungan emas (`UI_Icon_QuestScroll_Gold`), pil amber **`QUEST OBJECTIVE`**, badge status kanan (**`TRAVEL`** / **`ACTIVE TASK`** hijau emerald `#5C8D46`), judul objective utama, dan sub-teks petunjuk singkat:
     * **Saat Game Load & Pemain Spawn:** Status `TRAVEL` -> *"Fly to the marked village (Smog Valley)"* · *"Follow the village marker · Narrative opens on arrival"*.
     * **Setelah Narasi Desa Ditutup:** Otomatis ter-update ke status `ACTIVE TASK` sesuai misi desa aktif (mis. *"Disperse the smog — Flap your wings!"*).
1. **Tengah Atas (Timer & Objektif Desa Mungil):**
   * Kapsul krem solid `#FBF6EB` ramping tanpa pita: ikon jam pasir + digital countdown `02:15`, pemisah titik, dan status `VILLAGES: 1 / 3` dengan tanda centang hijau.
2. **Kanan Atas (Tombol Pause):**
   * Tombol pil `ESC · PAUSE` solid `#FBF6EB` tetap konsisten di sudut kanan atas.
3. **Sisi Kiri Layar (Global Vitality Progress Bar Vertikal):**
   * Bar meteran vertikal ramping mengisi dari bawah ke atas warna hijau emerald mewakili energi pemulihan pulau, dengan lencana persentase `33%` di puncak.
4. **Klaster Kanan Bawah (Telemetri Naga + Header Kontrol Kamera + Monitor PIP 16:9):**
   * **Dock Telemetri (`y: 632`):** Kapsul ramping paling atas menampilkan ketinggian `▲ 42m`, kecepatan angin `12.0m/s`, dan badge cyan `POSE T` (`POSE Y` menanjak / `POSE A` menukik / `IDLE FLY` saat di atas desa).
   * **Header Kontrol Kamera PIP (`PIP Camera Control Header`, `420×112` tepat di atas kotak PIP):** Kartu krem solid `#FBF6EB` tanpa transparansi berisi:
     * Baris status: badge state (`● LIVE · LATAR BELAKANG`, dll.) + tombol merah taktil **`Matikan`** di kanan.
     * Baris pemilih input: dropdown kamera (`Pilih Input Kamera... ▼` / `[0] USB2.0 HD UVC WebCam ▼`, popup solid 100% opaque `#FBF6EB`) + tombol **`Scan`**.
     * Baris aktivasi YOLO: tombol **`Latar Belakang`** dan **`Jendela Preview`** (terkunci sebelum pilih kamera; langsung switch mode saat aktif).
   * **Monitor Kamera PIP (`420×236`, rasio 16:9):** Outline tinta seragam `#332C25` pada seluruh state (state `LIVE`, `STANDBY`, `MEMUAT YOLO`, dan `ERROR` ditunjukkan oleh isi badge status, dot indikator, tombol mode aktif, serta pesan panduan di dalam panel — tanpa outline berwarna), menampilkan live feed webcam + skeleton pose atau pesan panduan state terkait.
5. **Tengah Layar (Subtle Waypoint Compass Pointer):**
   * Panah chevron kartun halus mengarah ke desa bertanda (*marked village*): `▲ SMOG VALLEY · 140m`. (Otomatis tersembunyi saat narasi desa dibuka atau tidak ada target perjalanan aktif).
6. **Kelegaan Layar:** Dengan menempatkan `Quest Objective Panel` sejajar garis atas bersama kapsul Timer & tombol Pause serta memadatkan kontrol kamera di kanan bawah, viewport pandang tengah tetap sangat lega (> `82%` terbuka) tanpa tabrakan dengan Vitality Bar kiri (`y: 280..810`).

### 3.1.1 Quest Narrative Text Box / `SCREEN 08` (Saat Fanamir Masuk Area Desa)
*Artboard FigCraft:* `SCREEN 08 - Quest Narrative & Objective - Design (1920x1080)` (`id: 2114`)

Saat **Fanamir** memasuki area trigger desa (*village arrival*), game beralih memainkan animasi **`IDLE FLY`** (terbang di tempat) di atas desa dan memunculkan **Quest Narrative Text Box** pada area **bawah-tengah / kiri-bawah (`x: 156, y: 828, 1236×212`)** tanpa bertabrakan dengan **Vitality Track kiri** (`x: 80..120`, berakhir di `y: 810`) maupun klaster **PIP Kamera & Telemetri kanan bawah** (`x: 1440..1860`):
1. **Banner Pembicara / Desa (`y: -18` overlap atas kiri, `344×38`):** Banner emas `SMOG VALLEY · ELDER ROWAN` (`UI_Banner_SpeakerTag_Blank`) + pil pojok kanan atas `VILLAGE BRIEFING · 1 / 3`.
2. **Emblem Biome Kiri (`132×150`):** Plakat gelap berisi ilustrasi ringkas ikon kondisi desa + nama desa (`SMOG VALLEY`).
3. **Narasi Masalah Desa (`2–3` baris):** Sub-tag `VILLAGE CRISIS · INDUSTRIAL HAZE` dan narasi jelas tentang krisis yang terjadi di desa untuk menyelamatkan warga/alam oleh Fanamir.
4. **Strip Instruksi Aksi (`WHAT TO DO:`):** Strip resessif taupe `#E7DFD0` + soket ikon gelap berisi petunjuk eksplisit apa yang harus dilakukan pemain (mis. *"Flap both wings steadily in front of your webcam to clear the smog!"*).
5. **Indikator Update & Tombol Lanjut/Tutup:** Chip `UPDATES QUEST OBJECTIVE` di atas tombol taktil hijau **`CONTINUE ▶ [ SPACE ] OR [ CLICK ]`**. Menekan tombol/Space akan menutup Narrative Text Box, memperbarui `Quest Objective Panel` menjadi status **`ACTIVE TASK`**, lalu memulai tantangan pemulihan desa (`SCREEN 03` / progress bar desa).

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
   * Menggunakan **TextMeshPro (TMP)** dengan font bergaya *clean geometric rounded sans-serif* (seperti *Nunito*, *Quicksand*, atau *Fredoka One*) dengan warna tinta `#2D2926` (**wajib** dipasang sebagai runtime TMP pada seluruh aset blank di `03 - Unity Export Kit`).

---

## 🧩 5. KATALOG PRA-EKSPOR UNITY (`03 - Unity Export Kit`, Page `1260`)

Halaman **`03 - Unity Export Kit`** (`id: 1260`, kanvas `4000 px` lebar, latar netral `#DFD9CB`) memisahkan seluruh komponen UI `Design` menjadi **139 layer ekspor modular (`PNG 1x`, *textless*, *transparent wrapper* `clip: false`)** bertag `/`-prefixed subfolder agar ekspor `file.exportLayers` dari FigCraft langsung membentuk struktur folder siap impor Unity 6 (`uGUI` + `CanvasScaler 1920×1080`):

> **Catatan Bounding Box Ekspor (`clip: false`):** Layer wrapper ekspor diset `fill: "none"` dan `clip: false` agar *hard cast shadow* dan overhang sudut *stepped* tidak terpotong (contoh: `UI_Panel_Parchment_Full_9Slice` wrapper `240×240` mengekspor `264×271 px` utuh dengan bayangan; `UI_Panel_QuestObjective_9Slice` `240×135 px`; `UI_Panel_NarrativeBox_9Slice` `304×200 px`; `UI_Illust_Dragon_WaterBreath` `269×170 px`; `UI_Bg_Lobby_AerialIsland` tepat `1920×1080 px`). Seluruh **`139` PNG komponen** & **`9` PNG desain `1920×1080`** (`00..08`) sudah diekspor ke [`UI_Unity_Export/`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/) — lihat **[[panduan_aset_ui_unity]]** untuk ukuran piksel final, nilai border Unity Sprite Editor (`L, T, R, B`), peringatan notch, dan panduan perakitan Scene Unity 6. Set `Image.Type = Sliced` pada seluruh aset `*_9Slice` sesuai nilai terukur, dan `Image.Type = Filled` pada `UI_HUD_Vitality_Fill_34x474`, `UI_Progress_Horiz_Fill_Blue_9Slice`, serta `UI_Slider_Fill_*`.

| Section | Folder Ekspor | Jml PNG | Cakupan Aset Utama |
| :--- | :--- | :---: | :--- |
| **01 · 9-Slice Panels & Backgrounds** (`1263`) | `01_Panels/` | **13** | Panel *Stepped Parchment* (`Full 264×271`, `Calib 224×230`), *SubParchment*, `PoseCard_Default/Active`, `StatRow`, `Recessed_Track`, `NotesBox`, `Locked_Charcoal`, `Overlay_Popup_Opaque`, `Scrim_LockedCard` (`64×64`), plus **`UI_Panel_QuestObjective_9Slice` (`240×135`)** & **`UI_Panel_NarrativeBox_9Slice` (`304×200`)**. |
| **02 · Banners, Ornaments, Ribbons & Medallions** (`1264`) | `02_Ornaments/`, `02_Banners/`, `02_Tags/`, `02_Callouts/`, `02_Chips/` | **21** | *Baroque Scrollwork*, *Corner Fan*, *Diamond Fleuron*, *Socket 38/32*, `Timer_Coin`, `Pill_Gold`, 3 *Stepped Banner* blank + `Stamp_Defeat_Red_Blank` + **`UI_Banner_SpeakerTag_Blank` (`240×42`)**, 3 `Tag_Stage` blank, 2 `Callout_SpeechBubble` (dengan & tanpa koin), `Chip_Bar`, `Header_Bar`. |
| **03 · Interactive Buttons, Chrome & Navigation Tabs** (`1265`) | `03_Buttons/`, `03_Chrome/`, `03_Navbar/` | **17** | Tombol 3D tactile bevel (`Primary_Green`, `Secondary_Cream`, `Locked_Charcoal`, `Small_*`, `Danger_Coral Active/Disabled`), 4 tombol mode kamera (`ActiveGreen`, `LoadingCyan`, `UtilityCream`, `Disabled`), `TopChrome_Cream`, `CloseX_20`, `Pause_20`, `Navbar_Rail`, `Tab_Default`, `Tab_Active_Gold`. |
| **04 · Sliders, Dropdowns, Camera States & Viewports** (`1266`) | `04_Controls/`, `04_Dropdown/`, `04_CameraStates/`, `04_Viewports/` | **36** | Slider track/fill (`Green`, `Amber`), knob, `ValueBox`, Dropdown trigger (`Closed`/`Open`), Popup opaque `#FBF6EB` + row (`Even`/`Odd`/`Selected`), Chevron/Checkmark, 4 `Badge_Cam_*`, 3 `NavCam_*`, 4 dot status, ikon kamera/spinner/alert, **viewport berlubang alpha=0 (`Calib_Frame_Hollow`, `PIP_Bezel_Hollow`)**, 2 bracket viewfinder, dan panel kalibrasi gelap. |
| **05 · HUD, Challenge & Rewards** (`1267`) | `05_HUD/`, `05_Challenge/`, `05_Rewards/` | **17** | `Capsule_Parchment`, `Vitality_Track_40x480` + `Fill_34x474` + `Ticks_Overlay` terpisah, `Waypoint_Chevron`, `CheckDot_Green`, ilustrasi terisolasi `UI_Illust_Dragon_WaterBreath` + `Frame_Dark`, progress horizontal biru, `Badge_Active_Green_Blank`, slot bintang `StarBox_Card/Victory`, `Star_Gold/Empty`, `Box_Locked_Center`, dan `Icon_Padlock_Cream`. |
| **06 · Vector Icon Library & 15 Calibration Poses** (`1268`) | `06_Icons/`, `06_Poses/` | **27** | **11** ikon UI (`Hourglass`, `Leaf`, `ScoreStar`, `CoinMedallion`, `Speaker_Audio`, `MusicNote`, `Bolt`, `Sensitivity`, `Padlock_Vector`, **`QuestScroll_Gold`**, **`PromptNext_Arrow`**), `Medallion_Slot_58` kosong, serta **15 ikon pose kalibrasi** (`01_GroundedIdle` s.d. `15_VillageActions`, termasuk varian `08_Glide_ActiveGold`). |
| **07 · Biome Illustrations & Lobby Background** (`1269`) | `07_Biomes/`, `07_Backgrounds/` | **8** | 3 ilustrasi biome versi **`_Raw`** (`380×210`, *borderless* tanpa radius/scrim), 3 versi **`_Framed`** (`392×222`, outline + shadow; Biome 03 Drought bersih tanpa `Locked Scrim`), `UI_Biome_Frame_Border_9Slice` berlubang (`392×222`), dan `UI_Bg_Lobby_AerialIsland` resolusi penuh `1920×1080` (`1:1`). |
| **08 · DO NOT EXPORT — Runtime Placeholders Reference** (`1270`) | *(Tidak Diekspor — 0 `exportSettings`)* | **0** | Referensi visual elemen yang diganti sistem runtime Unity: `Skeleton Tracking Viewport` (UDP `5053`), `Dragon 3D Viewport` (`RenderTexture`), `Webcam PIP Monitor` (MJPEG `RawImage`), panduan lapisan **`TextMeshProUGUI`**, dan status overlay gembok `CanvasGroup`. |

---
**Navigasi Obsidian:** [[00 - Overview]] · [[panduan_aset_ui_unity]] · [[konteks]] · [[prd]] · [[Tasks]] · [[Devlog]]
