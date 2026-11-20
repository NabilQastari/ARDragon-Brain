---
title: Perencanaan Revisi Mekanisme Momentum, Y-Glide Climb, Chin-Up Flap, & Hover L-Flap
type: specification
tags:
  - flight-physics
  - momentum
  - computer-vision
  - ui-hud
  - laptop-a
  - laptop-b
  - synchronization
date: 2026-11-18
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[panduan_detail_pose_dan_sync_laptop_b]]"
  - "[[kontrak_integrasi_ui_dan_progress]]"
  - "[[planning_revisi_titik_wajah_dan_pov]]"
---

# 🚀 Revisi Mekanisme Penerbangan: Sistem Momentum Terpadu & Penyelarasan Kontrol (`v4.0`)

> **Dokumen Acuan Visual:**
> - **Buku Panduan Resmi (19 Pose + Momentum):** [`Panduan_19_Pose_Fanamir_CV.pdf`](file:///e:/Semester%205/Game/AR%20Dragon/Panduan_19_Pose_Fanamir_CV.pdf) & [`.docx`](file:///e:/Semester%205/Game/AR%20Dragon/Panduan_19_Pose_Fanamir_CV.docx)
> - **Aset Vektor CV (`19` kartu + sheet):** [`assets/poses_cv_vector/`](file:///e:/Semester%205/Game/AR%20Dragon/assets/poses_cv_vector/) (baru: `Pose_06B_SteepClimb_ChinUpFlap.png`, `Pose_09B_SlowGlide_LPose.png`, `All_19_Poses_Sheet.png`, serta pembaruan `Pose_09_Hover.png`)
> - **Aset UI HUD & Pose Guide Bersih (Unity 6):**
>   - Baru: `UI_HUD_Momentum_Track_206x20.png` & `UI_HUD_Momentum_Fill_200x14.png` di [`assets/UI_Unity_Export/Components/05_HUD/`](file:///e:/Semester%205/Game/AR%20Dragon/assets/UI_Unity_Export/Components/05_HUD/)
>   - Terbarui: [`UI_Pose_09_HoverMode.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/UI_Unity_Export/Components/06_Poses/UI_Pose_09_HoverMode.png), [`01_Calibration_MotionTutorial.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/UI_Unity_Export/Designs/01_Calibration_MotionTutorial.png), [`02_InGame_FlightHUD.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/UI_Unity_Export/Designs/02_InGame_FlightHUD.png), [`07_PoseGuide_OverlayModal.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/UI_Unity_Export/Designs/07_PoseGuide_OverlayModal.png)

---

## 1. Filosofi & Hukum Fisika Momentum Terpadu

Revisi ini menggabungkan kecepatan pose yang sebelumnya terpisah-pisah ke dalam satu variabel kontinu **`Momentum` (`0.0` s/d `1.0`, ditampilkan `0%–100%`)** di `DragonFlightController.cs`:

1. **Hukum Inersia Tunggal:** Selama `Momentum > 0%`, naga **terus terdorong maju** — mengerem tidak langsung berhenti `0 m/s` seketika, melainkan **melambat secara perlahan** hingga momentum habis.
2. **Penambahan Berangsur (*Gradual Accumulation*):** Tidak ada pose yang melompat instan `+Sekian%`. Semua penambahan diintegrasikan bertahap per frame (`Time.deltaTime`), per kepakan, atau per siklus putaran propeler (`360°`).
3. **Aturan *Soft Cap* (Anti-Pemangkasan):** Pose biasa memiliki limit penambahan (mis. `#06 Flap` mentok `30%`), namun jika momentum saat ini sudah lebih tinggi dari limit tersebut (mis. `80%` pasca *Dive*/*Propeller*), pose tersebut **TIDAK memotong momentum ke bawah** — hanya berhenti menambah dan membiarkannya meluruh alami.
4. **Pengisi `100%` Hanya Lewat Pengulangan (`#13` & `#14`):** `#13 Tuck to Dive` dan `#14 Propeller Dash` dapat mencapai `100%`, tetapi lajunya bertahap sehingga pemain harus melakukannya **berkali-kali** (mis. siklus *Dive $\rightarrow$ Glide $\rightarrow$ Dive* atau beberapa sesi putaran *Propeller*).

---

## 2. Tabel Parameter Fisika & Sayap 3D per Pose (`DragonLocomotionConfig`)

| Pose | Syarat Pose & CV | Limit Cap Momentum | Laju Momentum (`/dtk` atau `/siklus`) | Kecepatan Maju (`Forward`) | Gerak Vertikal (`Vy`) | Animasi Sayap 3D (`DragonWingAnimator`) |
| :--- | :--- | :---: | :--- | :--- | :--- | :--- |
| **`#01` Grounded Idle** | Arm Gate Tucked di darat | Reset **`0%`** | Decay instan saat mendarat diam (`-100%/s`) | `0 m/s` | `0 m/s` | Terlipat rapi di samping tubuh |
| **`#04` Takeoff** | Kepak kuat dari darat | **`15%`** | `+18%/s` selama fase angkat (~`0.7s`) | Transisi awal `~8–10 m/s` | `+7m` lift | Kepak kuat naik-turun |
| **`#06` Flap to Climb** *(Dagu Netral)* | Kepak aktif + `HEAD_PITCH <= +0.12` | **`30%`** | **`+6%`** per kepak penuh | `14–19.5 m/s` (atau ikut sisa momentum) | Naik **landai** `+2.5..+3.2 m/s` (`pitch +8°`) | Kepak ritmis datar/ringan |
| **`#06B` Steep Climb** *(Chin-Up Flap)* | Kepak aktif + **`HEAD_PITCH > +0.12`** | **`20%`** | **`+5%`** per kepak penuh *(tanpa butuh momentum!)* | `10–13 m/s` (ikut sisa momentum bila ada) | Naik **jauh lebih curam** `+6.5..+8.0 m/s` (`pitch +24°`) | Kepala/tubuh mendongak curam + kepak kuat |
| **`#07` Fly Up** *(Y-Pose)* | Open Y-Pose (`~45°` di atas bahu) | **Butuh `> 0%`** | **Konsumsi `-25%/s`** (*zoom climb*) | Mengikuti sisa momentum (`14–22 m/s`) | **Saat `>0%`: naik curam `+7..+9.5 m/s`**. Saat `0%`: *gentle stall* melandai $\rightarrow$ turun `-1.2 m/s` | **Sayap 3D terangkat membentuk huruf Y!** |
| **`#08` Glide** *(T-Pose)* | Horizontal `180°` | Bawa sisa | **Drag sangat rendah (`-3.5%/s`)** | **`14 m/s` (`0%`) $\rightarrow$ `27 m/s` (`100%`)** | Datar (`-0.3 m/s` di `0%`); Swoop pasca Dive | Bentang lebar datar T-shape |
| **`#09A` Hover Active** *(L-Pose + Flutter)* | L-Pose `90°` + **kepak lengan bawah** | **Air Brake `-> 0%`** | **Rem bertahap `-40%/s`** (`~2.5s` dari `100%`) | Selama `>0%` tetap maju melambat (`-> 0 m/s`); **diam `0 m/s` saat `0%`** | Bertahan mengambang (`0 m/s`) | Tekuk stabil + kepak ujung sayap cepat |
| **`#09B` Slow Glide** *(L-Pose Hold)* | Tahan L-Pose `90°` **tanpa kepak** | Bawa sisa | **Drag ringan `-10%/s`** | **Slow Glide dasar `~6–8 m/s`** + sisa momentum melambat | Turun sangat ringan `-0.6 m/s` | Sayap setengah terlipat kompak (*compact glide*) |
| **`#10` Gentle Descent** | Low-A Pose (`~45°` bawah) | **Rem lembut `-> 0%`** | **`-20%/s`** | Menuju `8.5 m/s` + sisa momentum melambat | Turun terkontrol `-1.6 m/s` | Menguncup A-rendah |
| **`#11` Fly Down (Y-Dive)** | Y-Dive bersiku | **`55%`** | **`+12%/s`** | `12 m/s` + bonus momentum | Menukik diagonal `-5.5 m/s` | Y-dive bersiku |
| **`#12` Fly Down Idle (W)** | W-Pose (`siku pinggang`) | **Rem aktif `-> 0%`** | **`-40%/s`** | Selama `>0%` masih terseret maju melambat; **`0 m/s` saat `0%`** | Turun vertikal `-3.2 m/s` | Tekuk W menahan udara |
| **`#13` Tuck to Dive** | Lipat lengan rapat di dada | **`100%`** | **`+16%/s`** *(perlu tukik panjang / berkali-kali)* | Menukik hingga `28 m/s` | Menukik tajam `-45°` | Lipat rapat aerodinamis |
| **`#14` Propeller Dash** | Lengan atas `180°` + putar lengan bawah | **`100%`** | **`+7%` per putaran `360°`** *(cap `~18%/s`, perlu berkali-kali)* | Dorongan burst hingga `26 m/s` | Ikut head pitch ringan | Vortex / getar akselerasi sayap |

> **Tambahan `VillageZoneController` (Auto-Hover Desa):** Saat pemain memasuki radius narasi/tantangan desa (`R = 35m`) dengan momentum tinggi, *Auto-Hover* desa menerapkan *assisted braking* lembut (`-85%/s`, `~0.8–1.2 detik` berhenti di titik fokus) agar naga tidak terpental keluar lingkaran sebelum dialog/tantangan mulai.

---

## 3. Penyesuaian Python CV (`ARDragonSkyGuardian_CV/`)

1. **Proteksi 6 Pose Terkunci (`#01, #02, #04, #05, #08, #13`):** **DILARANG merusak** threshold maupun akurasi ke-6 pose ini (`100% zero regression`).
2. **Pemisahan `#09A Hover Active (L-Flap)` vs `#09B Slow Glide (L-Hold)` (`motion_recognizer.py` & UDP):**
   - `HoverClassifier` tetap mendeteksi bentuk **L-Pose (`55°–130°`, upper arm tucked)**, namun menghasilkan **dua status / flag terpisah** (bisa lewat nilai `HoverPower` kontinu `0.0–1.0` atau flag `IsHoverFlapping`):
     - **L-Pose + kepak lengan bawah (`forearm_vert_speed >= 0.25`)** $\rightarrow$ `#09A Hover Active` (`HoverPower = 1.0`).
     - **L-Pose diam (`forearm_vert_speed < 0.20`)** $\rightarrow$ `#09B Slow Glide` (mis. kirim flag/substate Slow Glide atau `HoverPower` rendah `~0.25` sehingga Unity mengenali L-Pose diam = *Slow Glide*, L-Pose flutter = *Active Hover Brake*).
3. **Dekoupling `#07 Fly Up (Y-Pose)` dari `HEAD_PITCH` Tunggal:**
   - Hapus pemicu `IsFlyUp` yang hanya mengandalkan `HeadPitch > 0.12f` saat tangan sedang turun/biasa!
   - **`#07 Fly Up`** hanya aktif oleh bentuk **Open Y-Pose** di Python CV / Unity (`IsFlyUp`).
   - **`HEAD_PITCH > +0.12f` + `IsFlapping`** dipetakan bersih di `DragonFlightController.cs` sebagai **`#06B Steep Climb Flap`** (menanjak jauh lebih curam daripada `#06` tanpa perlu momentum), sementara **`HEAD_PITCH > +0.12f` sendirian** di udara hanya mendongakkan kepala `DragonHeadLookController` / pitch pandangan halus tanpa memalsukan `#07 Fly Up`.
4. **Sinyal Siklus Propeler & Kepakan:**
   - Pastikan `MotionRecognizer` mengirim laju/pulsa putaran propeler & stroke kepakan yang konsisten agar akumulasi momentum `+6%/flap` dan `+7%/orbit` terasa halus di Unity.

---

## 4. Pembaruan UI Unity (`SCREEN 01`, `SCREEN 02`, `SCREEN 07`) & Paket Delta Aset

1. **Paket Delta Siap Impor (`assets/Export_Delta_LaptopB/` & `Export_Delta_LaptopB.zip`):**
   - Berisi **tepat 28 file baru/terbarui** terhadap `UI_Unity_Exportv2.zip` + `poses_cv_vector.zip` (lihat `MANIFEST_DELTA.md`):
     - **15 Ikon Medali UI Bersih (`06_Poses/UI_Pose_01..15.png`):** Menggantikan kartu berkerangka/titik CV yang sempat terpasang di UI medali Unity dengan **siluet stickman bersih UI (tanpa kerangka maupun titik CV)** + pembaruan `#09` L-Flap flutter.
     - **2 Komponen Momentum Bar (`05_HUD/UI_HUD_Momentum_Track_206x20.png`, `UI_HUD_Momentum_Fill_200x14.png`):** Set fill bar sebagai `Image.Type = Filled`, `FillMethod = Horizontal`.
     - **5 Referensi Desain `1920×1080` (`UI_Unity_Export/Designs/`):** `01_Calibration_MotionTutorial.png`, `02_InGame_FlightHUD.png`, `07_PoseGuide_OverlayModal.png` (ditambah `09_SplashScreen.png` & `10_StartScreen.png` sebagai pelengkap zip v2).
     - **4 Aset Vector CV (`poses_cv_vector/`):** `Pose_06B_SteepClimb_ChinUpFlap.png`, `Pose_09_Hover.png`, `Pose_09B_SlowGlide_LPose.png`, `All_19_Poses_Sheet.png`.
     - **2 Buku Panduan Terbaru (`docs/`):** `Panduan_19_Pose_Fanamir_CV.pdf` & `.docx`.
2. **HUD Gameplay `Momentum Dock` (`InGameFlightHUD.cs` + `UISceneAndPrefabBuilder.cs`):**
   - Pasang `MomentumDock` (`420 × 40`, parchment plaque) tepat di atas `TelemetryDock` (`y` offset `+48 px` di atas `TelemetryDock`, anchor kanan bawah) sesuai [`02_InGame_FlightHUD.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/UI_Unity_Export/Designs/02_InGame_FlightHUD.png).
   - Method publik `SetMomentum(float normalized01, bool canYClimb)` untuk mengisi bar horizontal dan memperbarui badge kanan (`"{pct}% · Y-OK"` warna emas `#E5AD44` saat `> 0.02f`, atau `"0% · EMPTY"` abu-abu netral saat kosong).
3. **`PoseGuideCatalog.cs`, `CalibrationPanelUI.cs`, & `PoseGuideOverlayUI.cs`:**
   - Gunakan ke-`15` sprite medali bersih `06_Poses/UI_Pose_01..15` (tanpa CV skeleton/dots, tata letak `3×5` sesuai `07_PoseGuide_OverlayModal.png`), lalu selaraskan teks `#06, #07, #08, #09, #13, #14` persis dengan desain terbaru.

---

## 5. 🤖 Prompt Siap Pakai untuk AI Agent Laptop B

> Salin seluruh blok prompt di bawah ini dan kirimkan ke AI Agent di **Laptop B** setelah memastikan `brain/` sudah di-pull dan paket **`Export_Delta_LaptopB/`** (atau `Export_Delta_LaptopB.zip`) sudah diletakkan di workspace Laptop B.

```markdown
Tugas: Terapkan seluruh pembaruan aset delta dari `Export_Delta_LaptopB/` (ikon guide UI bersih tanpa titik/kerangka CV + komponen Momentum Bar), implementasikan sistem **Momentum Terpadu (`0–100%`)** dan revisi kontrol penerbangan (`#06B Steep Climb Chin-Up Flap`, `#07 Momentum Y-Glide Up`, `#08 Momentum Cruise`, `#09A L-Flap Hover Brake`, `#09B Slow Glide`, `#13/#14` akumulasi bertahap berulang) beserta **Momentum Bar HUD (`SCREEN 02`)** & pembaruan **Pose Guide (`SCREEN 01`/`07`)** sesuai spesifikasi Laptop A di `brain/laptop-a/planning_momentum_dan_revisi_kontrol.md`.

⚠️ BATASAN WAJIB — ZERO REGRESSION 6 POSE TERKUNCI:
Jangan merusak logika deteksi/threshold 6 pose CV yang sudah teruji (`#01` Idle, `#02` Walk, `#04` Takeoff, `#05` Lean & Arm Tilt, `#08` Glide, `#13` Tuck to Dive). Pertahankan kelulusan 100%.

### 1. BACA DOKUMEN ACUAN & SALIN PAKET DELTA (`Export_Delta_LaptopB/`)
1. Jalankan `git -C brain pull`. Patuhi `AGENTS.md` Laptop B (`brain/laptop-a/` read-only, log hanya di `brain/laptop-b/`).
2. Baca tuntas `brain/laptop-a/planning_momentum_dan_revisi_kontrol.md` dan `Export_Delta_LaptopB/MANIFEST_DELTA.md`.
3. Salin isi paket delta ke direktori project (timpa yang lama):
   - `Export_Delta_LaptopB/UI_Unity_Export/Components/05_HUD/*` -> `AR Dragon Sky Guardian/Assets/UI/Components/05_HUD/`
   - `Export_Delta_LaptopB/UI_Unity_Export/Components/06_Poses/UI_Pose_01..15*.png` -> `AR Dragon Sky Guardian/Assets/UI/Components/06_Poses/` *(GANTI/BERSIHKAN kartu `Pose_01..15C` berkerangka CV dari `Assets/UI/Components/06_Poses/`! UI medali game harus memakai siluet bersih `UI_Pose_01..15.png` tanpa kerangka & titik CV!)*
   - `Export_Delta_LaptopB/UI_Unity_Export/Designs/*` -> `docs/UI_Designs_Reference/`
   - `Export_Delta_LaptopB/poses_cv_vector/*` -> `assets/poses_cv_vector/` (arsip referensi CV 19 pose + `All_19_Poses_Sheet.png`)
   - `Export_Delta_LaptopB/docs/*` -> `docs/` & root (`Panduan_19_Pose_Fanamir_CV.pdf`/`.docx`).

### 2. PERUBAHAN PYTHON CV (`ARDragonSkyGuardian_CV/`)
1. **Pisahkan `#09A Hover (L-Pose + Flutter)` vs `#09B Slow Glide (L-Pose Diam)`:**
   - Pada `HoverClassifier` (`motion_recognizer.py`) & pengiriman data ke Unity (`udp_sender.py` / `active_cv_sender.py`): bedakan dengan jelas saat pemain menahan **L-Pose `90°` sambil mengepakkan lengan bawah** (`forearm_vert_speed >= 0.25` -> active Hover Brake) vs **hanya menahan L-Pose `90°` diam** (`forearm_vert_speed < 0.20` -> Slow Glide `~6–8 m/s`).
2. **Pastikan `#07 Fly Up` HANYA aktif dari Open Y-Pose (bukan `HEAD_PITCH` sendirian):**
   - Jangan campur dongak kepala netral tanpa Y-Pose ke state `FlyUp`. `HeadPitch` tetap dikirim murni di `tokens[43]` untuk mendrive `#03`, `#06B Steep Climb Flap`, dan kontrol kepala/pitch.
3. **Pulsa/Laju Berangsur untuk `#14 Propeller` & `#06 Flap`:**
   - Pastikan telemetri rotasi propeler & stroke kepakan stabil agar Unity bisa mengintegrasikan akumulasi momentum secara berangsur.

### 3. REVISI FISIKA MOMENTUM & ANIMASI SAYAP UNITY (`AR Dragon Sky Guardian/Assets/_Scripts/`)
1. **Tambahkan `CurrentMomentum` (`0..1`) di `DragonFlightController.cs` & parameter di `DragonLocomotionConfig.cs`:**
   - **Soft-Cap Gradual Builder Rule:** Tidak boleh ada penambahan instan sekali pose. Integrasikan berangsur:
     - `#04 Takeoff`: gradual cap `0.15f`.
     - `#06 Flap (dagu netral)`: naik landai (`+2.5..+3.2 m/s`), tambah `+0.06f` per kepak hingga soft-cap `0.30f` (jika sudah `> 0.30f`, JANGAN dipotong, cukup drag alami).
     - **`#06B Steep Climb Flap` (`isFlapping && HeadPitch > 0.12f`):** tanpa butuh momentum, badan mendongak curam (`~+24°`) dan naik jauh lebih curam (`+6.5..+8.0 m/s`), soft-cap `0.20f`.
     - `#11 Fly Down (Y-Dive)`: gradual `+0.12f/s`, soft-cap `0.55f`.
     - `#13 Tuck to Dive`: gradual `+0.16f/s` sampai `1.0f` (perlu tukik panjang atau berkali-kali siklus Dive-Glide-Dive).
     - `#14 Propeller Dash`: gradual `+0.07f` per putaran `360°` (cap rate `~0.18f/s`) sampai `1.0f` (perlu putaran berulang).
   - **Efek Momentum ke Kontrol:**
     - **`#08 Glide (T-Pose)`:** `drag = -0.035f/s`; kecepatan maju skala mulus dari `14 m/s` (`0%`) hingga `27 m/s` (`100%`).
     - **`#07 Fly Up (Y-Pose)`:** **Butuh `CurrentMomentum > 0`**! Meluncur naik ke atas (*zoom climb*) sambil mengonsumsi momentum `-0.25f/s`. Jika `CurrentMomentum <= 0`, lakukan *gentle stall* (melandai lalu turun halus `-1.2 m/s`, bukan menanjak terus).
     - **`#09A Hover Active (L-Flap)` & `#12 W-Pose`:** Terapkan **air braking bertahap `-0.40f/s`** (~`2.2–2.5s` dari `100% -> 0%`). Selama `CurrentMomentum > 0`, naga TETAP bergerak maju sambil melambat perlahan — baru mencapai `0 m/s` stasioner setelah momentum habis!
     - **`#09B Slow Glide (L-Hold)`:** `drag = -0.10f/s`, cruise dasar lambat `~7 m/s` + sisa momentum, turun tipis `-0.6 m/s`.
     - **`#10 Gentle Descent`:** rem lembut `-0.20f/s` menuju `8.5 m/s`.
     - **Mendarat / Ground Idle:** reset momentum ke `0`.
     - *(Jika `VillageZoneController` / Auto-Hover desa aktif):* tambahkan *assisted braking* lembut (`-0.85f/s`) agar naga cepat stabil di zona `R = 35m` tanpa terlempar keluar.
2. **Animasi Sayap 3D (`DragonWingAnimator.cs`):**
   - Pastikan saat **`#07 Fly Up (Y-Pose)`**, ke-12 tulang sayap Fanamir terangkat membentuk siluet huruf **Y** (`dihedral` tinggi ke atas) sesuai bentuk Y-Pose!
   - Bedakan animasi **`#09A` (sayap mengepak ringan menahan udara)** vs **`#09B` (sayap setengah tertekuk kompak Slow Glide)**.

### 4. PEMBARUAN UI HUD MOMENTUM BAR & POSE GUIDE (`SCREEN 01`, `SCREEN 02`, `SCREEN 07`)
1. Pastikan seluruh `15` sprite medali **`UI_Pose_01..15.png` (siluet bersih tanpa titik/kerangka CV)** dan **`UI_HUD_Momentum_Track_206x20.png` + `UI_HUD_Momentum_Fill_200x14.png`** terdaftar di `UISpriteSetupUtility.cs` lalu jalankan `UISpriteSetupUtility.SetupAllSprites()`.
2. **Tambahkan `MomentumDock` di `InGameFlightHUD.cs` (`UISceneAndPrefabBuilder.cs`)** tepat di atas `TelemetryDock` (`420×40`, sesuai `02_InGame_FlightHUD.png`), lengkap dengan `SetMomentum(float norm01, bool canYClimb)` yang terhubung langsung ke `DragonFlightController.CurrentMomentum` secara live!
3. **Kembalikan `SCREEN 07` (`PoseGuideOverlayUI.cs`, `PF_PoseGuideOverlay.prefab`, `PoseGuideCatalog.cs`) & `SCREEN 01` (`CalibrationPanelUI.cs`)** ke `15` kartu utama (`3×5` persis `07_PoseGuide_OverlayModal.png`) memakai sprite medali bersih `UI_Pose_01..15`, dan perbarui teks `#06` (menjelaskan juga `#06B Chin-Up Steep Climb`), `#07`, `#08`, `#09` (`HOVER / SLOW GLIDE`), `#13`, `#14` — lalu jalankan `UISceneAndPrefabBuilder.BuildAll()`. *(Sub-varian `#06B` & `#09B` tetap dipetakan highlight live ke kartu `#06` / `#09` saat aktif).*

### 5. VERIFIKASI LENGKAP & UPDATE `brain/laptop-b/`
1. Tambahkan unit test Python CV & Play Mode / physics test Unity untuk memverifikasi:
   - Penambahan momentum gradual & soft-cap (no instant jump, no over-cap truncation, repeated Dash/Dive reaches `100%`).
   - `#07 Y-Pose` menanjak saat `Momentum > 0` (sayap Y) dan stall halus saat `Momentum == 0`.
   - `#06B Chin-Up + Flap` menanjak lebih curam daripada `#06` tanpa membutuhkan momentum.
   - `#09A L-Flap` mengerem bertahap menuju `0 m/s` (masih maju selama momentum tersisa), sementara `#09B L-Hold` melakukan Slow Glide `~7 m/s`.
   - Zero regression pada ke-6 pose terkunci (`#01, #02, #04, #05, #08, #13`) dan `0` Unity console errors/warnings.
2. Catat hasil implementasi & verifikasi di `brain/laptop-b/Devlog.md` dan `brain/laptop-b/Tasks.md`, lalu `commit` & `push` `brain/`.
```
