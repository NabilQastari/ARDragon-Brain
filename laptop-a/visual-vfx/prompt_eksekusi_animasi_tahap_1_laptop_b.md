---
title: Prompt Eksekusi Peningkatan Animasi Naga Fanamir — Tahap 1 (Laptop B)
tags:
  - prompt
  - laptop-b
  - animation
  - procedural-animation
  - flight-legs
  - hover-posture
  - unity
owner: Laptop A
updated: 2026-11-23
status: ready-for-execution
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[visual-vfx/planning_perbaikan_animasi_dan_pose]]"
  - "[[visual-vfx/arsitektur_animasi_naga_fanamir]]"
---

# 🚀 Prompt Eksekusi Peningkatan Animasi Naga Fanamir — Tahap 1 (Laptop B)

> **Petunjuk Penggunaan:** Salin (*copy*) seluruh teks di dalam kotak **"MASTER PROMPT EKSEKUSI TAHAP 1 UNTUK LAPTOP B"** di bagian akhir dokumen ini, lalu kirimkan langsung ke AI Agent di **Laptop B** setelah menjalankan `git -C brain pull`.

---

## 🧭 1. Konteks & Sasaran Tahap 1 (Quick Win: Anatomi Kaki & Sikap Hover)

Tahap 1 adalah langkah awal dari **4 Tahap Peningkatan Animasi & Pose Fanamir** yang telah disetujui. Fokus tahap ini adalah penyempurnaan postur statis tubuh naga yang paling sederhana, berisiko nol, namun berdampak visual tinggi:
1. **Anatomi Kaki Saat Terbang (*Flight Tuck*):** Mengacu pada lembar rujukan resmi pengguna (`Referensi/Ref_Dragon_Flight_Leg_Anatomy.jpg`):
   * Kaki depan (*forelegs*): Melipat rapi ke arah belakang di bawah tulang dada (*folded neatly under chest*).
   * Kaki belakang (*hind legs*): Lurus memanjang ke arah belakang merapat sejajar di sepanjang pangkal ekor (*extended backward, streamlined along tail*).
2. **Koreksi Sikap Tubuh Saat Hover (*Air Idle*):**
   * Dada terangkat tegap $+14^\circ \sim +18^\circ$ dari garis horizontal, memperlihatkan permata dada (*Chest Gem*) ke arah depan-bawah.
   * Osilasi apung magis vertikal lembut (*buoyant bobbing* $0.40\text{ Hz}, \pm 0.18\text{ m}$).
   * Kaki beralih ke postur menjuntai turun rileks (*Hover Drop*).

Target berkas yang diubah: **Hanya [`Assets/_Scripts/Controllers/DragonBodyAnimator.cs`](file:///d:/Fauzih/Kuliah/SEMESTER%20V/Proyek%20Multimedia%20dan%20Game/AR%20Dragon/AR%20Dragon%20Sky%20Guardian/Assets/_Scripts/Controllers/DragonBodyAnimator.cs)**.

---

## 🛠️ 2. Rincian Teknis Modifikasi di `DragonBodyAnimator.cs`

### A. Anatomi Kaki Saat Meluncur Cepat (`_legTuckWeight -> 1.0f`, Kecepatan $\ge 12\text{ m/s}$ / Glide / Dive / Dash)
Perbarui metode `UpdateLegsAnimation()` agar menerapkan rotasi lokal spesifik sesuai gambar:
* **Kaki Depan Kiri & Kanan (`_frontLegs`):**
  * Paha/Lengan Atas (`UpperArm_Front`): Tarik ke belakang ke arah dada ($\approx -50^\circ \sim -65^\circ$ pitch lokal).
  * Lengan Bawah (`Forearm_Front`): Tekuk merapat di bawah dada ($\approx +60^\circ \sim +75^\circ$).
  * Telapak Cakar (`Paw_Front`): Tekuk santai sejajar perut ($\approx -15^\circ \sim -25^\circ$).
  * *Hasil:* Kaki depan terlipat rapi menempel di bawah tulang dada tanpa menjuntai ke bawah.
* **Kaki Belakang Kiri & Kanan (`_backLegs`):**
  * Paha Belakang (`Thigh_Hind`): Luruskan ke belakang sejajar sumbu tubuh ($\approx +60^\circ \sim +75^\circ$ pitch lokal).
  * Tulang Kering (`Shin_Hind`): Luruskan merapat ke pangkal ekor ($\approx -20^\circ \sim -35^\circ$).
  * Kaki & Cakar Belakang (`Metatarsus_Hind`, `Foot_Hind`): Luruskan ke belakang merapat sejajar ekor ($\approx -25^\circ \sim -40^\circ$).
  * *Hasil:* Siluet ramping seperti burung pemangsa berkecepatan tinggi (*streamlined rearward pose*).

### B. Elevasi Dada & Buoyant Bobbing Saat Hover (`flightController.IsHovering` atau `airspeed < 6f` saat terbang)
Perbarui metode `UpdateSpineAndTailAnimation()`:
* **Elevasi Dada:**
  * Saat kondisi Hover terdeteksi, tambahkan rotasi pitch ke atas sebesar $+8^\circ$ pada `Spine_01` dan $+8^\circ$ pada `Spine_02` (total elevasi dada $+16^\circ$) menggunakan `Mathf.SmoothDamp` ($0.25\text{ s}$).
* **Buoyant Hover Bobbing:**
  * Tambahkan osilasi lembut pada posisi vertikal lokal tubuh:
    $$\Delta Y_{\text{bob}} = 0.18\text{ m} \cdot \sin(2\pi \cdot 0.40\text{ Hz} \cdot \text{Time.time})$$
* **Kaki Hover Drop (`_legTuckWeight -> 0.0f`):**
  * Kaki depan menjuntai turun rileks (pitch $\sim 25^\circ$, siku sedikit tertekuk alami).
  * Kaki belakang menjuntai turun santai (pitch $\sim 35^\circ$ siap menapak daratan).

---

## 📋 3. MASTER PROMPT EKSEKUSI TAHAP 1 UNTUK LAPTOP B (SIAP SALIN)

```markdown
Halo Laptop B! Roadmap 4 Tahap Peningkatan Animasi & Pose Naga Fanamir telah disetujui.
Sekarang jalankan eksekusi: TAHAP 1 (Anatomi Kaki Terbang Aerodinamis Sesuai Gambar Referensi & Elevasi Dada Saat Hover).

Rujukan Desain:
- `brain/laptop-a/visual-vfx/planning_perbaikan_animasi_dan_pose.md`
- `brain/laptop-a/visual-vfx/prompt_eksekusi_animasi_tahap_1_laptop_b.md`
- Gambar Referensi Anatomi: `Referensi/Ref_Dragon_Flight_Leg_Anatomy.jpg`

Target File:
- Modifikasi `Assets/_Scripts/Controllers/DragonBodyAnimator.cs` pada GameObject `/Dragon_Player` di `MainIsland_Gameplay.unity` & `Test_Dragon_Flight.unity`.

Instruksi Eksekusi Teknis:
1. Sinkronisasi Awal: Jalankan `git -C brain pull` untuk mengambil dokumen perencanaan terbaru dari Laptop A.
2. Perbarui Postur Kaki Terbang (Flight Tuck, `_legTuckWeight -> 1.0f`) di `DragonBodyAnimator.cs`:
   - Kaki Depan (Forelegs): Lipat rapi ke arah belakang merapat di bawah tulang dada ("Tucked backward against body / Folded neatly under chest"). Siku menekuk ke belakang, cakar merapat di bawah sternum/chest gem.
   - Kaki Belakang (Hindlegs): Luruskan memanjang ke belakang merapat sejajar di sepanjang pangkal ekor ("Extended backward, streamlined along tail / Streamlined rearward pose"). Paha dan tulang kering melurus ke belakang, cakar menunjuk ke belakang sejajar ekor.
3. Perbarui Postur Hover (Air Idle / `IsHovering` / Kecepatan < 6 m/s):
   - Elevasi Dada: Naikkan sudut pitch dada sebesar +16° (+8° pada Spine_01 dan +8° pada Spine_02) menggunakan SmoothDamp (0.25s) agar tubuh naga tegap memperlihatkan permata dada (Chest Gem) ke arah depan-bawah.
   - Buoyant Hover Bobbing: Tambahkan osilasi apung vertikal lembut (frekuensi 0.40 Hz, amplitudo 0.18m).
   - Kaki Hover Drop: Kaki depan dan belakang menjuntai turun rileks siap menapak daratan saat _legTuckWeight mendekati 0.
4. Verifikasi & Non-Regresi:
   - Kompilasi Unity: Pastikan 0 compilation errors dan 0 console warnings.
   - Jalankan `DragonAnimationVerificationRunner.cs` di scene `MainIsland_Gameplay.unity`: pastikan seluruh tes animasi lulus 100%.
   - Ambil tangkapan layar mandiri di Play Mode:
     * `docs/figures/raw_screenshots/SS_Animasi_Tahap1_FlightLegs.png` (tampilan samping/3/4 saat meluncur cepat dengan kaki merapat aerodinamis).
     * `docs/figures/raw_screenshots/SS_Animasi_Tahap1_HoverChest.png` (tampilan depan/3/4 saat naga hover dengan dada terangkat tegap).
5. Dokumentasi:
   - Catat hasil pengujian dan nama file screenshot di `brain/laptop-b/Devlog.md` dan `brain/laptop-b/Tasks.md`.
   - Jalankan commit dan push sub-repo `brain/`.
```
