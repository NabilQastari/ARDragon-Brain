---
title: "Perencanaan Pemasangan Skill VFX Effekseer dan Integrasi Unity URP untuk Laptop B"
type: session-memory
tags:
  - session-memory
  - laptop-a
  - vfx
  - effekseer
  - planning
  - unity-urp
  - sesi-4
date: 2026-11-18
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[rencana_skill_vfx_effekseer_laptop_b]]"
  - "[[laptop B Patch 1 planning]]"
  - "[[mekanisme_tantangan_desa]]"
  - "[[kontrak_integrasi_ui_dan_progress]]"
---

# Perencanaan Pemasangan Skill VFX Effekseer dan Integrasi Unity URP untuk Laptop B

## 1. Tujuan & Konteks Sesi
Sesi ini bertujuan merespons permintaan pengguna untuk menyusun perencanaan teknis pemasangan paket *Agent Skills* **`kanaworksai-hue/effekseer-vfx-skill`** di **Laptop B** guna kebutuhan pembuatan *Visual Effects* (VFX) pemulihan 3 desa dan dinamika penerbangan **Fanamir** pada **Sesi 4**.

Sebagai **Laptop A** (fokus desain, ide, & arsitektur), seluruh perencanaan dirumuskan secara komprehensif tanpa menyentuh atau mengedit file di folder `brain/laptop-b/`, lalu didokumentasikan ke dalam *single source of truth* arsitektur di `brain/laptop-a/rencana_skill_vfx_effekseer_laptop_b.md` serta dihubungkan ke dokumen-dokumen pendukung.

---

## 2. Keputusan Desain / Arsitektur / Teknis

### A. Adopsi Skill `kanaworksai-hue/effekseer-vfx-skill`
- **Profil & Lisensi:** Paket open-source berlisensi MIT yang dirancang khusus untuk memandu AI coding agent (Antigravity / Gemini CLI / Cursor) membuat efek partikel game *real-time* biner (`.efkefc` / `.efk`) maupun lembar animasi (*video-model flipbook sheets*).
- **Dual Skill Structure:**
  1. **`effekseer-vfx` (Authoring Skill):** Mengubah prompt natural language menjadi resep partikel dari ~30 *layer primitives*, mengompilasi biner via `EffekseerCore`, dan melakukan *linting* otomatis.
  2. **`vfx-review` (Reviewer Skill):** Agen evaluator independen yang memeriksa filmstrip visual hasil render efek untuk menilai timing, kontras warna, dan keterbacaan efek tanpa bias kode resep.

### B. Strategi Integrasi Engine di Laptop B
- **Lingkungan Target:** Windows 11 x64, Python 3.10+/3.11 (`ARDragonSkyGuardian_CV/`), Unity 6 (`6000.3.7f1`) dengan *Universal Render Pipeline* (URP).
- **Prosedur Pemasangan Skill:**
  - *Opsi A (CLI Otomatis):* `npx skills add kanaworksai-hue/effekseer-vfx-skill`.
  - *Opsi B (Manual):* Git clone ke direktori lokal `.agents/skills/effekseer-vfx` dan `.agents/skills/effekseer-vfx-review`.
- **Integrasi Unity URP:** Mengimpor plugin resmi `EffekseerForUnity` via UPM Git URL (`https://github.com/effekseer/EffekseerForUnity.git#package`) dan wajib mendaftarkan `EffekseerRendererFeature` pada aset *Universal Renderer Data* agar partikel ter-render di pass kamera gameplay.

### C. Pemetaan Katalog Kebutuhan VFX Game *Fanamir* (Sesi 4)
1. **Desa Zephyria (Biome 1 — Smog Valley):**
   - Gestur pemicu: *Flapping* (Mengepakkan sayap berkali-kali).
   - Kebutuhan aset: `VFX_WindGust_Flap` (busur sabit angin & pusaran spiral), `VFX_Smog_Dissipation` (penguraian partikel kabut polusi ungu/kelabu menjadi udara transparan), dan `VFX_Zephyria_Restoration_Glow` (pemberkatan angin sepoi-sepoi di desa).
2. **Desa Pyrewood (Biome 2 — Wildfire Woods):**
   - Gestur pemicu: Isi air di Danau Zephyria $\rightarrow$ *Water Breath* (membuka mulut lebar atau menangkupkan tangan di dada).
   - Kebutuhan aset: `VFX_Water_Gathering` (hisapan pusaran air di danau ke tubuh naga), `VFX_Dragon_WaterBreath_Stream` (semburan air deras bertekanan tinggi dari socket mulut naga), dan `VFX_Fire_Extinguish_Steam` (kepulan uap air putih tebal saat kobaran api padam).
3. **Desa Solaria (Biome 3 — Drought Plateau):**
   - Gestur pemicu: *Raise Both Hands / Dew Summoning* (mengangkat kedua tangan stabil di atas kepala saat auto-hover).
   - Kebutuhan aset: `VFX_DewSummon_Aura` (cincin kristal embun zamrud/cyan di sekeliling naga), `VFX_Radiant_DewRain` (hujan rintik kristal pemulih yang turun membasahi tanah retak), dan `VFX_Solaria_Bloom_Sprouts` (tunas hijau mekar dan danau terisi air jernih).
4. **VFX Dinamika Penerbangan (Locomotion Feedback):**
   - `VFX_Wingtip_Trail` (pita udara di ujung sayap kiri & kanan saat *Glide* / belok tajam).
   - `VFX_Dive_WindCone` (kerucut distorsi angin saat manuver *Tuck Dive* $28\text{ m/s}$).
   - `VFX_Dash_SonicRing` (cincin kejut sonik di belakang tubuh saat *Propeller Dash* $26\text{ m/s}$).

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset

1. **Pembuatan Dokumen Panduan Utama:**
   - Dibuat berkas [`rencana_skill_vfx_effekseer_laptop_b.md`](file:///e:/Semester%205/Game/AR%20Dragon/brain/laptop-a/rencana_skill_vfx_effekseer_laptop_b.md) di `brain/laptop-a/` berisi latar belakang, prasyarat sistem, 2 metode instalasi, setup plugin Unity URP, katalog lengkap VFX 3 desa & naga, cuplikan kode integrasi C# `VillageZoneController.cs`, dan checklist eksekusi Laptop B.
2. **Pembaruan Dokumen Perencanaan Laptop B:**
   - Diperbarui [`laptop B Patch 1 planning.md`](file:///e:/Semester%205/Game/AR%20Dragon/brain/laptop-a/laptop%20B%20Patch%201%20planning.md) pada poin 5 (*Sesi 4 — VillageZoneController.cs, VFX Effekseer, & Feature-to-UI Sync*) dengan menautkan rencana instalasi skill dan rincian efek pemulihan.
3. **Pembaruan Peta Pengetahuan & Pelacak Tugas:**
   - Diperbarui [`00 - Overview.md`](file:///e:/Semester%205/Game/AR%20Dragon/brain/00%20-%20Overview.md) menambahkan tautan dokumen rencana VFX di bawah daftar dokumen Laptop A.
   - Diperbarui [`Tasks.md`](file:///e:/Semester%205/Game/AR%20Dragon/brain/laptop-a/Tasks.md) dengan menambahkan item fokus aktif pemantauan instalasi skill VFX di Laptop B serta mencatat penyusunan rencana di daftar tugas selesai.
   - Diperbarui [`Devlog.md`](file:///e:/Semester%205/Game/AR%20Dragon/brain/laptop-a/Devlog.md) dengan mencatat entri kronologis keputusan arsitektur VFX Effekseer.
4. **Sinkronisasi Git Vault Brain:**
   - Dilakukan `git add`, `git commit` (`docs(laptop-a): add planning for effekseer-vfx-skill installation and game vfx roadmap for laptop b`), dan `git push` ke repositori `NabilQastari/ARDragon-Brain` branch `main` (`0f770cc`).

---

## 4. Detail Penting yang Harus Diingat

- **URL Repositori Skill:** `https://github.com/kanaworksai-hue/effekseer-vfx-skill.git` (perintah: `npx skills add kanaworksai-hue/effekseer-vfx-skill`).
- **URL Paket Unity Effekseer:** `https://github.com/effekseer/EffekseerForUnity.git#package` (UPM Git URL).
- **Aturan Rendering URP:** Wajib menambahkan `EffekseerRendererFeature` pada aset URP Forward Renderer (`UniversalRendererData`), jika tidak maka partikel `.efkefc` tidak akan muncul pada kamera game.
- **Titik Pemasangan Sockets Naga:**
  - Mulut naga: `Mouth_Socket` (untuk `VFX_Dragon_WaterBreath_Stream`).
  - Ujung sayap naga: `Wing_Socket.L` & `Wing_Socket.R` (untuk `VFX_Wingtip_Trail`).
- **Aturan Kolaborasi:** Laptop A hanya memproduksi dokumen desain & arsitektur di `brain/laptop-a/`; implementasi package dan pembuatan partikel diserahkan ke sesi di Laptop B.

---

## 5. Tugas Tersisa & Langkah Selanjutnya

- [ ] Di Laptop B: Jalankan instalasi `npx skills add kanaworksai-hue/effekseer-vfx-skill` (atau manual clone ke `.agents/skills/`).
- [ ] Di Laptop B: Impor `EffekseerForUnity` ke project Unity 6 URP dan daftarkan `EffekseerRendererFeature`.
- [ ] Di Laptop B: Uji compile efek uji coba dengan agen AI, lalu mulai pembuatan 3 efek utama desa (`VFX_WindGust_Flap`, `VFX_Dragon_WaterBreath_Stream`, `VFX_Radiant_DewRain`).
- [ ] Di Laptop B: Hubungkan komponen `EffekseerEmitter` ke `VillageZoneController.cs` dan integrasikan ke `VillageChallengeUI` serta `GlobalVitalityBar`.

---

[[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[rencana_skill_vfx_effekseer_laptop_b]] · [[laptop B Patch 1 planning]]
