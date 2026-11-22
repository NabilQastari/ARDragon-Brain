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
     * Jangan gunakan sudut negatif ekstrim yang melipat kaki ke dalam perut!
     * Paha belakang, lutut, tulang kering, dan cakar harus LURUS MEMANJANG KE BELAKANG sejajar dan merapat di samping pangkal ekor ("Extended backward, streamlined along tail"). Sendi lutut hampir lurus (0°..15°), cakar menunjuk ke belakang.
   - Kaki Depan:
     * Siku melipat ke belakang, cakar merapat rata di bawah rongga dada ("Folded neatly under chest").
   - Kemiringan Badan:
     * Kembali datar horizontal ramping (0°) sejajar vektor penerbangan.
4. Verifikasi Visual Langsung di Editor:
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
