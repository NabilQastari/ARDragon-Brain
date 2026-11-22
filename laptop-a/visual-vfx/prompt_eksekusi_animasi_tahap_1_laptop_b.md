---
title: Prompt Eksekusi Peningkatan Animasi Naga Fanamir — Tahap 1 Revisi Kritis (Laptop B)
tags:
  - prompt
  - laptop-b
  - animation
  - procedural-animation
  - flight-legs
  - hover-posture
  - bugfix
  - unity
owner: Laptop A
updated: 2026-11-23
status: ready-for-execution
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[visual-vfx/evaluasi_dan_revisi_pose_tahap_1]]"
  - "[[visual-vfx/planning_perbaikan_animasi_dan_pose]]"
---

# 🚀 Prompt Eksekusi Peningkatan Animasi Naga Fanamir — Tahap 1 Revisi Kritis (Laptop B)

> **Petunjuk Penggunaan:** Salin (*copy*) seluruh teks di dalam kotak **"MASTER PROMPT REVISI KRITIS TAHAP 1 UNTUK LAPTOP B"** di bagian akhir dokumen ini, lalu kirimkan langsung ke AI Agent di **Laptop B** setelah menjalankan `git -C brain pull`.

---

## 🧭 1. Evaluasi Kritis Hasil Tangkapan Layar Sebelumnya

Tangkapan layar hasil implementasi Tahap 1 awal di Laptop B menunjukkan dua masalah besar:
1. **Pose Meluncur (`GLIDE` di Gambar 1):** Kaki belakang terlipat remuk saling tumpang-tindih ke dalam perut naga (*accordion fold / mesh clipping*), bukannya lurus memanjang ke belakang. Kaki depan membentuk benjolan aneh.
2. **Pose Melayang (`HOVER` di Gambar 2):** Badan naga $100\%$ datar horizontal (sama sekali tidak ada tanda dada terangkat), dan keempat kaki berdiri tegak lurus $90^\circ$ ke bawah seperti meja menjejak lantai gaib.

---

## 🛠️ 2. Petunjuk Perbaikan Teknis

### A. Kaki Belakang Meluncur Cepat (`Flight Tuck` — LURUS MEMANJANG KE BELAKANG)
* **Konsep Rujukan:** *"HIND LEGS: Extended backward, streamlined along tail"*.
* **Akar Masalah Sebelumnya:** Nilai rotasi negatif besar (`-88°, 32°, -52°, -65°`) melipat kaki ke dalam perut.
* **Koreksi:**
  * Paha belakang (`Thigh_Hind`): Orientasikan lurus ke arah belakang mengikuti sumbu ekor (-Z visual).
  * Lutut / Tulang Kering (`Shin_Hind`): Jangan ditekuk tajam ke dalam! Pertahankan sendi lutut hampir lurus ($\sim 0^\circ\text{--}15^\circ$).
  * Cakar Belakang (`Metatarsus_Hind`, `Foot_Hind`): Luruskan memanjang ke belakang, merapat sejajar pangkal ekor, menunjuk ke belakang.
  * *Hasil Visual:* Kaki belakang terlihat memanjang anggun dan menyatu dengan ekor Fanamir, bebas dari lipatan remuk di perut.

### B. Kaki Depan Meluncur Cepat (`Flight Tuck` — MELIPAT RATA DI BAWAH DADA)
* **Konsep Rujukan:** *"FRONT LEGS: Tucked backward against body / Folded neatly under chest"*.
* **Koreksi:**
  * Siku melipat ke belakang, cakar merapat rata menempel di bawah dada/tulang rusuk (*flat against chest*), tidak mencuat keluar atau menjadi benjolan ganjil.

### C. Sikap Tubuh Saat Hover (`HOVER #09A` — DADA TERANGKAT TEGAP)
* **Akar Masalah Sebelumnya:** Tubuh naga $100\%$ datar karena rotasi Spine tidak terlihat atau ditahan oleh root `Visual_Dragon`.
* **Koreksi:**
  * **Attitude Pitch Tubuh Naik ($+18^\circ \sim +22^\circ$):** Saat `IsHovering` / kecepatan $< 4\text{ m/s}$ di udara, seluruh postur tubuh naga (melalui rotasi pitch `Visual_Dragon` atau rotasi kumulatif `Spine_01..02`) harus terangkat miring naik $+18^\circ \sim +22^\circ$ secara nyata! Permata dada (*Chest Gem*) memancar jelas ke arah depan-bawah.
  * **Kepala Menatap Mendatar:** Leher (`Neck_01..02`) mengimbangi kemiringan dada dengan melengkung anggun ke depan sehingga kepala tetap menatap lurus mendatar.
  * **Kaki Hover Drop (MELAYANG SANTAI, BUKAN BERDIRI TEGAK $90^\circ$!):**
    * Kaki depan: Tertekuk santai ke belakang bawah ($\sim 40^\circ$), cakar menggantung lembut tertiup angin.
    * Kaki belakang: Tertekuk rileks ke bawah belakang ($\sim 35^\circ$), sendi lutut santai melayang, BUKAN lurus tegak ke bawah menjejak tanah.

---

## 📋 3. MASTER PROMPT REVISI KRITIS TAHAP 1 UNTUK LAPTOP B (SIAP SALIN)

```markdown
Halo Laptop B! Kami telah memeriksa tangkapan layar Play Mode Tahap 1. Terdapat dua anomali visual kritis yang harus segera diperbaiki:
1. Pada Pose Glide: Kaki belakang tampak remuk/menciut ke dalam perut (accordion fold), dan kaki depan membentuk benjolan aneh.
2. Pada Pose Hover: Badan naga 100% datar horizontal (elevasi dada tidak tampak sama sekali), dan ke-4 kakinya tegak lurus 90° ke bawah persis seperti naga berdiri di atas lantai/meja.

Rujukan Analisis Detail:
- Baca: `brain/laptop-a/visual-vfx/evaluasi_dan_revisi_pose_tahap_1.md`
- Gambar Panduan Resmi: `Referensi/Ref_Dragon_Flight_Leg_Anatomy.jpg`

Target File:
- `Assets/_Scripts/Controllers/DragonBodyAnimator.cs` (dan bila perlu periksa orientasi attitude pitch di `DragonFlightController.cs`).

Instruksi Perbaikan Teknis:
1. Sinkronisasi Awal: Jalankan `git -C brain pull` untuk mengambil dokumen evaluasi terbaru dari Laptop A.
2. Perbaiki Kaki Terbang (Flight Tuck, saat kecepatan >= 12 m/s / Glide / Dive):
   - Kaki Belakang (WAJIB LURUS KE BELAKANG): Jangan gunakan sudut negatif ekstrim yang melipat lutut ke dalam perut! Paha belakang, tulang kering, dan cakar harus LURUS MEMANJANG KE BELAKANG merapat sejajar di samping pangkal ekor ("Extended backward, streamlined along tail"). Sendi lutut hampir lurus, cakar menunjuk ke belakang.
   - Kaki Depan: Siku melipat ke belakang, cakar merapat rata di bawah rongga dada ("Folded neatly under chest").
3. Perbaiki Postur Hover (saat IsHovering / kecepatan < 4 m/s di udara):
   - Elevasi Badan Nyata (+18° s.d. +22°): Pastikan badan naga condong naik secara visual di Game View! Jika rotasi Spine_01..02 tidak cukup mengangkat badan di kamera, sesuaikan attitude pitch tubuh atau integrasikan dengan Visual_Dragon agar dada Fanamir terangkat tegap dan permata dada (Chest Gem) terlihat jelas ke arah depan-bawah.
   - Kaki Hover Drop (MELAYANG SANTAI, BUKAN BERDIRI MEJA 90°): Jangan buat kaki tegak lurus 90° ke bawah! Kaki depan tertekuk rileks ke belakang bawah (~40°), kaki belakang tertekuk santai ke bawah-belakang (~35°), cakar menggantung santai di udara layaknya naga yang melayang bebas.
4. Verifikasi Visual Langsung:
   - Jalankan Play Mode di Unity Editor, amati langsung di Scene View & Game View saat naga GLIDE dan HOVER:
     * Apakah kaki belakang sudah lurus memanjang ke belakang sejajar ekor?
     * Apakah saat Hover dada naga sudah terangkat tegap dan kakinya melayang santai (bukan berdiri kaku)?
   - Jalankan `DragonAnimationVerificationRunner.cs` di scene `MainIsland_Gameplay.unity`.
   - Ambil screenshot Play Mode baru untuk menggantikan tangkapan layar sebelumnya di `docs/figures/raw_screenshots/SS_Animasi_Tahap1_FlightLegs.png` dan `SS_Animasi_Tahap1_HoverChest.png`.
5. Dokumentasi:
   - Catat perbaikan sudut bone dan konfirmasi visual di `brain/laptop-b/Devlog.md` dan `brain/laptop-b/Tasks.md`.
   - Jalankan commit dan push sub-repo `brain/`.
```
