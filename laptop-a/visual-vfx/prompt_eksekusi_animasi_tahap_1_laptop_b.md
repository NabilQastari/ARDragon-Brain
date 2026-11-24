---
title: Master Prompt Revisi Kritis Tahap 1 — Anatomi Resmi Hover & Flight (Laptop B)
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

# 🚀 Master Prompt Revisi Kritis Tahap 1 — Berdasarkan 2 Lembar Rujukan Resmi (Laptop B)

> **Petunjuk Penggunaan:** Salin (*copy*) seluruh teks di dalam kotak **"MASTER PROMPT REVISI KRITIS TAHAP 1 UNTUK LAPTOP B"** di bawah ini, lalu kirimkan langsung ke AI Agent di **Laptop B** setelah menjalankan `git -C brain pull`.

---

## 📋 MASTER PROMPT REVISI KRITIS TAHAP 1 UNTUK LAPTOP B (SIAP SALIN)

```markdown
Halo Laptop B! Kami telah memeriksa tangkapan layar Play Mode Tahap 1 dan membandingkannya dengan 2 LEMBAR RUJUKAN RESMI dari pengguna.
Hasil sebelumnya masih aneh karena kaki belakang terlipat remuk ke dalam perut (Glide) dan badan 100% datar dengan kaki meja 90° (Hover).

Rujukan Desain Wajib:
1. Lembar Hover Resmi: `Referensi/Ref_Dragon_Hover_Leg_Anatomy.jpg`
2. Lembar Flight Resmi: `Referensi/Ref_Dragon_Flight_Leg_Anatomy.jpg`
3. Dokumen Evaluasi: `brain/laptop-a/visual-vfx/evaluasi_dan_revisi_pose_tahap_1.md`

Target File:
- `Assets/_Scripts/Controllers/DragonBodyAnimator.cs` pada GameObject `/Dragon_Player` (dan bila perlu periksa pitch kemiringan tubuh di `DragonFlightController.cs`).

Instruksi Perbaikan Teknis:
1. Sinkronisasi Awal: Jalankan `git -C brain pull` untuk mengambil berkas rujukan dan panduan terbaru.
2. Perbaiki Mode Hover (Air Idle, IsHovering / kecepatan < 4 m/s di udara) mengacu ke `Ref_Dragon_Hover_Leg_Anatomy.jpg`:
   - Kemiringan Badan (Upright Hovering Posture):
     * Badan naga TIDAK BOLEH datar horizontal! Kemiringan pitch tubuh harus condong naik tegap sebesar +28° s.d. +35° (terapkan pada Visual_Dragon attitude pitch atau rotasi kumulatif Spine_01..02). Dada membusung tinggi dan permata dada (Chest Gem) terlihat jelas memancar ke arah depan-bawah.
   - Kaki Depan (Forelegs):
     * Siku menekuk santai 80°..90°, lengan bawah dan cakar menggantung santai ke bawah-depan ("Dangling loosely, slightly forward"). BUKAN menjejak kaku ke tanah!
   - Kaki Belakang (Hindlegs):
     * Paha mengarah ke bawah-depan, lutut tertekuk santai 35°..45°, cakar menggantung melayang bebas di bawah tubuh ("Hanging naturally below body"). JANGAN lurus tegak 90° seperti meja!
   - Ekor (Tail Counter-Balance):
     * Pangkal ekor turun mengikuti panggul, lalu ujung ekor sedikit melengkung anggun ke atas sebagai penyeimbang bobot.
3. Perbaiki Mode Flight (Glide, Tuck Dive, kecepatan >= 12 m/s) mengacu ke `Ref_Dragon_Flight_Leg_Anatomy.jpg`:
   - Kaki Belakang (WAJIB LURUS KE BELAKANG):
     * Jangan gunakan sudut negatif sembarangan tanpa memperhatikan local roll tulang! Local X pada Thigh_Hind memiliki orientasi diagonal (~50°), sehingga membutuhkan intrinsic roll rz = +90° (Left) / -90° (Right) agar paha terarah lurus ke belakang sejajar ekor.
     * Nilai Euler Terverifikasi (Matematika Blender & Three.js Studio):
       - Thigh_Hind.L: rx = -32°, rz = +90° | Thigh_Hind.R: rx = -32°, rz = -90°
       - Shin_Hind.L: rx = -50°, rz = -20° | Shin_Hind.R: rx = -50°, rz = +20°
       - Metatarsus_Hind.L: rx = -60°, rz = -10° | Metatarsus_Hind.R: rx = -60°, rz = +10°
       - Foot_Hind.L/R: rx = -65° (menunjuk lurus ke belakang)
   - Kaki Depan (Folded neatly under chest):
     * Shoulder_Front.L/R: rx = +15°, rz = ∓5°
     * UpperArm_Front.L/R: rx = +65°, rz = ∓14°
     * Forearm_Front.L/R: rx = +105°, rz = ±10°
     * Paw_Front.L/R: rx = -40°
   - Kemiringan Badan:
     * Datar horizontal ramping (0°) sejajar vektor penerbangan.

4. Referensi Rotasi Euler Terverifikasi Mode Hover (Air Idle / IsHovering):
   - Body Pitch: +28° s.d. +35° (naik tegap membusung)
   - Spine Curve: Spine_01 = +6.3°, Spine_02 = +7.7° (total elevasi dada +14°)
   - Head Counter-Pitch: Neck_01 = -11.4°, Neck_02 = -10.0°, Head = -7.1° (kepala tetap sejajar horizontal)
   - Forelegs (Dangling loosely beneath chest):
     * Shoulder_Front: rx = -5°
     * UpperArm_Front: rx = +45°, rz = ∓10°
     * Forearm_Front: rx = -65°, rz = ±6°
     * Paw_Front: rx = +25°
   - Hindlegs (Dangling naturally below body):
     * Thigh_Hind: rx = +18°, rz = ∓6°
     * Shin_Hind: rx = +38°
     * Metatarsus_Hind: rx = -30°
     * Foot_Hind: rx = -10°

5. Verifikasi Visual Langsung di Editor:
   - Jalankan Play Mode di Unity Editor, amati di Game View dan Scene View:
     * Saat HOVER: Apakah badan Fanamir sudah tegap miring naik (+28°..+35°) dan ke-4 kakinya menggantung rileks melayang (bukan berdiri di lantai)?
     * Saat GLIDE: Apakah kaki belakang sudah lurus memanjang ke belakang menyatu dengan ekor tanpa remuk di perut?
   - Jalankan `DragonAnimationVerificationRunner.cs` di scene `MainIsland_Gameplay.unity` (pastikan 100% PASS).
   - Ambil tangkapan layar Play Mode baru untuk memperbarui:
     * `docs/figures/raw_screenshots/SS_Animasi_Tahap1_FlightLegs.png`
     * `docs/figures/raw_screenshots/SS_Animasi_Tahap1_HoverChest.png`
5. Dokumentasi:
   - Catat rotasi sudut final dan konfirmasi visual di `brain/laptop-b/Devlog.md` dan `brain/laptop-b/Tasks.md`.
   - Lakukan commit dan push sub-repo `brain/`.
```
