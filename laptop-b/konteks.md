# 🐉 PROJECT CONTEXT & TECHNICAL BIBLE: AR DRAGON SKY GUARDIAN (v6.0 — ADVANCED SKY PECK FLIGHT & MODULAR ARCHITECTURE)

> **Fungsi Dokumen:** Rujukan teknikal utama (*Single Source of Truth*) bagi kolaborasi antara Manusia (Developer) dan AI Agent (Hermes / Antigravity Agent).
> **Pasangan Dokumen:** Gunakan bersama `prd.md` untuk implementasi alur kerja berperingkat.
> **Status Implementasi:** Seluruh arsitektur penerbangan tubuh ala **Sky Peck** (Flap to Climb, Continuous Lean to Turn, Tuck to Dive 28 m/s, Propeller Dash 26 m/s, Swoop Climb, Hover, Fly Up), **Peluncur Otomatis Python CV (`PythonProcessManager.cs`)**, **Arsitektur Input Modular (`IFlightInputProvider.cs` & `DefaultFlightInputMapper.cs`)**, dan **Protokol Telemetri UDP 39-Float** telah **100% aktif, terkompilasi bersih (0 Compiler Errors), dan terverifikasi di Unity Play Mode**.

---

## 1. RINGKASAN KONSEP & IDENTITAS PROJEK

### 1.1 Identitas Game
- **Nama Game / Project Unity:** AR Dragon Sky Guardian (nama kode: `DragonWingAR.*`)
- **Genre:** Exergaming PC berbasis Webcam / 3D Semi-Open-World Sky Peck Flight Action / Edukasi Lingkungan Hidup
- **Engine & Technology Stack:**
  - **Unity:** Unity 6 (`6000.3.7f1`) Universal Render Pipeline (URP)
  - **Python:** Python 3.10+ (PyTorch CUDA, YOLOv8n-pose Tensor Cores ~18 ms / 54 FPS, OpenCV 4.x)
  - **Blender:** Blender 5.1 / 5.2 CLI pipeline (Bake Transforms & Vertex Normals)
- **Karakter Utama:** Naga Penjaga Langit Elemen Air & Embun Purba (`Dragon_Model.blend` / `Dragon_Model.fbx`)
- **Misi Utama:** Menjelajah pulau daratan besar 800x800m dan memulihkan 3 desa dari polusi udara, kebakaran hutan, dan kemarau menggunakan gestur tubuh alami di hadapan webcam.

### 1.2 Matriks Pembagian Tanggung Jawab (Semi-Autonomous Workflow)

| Kategori Tugas | Pelaksana Utama | Tanggung Jawab |
| :--- | :--- | :--- |
| **Python CV Engine & UDP Streaming (5052 & 5053)** | 🤖 **AI Agent** | Menulis modul inferensi YOLOv8n-pose, RobustKeypointTracker, BiometricNormalizer, GestureStateMachine, detektor Propeller Dash, dan video PIP. |
| **Unity C# Engine (Sky Peck Physics, Input Mapper, HUD, Lifecycle)** | 🤖 **AI Agent** | Menulis kode C# bebas error (*0 Compiler Errors*), dinamika Sky Peck, arsitektur modular `IFlightInputProvider`, auto-launch Python process, dan raycast protection. |
| **Integrasi & Skala Aset 3D (`.blend` & `.fbx`)** | 🤖 **AI Agent + User** | Ekspor Blender CLI dengan bake transforms, pengaturan skala naga 5m dan pulau 800m di scene Unity. |
| **Pengujian Play Mode & Evaluasi Gerakan Fisik** | 👨‍💻 **Manusia (User)** | Menguji gerakan tubuh di depan webcam, validasi responsivitas gameplay, dan evaluasi kenyamanan bermain. |

---

## 2. SPESIFIKASI ASET 3D & SKALA DUNIA

### 2.1 Spesifikasi Karakter Naga (`Dragon_Model.blend` / `Dragon_Model.fbx`)
- **Aset 3D:** Model naga resmi menggunakan `Dragon_Model.blend` (diekspor menjadi `Dragon_Model.fbx` di folder `Assets/Assets 3D/`).
- **Dimensi Fisik Nyata (1 Unity unit = 1 meter):**
  - **Bentang Sayap (Wingspan X):** $5.06\text{ meter}$
  - **Panjang Tubuh (Length Z):** $3.60\text{ meter}$ (dari moncong kepala ke ujung ekor)
  - **Tinggi Tubuh (Height Y):** $1.59\text{ meter}$
  - **Pusat Pivot:** Terpusat di `(0, 0, 0)` koordinat lokal pemain.
- **Material & Shading:**
  - 12 Material **URP Lit** dengan mode *Double-Sided Rendering* (`_Cull = 0`) agar membran sayap dan sirip ekor tampak solid dari kedua sisi.
  - Palet: Teal body (`M_Dragon_Body_Teal`), turquoise wings (`M_Dragon_Wing_Membrane`), cream belly (`M_Dragon_Belly_Cream`), amber eyes (`M_Dragon_Eye_Amber`), dark dorsal ridges (`M_Dragon_Dorsal_DarkTeal`), and crystal chest (`M_Dragon_Chest_Crystal`).
- **Kamera Follow (Third-Person Cinematic):**
  - Parent: Child terhadap `Dragon_Player`
  - `localPosition`: `(0.00, 2.50, -6.50)`
  - `localEulerAngles`: `(14.00, 0.00, 0.00)`
  - FOV: $60^\circ$ | Far Clip Plane: $1500\text{ m}$

### 2.2 Desain Daratan Pulau (`pulau.blend` / `pulau.fbx`)
- **Bentuk:** Daratan besar berkarakter *grounded cutaway slab* dengan 3 danau organik, bukit terjal, padang rumput, dan bukit vulkanik tinggi di sisi barat.
- **Dimensi Bounding Box:**
  - Panjang x Lebar: **$813.19\text{ m} \times 800.00\text{ m}$** (Skala $800 \times 800\text{ m}$, rasio 1:1, pusat di `(0, 0, 0)`).
  - Ketebalan Blok Medan: Dasar permukaan air $Y \approx -0.8\text{ m}$ hingga $-20\text{ m}$.
  - Ketinggian Puncak Gunung: Puncak tertinggi di $Y = 118.9\text{ m}$ pada koordinat `(-337, 119, -31)`.
- **Submesh & Kolisi:**
  - `Island_Terrain` (MeshCollider aktif)
  - `Island_Rocks` (MeshCollider aktif)
  - `Island_Water_01`, `Island_Water_02`, `Island_Water_03` (Renderer air danau URP Lit)
- **Titik Marker Lokasi 3 Desa (Pad Markers):**
  - **Desa 1 (Polusi Udara):** `Pad_Desa1_Polusi` di koordinat `(-200.00, 0.13, 80.00)`
  - **Desa 2 (Kebakaran Hutan):** `Pad_Desa2_HutanKebakaran` di koordinat `(10.00, 0.23, -133.00)`
  - **Desa 3 (Kekeringan Tanah):** `Pad_Desa3_Kekeringan` di koordinat `(260.00, 0.82, 13.00)`

---

## 3. DINAMIKA PENERBANGAN ALA SKY PECK (SKY PECK MOTION FLIGHT DYNAMICS)

Sistem penerbangan dirancang mengadopsi mekanisme game terbang tubuh indie **Sky Peck** (*skypeck.fun*), memadukan kebebasan meluncur anggun dengan respon fisik alami tanpa menyebabkan disorientasi kamera atau goyangan pitch:

```
        \   /                 — o —                    | o |                     ( @ )
         \o/                    |                     /  |  \                   /  |  \
   FLAP TO CLIMB             GLIDE                   TUCK TO DIVE            PROPELLER DASH
   (Mendaki Halus)       (Meluncur Anggun)       (Menukik Cepat ↓↓)       (Dorongan Rotasi Lengan)
   Lift Vertikal + Dorong  Jelajah 14 m/s          Pitch: -40° s/d -45°     Akselerasi Impulsif
   Pitch Stabil & Tenang  Sink Rate: -1.0 m/s      Akselerasi Gravitasi     Hingga 26 m/s!
   Tanpa Nodding!         Otomatis SWOOP CLIMB!    Hingga 28 m/s!
```

### 3.1 Tujuh Mode Gerak Sky Peck
1. **Flap to Climb (Mengepakkan Tangan untuk Mendaki):**
   - Pemain mengepakkan tangan naik-turun secara ritmis melintasi bahu.
   - Menghasilkan gaya angkat vertikal halus ($+6.5\text{ m/s}$) dan dorongan akselerasi ($+12\text{ m/s}^2$).
   - **Pitch Decoupling:** Gerakan kepakan tidak memicu perubahan pitch naik-turun (*zero pitch nodding*). Hidung naga tetap tenang mendaki ($+8^\circ \sim +12^\circ$).
2. **Continuous Lean to Bank & Turn (Kemudi Alami Torso + Kemiringan Sayap):**
   - Pemain berbelok dengan mencondongkan torso (*torso lean* $\frac{\text{mid\_shoulder.x} - \text{mid\_hip.x}}{\text{shoulder\_dist}}$) ataupun memiringkan lengan (*arm tilt*).
   - Menggabungkan $50\%$ kemiringan tangan $+ 50\%$ condong tubuh dengan kurva non-linear ($1.35$) untuk kemudi mikro yang presisi. Kemudi Yaw mencapai $65^\circ/\text{s}$ dan banking sayap visual $\pm 35^\circ$.
3. **Tuck to Dive (Menukik Curam Kecepatan Tinggi — Pose Foto Pengguna):**
   - Tangan dirapatkan mengepal di dada atas dengan siku mengarah ke bawah di samping badan.
   - Sayap melipat aerodinamis rapat, koefisien drag berkurang, hidung menukik tajam ($-40^\circ \sim -45^\circ$), berakselerasi kencang hingga $28\text{ m/s}$.
4. **Propeller Dash (Dorongan Akselerasi Rotasi Lengan):**
   - Pemain memutar lengan secara kontinu menyerupai baling-baling (*monotonic angular displacement*).
   - Menghasilkan dorongan kecepatan instan (*dash impulse*) hingga $26\text{ m/s}$ untuk melintasi jarak jauh dengan cepat.
5. **Swoop Climb Out of Dive (Luncuran Melambung Menanjak Otomatis):**
   - Membuka kembali tangan ke posisi Glide setelah dive berkecepatan tinggi ($> 14\text{ m/s}$) secara otomatis mengonversi kelebihan energi kinetik menjadi luncuran melambung naik (*swoop soaring climb*) yang megah ke angkasa.
6. **Glide & Gentle Descent (Meluncur Santai & Anggun):**
   - Kedua tangan direntangkan mendatar ke samping.
   - Meluncur stabil pada kecepatan jelajah $14\text{ m/s}$ dengan laju turun alami lembut ($-1.0\text{ m/s}$).
7. **Hover & Fly Up (Melayang Halus & Penyesuaian Ketinggian):**
   - Lengan atas dirapatkan ke samping badan dengan gerakan kepakan ringan lengan bawah untuk melayang di tempat (*hover*).
   - Mengangkat dada dan mencondongkan badan ke atas untuk mendaki lembut (*fly up*).

### 3.2 Proteksi Ketinggian Daratan (Terrain Clearance Protection)
- Naga dilengkapi sensor raycast otomatis ke bawah sejauh 500 meter.
- **Plafon Minimum Dinamis:** `safeMinAltitude = terrainHeight + 3.5f`.
- Jika jarak ke daratan $< 8.0\text{ m}$, hidung naga secara otomatis diratakan (pitch dibatasi tidak boleh menukik), dan jika jarak $< 3.5\text{ m}$, naga secara mulus menyusuri kontur bukit pulau tanpa menembus geometri daratan.
- **Plafon Maksimum:** `maxAltitude = 160f` (memungkinkan naga terbang bebas melintasi puncak gunung tertinggi $118.9\text{ m}$).

---

## 4. ARSITEKTUR INPUT MODULAR & PENGELOLA PROSES PYTHON

### 4.1 Arsitektur Input Modular Unity (`IFlightInputProvider`)
Untuk memastikan kode pergerakan naga terisolasi dari detail teknis sensor kamera, sistem menggunakan pola *Dependency Inversion*:

```
[UDPPoseReceiver.cs] (Sensor Raw Pose 39-Float)
         │
         ▼
[DefaultFlightInputMapper.cs] (Mapping, Deadzone, Ease Curves)
         │
         ▼  (FlightCommand Struct)
[DragonFlightController.cs] (Sky Peck Aerodynamics, Collision, Physics)
```

- **`FlightCommand`:** Struct serializable yang menyimpan `TurnInput`, `PitchInput`, `BodyPitch`, `IsFlapping`, `FlapPower`, `IsTuckDiving`, `IsPropellerDash`, `DashPower`, `IsGliding`, `IsWaterBreathing`, `IsHandsHighUp`, `IsHover`, `IsFlyUp`, dan `ActiveStateId`.
- **`DefaultFlightInputMapper.cs`:** Menerapkan kurva kenyamanan non-linear, deadzone, dan filter anti-fatigue.

### 4.2 Peluncur Proses Otomatis (`PythonProcessManager.cs`)
- **Zero-Manual Launch:** Pemain/Developer **tidak perlu lagi membuka terminal terpisah** untuk menjalankan Python.
- **Auto-Lifecycle:**
  1. **Saat Play Mode / Game Dimulai (`Awake()`):** `PythonProcessManager.cs` mendeteksi keberadaan `venv/Scripts/python.exe` dan `active_cv_sender.py`, lalu meluncurkannya sebagai child process Windows dengan PID yang dipantau.
  2. **Saat Play Mode / Game Berhenti (`ExitingPlayMode` / `OnApplicationQuit`):** Proses Python dihentikan seketika dengan `process.Kill()` dan *Windows TaskKill tree fallback* (`taskkill /PID {pid} /F /T`) sehingga webcam langsung dilepaskan tanpa tersangkut (zero webcam lock).
- **Indikator HUD:** In-Game HUD menampilkan status koneksi live `Python CV : AKTIF (PID: xxxxx) [AUTO-SYNC]`.

---

## 5. PROTOKOL JARINGAN UDP GANDA (DUAL-PORT UDP v6.0)

Modul Python dan Unity berkomunikasi melalui 2 port socket UDP terpisah berkecepatan tinggi:

```
[Python CV Sender]
   ├── UDP 127.0.0.1:5052 (Pose Telemetri 39-Float CSV) ──> [UDPPoseReceiver.cs]
   └── UDP 127.0.0.1:5053 (JPEG Frame PIP Video) ──────────> [InGameFlightHUD.cs]
```

### 5.1 Port 5052: Telemetri Pose & Gestur (39-Float CSV)
- **Host & Port:** `127.0.0.1:5052` | **Frekuensi:** $\ge 30\text{ FPS}$ (latensi AI $\approx 18\text{ ms}$ pada CUDA GPU)
- **Format String CSV (39 Nilai):**
  - `0..11` = Koordinat sendi dasar (LS_X, LS_Y, RS_X, RS_Y, LW_X, LW_Y, RW_X, RW_Y, LH_X, LH_Y, RH_X, RH_Y)
  - `12` = Nilai Bukaan Mulut `MOUTH_OPEN` ($0.0 = \text{Tutup}, 1.0 = \text{Buka Lebar}$)
  - `13..16` = Koordinat Siku (LE_X, LE_Y, RE_X, RE_Y)
  - `17` = Flag Gestur Tuck Dive (`IS_DIVE`: $1.0$ jika aktif)
  - `18` = Flag Gestur Kepakan Sayap (`IS_FLAP`: $1.0$ jika aktif)
  - `19` = Frekuensi Kepakan (`FLAP_FREQ` dalam Hz)
  - `20` = Daya Dorong Kepakan (`FLAP_POWER`: $0.0 - 1.0$)
  - `21` = Orientasi Tubuh (`BODY_PITCH`: $-1.0 \sim +1.0$)
  - `22..23` = Koordinat Hidung (NOSE_X, NOSE_Y)
  - `24..27` = Biometrik (WINGSPAN_RATIO, FLAP_HEIGHT, LEAN_ANGLE_DEG, TRACKING_STATE)
  - `28..31` = Gestur Lanjutan (IS_DASH, DASH_POWER, STEERING_INPUT, ACTIVE_STATE_ID)
  - `32..33` = Kalibrasi (CALIB_STATE_ID, CALIB_PROGRESS)
  - `34..38` = Gestur Ekstensi (IS_HOVER, IS_GENTLE_DESCENT, IS_FLY_UP, CHEST_ELEVATION, FOREARM_FLAP_POWER)

### 5.2 Port 5053: Streaming Video Webcam PIP (Picture-in-Picture)
- **Host & Port:** `127.0.0.1:5053`
- **Format Paket:** Raw byte buffer gambar JPEG terkompresi ($320 \times 180$ piksel, *quality* 48, ukuran paket $\approx 3-4\text{ KB} < 65\text{ KB}$ MTU UDP).
- **Fungsi di Unity:** Didekodekan langsung oleh `InGameFlightHUD.cs` menggunakan `Texture2D.LoadImage(bytes)` dan ditampilkan di kotak preview kanan atas layar secara real-time.
- **Fallback Otomatis:** Jika Python video stream belum aktif, kotak PIP secara otomatis menampilkan radar kerangka (*interactive skeleton wireframe*) lengkap dengan sendi siku.

---

## 6. KONTROL AKSI DESA & AUTO-HOVER

Ketika naga terbang mendekati dan memasuki area *Trigger Collider* desa (`SphereCollider`, radius 25-35m):
1. **Auto-Hover:** Naga berhenti maju otomatis dan memutar badan secara anggun menghadap pusat desa dengan osilasi mengambang (*hover bobbing* $\pm 0.4\text{ m}$).
2. **Eksekusi Gestur Interaktif (5 detik = 100% Selesai):**

| Desa & Masalah | Aksi Naga | Gestur Pemain | Logika Pengesanan C# |
| :--- | :--- | :--- | :--- |
| **Desa 1 (Polusi Udara)** | Mengibas Kabut Asap | Kepakkan kedua tangan naik-turun melewati bahu | Status `IsFlapping` aktif saat tangan berganti posisi di atas dan bawah bahu. |
| **Desa 2 (Kebakaran Hutan)** | Semburan Air Purba | Buka mulut lebar (`MouthOpen > 0.45`) ATAU tangkupkan kedua tangan di dada | Status `IsWaterBreathing` aktif, mematikan partikel api desa. |
| **Desa 3 (Kekeringan Tanah)** | Memanggil Hujan Embun | Angkat kedua tangan tinggi ke angkasa di atas kepala | `IsHandsHighUp` aktif saat `LW.y < LS.y - 0.14f` dan `RW.y < RS.y - 0.14f`. |

3. **Penyelesaian:** Begitu progres desa mencapai 100%, status Auto-Hover dilepaskan dan naga kembali dapat terbang menjelajah ke desa berikutnya.

---

## 7. SISTEM KONTROL KEYBOARD HYBRID (*ZERO-CONFLICT*)

Untuk menjamin kelancaran pengujian kapan pun tanpa kamera, sistem keyboard fallback dirancang dengan aturan isolasi ketat:

- **Aturan Isolasi:** Jika data kamera UDP terhubung dan pengguna tidak menekan tombol fisik, keyboard **dilarang keras menimpa data gestur kamera**.
- **Pemetaan Tombol Keyboard Sky Peck:**
  - **Kepakan Sayap (Flap to Climb ↑):** `Spasi` atau `W`
  - **Tuck Dive (Terjun Menukik Tajam -45° ↓↓):** `S`
  - **Meluncur Mendatar Glide (-4° Glide):** `T` (atau lepas tombol)
  - **Propeller Dash (Dorongan Cepat):** `Left Shift` atau `E`
  - **Arahkan Badan ke Atas (Climb/Soar):** `Panah Atas` atau `Y`
  - **Kemudi Belok (Lean to Turn):** `Panah Kiri / Kanan` atau `A / D`
  - **Simulasi Aksi Desa:** Tombol `1` (Kepak Sayap), `2` / `F` (Semburan Air), `3` / `R` (Panggil Hujan)

---

## 8. STRUKTUR SCENE & FILE UTAMA

- **Scene Utama:** `Assets/_Scenes/Test_Dragon_Flight.unity` (Build Settings Index `0`).
- **Skrip C# (`Assets/_Scripts/`):**
  - `Controllers/DragonFlightController.cs`: Pengendali penerbangan Sky Peck 3D sejati, lift vertikal kepakan, akselerasi dive, Propeller Dash boost, konversi energi kinetik swoop soar, pelicinan kemudi lean, dan auto-hover desa.
  - `Controllers/IFlightInputProvider.cs`: Antarmuka modular penyedia perintah penerbangan standar (`FlightCommand`).
  - `Controllers/DefaultFlightInputMapper.cs`: Sistem pemeta input modular, kurva sensitivitas non-linear, deadzone, dan fatigue ease.
  - `CV_Receiver/PythonProcessManager.cs`: Pengelola otomatis proses background Python (`active_cv_sender.py`), auto-launch pada Play Mode, resolver path venv otomatis, dan safe termination tree.
  - `CV_Receiver/IPoseProvider.cs`: Interface abstraksi penyedia pose (mendukung Tuck Dive, Flapping, Lean to Turn, Propeller Dash, dan Siku).
  - `CV_Receiver/UDPPoseReceiver.cs`: Penerima data pose UDP 5052 (39-float), smoothing Vector2.Lerp, kalkulasi gabungan arm tilt + torso lean, dan keyboard fallback zero-conflict.
  - `UI/InGameFlightHUD.cs`: GUI telemetri kiri atas (Airspeed, Pitch, Alt, Sky Peck Mode, Python CV Status PID), kotak preview webcam PIP kanan atas (port 5053), radar kerangka bersiku, dan panduan kontrol bawah.
- **Skrip Python (`ARDragonSkyGuardian_CV/`):**
  - `active_cv_sender.py`: Pengirim pose resmi YOLOv8n-pose CUDA GPU + deteksi geometri Tuck Dive + Propeller Dash + Biometric Normalizer + streaming video JPEG PIP ke port 5053.
  - `pose_stabilizer.py`: RobustKeypointTracker, Adaptive One-Euro Filter, Dynamic Arm Clamping, dan WingFlapDetector.
  - `motion_recognizer.py`: Mesin pengenal gestur Sky Peck (Flap, Lean, Dive, Dash, Hover, Descent).
  - `player_calibrator.py`: Mesin kalibrasi biometrik pemain personal (State 0-5).
  - `udp_sender.py`: Helper socket UDP data pose 39-float & video JPEG PIP.
  - `test_send_dummy_udp.py`: Bench pengujian sintetis 4-fase Sky Peck (Glide -> Flap Climb -> Tuck Dive -> Swoop Climb & Lean).

---

## 9. ON-GROUND MECHANICS & ARM-PROXIMITY LOCOMOTION (UPGRADE v6.1)

### 9.1 Diagnosa Masalah Awal & Sasaran Desain
* **Akar Masalah:**
  1. Default spawn position `Dragon_Player` di scene berada pada ketinggian $Y=22$ (melayang di udara).
  2. Inisialisasi awal `_currentAirspeed = cruiseSpeed` ($14\text{ m/s}$) dan `_activeCommand.IsGliding = true` membuat naga seketika meluncur maju begitu game dimulai, sebelum kamera webcam mendeteksi pose pemain atau saat tracking belum siap.
  3. Perilaku terbang otomatis ini tidak realistis dan tidak memberi kesempatan bagi pemain untuk bersiap.
* **Tujuan Rekayasa:**
  1. Naga spawn langsung di atas daratan (**Grounded Spawn**, $Y = \text{groundSurfaceY} + \text{groundClearance}$) dalam keadaan diam sempurna ($0\text{ m/s}$).
  2. Naga hanya bergerak jika ada pose aktif yang terverifikasi dari pemain.
  3. Satu kerangka kendali tunggal yang konsisten: **Jarak Lengan Atas ke Torso (*Upper Arm Proximity / Openness*)**.
  4. Proteksi *tracking loss*: jika tracking hilang saat di darat, naga tetap berdiam di tanah (*safe state*).

---

### 9.2 Spesifikasi 12 State Terpadu (Ground & Airborne)

| State | Lingkungan | Syarat Gestur Tubuh | Karakteristik Kinematika & Kecepatan |
| :--- | :---: | :--- | :--- |
| **Grounded Idle** | Ground | Lengan atas rapat ke badan (*tucked*), tanpa kepakan. | Naga diam di darat. Forward Speed $= 0\text{ m/s}$, Vertical Speed $= 0\text{ m/s}$. |
| **Grounded Walk** | Ground | Lengan atas menjauh/terangkat (*away*). | Berjalan maju di darat. Forward Speed $= 3.5\text{ m/s}$ (`walkSpeed`), Vertical Speed $= 0\text{ m/s}$. |
| **Grounded Look Up** | Ground | Torso tilt ke atas / dada terangkat, di darat. | Mendongak ke atas (Pitch $+15^\circ$), diam di tempat ($0\text{ m/s}$), tidak terbang. |
| **Takeoff** | Transisi | Kepakan kuat di darat ($\text{flapPower} \ge 0.65$, durasi $\ge 0.25\text{ s}$). | Naik impulsif vertikal $+6 \sim 8\text{ meter}$ memasuki fase Airborne. |
| **Air Idle / Gentle Descent** | Air | Lengan atas rapat (*tucked*), lengan bawah terentang santai, tanpa kepak. | Meluncur santai ke depan sambil turun perlahan ($8 \sim 10\text{ m/s}$, sink $-1.5\text{ m/s}$) hingga menyentuh tanah. |
| **Hover** | Air | Lengan atas rapat (*tucked*) + kepakan lengan bawah (*forearm flap*). | Melayang stasioner di udara. Forward Speed $= 0\text{ m/s}$, Vertical Speed $\approx 0\text{ m/s}$ (hover bobbing). |
| **Forward Flight** | Air | Kepakan kedua tangan penuh secara ritmis (lengan atas *away*). | Daya dorong akseleratif ($12 \sim 21\text{ m/s}$) dan gaya angkat climb ($+6.5\text{ m/s}$). |
| **Glide** | Air | Lengan atas terangkat mendatar (*away*, T-pose udara) tanpa kepak. | Meluncur anggun. Kecepatan mengikuti sisa momentum dive/dash (meluruh ke $14\text{ m/s}$), sink $-1.0\text{ m/s}$. |
| **Fly Up** | Air | Torso tilt atas / dada terangkat + kepakan kedua tangan aktif. | Menanjak agresif (Pitch $+24^\circ$, Vertical Climb $+11\text{ m/s}$, Forward Speed hingga $21\text{ m/s}$). |
| **Fly Down (Pose Y)** | Air | Lengan atas menjauh (*away*) + lengan bawah mengarah ke atas (Pose "Y"). | Hidung menunduk turun sambil meluncur maju (Sink $-4.5\text{ m/s}$, Forward Speed $12\text{ m/s}$). |
| **Fly Down Idle (Pose W)** | Air | Lengan atas rapat (*tucked*) + lengan bawah mengarah ke atas (Pose "W"). | Turun perlahan di tempat (Forward Speed $= 0\text{ m/s}$, Sink $-2.5\text{ m/s}$). Terpisah dari Gentle Descent! |
| **Dive (Tuck Dive)** | Air | Tangan mengepal di dada/sternum, siku mengarah ke bawah di samping badan. | Menukik tajam (Pitch $-40^\circ \sim -45^\circ$), akselerasi gravitasi tinggi hingga $28\text{ m/s}$, momentum kinetik tinggi. |

> [!NOTE]
> **Asumsi Desain:** **Glide** (T-pose penuh, lengan atas away) dan **Gentle Descent** (lengan atas tucked, tanpa kepak) adalah dua state terpisah. Gentle Descent lebih lambat ($8\sim 10\text{ m/s}$) dan lebih ringan momentumnya dibanding Glide ($14\text{ m/s}$ cruise).

---

### 9.3 Matriks Input $\to$ Respon Aksi (Ground vs Air)

| Masukan Gestur | Respon Daratan (Ground) | Respon Udara (Airborne) | Parameter Kecepatan |
| :--- | :--- | :--- | :--- |
| **Upper Arm Tucked** | Naga berhenti total (**IDLE**) | **Gentle Descent** (turun pelan maju) ATAU **Hover** (jika forearm flap) | Ground: $0\text{ m/s}$<br>Air: $0\text{ m/s}$ / $9\text{ m/s}$ |
| **Upper Arm Away** | Naga bergerak maju (**WALK**) | **Glide** mempertahankan momentum luncur | Ground: $3.5\text{ m/s}$<br>Air: $14\text{ m/s}$ (cruise) |
| **Lean Tubuh (L/R)** | **Rotasi di tempat** (saat Idle) / **Belok sambil jalan** (saat Walk) | **Continuous Bank & Turn** (Yaw & Roll) | Ground: $65^\circ/\text{s}$<br>Air: $72^\circ/\text{s}$ |
| **Torso Tilt Up** | **Look Up** (mendongak di tempat tanpa terbang) | **Fly Up** (jika mengepak) menanjak ke langit | Ground: $0\text{ m/s}$<br>Air: Max $21\text{ m/s}$ |
| **Flapping Penuh** | Memicu **Takeoff** (jika $\ge 0.65$ power) | Gaya angkat & dorongan maju (**Forward Flight**) | Ground: $+6\text{ m/s}$ lift<br>Air: $+6.5\text{ m/s}$ lift |
| **Pose Y (Forearm Up + Arm Away)** | Pose netral / walk lambat | **Fly Down Maju** (turun sambil maju) | Maju $12\text{ m/s}$, Sink $-4.5\text{ m/s}$ |
| **Pose W (Forearm Up + Arm Tucked)** | Pose netral / idle | **Fly Down Idle** (turun pelan di tempat) | Maju $0\text{ m/s}$, Sink $-2.5\text{ m/s}$ |
| **Tuck Dive (Tangan di Dada)** | Pose netral / idle | **Dive Kecepatan Tinggi** menukik tajam | Akselerasi hingga $28\text{ m/s}$ |

---

### 9.4 Aturan Transisi & Prioritas Mesin State

1. **Takeoff (Ground $\to$ Air):**
   * Pemicu: Naga `isGrounded = true`, kepakan kuat terdeteksi `flapPower >= 0.65` selama minimal $0.25\text{ detik}$.
   * Efek: Memberikan dorongan impuls vertikal $+6 \sim 8\text{ meter}$ mengangkat naga dari tanah. Begitu terangkat di atas permukaan batas kontak, state berubah menjadi `isGrounded = false`. Ketinggian selanjutnya hanya dapat ditambah via **Fly Up** (dada terangkat + kepak ritmis).
2. **Landing (Air $\to$ Ground):**
   * Pemicu: Raycast permukaan daratan mendeteksi `groundDistance <= 0.35m` dan `verticalVelocity <= 0.5m/s`.
   * Efek: Transisi mulus ke `isGrounded = true`. Kecepatan maju diredam secara lembut via lerq friksi darat ke $0\text{ m/s}$ (jika lengan *tucked*) atau ke `walkSpeed` ($3.5\text{ m/s}$) (jika lengan *away*). Zero collision glitch / zero sudden stop.
3. **Safe State Tracking Loss:**
   * Ground: Jika tracking terputus / belum siap, naga mengunci state ke `Grounded Idle` ($0\text{ m/s}$ maju, $0\text{ m/s}$ rotasi).
   * Air: Jika tracking terputus, naga masuk ke `Gentle Descent` meluncur turun perlahan ke daratan.

---

### 9.5 Parameter Terkonfigurasi (Configuration Parameters)

* `walkSpeed`: $3.5\text{ m/s}$
* `groundTurnSpeed`: $65^\circ/\text{s}$
* `groundFrictionRate`: $5.0\text{ s}^{-1}$
* `takeoffClimbHeight`: $7.0\text{ m}$
* `takeoffFlapThreshold`: $0.65$
* `takeoffMinDuration`: $0.25\text{ s}$
* `groundContactOffset`: $0.35\text{ m}$
* `cruiseSpeed`: $14.0\text{ m/s}$
* `glideDecayRate`: $0.85\text{ m/s}^2$
* `diveMomentumRetention`: $0.85$
* `flyDownYSinkRate`: $4.5\text{ m/s}$
* `flyDownWSinkRate`: $2.5\text{ m/s}$
* `gentleDescentSpeed`: $9.0\text{ m/s}$
* `gentleDescentSinkRate`: $1.5\text{ m/s}$
* `flyUpLiftPower`: $11.0\text{ m/s}$
* `upperArmTuckedThreshold`: $0.38$
* `upperArmAwayThreshold`: $0.55$
* `proximityHysteresisDuration`: $0.15\text{ s}$
* `forearmUpAngleThreshold`: $65^\circ$
* `chestElevationThreshold`: $1.72$

---

### 9.6 DEFERRED — DRAGON ANIMATION & POSE PLAN (DOKUMENTASI PERENCANAAN)

> [!IMPORTANT]
> **Status Fitur:** **DITANGGUHKAN (*DEFERRED*)** — Dokumentasi konseptual saja. Tidak ada perubahan kode rigging, skeleton bone, atau animation layers pada fase lokomosi ini.

* **Wing Pose Mapping:** Menghubungkan rotasi siku dan pergelangan tangan pemain ke orientasi sayap naga secara dinamis.
* **Special Pose Override:** Mengunci sayap ke pose *tucked aerodynamic dive* saat dalam mode Dive.
* **Decoupled Architecture:** Sistem lokomosi dan input mapper sepenuhnya terisolasi dari animasi, sehingga penambahan visual controller di kemudian hari tidak akan merusak kalkulasi CV maupun fisika pergerakan.
