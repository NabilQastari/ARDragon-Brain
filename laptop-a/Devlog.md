---
title: Laptop A — Devlog (Desain, Ide, & Arsitektur)
tags:
  - devlog
  - laptop-a
owner: Laptop A
updated: 2026-11-14
---

# 📝 Devlog — Laptop A

> Catatan kronologis keputusan desain, ide, arsitektur, dan pembaruan dokumentasi dari **Laptop A**. Kembali ke [[00 - Overview]].

---

## 2026-11-14 — Penyempurnaan Kriya Visual UI Mendekati 3 Referensi (`Design` v1.6)
- **Tujuan:** Menyelaraskan detail komponen seluruh 7 artboard (`SCREEN 00–06`) di page `Design` (`id: 262`) pada [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) (`859905` bytes) agar lebih mendekati tiga gambar referensi RPG kartu perkamen sambil tetap mematuhi aturan outline netral seragam (`v1.5`) & ruang blank `>85%` di `SCREEN 02`.
- **Peningkatan Komponen per Area:**
  - **Kartu Utama, Spanduk & Ornamen (`SCREEN 00–06`):** Menambah bibir 3D batu gelap (`#4A4238` `[0, 7]`), menebalkan inner stepped/scallop trim (`#A39884`/`#827766`), memasang 4 ornamen sudut **Corner Fan-Medallion (`24×24 px`)** pada kartu utama `SCREEN 00, 03, 04, 05`, mengubah seluruh banner judul (`SCREEN 00, 01, 03, 04, 05, 06`) menjadi **Stepped Banner** seragam, menurunkan banner `SCREEN 03` (`y=155`) menumpuk batas atas kartu, menyempurnakan **Baroque Scrollwork Crest** simetris di `SCREEN 03–05` dengan jarak napas rapi di bawah navbar `SCREEN 05`, dan menambah sheen & pemusatan vertikal tag `STAGE 01–03`.
  - **Hirarki Kartu Referensi (`SCREEN 00, 03, 04, 05, 06`):** Mengubah baris info/track (`Sub Track`, `Lean Tag`, `Val Track`, `Notes 1–5`, `Proof Rows`) menjadi gaya **borderless recessed warm-taupe** (`#C8BFA8`/`#D5CCBA`, `strokes: []`) bermedali kiri tumpang-tindih (`#38312A`), menambah pill label gelap bawah (`STAR 1..3`, `STAR 01..03`, `0 / 1 ★`) di kotak bintang & merapikan posisi bintang, menempatkan tag speech-bubble emas `#E2A43B` (*coin medallion + corner hatch ticks*) tepat di atas tombol `PLAY`, `SELECT`, dan `NEXT LEVEL`, serta mengonversi slot & tombol terkunci `Card 03` ke *charcoal slate* `#2B2E31`/`#2E3236`.
  - **`SCREEN 01` & `SCREEN 02`:** Memperbaiki kontras teks tombol `RECALIBRATE (C)` ke putih `#FFFFFF` lengkap dengan sheen & double left notch dengan jarak bersih, menambah bibir 3D `[0, 4]` & `[0, 3]` pada seluruh 15 kartu pose, 15 medali ikon, strip kamera, dan seluruh kapsul/track HUD `SCREEN 02`.
- **Verifikasi & Simpan:** Seluruh 7 artboard (`263, 314, 348, 365, 377, 400, 876`) diverifikasi ulang via screenshot render FigCraft dan disinkronkan ke [[rencana_desain_ui]] (`v1.6`).

## 2026-11-14 — Pengembalian Biome 1 ke Flat Layout, Shade Smooth, & Render 4 Foto Referensi 3D Individual + Prompt
- **Keputusan Desain:**
  - Atas arahan pengguna, **Biome 1 (`SM_Island_Biome1_Smog`) dikembalikan ke bentuk datar (*flat*)** seperti ketiga pulau lainnya (seluruh pulau kembali pada `Z = 0.000 m` hingga `-1.500 m`, koleksi `COL_Biome1_Water` serta relief migrasi `Island_v02` dilepas dari scene utama).
  - Sebelum memodelkan ulang kontur elevasi, gunung, dan danau tiap wilayah, disiapkan **1 foto 3D sudut *3/4 bird's-eye* terisolasi per pulau** (tanpa pulau tetangga dan tanpa lingkaran marker agar AI image generator fokus 100% pada siluet garis pantai masing-masing) disertai **4 prompt generasi gambar 3D** sesuai spesifikasi [[konteks]] Bagian 2.3 & [[GDD_Pulau]].
- **Implementasi di [`Pulau_Archipelago.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/Pulau_Archipelago.blend) (`230,048` bytes):**
  - Keempat pulau datar (`SM_Island_Center` `574v/1077f`, `SM_Island_Biome1_Smog` `658v/1230f`, `SM_Island_Biome2_Wildfire` `620v/1159f`, `SM_Island_Biome3_Drought` `836v/1575f`) & 4 marker (`COL_Layout_Markers`) seluruhnya menerapkan **Shade Smooth** pada permukaan top & dinding pantai melengkung dengan `sharp_edge` pada bibir pantai 90° dan alas bawah (`qa_check.py`: keempatnya `pass=True`, `errors=[]`, `uv_overlap=0.0%`; `scene_audit.py`: `ok=True`, `objects=12`, `issues={}`, `orphans=0`).
  - **4 Foto Referensi Pulau Datar Terisolasi (`1280 × 1280 px`, sudut 3/4 SSW -> NNE):**
    - Central Island: [`ref_flat_3d_center.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/ref_flat_3d_center.png) (`1,460,074` B)
    - Biome 1 (Smog Valley): [`ref_flat_3d_biome1_smog.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/ref_flat_3d_biome1_smog.png) (`1,448,905` B)
    - Biome 2 (Wildfire Woods): [`ref_flat_3d_biome2_wildfire.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/ref_flat_3d_biome2_wildfire.png) (`1,474,458` B)
    - Biome 3 (Drought Plateau): [`ref_flat_3d_biome3_drought.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/ref_flat_3d_biome3_drought.png) (`1,408,313` B)
  - Ikhtisar archipelago datar diperbarui di [`flat_topdown.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/flat_topdown.png) (`1,513,346` B) & [`flat_perspective.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/flat_perspective.png) (`1,266,514` B).

## 2026-11-14 — Migrasi Kontur Ketinggian, 3 Danau, & Gunung `Island_v02.blend` ke Biome 1 (`SM_Island_Biome1_Smog`)
- **Keputusan Desain & Teknik Pemetaan (*1:1 Square -> Organic Coastline*):**
  - Sumber [`Island_v02.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/Island_v02.blend) (`800 × 800 m` grid `1:1`, `18.817` verts / `35.905` faces, elevasi hingga `118.90 m`) dipindahkan **hanya** ke Biome 1 (`SM_Island_Biome1_Smog`) dengan **garis pantai luar *smooth-minimalist* `XY` Biome 1 dipertahankan 100% utuh** (`189.81 × 174.14 m` Blender); pulau `Center`, `Biome 2`, dan `Biome 3` tetap datar. Batu lepas `Island_Rocks` tidak ikut dimigrasi.
  - Menggunakan **Fong Elliptical Grid Mapping** ($C^\infty$ tanpa lipatan diagonal) dengan rotasi **`-65°`** dan ekspansi domain radial **`B1_MAP_EXPAND = 1.22`** agar ketiga danau dan punggung gunung luar dari `Island_v02` tertarik masuk aman ke dalam pulau (`jarak pantai >= 7.5 m`), dipadukan dengan taper pantai halus (`B1_COAST_BLEND = 6.8 m`) agar pantai tetap landai berumput (`Z = 0.0 m`) kecuali tebing gunung curam (`> 40°`).
  - Faktor elevasi `B1_Z_SCALE = 0.185` + datum lembah `B1_Z_LIFT = 1.85 m` menghasilkan puncak tertinggi **`20.42 m`** di Blender (**`~122.5 m`** saat di Unity `Scale = (6, 6, 6)`), aman di bawah batas `maxAltitude = 160 m`.
- **Implementasi & Hasil di [`Pulau_Archipelago.blend`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/Pulau_Archipelago.blend) (`505,063` bytes):**
  - **`SM_Island_Biome1_Smog` (`5.698` verts, `11.310` faces / `11.392` tris, `dim = 189.81 × 174.14 × 21.92 m`, `Z = [-1.500, 20.423] m`):** Teras bertingkat hijau (`M_Flat_Biome1_Smog`), tebing & punggung batu (`M_Biome1_Rock`), dinding kawah/puncak gelap (`M_Biome1_RockDark`), tepi danau tanah hangat (`M_Biome1_LakeShore`), serta tebing luar (`M_Flat_CoastEdge`).
  - **Shading (*Shade Smooth + Sharp Perimeter Rims*):** Seluruh permukaan top terrain & sisi dinding pantai melengkung di-set *Shade Smooth* (`SM_Island_Biome1_Smog` `5.696/11.310` face top+side halus; `SM_Island_Center` `572/1077`, `SM_Island_Biome2_Wildfire` `618/1159`, `SM_Island_Biome3_Drought` `834/1575`; `SM_Biome1_Water_01..03` `96/144`; `Marker_Desa1_Smog` `72/72`), dengan atribut `sharp_edge` dikunci pada bibir pantai 90° (`top <-> side`), alas bawah (`side <-> bot`), dan sudut tebing `> 45°` sehingga visual *low-poly* faset menghilang tanpa kebocoran gradasi normal gelap di tepian datar.
  - **Koleksi `COL_Biome1_Water` (3 danau bertopologi *watertight wafer* bersudut bersih, masing-masing `98` verts / `144` faces / `192` tris, tebal `0.14 m`, `uv_overlap_pct = 0.0%`):**
    - `SM_Biome1_Water_01` (`37.04 × 23.51 m`, `loc = (-59.57, 128.91, 1.63)`)
    - `SM_Biome1_Water_02` (`13.81 × 17.04 m`, danau kawah berbatu gelap, `loc = (-76.63, 47.49, 1.46)`)
    - `SM_Biome1_Water_03` (`14.98 × 30.47 m`, `loc = (-116.90, 65.07, 1.58)`)
  - **`Marker_Desa1_Smog`:** Otomatis diposisikan di teras rumput datar tengah pulau pada `(-86.33, 74.00, 6.22)` (`z_base = 6.07 m`).
  - Ketiga pulau lainnya tetap datar identik (`SM_Island_Center`: `144.24 × 145.68 × 1.50 m`, `SM_Island_Biome2_Wildfire`: `134.37 × 191.81 × 1.50 m`, `SM_Island_Biome3_Drought`: `225.58 × 158.88 × 1.50 m`).
- **Verifikasi QA & Visual:**
  - `qa_check.py` (`profile="game"`): `SM_Island_Biome1_Smog` + `SM_Biome1_Water_01..03` lulus penuh (`pass=True`, `errors=[]`, `zfighting=[]`, `floating=[]`, `0` N-gons, `0` boundary/non-manifold edges, `0` zero-area UVs, `0.0%` UV overlap); ketiga pulau datar juga `pass=True`, `errors=[]`.
  - `scene_audit.py`: `ok=True`, `objects=17`, `issues={}`, `orphans=0`, `missing=[]`, `absolute_paths=[]`.
  - Render pratinjau tersimpan di [`biome1_migrated_topdown.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/biome1_migrated_topdown.png) (`1,136,616` B), [`biome1_migrated_persp.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/biome1_migrated_persp.png) (`1,398,356` B), [`flat_topdown.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/flat_topdown.png) (`1,613,862` B), dan [`flat_perspective.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/previews/flat_perspective.png) (`1,308,559` B), serta diverifikasi wireframe & solid shading via `look` MCP.

## 2026-11-14 — Pembuatan & Penyempurnaan Fase 1 Flat Layout Pulau *Smooth Minimalist* (`Pulau_Archipelago.blend`, Skala `400 × 400 m`)
- **Keputusan Pipeline, Skala, & Gaya Garis Pantai:**
  - Pembuatan remodel pulau dikerjakan **langsung di Blender (`5.2.3 LTS`)** tanpa perantara Roblox Studio.
  - Dimulai bertahap dari **Fase 1: Flat Layout** — menampilkan bentuk garis pantai, lebar selat pemisah, dan tata letak keempat daratan secara datar (`Z = 0.0 m` sampai `-1.50 m`) **tanpa** mesh laut, perbedaan level ketinggian (*level ground*), gunung, maupun danau.
  - **Kanvas Blender `400 × 400 m` (Rasio `1:6`):** Berdasarkan [`Peta_Denah_Resmi_Genshin_Style.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/Peta_Denah_Resmi_Genshin_Style.jpg) (`2400 × 2400 m` termasuk laut luar), skala di Blender ditetapkan **`400 × 400 m`** agar viewport ringan dan seluruh kepulauan tampak utuh di kamera/viewport. Saat diimpor ke Unity nanti, atur **`Scale = (6, 6, 6)`** untuk mengembalikan ukuran dunia asli.
  - **Refinement Garis Pantai (*Smooth Minimalist*):** Kontur awal *trace* (`approxPolyDP 2.3 px`) terlalu bergerigi/semi-realis dengan banyak lekukan mikro, sehingga diperhalus per pulau menggunakan *morphological close/open* (`29 px` / `15 px`), *cyclic Gaussian arc-length smoothing* (`sigma = 20.0 px`), dan resampling seragam `18.0 px` (`~7.0 m` per segmen di ruang Blender `400 m`) — menghilangkan gerigi tajam sambil tetap mempertahankan siluet teluk, tanjung, dan selat khas masing-masing pulau agar tidak terlalu kaku/minimalis (`Center 67v`, `Biome 1 82v`, `Biome 2 77v`, `Biome 3 93v`).
- **Implementasi di Blender MCP (`Pulau_Archipelago.blend`, `228,091` bytes):**
  - **Koleksi `COL_Island_Flat`** (4 mesh daratan datar, triangulasi seragam *Constrained Delaunay* + tepi tebing `1.50 m` bermaterial `M_Flat_CoastEdge`, skala `(1, 1, 1)`, origin `(0, 0, 0)`, bounding box gabungan daratan `332.49 × 341.38 × 1.50 m` pada `X=[-164.69, 167.80]`, `Y=[-173.22, 168.16]`):
    - `SM_Island_Center`: `144.24 × 145.68 × 1.50 m` (`574` verts, `1077` faces / `1144` tris, `M_Flat_Center`)
    - `SM_Island_Biome1_Smog`: `189.81 × 174.14 × 1.50 m` (`658` verts, `1230` faces / `1312` tris, `M_Flat_Biome1_Smog`)
    - `SM_Island_Biome2_Wildfire`: `134.37 × 191.81 × 1.50 m` (`620` verts, `1159` faces / `1236` tris, `M_Flat_Biome2_Wildfire`)
    - `SM_Island_Biome3_Drought`: `225.58 × 158.88 × 1.50 m` (`836` verts, `1575` faces / `1668` tris, `M_Flat_Biome3_Drought`)
  - **Koleksi `COL_Layout_Markers`:** Kandidat penanda datar `Marker_Spawn_Center` `(0.00, 0.00)` (jarak pantai `52.90 m`), `Marker_Desa1_Smog` `(-68.33, 70.00)` (jarak pantai `11.13 m`), `Marker_Desa2_Wildfire` `(98.33, 25.00)` (`23.31 m`), `Marker_Desa3_Drought` `(15.00, -96.67)` (`29.28 m`), serta `Ref_Dragon_Scale_5m` (proksi naga skala `1:6`, `0.60 × 0.59 × 0.26 m` menghadap Desa 1).
  - **Koleksi `COL_Reference` & `COL_Cameras_Lights`:** `Ref_Blueprint_400m` (`400 × 400 m`, disembunyikan di viewport & render, path relatif `//Peta_Denah_Resmi_Genshin_Style.jpg`), `SUN_Key`, `CAM_TopDown` (`ORTHO`, `ortho_scale 390 m`), dan `CAM_Perspective` (`PERSP`, `(-255, -330, 255)`, `clip_end 2000 m`).
- **Verifikasi QA & Visual:**
  - `qa_check.py` (`profile="game"`, per pulau): keempat pulau `pass=True`, `errors=[]`, `0` N-gons, `0` boundary/non-manifold edges, `0` zero-area UVs, dan `uv_overlap_pct = 0.0%` di **semua 4 pulau** (peringatan `ground_z = -1.5` disengaja karena datum atas di `Z = 0.0`).
  - `scene_audit.py`: `ok=True`, `issues={}`, `orphans=0`, `absolute_paths=[]`, `missing=[]`.
  - Render pratinjau tersimpan di `assets/upgrade model pulau/previews/flat_topdown.png` (`1,513,346` bytes) dan `flat_perspective.png` (`1,267,029` bytes), serta diverifikasi via `look` MCP (`wireframe` & `material`).

## 2026-11-14 — Penyeragaman Warna Outline Netral Seluruh UI (`Design` v1.5)
- **Konsistensi Outline Seluruh Artboard (`SCREEN 00–06`):** Menyeragamkan seluruh warna outline kontainer UI di [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) (`791985` bytes) agar tidak memakai outline berwarna-warni (merah, biru/cyan, hijau, oranye/kuning).
- **Aturan Token Outline Baru:**
  - Kontainer kartu, panel kalibrasi, dropdown kamera, viewport preview/PIP, badge status kamera navbar, dan kartu state di `SCREEN 06` diseragamkan ke tinta utama `#332C25`.
  - `Inner Rim` tombol tutup/jeda (`ESC` / `Close`) pada seluruh layar diubah dari `#C45148` menjadi `#A89E8B`; tombol nonaktif `Matikan` memakai `#9D9384`; dan badge di dalam viewport gelap `#1A2125` memakai `#4B565D`.
  - Pembeda 5 state kamera (`STANDBY`, `MEMUAT YOLO`, `LIVE`, `ERROR`) tetap jelas lewat *fill* badge status, dot indikator, teks panduan, dan tombol mode aktif tanpa mewarnai bingkai luar.
- **Verifikasi Visual & Dokumentasi:** Seluruh 6 artboard terdampak (`263`, `314`, `348`, `377`, `400`, `876`) telah diverifikasi lewat render ulang FigCraft (`SAVE` sukses) dan disinkronkan ke [[rencana_desain_ui]] (`v1.5`).

## 2026-11-14 — Perbaikan Geometri Sudut Kartu (*Stepped Rabbeted Plaques + Scalloped Inner Frame*, `Design` v1.4)
- **Umpan Balik Desain:** Menggantikan seluruh sudut membulat (`cornerRadius: 4..10 px`) dan *corner bracket* mengambang pada page `Design` (`id: 262`) di [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) agar sesuai penuh dengan siluet sudut kartu perkamen pada 3 referensi UI terbaru.
- **Implementasi Geometri Vektor (`Design` id `262`, 7 Artboard):**
  - **Kartu Utama & Panel Kalibrasi (`SCREEN 00, 01, 03, 04, 05`):** Dikonversi menjadi siluet vektor **Stepped Rabbeted Plaque (`a = 14, b = 7`)** bertakik 2 undakan pada 4 sudutnya (`strokeAlign: inside`, `strokeJoin: miter`), dipasangi dua bingkai dalam kontinu (*Stepped Outer Frame* `inset 10 px, #B2A895` + *Concave Scalloped Arch Frame* `inset 15 px, arch r = 18, #8D8371`, `fill: none`).
  - **Sub-Panel & Kartu Mikro:** `Victory Stat Container Box` (`560×278`), `Camera Input & AI YOLO Module` (`640×342`), dan ketujuh kartu spec `SCREEN 06` (`884..890`) memakai **Stepped Plaque (`a = 10, b = 5`)** + inner stepped trim `a = 8, b = 4`; seluruh **15 kartu pose `SCREEN 01`** (`340×122`, termasuk `Card 08 Active` dengan double stepped border `#DF9E38` + `#332C25`), 4 baris slider `SCREEN 05`, 3 baris stat `SCREEN 04`, dan plakat gestur `SCREEN 03` dikonversi ke **Stepped Plaque (`a = 8, b = 4` / `a = 6, b = 3`)**.
  - **Kontrol & Elemen Kompak (`SCREEN 00–06`):** Seluruh tombol aksi, badge, tab navbar, dropdown, viewport preview, box bintang, padlock, dan nilai stat diset seragam ke sudut tajam `r = 2 px` (`r = 3 px` untuk container navbar/HUD kapsul `SCREEN 02`, `r = 1 px` untuk crimson inner rim `ESC`/`Lock Inner Rim`), disertai pembaruan legenda token di `SCREEN 06` (`1019`).
- **Verifikasi & Artefak:** Seluruh 7 artboard diverifikasi ulang via screenshot FigCraft (`steps/954`–`957`, `966`–`967`, `977`), dokumen disinkronkan ke [[rencana_desain_ui]] (`v1.4`), dan [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) tersimpan bersih (`792157` bytes).

## 2026-11-14 — Penyempurnaan Gaya Visual *Fantasy Parchment-and-Ink Tabletop UI* Seluruh 7 Artboard (`Design` v1.3)
- **Tujuan:** Menyelaraskan seluruh 7 artboard di halaman `Design` (`id: 262`) pada [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) agar mendekati kualitas kriya tiga referensi RPG kartu perkamen terbaru (`r = 4..10 px` chamfered plaques, bingkai tinta `#332C25` + hard cast shadow nol-blur, spanduk heksagonal runcing berornamen diamond *fleuron*, tag speech-bubble emas `#DF9E38`, rel tab bookmark `#CFC6B4` + `#E5AD44`, panel stat resessif `#DDD5C5`/`#C7BDAA`, dan tombol taktil `#5C8D46` dengan highlight *sheen* & takik ganda kiri).
- **Perbaikan Teknis & Visual per Artboard (`Design` id `262`):**
  - **Fix Artifact & Z-Order Global:** Membersihkan default fill `#D9D9D9` (`fill: "none"`) pada seluruh vektor terbuka (*stepped corners*, *X stroke*, *pause bars*, *vitality ticks*, *dragon wing ribs*), mengganti transparansi `#FBF6EAE6` pada `Dragon Telemetry Bar` (`687`) menjadi solid 100% opaque `#F2EFE7`, serta memisahkan `Card Surface BG` ke `index: 0` sehingga seluruh spanduk/ribbon bertumpuk (`01–03`, `CAMERA MIRROR · TRACKING`, `LIVE DRAGON PREVIEW`, `STAGE COMPLETE`, `SETTINGS & AUDIO CONTROLS`) menindih bersih garis batas atas kartu tanpa garis potong.
  - **`SCREEN 00` (`id: 263`):** Kartu biome `r = 10` + stepped corners, banner nama level runcing dengan *fleuron*, strip subtitle `#C7BDAA`, tag speech-bubble emas `90s`/`150s`, tombol `PLAY`/`SELECT` chamfered `r = 6`, box `LOCKED`, serta tombol `ESC · QUIT` krem-merah `#F3EFE6`/`#D15046`.
  - **`SCREEN 01` (`id: 314`):** Kedua panel kalibrasi memakai overlapping header plaque heksagonal sehingga status `LIVE · JENDELA PREVIEW` + `Matikan` tidak berdesakan dengan judul, ke-15 kartu pose & medallion diseragamkan ke `r = 6..8`, pil bawah `ARM GATE RULE` & `SEATED & SAFETY` serta CTA `READY TO FLY` beralih ke `r = 6`.
  - **`SCREEN 02` (`id: 348`) & `SCREEN 03` (`id: 365`):** Tetap mempertahankan latar putih bersih `#FFFFFF` (`> 85%` ruang gameplay terbuka di `SCREEN 02`), penyeragaman kapsul timer/waypoint/telemetri/`PIP Camera Control Header`/`Webcam PIP Monitor` ke `r = 6`, ditambah baroque *scrollwork flourish* & spanduk heksagonal `VILLAGE 2: WILDFIRE WOODS` + plakat instruksi resessif `#DDD5C5` pada `SCREEN 03`.
  - **`SCREEN 04` (`id: 377`) & `SCREEN 05` (`id: 400`):** Baroque scrollwork crest di atas kartu modal, spanduk heksagonal bertumpuk `STAGE COMPLETE` / `SETTINGS & AUDIO CONTROLS`, kotak stat resessif `#DDD5C5` + pill nilai `#C7BDAA`, 4 baris slider `Master/BGM/SFX/Sensitivity` yang diselaraskan presisi antara bar fill dan knob, modul kamera standby `r = 6`, serta `APPLY & CLOSE` / `NEXT LEVEL` / `REPLAY` `r = 6`.
  - **`SCREEN 06` (`id: 876`):** Banner spesifikasi heksagonal ber-*fleuron*, tombol `ESC · BACK` dengan rim crimson & X vektor putih bersih, ke-5 kartu state kamera + dropdown popup solid `#FBF6EB` + token badge navbar dinamis diseragamkan ke `r = 4..8`, plus pembaruan legenda palet token `v1.3`.
- **Verifikasi & Artefak:** Seluruh 7 artboard diinspeksi lewat tangkapan layar render FigCraft, file disimpan (`UI AR Dragon.figcraft`, `730169` bytes), dan spesifikasi disinkronkan di [[rencana_desain_ui]] (`v1.3`).

## 2026-11-14 — Pembaruan Desain UI Kontrol Kamera & Aktivasi Dual-Mode YOLO (FigCraft + Spec v1.2)
- **Konteks Integrasi (`laptop-b`):** Menyelaraskan spesifikasi UI dan artboard FigCraft dengan sistem kontrol kamera manual (`autoLaunchPython = false`, dropdown unselected default `"Pilih Input Kamera... ▼"`, tombol `Scan` kamera DirectShow, pemisahan mode `Latar Belakang` / `Jendela Preview`, tombol `Matikan`, serta popup dropdown 100% solid `alpha = 1.0f`).
- **Pembaruan [[rencana_desain_ui]] (`v1.2`):**
  - **SCREEN 00 (Lobby/Missions):** Badge navbar kanan atas dinamis (`● STANDBY · PILIH KAMERA` vs `● CAM READY · LATAR BELAKANG` / `● CAM READY · JENDELA PREVIEW`), tombol `ESC · QUIT` konsisten di kanan atas, serta rujukan 3 ilustrasi biome (`UI_Biome_01_Smog_Valley.jpg`, `UI_Biome_02_Wildfire.jpg`, `UI_Biome_03_Drought.jpg`).
  - **SCREEN 01 (Calibration):** Penambahan status pill (`● LIVE · JENDELA PREVIEW`) + tombol `Matikan` + baris selector (`[6] OBS Virtual Camera ▼`, `Scan`, `Latar Belakang`, `● Jendela Preview`) pada panel kiri kamera mirror.
  - **SCREEN 02 (In-Game Flight HUD):** Penambahan `PIP Camera Control Header` solid `#FBF6EB` (`420×112`) tepat di atas `Webcam PIP Monitor` 16:9 (`420×236`), tetap menjaga `> 85%` ruang pandang gameplay bebas halangan.
  - **SCREEN 05 (Settings Modal):** Perluasan container menjadi 4 slider + modul `Camera Input & AI YOLO Module (Standby)` dengan default `"Pilih Input Kamera... ▼"`, `Scan`, tombol mode disabled-until-selected, dan tombol `Matikan`.
  - **SCREEN 06 (Spec Sheet Baru — `1920×1080` di `x: 0, y: 2400`):** Dokumentasi visual **5 State Panel Kamera** (`STANDBY Belum Pilih`, `STANDBY Terpilih`, `MEMUAT YOLO`, `LIVE`, `ERROR`), simulasi **popup dropdown 100% solid `#FBF6EB`** anti tembus teks, serta **3 varian badge navbar `SCREEN 00`**.
- **Artefak Tersimpan:** [`UI AR Dragon.figcraft`](file:///e:/Semester%205/Game/AR%20Dragon/UI%20AR%20Dragon.figcraft) (`673172` bytes — page `Wireframe` & `Design`) dan [`rencana_desain_ui.md`](file:///e:/Semester%205/Game/AR%20Dragon/brain/laptop-a/rencana_desain_ui.md).

## 2026-11-14 — Pemasangan Ilustrasi Masalah Biome pada 3 Kartu Level Main Menu (`Design`)
- **Aksi:**
  - Menyimpan 3 aset ilustrasi raster `16:9` *cel-shaded low-poly* hasil generasi prompt dari `referensi/user upload` ke `Referensi UI/`:
    - `Referensi UI/UI_Biome_01_Smog_Valley.jpg` (`144802` bytes) — lembah hijau & danau, rumah desa berdinding krem/atap terakota, serta cerobong batu mengepulkan asap kabut abu-abu gelap.
    - `Referensi UI/UI_Biome_02_Wildfire.jpg` (`141454` bytes) — perbukitan pinus & desa berhadapan dengan tanah hangus hitam dan kobaran api merah-oranye.
    - `Referensi UI/UI_Biome_03_Drought.jpg` (`156588` bytes) — dataran tanah retak kering, danau surut, pepohonan kering tanpa daun, dan desa kecil di tengah kekeringan.
  - Mengganti seluruh placeholder vektor di halaman `Design` -> `SCREEN 00 - Main Menu & Level Selection` (`id: 263`) pada kotak karya `380 × 210 px` (`r = 12`, `clip: true`, bingkai tinta `#2D2926` / `#4A443C`) masing-masing menjadi `Biome Art Box - Smog Valley` (`277`), `Biome Art Box - Wildfire` (`292`), dan `Biome Art Box - Drought` (`307`), dengan mempertahankan layer `Locked Scrim` (`467`) di atas gambar *Drought* agar status terkunci tetap jelas secara visual.
  - Diverifikasi melalui tangkapan layar kanvas FigCraft (`1600×900`) dan disimpan ke `E:\Semester 5\Game\AR Dragon\UI AR Dragon.figcraft` (`603592` bytes).

## 2026-11-14 — Perbaikan Halaman `Calibration` (Page `Design`) Selaras Mekanisme Nyata Laptop B (Ekspansi 15 Kartu Pose)
- **Konteks Audit (`brain/laptop-b/`):** Halaman Calibration pada page `Design` (`SCREEN 01`, `id: 314`) sebelumnya masih memakai tutorial lama `Pose Y (Climb) / Pose T (Cruise) / Pose A (Dive)`, kemudian setelah diperbarui menjadi 4 kartu tetap belum mencakup seluruh gestur nyata di [[laptop-b/konteks]] (v6.0 & v6.1) dan [[laptop-b/prd]] (termasuk *Grounded Idle* berdiri tegak diam di tempat, *Grounded Walk*, *Grounded Look Up*, *Takeoff*, *Fly Up*, *Hover*, *Gentle Descent*, *Pose Y*, *Pose W*, *Tuck Dive*, *Lean & Arm Tilt*, *Propeller Dash*, dan gestur 3 desa).
- **Perbaikan pada `UI AR Dragon.figcraft` (`id: 314`) & [[rencana_desain_ui]] (Bagian 2.2):**
  - **Panel Atas Kompak (`860 × 410 px` pada `y = 114`) untuk Ruang Grid Penuh:** Menata *Camera Mirror* (`532 × 294` + sidebar HUD kalibrasi di kanan dalam viewport gelap) dan *Live Dragon Preview* (skala proporsional `0.62×` di pedestal + bar telemetri atas), tanpa memotong tombol pemicu UDP `5054` (`RECALIBRATE (C)`, `RESET DEFAULT`) dan pil status `GROUND: WALK 3.5 m/s · LEAN: 0°`.
  - **Matriks Lengkap 15 Kartu Pose & Mekanik (Grid `3 × 5`, `340 × 122 px` per kartu):**
    - **Baris 1 (Ground, Transisi, & Kemudi):** `GROUNDED IDLE` (berdiri/duduk tegak lurus tangan rapat di sisi -> diam total `0 m/s`), `GROUNDED WALK` (*arms away* `3.5 m/s`), `GROUNDED LOOK UP` (angkat dada/tilt tanpa kepak -> mendongak `0 m/s`), `TAKEOFF` (kepakan kuat di darat `>=0.25s`), dan `LEAN & ARM TILT` (kemudi 50/50 torso + lengan di darat/udara, dukung mode duduk).
    - **Baris 2 (Penerbangan Udara Inti):** `FLAP TO CLIMB` (`12–19.5 m/s`), `FLY UP` (angkat dada + kepak `+19°`), `GLIDE (T-POSE)` `[ACTIVE]` (`14 m/s` + *Swoop Climb* otomatis paska-dive), `HOVER MODE` (lengan atas rapat + kepak lengan bawah `0 m/s`), dan `GENTLE DESCENT` (lengan atas rapat diam `8.5 m/s`, sink `-1.6 m/s`).
    - **Baris 3 (Turun Presisi, Tukik, Boost, & Desa):** `FLY DOWN (POSE Y)` (*arms away + forearms up*, maju `12 m/s`, `-5.5 m/s`), `FLY DOWN IDLE (POSE W)` (*arms tucked + forearms up*, vertikal `0 m/s`, `-3.2 m/s`), `TUCK TO DIVE` (tangan merapat di dada `28 m/s`), `PROPELLER DASH` (putar kedua lengan melingkar `26 m/s`), dan `VILLAGE ACTIONS` (Smog: *Flap*, Fire: *Open mouth / Clasp chest*, Drought: *Hands high overhead*).
  - **Bar Aturan Inti & CTA Bawah:** 2 pil ringkas di kiri `READY TO FLY`: `ARM GATE RULE` (ringkasan logika terpadu *Tucked* vs *Away*) dan `SEATED & SAFETY` (dukungan *Virtual Hips* mode duduk & perilaku aman *Tracking Loss*).
  - Disimpan ke `E:\Semester 5\Game\AR Dragon\UI AR Dragon.figcraft` (`161309` bytes).

## 2026-11-14 — Implementasi Desain Visual 6 Layar UI pada Page `Design` di FigCraft
- **Aksi:**
  - Menduplikasi page `Wireframe` (`id: 1`) menjadi page baru bernama `Design` (`id: 262`) pada dokumen `UI AR Dragon` (`E:\Semester 5\Game\AR Dragon\UI AR Dragon.figcraft`) dengan mempertahankan posisi & proporsi layout wireframe 100%.
  - Menerapkan bahasa visual dari 3 referensi RPG *parchment-stone* user: backdrop `BG_Island_Aerial_View.jpg` khusus untuk 3 layar menu (`Main Menu`, `Calibration`, `Settings`) dengan header teks tinta gelap `#2D2926`, serta latar belakang blank putih bersih (`#FFFFFF`) pada 3 layar *in-game* (`In-Game Flight HUD`, `Village Challenge Overlay`, `Finish UI Victory Modal`) karena di Unity akan menampilkan viewport game secara langsung.
  - Komponen UI memakai kartu krem parchment (`#EFE9DD`) berbingkai tinta gelap `#2D2926`, *double-line inner border* + ornamen sudut vektor, banner judul berornamen diamond filigree, tab navbar aktif bernuansa emas (`#E0A23B`), CTA primer hijau 3D-bevel (`#5E8E49`) dengan notch kiri, tombol sekunder `#E5DBC7`, tombol `ESC` krem-merah konsisten di kanan atas, kotak bintang gradien vektor, gembok batu gelap di level terkunci, slider vektor dengan ikon medallion, serta ilustrasi vektor 2D untuk 3 biome, skeleton kalibrasi, pratinjau naga, PIP monitor, dan *water breath challenge*.
  - Menyimpan hasil ke `E:\Semester 5\Game\AR Dragon\UI AR Dragon.figcraft`.

## 2026-11-13 — Klarifikasi Status Final Remodel Model Naga (`remodel naga v3/Dragon_Model.blend`)
- **Aksi:** Mengonfirmasi bahwa remodel model naga 3D telah selesai pada `remodel naga v3/Dragon_Model.blend` ([[Pembuatan Model Naga 3D v3]]), sehingga item tugas remodel lama (`assets/Naga V2 Tes 2.blend`) dihapus dari daftar fokus aktif di [[Tasks]].

## 2026-11-13 — Simplifikasi Nama File Memori & Update Aturan Penamaan
- **Aksi:** Menyederhanakan seluruh 12 nama file di `brain/laptop-a/memories/` menjadi judul ringkas (3–6 kata) agar mudah dibaca di Obsidian, memperbarui seluruh `[[wikilinks]]` di [[Devlog]] dan [[Tasks]], serta menyempurnakan aturan penamaan pada command `"jadikan sesi ini memori"` di [[00 - Overview]].

## 2026-11-13 — Penambahan Command Bersama `"jadikan sesi ini memori"`
- **Aksi:** Menambahkan spesifikasi command `"jadikan sesi ini memori"` di [[00 - Overview]] (dan rujukannya di `AGENTS.md` Laptop A) agar baik Laptop A maupun Laptop B otomatis mengenali perintah tersebut, menyimpan catatan sesi deskriptif di `memories/` masing-masing, memperbarui `Devlog.md`/`Tasks.md`, serta melakukan `commit` dan `push` ke `origin/main`.

## 2026-11-13 — Dokumentasi Sesi Remodel Naga 3D v3 ([[Pembuatan Model Naga 3D v3]])
- **Aksi:** Menyusun catatan memori terstruktur [[Pembuatan Model Naga 3D v3]] yang merekam seluruh iterasi pembuatan `remodel naga v3/Dragon_Model.blend` dari 0, transfer kepala v1/v2 (`0.685×`), perbaikan sayap, transisi $C^2$ body–ekor (`24` vertex/cincin), *caudal leaf fin*, gem dada *pear brilliant-cut* (`+21.5°` X-rot), orientasi spike kepala/punggung, penghapusan 2 spike leher atas, serta hasil verifikasi `qa_check.py`.

## 2026-11-13 — Dokumentasi Sesi Perancangan UI, Wireframe FigCraft, & Denah Pulau 3 Biome ([[Desain UI dan Denah Pulau 3 Biome]])
- **Aksi:** Menyusun catatan memori terstruktur [[Desain UI dan Denah Pulau 3 Biome]] yang merangkum penggabungan `konteks.md`/`prd.md` v4.0, penguncian aset `Referensi UI/` (8 file: Lobby, Kalibrasi, Flight HUD, Background, serta Wireframe Finish & Village Action), penguncian cetak biru peta 1:1 `assets/upgrade model pulau/Peta_Denah_Resmi_Genshin_Style.jpg` (*Tight-Knit Caldera Archipelago* $1200 \times 1200\text{ m}$), serta penyusunan 1 prompt wireframe tunggal FigCraft untuk 5 layar tersisa.

## 2026-11-13 — Dokumentasi Sesi Eksperimen Daratan Pulau di Roblox Studio ([[Eksperimen Terrain Pulau Roblox]])
- **Aksi:** Menyusun catatan memori terstruktur [[Eksperimen Terrain Pulau Roblox]] yang merekam iterasi pembuatan peta pulau `1600 × 1600` studs di Roblox Studio (mulai dari 9 formasi plateau blueprint, part & `WedgePart`/`CornerWedgePart` berorientasi diagonal, hingga Smooth Terrain prosedural `WriteVoxels` 16 chunk bebas *seam* tanpa bukit/danau) serta pembersihan total (*clean slate*) di akhir sesi untuk diskusi ulang dari awal.

## 2026-11-13 — Dokumentasi Sesi Blender MCP Pembuatan Daratan 800×800m ([[Generasi Daratan Blender Tahap 1]])
- **Aksi:** Mengarsipkan seluruh hasil sesi 2026-11-08 ke [[Generasi Daratan Blender Tahap 1]] (verifikasi koneksi Blender MCP `5.2.3 LTS`, analisis 3 referensi terrain, identifikasi 3 kontradiksi spesifikasi, serta eksekusi Tahap 1 `island_gen.py` menjadi `Island_v01.blend` [`HEIGHT_MULT = 1.0`] dan `Island_v02.blend` [`HEIGHT_MULT = 3.0`] yang keduanya lolos 14/14 cek `validate()`).

## 2026-11-13 — Konfigurasi & Integrasi MCP Server FigCraft ([[Integrasi MCP FigCraft]])
- **Aksi:** Mengonfigurasi server MCP `figcraft` (`E:\ArtCraft\Apps\FigCraft\FigCraft\figcraft-cli.exe` dengan argumen `["mcp"]`) di `C:\Users\nmast\.gemini\config\mcp_config.json`, mengekspor `instructions.md` serta 19 skema JSON tool ke `C:\Users\nmast\.gemini\antigravity\mcp\figcraft\`, dan mendokumentasikan detailnya di [[Integrasi MCP FigCraft]].

## 2026-11-13 — Iterasi Kaki, Eksperimen & Revert Body/Sayap Naga V2, dan Eksekusi `generate_dragon.py` ([[Iterasi Naga v2 dan Eksekusi Generator]])
- **Aksi:** Menyusun catatan memori terstruktur [[Iterasi Naga v2 dan Eksekusi Generator]] yang merekam perbaikan mesh `kaki` & `kaki2` pada `assets/Naga V2 Tes 2.blend`, pembuatan prompt Hermes (analisis referensi dengan `antigravity`/`gemini flash` + pemodelan Blender dengan `opus 4.6`), eksperimen dan *revert* bersih pada *body* (`Naga V2 Tes 2_backup_before_body.blend`) serta sayap (`Naga V2 Tes 2_backup_before_wings.blend`), hingga perbaikan `SyntaxError` dan eksekusi `generate_dragon.py` ke `assets/AncientDewWaterDragon_Generated.blend`.

## 2026-11-13 — Setup FigCraft MCP Live Control & Wireframe Kit 6 Layar ([[Wireframe 6 Layar UI di FigCraft]])
- **Aksi:** Mengonfigurasi FigCraft MCP (`--attach 9877`, shortcut `FigCraft (MCP Control)`), membangun 6 artboard wireframe 16:9 (`Main Menu & Level Selection`, `Calibration`, `In-Game Flight HUD`, `Village Challenge Overlay`, `Finish Victory Modal`, `Settings Modal`) dalam grid 2×3 secara live via socket kontrol, serta menyeragamkan seluruh tombol `ESC` ke sudut kanan atas ([[Wireframe 6 Layar UI di FigCraft]]).

## 2026-11-13 — Pengecekan Blender MCP & Eksekusi `generate_dragon.py` ([[Perbaikan dan Eksekusi generate_dragon]])
- **Aksi:** Memverifikasi koneksi Blender MCP (`Blender 5.2.3 LTS`, Addon `v1.8`), memperbaiki *SyntaxError* (*unterminated string literal* pada fungsi `export_standalone_obj()`) di `generate_dragon.py`, serta mengeksekusi skrip di Blender hingga menghasilkan 51 mesh komponen naga dalam koleksi `AncientDewWaterDragon` ([[Perbaikan dan Eksekusi generate_dragon]]).

## 2026-11-13 — Dokumentasi Sesi Setup Output Remodel Naga v2 ([[Setup Output Remodel Naga v2]])
- **Aksi:** Menyusun catatan memori terstruktur [[Setup Output Remodel Naga v2]] yang merekam pembuatan struktur direktori `remodel naga v2/` (`Dragon_Model.blend`, `renders/` 5 sudut pandang, `previews/` workbench & contact sheet, `textures/README.txt`), perbaikan orientasi kamera `side`/`back`, serta verifikasi reopen headless Blender 5.2.3 LTS tanpa file tercecer atau path absolut.

## 2026-11-13 — Dokumentasi Sesi Setup Blender MCP, Skill Modeling, & Konsep Pulau Sederhana ([[Setup Blender MCP dan Konsep Pulau]])
- **Aksi:** Menyusun catatan memori terstruktur [[Setup Blender MCP dan Konsep Pulau]] yang merekam konfigurasi `mcp-for-blender` (Blender 5.2.3 LTS, port 9876), instalasi 28 skill `EL4CTEO/blender-skills` di `.agents/skills/`, iterasi awal aset `Assets/pulau_sky_guardian.blend`/`.fbx`, serta keputusan penyederhanaan pulau (fokus pada elemen tantangan: sungai, hutan, cerobong asap, lahan tandus `#8B5A2B`, tanpa air terjun/pulau satelit) beserta prompt referensi gambar 3D-nya.

## 2026-11-13 — Dokumentasi Sesi Blockout Roblox Studio & Penataan Folder Proyek
- **Aksi:** Mencatat seluruh progres eksplorasi *blockout* pulau melayang (*Sky Guardian Island*) di Roblox Studio serta penataan struktur folder proyek (`assets/` dan `misc/`) ke dalam [[Blockout Pulau Roblox dan Penataan Folder]].

## 2026-11-13 — Penamaan Ulang Catatan Sesi ke Format Deskriptif
- **Aksi:** Mengganti nama `Memory1.md` menjadi [[Harmonisasi List Asset dan Time Schedule]] di `brain/laptop-a/memories/` agar seluruh catatan sesi langsung menggambarkan isi pembahasannya tanpa awalan `Memory*`.

## 2026-11-13 — Dokumentasi Sesi List Asset & Jadwal TS ([[Harmonisasi List Asset dan Time Schedule]])
- **Aksi:** Menyusun catatan memori terstruktur [[Harmonisasi List Asset dan Time Schedule]] yang mendokumentasikan perbaikan workbook `TS & List Asset.xlsx` (penyelarasan ke Versi 2 yang ramping sesuai TS, penguncian jadwal VFX Particles pekan 10–11, penataan arsip detail aset di sheet pendukung, dan penyusunan prompt Blender MCP untuk model pulau).

## 2026-11-13 — Pembersihan Dokumen Lama (`brain/laptop-a/`)
- **Aksi:** Menghapus 3 dokumen `.md` usang/sementara dari `brain/laptop-a/`:
  - `prompt_naga.md` (remodel naga v3 sudah selesai di `Dragon_Model.blend`).
  - `prompt_v2.md` (prompt daratan lama 800×800m sudah tidak dipakai).
  - `gdd_peta_pulau.md` (dihapus untuk dibuat ulang sesuai konsep baru Kepulauan 3 Biome $1200 \times 1200\text{ m}$).
- **Dokumen Aktif Tersisa:** [[00 - Overview]], [[konteks]], [[prd]], [[rencana_desain_ui]], [[Tasks]], dan [[Devlog]].

## 2026-11-13 — Setup Obsidian Brain & Migrasi Dokumen Desain
- **Aksi:** Menginisialisasi Obsidian vault terpisah di `brain/` yang terhubung ke remote `ARDragon-Brain` dan terisolasi dari repo utama melalui `.gitignore`.
- **Migrasi:** Memindahkan dokumen desain dari root ke `brain/laptop-a/` dengan format YAML frontmatter & `[[wikilinks]]` (termasuk [[konteks]], [[prd]], [[rencana_desain_ui]], serta file lama `gdd_peta_pulau.md`, `prompt_naga.md`, dan `prompt_v2.md` yang kemudian dibersihkan).
- **Aturan Kerja:** Menetapkan pemisahan tulis (*anti-collision*) antara `laptop-a/` (desain & arsitektur) dan `laptop-b/` (eksekusi & pengembangan program) di `AGENTS.md`.
- **Status Project Saat Ini:** Fokus aktif roadmap berada pada **Sesi GUI** (implementasi di Unity sesuai [[rencana_desain_ui]]), disusul **Sesi Model Pulau** (remodel kepulauan 3 biome).
