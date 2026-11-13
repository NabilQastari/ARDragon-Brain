---
title: "Project Context & Technical Bible (v4.0)"
tags:
  - architecture
  - technical-bible
  - laptop-a
  - unity
  - python-cv
status: production-ready
owner: Laptop A
updated: 2026-11-13
related:
  - "[[00 - Overview]]"
  - "[[prd]]"
  - "[[rencana_desain_ui]]"
  - "[[gdd_peta_pulau]]"
---

# 🐉 PROJECT CONTEXT & TECHNICAL BIBLE: AR DRAGON SKY GUARDIAN (v4.0 — PRODUCTION READY)

> **Fungsi Dokumen:** Rujukan teknikal utama (*Single Source of Truth*) bagi kolaborasi antara Manusia (Developer) dan AI Agent (Hermes / Antigravity Agent).
> **Pasangan Dokumen:** Gunakan bersama [[prd]] untuk implementasi alur kerja berperingkat.
> **Status Implementasi:** Sistem navigasi penerbangan naga 3D (Y-T-A), integrasi model 3D pulau 800x800m, integrasi model naga `Dragon_Model.blend`, GUI HUD telemetri, dan streaming PIP webcam port 5053 telah **100% aktif dan terverifikasi**. Sesi 4 (Interaksi 3 Desa) ditunda sementara menunggu remodel daratan pulau; fokus aktif saat ini adalah **Sesi GUI (Menu Lobby, Tab Kalibrasi, & Finish Modal)** sesuai dokumen [[rencana_desain_ui]] dan folder [`Referensi UI/`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi%20UI/).

---

## 1. RINGKASAN KONSEP & IDENTITAS PROJEK

### 1.1 Identiti Gim
- **Nama Gim / Project Unity:** AR Dragon Sky Guardian (nama kode lama: `Dragon Wing AR`, dipakai pada namespace C# `DragonWingAR.*` dan menu Editor `DragonWingAR/...`)
- **Genre:** Exergaming PC berasaskan Webcam / 3D Semi-Open-World Flight Action / Edukasi Alam Sekitar
- **Enjin & Stack:**
  - **Unity:** Unity 6 (`6000.3.7f1`) Universal Render Pipeline (URP)
  - **Python:** Python 3.10+ (PyTorch CUDA, YOLOv8n-pose GPU, OpenCV 4.x)
  - **Blender:** Blender 5.1 / 5.2 CLI pipeline
- **Watak Utama:** Naga Penjaga Langit Elemen Air & Embun Purba (`Dragon_Model.blend` / `Dragon_Model.fbx`)
- **Misi Utama:** Menjelajah pulau daratan besar 800x800m dan memulihkan 3 desa daripada pencemaran udara, kebakaran hutan, dan kemarau menggunakan gestur tubuh interaktif di hadapan webcam sebelum masa tamat.

### 1.2 Matriks Pembahagian Tugas (Semi-Autonomous Workflow)

| Kategori Tugas | Pelaksana Utama | Tanggungjawab & Kolaborasi |
| :--- | :--- | :--- |
| **Skrip Python CV & UDP Streaming (5052 & 5053)** | 🤖 **AI Agent** | Menulis modul inferensi YOLOv8n-pose, One-Euro filter, head-crop mouth tracker, dan streaming JPEG PIP. |
| **Skrip C# Unity (Navigasi 3D, Pose Y-T-A, HUD, UI)** | 🤖 **AI Agent** | Menulis keseluruhan kod C# bebas ralat (*0 Compiler Errors*), penyesuaian URP Lit, dan proteksi raycast tanah. |
| **Skrip Pembantu Editor (`[MenuItem]`)** | 🤖 **AI Agent** | Menyediakan menu automatik di bar atas Unity (`DragonWingAR -> ...`) untuk setup pad, collider, dan scene sekali klik. |
| **Integrasi & Skala Aset 3D (`.blend` & `.fbx`)** | 🤖 **AI Agent + User** | Ekspor Blender CLI dengan bake transforms, pengaturan skala naga 5m dan pulau 800m di scene Unity. |
| **Ujian Play Mode & Kalibrasi Webcam Nyata** | 👨‍💻 **Manusia (User)** | Menguji gerakan tubuh di depan webcam, validasi responsivitas gameplay, dan evaluasi pengalaman bermain. |

---

## 2. PRINSIP DESAIN & ARSITEKTUR TEKNIS

### 2.1 Prinsip "Produksi Mudah & Anti-Bug"
1. **Satu Scene Pulau untuk Semua Level (`Assets/_Scenes/Test_Dragon_Flight.unity` / `2_MainIslandScene.unity`):**
   - Hanya 1 Scene pulau utama yang mengandungi Desa 1, Desa 2, dan Desa 3.
   - `GameManager.cs` mengawal status aktif desa (`SetActive(true/false)`) berdasarkan level yang dipilih dari UI Lobby.
2. **Tiada Aset 3D Berganda (Rosak vs Pulih):** Gunakan 1 set model 3D bagi setiap desa, dan ubah keadaannya secara realtime melalui kod C#:
   - **Desa 1 (Polusi Udara):** Kurangkan `emission.rateOverTime` pada *Particle System* asap hitam (`VFX_BlackSmoke`) daripada `50f` kepada `0f` mengikut peratusan progres desa (`0% -> 100%`).
   - **Desa 2 (Kebakaran Hutan):** Kecilkan `transform.localScale` pada *Particle System* api (`VFX_Fire`) daripada `Vector3.one` kepada `Vector3.zero` serentak dengan semburan air purba (`VFX_WaterBreath`).
   - **Desa 3 (Kekeringan Tanah):** Munculkan hujan embun (`VFX_DewRain`) dan tukar warna *Material* tanah dan pokok daripada coklat kering (`#8B5A2B`) kepada hijau subur (`#2E8B57`) menggunakan `Color.Lerp(dryColor, lushColor, localProgress / 100f)`.

### 2.2 Spesifikasi Karakter Naga (`Dragon_Model.blend` / `Dragon_Model.fbx`)
- **Aset 3D:** Model naga resmi menggunakan `Dragon_Model.blend` yang diekspor menjadi `Dragon_Model.fbx` di folder `Assets/Assets 3D/` (remodel naga v3).
- **Dimensi Fisik Nyata (1 Unity unit = 1 meter):**
  - **Bentang Sayap (X):** $5.06\text{ meter}$
  - **Panjang Tubuh (Z):** $3.60\text{ meter}$ (dari moncong kepala ke ujung ekor)
  - **Tinggi (Y):** $1.59\text{ meter}$
  - **Pusat Pivot:** Di-center tepat pada titik `(0, 0, 0)` koordinat lokal pemain.
- **Material & Shading:**
  - 12 Material **URP Lit** dengan mode *Double-Sided Rendering* (`_Cull = 0`) agar membran sayap dan sirip ekor tampak solid dari kedua sisi.
  - Palet warna: Teal body (`M_Dragon_Body_Teal`), turquoise wings (`M_Dragon_Wing_Membrane`), cream belly (`M_Dragon_Belly_Cream`), amber eyes (`M_Dragon_Eye_Amber`), dark dorsal ridges (`M_Dragon_Dorsal_DarkTeal`), and crystal chest (`M_Dragon_Chest_Crystal`).
- **Kamera Follow (Third-Person Cinematic):**
  - Parent: Child kepada `Dragon_Player`
  - `localPosition`: `(0.00, 2.50, -6.50)`
  - `localEulerAngles`: `(14.00, 0.00, 0.00)`
  - FOV: $60^\circ$ | Far Clip Plane: $1500\text{ m}$

### 2.3 Desain Daratan Kepulauan Baru (Tight-Knit Caldera Archipelago)
*Rujukan Visual Resmi:* [`assets/upgrade model pulau/Peta_Denah_Resmi_Genshin_Style.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/assets/upgrade%20model%20pulau/Peta_Denah_Resmi_Genshin_Style.jpg)

Model pulau telah ditingkatkan dari satu balok persegi 800x800m menjadi **Kepulauan Kompak 4 Wilayah (*Tight-Knit Caldera Archipelago*)** bergaya peta Genshin Impact dengan perairan laut biru teal dan daratan hijau alami yang dominan:
- **Dimensi Bounding Box:**
  - Skala Total: **$1200\text{ m} \times 1200\text{ m}$** (Rasio 1:1, pusat di `(0, 0, 0)`), memberikan ruang terbang luas dan megah.
  - Pemisah Wilayah: Selat laut dan kanal air sempit (*narrow saltwater straits*) yang menghubungkan lautan luar ke pusat.
- **Empat Zona Wilayah Utama:**
  1. **CENTER (Gunung Naga Suci - Dragon Spawn Peak):**
     - Terletak persis di tengah peta pada koordinat `(0, Y, 0)`.
     - Berupa pegunungan hijau megah dengan undakan kontur melingkar dan puncak tertinggi tempat naga *spawn* dan lepas landas.
  2. **BIOME 1 (Smog Valley - Barat Laut):**
     - Wilayah perbukitan hijau subur dengan 2–3 danau biru alami berukuran besar.
     - Mengalami bencana kabut asap polusi udara kelabu pekat. Lokasi **Desa 1 (Polusi Udara)**.
  3. **BIOME 2 (Wildfire Woods - Timur):**
     - Wilayah perbukitan dan hutan hijau yang bertransisi menjadi tanah gosong/arang kehitaman dengan semburat bara api oranye (*ember wash*).
     - Lokasi **Desa 2 (Kebakaran Hutan)**.
  4. **BIOME 3 (Drought Plateau - Selatan):**
     - Wilayah dataran tinggi hijau yang memudar menjadi tanah retak kering kekuningan (*drought-cracked tan-yellow plateau*).
     - Lokasi **Desa 3 (Kekeringan Tanah)**.
- **Urutan Alur Kerja:** Pemodelan 3D di Blender akan dieksekusi pada **Sesi Pembaruan Model Pulau** setelah seluruh sistem UI selesai, diawali dengan *render concept sheet* per biome.

---

## 3. SISTEM NAVIGASI TERBANG DINAMIS & GESTUR ALFABET (Y - T - A)

Penerbangan naga telah ditingkatkan dari *fixed altitude* menjadi **Sistem Navigasi 3D Penuh** berbasis gestur alfabet:

```text
      \   /               — o —              /   \
       \o/                  |               /  o  \
     POSE Y               POSE T            POSE A
    (NAIK ↑)          (LURUS MENDATAR)     (TURUN ↓)
  Pitch Input > 0      Pitch Input = 0   Pitch Input < 0
   +8 ~ +10 m/s           0.0 m/s         -10 ~ -12 m/s
```

### 3.1 Detail Tiga Sikap Pose Alfabet
1. **Pose Y (Naik Menanjak $\uparrow$):**
   - **Bentuk Gestur:** Kedua tangan diangkat ke atas membentuk huruf **Y** (pergelangan tangan di atas bahu).
   - **Logika Deteksi:** `(dyL < -0.025f && dyR < -0.025f) || avgDeltaY < -0.035f || dyL < -0.06f || dyR < -0.06f`.
   - **Aksi Naga:** Ketinggian bertambah secara mulus (`climbSpeed = 10f`), kepala naga mendongak visual (Pitch $-18^\circ$ hingga $-22^\circ$), kecepatan maju sedikit berkurang (efek hambatan gravitasi).
2. **Pose T (Meluncur Lurus Mendatar $\rightarrow$):**
   - **Bentuk Gestur:** Kedua tangan direntangkan mendatar sejajar bahu membentuk huruf **T**.
   - **Logika Deteksi:** Tangan berada dalam *deadzone* bahu (antara $-0.03f$ dan $+0.04f$).
   - **Aksi Naga:** Naga meluncur mendatar pada ketinggian jelajah saat ini (`0.0 m/s` vertikal), kecepatan maju stabil $12.0\text{ m/s}$, kepala mendatar rata ($0^\circ$).
3. **Pose A (Menukik Turun $\downarrow$):**
   - **Bentuk Gestur:** Kedua tangan diarahkan serong ke bawah ke sisi paha membentuk huruf **A**.
   - **Logika Deteksi:** `(dyL > 0.035f && dyR > 0.035f) || avgDeltaY > 0.045f || dyL > 0.06f || dyR > 0.06f`.
   - **Aksi Naga:** Naga menukik tajam (`diveSpeed = 12f`), kepala naga menunduk visual (Pitch $+18^\circ$ hingga $+22^\circ$), akselerasi *Dive Speed Boost* otomatis aktif (kecepatan maju meningkat hingga $16.2\text{ m/s}$).

### 3.2 Kemudi Belok & Kemiringan Tubuh (Yaw Turn & Roll Banking)
- **Kemudi Belok:** Berdasarkan selisih ketinggian tangan: `float tiltDelta = LeftWrist.y - RightWrist.y;`
- **Deadzone:** Jika `Mathf.Abs(tiltDelta) < 0.08f` $\rightarrow$ Kemudi lurus.
- **Sensitivitas:** `turnInput = Mathf.Clamp(tiltDelta * 2.5f, -1f, 1f)`.
- **Putaran Yaw:** $65^\circ/\text{detik}$ di bidang horizontal (`transform.Rotate(Vector3.up * turnInput * 65f * Time.deltaTime)`).
- **Roll Banking Visual:** Model naga miring secara visual pada sumbu Z sebesar `-turnInput * 35f` derajat.

### 3.3 Proteksi Ketinggian Daratan (Raycast Ground Clearance Protection)
- Naga dilengkapi sensor raycast otomatis ke bawah sejauh 500 meter.
- **Plafon Minimum Dinamis:** `safeMinAltitude = Mathf.Max(5f, terrainHeight + 2.5f)`.
- Naga tidak akan pernah tembus atau tertimbun ke dalam tanah bukit pulau saat melakukan aksi menukik Pose A.
- **Plafon Maksimum:** `maxAltitude = 140f` (memungkinkan naga terbang bebas melintasi puncak gunung tertinggi $118.9\text{ m}$).

---

## 4. PROTOKOL JARINGAN UDP GANDA (DUAL-PORT UDP)

Modul Python dan Unity berkomunikasi melalui 2 port socket UDP terpisah berkecepatan tinggi:

```text
[Python CV Sender]
   ├── UDP 127.0.0.1:5052 (Pose CSV Telemetri)  ───────> [UDPPoseReceiver.cs]
   └── UDP 127.0.0.1:5053 (JPEG Frame PIP Video) ──────> [InGameFlightHUD.cs]
```

### 4.1 Port 5052: Telemetri Pose Tubuh (13-Float CSV)
- **Host & Port:** `127.0.0.1:5052`
- **Frekuensi:** $\ge 30\text{ FPS}$ (latensi AI $\approx 10-15\text{ ms}$ pada CUDA GPU)
- **Format String CSV (13 nilai float ternormalisasi `0.0000` s/d `1.0000`):**
  ```text
  LS_X,LS_Y,RS_X,RS_Y,LW_X,LW_Y,RW_X,RW_Y,LH_X,LH_Y,RH_X,RH_Y,MOUTH_OPEN
  ```
  - `0..1` = Bahu Kiri (LS)
  - `2..3` = Bahu Kanan (RS)
  - `4..5` = Pergelangan Tangan Kiri (LW)
  - `6..7` = Pergelangan Tangan Kanan (RW)
  - `8..9` = Pinggul Kiri (LH)
  - `10..11` = Pinggul Kanan (RH)
  - `12` = Nilai Bukaan Mulut `MOUTH_OPEN` ($0.0 = \text{Tutup}, 1.0 = \text{Buka Lebar}$)

### 4.2 Konvensi Koordinat Layar & Smoothing
- Citra webcam di Python wajib dibalik secara horizontal (`cv2.flip(frame, 1)`).
- **Paksi X:** `0.0` (Kiri Layar) hingga `1.0` (Kanan Layar).
- **Paksi Y:** `0.0` (Atas Layar) hingga `1.0` (Bawah Layar). Semakin tinggi tangan diangkat di atas kepala, semakin **KECIL** nilai Y.
- **Smoothing Unity:** Seluruh titik dihaluskan setiap frame menggunakan:
  ```csharp
  smoothedPoint = Vector2.Lerp(smoothedPoint, rawPoint, Time.deltaTime * 12f);
  ```

### 4.3 Port 5053: Streaming Video Webcam PIP (Picture-in-Picture)
- **Host & Port:** `127.0.0.1:5053`
- **Format Paket:** Raw byte buffer gambar JPEG terkompresi ($320 \times 180$ piksel, *quality* 48, ukuran paket $\approx 3-4\text{ KB} < 65\text{ KB}$ MTU UDP).
- **Fungsi di Unity:** Didekodekan langsung oleh `InGameFlightHUD.cs` menggunakan `Texture2D.LoadImage(bytes)` dan ditampilkan di kotak preview kanan atas layar.
- **Fallback Otomatis:** Jika Python video stream belum aktif, kotak PIP secara otomatis menampilkan radar kerangka (*interactive skeleton wireframe*) dengan badge status pose Y-T-A.

---

## 5. KONTROL AKSI DESA & AUTO-HOVER

Ketika naga terbang mendekati dan memasuki area *Trigger Collider* desa (`SphereCollider`, radius 28-32m):
1. **Auto-Hover:** Naga berhenti maju otomatis dan memutar badan secara anggun menghadap pusat desa dengan osilasi mengambang (*hover bobbing* $\pm 0.4\text{ m}$).
2. **Eksekusi Gestur Interaktif (5 detik = 100% Selesai, +20%/detik):**

| Desa & Masalah | Aksi Naga | Gestur Pemain | Logika Pengesanan C# |
| :--- | :--- | :--- | :--- |
| **Desa 1 (Polusi Udara)** | Mengibas Kabut Asap | Kepakkan kedua tangan naik-turun melewati bahu | Status `IsFlapping` aktif saat tangan berganti posisi di atas dan bawah bahu secara berulang (`flapTimer = 0.6f`). |
| **Desa 2 (Kebakaran Hutan)** | Semburan Air Purba | Buka mulut lebar (`MouthOpen > 0.45`) ATAU tangkupkan kedua tangan di dada | Status `IsWaterBreathing` aktif (`MouthOpen > 0.45f \|\| Vector2.Distance(LW, RW) < 0.14f`), mematikan partikel api desa. |
| **Desa 3 (Kekeringan Tanah)** | Memanggil Hujan Embun | Angkat kedua tangan tinggi ke angkasa di atas kepala | `IsHandsHighUp` aktif saat `LW.y < LS.y - 0.14f` dan `RW.y < RS.y - 0.14f`. |

3. **Penyelesaian:** Begitu progres lokal mencapai 100%, status Auto-Hover dilepaskan dan naga kembali dapat terbang menjelajah ke desa berikutnya.

---

## 6. SISTEM KONTROL KEYBOARD HYBRID (*ZERO-CONFLICT*)

Untuk menjamin kelancaran pengujian kapan pun tanpa kamera, sistem keyboard fallback dirancang dengan aturan isolasi ketat:

- **Aturan Isolasi:** Jika data kamera UDP terhubung dan pengguna tidak menekan tombol fisik, keyboard **dilarang keras menimpa data gestur kamera**.
- **Pemetaan Tombol Keyboard:**
  - **Naik $\uparrow$ (Pose Y):** `W`, `Y`, `Panah Atas`, `Spasi`
  - **Turun $\downarrow$ (Pose A):** `S`, `A`, `Panah Bawah`
  - **Lurus Mendatar (Pose T):** `T` (atau lepas tombol)
  - **Kemudi Belok:** `Panah Kiri / Kanan`, `D`, `Q / E`
  - **Simulasi Aksi Desa:**
    - Tombol `1`: Simulasi Kepakan Sayap (Desa 1)
    - Tombol `2` / `F`: Simulasi Semburan Air (Desa 2)
    - Tombol `3` / `R`: Simulasi Panggil Hujan (Desa 3)

---

## 7. STRUKTUR LEVEL, WAKTU, & SISTEM BINTANG

| Level | Desa Aktif | Batas Waktu | Kontribusi Progres Global | Syarat Buka Kunci |
| :--- | :--- | :-: | :-: | :--- |
| **Level 1 (Pemula)** | Desa 1 (Polusi) | 90 saat | 100% per desa | Terbuka sejak awal |
| **Level 2 (Menengah)** | Desa 1 + Desa 2 | 150 saat | 50% per desa | Bintang Level 1 $\ge 1$ |
| **Level 3 (Ahli)** | Desa 1 + Desa 2 + Desa 3 | 210 saat | 33.33% per desa | Bintang Level 2 $\ge 1$ |

**Aturan Penilaian Bintang:**
- Selesai sebelum batas waktu & `GlobalProgress >= 100%` $\rightarrow$ **3 Bintang** ⭐⭐⭐ (Membuka level berikutnya).
- Waktu habis & `GlobalProgress >= 67%` $\rightarrow$ **2 Bintang** ⭐⭐ (Membuka level berikutnya).
- Waktu habis & `GlobalProgress >= 34%` $\rightarrow$ **1 Bintang** ⭐ (Membuka level berikutnya).
- Waktu habis & `GlobalProgress < 34%` $\rightarrow$ **0 Bintang / Gagal** (Level berikutnya tetap terkunci).

---

## 8. STRUKTUR SCENE & FILE UTAMA

- **Scene Utama:**
  - `Assets/_Scenes/Test_Dragon_Flight.unity` (Build Settings Index `0` — scene aktif verifikasi v4.0).
  - `Assets/_Scenes/2_MainIslandScene.unity` (Scene rujukan arsitektur pulau final).
- **Skrip C# Unity (`Assets/_Scripts/`):**
  - `Controllers/DragonFlightController.cs`: Pengendali penerbangan 3D, manuver Y-T-A, dive boost, attitude roll/pitch, ground raycast, dan auto-hover.
  - `Controllers/VillageZoneController.cs`: Deteksi trigger desa, state auto-hover desa, validasi gestur, penambahan progres +20%/s, dan kontrol visual VFX.
  - `CV_Receiver/IPoseProvider.cs`: Interface abstraksi penyedia data pose.
  - `CV_Receiver/UDPPoseReceiver.cs`: Penerima data pose UDP 5052, smoothing, deteksi Y-T-A, dan keyboard fallback zero-conflict.
  - `UI/InGameFlightHUD.cs`: GUI telemetri kiri atas, kotak preview webcam PIP kanan atas (port 5053), radar kerangka, dan panduan kontrol bawah.
  - `Managers/GameManager.cs`: Pengendali alur game, pemilihan level, timer countdown, akumulasi progres global, dan evaluasi bintang.
- **Skrip Python (`ARDragonSkyGuardian_CV/`):**
  - `active_cv_sender.py`: Pengirim pose resmi YOLOv8n-pose CUDA GPU (port 5052) + streaming video JPEG PIP (port 5053).
  - `udp_sender.py`: Helper socket UDP data pose & video buffer.
  - `test_send_dummy_udp.py`: Bench pengujian sintetis siklus Y-T-A tanpa kamera.


---
**Navigasi Obsidian:** [[00 - Overview]] · [[prd]] · [[rencana_desain_ui]] · [[gdd_peta_pulau]] · [[Tasks]] · [[Devlog]]
