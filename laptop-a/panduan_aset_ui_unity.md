---
title: Panduan & Referensi Aset UI Unity 6
tags:
  - ui-ux
  - unity-ui
  - assets
  - export-guide
  - laptop-a
status: ready-for-implementation
owner: Laptop A
updated: 2026-11-14
related:
  - "[[00 - Overview]]"
  - "[[rencana_desain_ui]]"
  - "[[konteks]]"
  - "[[prd]]"
---

# 📦 PANDUAN PENGGUNAAN ASET UI UNITY (`UI_Unity_Export/`)
## AR DRAGON: SKY GUARDIAN

> **Sumber Desain:** [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) — Page **`03 - Unity Export Kit`** (`134` PNG komponen modular) & Page **`Design`** (`7` PNG artboard layar penuh `1920×1080`).
> **Target Engine:** **Unity 6 (`6000.3.7f1`, URP)** dengan `uGUI` + `TextMeshProUGUI` (*namespace* `DragonWingAR.*`).
> **Prinsip Utama:** Seluruh komponen diekspor **`PNG 1x` tanpa teks statis (*textless*)**, berlatar solid opaque (**`alpha = 1.0f`, zero transparency** pada seluruh perkamen `#F2EFE7` / `#FBF6EB`), serta viewport kamera/naga diekspor **berlubang transparan (`centerA = 0`)** agar `RawImage` & `RenderTexture` dapat tampil langsung di belakang bingkai.

---

## 1. STRUKTUR FOLDER EKSPOR

Seluruh file ekspor berada di folder [`UI_Unity_Export/`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/):

```text
UI_Unity_Export/
├── Designs/                         # 7 PNG layar penuh 1920×1080 (referensi visual perakitan di Unity)
│   ├── 00_MainMenu_LevelSelection.png
│   ├── 01_Calibration_MotionTutorial.png
│   ├── 02_InGame_FlightHUD.png
│   ├── 03_VillageChallenge_Overlay.png
│   ├── 04_Finish_VictoryModal.png
│   ├── 05_Settings_AudioModal.png
│   └── 06_CameraControl_5VisualStates.png
└── Components/                      # 134 PNG komponen siap impor ke Unity Assets/UI/
    ├── 01_Panels/                   (11 file)
    ├── 02_Banners/                  (5 file)
    ├── 02_Callouts/                 (2 file)
    ├── 02_Chips/                    (1 file)
    ├── 02_Ornaments/                (9 file)
    ├── 02_Tags/                     (3 file)
    ├── 03_Buttons/                  (11 file)
    ├── 03_Chrome/                   (3 file)
    ├── 03_Navbar/                   (3 file)
    ├── 04_CameraStates/             (15 file)
    ├── 04_Controls/                 (5 file)
    ├── 04_Dropdown/                 (9 file)
    ├── 04_Viewports/                (7 file)
    ├── 05_Challenge/                (5 file)
    ├── 05_HUD/                      (6 file)
    ├── 05_Rewards/                  (6 file)
    ├── 06_Icons/                    (10 file)
    ├── 06_Poses/                    (15 file)
    ├── 07_Backgrounds/              (1 file)
    └── 07_Biomes/                   (7 file)
```

> [!TIP]
> **Jangan masukkan folder `Designs/` ke dalam Build Unity!** Gunakan [`UI_Unity_Export/Designs/`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/Designs/) hanya sebagai referensi komposisi saat menata Scene UI di editor (atau taruh di folder non-Build / luar `Assets/`). Hanya folder [`UI_Unity_Export/Components/`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/Components/) yang perlu disalin ke `Assets/UI/` di project Unity.

### Ringkasan File Referensi Layar (`Designs/`)
| Nama File | Resolusi | Ukuran (Byte) | Kegunaan Referensi di Unity |
| :--- | :---: | :---: | :--- |
| [`00_MainMenu_LevelSelection.png`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/Designs/00_MainMenu_LevelSelection.png) | `1920×1080` | `1,282,793` | Referensi Scene/Panel **Main Menu / Missions** (`SCREEN 00`). |
| [`01_Calibration_MotionTutorial.png`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/Designs/01_Calibration_MotionTutorial.png) | `1920×1080` | `798,764` | Referensi Tab **Calibration & 15 Pose Cards** (`SCREEN 01`). |
| [`02_InGame_FlightHUD.png`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/Designs/02_InGame_FlightHUD.png) | `1920×1080` | `81,533` | Referensi **In-Game Flight HUD** (`SCREEN 02`; overlay transparan di atas kamera 3D). |
| [`03_VillageChallenge_Overlay.png`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/Designs/03_VillageChallenge_Overlay.png) | `1920×1080` | `93,890` | Referensi **Village Action Overlay** (`SCREEN 03`; overlay saat auto-hover desa). |
| [`04_Finish_VictoryModal.png`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/Designs/04_Finish_VictoryModal.png) | `1920×1080` | `102,783` | Referensi **Finish Modal (Victory/Defeat)** (`SCREEN 04`). |
| [`05_Settings_AudioModal.png`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/Designs/05_Settings_AudioModal.png) | `1920×1080` | `1,024,558` | Referensi Tab **Settings & Camera Audio Module** (`SCREEN 05`). |
| [`06_CameraControl_5VisualStates.png`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/Designs/06_CameraControl_5VisualStates.png) | `1920×1080` | `637,345` | Lembar spesifikasi **5 State Visual Kamera**, **Popup Dropdown Opaque `#FBF6EB`**, & **3 Badge Navbar `SCREEN 00`**. |

---

## 2. KONFIGURASI WAJIB UNITY 6 (`uGUI` & SPRITE IMPORT)

### 2.1 Pengaturan `Canvas` & `CanvasScaler`
Seluruh layar UI didesain pada kanvas **`1920 × 1080` (`16:9`)** dengan unit ekspor **`1x` (`1 px desain = 1 unit UI`)**:
* **Render Mode:** `Screen Space - Overlay` (atau `Screen Space - Camera` jika membutuhkan efek pasca-proses tertentu).
* **UI Scale Mode:** `Scale With Screen Size`.
* **Reference Resolution:** `X = 1920`, `Y = 1080`.
* **Screen Match Mode:** `Match Width or Height` -> `Match = 0.5`.

### 2.2 Pengaturan *Texture Importer* Semua PNG Komponen
Agar garis tinta tipis `#332C25` (`1.5–3 px`) dan sudut undakan *stepped* tidak pecah/berartefak kompresi blok, terapkan preset berikut pada seluruh **`134` PNG**:
* **Texture Type:** `Sprite (2D and UI)`
* **Sprite Mode:** `Single`
* **Pixels Per Unit:** `100`
* **Mesh Type:** `Full Rect` *(wajib untuk aset `9-Slice`, sangat disarankan untuk semua kontrol/panel)*
* **sRGB (Color Texture):** `Checked`
* **Alpha Is Transparency:** `Checked`
* **Wrap Mode:** `Clamp`
* **Filter Mode:** `Bilinear`
* **Compression:** **`None`** *(atau `High Quality`, hindari kompresi agresif agar outline gelap & gradien lembut bebas banding)*

### 2.3 Konvensi `9-Slice` Inspector (`L, T, R, B`) vs C# (`Sprite.border`)
> [!IMPORTANT]
> **Awas Perbedaan Urutan Sumbu Border di Unity!**
> * **Di Unity Sprite Editor Inspector** (nilai pada tabel katalog dokumen ini): urutan kolom dari atas ke bawah adalah **`L, T, R, B`** (*Left, Top, Right, Bottom* — searah jarum jam).
> * **Di Kode C# (`Sprite.border` `Vector4`)**: urutannya adalah **`(Left, Bottom, Right, Top)`** (`x = L, y = B, z = R, w = T`), berbeda dengan tampilan Inspector!
> * Saat `Image.Type = Sliced` aktif di Unity, centang **`Fill Center`** pada hampir semua panel dan tombol.

### 2.4 Kompensasi Padding Transparan & *Hard Cast Shadow* (`10–14 px`)
Sebagian komponen vektor (seperti `UI_Panel_Parchment_Full_9Slice`, `UI_Plaque_PoseCard_*`, `UI_Banner_Stepped_*`, dan `UI_Callout_SpeechBubble_*`) diekspor dengan **`clip: false`** agar bingkai bertingkat dan *hard cast shadow* tidak terpotong di tepi PNG:
1. Nilai **`L, T, R, B`** pada katalog di Bagian 3 **sudah dihitung langsung dari piksel PNG hasil ekspor** (sudah mencakup margin transparan `m=(L,T,R,B)` + overhang bayangan bawah sehingga tepi/bayangan tidak ikut tertarik saat *resize*).
2. **Dampak ke `RectTransform`:** Jika di artboard `Design` ukuran bodi panel adalah misalnya `460×680`, tetapi PNG-nya punya margin transparan `12 px` di tiap sisi + overhang shadow `~7 px` di bawah, tambahkan `24 px` ke lebar dan `~31 px` ke tinggi `RectTransform` (atau kompensasikan anchor-nya) agar *bodi visual kartu* tepat sebesar `460×680`.

### 2.5 Aturan *Linear Color Space* (`alpha = 1.0f` Solid Opaque)
Proyek Unity 6 URP berjalan di **Linear Color Space**. Jangan menurunkan `Color.a` pada background panel, kartu, atau **Popup Dropdown Kamera**:
* Biarkan **`Image.color = #FFFFFF` dengan `A = 255` (`1.0f`)** karena warna krem/perkamen (`#F2EFE7` / `#FBF6EB`) sudah tertanam langsung di dalam PNG.
* Khusus **`UI_Scrim_LockedCard_Overlay.png`** (`64×64`), nilai alpha semi-transparan (`centerA = 148` ≈ `58%`) memang sudah bawaan PNG untuk meredupkan ilustrasi kartu Stage 03 yang terkunci.

---

## 3. KATALOG LENGKAP `134` KOMPONEN & NILAI `Sprite Editor`

> **Cara Membaca Kolom:**
> * **Ukuran PNG:** Dimensi aktual `W×H` piksel pada disk.
> * **Margin `m=(L,T,R,B)`:** Padding transparan bawaan PNG di luar artwork/shadow.
> * **`Image.Type`:** Mode yang dipilih pada komponen `UnityEngine.UI.Image`.
> * **`L, T, R, B` (Unity Sprite Editor):** Nilai Border yang diketikkan di Unity **Sprite Editor** (hanya diisi untuk tipe `Sliced` atau `Horizontal 3-Slice`). Untuk `Simple` dan `Filled`, biarkan Border **`0, 0, 0, 0`**.

---

### 3.1 `01_Panels/` (`11` File)
| Nama File | Ukuran PNG | Margin `m=(L,T,R,B)` | `Image.Type` | Border Unity `L, T, R, B` | Fungsi & Catatan Teknis |
| :--- | :---: | :---: | :---: | :---: | :--- |
| `UI_Panel_Parchment_Full_9Slice.png` | `264×271` | `(12,12,12,12)` | `Sliced` | **`52, 52, 52, 59`** | Kartu utama `SCREEN 00` (`460×680`) & modal `SCREEN 03/04/05`. Pasangi 4x `UI_Corner_Fan_Medallion_24` di tiap sudut dalam. |
| `UI_Panel_Parchment_Calib_9Slice.png` | `224×230` | `(12,12,12,12)` | `Sliced` | **`33, 33, 33, 39`** | Dua panel utama `SCREEN 01` (*Camera Mirror* kiri & *Live Dragon Preview* kanan). |
| `UI_Plaque_SubParchment_9Slice.png` | `180×184` | `(10,10,10,10)` | `Sliced` | **`34, 34, 34, 38`** | Sub-kontainer dalam modal (`Victory Stat Box` `SCREEN 04`, `Cam Module` `SCREEN 05`, panel state `SCREEN 06`). |
| `UI_Plaque_PoseCard_Default_9Slice.png` | `200×133` | `(10,10,10,10)` | `Sliced` | **`24, 24, 24, 27`** | Kartu panduan pose normal pada grid `3×5` `SCREEN 01` (`14` kartu non-aktif). |
| `UI_Plaque_PoseCard_Active_9Slice.png` | `208×143` | `(14,14,14,14)` | `Sliced` | **`27, 27, 27, 32`** | Kartu pose aktif yang disorot (contoh: `GLIDE (T-POSE)` dengan highlight emas/ink). |
| `UI_Plaque_StatRow_9Slice.png` | `240×90` | `(10,10,10,10)` | `Sliced` | **`24, 23, 24, 27`** | Baris slider `SCREEN 05` & baris statistik hasil `SCREEN 04` (pasangi `UI_Socket_Medallion_38_Dark` di sisi kiri). |
| `UI_Row_Recessed_Track_9Slice.png` | `180×44` | `(0,0,0,0)` | `Sliced` | **`5, 6, 5, 5`** | Sumur info *borderless recessed taupe* (strip `LIMIT ...` `SCREEN 00`, baris info `SCREEN 01/06`). |
| `UI_Row_NotesBox_Beige_9Slice.png` | `180×56` | `(0,0,0,0)` | `Sliced` | **`5, 5, 5, 5`** | Kotak catatan panduan teks multi-baris (bawah kartu state `SCREEN 06`, aturan `SCREEN 01`). |
| `UI_Overlay_Popup_Opaque_9Slice.png` | `180×126` | `(0,0,0,0)` | `Sliced` | **`5, 5, 5, 11`** | Kartu dasar solid `#FBF6EB` untuk header `PIP Camera Control` (`420×112` di `SCREEN 02`) & popup overlay solid. |
| `UI_Box_Locked_Charcoal_9Slice.png` | `200×94` | `(0,0,0,0)` | `Sliced` | **`20, 46, 8, 15`** | **⚠️ Peringatan Notch:** Memiliki takik ganda `=` di sisi kiri (`y = 36..44`) sehingga zona `Top=46` besar. Jaga tinggi `≥ 90 px` agar pita stretch bawah tetap aman; atau gunakan `UI_Box_Locked_Center_96x80` untuk kotak gembok tengah Stage 03. |
| `UI_Scrim_LockedCard_Overlay.png` | `64×64` | `(0,0,0,0)` | `Simple` *(Stretch)* | `0, 0, 0, 0` | Overlay gelap semi-transparan (`alpha ≈ 58%`) di atas `UI_Biome_03_Drought` pada kartu Level 03 saat masih terkunci. Nonaktifkan `GameObject` saat stage terbuka. |

---

### 3.2 `02_Banners/`, `02_Callouts/`, `02_Chips/`, `02_Ornaments/`, `02_Tags/` (`20` File)
> [!NOTE]
> **Aturan Horizontal 3-Slice (`UI_Banner_Stepped_*`, `UI_Stamp_Defeat_Red_Blank`, `UI_Callout_SpeechBubble_*`):**
> Komponen pita/spanduk & gelembung bicara memiliki telinga sudut *stepped* atau ekor gelembung yang tidak boleh melar secara vertikal. Pada Unity Sprite Editor, isi **`L` dan `R` sesuai tabel lalu biarkan `T = 0, B = 0`**, dan kunci **tinggi (`Height`) `RectTransform` sama persis dengan tinggi PNG (`72`, `77`, `57`, atau `61 px`)**.

| Folder | Nama File | Ukuran PNG | Margin `m` | `Image.Type` | Border Unity `L, T, R, B` | Fungsi & Catatan Teknis |
| :--- | :--- | :---: | :---: | :---: | :---: | :--- |
| `02_Banners/` | `UI_Banner_Stepped_Card_Blank.png` | `416×72` | `(10,10,10,10)` | `Sliced` *(3-Slice H)* | **`52, 0, 52, 0`** | Pita judul Level (`SMOG VALLEY`, `WILDFIRE`, `DROUGHT`) di `SCREEN 00`. |
| `02_Banners/` | `UI_Banner_Stepped_Modal_Blank.png` | `560×77` | `(10,10,10,10)` | `Sliced` *(3-Slice H)* | **`58, 0, 58, 0`** | Banner judul besar di atas kartu modal (`SCREEN 03`, `04 Victory`, `05`). |
| `02_Banners/` | `UI_Banner_Stepped_SubPanel_Blank.png` | `396×57` | `(8,8,8,8)` | `Sliced` *(3-Slice H)* | **`42, 0, 42, 0`** | Banner judul di tepi atas panel `SCREEN 01` (*Camera Mirror* & *Live Dragon Preview*). |
| `02_Banners/` | `UI_Stamp_Defeat_Red_Blank.png` | `380×77` | `(10,10,10,10)` | `Sliced` *(3-Slice H)* | **`52, 0, 52, 0`** | Stempel banner merah untuk modal `Defeat / Waktu Habis` (`SCREEN 04`). |
| `02_Banners/` | `UI_Header_Bar_Parchment_9Slice.png` | `240×68` | `(10,10,10,10)` | `Sliced` | **`22, 21, 22, 25`** | Strip header bar perkamen memanjang (judul atas `SCREEN 06` & strip telemetri `STATE: GLIDE...` `SCREEN 01`). |
| `02_Callouts/` | `UI_Callout_SpeechBubble_Gold_Blank.png` | `133×61` | `(7,7,6,7)` | `Sliced` *(3-Slice H)* | **`17, 0, 49, 0`** | Tag speech-bubble emas di atas tombol sekunder / tombol `SCREEN 04`. Zona `R = 49` menjaga ekor lancip kanan bawah tidak ikut tertarik saat teks diperpanjang. |
| `02_Callouts/` | `UI_Callout_SpeechBubble_Gold_WithCoin.png` | `133×61` | `(2,6,6,7)` | `Sliced` *(3-Slice H)* | **`29, 0, 49, 0`** | Sama seperti di atas dengan koin emas di kiri (`90s`, `150s` di `SCREEN 00`, `+3 STARS` di `SCREEN 04`). |
| `02_Chips/` | `UI_Chip_Bar_Cream_9Slice.png` | `260×56` | `(0,0,0,0)` | `Sliced` | **`4, 4, 4, 8`** | Bar krem aturan bawah `SCREEN 01` (`ARM GATE RULE` & `SEATED & SAFETY`), serta latar dropdown container modul. |
| `02_Ornaments/` | `UI_Badge_Label_Amber_Blank.png` | `140×40` | `(2,1,2,1)` | `Sliced` | **`5, 4, 5, 6`** | Tag kapsul amber kiri di dalam `UI_Chip_Bar_Cream_9Slice` (`ARM GATE RULE`). |
| `02_Ornaments/` | `UI_Badge_Label_Teal_Blank.png` | `154×40` | `(2,1,2,1)` | `Sliced` | **`5, 4, 5, 6`** | Tag kapsul teal (`STATUS: GLIDE` & `SEATED & SAFETY` di `SCREEN 01`). |
| `02_Ornaments/` | `UI_Pill_Active_Gold.png` | `70×26` | `(3,2,3,2)` | `Sliced` | **`6, 5, 6, 7`** | Lencana mini `ACTIVE` di pojok kanan atas kartu pose `UI_Plaque_PoseCard_Active_9Slice`. |
| `02_Ornaments/` | `UI_Corner_Fan_Medallion_24.png` | `40×40` | `(7,7,7,7)` | `Simple` | `0, 0, 0, 0` | Ornamen kipas sudut perkamen. Duplikasi 4× ke tiap pojok dalam panel `Parchment_Full` (putar `RectTransform` Z `0° / -90° / 180° / 90°` per sudut). |
| `02_Ornaments/` | `UI_Crest_Baroque_Scrollwork.png` | `300×54` | `(8,8,9,9)` | `Simple` *(Preserve Aspect)* | `0, 0, 0, 0` | Mahkota ukiran barok simetris (`centerA = 0`), dipasang tepat di atas `UI_Banner_Stepped_Modal_Blank` (`SCREEN 03, 04, 05`). |
| `02_Ornaments/` | `UI_Diamond_Fleuron_18.png` | `26×26` | `(3,3,3,3)` | `Simple` | `0, 0, 0, 0` | Hiasan berlian kiri/kanan pada banner `SCREEN 00–06` (opsional jika banner ingin teks dinamis dengan posisi ikon fleuron ikut bergerak). |
| `02_Ornaments/` | `UI_Badge_Timer_Coin_26.png` | `32×32` | `(3,3,3,3)` | `Simple` | `0, 0, 0, 0` | Koin timer kecil (`26 px` visual) untuk aksen header waktu/skor. |
| `02_Ornaments/` | `UI_Socket_Medallion_32_GreenCore.png` | `38×38` | `(3,2,3,2)` | `Simple` | `0, 0, 0, 0` | Soket medali kiri untuk baris `UI_Row_Recessed_Track` (`SCREEN 00` / `01`). |
| `02_Ornaments/` | `UI_Socket_Medallion_38_Dark.png` | `44×44` | `(3,1,3,2)` | `Simple` | `0, 0, 0, 0` | Soket medali bulat gelap (`#38312A`) tempat menumpang ikon di kiri `UI_Plaque_StatRow` (`SCREEN 04 & 05`). |
| `02_Tags/` | `UI_Tag_Stage_Green_Blank.png` | `132×48` | `(0,0,0,0)` | `Sliced` | **`4, 7, 4, 8`** | Pita tag atas kartu **`STAGE 01`** (aktif). Set teks TMP putih tebal. |
| `02_Tags/` | `UI_Tag_Stage_Gold_Blank.png` | `132×48` | `(0,0,0,0)` | `Sliced` | **`4, 7, 4, 8`** | Pita tag atas kartu **`STAGE 02`** (terbuka). Set teks TMP gelap `#332C25`. |
| `02_Tags/` | `UI_Tag_Stage_Dark_Blank.png` | `132×48` | `(0,0,0,0)` | `Sliced` | **`4, 7, 4, 8`** | Pita tag atas kartu **`STAGE 03`** (terkunci). Set teks TMP putih. |

---

### 3.3 `03_Buttons/`, `03_Chrome/`, `03_Navbar/` (`17` File)
> [!WARNING]
> **Batasan Tinggi Tombol Bertakik Kiri (`=`):**
> Tiga tombol utama `220×68` (`UI_Btn_Primary_Green`, `Secondary_Cream`, `Locked_Charcoal`) dan `UI_Btn_Small_Green` (`166×43`) memiliki takik garis ganda `=` di sisi kiri. Nilai `Border` di bawah mengisolasi takik tersebut di pojok aman, sehingga **lebar boleh ditarik bebas**, tetapi **pertahankan tinggi (`Height`) tetap di kisaran ukuran asli PNG (`64–68 px` untuk utama, `40–44 px` untuk small green)**.

| Folder | Nama File | Ukuran PNG | Margin `m` | `Image.Type` | Border Unity `L, T, R, B` | Fungsi & Catatan Teknis |
| :--- | :--- | :---: | :---: | :---: | :---: | :--- |
| `03_Buttons/` | `UI_Btn_Primary_Green_9Slice.png` | `220×68` | `(0,0,0,0)` | `Sliced` | **`27, 8, 4, 44`** | CTA hijau utama (`PLAY`, `READY TO FLY`, `APPLY & CLOSE`, `LEVEL BERIKUTNYA`). Tinggi tetap `~64–68 px`. |
| `03_Buttons/` | `UI_Btn_Secondary_Cream_9Slice.png` | `220×68` | `(0,0,0,0)` | `Sliced` | **`27, 8, 4, 44`** | CTA sekunder krem (`SELECT`, `KEMBALI KE LOBBY`, `COBA LAGI`). Tinggi tetap `~64–68 px`. |
| `03_Buttons/` | `UI_Btn_Locked_Charcoal_9Slice.png` | `220×68` | `(0,0,0,0)` | `Sliced` | **`27, 4, 4, 44`** | Tombol terkunci charcoal pada Stage 03 (`UNLOCK: 1 STAR IN WILDFIRE`). Tinggi tetap `~64–68 px`. |
| `03_Buttons/` | `UI_Btn_Small_Green_9Slice.png` | `166×43` | `(0,0,0,0)` | `Sliced` | **`15, 23, 3, 11`** | Tombol aksi kecil hijau (contoh: `RECALIBRATE (C)` di `SCREEN 01`). Tinggi tetap `~40–44 px`, teks putih kontras tinggi. |
| `03_Buttons/` | `UI_Btn_Small_Cream_9Slice.png` | `144×43` | `(0,0,0,0)` | `Sliced` | **`3, 6, 3, 11`** | Tombol kecil sekunder (`Scan`, `RESET DEFAULT`). Bebas ukuran wajar. |
| `03_Buttons/` | `UI_Btn_CamMode_ActiveGreen_9Slice.png` | `154×33` | `(0,0,0,0)` | `Sliced` | **`3, 3, 3, 6`** | Tombol mode kamera (`Latar Belakang` / `Jendela Preview`) saat sedang berjalan **LIVE**. |
| `03_Buttons/` | `UI_Btn_CamMode_LoadingCyan_9Slice.png` | `154×33` | `(0,0,0,0)` | `Sliced` | **`3, 3, 3, 6`** | Tombol mode kamera terpilih saat fase **MEMUAT YOLO**. |
| `03_Buttons/` | `UI_Btn_CamMode_UtilityCream_9Slice.png` | `154×33` | `(0,0,0,0)` | `Sliced` | **`3, 3, 3, 9`** | Tombol mode kamera saat siap diklik (`STANDBY Kamera Terpilih` atau mode alternatif saat `LIVE`). |
| `03_Buttons/` | `UI_Btn_CamMode_Disabled_9Slice.png` | `200×48` | `(0,0,0,0)` | `Sliced` | **`3, 3, 3, 3`** | Tombol mode kamera redup/terkunci sebelum kamera dipilih di dropdown. Set `Button.interactable = false`. |
| `03_Buttons/` | `UI_Btn_Danger_Coral_Active_9Slice.png` | `138×33` | `(0,0,0,0)` | `Sliced` | **`3, 3, 3, 6`** | Tombol **`Matikan`** aktif (merah coral) saat status `MEMUAT YOLO` atau `LIVE`. |
| `03_Buttons/` | `UI_Btn_Danger_Coral_Disabled_9Slice.png` | `138×30` | `(0,0,0,0)` | `Sliced` | **`3, 3, 3, 3`** | Tombol **`Matikan`** nonaktif (outline `#9D9384`) saat `STANDBY`. |
| `03_Chrome/` | `UI_Btn_TopChrome_Cream_9Slice.png` | `140×50` | `(0,0,0,0)` | `Sliced` | **`7, 7, 7, 11`** | Tombol pojok kanan atas di semua layar (`ESC · QUIT`, `ESC · BACK`, `ESC · PAUSE`). |
| `03_Chrome/` | `UI_IconBadge_CloseX_20.png` | `26×26` | `(3,3,4,4)` | `Simple` | `0, 0, 0, 0` | Ikon kotak coral **`X`** di kiri teks `ESC · QUIT` / `ESC · BACK`. |
| `03_Chrome/` | `UI_IconBadge_Pause_20.png` | `24×28` | `(3,4,3,5)` | `Simple` | `0, 0, 0, 0` | Ikon jeda **`||`** di kiri teks `ESC · PAUSE` pada `SCREEN 02`. |
| `03_Navbar/` | `UI_Navbar_Rail_9Slice.png` | `260×58` | `(0,0,0,0)` | `Sliced` | **`4, 4, 4, 8`** | Rel dasar `#CFC6B4` wadah 3 tab navbar terpusat (`CALIBRATION`, `MISSIONS`, `SETTINGS`). |
| `03_Navbar/` | `UI_Tab_Active_Gold_9Slice.png` | `160×45` | `(0,0,0,0)` | `Sliced` | **`4, 4, 4, 12`** | Latar tab navbar aktif (emas `#E5AF46` berbibir `#B37C1E`). |
| `03_Navbar/` | `UI_Tab_Default_9Slice.png` | `160×45` | `(0,0,0,0)` | `Sliced` | **`3, 3, 3, 6`** | Latar tab navbar nonaktif (krem datar). |

---

### 3.4 `04_CameraStates/`, `04_Controls/`, `04_Dropdown/`, `04_Viewports/` (`36` File)

#### `04_CameraStates/` (`15` File)
| Nama File | Ukuran PNG | Margin `m` | `Image.Type` | Border Unity `L, T, R, B` | Fungsi & Catatan Teknis |
| :--- | :---: | :---: | :---: | :---: | :--- |
| `UI_Badge_Cam_Standby_Amber_9Slice.png` | `160×36` | `(0,0,0,0)` | `Sliced` | **`3, 3, 3, 3`** | Pill status header modul kamera saat State 1 & 2 (`KAMERA STANDBY`). |
| `UI_Badge_Cam_Loading_Cyan_9Slice.png` | `160×36` | `(0,0,0,0)` | `Sliced` | **`3, 3, 3, 3`** | Pill status saat State 3 (`MEMUAT YOLO...`). |
| `UI_Badge_Cam_Live_Emerald_9Slice.png` | `160×36` | `(0,0,0,0)` | `Sliced` | **`3, 3, 3, 3`** | Pill status saat State 4 (`LIVE · LATAR BELAKANG` / `JENDELA PREVIEW`). |
| `UI_Badge_Cam_Error_Coral_9Slice.png` | `160×36` | `(0,0,0,0)` | `Sliced` | **`3, 3, 3, 3`** | Pill status saat State 5 (`KAMERA GAGAL DIBUKA`). |
| `UI_NavCam_Standby_Amber_9Slice.png` | `180×33` | `(0,0,0,0)` | `Sliced` | **`4, 4, 4, 7`** | Badge status kamera dinamis navbar `SCREEN 00`: `● STANDBY · PILIH KAMERA`. |
| `UI_NavCam_Live_BgGreen_9Slice.png` | `180×33` | `(0,0,0,0)` | `Sliced` | **`4, 4, 4, 7`** | Badge navbar `SCREEN 00`: `● CAM READY · LATAR BELAKANG`. |
| `UI_NavCam_Live_WindowTeal_9Slice.png` | `180×33` | `(0,0,0,0)` | `Sliced` | **`4, 4, 4, 7`** | Badge navbar `SCREEN 00`: `● CAM READY · JENDELA PREVIEW`. |
| `UI_Dot_Status_Amber_10.png` | `14×14` | `(2,2,2,2)` | `Simple` | `0, 0, 0, 0` | Titik indikator state Standby (dipasang di kiri badge status / tombol). |
| `UI_Dot_Status_Cyan_10.png` | `14×14` | `(2,2,2,2)` | `Simple` | `0, 0, 0, 0` | Titik indikator state Loading Cyan. |
| `UI_Dot_Status_Green_10.png` | `14×14` | `(2,2,2,2)` | `Simple` | `0, 0, 0, 0` | Titik indikator state Live Hijau. |
| `UI_Dot_Status_Coral_10.png` | `14×14` | `(2,2,2,2)` | `Simple` | `0, 0, 0, 0` | Titik indikator state Error Coral. |
| `UI_Icon_Camera_Amber_32.png` | `48×36` | `(2,5,2,5)` | `Simple` | `0, 0, 0, 0` | Ikon kamera tengah saat State 1 (`STANDBY Belum Pilih Kamera`). |
| `UI_Icon_Camera_Teal_32.png` | `48×36` | `(2,5,2,5)` | `Simple` | `0, 0, 0, 0` | Ikon kamera tengah saat State 2 (`STANDBY Kamera Terpilih`). |
| `UI_Icon_SpinnerRing_Cyan_38.png` | `44×44` | `(3,3,3,3)` | `Simple` | `0, 0, 0, 0` | Cincin loading State 3 (`centerA = 0`). Putar `transform.Rotate(0, 0, -240f * Time.deltaTime)` saat memuat YOLO. |
| `UI_Icon_ErrorAlert_Coral_36.png` | `42×42` | `(3,3,3,3)` | `Simple` | `0, 0, 0, 0` | Ikon tanda seru Error State 5 (*Kamera gagal dibuka! Pilih kamera lain.*). |

#### `04_Controls/` (`5` File)
| Nama File | Ukuran PNG | Margin `m` | `Image.Type` | Border Unity `L, T, R, B` | Fungsi & Catatan Teknis |
| :--- | :---: | :---: | :---: | :---: | :--- |
| `UI_Slider_Track_9Slice.png` | `240×12` | `(0,0,0,0)` | `Sliced` | **`7, 6, 7, 6`** | Rel latar slider (`Background` pada `UnityEngine.UI.Slider`). Tinggi tetap `12 px`. |
| `UI_Slider_Fill_Green_9Slice.png` | `180×12` | `(0,0,0,0)` | `Sliced` / `Filled` | **`7, 6, 6, 6`** | Fill area slider audio (`Master Volume`, `BGM Volume`, `SFX Volume`). Gunakan `Sliced` sebagai `Fill Rect` `Slider` (atau `Filled Horizontal Left` bila kustom). |
| `UI_Slider_Fill_Amber_9Slice.png` | `180×12` | `(0,0,0,0)` | `Sliced` / `Filled` | **`7, 6, 6, 6`** | Fill area slider **`Turn Sensitivity`** (`1.0×`–`3.0×`). |
| `UI_Slider_Knob_Cream.png` | `28×28` | `(2,1,2,1)` | `Simple` | `0, 0, 0, 0` | Handle kenop slider (`Handle Rect` pada `Slider`). |
| `UI_Input_ValueBox_Taupe_9Slice.png` | `72×34` | `(0,0,0,0)` | `Sliced` | **`5, 5, 5, 5`** | Kotak pembacaan nilai di kanan tiap slider (`80%`, `70%`, `90%`, `1.5x`). |

#### `04_Dropdown/` (`9` File)
| Nama File | Ukuran PNG | Margin `m` | `Image.Type` | Border Unity `L, T, R, B` | Fungsi & Catatan Teknis |
| :--- | :---: | :---: | :---: | :---: | :--- |
| `UI_Dropdown_Trigger_Closed_9Slice.png` | `280×44` | `(0,0,0,0)` | `Sliced` | **`3, 13, 37, 27`** | Kotak pemilih kamera saat tertutup. **`Right = 37`** melindungi kotak pemisah kanan untuk chevron; jaga **tinggi `44 px`**. |
| `UI_Dropdown_Trigger_Open_9Slice.png` | `280×44` | `(0,0,0,0)` | `Sliced` | **`3, 13, 37, 27`** | Tampilan saat popup daftar kamera terbuka. |
| `UI_Icon_Chevron_Down_16.png` | `20×20` | `(4,7,4,6)` | `Simple` | `0, 0, 0, 0` | Ikon panah bawah `▼` diletakkan di area kanan `Trigger_Closed` (`~18 px` dari tepi kanan). |
| `UI_Icon_Chevron_Up_16.png` | `20×20` | `(4,6,4,7)` | `Simple` | `0, 0, 0, 0` | Ikon panah atas `▲` pada `Trigger_Open`. |
| `UI_Dropdown_Popup_9Slice.png` | `260×126` | `(0,0,0,0)` | `Sliced` | **`4, 4, 4, 10`** | Kontainer list popup kamera (`#FBF6EB` **100% Solid Opaque** + shadow `[0, 6]`). Set `Canvas.sortingOrder` tertinggi di atas modul lain! |
| `UI_Dropdown_Row_Even_9Slice.png` | `240×40` | `(0,0,0,0)` | `Sliced` | **`3, 3, 3, 3`** | Item baris genap pada list dropdown kamera (`Item 0, 2, 4...`). |
| `UI_Dropdown_Row_Odd_9Slice.png` | `240×40` | `(0,0,0,0)` | `Sliced` | **`3, 3, 3, 3`** | Item baris ganjil pada list dropdown (`Item 1, 3, 5...`). |
| `UI_Dropdown_Row_Selected_Gold_9Slice.png` | `240×42` | `(0,0,0,0)` | `Sliced` | **`3, 3, 3, 3`** | Highlight emas untuk item kamera terpilih aktif saat ini. |
| `UI_Icon_Checkmark_16.png` | `32×30` | `(8,8,8,8)` | `Simple` | `0, 0, 0, 0` | Ikon centang `✓` di sisi kanan `UI_Dropdown_Row_Selected_Gold`. |

#### `04_Viewports/` (`7` File)
| Nama File | Ukuran PNG | Margin `m` | `Image.Type` | Border Unity `L, T, R, B` | Fungsi & Catatan Teknis |
| :--- | :---: | :---: | :---: | :---: | :--- |
| `UI_Viewport_Calib_Frame_Hollow_9Slice.png` | `260×200` | `(0,0,0,0)` | `Sliced` | **`7, 7, 7, 7`** | **Berlubang Tengah (`centerA = 0`)**: letakkan di atas `RawImage` stream webcam/skeleton (`SCREEN 01` kiri) dan `RenderTexture` naga 3D (`SCREEN 01` kanan). Set `Raycast Target = false`. |
| `UI_Viewport_PIP_Bezel_Hollow_9Slice.png` | `220×175` | `(0,0,0,0)` | `Sliced` | **`7, 9, 7, 12`** | **Berlubang Tengah (`centerA = 0`)**: bingkai Monitor PIP 16:9 (`420×236`) di sudut kanan bawah `SCREEN 02`. Letakkan di atas `RawImage` UDP `5053`. |
| `UI_Viewport_DarkFallback_BG_9Slice.png` | `160×120` | `(0,0,0,0)` | `Sliced` | **`3, 3, 3, 3`** | Latar gelap `#1A2125` di belakang ikon kamera/spinner/error saat feed live belum berjalan (`STANDBY`, `MEMUAT YOLO`, `ERROR`). |
| `UI_Viewfinder_Brackets_Green_9Slice.png` | `168×128` | `(10,10,10,10)` | `Sliced` | **`33, 33, 33, 33`** | Bracket siku viewfinder hijau (`centerA = 0`), di-overlay di atas viewport saat tracking aktif (`SCREEN 01`). |
| `UI_Viewfinder_Brackets_Teal_9Slice.png` | `168×128` | `(10,10,10,10)` | `Sliced` | **`33, 33, 33, 33`** | Bracket siku viewfinder teal (`centerA = 0`). |
| `UI_Calib_SidebarCard_Dark_9Slice.png` | `200×90` | `(0,0,0,0)` | `Sliced` | **`3, 3, 3, 3`** | Kartu gelap solid di kolom kanan viewport `SCREEN 01` (progress bar `CALIBRATED: 100% (5/5)`, status pill). |
| `UI_Calib_InstructionStrip_Dark_9Slice.png` | `240×64` | `(0,0,0,0)` | `Sliced` | **`3, 3, 3, 3`** | Strip instruksi gelap di bawah kartu kalibrasi (`Hold neutral pose 3s...`) atau label `● LIVE FEED 16:9 + SKELETON POSE` di `SCREEN 06`. |

---

### 3.5 `05_Challenge/`, `05_HUD/`, `05_Rewards/` (`17` File)

#### `05_Challenge/` (`5` File)
| Nama File | Ukuran PNG | Margin `m` | `Image.Type` | Border Unity `L, T, R, B` | Fungsi & Catatan Teknis |
| :--- | :---: | :---: | :---: | :---: | :--- |
| `UI_Badge_Active_Green_Blank.png` | `240×52` | `(0,0,0,0)` | `Sliced` | **`31, 27, 4, 15`** | Pill badge status hijau di bawah judul overlay desa `SCREEN 03` (`VILLAGE ACTION ACTIVE` / kondisi auto-hover). |
| `UI_Illust_Frame_Dark_9Slice.png` | `240×160` | `(0,0,0,0)` | `Sliced` | **`4, 16, 4, 139`** | Bingkai gelap latar ilustrasi aksi gestur `SCREEN 03`. Gunakan ukuran tetap/`T=16,B=139` melindungi kontur tanah bawah. |
| `UI_Illust_Dragon_WaterBreath.png` | `269×170` | `(7,13,7,35)` | `Simple` *(Preserve Aspect)* | `0, 0, 0, 0` | Ilustrasi terisolasi naga menyemburkan air purba (`SCREEN 03`). Bisa dianimasikan scale pulse halus saat fase auto-hover desa. |
| `UI_Progress_Horiz_Track_9Slice.png` | `280×41` | `(0,0,0,0)` | `Sliced` | **`4, 4, 4, 7`** | Track bar progres lokal pemulihan desa (`0% -> 100%`) di `SCREEN 03`. |
| `UI_Progress_Horiz_Fill_Blue_9Slice.png` | `274×32` | `(0,0,0,0)` | `Filled` *(Horiz Left)* / `Sliced` | `0, 0, 0, 0` *(atau `3, 9, 3, 3`)* | Bar isi progres air biru. Untuk Unity paling mulus gunakan `Image.Type = Filled` (`Horizontal`, `Origin = Left`, `Fill Amount = 0..1`) di dalam Track. |

#### `05_HUD/` (`6` File)
| Nama File | Ukuran PNG | Margin `m` | `Image.Type` | Border Unity `L, T, R, B` | Fungsi & Catatan Teknis |
| :--- | :---: | :---: | :---: | :---: | :--- |
| `UI_HUD_Capsule_Parchment_9Slice.png` | `220×60` | `(0,0,0,0)` | `Sliced` | **`4, 4, 4, 8`** | Kapsul krem HUD `SCREEN 02`: kapsul tengah atas (`02:15 · VILLAGES: 1/3`) dan kapsul dock telemetri kanan bawah (`▲ 42m 12.0m/s [POSE T]`). |
| `UI_HUD_CheckDot_Green.png` | `16×16` | `(3,3,3,3)` | `Simple` | `0, 0, 0, 0` | Lingkaran ikon centang hijau kecil pada `VILLAGES: 1/3`. |
| `UI_HUD_Vitality_Track_40x480.png` | `40×484` | `(0,0,0,0)` | `Sliced` *(atau Simple)* | **`4, 4, 4, 8`** | Track meteran vertikal Vitality di kiri layar `SCREEN 02`. *(Catatan: file aktual `40×484 px` karena overhang shadow bawah `4 px`)*. |
| `UI_HUD_Vitality_Fill_34x474.png` | `34×474` | `(0,0,0,0)` | `Filled` *(Vert Bottom)* | `0, 0, 0, 0` | Fill hijau emerald di dalam Track. Wajib set **`Image.Type = Filled` -> `Fill Method = Vertical` -> `Fill Origin = Bottom`**, update via `fillAmount` (`0f..1f`). |
| `UI_HUD_Vitality_Ticks_Overlay.png` | `34×474` | `(11,119,11,113)` | `Simple` | `0, 0, 0, 0` | Garis skala meteran transparan (`centerA = 0`). Tumpuk tepat di atas Fill (`Raycast Target = false`). |
| `UI_HUD_Waypoint_Chevron.png` | `33×32` | `(6,8,7,8)` | `Simple` | `0, 0, 0, 0` | Panah kompas penunjuk arah desa di tengah layar (`▲ WILDFIRE · 140m`). |

#### `05_Rewards/` (`6` File)
| Nama File | Ukuran PNG | Margin `m` | `Image.Type` | Border Unity `L, T, R, B` | Fungsi & Catatan Teknis |
| :--- | :---: | :---: | :---: | :---: | :--- |
| `UI_StarBox_Card_Slot_80x76.png` | `80×80` | `(0,0,0,0)` | `Sliced` / `Simple` | **`7, 4, 7, 27`** | Kotak slot bintang ber-pill gelap `STAR 1..3` / `0 / 1 ★` di kartu `SCREEN 00` (`80×80 px` aktual termasuk shadow bawah). `B = 27` menjaga label gelap bawah tetap tajam. |
| `UI_StarBox_Victory_Slot_100x90.png` | `100×94` | `(0,0,0,0)` | `Sliced` / `Simple` | **`9, 4, 9, 31`** | Kotak slot bintang versi lebih besar (`100×94 px` aktual termasuk shadow) untuk `Victory Modal` (`SCREEN 04`). |
| `UI_Icon_Star_Gold_40.png` | `44×44` | `(5,4,5,7)` | `Simple` | `0, 0, 0, 0` | Ikon bintang emas bersinar (status tercapai). |
| `UI_Icon_Star_Empty_40.png` | `44×44` | `(5,4,5,7)` | `Simple` | `0, 0, 0, 0` | Ikon bintang kosong gelap (belum tercapai). |
| `UI_Box_Locked_Center_96x80.png` | `96×84` | `(0,0,0,0)` | `Sliced` | **`8, 8, 8, 14`** | Kotak latar charcoal pengganti slot bintang di kartu `STAGE 03` saat terkunci (`96×84 px` aktual). Alternatif lebih aman jika butuh kotak gembok lebih lebar dibanding `UI_Box_Locked_Charcoal_9Slice`. |
| `UI_Icon_Padlock_Cream_36.png` | `72×72` | `(17,21,17,6)` | `Simple` | `0, 0, 0, 0` | Ikon gembok krem untuk di tengah kotak/kartu `LOCKED`. |

---

### 3.6 `06_Icons/` & `06_Poses/` (`25` File)

#### `06_Icons/` (`10` File — Semua `Image.Type = Simple`, Border `0, 0, 0, 0`)
| Nama File | Ukuran PNG | Margin `m` | `Preserve Aspect` | Lokasi Pemakaian |
| :--- | :---: | :---: | :---: | :--- |
| `UI_Icon_Hourglass_Amber.png` | `48×48` | `(17,15,17,15)` | `true` | Kapsul Timer `SCREEN 02` & Baris Waktu Tempuh `SCREEN 04`. |
| `UI_Icon_Leaf_Green.png` | `48×48` | `(15,15,15,16)` | `true` | Lencana persentase `Vitality` (`33%` di `SCREEN 02`) & Baris Desa Pulih `SCREEN 04`. |
| `UI_Icon_ScoreStar_Gold.png` | `48×48` | `(14,14,14,16)` | `true` | Baris Rating/Skor `SCREEN 04`. |
| `UI_Icon_CoinMedallion_Gold.png` | `48×48` | `(8,8,8,8)` | `true` | Medali koin emas standalone (bila membuat badge reward kustom di luar `SpeechBubble_WithCoin`). |
| `UI_Icon_Speaker_Audio.png` | `48×48` | `(15,14,14,14)` | `true` | Soket medali baris slider `Master Volume` (`SCREEN 05`). |
| `UI_Icon_MusicNote_BGM.png` | `48×48` | `(14,15,14,16)` | `true` | Soket medali baris slider `BGM Volume` (`SCREEN 05`). |
| `UI_Icon_Bolt_SFX.png` | `48×48` | `(17,13,17,13)` | `true` | Soket medali baris slider `SFX Volume` (`SCREEN 05`). |
| `UI_Icon_Sensitivity_Arrows.png` | `48×48` | `(14,15,14,16)` | `true` | Soket medali baris slider `Turn Sensitivity` (`SCREEN 05`). |
| `UI_Icon_Padlock_Vector.png` | `72×69` | `(17,21,17,3)` | `true` | Varian gembok vektor bersih (`69 px` tinggi) bila diperlukan di overlay/badge. |
| `UI_Pose_Medallion_Slot_58.png` | `64×64` | `(3,2,3,1)` | `true` | Soket medali latar kosong jika ingin merender ikon pose secara terpisah. |

#### `06_Poses/` (`15` File — Grid `3×5` Kalibrasi `SCREEN 01`)
Seluruh file pose memakai **`Image.Type = Simple`**, **`Preserve Aspect = true`**, Border `0, 0, 0, 0`. Sudah menyatu dengan medali bingkai & shadow `[0, 3]`:
* **Baris 1 (Ground & Steering):**
  * `UI_Pose_01_GroundedIdle.png` (`64×64`, `m=(3,2,3,1)`)
  * `UI_Pose_02_GroundedWalk.png` (`64×64`, `m=(3,2,3,1)`)
  * `UI_Pose_03_GroundedLookUp.png` (`64×64`, `m=(3,2,3,1)`)
  * `UI_Pose_04_Takeoff.png` (`64×64`, `m=(3,2,3,1)`)
  * `UI_Pose_05_LeanArmTilt.png` (`64×64`, `m=(3,2,3,1)`)
* **Baris 2 (Core Flight):**
  * `UI_Pose_06_FlapToClimb.png` (`64×64`, `m=(3,2,3,1)`)
  * `UI_Pose_07_FlyUp.png` (`64×64`, `m=(3,2,3,1)`)
  * `UI_Pose_08_Glide_ActiveGold.png` (`64×64`, `m=(3,2,3,1)`) *(aksen medali emas untuk kartu pose aktif default)*
  * `UI_Pose_09_HoverMode.png` (`64×64`, `m=(3,2,3,1)`)
  * `UI_Pose_10_GentleDescent.png` (`64×64`, `m=(3,2,3,1)`)
* **Baris 3 (Descent, Dive, Boost & Village):**
  * `UI_Pose_11_FlyDown_Y.png` (`64×64`, `m=(3,2,3,1)`)
  * `UI_Pose_12_FlyDownIdle_W.png` (`64×64`, `m=(3,2,3,1)`)
  * `UI_Pose_13_TuckToDive.png` (`64×64`, `m=(3,2,3,1)`)
  * **`UI_Pose_14_PropellerDash.png` (`66×64`, `m=(4,2,4,1)`)** *(⚠️ `2 px` lebih lebar akibat ujung lingkaran putaran tangan — pasang pada `RectTransform` `66×64` atau `64×64` dengan `Preserve Aspect = true`)*
  * `UI_Pose_15_VillageActions.png` (`64×64`, `m=(3,2,3,1)`)

---

### 3.7 `07_Backgrounds/` & `07_Biomes/` (`8` File)
| Folder | Nama File | Ukuran PNG | Margin `m` | `Image.Type` | Border Unity `L, T, R, B` | Fungsi & Catatan Teknis |
| :--- | :--- | :---: | :---: | :---: | :---: | :--- |
| `07_Backgrounds/` | `UI_Bg_Lobby_AerialIsland.png` | `1920×1080` | `(0,0,0,0)` | `Simple` *(Preserve Aspect / Envelope)* | `0, 0, 0, 0` | Background penuh Lobby (`SCREEN 00`, `01`, `05`, `06`). Gunakan **`AspectRatioFitter (EnvelopeParent, 1.7778)`** jika mendukung rasio layar selain 16:9. |
| `07_Biomes/` | `UI_Biome_01_SmogValley_Framed.png` | `392×222` | `(6,4,6,4)` | `Simple` | `0, 0, 0, 0` | Ilustrasi Biome 01 lengkap dengan bingkai & shadow (siap pakai langsung di Card 01 `SCREEN 00`). |
| `07_Biomes/` | `UI_Biome_02_Wildfire_Framed.png` | `392×222` | `(6,4,6,4)` | `Simple` | `0, 0, 0, 0` | Ilustrasi Biome 02 lengkap dengan bingkai & shadow (siap pakai di Card 02). |
| `07_Biomes/` | `UI_Biome_03_Drought_Framed.png` | `392×222` | `(6,4,6,4)` | `Simple` | `0, 0, 0, 0` | Ilustrasi Biome 03 lengkap bingkai (bersih tanpa scrim terkunci; tumpuk `UI_Scrim_LockedCard_Overlay` di Unity saat terkunci). |
| `07_Biomes/` | `UI_Biome_01_SmogValley_Raw.png` | `380×210` | `(0,0,0,0)` | `Simple` | `0, 0, 0, 0` | Versi polos tanpa bingkai untuk keperluan animasi parallax / masking kustom di atas `UI_Biome_Frame_Border_9Slice`. |
| `07_Biomes/` | `UI_Biome_02_Wildfire_Raw.png` | `380×210` | `(0,0,0,0)` | `Simple` | `0, 0, 0, 0` | Versi polos Biome 02 tanpa bingkai. |
| `07_Biomes/` | `UI_Biome_03_Drought_Raw.png` | `380×210` | `(0,0,0,0)` | `Simple` | `0, 0, 0, 0` | Versi polos Biome 03 tanpa bingkai. |
| `07_Biomes/` | `UI_Biome_Frame_Border_9Slice.png` | `392×222` | `(6,4,6,4)` | `Sliced` | **`10, 12, 10, 12`** | Bingkai berlubang (`centerA = 0`) khusus untuk membungkus versi `_Raw` atau thumbnail dinamis. |

---

## 4. PANDUAN PERAKITAN PER LAYAR (`SCREEN 00–06`) & `SECTION 08` RUNTIME

Seluruh **134 PNG bersifat tanpa teks**. Setiap label, angka timer, dan status state dibuat menggunakan **`TextMeshProUGUI`** di dalam Scene Unity.

### 4.1 Palet Tipografi & Warna Teks (`TextMeshProUGUI`)
* **Font Disarankan** (mengacu [[rencana_desain_ui]]): *Clean geometric rounded sans-serif* (**`Fredoka`** / **`Fredoka One`** untuk judul/tag/CTA, dan **`Nunito`** / **`Quicksand`** untuk deskripsi & telemetri).
* **Warna Teks Tinta Perkamen (Judul Kartu, Navbar Idle, Stat, Deskripsi):** **`#2D2926`** atau **`#332C25`** (`RGB 45, 41, 38` / `51, 44, 37`).
* **Warna Teks Kontras Gelap (di atas tombol Hijau, Tombol Charcoal, Stage Tag 01/03, & Viewport Gelap `#1A2125`):** **`#FFFFFF`** (Putih murni) atau krem lembut `#FBF6EB`.
* **Warna Aksen Teks di Viewport Gelap / State:**
  * Status Aktif/Siap: Hijau `#5C8D46` / Cyan `#29B6D8` / Amber `#E2A43B` / Error Coral `#D15046`.

### 4.2 Urutan Layer & Komponen Tiap Layar
1. **Shared Top Bar (`SCREEN 00, 01, 05`):**
   * Kiri: Judul **`AR DRAGON · SKY GUARDIAN`** (`TMP` warna `#2D2926`).
   * Tengah: `UI_Navbar_Rail_9Slice` -> 3 tab (`UI_Tab_Default_9Slice` / `UI_Tab_Active_Gold_9Slice` yang dikontrol `LobbyNavigationManager.cs`).
   * Kanan: Badge status dinamis `UI_NavCam_*_9Slice` (khusus `SCREEN 00`) + `UI_Btn_TopChrome_Cream_9Slice` berisi `UI_IconBadge_CloseX_20` & teks `ESC · QUIT` / `ESC · BACK`.
2. **`SCREEN 00` — Missions / Level Selection:**
   * Latar: `UI_Bg_Lobby_AerialIsland`.
   * 3 Kartu Level (`UI_Panel_Parchment_Full_9Slice` + 4× `UI_Corner_Fan_Medallion_24`):
     * Top Ribbon Overlap: `UI_Tag_Stage_Green_Blank` (`STAGE 01`), `Gold` (`STAGE 02`), `Dark` (`STAGE 03`).
     * Thumbnail: `UI_Biome_01..03_Framed` (atau `_Raw` + `UI_Biome_Frame_Border_9Slice`). Pada Card 03 saat terkunci, aktifkan `UI_Scrim_LockedCard_Overlay` tepat di atas gambar `380×210`.
     * Judul Level: `UI_Banner_Stepped_Card_Blank` + `TMP`.
     * Strip Limit: `UI_Row_Recessed_Track_9Slice` + `UI_Socket_Medallion_32_GreenCore`.
     * Baris Bintang / Terkunci: 3× `UI_StarBox_Card_Slot_80x76` berisi `UI_Icon_Star_Gold_40` / `Empty_40`; atau pada Stage 03 saat terkunci gunakan `UI_Box_Locked_Charcoal_9Slice` / `UI_Box_Locked_Center_96x80` + `UI_Icon_Padlock_Cream_36`.
     * Callout & CTA Bawah: `UI_Callout_SpeechBubble_Gold_WithCoin` (`90s`, `150s`) overlap di atas kanan tombol `UI_Btn_Primary_Green_9Slice` (`PLAY`) / `Secondary_Cream` (`SELECT`) / `Locked_Charcoal` (`UNLOCK: ...`).
3. **`SCREEN 01` — Calibration & 15 Motion Pose Tutorial:**
   * **Panel Kiri & Kanan:** `UI_Panel_Parchment_Calib_9Slice` + `UI_Banner_Stepped_SubPanel_Blank` + `UI_Header_Bar_Parchment_9Slice`.
   * **Kontrol Kamera (`Panel Kiri`):** `UI_Badge_Cam_*` + `UI_Btn_Danger_Coral_*` + `UI_Dropdown_Trigger_*` + `UI_Btn_Small_Cream` (`Scan`) + 2× `UI_Btn_CamMode_*`.
   * **Viewport Kalibrasi & Naga (`Section 08 Runtime Replacement`):**
     * Letakkan **`RawImage`** (feed kamera/skeleton dari UDP `5053`) atau **`RawImage` dengan `RenderTexture`** (model 3D naga dari kamera terisolasi di sisi kanan) **di belakang** `UI_Viewport_Calib_Frame_Hollow_9Slice`.
     * Saat kamera `STANDBY` / `MEMUAT` / `ERROR`, aktifkan panel `UI_Viewport_DarkFallback_BG_9Slice` + ikon/teks panduan state terkait.
     * Sisi kanan dalam viewport kiri: `UI_Calib_SidebarCard_Dark_9Slice` + `UI_Calib_InstructionStrip_Dark_9Slice` + `UI_Viewfinder_Brackets_Green/Teal`.
     * Trigger UDP `5054`: `UI_Btn_Small_Green` (`RECALIBRATE (C)` -> `CALIBRATE` / `CANCEL`) + `UI_Btn_Small_Cream` (`RESET DEFAULT` -> `CALIBRATE_RESET`).
   * **Grid 15 Kartu Pose:** 15× `UI_Plaque_PoseCard_Default_9Slice` (ganti ke `Active_9Slice` + `UI_Pill_Active_Gold` saat pose terdeteksi oleh `UDPPoseReceiver.cs`) dengan child `UI_Pose_01..15`.
   * **Bar Bawah:** 2× `UI_Chip_Bar_Cream_9Slice` (`UI_Badge_Label_Amber_Blank` & `Teal_Blank`) + `UI_Btn_Primary_Green` (`READY TO FLY`).
4. **`SCREEN 02` — In-Game Flight HUD:**
   * Latar Canvas **transparan** agar scene pulau 3D terlihat langsung (`> 85%` area layar bebas).
   * Top Center: `UI_HUD_Capsule_Parchment_9Slice` + `UI_Icon_Hourglass_Amber` + `UI_HUD_CheckDot_Green` (`02:15 · VILLAGES: 1/3`).
   * Top Right: `UI_Btn_TopChrome_Cream_9Slice` + `UI_IconBadge_Pause_20` (`ESC · PAUSE`).
   * Sisi Kiri: `UI_HUD_Vitality_Track_40x480` + `UI_HUD_Vitality_Fill_34x474` (`Filled Vertical Bottom`) + `UI_HUD_Vitality_Ticks_Overlay` + pill `UI_HUD_Capsule_Parchment` + `UI_Icon_Leaf_Green` (`33%`).
   * Tengah: `UI_HUD_Waypoint_Chevron` + `TMP`.
   * Kanan Bawah: Dock Telemetri (`UI_HUD_Capsule_Parchment` + badge mode `POSE T/Y/A`), Header Kontrol PIP (`UI_Overlay_Popup_Opaque_9Slice` + kontrol state kamera), dan kotak PIP 16:9 (`RawImage` UDP `5053` dibingkai `UI_Viewport_PIP_Bezel_Hollow_9Slice`).
5. **`SCREEN 03` — Village Action Overlay:**
   * `UI_Panel_Parchment_Full_9Slice` (dengan 4× `Corner_Fan_Medallion_24`) + `UI_Crest_Baroque_Scrollwork` + `UI_Banner_Stepped_Modal_Blank` + `UI_Badge_Active_Green_Blank` + `UI_Illust_Frame_Dark_9Slice` + `UI_Illust_Dragon_WaterBreath` + `UI_Progress_Horiz_Track_9Slice` + `UI_Progress_Horiz_Fill_Blue_9Slice` (`Filled Horizontal Left`).
6. **`SCREEN 04` — Finish Modal (Victory & Defeat):**
   * Victory memakai `UI_Banner_Stepped_Modal_Blank`; Defeat memakai `UI_Stamp_Defeat_Red_Blank`.
   * Isi modal: `UI_Plaque_SubParchment_9Slice` + 3× `UI_StarBox_Victory_Slot_100x90` (`UI_Icon_Star_Gold/Empty_40`) + 3× `UI_Plaque_StatRow_9Slice` (`UI_Socket_Medallion_38_Dark` + `UI_Icon_Hourglass_Amber`, `Leaf_Green`, `ScoreStar_Gold`) + `UI_Callout_SpeechBubble_Gold_*` + `UI_Btn_Primary_Green` + `UI_Btn_Secondary_Cream`.
7. **`SCREEN 05` & `SCREEN 06` — Settings Modal, Slider & Solid Camera Dropdown:**
   * 4 baris setting memakai `UI_Plaque_StatRow_9Slice` + `UI_Socket_Medallion_38_Dark` + ikon (`Speaker_Audio`, `MusicNote_BGM`, `Bolt_SFX`, `Sensitivity_Arrows`) + `Slider` (`UI_Slider_Track`, `UI_Slider_Fill_Green/Amber`, `UI_Slider_Knob_Cream`) + `UI_Input_ValueBox_Taupe`.
   * **Popup Daftar Kamera (`TMPro.TMP_Dropdown` Template):**
     * Ganti background **`Template`** menjadi `UI_Dropdown_Popup_9Slice` (**`alpha = 1.0f` solid opaque**) agar teks modul/slider di belakang popup tertutup sempurna di *Linear Color Space*.
     * Gunakan `UI_Dropdown_Row_Even/Odd_9Slice` dan `UI_Dropdown_Row_Selected_Gold_9Slice` + `UI_Icon_Checkmark_16` untuk item aktif.

---

## 5. CHECKLIST INTEGRASI C# (`DragonWingAR.*`)

| Skrip Unity Target | Elemen UI Terikat (`Components/` & `TMP`) | Aksi / Event |
| :--- | :--- | :--- |
| **`LobbyNavigationManager.cs`** | `UI_Tab_Default/Active_Gold_9Slice`, panel `SCREEN 00 / 01 / 05` | Mengaktifkan panel tab & menukar sprite tab aktif emas. |
| **`PythonProcessManager.cs`** | `UI_NavCam_*`, `UI_Badge_Cam_*`, `UI_Btn_CamMode_*`, `UI_Btn_Danger_Coral_*`, `UI_Dropdown_*`, `UI_Viewport_DarkFallback_BG`, `UI_Icon_*` | Memindai kamera (`RefreshCameraListAsync()` / `--list-json`), `SelectCamera()`, `LaunchBackgroundMode()` (`--headless`), `LaunchExternalPreviewMode()`, menghentikan YOLO (`Matikan`), dan menyinkronkan ke-5 state visual kamera di `SCREEN 00, 01, 02, 05`. |
| **`InGameFlightHUD.cs`** | `UI_HUD_Vitality_Fill_34x474`, `UI_HUD_Capsule_Parchment`, `UI_HUD_Waypoint_Chevron`, `UI_Viewport_PIP_Bezel_Hollow`, modul PIP & `SCREEN 03/04` | Memperbarui `fillAmount` Vitality, countdown timer, waypoint, telemetri kecepatan/ketinggian/pose, `RawImage` stream PIP UDP `5053`, serta kontrol kamera in-game. |
| **`UDPPoseReceiver.cs`** | 15× `UI_Plaque_PoseCard_*`, `UI_Pill_Active_Gold`, `RawImage` (`RenderTexture` naga), `UI_Btn_Small_Green/Cream` (`SCREEN 01`) | Menerima paket pose UDP `5052`, menggerakkan pratinjau model naga 3D & highlight kartu pose aktif, serta mengirim perintah kalibrasi UDP `5054` (`CALIBRATE`, `CANCEL`, `CALIBRATE_RESET`). |

---
**Navigasi Obsidian:** [[00 - Overview]] · [[rencana_desain_ui]] · [[konteks]] · [[prd]] · [[Tasks]] · [[Devlog]]
