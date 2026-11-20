---
title: Prompt Eksekusi Sistem VFX Effekseer, Skybox Dinamis, & Dynamic FOV (Laptop B)
tags:
  - prompt
  - laptop-b
  - vfx
  - effekseer
  - camera-fov
  - skybox
  - unity-urp
owner: Laptop A
updated: 2026-11-21
status: ready-for-execution
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[planning_vfx_fov_skybox_dan_animasi]]"
  - "[[rencana_skill_vfx_effekseer_laptop_b]]"
  - "[[mekanisme_tantangan_desa]]"
---

# 🚀 Prompt Eksekusi Sistem VFX Effekseer, Skybox Dinamis, & Dynamic FOV untuk Laptop B

> **Petunjuk untuk Pengguna:** Salin (*copy*) seluruh isi kotak **"MASTER PROMPT EKSEKUSI UNTUK LAPTOP B"** di bagian akhir dokumen ini, lalu kirimkan langsung ke AI Agent di **Laptop B** setelah menjalankan `git -C brain pull`.

---

## 🧭 Ikhtisar Arsitektur & Target Eksekusi (Laptop B)

Sesi 4 (Mekanisme Inti 3 Desa) telah selesai dan terverifikasi 100% di Laptop B. Langkah selanjutnya adalah menyematkan **tiga pilar visual dan atmosfer** agar game terasa hidup, megah, dan sensasi kecepatan terbangnya maksimal:

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│                     TRIO SISTEM VISUAL & ATMOSFER (SESI 4B)                 │
├──────────────────────────┬──────────────────────────┬───────────────────────┤
│ 1. DYNAMIC CAMERA FOV    │ 2. DYNAMIC SKYBOX & FOG  │ 3. VFX EFFEKSEER      │
│ • Rentang 60° (Hover)    │ • Lautan: Biru Fantasi   │ • 7 Berkas .efkefc    │
│   hingga 85° (Dash)      │ • Krisis: Smog, Api, Debu│ • Emitter 3 Desa      │
│ • Pull-Back -6.0m -> -7.5m│ • Pulih: Langit Terbuka  │ • Wingtip & Sonic     │
│ • SmoothDamp 0.30s       │ • URP Horizon Fog Lerp   │   Dash Shockwave      │
└──────────────────────────┴──────────────────────────┴───────────────────────┘
```

---

## 📋 Rincian 4 Tahapan Eksekusi di Laptop B

### Tahap 1: Pemasangan Skill Agen & Plugin Effekseer URP
1. Pasang skill Effekseer di root workspace:
   ```bash
   npx skills add kanaworksai-hue/effekseer-vfx-skill
   ```
   *(Atau git clone ke `.agents/skills/effekseer-vfx` dan `.agents/skills/effekseer-vfx-review`).*
2. Tambahkan Package Effekseer ke Unity Package Manager (`Packages/manifest.json`):
   ```text
   https://github.com/effekseer/EffekseerForUnity.git#package
   ```
3. Tambahkan **`Effekseer Renderer Feature`** pada aset URP Renderer aktif (`UniversalRendererData`).

---

### Tahap 2: Pembuatan 7 Aset Partikel Effekseer (`.efkefc`)
Gunakan skill `effekseer-vfx` untuk menghasilkan berkas efek di folder `Assets/VFX/Effekseer/`:
1. `VFX_Zephyria_WindGust.efkefc`: Gelombang sabit angin toska transparan berhembus ke lembah.
2. `VFX_Lake_WaterSiphon.efkefc`: Spiral partikel air jernih tersedot ke dada naga saat auto-hover di danau.
3. `VFX_Pyrewood_WaterBreath.efkefc`: Kolom semburan jet air berkabut deras dari soket mulut naga.
4. `VFX_Pyrewood_ExtinguishSteam.efkefc`: Letupan uap air putih tebal saat api pohon padam.
5. `VFX_Solaria_CelestialDewRain.efkefc`: Pilar cahaya keemasan dari langit + hujan rintik embun berkilau.
6. `VFX_Flight_WingtipTrails.efkefc`: Pita uap tipis bercahaya di ujung kedua sayap saat kecepatan $\ge 18\text{ m/s}$ atau belok tajam.
7. `VFX_Flight_SonicDashShockwave.efkefc`: Cincin gelombang kejut sonik di belakang naga saat *Propeller Dash* (`#14`).

---

### Tahap 3: Implementasi Dynamic Camera FOV (`DynamicFlightCameraController.cs`)
* Pasang atau gabungkan ke sistem kamera naga (`FixedFlightCamera` / `UpdateCameraPolish`):
  * **Target FOV:**
    * Grounded / Auto-Hover: **`60°`** (Netral, Z Offset `-6.0 m`).
    * Cruising Glide: **`65°–68°`** (Z Offset `-6.3 m`).
    * Tuck to Dive (`#13`): **`78°–80°`** (Z Offset `-7.2 m`).
    * Propeller Dash (`#14`): **`85°`** (Z Offset `-7.5 m`).
  * Gunakan `Mathf.SmoothDamp` (waktu redam $0.30\text{ detik}$) untuk transisi FOV dan jarak Z.
  * Tambahkan getaran impuls mikro (*subtle camera rumble*) saat *Tuck Dive* dan semburan air *Water Breath*.

---

### Tahap 4: Implementasi Skybox & Atmosfer Dinamis (`BiomeAtmosphereController.cs`)
* Tempatkan controller di scene `MainIsland_Gameplay.unity`:
  * **Penyatuan Horizon:** `RenderSettings.fogColor` disamakan dengan warna bawah horizon skybox, menghilangkan garis potong tepi laut pada jarak kliping jauh ($>2000\text{ m}$).
  * **Transisi Radius Biome ($<250\text{ m}$):**
    * *Zephyria Krisis:* Kabut kelabu kehijauan beracun (*toxic smog*), jarak kabut memendek ke $150\text{ m}$.
    * *Pyrewood Krisis:* Kabut oranye membara, abu api, matahari tertutup asap hitam.
    * *Solaria Krisis:* Langit pudar terik kekuningan, kabut debu panas bergelombang.
  * **Transisi Pemulihan (100% Restored):**
    * Begitu `VillageZoneController.CompleteVillageRestoration()` dipanggil, interpolasikan langit dan kabut selama $2.5–3.0\text{ detik}$ kembali ke langit cerah toska (Zephyria), hijau hutan sejuk (Pyrewood), atau pilar keemasan (Solaria).

---

### Tahap 5: Hubungkan ke Gameplay & Screenshot Mandiri
1. Sambungkan pemicu emisi partikel ke `VillageZoneController.cs` dan `DragonFlightController.cs`.
2. Jalankan pengujian Play Mode otomatis (`VFXAtmosphereVerificationRunner.cs`) untuk memastikan 0 compilation error dan 0 warning.
3. Ambil tangkapan layar mandiri (gameplay berpartikel & langit dinamis) dan simpan ke `docs/figures/raw_screenshots/` sesuai [[panduan_screenshot_mandiri_dan_laporan]].

---

## 📋 MASTER PROMPT EKSEKUSI UNTUK LAPTOP B (SIAP SALIN)

```markdown
Halo Laptop B! Sesi 4 (Mekanisme Inti 3 Desa) telah selesai dan diverifikasi lulus 100%. 
Sekarang jalankan eksekusi Sesi 4B: Implementasi Paket Visual & Flight Feel (VFX Effekseer, Dynamic Camera FOV 60°–85°, & Dynamic Biome Skybox/Fog) mengacu pada dokumen brain:
- `brain/laptop-a/planning_vfx_fov_skybox_dan_animasi.md`
- `brain/laptop-a/rencana_skill_vfx_effekseer_laptop_b.md`
- `brain/laptop-a/panduan_screenshot_mandiri_dan_laporan.md`

Berikut instruksi langkah demi langkah yang harus dikerjakan:

### 1. Pemasangan Plugin Effekseer Unity URP & Skill Agen:
- Pasang skill agen di workspace: `npx skills add kanaworksai-hue/effekseer-vfx-skill` (atau clone repo ke `.agents/skills/`).
- Pasang package Unity `https://github.com/effekseer/EffekseerForUnity.git#package` di Unity Package Manager.
- Daftarkan `Effekseer Renderer Feature` pada UniversalRendererData (URP Forward Renderer).

### 2. Pembuatan & Kompilasi 7 Berkas Partikel Effekseer (`Assets/VFX/Effekseer/`):
Gunakan skill `effekseer-vfx` untuk membuat 7 berkas partikel:
1. `VFX_Zephyria_WindGust.efkefc`: Gelombang sabit angin toska transparan saat mengepak (#06).
2. `VFX_Lake_WaterSiphon.efkefc`: Spiral air berkilau naik ke dada naga saat di danau Zephyria.
3. `VFX_Pyrewood_WaterBreath.efkefc`: Jet semburan air berkabut deras dari mulut naga (#15B).
4. `VFX_Pyrewood_ExtinguishSteam.efkefc`: Uap air putih tebal saat api pohon padam.
5. `VFX_Solaria_CelestialDewRain.efkefc`: Pilar cahaya keemasan + rintik embun berkilau (#15C).
6. `VFX_Flight_WingtipTrails.efkefc`: Pita uap bercahaya di ujung kedua sayap saat kecepatan >= 18 m/s atau belok tajam.
7. `VFX_Flight_SonicDashShockwave.efkefc`: Cincin gelombang kejut sonik di ekor naga saat Propeller Dash (#14).

### 3. Implementasi Dynamic Camera FOV (60°–85°):
- Perbarui sistem kamera naga (`DragonFlightController` / kamera penerbangan):
  - Auto-Hover / Grounded: 60° (Offset Z: -6.0m)
  - Cruising Glide: 65°–68° (Offset Z: -6.3m)
  - Tuck to Dive (#13): 78°–80° (Offset Z: -7.2m) + micro-rumble
  - Propeller Dash (#14): 85° (Offset Z: -7.5m)
  - Interpolasikan perubahan FOV & jarak kamera menggunakan `Mathf.SmoothDamp` (smoothTime: 0.30s).

### 4. Implementasi `BiomeAtmosphereController.cs` (Skybox & URP Fog Dinamis):
- Buat skrip `BiomeAtmosphereController.cs` dan pasang di `MainIsland_Gameplay.unity`.
- Selaraskan `RenderSettings.fogColor` dengan warna batas horizon skybox untuk menghilangkan batas potong laut.
- Saat naga mendekati desa (< 250m) yang belum selesai (Unvisited / In-Progress):
  - Zephyria: lerp ke toxic smog kelabu-hijau & kabut tebal 150m.
  - Pyrewood: lerp ke langit membara oranye pekat & abu api beterbangan.
  - Solaria: lerp ke langit terik pucat berdebu & kabut panas.
- Saat `CompleteVillageRestoration()` dipanggil (100% pulih):
  - Lerp atmosfer selama 2.5–3.0 detik menjadi langit cerah bersih yang hidup (toska segar, hijau hutan sejuk, atau pilar keemasan).

### 5. Integrasi Gameplay, Verifikasi Play Mode, & Screenshot:
- Pasang emitter partikel pada naga (`/Dragon_Player`) dan `VillageZoneController.cs`.
- Jalankan verifikasi Play Mode Unity untuk memastikan 0 error dan non-regresi terhadap Sesi 4.
- Ambil tangkapan layar mandiri (C# ScreenCapture atau Python ImageGrab) dan simpan ke `docs/figures/raw_screenshots/` dengan nama yang rapi.
- Perbarui `brain/laptop-b/Devlog.md` dan `Tasks.md`, lalu commit dan push ke origin main.
```
