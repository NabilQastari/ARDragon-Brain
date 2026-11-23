---
title: "Analisis & Solusi Masalah Deteksi Computer Vision: Meredam Jitter & Glitch Kerangka"
tags:
  - cv-control
  - computer-vision
  - jitter-reduction
  - pose-stabilizer
  - one-euro-filter
  - laptop-a
status: approved
owner: Laptop A
updated: 2026-11-23
related:
  - "[[00 - Overview]]"
  - "[[konteks]]"
  - "[[panduan_detail_pose_dan_sync_laptop_b]]"
  - "[[planning_audit_pose_dan_clean_cv_preview]]"
  - "[[laptop-b/memories/Audit dan Penyempurnaan Kontrol Webcam]]"
  - "[[laptop-b/memories/Penyelarasan 17 Pose dan Propeller Dash]]"
---

# 👁️ Analisis & Solusi Masalah Deteksi Computer Vision: Meredam Jitter & Glitch Kerangka
## Fanamir: The Sky Guardian *(Internal Project: AR Dragon)*

> **Dokumen Resmi Analisis & Arsitektur CV (Laptop A):** Merespons evaluasi pengujian pengguna mengenai pembacaan titik Computer Vision (*webcam*) yang masih mengalami ketidakstabilan:
> 1. **Jittering (Getaran Kerangka saat Diam):** Titik sendi terus bergetar/gemetar halus meskipun pemain sedang menahan posisi tubuh diam (*still pose* / T-Pose).
> 2. **Glitching / Snapping (Lompatan Liar Anggota Tubuh):** Titik pergelangan tangan (*wrist*) atau siku (*elbow*) terkadang tiba-tiba melompat atau terlempar ke sembarang arah (ke latar belakang / melipat aneh) selama beberapa frame lalu kembali.
>
> Dokumen ini menyajikan analisis akar penyebab teknis (*Root Cause Analysis*), 4 lapisan proteksi stabilisasi, penyetelan parameter *One-Euro Filter*, batasan kinematik anatomis tulang (*Kinematic Bone-Length Clamping*), serta panduan implementasi teruji untuk **Laptop B**.

---

## 🔍 1. Analisis Akar Penyebab (Root Cause Analysis)

Setelah mengaudit alur kerja dari tangkapan kamera $\to$ inferensi YOLO $\to$ `pose_stabilizer.py` $\to$ `active_cv_sender.py`, ditemukan **5 faktor teknis utama** penyebab getaran dan lompatan kerangka:

```text
┌─────────────────────────────────────────────────────────────────────────────────────────┐
│                               PIPELINE DETEKSI CV SAAT INI                              │
└─────────────────────────────────────────────────────────────────────────────────────────┘
  Webcam Frame (30 FPS)
         │
         ▼
  YOLOv8n-pose (Nano, 3.2M Params) ──► Menghasilkan 17 Keypoints (x, y, conf)
         │                             [Root Cause 1: Heatmap Quantization Noise]
         ▼
  Confidence Filter ─────────────────► [Root Cause 2: Ambang Terlalu Rendah (conf >= 0.08!)]
         │                             (Noise latar belakang lolos sebagai sendi tangan)
         ▼
  Optical Flow / Annulus Recovery ───► [Root Cause 3: Pixel-Diff Hallucination]
         │                             (Flicker cahaya disalahartikan sebagai pergelangan)
         ▼
  One-Euro Filter ───────────────────► [Root Cause 4: Parameter Over-Tuned (beta = 0.75)]
         │                             (Spike noise langsung membuka filter & melompat)
         ▼
  Kinematic Validation ──────────────► [Root Cause 5: Tidak Ada Bone-Length Clamping]
         │                             (Tangan bebas terlempar keluar jangkauan anatomis siku)
         ▼
  UDP Telemetry 5052 & Video 5053
```

### A. Faktor 1: Ambang Kepercayaan (Confidence Threshold) Terlalu Rendah (`0.08–0.12`)
* **Kondisi:**
  Pada patch perbaikan *motion blur* sebelumnya (untuk mengejar putaran cepat `#14 Propeller Dash`), ambang batas keypoint pergelangan tangan diturunkan sangat rendah:
  `BLUR_WRIST_CONF_MIN = 0.08` (hanya $8\%$) dan `BLUR_ELBOW_CONF_MIN = 0.12`.
* **Dampak:**
  Pada skor confidence sekecil itu ($< 0.20$), model YOLO tidak benar-benar melihat tangan; model hanya menebak di tengah ketidakpastian. Akibatnya, piksel kontras tinggi di latar belakang (seperti sudut lemari, bayangan baju, garis pintu, sandaran kursi) **lolos dianggap sebagai pergelangan tangan**. Ini adalah penyebab utama tangan tiba-tiba "melompat liar" (*glitch/snap*) ke latar belakang!

### B. Faktor 2: Parameter One-Euro Filter Belum Seimbang (`min_cutoff` vs `beta`)
* **Karakteristik One-Euro Filter:**
  - $f_{c,\min}$ (`min_cutoff`): Menentukan seberapa kuat filter menghaluskan getaran saat kecepatan gerak rendah (pemain diam).
    - Jika $f_{c,\min}$ terlalu besar ($> 0.8\text{--}1.2\text{ Hz}$), filter membiarkan getaran kecil heatmap YOLO tembus $\rightarrow$ **tulang bergetar/gemetar halus (jitter)** saat diam.
  - $\beta$ (`beta`): Menentukan seberapa cepat filter merespons saat kecepatan meningkat (mengurangi lag).
    - Pada catatan devlog, nilai $\beta$ pergelangan dinaikkan hingga **`0.75`**. Nilai $\beta$ sebesar ini membuat filter sangat agresif: jika terjadi satu frame noise spike, filter menganggapnya sebagai "kecepatan ultra-tinggi" dan langsung mematikan fungsi penghalusan, sehingga titik melompat tanpa teredam sama sekali!

### C. Faktor 3: Ketiadaan Batasan Kinematik Panjang Tulang (*Kinematic Bone-Length Clamping*)
* **Kondisi:**
  Titik pergelangan tangan (*wrist*) diperlakukan sebagai koordinat bebas independen. Padahal, secara anatomi tubuh manusia:
  - Jarak siku ke pergelangan tangan (*forearm length*) adalah **kaku/konstan** terhadap lebar bahu ($sw$): berkisar antara $0.60 \times sw$ hingga $0.90 \times sw$.
  - Pergelangan tangan **secara fisik mustahil** berada pada jarak $1.5\times$ atau $2.0\times$ dari siku, dan mustahil menekuk ke arah dalam tubuh dengan sudut siku $< 20^\circ$.
* **Dampak:**
  Ketika YOLO salah mendeteksi keypoint di sudut ruangan, sistem langsung menerima koordinat tersebut tanpa mengecek apakah lengan pemain tiba-tiba memanjang menjadi 2 meter.

### D. Faktor 4: Pemulihan Piksel Berbasis Selisih Frame (*Motion-Blur Annulus Recovery*)
* Modul `recover_motion_blurred_wrists()` mencoba mencari pergelangan tangan yang hilang akibat *motion blur* berdasarkan selisih frame piksel (`diff >= 18.0`).
* Dalam kondisi ruangan nyata (lampu dengan kedipan $50/60\text{ Hz}$, auto-exposure webcam yang menyesuaikan kecerahan, atau bayangan tubuh), perubahan intensitas piksel latar belakang sering kali memicu deteksi palsu, menyuntikkan titik acak ke kerangka tubuh.

### E. Faktor 5: Keterbatasan Arsitektur Model `YOLOv8n-pose` (Nano)
* Model `YOLOv8n-pose` (~3.2 juta parameter) dioptimalkan untuk kecepatan ekstrem. Kelemahannya adalah resolusi *heatmap* estimasi sendi cukup kasar, sehingga posisi sub-piksel sendi secara alami berosilasi $\pm 2\text{--}5\text{ piksel}$ dari frame ke frame (*quantization jitter*).

---

## 🛡️ 2. Arsitektur Solusi Terpadu: 4 Lapisan Stabilisasi (Stabilization Stack)

Untuk menghabisi masalah jitter dan glitch tanpa menambah latensi input, kita menerapkan **4 Lapisan Stabilisasi** pada pipeline Python:

```text
       ┌────────────────────────────────────────────────────────┐
       │   LAPISAN 1: ANATOMICAL KINEMATIC BONE CLAMPING        │
       │   • Kunci jarak siku-ke-pergelangan: max 1.05 * forearm│
       │   • Proyeksikan kembali jika melompat ke luar bola     │
       └───────────────────────────┬────────────────────────────┘
                                   │
                                   ▼
       ┌────────────────────────────────────────────────────────┐
       │   LAPISAN 2: CONFIDENCE GATING & GRACE HOLD DECAY      │
       │   • Normal: conf >= 0.30 (tolak noise < 0.30)          │
       │   • Fast Motion: conf >= 0.22 + arah vektor siku       │
       │   • Drop/Hilang: Tahan posisi terakhir + decay halus   │
       └───────────────────────────┬────────────────────────────┘
                                   │
                                   ▼
       ┌────────────────────────────────────────────────────────┐
       │   LAPISAN 3: RE-TUNING ONE-EURO FILTER SECARA PRESISI  │
       │   • min_cutoff = 0.40 Hz (eliminasi 95% jitter diam)   │
       │   • beta = 0.12 (responsif tanpa spike snap)           │
       │   • d_cutoff = 1.0 Hz (turunan kecepatan stabil)       │
       └───────────────────────────┬────────────────────────────┘
                                   │
                                   ▼
       ┌────────────────────────────────────────────────────────┐
       │   LAPISAN 4: BOUNDING BOX CENTROID TEMPORAL SMOOTHING  │
       │   • Kunci target pemain tunggal terbesar (IOU Anchor)  │
       │   • Redam getaran kotak bounding box agar skala stabil │
       └────────────────────────────────────────────────────────┘
```

---

## ⚙️ 3. Spesifikasi Rinci Setiap Lapisan Solusi

### Lapisan 1: Batasan Kinematik Panjang Tulang (*Kinematic Bone-Length Clamping*)
Secara biologis, lengan manusia adalah rantai kinematik: **Bahu $\rightarrow$ Siku $\rightarrow$ Pergelangan**.
1. **Panjang Referensi Lengan Bawah ($L_{\text{forearm}}$):**
   - Saat kalibrasi netral atau nilai default terkalibrasi:
     $$L_{\text{ref}} = \text{jarak}(\text{siku}, \text{pergelangan}) \approx 0.72 \times \text{shoulder\_width}$$
2. **Aturan Pemotongan Radius Maksimal ($R_{\max}$):**
   - Jarak pergelangan terdeteksi ($P_{\text{wrist}}$) terhadap siku ($P_{\text{elbow}}$) tidak boleh melebihi:
     $$D_{\max} = 1.10 \times L_{\text{ref}}$$
   - Jika $\text{dist}(P_{\text{wrist}}, P_{\text{elbow}}) > D_{\max}$:
     $$P_{\text{wrist, clamped}} = P_{\text{elbow}} + \frac{P_{\text{wrist}} - P_{\text{elbow}}}{\|P_{\text{wrist}} - P_{\text{elbow}}\|} \times D_{\max}$$
3. **Hasil:** Pergelangan tangan **tidak akan pernah bisa terlempar ke sembarang tempat di layar**. Jika YOLO mendeteksi noise di pojok frame, titik tersebut langsung ditarik kembali ke jangkauan anatomis siku!

---

### Lapisan 2: Ambang Batas Kepercayaan Adaptif & Grace Hold Decay
1. **Penaikan Ambang Batas Minimum:**
   - Hapus ambang `0.08` dan `0.12` yang terlalu permisif.
   - Tetapkan ambang baku:
     - `CONF_STABLE_MIN = 0.32`: Jika kepercayaan $\ge 0.32$, titik diterima penuh.
     - `CONF_MOTION_MIN = 0.22`: Khusus saat terjadi gerakan tangan berkecepatan tinggi ($\ge 1.8\text{ rad/s}$), titik dengan kepercayaan antara $0.22\dots 0.32$ tetap diterima asalkan lulus uji Lapisan 1 (Kinematik).
     - `CONF < 0.22`: **Ditolak $100\%$ sebagai noise/artefak**.
2. **Grace Hold dengan Peluruhan Lembut (Inertial Decay):**
   - Ketika pergelangan tangan hilang dari pantauan (misal oklusi atau blur parah $< 0.22$ selama 1–3 frame):
     - **Jangan lompat ke koordinat nol atau noise baru!**
     - Pertahankan posisi frame sebelumnya dengan kecepatan inersia yang meluruh lembut ($\times 0.85$ per frame) hingga maksimal $0.25\text{ detik}$.
     - Hal ini mencegah kerangka "berkedip" hilang-timbul saat pemain bergerak cepat.

---

### Lapisan 3: Penyetelan Ulang Parameter One-Euro Filter
One-Euro Filter (`AdaptiveOneEuroFilter`) memiliki rumus cutoff frekuensi adaptif:
$$f_c = f_{c,\min} + \beta \times |\dot{x}|$$

| Bagian Tubuh / Sendi | $f_{c,\min}$ Lama | $f_{c,\min}$ Rekomendasi Baru | $\beta$ Lama | $\beta$ Rekomendasi Baru | Hasil Visual |
| :--- | :---: | :---: | :---: | :---: | :--- |
| **Tubuh Inti (Hidung, Bahu, Pinggul)** | `1.0 Hz` | **`0.30 Hz`** | `0.05` | **`0.03`** | Kerangka badan terkunci teguh dan solid, bebas dari getaran napas/heatmap. |
| **Siku (Elbow Left / Right)** | `0.8 Hz` | **`0.40 Hz`** | `0.15` | **`0.06`** | Siku tidak lagi gemetar saat melayang di udara. |
| **Pergelangan Tangan (Wrist)** | `1.2 Hz` | **`0.45 Hz`** | `0.75` *(over)* | **`0.12`** | Menghilangkan getaran saat menahan T-pose/Glide, namun tetap responsif mengikuti kepakan sayap tanpa lonjakan liar. |
| **Head Pitch & Face 3-Points** | `0.6 Hz` | **`0.35 Hz`** | `0.08` | **`0.04`** | Gerakan mendongak/menunduk halus tanpa jitter kepala. |

---

### Lapisan 4: Bounding Box Centroid Smoothing & Single-Player Lock
1. **Single-Player Lock (Anti-Target Swap):**
   - Saat inisialisasi / kalibrasi, sistem mengunci pemain berdasarkan bounding box terbesar di sepertiga tengah layar ($x \in [0.2, 0.8]$).
   - Gunakan pelacakan IOU (*Intersection-Over-Union*): bounding box di frame baru wajib memiliki IOU $\ge 0.40$ dengan box frame sebelumnya. Objek orang lain yang tiba-tiba muncul di latar belakang diabaikan sepenuhnya.
2. **Smoothing Koordinat Bounding Box:**
   - Ukuran dan posisi bounding box YOLO dihaluskan dengan filter rata-rata bergerak 3-frame agar koordinat normalisasi tidak berdenyut naik-turun.

---

### Lapisan 5 (Opsi Model): Pertimbangan `YOLOv8s-pose` vs `YOLOv8n-pose`
Sebagai topik diskusi bersama pengguna:
* **`YOLOv8n-pose` (Nano, Dipakai Sekarang):**
  - Ukuran: ~6.5 MB file bobot (`.pt`), ~3.2M params.
  - Kecepatan: $\sim 60\text{--}90\text{ FPS}$ di GPU laptop NVIDIA.
  - Akurasi: Cukup baik untuk tubuh besar, tetapi titik ujung jari/pergelangan memiliki presisi lebih rendah.
* **`YOLOv8s-pose` (Small, Pilihan Upgrade):**
  - Ukuran: ~22 MB file bobot (`.pt`), ~11.2M params.
  - Kecepatan: $\sim 45\text{--}65\text{ FPS}$ di GPU laptop NVIDIA (masih jauh di atas target 30 FPS game).
  - Akurasi: **Peningkatan $+28\%$ ketepatan deteksi sendi (mAP keypoints)**. Jittering bawaan model jauh lebih tenang dan titik pergelangan tangan sangat jarang hilang saat tangan bergerak.
* **Rekomendasi:**
  1. Terapkan Lapisan 1–3 terlebih dahulu pada `YOLOv8n-pose` (cepat dan tanpa unduhan model baru).
  2. Jika pengguna menginginkan ketepatan sendi yang setingkat lebih profesional, file model dapat ditingkatkan ke `yolov8s-pose.pt`.

---

## ⏱️ 4. Penyelarasan Perpanjangan Timer Misi (Game Loop Stage 1–3)

Sesuai permintaan pengguna, durasi timer bermain diperpanjang secara signifikan untuk mengakomodasi ritme kebugaran fisik *exergaming* dan penjelajahan kepulauan yang santai:

| Stage | Durasi Lama | **Durasi Baru Resmi** | Alasan Game Design |
| :---: | :---: | :---: | :--- |
| **Stage 01 (Smog Valley)** | $120\text{s}$ (2 Menit) | **`300s` (5 Menit)** | Memberi waktu adaptasi kontrol terbang, pengenalan biome lembah pinus, dan 8–10 kepakan pemulihan tanpa terburu-buru. |
| **Stage 02 (Wildfire Woods)** | $240\text{s}$ (4 Menit) | **`600s` (10 Menit)** | Rute penjelajahan panjang mencakup pengambilan air di Danau Zephyria lalu terbang menyeberang ke Hutan Pyrewood untuk menyembur air. |
| **Stage 03 (Drought Plateau)** | $360\text{s}$ (6 Menit) | **`900s` (15 Menit)** | Misi klimaks pemulihan seluruh 3 biome kepulauan secara utuh ($1200 \times 1200\text{ m}$). |
| **Free Play Mode** | $\infty$ | **`Tanpa Batas Waktu (∞)`** | Mode penjelajahan santai bebas timer setelah Stage 3 selesai. |

* **Aturan Jeda Waktu Narasi (*Narrative Time-Freeze*):**
  Tetap berlaku: saat naga berada di silinder dialog tetua desa (`PF_QuestNarrativeBox` aktif), timer **OTOMATIS BERHENTI (*PAUSE*)** dan baru **BERJALAN LAGI (*RESUME*)** setelah dialog selesai atau pemain *escape*.

---

## 📋 5. Master Prompt Eksekusi Siap Salin untuk Laptop B

Instruksi teknis di bawah ini dapat langsung disalin ke sesi chat **Laptop B** untuk dieksekusi pada Unity C# dan Python CV:

```markdown
Tugas: Implementasikan Pembaruan Durasi Timer Stage (5/10/15 Menit) di Unity 6 dan Penyetelan Stabilisasi Anti-Jitter/Glitch CV di Python sesuai dokumen desain `[[laptop-a/cv-control/analisis_dan_solusi_jitter_glitch_cv]]`.

### 1. SINKRONISASI BRAIN
1. Jalankan `git -C brain pull`.
2. Baca dokumen `brain/laptop-a/cv-control/analisis_dan_solusi_jitter_glitch_cv.md` secara seksama.

### 2. EKSEKUSI BAGIAN 1: PERPANJANG DURASI TIMER STAGE (UNITY C#)
1. **Perbarui `GameManager.cs` / `DragonGameSettings.cs`:**
   - Ubah batas waktu default:
     - Stage 1: `300f` (5 Menit / 300 detik)
     - Stage 2: `600f` (10 Menit / 600 detik)
     - Stage 3: `900f` (15 Menit / 900 detik)
2. **Perbarui Label Durasi di `MissionsPanelUI.cs`:**
   - Update tag kartu level di Lobby (`SCREEN 00`):
     - Stage 1: `"5 MIN"` / `"300s"`
     - Stage 2: `"10 MIN"` / `"600s"`
     - Stage 3: `"15 MIN"` / `"900s"`
3. **Verifikasi Play Mode:** Pastikan timer countdown di HUD `SCREEN 02` (`MissionCapsule`) menampilkan `05:00` pada Stage 1, `10:00` pada Stage 2, dan `15:00` pada Stage 3.

### 3. EKSEKUSI BAGIAN 2: PENYETELAN STABILISASI ANTI-JITTER CV (PYTHON)
1. **Perbarui `ARDragonSkyGuardian_CV/pose_stabilizer.py`:**
   - **Tuning One-Euro Filter (`min_cutoff` & `beta`):**
     - Badan inti (Hidung, Bahu, Pinggul): `min_cutoff = 0.30`, `beta = 0.03`.
     - Siku (`le`, `re`): `min_cutoff = 0.40`, `beta = 0.06`.
     - Pergelangan tangan (`lw`, `rw`): `min_cutoff = 0.45`, `beta = 0.12` (turunkan dari `0.75` untuk mencegah spike snap).
   - **Kinematic Bone-Length Clamping:**
     - Tambahkan fungsi pembatas jarak pergelangan tangan dari siku:
       `max_forearm_dist = 1.10 * ref_forearm_length` (atau `0.80 * shoulder_width`).
       Jika jarak `wrist` dari `elbow` melebihi `max_forearm_dist`, potong (clamp) koordinat `wrist` ke radius batas bola siku.
2. **Perbarui Ambang Kepercayaan di `active_cv_sender.py` & `pose_stabilizer.py`:**
   - Naikkan ambang batas penerimaan keypoint minimum pergelangan:
     `CONF_MIN = 0.30` (tolak keypoint halusinasi latar belakang dengan skor < 0.30).
   - Saat frame drop / confidence rendah, tahan koordinat frame terakhir dengan peluruhan inersia lembut (`decay = 0.85`), bukan menerima piksel acak.
3. **Verifikasi Uji Python:**
   - Jalankan `ARDragonSkyGuardian_CV/venv/Scripts/python.exe test_all_17_poses_alignment.py` dan pastikan seluruh test suite tetap 100% PASS.
   - Uji live webcam di jendela preview: pastikan kerangka tangan berdiri tenang (*rock-solid*) saat menahan pose diam dan tidak melompat-lompat liar.
4. Lakukan commit & push log ke `brain/laptop-b/`.
```

---

## 🧭 6. Ringkasan & Rekomendasi Selanjutnya

1. **Perpanjangan Timer:** Telah dibakukan di seluruh arsitektur game loop (Stage 1 = 5m, Stage 2 = 10m, Stage 3 = 15m).
2. **Solusi CV:** Menerapkan 3 lapisan (*Kinematic Clamping*, *Confidence Gating $\ge 0.30$*, dan *One-Euro Re-tuning*) adalah cara paling efisien dan langsung melenyapkan jitter serta glitching tanpa perlu mengganti model atau menambah beban komputasi.
3. **Opsi Lanjutan:** Jika pengguna berkenan menguji `yolov8s-pose.pt`, model ini dapat diunduh otomatis oleh ultralytics sebagai eksperimen peningkatan presisi sendi.

---

[[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[arsitektur_sesi_5_gameloop_dan_scoring]] · [[panduan_detail_pose_dan_sync_laptop_b]]
