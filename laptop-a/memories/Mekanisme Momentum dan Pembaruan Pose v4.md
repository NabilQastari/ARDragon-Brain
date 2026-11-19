---
title: Mekanisme Momentum, Pembaruan Pose v4, & Paket Delta Ekspor
type: session-memory
tags:
  - session-memory
  - laptop-a
  - flight-physics
  - momentum
  - pose-v4
  - cv-control
  - ui-hud
  - delta-export
date: 2026-11-18
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[planning_momentum_dan_revisi_kontrol]]"
  - "[[panduan_detail_pose_dan_sync_laptop_b]]"
  - "[[mekanisme_tantangan_desa]]"
  - "[[checklist_finalisasi_pra_sesi_4]]"
  - "[[panduan_screenshot_mandiri_dan_laporan]]"
---

# 🧠 Memori Sesi — Mekanisme Momentum, Pembaruan Pose v4, & Paket Delta Ekspor

## 1. Tujuan & Konteks Sesi
- **Proyek:** *Fanamir: The Sky Guardian* (namespace C# internal `DragonWingAR.*`), workspace **Laptop A** (fokus desain, ide, arsitektur, & aset visual).
- **Konteks & Feedback Pengguna:**
  1. **Uji Coba Webcam di Laptop B:** Pengguna mengonfirmasi bahwa dari 17 pose yang ada, **6 pose telah lolos uji dan sudah sesuai**: `#01 Grounded Idle`, `#02 Grounded Walk`, `#04 Takeoff`, `#05 Lean & Arm Tilt`, `#08 Glide`, dan `#13 Tuck to Dive`. Sisanya masih kurang pas dan perlu disempurnakan tanpa merusak ke-6 pose yang sudah lolos uji (*zero regression*).
  2. **Penyempurnaan UI Game (Menu Kalibrasi & Guide):** Ikon panduan pose di menu Kalibrasi (`SCREEN 01`) dan modal `Pose Guide` (`SCREEN 07`) harus berupa **siluet bersih UI tanpa kerangka dan tanpa titik CV**.
  3. **Revisi Mekanisme Penerbangan (Sistem Momentum Kontinu):** Menambahkan variabel kontinu `Momentum (0–100%)` berbasis hukum inersia di mana naga tidak langsung berhenti saat direm, penambahan momentum berlangsung bertahap dengan *soft-cap* per pose, navigasi `#07 Fly Up (Y-Pose)` bertindak sebagai *Y-Glide Up* (butuh momentum $>0\%$ dan sayap 3D membentuk huruf Y), menanjak tanpa momentum dilakukan via angkat dagu sedikit + kepakan (`#06B Steep Climb`), dan `#09 Hover` memerlukan kepakan lengan bawah (*flutter*) untuk rem aktif ke $0\text{ m/s}$ (L-pose diam meluncur perlahan `~6–8 m/s`).
  4. **Manajemen Aset Delta & Sinkronisasi:** Menyediakan folder dan berkas ZIP khusus aset yang belum/berubah dari ekspor sebelumnya (`Export_Delta_LaptopB.zip`), buku panduan visual resmi `v4.0` (12 halaman), serta prompt sinkronisasi lengkap untuk agen Laptop B.

---

## 2. Keputusan Desain / Arsitektur / Teknis

### A. Parameter & Dinamika Fisika Momentum Kontinu
- **Variabel Kontinu:** `Momentum` berada pada rentang $[0.0, 1.0]$ ($0\% \dots 100\%$).
- **Kecepatan Maju (Inersia):** $V_{\text{fwd}} = V_{\text{min}} + (V_{\text{max}} - V_{\text{min}}) \times \text{Momentum}$.
  - Pada `#08 Glide`: $V_{\text{fwd}}$ berskala dari $14\text{ m/s}$ ($0\%$ momentum) hingga $27\text{ m/s}$ ($100\%$ momentum).
  - Naga tidak langsung berhenti saat pose netral/glide; ia terus melaju memanfaatkan sisa inersia.
- **Aturan Penambahan Berangsur (*Gradual*) & Soft-Cap:**
  - `#04 Takeoff`: $+8.0\%/\text{kepakan}$, *soft-cap* $15\%$.
  - `#06 Flap to Climb`: $+4.0\%/\text{kepakan}$, *soft-cap* $30\%$.
  - `#06B Steep Climb (Chin-Up Flap)`: $+2.5\%/\text{kepakan}$, *soft-cap* $20\%$.
  - `#11 Fly Down Y-Dive`: $+8.0\%/\text{detik}$, *soft-cap* $55\%$.
  - *Aturan Soft-Cap:* Jika momentum saat ini sudah berada di atas batas *cap* (misal $80\%$ dari *Tuck Dive*), pose dengan *cap* rendah tidak akan memangkas/menurunkan momentum ke batasnya.
- **Pengisi Penuh ($100\%$ Maksimal):**
  - `#13 Tuck to Dive`: $+16.0\%/\text{detik}$ (menukik curam mengisi hingga $100\%$).
  - `#14 Propeller Dash`: $+7.0\%/\text{putaran}$ lengan bawah (dapat diulang berkali-kali hingga $100\%$).
- **Hukum Pengereman & Peluruhan (*Decay*):**
  - *Natural Cruise Decay:* $-3.0\%/\text{detik}$ saat meluncur biasa.
  - *Air Brake `#09A Hover` & `#12 W-Pose`:* $-40.0\%/\text{detik}$ (~$2.2\dots 2.5\text{ detik}$ melambat halus hingga $0\text{ m/s}$, tidak *snap* instan).
  - *Auto-Hover Radius Desa ($R=35\text{ m}$):* Assisted braking lembut melambat dalam waktu ~$1.8\text{ detik}$.

### B. Pemisahan Tiga Mode Menanjak & Rigging Sayap 3D
1. **`#06 Flap to Climb` (Dagu Netral):** Kepakan biasa menanjak landai ($+3.5\text{ m/s}$) tanpa memerlukan momentum awal.
2. **`#06B Steep Climb` (Angkat Sedikit Dagu + Kepak):** Kombinasi `HEAD_PITCH > 0.12f` + kepakan tangan. Menanjak curam ($+7.0\text{ m/s}$) kapan saja tanpa butuh momentum, berguna untuk manuver darurat melewati tebing kaldera.
3. **`#07 Fly Up (Y-Pose)` $\rightarrow$ `Y-Glide Up`:**
   - **Wajib Momentum $>0\%$:** Mengonsumsi momentum sebesar $-25.0\%/\text{detik}$.
   - **Visual Sayap 3D:** Sayap naga diatur membentuk dihedral huruf Y yang megah (`DragonWingAnimator.cs`).
   - **Gentle Stall:** Saat momentum habis ($0\%$), naga mengalami *stall* halus: hidung sedikit menunduk landai, turun perlahan $-1.2\text{ m/s}$ (tidak jatuh bebas).

### C. Pemisahan `#09A Hover` vs `#09B Slow Glide`
- **`#09A Hover Mode` (L-Pose + Kepakan Lengan Bawah / *Flutter*):** Lengan atas horizontal/menempel, siku menekuk $90^\circ$, lengan bawah mengepak kecil aktif. Bertindak sebagai *Active Air Brake* meluruhkan kecepatan maju ke $0\text{ m/s}$.
- **`#09B Slow Glide` (L-Pose Diam):** L-pose ditahan diam tanpa kepakan lengan bawah. Naga tetap meluncur santai di udara dengan kecepatan stabil $\sim 6\dots 8\text{ m/s}$.

### D. Integrasi UI HUD `SCREEN 02` & Medali Bersih
- **`Momentum Dock` (`SCREEN 02`):** Wadah dock $420 \times 40\text{ px}$ di atas *Telemetry Dock*. Menampilkan bar meteran momentum dari komponen modular `UI_HUD_Momentum_Track_206x20.png` dan `UI_HUD_Momentum_Fill_200x14.png`, dilengkapi badge status `Y-OK` (emas/hijau saat momentum $>0\%$) atau `EMPTY` (merah redup).
- **Medali Bersih Menu Kalibrasi & Guide (`SCREEN 01` & `SCREEN 07`):** Ke-15 medali pose (`UI_Pose_01..15.png`) diperbarui dengan siluet vektor minimalis murni tanpa titik koordinat CV maupun kerangka kawat, sesuai preferensi visual UI game RPG.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset

1. **Aset Vektor Computer Vision (`assets/poses_cv_vector/`):**
   - Dibuat di FigCraft Page `Pose`: `Pose_06B_SteepClimb_ChinUpFlap.png` (37,955 B), `Pose_09B_SlowGlide_LPose.png` (23,795 B), pembaruan `Pose_09_Hover.png` (26,365 B, dilengkapi panah *flutter* lengan bawah), dan lembar ringkas lengkap **`All_19_Poses_Sheet.png`** (619,873 B).
2. **Aset UI Unity & FigCraft (`UI AR Dragon.figcraft` — 2,868,089 B):**
   - Ditambahkan komponen HUD baru pada Section `05_HUD`: `UI_HUD_Momentum_Track_206x20.png` (447 B) dan `UI_HUD_Momentum_Fill_200x14.png` (306 B).
   - Diperbarui 15 medali siluet bersih di `assets/UI_Unity_Export/Components/06_Poses/UI_Pose_01..15.png` (termasuk `UI_Pose_09_HoverMode.png` 2,049 B).
   - Diperbarui artboard referensi $1920 \times 1080$: `02_InGame_FlightHUD.png` (102,842 B), `01_Calibration_MotionTutorial.png` (671,646 B), dan `07_PoseGuide_OverlayModal.png` (572,782 B).
3. **Paket Delta Ekspor Laptop B:**
   - Dihasilkan via audit SHA-256 byte-level: folder [`assets/Export_Delta_LaptopB/`](file:///e:/Semester%205/Game/AR%20Dragon/assets/Export_Delta_LaptopB/) berisi tepat **28 file baru/berubah** dilengkapi [`MANIFEST_DELTA.md`](file:///e:/Semester%205/Game/AR%20Dragon/assets/Export_Delta_LaptopB/MANIFEST_DELTA.md).
   - Dikompresi menjadi arsip siap kirim: [`Export_Delta_LaptopB.zip`](file:///e:/Semester%205/Game/AR%20Dragon/Export_Delta_LaptopB.zip) (6,250,581 B).
4. **Buku Panduan Visual Resmi v4.0 (12 Halaman):**
   - Diterbitkan via skrip Python otomatis: [`Panduan_19_Pose_Fanamir_CV.pdf`](file:///e:/Semester%205/Game/AR%20Dragon/Panduan_19_Pose_Fanamir_CV.pdf) (956,918 B) dan [`Panduan_19_Pose_Fanamir_CV.docx`](file:///e:/Semester%205/Game/AR%20Dragon/Panduan_19_Pose_Fanamir_CV.docx) (2,598,036 B) di root dan `docs/`.
5. **Dokumentasi & Blueprint Teknis:**
   - Menyusun spesifikasi komprehensif fisika momentum dan prompt siap salin untuk agen Laptop B di [`brain/laptop-a/planning_momentum_dan_revisi_kontrol.md`](file:///e:/Semester%205/Game/AR%20Dragon/brain/laptop-a/planning_momentum_dan_revisi_kontrol.md).
   - Mengunci arsitektur zona tantangan 3 desa di [[mekanisme_tantangan_desa]] dan checklist pra-Sesi 4 di [[checklist_finalisasi_pra_sesi_4]].
   - Menyusun SOP screenshot mandiri Laptop B di [[panduan_screenshot_mandiri_dan_laporan]] serta draf laporan Capstone di `docs/Laporan_Capstone_Fanamir.docx`.

---

## 4. Detail Penting yang Harus Diingat
- **Kunci Zero-Regression (6 Pose Sakral):** Jangan pernah mengubah ambang deteksi atau logika C#/Python untuk pose yang sudah dinyatakan lulus uji oleh pengguna:
  - `#01 Grounded Idle`
  - `#02 Grounded Walk`
  - `#04 Takeoff`
  - `#05 Lean & Arm Tilt`
  - `#08 Glide`
  - `#13 Tuck to Dive`
- **Konstanta Laju Fisika Momentum:**
  - `MOMENTUM_FLAP_INC = 0.04f` (Cap `0.30f`)
  - `MOMENTUM_STEEP_INC = 0.025f` (Cap `0.20f`)
  - `MOMENTUM_YDIVE_RATE = 0.08f` (Cap `0.55f`)
  - `MOMENTUM_TUCK_RATE = 0.16f` (Cap `1.00f`)
  - `MOMENTUM_DASH_PULSE = 0.07f` (Cap `1.00f`)
  - `MOMENTUM_CRUISE_DECAY = 0.03f`
  - `MOMENTUM_BRAKE_RATE = 0.40f` (~$2.2\dots 2.5\text{s}$ hingga henti)
  - `MOMENTUM_YGLIDE_CONSUME = 0.25f`
  - Kecepatan vertikal `#06B Steep Climb`: `+7.0 m/s`.
  - Kecepatan vertikal `#07 Y-Glide Up`: `+5.0 m/s` (selama momentum $>0\%$).
  - Kecepatan vertikal `#07 Stall`: `-1.2 m/s` (saat momentum $0\%$).
- **Aturan Impor UI Unity Laptop B:** Komponen di `assets/UI_Unity_Export/Components/06_Poses/` adalah medali siluet UI bersih. Gambar di `assets/poses_cv_vector/` adalah kartu panduan CV yang hanya dipakai untuk referensi dokumentasi/buku panduan/overlay bantuan, jangan tertukar.
- **Isolasi Folder:** Seluruh catatan memori Laptop A berada di `brain/laptop-a/` dan tidak boleh menyentuh `brain/laptop-b/`.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
1. **Pengiriman Aset Delta & Prompt ke Laptop B:**
   - Pengguna mentransfer berkas `Export_Delta_LaptopB.zip` ke Laptop B lalu mengekstraknya untuk menimpa aset UI dan vektor terkait.
   - Pengguna menyalin prompt sinkronisasi dari Bagian 5 `brain/laptop-a/planning_momentum_dan_revisi_kontrol.md` ke sesi obrolan Laptop B.
2. **Monitoring Eksekusi di Laptop B:**
   - Memantau penambahan field `currentMomentum` di `DragonFlightController.cs`, bentuk sayap Y di `DragonWingAnimator.cs`, dock bar di `FlightHUDController.cs`, dan pengenalan kepakan lengan bawah pada `#09A Hover` di `ARDragonSkyGuardian_CV/pose_detector.py`.
3. **Eksekusi Sesi 4 (Mekanisme Tantangan Desa):**
   - Setelah momentum terpasang stabil, lanjutkan pengiriman prompt Sesi 4 (`VillageZoneController.cs`, marker 3 desa, sub-marker danau, dan auto-hover).

---

### Navigasi Vault
- 🏠 [[00 - Overview]]
- 📋 [[Tasks]]
- 📝 [[Devlog]]
- ✈️ [[planning_momentum_dan_revisi_kontrol]]
- 🎯 [[mekanisme_tantangan_desa]]
- 📑 [[checklist_finalisasi_pra_sesi_4]]
