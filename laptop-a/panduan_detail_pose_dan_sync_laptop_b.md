---
title: Panduan Detail 17 Pose Kontrol & Sinkronisasi Implementasi Laptop B
type: specification
tags:
  - pose-guide
  - computer-vision
  - laptop-a
  - laptop-b
  - synchronization
  - motion-recognizer
date: 2026-11-18
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[laptop B Patch 1 planning]]"
  - "[[planning_revisi_titik_wajah_dan_pov]]"
  - "[[planning_audit_pose_dan_clean_cv_preview]]"
  - "[[kontrak_integrasi_ui_dan_progress]]"
---

# 🦅 Panduan Detail 17 Pose Kontrol Fanamir & Sinkronisasi Laptop B

> **Sumber Aset Gambar Resmi:** [`assets/poses_cv_vector/`](file:///e:/Semester%205/Game/AR%20Dragon/assets/poses_cv_vector/) (`17` kartu pose individual `480×500 px` `2x` PNG + `1` lembar gabungan [`All_17_Poses_Sheet.png`](file:///e:/Semester%205/Game/AR%20Dragon/assets/poses_cv_vector/All_17_Poses_Sheet.png)).
> **Kode Warna Keypoints pada Gambar:**
> - **Oranye (`#EA580C` / `#FB923C`):** Lengan & Kaki **Kanan** pemain (*viewer left* pada mode mirror webcam).
> - **Cyan / Biru (`#0284C7` / `#38BDF8`):** Lengan & Kaki **Kiri** pemain (*viewer right* pada mode mirror webcam).
> - **Cyan Terang (`#00E5FF`):** 5 titik wajah (mata kanan/kiri, hidung, mulut, dagu) serta panah/indikator arah & gelombang gerak.

---

## 📖 Bagian 1: Penjelasan Detail Seluruh 17 Pose (Segmen 1–4)

### Segmen 1 · Grounded Poses (Kontrol di Darat)

| ID & Nama Pose | Gambar Resmi | Tipe | Detail Postur & Sendi CV | Fungsi & Perilaku Gameplay |
| :--- | :--- | :--- | :--- | :--- |
| **`#01` Grounded Idle** | `Pose_01_GroundedIdle.png` | Static Hold | Tubuh berdiri tegak lurus. Kedua lengan menggantung lurus rileks di samping badan (`upper_arm` rapat ke torso). Kedua kaki lurus rapat vertikal. Titik wajah netral menghadap kamera. | Posisi diam saat di darat (`0 m/s`). Sayap Fanamir terlipat tenang di samping tubuh. |
| **`#02` Grounded Walk** | `Pose_02_GroundedWalk.png` | Static Hold | **T-Pose di Darat:** Kedua lengan direntangkan lurus mendatar `180°` ke samping setinggi bahu (*arms away* dari torso), sementara **kedua kaki tetap berdiri lurus diam** (tidak perlu jalan di tempat). | Membuka *Arm-Proximity Gate* saat di darat agar Fanamir berjalan/meluncur maju di permukaan tanah. |
| **`#03` Grounded Look Up** | `Pose_03_GroundedLookUp.png` | Static Hold | Tubuh dan lengan tetap netral seperti Idle (atau diam di tempat), namun **kepala mendongak ke atas** sehingga titik wajah bergeser naik (`HEAD_PITCH > 0`). Ditandai panah `↑` di atas kepala. | Mengarahkan kepala & pandangan Fanamir mendongak ke langit saat berada di darat tanpa memicu takeoff. |

---

### Segmen 2 · Core Flight & Locomotion (Penerbangan Inti)

| ID & Nama Pose | Gambar Resmi | Tipe | Detail Postur & Sendi CV | Fungsi & Perilaku Gameplay |
| :--- | :--- | :--- | :--- | :--- |
| **`#04` Takeoff (Flap)** | `Pose_04_Takeoff.png` | **Active Motion** | Berawal dari posisi darat lalu **kedua lengan mengepak naik-turun** melewati garis horizontal bahu (ditampilkan dengan jejak bayangan 6 lengan atas-bawah + busur kepak). Kedua kaki tetap tegak lurus diam. | Mengangkat Fanamir lepas landas dari permukaan tanah (*Grounded* $\rightarrow$ *Airborne*) secara mulus tanpa snap. |
| **`#05` Lean & Arm Tilt** | `Pose_05_LeanArmTilt.png` | Dynamic Hold | Tubuh bagian atas condong (*torso lean*) ke arah belokan **searah** dengan kemiringan rentangan lengan (*arm tilt*: lengan sisi yang dituju turun, sisi berlawanan terangkat). **Kedua kaki tetap lurus vertikal** tidak ditekuk. | Kemudi belok (*yaw turn*) dan kemiringan badan sayap (*banking roll*) ke kiri/kanan baik saat di darat maupun terbang. |
| **`#06` Flap to Climb** | `Pose_06_FlapToClimb.png` | **Active Motion** | Saat sudah mengudara, **kedua lengan mengepak ritmis mengayun naik** dari posisi bawah/horizontal ke posisi serong atas dengan siku sedikit lentur (ditandai 2 lapis *ghost arms* bawah/tengah + busur panah naik `↑`). | Memberi gaya angkat vertikal stabil (*climb lift*) dan dorongan maju selama di udara tanpa efek *pitch nodding*. |
| **`#07` Fly Up (Y-Pose)** | `Pose_07_FlyUp.png` | Static Hold | Kedua lengan diangkat lurus terbuka ke atas-luar membentuk huruf **Y simetris (~`45°` di atas bahu)** dengan siku relatif lurus, kepala sedikit mendongak (`HEAD_PITCH >= 0`). Ditandai panah `↑` di atas kepala. | Menanjak cepat secara vertikal ke langit (*high climb rate* + pitch menanjak). |
| **`#08` Glide (T-Pose)** | `Pose_08_Glide.png` | Static Hold | Kedua lengan direntangkan lurus horizontal penuh `180°` setinggi bahu membentuk huruf **T** sejajar (ditandai garis aliran udara mendatar di kedua ujung tangan). Kaki tetap lurus vertikal. | Meluncur stabil mendatar (*cruise flight*) atau memicu *Swoop Climb* bila dibuka setelah menukik cepat. |

---

### Segmen 3 · Special Maneuvers (Manuver Khusus Udara)

| ID & Nama Pose | Gambar Resmi | Tipe | Detail Postur & Sendi CV | Fungsi & Perilaku Gameplay |
| :--- | :--- | :--- | :--- | :--- |
| **`#09` Hover (L-Pose)** | `Pose_09_Hover.png` | Hold / Light Flutter | **L-Arm Pose (`90°`):** Lengan atas merapat lurus ke samping tubuh (*tucked*), siku ditekuk tajam `~90°` sehingga lengan bawah membentang mendatar ke samping/depan dada. Ditandai cincin cyan di bawah kaki. | Melayang diam di tempat di udara (*station keeping*), memperlambat kecepatan maju ke `0 m/s`. |
| **`#10` Gentle Descent** | `Pose_10_GentleDescent.png` | Static Hold | **Low-A Pose (`~45°` bawah):** Kedua lengan lurus mengarah serong diagonal ke samping-bawah membentuk huruf **A** rendah (berada di antara posisi Idle yang rapat paha dan Glide yang horizontal). Ditandai panah `↓` di bawah kaki. | Meluncur turun perlahan dan terkontrol (*gentle sink rate*) untuk persiapan mendarat mulus. |
| **`#11` Fly Down (Y-Dive)** | `Pose_11_FlyDown.png` | Static Hold | **Y-Dive Bertekuk:** Lengan atas terbuka mendatar setinggi bahu, sedangkan siku ditekuk ke atas-luar sehingga membentuk siluet huruf Y melebar yang bersiku (kepala cenderung netral/menunduk ringan, garis kecepatan vertikal di sisi). | Menukik turun diagonal terkendali sambil tetap mempertahankan laju gerak maju sedang. |
| **`#12` Fly Down Idle (W-Pose)** | `Pose_12_FlyDownIdle.png` | Static Hold | **Full W-Pose:** Siku ditekuk turun ke sisi luar pinggang, sedangkan kedua lengan bawah mengarah **vertikal lurus ke atas** sejajar di kanan-kiri kepala membentuk huruf **W** yang tegas. | Turun vertikal di tempat tanpa dorongan maju yang besar (*vertical elevator descent*). |
| **`#13` Tuck to Dive** | `Pose_13_TuckToDive.png` | Static Hold | **Aerodynamic Tuck:** Kedua siku rapat ke rusuk dan kedua lengan bawah dilipat menyilang/merapat di depan dada membentuk siluet ramping aerodinamis (ditandai kerucut *sonic cone* di atas kepala). | Menukik curam berkecepatan tinggi (*high-speed dive boost*), memperkecil drag dan memicu *Swoop Climb* saat sayap dibuka kembali. |
| **`#14` Propeller Dash** | `Pose_14_PropellerDash.png` | **Active Motion** | **Twin Wingtip Propellers:** Kedua **lengan atas direntangkan melebar lurus horizontal `180°` ke samping**, lalu **kedua lengan bawah diputar melingkar terus-menerus mengelilingi sumbu siku** (ditandai cincin orbit siku + panah rotasi + jejak *motion blur* 4 fase). | Memberikan lonjakan akselerasi mendadak (*dash burst impulse*) untuk melesat cepat melintasi jarak jauh. |

---

### Segmen 4 · Village Actions (Aksi Khusus Pemulihan 3 Desa)

| ID & Nama Pose | Gambar Resmi | Tipe | Detail Postur & Sendi CV | Fungsi & Perilaku Gameplay |
| :--- | :--- | :--- | :--- | :--- |
| **`#15A` Zephyria · Wind Flap** | `Pose_15A_Zephyria_WindFlap.png` | **Active Motion** | **Sweeping Downward Flap:** Kedua lengan diayunkan dari posisi melebar atas/tengah menyapu kuat **ke bawah** hingga ke samping paha (ditandai *ghost arms* atas-tengah + busur panah melengkung turun `↓` + gelombang angin ganda). | Memicu hembusan angin naga pembersih kabut polusi (*Cleansing Wind Gust*) pada tantangan **Desa Zephyria (Biome 1)**. |
| **`#15B` Pyrewood · Open Mouth** | `Pose_15B_Pyrewood_OpenMouth.png` | Static / Facial Hold | Tubuh berdiri/melayang tenang dengan kedua lengan santai di samping badan, sambil **membuka mulut lebar secara jelas** (`MOUTH_OPEN` via jarak bibir atas & bawah pada `MouthTracker`, ditandai titik mulut terpisah + busur semburan di depan wajah). | Menyemburkan aliran air purba (*Water Breath Stream*) untuk memadamkan kebakaran pada tantangan **Desa Pyrewood (Biome 2)**. |
| **`#15C` Solaria · Dew Rain** | `Pose_15C_Solaria_DewRain.png` | Static Hold | **Double I-Pose Vertikal (`11`):** Kedua lengan diangkat **lurus vertikal sejajar angka `11`** ke langit di sisi kanan-kiri kepala (*arms parallel straight up*, BUKAN terbuka lebar seperti `#07 Y-Pose`), dengan rintik embun di atas kepala. | Melakukan ritual pemanggilan hujan embun suci (*Radiant Dew Rain*) pada tantangan **Desa Solaria (Biome 3)**. |

---

## 🔍 Bagian 2: Audit Perbedaan Implementasi Lama Laptop B vs Gambar Pose Baru

Berdasarkan catatan terakhir di `brain/laptop-b/` (`Devlog.md`, `Tasks.md`, `Rework CV dan Kontrol Terbang Baru.md`, dan `On-Ground Mechanics dan Uji 16 Skenario.md`), terdapat **7 titik penyesuaian penting** yang harus disinkronkan oleh Laptop B:

1. **`#14 Propeller Dash`:**
   - *Implementasi/Teks Lama:* Beberapa catatan lama menyebut tangan berputar di depan dada / bersilangan.
   - *Gambar Baru (`Pose_14_PropellerDash.png`):* **Lengan atas melebar lurus horizontal ke samping (`180°`)**, dan **hanya lengan bawah yang berputar melingkar mengelilingi siku**. `PropellerRotationClassifier` (`motion_recognizer.py`) & deskripsi UI harus memastikan syarat `is_arms_away` (siku terentang jauh dari torso) + orbit pergelangan di sekitar posisi siku terpenuhi.
2. **`#10 Gentle Descent`:**
   - *Implementasi Lama (`GentleDescentClassifier`):* Menggunakan *Tucked T-Pose* (lengan atas rapat torso + lengan bawah membentang).
   - *Gambar Baru (`Pose_10_GentleDescent.png`):* Menggunakan **Low-A Pose (`~45°` ke samping bawah)**. Klasifier harus mendeteksi lengan relatif lurus mengarah ke bawah-samping (~`30°–55°` di bawah bahu), terpisah bersih dari `#01 Idle` (`< 20°`) dan `#08 Glide` (`> 70°`–`110°`).
3. **`#09 Hover (L-Pose)`:**
   - *Implementasi Lama (`HoverClassifier`):* Mensyaratkan `wrist_vert_speed >= 0.40` (harus terus mengayun cepat).
   - *Gambar Baru (`Pose_09_Hover.png`):* Menunjukkan **L-Pose siku `90°`** yang jelas. Klasifier perlu mengizinkan tahan **L-Pose stabil** (lengan atas dekat tubuh + siku ditekuk `~65°–125°` + pergelangan sejajar ketinggian siku) selain kepakan ringan lengan bawah agar lebih responsif dan sesuai gambar panduan.
4. **`#07 Fly Up (Y-Pose)` vs `#15C Solaria (Double I-Pose `11`)`:**
   - *Implementasi Lama (`FlyUpClassifier`):* Hanya membaca `chest_elevation` + kepak.
   - *Gambar Baru:* **`#07 Fly Up`** adalah **Y-Pose terbuka (`~45°` serong atas, lebar pergelangan $\gg$ lebar bahu)** + kepala mendongak (`HEAD_PITCH`), sedangkan **`#15C Solaria`** adalah **Double I-Pose vertikal sejajar `11` (lebar pergelangan $\approx$ lebar bahu, kedua lengan tegak ke atas)**. Keduanya harus dibedakan tegas berdasarkan rasio jarak horizontal antar-pergelangan terhadap `shoulder_width`!
5. **`#03 Grounded Look Up`:**
   - *Status Laptop B (`Tasks.md` item 56):* Tahap 1 (`mouth_tracker.py` token 43 `HEAD_PITCH`) sudah selesai di Python, namun **Tahap 2 di Unity (`DragonFlightController.cs`)** masih perlu disambungkan penuh ke `HEAD_PITCH` murni agar `#03` merespons langsung dongakan kepala pada gambar.
6. **`#15A Zephyria Wind Flap`:**
   - *Gambar Baru (`Pose_15A_Zephyria_WindFlap.png`):* Menekankan **ayunan kepak ke bawah yang tegas** (*downstroke sweep* menuju samping paha) yang siap dipicu saat berada di zona Desa Zephyria (Sesi 4).
7. **Aset Visual UI & Katalog Pose Unity (`Assets/UI/Components/06_Poses/`, `PoseGuideCatalog.cs`, `PoseGuideOverlayUI.cs`, `CalibrationPanelUI.cs`):**
   - Mengganti atau menyambungkan **17 gambar pose baru** dari [`assets/poses_cv_vector/`](file:///e:/Semester%205/Game/AR%20Dragon/assets/poses_cv_vector/) (`Pose_01..15C.png`) ke UI Kalibrasi & Modal Pose Guide (`SCREEN 07`), serta memperbarui seluruh judul/instruksi teks di `PoseGuideCatalog.cs` (termasuk memisahkan/menampilkan varian `#15A`, `#15B`, `#15C` atau 17 pose) agar 100% sinkron dengan gambar dan perilaku CV.

---

## 🤖 Bagian 3: Prompt Siap Pakai untuk AI Agent Laptop B

> Salin seluruh blok prompt di bawah ini dan kirimkan ke AI Agent di **Laptop B** setelah mem-pull repo `brain/` atau memastikan folder [`assets/poses_cv_vector/`](file:///e:/Semester%205/Game/AR%20Dragon/assets/poses_cv_vector/) sudah tersalin/tersedia di workspace Laptop B.

```markdown
Tugas: Audit dan selaraskan sistem deteksi pose Python CV (`ARDragonSkyGuardian_CV/`), kontroler & animasi Unity (`AR Dragon Sky Guardian/Assets/_Scripts/`), serta UI Calibration & Pose Guide (`SCREEN 01` & `SCREEN 07`) di Laptop B agar 100% sesuai dengan 17 gambar pose vektor terbaru di `assets/poses_cv_vector/` dan spesifikasi resmi di `brain/laptop-a/panduan_detail_pose_dan_sync_laptop_b.md`.

### 1. BACA KONTEKS & ATURAN WAJIB TERLEBIH DAHULU
1. Jalankan `git -C brain pull` terlebih dahulu. Patuhi aturan `AGENTS.md` Laptop B (dilarang mengedit file di `brain/laptop-a/`, catat seluruh pembaruan hanya di `brain/laptop-b/`).
2. Baca tuntas dokumen spesifikasi dari Laptop A (read-only):
   - `brain/laptop-a/panduan_detail_pose_dan_sync_laptop_b.md` (rujukan utama 17 pose & tabel gap!)
   - `brain/laptop-a/planning_revisi_titik_wajah_dan_pov.md` (Tahap 2 Unity `HEAD_PITCH`)
   - `brain/laptop-a/planning_audit_pose_dan_clean_cv_preview.md`
3. Periksa aset gambar baru di `assets/poses_cv_vector/` (`Pose_01_GroundedIdle.png` s.d. `Pose_15C_Solaria_DewRain.png` + `All_17_Poses_Sheet.png`).
4. Periksa implementasi eksisting di Laptop B:
   - Python CV: `ARDragonSkyGuardian_CV/pose_stabilizer.py`, `motion_recognizer.py`, `mouth_tracker.py`, `active_cv_sender.py`, `udp_sender.py`, `cv_locomotion_config.json`, dan seluruh file `test_*.py`.
   - Unity C#: `UDPPoseReceiver.cs`, `IPoseProvider.cs`, `DefaultFlightInputMapper.cs`, `DragonFlightController.cs`, `DragonWingAnimator.cs`, `PoseGuideCatalog.cs`, `PoseGuideOverlayUI.cs`, `CalibrationPanelUI.cs`, `UISpriteSetupUtility.cs`, `UISceneAndPrefabBuilder.cs`.

### 2. PENYELARASAN DETEKSI POSE PYTHON CV (`ARDragonSkyGuardian_CV/`)
Sesuaikan classifier dan geometri keypoint di `pose_stabilizer.py`, `motion_recognizer.py`, `mouth_tracker.py`, `active_cv_sender.py`, dan `cv_locomotion_config.json` agar cocok tepat dengan ke-17 gambar di `assets/poses_cv_vector/`:
1. **`#14 Propeller Dash` (`PropellerRotationClassifier`):**
   - Pastikan syarat utama pose adalah **kedua lengan atas direntangkan melebar horizontal ke samping** (`is_arms_away` / `upper_arm_openness` tinggi, sudut bahu-siku mendatar), dan **kedua lengan bawah berputar melingkar mengelilingi siku** (*wrist orbit* di sekitar `elbow`, BUKAN menyilang di depan dada).
   - Validasi arah & kecepatan sudut `est_omega` berbasis pergerakan pergelangan mengelilingi posisi siku masing-masing, tetap aman dari jitter mikro.
2. **`#10 Gentle Descent` (`GentleDescentClassifier`):**
   - Ubah/sesuaikan dari logika lama *Tucked T-Pose* menjadi **Low-A Pose (~`45°` serong ke samping bawah)**: kedua lengan relatif lurus mengarah ke samping-bawah (`arm_openness` sedang, sudut lengan terhadap vertikal torso di rentang ~`30°–55°`, pergelangan di bawah ketinggian siku & siku di bawah bahu, tidak mengepak).
   - Pastikan terpisah tegas dari `#01 Grounded Idle` (lengan rapat paha `< 22°`) dan `#08 Glide` (T-Pose horizontal `~75°–105°`).
3. **`#09 Hover` (`HoverClassifier`):**
   - Selaraskan dengan gambar **L-Pose (`90°`)**: lengan atas merapat ke samping torso (`upper_arm_proximity <= threshold`), siku ditekuk jelas (`avg_elbow_angle` sekitar `60°–125°`), dan pergelangan tangan terangkat setinggi area siku/dada.
   - Izinkan **L-Pose stabil** (atau dengan kepakan ringan lengan bawah) langsung mengaktifkan `HOVER` agar sesuai dengan gambar panduan tanpa memaksa pemain terus mengayun cepat.
4. **`#07 Fly Up (Y-Pose)` vs `#15C Solaria (Double I-Pose `11`)` & `#06 Flap to Climb`:**
   - **`#07 Fly Up (Y-Pose)`:** Deteksi saat kedua lengan diangkat lurus terbuka membentuk **huruf Y serong ke atas** (pergelangan jauh di atas bahu, sudut lengan serong atas terbuka dengan `wrist_span / shoulder_width` lebar mis. `>= 1.55`, bukan vertikal sejajar) dan/atau didukung `HEAD_PITCH` mendongak.
   - **`#15C Solaria (Double I-Pose `11`):** Bedakan secara eksplisit dari `#07` — kedua lengan diangkat **lurus vertikal sejajar di atas bahu** (`wrist_span / shoulder_width` sempit ~`0.7–1.35`, pergelangan tinggi di atas kepala) untuk aksi *Dew Rain* Desa Solaria.
   - **`#06 Flap to Climb`:** Deteksi ayunan kepakan ritmis naik-turun di area atas/tengah dengan hysteresis bersih agar tidak mudah bocor ke `#07` atau `#08`.
5. **`#11 Fly Down (Y-Dive)` & `#12 Fly Down Idle (W-Pose)` (`FlyDownClassifier`):**
   - **`#11 Y-Dive`:** Lengan atas melebar horizontal setinggi bahu, siku ditekuk ke atas-luar (`elbow_angle` tertekuk, pergelangan di atas siku dan lebih lebar dari siku).
   - **`#12 W-Pose`:** Siku ditekuk turun rendah di samping rusuk/pinggang, lengan bawah tegak lurus vertikal ke atas di sisi kiri/kanan kepala membentuk huruf **W** simetris.
6. **`#02 Grounded Walk` vs `#01 Grounded Idle` & `#03 Grounded Look Up`:**
   - Pastikan di darat: `#01 Idle` = lengan rapat ke tubuh (`0 m/s`), `#02 Grounded Walk` = T-Pose horizontal di darat (`is_arms_away` memicu jalan maju sambil kaki tetap tegak lurus diam), dan `#03 Grounded Look Up` = kepala mendongak murni dari `HEAD_PITCH` (`mouth_tracker.py` 3 titik wajah, tanpa ketergantungan `chest_elevation`).
7. **`#05 Lean & Arm Tilt`:**
   - Pastikan arah *torso lean* dan *arm tilt* searah dan konsisten pada mode mirror webcam (condong + miring kanan = belok kanan; condong + miring kiri = belok kiri) tanpa ada sumbu terbalik.
8. **`#15A Zephyria Wind Flap` & `#15B Pyrewood Open Mouth`:**
   - Pastikan `#15A` mendeteksi ayunan kepak menyapu ke bawah yang kuat (*downward sweep velocity* + transisi ke posisi lengan bawah), dan `#15B` membaca bukaan mulut `MOUTH_OPEN` secara akurat & responsif dari `MouthTracker`.
9. **Clean CV Preview (`active_cv_sender.py`):**
   - Pastikan preview CV port `5053` bersih menampilkan **keypoints + garis kerangka tubuh + 3 titik wajah + hanya angka FPS** (sesuai `planning_audit_pose_dan_clean_cv_preview.md`) tanpa kotak teks telemetri yang menutupi tubuh pemain.

### 3. PENYELARASAN CONTROLLER & ANIMASI UNITY (`AR Dragon Sky Guardian/Assets/_Scripts/`)
1. Selesaikan **Revisi Tahap 2 (`HEAD_PITCH` murni)** pada `UDPPoseReceiver.cs`, `DefaultFlightInputMapper.cs`, dan `DragonFlightController.cs`:
   - Hubungkan `HeadPitch` (`tokens[43]`) murni untuk `#03 Grounded Look Up`, kontrol dongak/tukik kepala, dan aksentuasi `#07 Fly Up` / `Pitch Down` tanpa tercampur `chest_elevation`.
   - Pastikan tidak ada regresi pada mode darat (`Grounded Idle`, `Grounded Walk` T-Pose, `Takeoff`), udara (`Glide`, `Flap`, `Hover` L-Pose, `Gentle Descent` Low-A, `Fly Down Y/W`, `Tuck Dive`, `Propeller Dash`), maupun parameter `DragonLocomotionConfig.asset`.
2. Periksa `DragonWingAnimator.cs` agar animasi 12 tulang sayap merespons setiap pose baru dengan mulus (terutama transisi `PropellerDash`, `GentleDescent` Low-A, `Hover` L-Pose, dan `FlyUp` Y-Pose).

### 4. PEMBARUAN UI POSE GUIDE & KALIBRASI (`SCREEN 01` & `SCREEN 07`)
1. Impor/perbarui sprite ikon pose di Unity (`Assets/UI/Components/06_Poses/` atau folder khusus CV poses) menggunakan ke-17 gambar PNG resmi terbaru dari `assets/poses_cv_vector/` (`Pose_01_GroundedIdle.png` s.d. `Pose_15C_Solaria_DewRain.png`), dan daftarkan di `Assets/Editor/UISpriteSetupUtility.cs`.
2. Perbarui **`PoseGuideCatalog.cs`**, **`PoseGuideOverlayUI.cs`** (`PF_PoseGuideOverlay.prefab` — `SCREEN 07`), dan **`CalibrationPanelUI.cs`** (`SCREEN 01`):
   - Tampilkan visual kartu pose baru yang kontras dan seragam.
   - Perbarui seluruh teks judul, sub-judul mekanik, dan deskripsi instruksi pemain pada ke-17 pose (termasuk memecah atau menampilkan jelas `#15A Zephyria · Wind Flap`, `#15B Pyrewood · Open Mouth`, `#15C Solaria · Dew Rain` pada grid `PoseGuideOverlayUI` tanpa terpotong di kanvas `1920×1080`, serta memperbarui deskripsi `#02 Grounded Walk`, `#05 Lean & Arm Tilt`, `#09 Hover`, `#10 Gentle Descent`, dan `#14 Propeller Dash`).
   - Pastikan highlight pose aktif (`SLOT 1 · ACTIVE` di Calibration MRU strip & highlight `ACTIVE` di `PoseGuideOverlayUI`) tetap sinkron live dengan state CV dari `UDPPoseReceiver` (termasuk saat uji pose `#01–#15C`).
3. Jalankan `UISceneAndPrefabBuilder.BuildAll()` agar seluruh scene (`MainMenu_Lobby.unity`, `MainIsland_Gameplay.unity`, `Test_Dragon_Flight.unity`) dan prefab tersinkronisasi bersih.

### 5. VERIFIKASI & PEMBARUAN `brain/laptop-b/`
1. Jalankan seluruh test suite Python CV (`.\ARDragonSkyGuardian_CV\venv\Scripts\python.exe`) — tambahkan/perbarui skenario unit test untuk pose yang direvisi (`PropellerDash` wide-arm + elbow-orbit, `GentleDescent` Low-A, `Hover` L-Pose, `FlyUp` Y-Pose vs `Solaria` 11-Pose, `Lean & Arm Tilt` searah) dan pastikan **100% PASS**.
2. Uji compile & Play Mode Unity (`MainMenu_Lobby.unity`, `MainIsland_Gameplay.unity`, `Test_Dragon_Flight.unity`):
   - `0` compiler errors & `0` runtime console errors/warnings.
   - Uji visual `SCREEN 01` & `SCREEN 07` menampilkan 17 pose baru dengan bersih dan highlight live bekerja sesuai pose.
   - Uji pengiriman paket UDP `5052/5053/5054` (simulasi seluruh 17 pose dan verifikasi live webcam jika tersedia).
3. Dokumentasikan seluruh perubahan di **`brain/laptop-b/Devlog.md`** dan **`brain/laptop-b/Tasks.md`**, lalu jalankan `git -C brain add laptop-b/ && git -C brain commit && git -C brain push`.
```
