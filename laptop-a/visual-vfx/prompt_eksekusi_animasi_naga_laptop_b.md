---
title: Prompt Eksekusi Sistem Animasi Tubuh Naga Fanamir (Laptop B)
tags:
  - prompt
  - laptop-b
  - animation
  - procedural-animation
  - unity
owner: Laptop A
updated: 2026-11-22
status: ready-for-execution
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[visual-vfx/arsitektur_animasi_naga_fanamir]]"
  - "[[visual-vfx/planning_vfx_fov_skybox_dan_animasi]]"
---

# 🚀 Prompt Eksekusi Sistem Animasi Tubuh Naga Fanamir untuk Laptop B

> **Petunjuk untuk Pengguna:** Salin (*copy*) seluruh isi kotak **"MASTER PROMPT EKSEKUSI ANIMASI NAGA UNTUK LAPTOP B"** di bagian akhir dokumen ini, lalu kirimkan langsung ke AI Agent di **Laptop B** setelah menjalankan `git -C brain pull`.

---

## 🧭 Ikhtisar Arsitektur Animasi Tubuh Fanamir (Laptop B)

Sistem penerbangan, sayap (`DragonWingAnimator.cs`), dan kepala (`DragonHeadLookController.cs`) telah bekerja mulus 100%. Tugas pada sesi ini adalah menambahkan **`DragonBodyAnimator.cs`** untuk menghidupkan 35 tulang tubuh lainnya pada rig `ARM_Dragon` (47 tulang):

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│                 TRIO ANIMASI TUBUH NAGA FANAMIR (SESI ANIMASI)              │
├──────────────────────────┬──────────────────────────┬───────────────────────┤
│ 1. RAHANG BUKA MULUT     │ 2. LIUKAN EKOR & PUNGGUNG│ 3. LIPATAN KAKI & CAKAR│
│ • Bone_Jaw buka 0°..-30° │ • Tail Sway belok kemudi │ • Flight Tuck melipat │
│ • MOUTH_OPEN CV (#15B)   │ • Cruising Undulation    │   saat speed >= 12 m/s│
│ • Victory Roar selebrasi │ • Spine Arch saat menukik│ • Hover Drop menjuntai│
│   desa 100% pulih        │   dan menanjak tajam     │   santai saat hover   │
└──────────────────────────┴──────────────────────────┴───────────────────────┘
```

---

## 📋 Rincian 4 Langkah Implementasi di Laptop B

### Langkah 1: Buat Skrip Prosedural `DragonBodyAnimator.cs`
* Lokasi: `Assets/Scripts/Controllers/DragonBodyAnimator.cs` (namespace `DragonWingAR.Controllers`).
* Atur `[DefaultExecutionOrder(55)]` agar dieksekusi setelah `DragonFlightController` dan `DragonWingAnimator`.
* Lakukan *auto-cache* transform tulang dari hierarki `Visual_Dragon/ARM_Dragon`:
  * Rahang: `Bone_Jaw` / `Jaw`
  * Tulang Belakang: `Spine_01`, `Spine_02`, `Spine_03`
  * Ekor: `Tail_01` s.d. `Tail_05`
  * Kaki Depan & Belakang: `Leg_Front_*`, `Leg_Back_*`

### Langkah 2: Implementasi Artikulasi Rahang Buka Mulut
* Baca nilai `MOUTH_OPEN` dari telemetri UDP `5052` (via `IPoseProvider` / `UDPPoseReceiver`).
* Rotasikan `Bone_Jaw` pada sumbu buka mulut (pitch lokal) dari `0°` (tertutup saat `MOUTH_OPEN < 0.25`) hingga `-30°` (terbuka lebar saat `MOUTH_OPEN > 0.40`).
* Buka otomatis rahang sebesar `-25°` saat `VillageZoneController` memicu auto-hover selebrasi kemenangan (*Victory Roar*).
* Haluskan pergerakan dengan `Mathf.SmoothDamp` ($0.12\text{ detik}$).

### Langkah 3: Implementasi Liukan Ekor Dinamis & Kelenturan Punggung
* **Tail Sway saat Belok:** Mengambil sudut kemudi naga (`TurnInput` / `rollAngle`), lalu rotasikan sumbu yaw `Tail_01..05` secara berderet dengan efek keterlambatan (*hierarchical wave lag* $15\% \rightarrow 100\%$).
* **Cruising Undulation:** Saat meluncur lurus (`Glide`), tambahkan gelombang sinus horizontal lambat ($0.6\text{ Hz}$, amplitudo $6^\circ$).
* **Spine Arch:** Saat naga menukik tajam (*Tuck Dive* `#13`), lengkungkan `Spine_01..03` sedikit ke bawah ($\sim -6^\circ$). Saat menanjak tajam (*Fly Up* `#07`), lentingkan sedikit ke atas ($\sim +5^\circ$).

### Langkah 4: Implementasi Lipatan Kaki & Cakar Aerodinamis
* Saat kecepatan naga $\ge 12\text{ m/s}$ (Glide / Dive / Dash): Lipat kaki depan dan belakang merapat ke bawah tubuh dengan `Mathf.Lerp` ($0.4\text{s}$).
* Saat kecepatan $< 6\text{ m/s}$ atau dalam status `IsHovering`: Rentangkan kaki menjuntai turun secara rileks.

---

## 📋 MASTER PROMPT EKSEKUSI ANIMASI NAGA UNTUK LAPTOP B (SIAP SALIN)

```markdown
Halo Laptop B! Sesi 4, 4B, dan 5 Dasar telah selesai dan lulus uji 100%.
Sekarang jalankan eksekusi: Implementasi Animasi Tubuh Prosedural Naga Fanamir (`DragonBodyAnimator.cs`) mengacu pada dokumen brain:
- `brain/laptop-a/visual-vfx/arsitektur_animasi_naga_fanamir.md`
- `brain/laptop-a/visual-vfx/prompt_eksekusi_animasi_naga_laptop_b.md`
- `brain/laptop-a/panduan_screenshot_mandiri_dan_laporan.md`

Berikut instruksi teknis yang harus dikerjakan:

### 1. Buat Skrip `DragonBodyAnimator.cs`:
- Buat skrip di `Assets/Scripts/Controllers/DragonBodyAnimator.cs` (namespace `DragonWingAR.Controllers`, `[DefaultExecutionOrder(55)]`).
- Pasangkan ke GameObject `/Dragon_Player` di scene `MainIsland_Gameplay.unity` (dan `Test_Dragon_Flight.unity` jika relevan).
- Auto-cache transform tulang tubuh dari armature `ARM_Dragon` (`Bone_Jaw`, `Spine_01..03`, `Tail_01..05`, dan tulang kaki depan/belakang).

### 2. Artikulasi Rahang Buka Mulut (`Bone_Jaw`):
- Hubungkan rotasi lokal `Bone_Jaw` ke sinyal telemetri `MOUTH_OPEN` (0.0 s.d. 1.0) dari `UDPPoseReceiver`:
  - `MOUTH_OPEN < 0.25` -> Mulut tertutup rapat (0°).
  - `MOUTH_OPEN > 0.40` -> Mulut terbuka lebar (-30° pitch lokal).
  - Buka rahang otomatis saat selebrasi kemenangan desa (Victory Roar).
  - Gunakan `Mathf.SmoothDamp` (0.12s) untuk penghalusan bebas getaran.

### 3. Liukan Ekor Dinamis & Punggung (`Spine & Tail Sway`):
- Liukan Ekor saat Belok: Rotasikan sumbu yaw `Tail_01..05` berlawanan arah sentakan kemudi dengan hierarki fleksi berjenjang (Tail_01: 15%, Tail_02: 30%, Tail_03: 55%, Tail_04: 75%, Tail_05: 100%).
- Cruising Undulation: Tambahkan gelombang sinus santai saat meluncur lurus (frekuensi ~0.6 Hz, amplitudo ~6°).
- Spine Arch: Saat Tuck Dive (#13) lengkungkan Spine ke bawah (-6°/segmen); saat Fly Up (#07) lentingkan Spine ke atas (+5°/segmen).

### 4. Lipatan Kaki & Cakar Aerodinamis:
- Kecepatan >= 12 m/s (Glide/Dive/Dash): Kaki depan dan belakang melipat rapat ke bawah tubuh (Flight Tuck).
- Kecepatan < 6 m/s / Auto-Hover: Kaki menjuntai turun santai (Hover Drop) dengan transisi lerp 0.5s.

### 5. Verifikasi Play Mode & Screenshot:
- Buat skrip verifikasi otomatis Play Mode (`DragonAnimationVerificationRunner.cs`) untuk memvalidasi:
  1. Bukaan rahang saat MOUTH_OPEN aktif.
  2. Defleksi ekor saat kemudi belok aktif.
  3. Transisi lipatan kaki saat kecepatan tinggi vs hover.
  4. Nol regresi terhadap sistem sayap `DragonWingAnimator` dan kemudi naga.
- Ambil tangkapan layar mandiri (`SS_Animasi_Dragon_TailSway.png` dan `SS_Animasi_Dragon_JawOpen.png`) ke `docs/figures/raw_screenshots/`.
- Perbarui `brain/laptop-b/Devlog.md` dan `Tasks.md`, lalu commit dan push ke origin main.
```
