---
title: Spesifikasi Kecepatan, Momentum v5.0, Kemudi Halus, & Escape Desa
tags:
  - flight-physics
  - speed
  - momentum
  - steering
  - village-escape
  - laptop-a
  - specifications
owner: Laptop A
updated: 2026-11-26
status: approved
---

# 🚀 Spesifikasi Kecepatan, Sistem Momentum v5.0, Kemudi Halus, & Mekanisme Escape Desa

> **Dokumen Single Source of Truth (SSOT):**  
> Mengintegrasikan keputusan desain terbaru pengguna mengenai:
> 1. Perbaikan sensitivitas kemudi belok (*Deadzone Tengah*, kurva kuadratik, & Slider Settings).
> 2. Standarisasi mekanisme *leave/escape* textbox narasi & auto-hover desa (3x Flap untuk Pyrewood & Solaria; 100% Tuck Dive untuk Zephyria).
> 3. Peningkatan drastis profil kecepatan & kurva momentum terpadu v5.0 (kecepatan hingga $100\text{--}150\text{ m/s}$, charge rate, konsumsi, dan pendaratan mulus).

---

## 1. Perbaikan Kemudi Belok & Sensitivitas (Anti Terlalu Sensitif)

### A. Masalah & Solusi Arsitektur
- **Akar Masalah:** Di Unity, `turnDeadzone` sebelumnya diatur sangat tipis (`~0`), `yawTurnSpeed = 95°/s`, dan *Wing Overdrive Steer* (1.5x) terpicu pada selisih lengan kecil ($|\Delta Y| > 0.15$). Pemain yang bahunya sedikit miring $1^\circ\text{--}2^\circ$ langsung membuat naga berbelok terus sehingga sulit terbang lurus.
- **Pemberian Deadzone Tengah yang Nyaman ($\pm 0.08$):**
  - Pada kemiringan bahu/tangan $\le \pm 0.08$ ($\approx \pm 3^\circ\text{--}4^\circ$), input kemudi di-clamp murni ke `0.0f` (*Deadzone Center Band*). Pemain dapat meluncur lurus dengan tenang dan rileks.
- **Kurva Respon Non-Linier Halus (*Exponential Ease Curve*):**
  - Di luar zona deadzone, sinyal kemudi dihitung dengan kurva eksponensial kuadratik:
    $$r_{\text{eff}} = \text{sign}(r) \times \left(\frac{|r| - \text{deadzone}}{1.0 - \text{deadzone}}\right)^{1.35}$$
  - Belokan mikro terasa sangat jinak dan stabil, sedangkan belokan tajam tetap lincah saat badan benar-benar miring.
- **Ambang Batas Wing Overdrive Steer:**
  - Dinaikkan dari $0.15 \to \mathbf{0.22}$ agar gerakan tangan saat meluncur biasa tidak memicu lonjakan belokan mendadak.
- **Slider Baru di Menu Settings (`SCREEN 05`):**
  - Komponen: `Row_SteeringSensitivity` pada `PF_SettingsPanel.prefab`.
  - Rentang: **`0.5x` s.d. `2.0x` (Default: `1.0x / Tenang`)**.
  - Key PlayerPrefs: `Fanamir_SteeringSensitivity` (tipe float, default `1.0f`).
  - Efek: Mengalikan sinyal kemudi efektif secara live di `DragonFlightController.cs`.

---

## 2. Mekanisme Leave / Escape Textbox Narasi & Event Desa

| Lokasi / Event | Aksi Escape Resmi | Alasan & Perilaku Sistem |
| :--- | :--- | :--- |
| **Desa Pyrewood & Desa Solaria** *(Narasi maupun Tantangan)* | **Kepakkan kedua sayap 3 KALI BERTURUT-TURUT** | Mencegah pemain terlempar keluar tanpa sengaja saat sekadar menggerakkan tangan 1 kali. Membutuhkan 3 siklus kepakan penuh dalam jendela waktu $2.5\text{ detik}$ sebelum memicu `ExitAutoHover()`. |
| **Desa Zephyria** *(Smog Valley)* | **100% HANYA DENGAN TUCK DIVE (`#13`)** | Karena misi Zephyria adalah *Flapping* (mengepak sayap), kepakan tangan di Zephyria **tidak boleh** membatalkan auto-hover. Satu-satunya cara keluar adalah dengan pose **Tuck Dive (`#13`)** yang ditahan selama $\ge 0.6\text{ detik}$. |

---

## 3. Matriks Komprehensif: Kecepatan & Sistem Momentum v5.0

### A. Profil Kecepatan Meluncur Dasar & Dinamis (`#08 Glide`)
- **Kecepatan Dasar (Tanpa Momentum / $0\%$):** **`16 m/s`** (naik dari $14\text{ m/s}$ agar penjelajahan pulau luas terasa lebih dinamis).
- **Kecepatan Dinamis Berdasarkan Bar Momentum:**
  - **$< 30\%$ Momentum:** Kecepatan meluncur **`25 – 40 m/s`** (berskala proporsional dari $25\text{ m/s}$ ke $40\text{ m/s}$).
  - **$\ge 30\%$ s.d. $< 75\%$ Momentum:** Kecepatan terkunci stabil di **`40 m/s`**.
  - **$\ge 75\%$ Momentum:** Mengikuti kecepatan dinamika **Propeller Dash Normal (`40 – 100 m/s`)**.
- **Laju Hambatan Alami (*Drag Decay*):** **`-3.0% / detik`** (dari $100\%$ butuh $\sim 33\text{ detik}$ meluncur lurus sebelum kembali ke $0\%$).

---

### B. Tabel Lengkap Pose Pengisi Momentum (*Momentum Charge*)

| Pose | Syarat Deteksi | Laju Pengisian (*Charge Rate*) | Batas Maks (*Soft Cap*) | Kecepatan Maju (*Forward Speed*) | Waktu Capai Maksimal |
| :--- | :--- | :---: | :---: | :---: | :---: |
| **`#04 Takeoff`** | Kepak kuat lepas landas dari darat | **`+10%` INSTAN** | $10\%$ | $20 – 25\text{ m/s}$ | Seketika saat terangkat |
| **`#06 Flap to Climb`** | Kepakan ritmis di udara (dagu netral) | **`+5% / detik`** | **`50%`** | **`25 – 40 m/s`** | **`3 detik`** |
| **`#06B Steep Climb`** | Kepakan bertenaga + kepala mendongak | **`+4% / detik`** | **`30%`** | **`20 – 35 m/s`** *(Vy +8..12 m/s)* | **`4 detik`** |
| **`#11 Fly Down (Y-Dive)`** | Y-pose bersiku menukik miring | **`+8% / detik`** | **`60%`** | **`35 – 60 m/s`** *(turun -20°)* | ~4.5 detik |
| **`#13 Tuck to Dive`** | Kedua lengan melipat rapat di dada | **`+13% / detik`** | **`100%`** | **`30 – 100 m/s`** *(turun -45°)* | **`2 detik`** mencapai 100 m/s |
| **`#14 Propeller Dash (Normal)`** | Lengan bawah berputar di siku | **`+10% / detik`** (`+1%/0.1s`) | **`100%`** | **`40 – 100 m/s`** | **`3 detik`** mencapai 100 m/s |
| **`#14 Propeller Dash (BOOST)`** | Tetap mode Propeller Dash saat momentum $100\%$ | — *(Mempertahankan 100%)* | **`100%`** | **`80 – 150 m/s`** | Mode Kecepatan Supersonik |

---

### C. Tabel Lengkap Pose Pengonsumsi / Pengereman Momentum (*Consumption & Brakes*)

| Pose | Gerakan / Sikap Tubuh | Laju Konsumsi (*Decay / Brake*) | Kecepatan Maju Terkendali | Gerak Vertikal (*Vy*) |
| :--- | :--- | :---: | :---: | :---: |
| **`#07 Fly Up / Glide Up`** | Kedua lengan membentuk huruf **Y** | **`-10% / detik`** | • $\ge 80\% \text{ Momentum} \to \mathbf{40\text{ m/s}}$<br>• $\ge 40\% \text{ Momentum} \to \mathbf{25\text{ m/s}}$<br>• $< 40\% \text{ Momentum} \to \mathbf{20\text{ m/s}}$<br>• $0\% \text{ Momentum (Normal)} \to \mathbf{12\text{ m/s}}$ | Menanjak tajam $+15\text{--}25\text{ m/s}$ (saat $0\%$ transisi ke gentle stall $-1.2\text{ m/s}$) |
| **`#08 Glide`** | Kedua lengan datar T-pose | **`-3.0% / detik`** | $16\text{ m/s}$ ($0\%$) s.d. $100\text{ m/s}$ ($\ge 75\%$) | Datar $-0.3\text{ m/s}$ |
| **`#09A Hover Active`** | L-pose $90^\circ$ + kepak lengan bawah | **`-40% / detik`** | Melambat cepat hingga **`0 m/s`** | Mengambang diam $0\text{ m/s}$ |
| **`#09B Slow Glide`** | L-pose $90^\circ$ ditahan diam | **`-10% / detik`** | Mengunci jelajah santai **`10 – 14 m/s`** | Turun perlahan $-0.6\text{ m/s}$ |
| **`#10 Gentle Descent`** | Low-A pose (~$45^\circ$ ke bawah) | **`-15% / detik`** | **`14 – 18 m/s`** | Turun melandai $-3.0\text{ m/s}$ |
| **`#12 Fly Down Idle (W)`** | Siku menempel pinggang (W-pose) | **`-35% / detik`** | Turun cepat ke **`5 m/s`** | Parasut vertikal $-6.0\text{ m/s}$ |

---

## 4. Aturan Khusus Peristiwa Lapangan (*Event Triggers*)

1. **Tabrakan Tebing / Batu Terjal (*Glancing Bounce*):**
   - Penalti momentum ringan: **hanya `-10%`** dari momentum saat itu (misal $70\% \to 60\%$).
   - Naga memantul elastis $+15^\circ$ ke atas dengan micro-rumble tanpa kehilangan daya jelajah secara masif.
2. **Pendaratan di Tanah (*Landing Mechanics*):**
   - **Pendaratan Kasual (Jatuh/Stall/Menukik ke Darat):** Momentum melambat dari $X\% \to 0\%$ **dalam 3 detik**, kecepatan melambat dari $X\text{ m/s} \to 0\text{ m/s}$ **dalam 3 detik** (efek sliding brake di rumput).
   - **Pengecualian Khusus (*Glide-to-Ground Seamless Walk*):** Jika naga menyentuh tanah **dalam posisi Glide datar (`#08`)**, naga **LANGSUNG bertransisi ke mode berjalan (`Ground Walk`) dengan kecepatan normal jalan kaki ($3.5\text{ m/s}$)** tanpa jeda atau henti mendadak!
3. **Pelepasan / Selesai Event Desa (*Launch Thrust*):**
   - Tidak menggunakan angka dorongan statis kaku.
   - Dorongan peluncuran saat dialog/tantangan selesai **bersifat dinamis mengikuti sisa bar momentum yang ada dan pose aktif** yang sedang dilakukan pemain saat melesat keluar.
