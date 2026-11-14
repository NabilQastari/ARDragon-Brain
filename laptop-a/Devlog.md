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
