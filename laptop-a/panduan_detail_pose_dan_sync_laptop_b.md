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

## 🔍 Bagian 2: Status Pengujian Pose & Titik Perbaikan Laptop B

> **Hasil Uji Empiris Pengguna (Terkonfirmasi):**
> - ✅ **6 Pose SUDAH SESUAI & TERUJI BAIK (LOCKED — JANGAN UBAH LOGIKA DETEKSI/THRESHOLD CV-NYA):**
>   - **`#01` Grounded Idle**, **`#02` Grounded Walk**, **`#04` Takeoff**, **`#05` Lean & Arm Tilt**, **`#08` Glide**, **`#13` Tuck to Dive**.
>   - *(Untuk 6 pose ini, cukup perbarui gambar kartu/deskripsi di UI Guide agar seragam dengan aset `assets/poses_cv_vector/`, tanpa merusak classifier/threshold yang sudah bekerja!)*
> - 🛠️ **Pose yang PERLU DIPERBAIKI / DISELARASKAN KE GAMBAR BARU:**
>   - **`#03` Grounded Look Up**, **`#06` Flap to Climb**, **`#07` Fly Up (Y-Pose)**, **`#09` Hover (L-Pose)**, **`#10` Gentle Descent (Low-A)**, **`#11` Fly Down (Y-Dive)**, **`#12` Fly Down Idle (W-Pose)**, **`#14` Propeller Dash**, serta aksi desa **`#15A`**, **`#15B`**, **`#15C`**.

Rincian penyesuaian yang harus dikerjakan di Laptop B untuk pose yang masih kurang bagus/belum sesuai:

1. **`#03 Grounded Look Up`:**
   - *Target Gambar (`Pose_03_GroundedLookUp.png`):* Berdiri netral dengan tangan di samping badan sambil mendongakkan kepala (`HEAD_PITCH > 0`).
   - *Perbaikan:* Selesaikan integrasi **Tahap 2 (`HEAD_PITCH` murni dari `mouth_tracker.py` token 43)** pada `DragonFlightController.cs` dan tuning sensitivitas dongak kepala agar `#03` responsif mendongak tanpa tertukar takeoff.
2. **`#06 Flap to Climb`:**
   - *Target Gambar (`Pose_06_FlapToClimb.png`):* Kepakan ritmis naik di area bahu/atas saat sudah mengudara.
   - *Perbaikan:* Perbaiki responsivitas & hysteresis `FlapStrokeClassifier` agar kepakan terasa ringan/tegas, terpisah bersih dari `#07 Fly Up` dan tidak mudah bocor kembali ke `#08 Glide` di tengah ayunan.
3. **`#07 Fly Up (Y-Pose)` vs `#15C Solaria (Double I-Pose `11`)`:**
   - *Target Gambar `#07` (`Pose_07_FlyUp.png`):* Kedua lengan lurus **terbuka membentuk huruf Y serong atas (~`45°`, `wrist_span / shoulder_width` lebar $\ge 1.55$)** + kepala mendongak.
   - *Target Gambar `#15C` (`Pose_15C_Solaria_DewRain.png`):* Kedua lengan lurus **vertikal sejajar angka `11` (`wrist_span / shoulder_width` sempit ~`0.7–1.35`)**.
   - *Perbaikan:* Jangan lagi hanya bergantung pada `chest_elevation` + kepak di `FlyUpClassifier`; gunakan bentuk Y-Pose terbuka di atas bahu untuk `#07` dan pisahkan tegas dari `#15C`!
4. **`#09 Hover (L-Pose)`:**
   - *Target Gambar (`Pose_09_Hover.png`):* **L-Pose siku `90°`** (lengan atas merapat ke torso, lengan bawah membentang mendatar di ketinggian siku/dada).
   - *Perbaikan:* `HoverClassifier` sebelumnya mewajibkan `wrist_vert_speed >= 0.40`. Longgarkan/sesuaikan agar **L-Pose stabil** (maupun kepakan ringan lengan bawah) langsung terdeteksi mulus sebagai Hover tanpa harus mengayun cepat terus-menerus.
5. **`#10 Gentle Descent (Low-A Pose)`:**
   - *Target Gambar (`Pose_10_GentleDescent.png`):* **Low-A Pose (~`45°` serong ke samping bawah)** dengan lengan relatif lurus, BUKAN *Tucked T-Pose*.
   - *Perbaikan:* Ubah `GentleDescentClassifier` dari logika lama *Tucked T-Pose* menjadi deteksi **Low-A Pose** (`~30°–55°` di bawah bahu, lengan relatif lurus, tidak mengepak), terpisah aman dari `#01 Idle` dan `#08 Glide`.
6. **`#11 Fly Down (Y-Dive)` & `#12 Fly Down Idle (W-Pose)`:**
   - *Target Gambar `#11` (`Pose_11_FlyDown.png`):* Lengan atas melebar horizontal setinggi bahu, siku ditekuk ke atas-luar membentuk Y bersiku lebar.
   - *Target Gambar `#12` (`Pose_12_FlyDownIdle.png`):* Siku ditekuk turun rendah di sisi pinggang, kedua lengan bawah tegak lurus vertikal ke atas di sisi kepala (**Full W-Pose**).
   - *Perbaikan:* Audit dan tuning sudut siku (`elbow_angle`), posisi siku relatif terhadap bahu, serta arah lengan bawah di `FlyDownClassifier` agar `#11` dan `#12` mudah dibentuk & konsisten di depan webcam nyata tanpa saling tertukar dengan `#07`, `#06`, atau `#08`.
7. **`#14 Propeller Dash`:**
   - *Target Gambar (`Pose_14_PropellerDash.png`):* **Kedua lengan atas melebar lurus horizontal `180°` ke samping**, lalu **kedua lengan bawah berputar melingkar mengelilingi sumbu siku**.
   - *Perbaikan:* Sesuaikan `PropellerRotationClassifier` (`motion_recognizer.py`) agar memvalidasi lengan atas yang terentang mendatar ke samping (`is_arms_away` / siku terpisah lebar dari torso) dan mengandalkan orbit pergelangan mengelilingi siku masing-masing (bukan di depan dada).
8. **`#15A Zephyria`, `#15B Pyrewood`, `#15C Solaria` (Village Actions):**
   - Pastikan deteksi ayunan kepak bawah tegas (`#15A`), bukaan mulut `MOUTH_OPEN` responsif (`#15B`), dan Double I-Pose vertikal `11` (`#15C`) siap dipakai / teruji.
9. **Aset Visual UI & Katalog Pose Unity (`Assets/UI/Components/06_Poses/`, `PoseGuideCatalog.cs`, `PoseGuideOverlayUI.cs`, `CalibrationPanelUI.cs`):**
   - Mengganti atau menyambungkan **17 gambar pose baru** dari [`assets/poses_cv_vector/`](file:///e:/Semester%205/Game/AR%20Dragon/assets/poses_cv_vector/) (`Pose_01..15C.png`) ke UI Kalibrasi & Modal Pose Guide (`SCREEN 07`), serta memperbarui seluruh deskripsi instruksi di `PoseGuideCatalog.cs`.

---

## 🤖 Bagian 3: Prompt Siap Pakai untuk AI Agent Laptop B

> Salin seluruh blok prompt di bawah ini dan kirimkan ke AI Agent di **Laptop B** setelah memastikan `brain/` sudah di-pull dan folder [`assets/poses_cv_vector/`](file:///e:/Semester%205/Game/AR%20Dragon/assets/poses_cv_vector/) tersedia di workspace Laptop B.

```markdown
Tugas: Perbaiki & selaraskan pose-pose kontrol yang masih belum sesuai / kurang bagus di Python CV (`ARDragonSkyGuardian_CV/`), kontroler Unity (`AR Dragon Sky Guardian/Assets/_Scripts/`), serta perbarui visual kartu & teks UI Pose Guide (`SCREEN 01` & `SCREEN 07`) di Laptop B agar 100% cocok dengan 17 gambar pose di `assets/poses_cv_vector/` dan `brain/laptop-a/panduan_detail_pose_dan_sync_laptop_b.md`.

⚠️ BATASAN PENTING — POSE YANG SUDAH LOLOS UJI (JANGAN DIUBAH LOGIKA CV-NYA!):
Pose **`#01` Grounded Idle, `#02` Grounded Walk, `#04` Takeoff, `#05` Lean & Arm Tilt, `#08` Glide, dan `#13` Tuck to Dive** sudah dites di webcam nyata dan dinyatakan **SUDAH SESUAI**.
- **DILARANG merusak atau mengubah threshold/classifier 6 pose tersebut!** Pertahankan kelulusannya 100% (regresi nol).
- Fokus perbaikan logika deteksi CV & kontroler hanya pada pose berikut: **`#03`, `#06`, `#07`, `#09`, `#10`, `#11`, `#12`, `#14`**, serta **`#15A`, `#15B`, `#15C`**.

### 1. BACA KONTEKS & ATURAN WAJIB TERLEBIH DAHULU
1. Jalankan `git -C brain pull` terlebih dahulu. Patuhi `AGENTS.md` Laptop B (dilarang mengedit `brain/laptop-a/`, catat log hanya di `brain/laptop-b/`).
2. Baca tuntas dokumen spesifikasi dari Laptop A (read-only):
   - `brain/laptop-a/panduan_detail_pose_dan_sync_laptop_b.md`
   - `brain/laptop-a/planning_revisi_titik_wajah_dan_pov.md` (Tahap 2 Unity `HEAD_PITCH`)
   - `brain/laptop-a/planning_audit_pose_dan_clean_cv_preview.md`
3. Lihat langsung gambar panduan baru di `assets/poses_cv_vector/` (`Pose_01_GroundedIdle.png` s.d. `Pose_15C_Solaria_DewRain.png`).
4. Audit implementasi terkait di Laptop B (`pose_stabilizer.py`, `motion_recognizer.py`, `mouth_tracker.py`, `active_cv_sender.py`, `cv_locomotion_config.json`, `UDPPoseReceiver.cs`, `DefaultFlightInputMapper.cs`, `DragonFlightController.cs`, `DragonWingAnimator.cs`, `PoseGuideCatalog.cs`, `PoseGuideOverlayUI.cs`, `CalibrationPanelUI.cs`).

### 2. PERBAIKAN POSE YANG MASIH KURANG BAGUS / BELUM SESUAI
Sesuaikan classifier Python CV & kontroler Unity khusus untuk pose berikut (tanpa menyebabkan regresi pada `#01, #02, #04, #05, #08, #13`):

1. **`#03 Grounded Look Up` (`DragonFlightController.cs`, `mouth_tracker.py`):**
   - Selesaikan **Tahap 2 `HEAD_PITCH` murni**: sambungkan `HeadPitch` (`tokens[43]`) untuk `#03 Grounded Look Up` agar saat kepala mendongak di darat (lengan santai di samping badan), naga mendongak mulus dan tidak tercampur `chest_elevation`.
2. **`#06 Flap to Climb` (`FlapStrokeClassifier` di `motion_recognizer.py`):**
   - Perbaiki responsivitas & hysteresis kepakan di udara agar ayunan naik-turun terasa ringan, konsisten, dan tidak bertabrakan dengan `#07 Fly Up` atau `#08 Glide`.
3. **`#07 Fly Up (Y-Pose)` vs `#15C Solaria (Double I-Pose `11`):**
   - **`#07 Fly Up`:** Deteksi berdasarkan pose **Y terbuka ke atas** (`~45°` di atas bahu, `wrist_span / shoulder_width` lebar mis. `>= 1.55`, siku relatif lurus) +/atau dukungan `HEAD_PITCH` mendongak (jangan hanya bergantung pada `chest_elevation` yang sulit terdeteksi).
   - **`#15C Solaria (Dew Rain)`:** Bedakan tegas dari `#07` — kedua lengan diangkat **lurus vertikal sejajar angka `11`** di sisi kepala (`wrist_span / shoulder_width` sempit ~`0.7–1.35`, pergelangan tinggi di atas kepala).
4. **`#09 Hover` (`HoverClassifier`):**
   - Ubah agar sesuai gambar **L-Pose (`90°`)**: lengan atas merapat di samping tubuh (`upper_arm_proximity`), siku ditekuk jelas (`~60°–125°`), dan pergelangan tangan di ketinggian area siku/dada.
   - **Wajib:** Izinkan **L-Pose stabil** (maupun dengan kepakan ringan lengan bawah) langsung mengaktifkan `HOVER`, jangan memaksa `wrist_vert_speed >= 0.40` terus-menerus.
5. **`#10 Gentle Descent` (`GentleDescentClassifier`):**
   - Ubah dari logika lama *Tucked T-Pose* menjadi **Low-A Pose (~`45°` serong ke samping bawah)**: kedua lengan relatif lurus mengarah serong bawah (`~30°–55°` di bawah bahu, tanpa kepakan). Pastikan tidak bentrok dengan `#01 Idle` (lengan rapat paha) maupun `#08 Glide` (horizontal `180°`).
6. **`#11 Fly Down (Y-Dive)` & `#12 Fly Down Idle (W-Pose)` (`FlyDownClassifier`):**
   - **`#11 Y-Dive`:** Lengan atas mendatar sejajar bahu, siku ditekuk ke atas-luar membentuk Y bersiku lebar.
   - **`#12 W-Pose`:** Siku ditekuk turun rendah di sisi luar pinggang, lengan bawah tegak lurus vertikal ke atas sejajar kanan-kiri kepala (**W** simetris).
   - Tuning batas sudut siku & relasi posisi pergelangan-siku-bahu agar kedua pose ini mudah dipicu dan akurat di webcam.
7. **`#14 Propeller Dash` (`PropellerRotationClassifier`):**
   - Perbaiki pose menjadi: **kedua lengan atas melebar lurus horizontal `180°` ke samping**, dan **kedua lengan bawah berputar melingkar mengelilingi siku** (*wrist orbit* di sekitar `elbow`, BUKAN di depan dada).
   - Pastikan logika deteksi memverifikasi rentangan lengan atas ke samping (`is_arms_away`) + kecepatan sudut orbit pergelangan terhadap siku masing-masing.
8. **`#15A Zephyria Wind Flap` & `#15B Pyrewood Open Mouth`:**
   - Pastikan ayunan kepak bawah tegas (`#15A`) dan bukaan mulut `MOUTH_OPEN` (`#15B`, threshold nyaman & responsif) siap terdeteksi bersih.
9. **Clean CV Preview (`active_cv_sender.py`):**
   - Pastikan overlay video `5053` bersih menampilkan **keypoints + skeleton + 3 titik wajah + hanya angka FPS** (hapus teks HUD telemetri yang menutupi tubuh pemain sesuai `planning_audit_pose_dan_clean_cv_preview.md`).

### 3. PEMBARUAN VISUAL & TEKS UI (`SCREEN 01` & `SCREEN 07`)
1. Impor ke-`15` ikon medali UI bersih terbaru (**tanpa kerangka/titik CV**) dari `assets/UI_Unity_Export/Components/06_Poses/` (`UI_Pose_01_GroundedIdle.png` s.d. `UI_Pose_15_VillageActions.png`) ke Unity (`Assets/UI/Components/06_Poses/`) dan pastikan terdaftar di `Assets/Editor/UISpriteSetupUtility.cs`. Gunakan pula `assets/UI_Unity_Export/Designs/01_Calibration_MotionTutorial.png` & `07_PoseGuide_OverlayModal.png` sebagai referensi visual layout terbaru. *(Catatan: gambar di `assets/poses_cv_vector/` memuat titik & kerangka CV untuk acuan teknis/arsip CV, sedangkan medali `06_Poses/` memakai siluet bersih khusus UI game).*
2. Perbarui **`PoseGuideCatalog.cs`**, **`PoseGuideOverlayUI.cs`** (`PF_PoseGuideOverlay.prefab` — `SCREEN 07`), dan **`CalibrationPanelUI.cs`** (`SCREEN 01`):
   - Tampilkan sprite medali baru `06_Poses/UI_Pose_01..15` pada setiap slot.
   - Perbarui deskripsi instruksi pose (termasuk `#03` look up skyward, `#07` wide Y-shape, `#09` 90° L-pose, `#10` Low-A ~45°, `#13` folded chest arms, `#14` upper arms wide horizontal + forearms spin in circles at elbows, dan `#15` Zephyria/Pyrewood/Solaria 11).
   - Pastikan highlight live (`SLOT 1 · ACTIVE` & modal `ACTIVE`) sinkron untuk semua pose, lalu jalankan `UISceneAndPrefabBuilder.BuildAll()`.

### 4. VERIFIKASI & PEMBARUAN `brain/laptop-b/`
1. Jalankan seluruh test suite Python CV (`.\ARDragonSkyGuardian_CV\venv\Scripts\python.exe`) + tambahkan unit test baru untuk `#03, #06, #07, #09, #10, #11, #12, #14, #15A/B/C`, sekaligus **verifikasi regresi nol pada `#01, #02, #04, #05, #08, #13`** (**100% PASS**).
2. Uji compile & Play Mode Unity (`MainMenu_Lobby.unity`, `MainIsland_Gameplay.unity`, `Test_Dragon_Flight.unity`) dengan `0` errors & `0` warnings.
3. Catat hasil perbaikan di **`brain/laptop-b/Devlog.md`** dan **`brain/laptop-b/Tasks.md`**, lalu `git -C brain add laptop-b/ && git -C brain commit && git -C brain push`.
```
