---
title: Rencana Instalasi & Implementasi Skill VFX Effekseer di Laptop B
type: planning
tags:
  - planning
  - vfx
  - effekseer
  - laptop-a
  - laptop-b
  - sesi-4
  - unity-urp
date: 2026-11-15
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[laptop B Patch 1 planning]]"
  - "[[mekanisme_tantangan_desa]]"
  - "[[kontrak_integrasi_ui_dan_progress]]"
---

# 🔮 Rencana Instalasi & Implementasi Skill VFX Effekseer di Laptop B

> **Tujuan Dokumen:** Memberikan panduan menyeluruh, terstruktur, dan siap pakai bagi **Laptop B** untuk memasang paket *Agent Skills* **`kanaworksai-hue/effekseer-vfx-skill`**, menghubungkannya dengan Unity 6 URP, serta mengeksekusi kebutuhan Visual Effects (VFX) pemulihan 3 desa dan dinamika penerbangan **Fanamir** pada **Sesi 4**.

---

## 📌 1. Latar Belakang & Identitas Skill

- **Nama Repositori:** [`kanaworksai-hue/effekseer-vfx-skill`](https://github.com/kanaworksai-hue/effekseer-vfx-skill) (Lisensi: *MIT License*).
- **Fungsi Utama:** Paket keahlian agen AI (*Agent Skills*) yang memandu asisten coding (Antigravity / Gemini CLI / Claude Code / Cursor) untuk merancang, membuat, mengompilasi, serta menguji efek visual partikel *real-time* game secara prosedural menggunakan **Effekseer** (`.efkefc` / `.efk`) maupun lembar animasi (*video-model flipbook sheets*).
- **Struktur Skill yang Disediakan:**
  1. **`effekseer-vfx` (Authoring Skill):**
     - Menerjemahkan deskripsi efek teks menjadi resep partikel terstruktur.
     - Menggunakan ~30 *layer primitives* terverifikasi untuk membentuk emisi, kecepatan, warna, gaya gravitasi/turbulensi, dan kurva umur partikel.
     - Dilengkapi *static linter* dan *compiler* resmi `EffekseerCore` untuk menghasilkan berkas efek biner siap pakai.
  2. **`vfx-review` (Reviewer Skill):**
     - Agen evaluator independen yang memeriksa hasil tangkapan visual/filmstrip animasi untuk menilai timing, kontras, saturasi, dan keterbacaan efek tanpa bias kode resep.

---

## ⚙️ 2. Prasyarat Sistem di Laptop B

Laptop B sebagai mesin eksekusi kode Unity dan Python CV telah memiliki fondasi yang sangat cocok:

| Komponen | Spesifikasi / Status di Laptop B | Catatan Kompatibilitas |
| :--- | :--- | :--- |
| **Sistem Operasi** | Windows 11 x64 | Didukung penuh (*authoring*, *compilation*, & *rendering*). |
| **Python** | Python 3.10+ / 3.11 (`ARDragonSkyGuardian_CV/`) | Memenuhi batas syarat minimum Python 3.10/3.11 untuk tool linter & script Effekseer. |
| **Engine Game** | Unity 6 (`6000.3.7f1`) | Mendukung URP (*Universal Render Pipeline*). |
| **Render Pipeline** | Universal Render Pipeline (URP) | Didukung melalui plugin resmi `EffekseerForUnity` + *EffekseerRendererFeature*. |
| **Node.js / NPX** | Tersedia di lingkungan pengembang | Dibutuhkan jika menggunakan installer otomatis `npx skills add`. |

---

## 🚀 3. Prosedur Instalasi di Laptop B

### Langkah 1: Instalasi Agent Skills ke Workspace

Terdapat dua metode instalasi yang dapat dipilih di Laptop B:

#### Opsi A — Otomatis via NPX Skills CLI (Direkomendasikan)
Jalankan perintah berikut di terminal root workspace Laptop B:
```bash
npx skills add kanaworksai-hue/effekseer-vfx-skill
```
*Perintah ini akan secara otomatis mengunduh dan menempatkan skill `effekseer-vfx` dan `vfx-review` ke dalam direktori skill agen.*

#### Opsi B — Manual Git Clone ke Direktori `.agents/skills/`
Jika ingin menyelaraskan struktur penyimpanan lokal seperti halnya skill Blender eksisting (`.agents/skills/blender-*`):
1. Clone repositori ke folder temporer:
   ```bash
   git clone https://github.com/kanaworksai-hue/effekseer-vfx-skill.git temp_vfx_skill
   ```
2. Salin folder skill ke `.agents/skills/` proyek:
   - Salin `temp_vfx_skill/skills/effekseer-vfx` $\rightarrow$ `.agents/skills/effekseer-vfx/`
   - Salin `temp_vfx_skill/skills/vfx-review` $\rightarrow$ `.agents/skills/effekseer-vfx-review/`
3. Hapus folder `temp_vfx_skill`.
4. Pastikan di dalam masing-masing folder terdapat file `SKILL.md` agar Antigravity langsung mengenali skill tersebut.

---

### Langkah 2: Instalasi Plugin Effekseer di Unity 6 URP

Agar efek berformat `.efkefc` dapat dimuat, dipratinjau, dan diputar langsung di Unity:

1. **Buka Unity Editor di Laptop B** (`AR Dragon Sky Guardian`).
2. Masuk ke menu **Window** $\rightarrow$ **Package Manager**.
3. Klik tombol **`+`** (kiri atas) $\rightarrow$ pilih **`Add package from git URL...`**.
4. Masukkan URL resmi paket Effekseer Unity:
   ```text
   https://github.com/effekseer/EffekseerForUnity.git#package
   ```
   *(Atau unduh berkas release `EffekseerUnity.unitypackage` dari repositori resmi GitHub Effekseer dan impor via `Assets -> Import Package -> Custom Package`).*
5. **Konfigurasi URP Forward Renderer Feature:**
   - Cari aset URP Renderer di project (misal `UniversalRendererData` atau renderer yang aktif digunakan pada `UniversalRenderPipelineAsset`).
   - Pada Inspector, klik **`Add Renderer Feature`** $\rightarrow$ pilih **`Effekseer Renderer Feature`**.
   - Hal ini wajib agar efek partikel Effekseer dirender tepat pada tahap pasca-opaque/transparan kamera Unity URP.

---

### Langkah 3: Verifikasi Kesiapan Skill

Di Laptop B, minta AI Agent untuk memverifikasi skill dengan prompt singkat:
> *"Tolong cek apakah skill `effekseer-vfx` sudah terpasang dan buatkan satu contoh efek partikel sederhana (misal 'sparkle ring') untuk memastikan compiler berjalan lancar."*

---

## 🎨 4. Pemetaan Kebutuhan VFX untuk Game *Fanamir* (Sesi 4)

Mengacu pada arsitektur [[mekanisme_tantangan_desa]] dan [[laptop B Patch 1 planning]], berikut adalah rincian efek visual yang akan dibuat menggunakan skill Effekseer:

```
                                  Katalog VFX Game
                                          │
       ┌──────────────────────────────────┼──────────────────────────────────┐
       │                                  │                                  │
▼ Biome 1: Zephyria                ▼ Biome 2: Pyrewood                ▼ Biome 3: Solaria
• Cleansing Wind Gust              • Water Gathering Vortex           • Celestial Beacon Aura
• Smog Dissipation Particle        • Torrential Water Stream          • Radiant Dew Rain
• Zephyria Restoration Glow        • Steam & Fire Quench FX           • Nature Bloom / Sprout FX
```

---

### A. Desa Zephyria (Smog Valley — Biome 1)
- **Tema:** Pembersihan Kabut Polusi Beracun.
- **Gestur Pemicu:** *Flapping* (Mengepakkan sayap ritmik).
- **Daftar Aset VFX:**
  1. **`VFX_WindGust_Flap` (Kepakan Pembersih):**
     - Emisi gelombang angin putih/hijau toska berbentuk busur sabit (*crescent wind blade*) yang meluncur ke depan setiap kali terjadi impuls flap sayap.
     - Partikel pusaran angin (*spiral swirl*) di sekitar sayap naga.
  2. **`VFX_Smog_Dissipation` (Penguraian Asap):**
     - Partikel kabut kelabu-keunguan tebal yang menggantung di desa secara bertahap memudar (*alpha fade-out*), terpecah menjadi serpihan cahaya kecil, menandakan udara kembali bersih.
  3. **`VFX_Zephyria_Restoration_Glow` (Pemberkatan Desa):**
     - Pendaran partikel hembusan angin sejuk lembut (*sparkling breeze*) yang membalut kincir angin dan rumah desa saat progress mencapai $100\%$.

---

### B. Desa Pyrewood (Wildfire Woods — Biome 2)
- **Tema:** Pemadaman Kebakaran Hutan & Semburan Air Naga.
- **Gestur Pemicu:** Mengambil air di Danau Zephyria $\rightarrow$ *Water Breath* (Membuka mulut / Menangkupkan tangan di dada).
- **Daftar Aset VFX:**
  1. **`VFX_Water_Gathering` (Pengisian Air):**
     - Efek hisapan partikel tetesan air (*inward spiral water droplets*) dari permukaan danau ke arah tubuh naga saat naga berada di radius pengisian air.
  2. **`VFX_Dragon_WaterBreath_Stream` (Semburan Air Deras):**
     - Semburan kolom air deras bertekanan tinggi (*high-velocity jet stream*) yang keluar dari mulut Fanamir (`Mouth_Socket`).
     - Percikan air berbusa (*turbulent water splash & foam spray*) di ujung benturan semburan.
  3. **`VFX_Wildfire_Flames` & `VFX_Fire_Extinguish_Steam` (Pemadaman Api):**
     - Api hutan oranye berkobar yang bertransisi menjadi kepulan asap uap air putih tebal (*dense white steam billow*) seketika tersentuh semburan air naga.

---

### C. Desa Solaria (Drought Plateau — Biome 3)
- **Tema:** Ritual Pemanggilan Hujan Embun & Pemulihan Tanah Retak.
- **Gestur Pemicu:** *Raise Both Hands / Dew Summoning* (Mengangkat kedua tangan stabil di atas kepala).
- **Daftar Aset VFX:**
  1. **`VFX_DewSummon_Aura` (Aura Pemanggilan Embun):**
     - Lingkaran cincin sihir kristal embun bercahaya zamrud/cyan lembut yang mengorbit di sekeliling Fanamir saat naga melakukan auto-hover dalam pose angkat tangan.
  2. **`VFX_Radiant_DewRain` (Hujan Embun Kristal):**
     - Tetesan air hujan berkilau keemasan-kebiruan turun dari langit di atas plato Solaria, memancarkan pendaran cahaya lembut saat menyentuh permukaan tanah retak.
  3. **`VFX_Solaria_Bloom_Sprouts` (Tunas Kehidupan):**
     - Efek partikel bunga/tunas hijau yang bermekaran (*sprout sparkle particles*) dan pemulihan air danau kering yang kembali berair jernih.

---

### D. VFX Pelengkap Penerbangan Fanamir (Locomotion Feedback)
1. **`VFX_Wingtip_Trail` (Pita Udara Ujung Sayap):**
   - Ribbon trail tipis di kedua ujung sayap (`Wing_Socket.L` & `Wing_Socket.R`) yang memanjang saat manuver belok tajam atau meluncur cepat (*Glide*).
2. **`VFX_Dive_WindCone` (Kerucut Kecepatan Menukik):**
   - Garis-garis distorsi kecepatan tinggi dan kerucut angin di sekitar kepala naga saat memasuki mode *Tuck to Dive* ($28\text{ m/s}$).
3. **`VFX_Dash_SonicRing` (Cincin Sonik Dash):**
   - Cincin kejut uap (*shockwave ring*) yang tertinggal di belakang saat manuver *Propeller Dash* ($26\text{ m/s}$).

---

## 🛠️ 5. Skema Integrasi Kode di Unity 6 (Laptop B)

```
[Gestur Pemain (CV UDP 5052)]
              │
              ▼
    [DragonFlightController] 
              │ (Posisi mulut & sayap Fanamir)
              ├────────────────────────────────────────┐
              ▼                                        ▼
    [VillageZoneController]                [EffekseerEmitter Component]
  (Auto-Hover, Stage Timer,                  (Play / Stop / Crossfade
   State Tantangan Desa)                       Instance VFX .efkefc)
              │                                        │
              ▼                                        ▼
   [VillageChallengeUI]                    [Tampilan Visual Layar (URP)]
   (Progress 0% -> 100%)                   (Pusaran Angin / Air / Hujan Embun)
```

### Integrasi Komponen Skrip:
- **`EffekseerEmitter`**: Pasang komponen bawaan Effekseer pada titik socket naga (misal `GameObject Socket_Mouth` untuk Water Breath, `Socket_WingTip_L/R` untuk ribbon).
- **`VillageZoneController.cs` (Sesi 4)**:
  ```csharp
  // Contoh cuplikan pengontrolan VFX desa di VillageZoneController:
  [Header("VFX Effekseer Emitters")]
  [SerializeField] private EffekseerEmitter villageProblemVfx;     // Misal: Api/Asap
  [SerializeField] private EffekseerEmitter villageActionSuccessVfx; // Misal: Semburan Air/Angin
  [SerializeField] private EffekseerEmitter villageRestoredVfx;     // Misal: Hujan Embun/Kilau Hijau

  public void OnActionProgressUpdated(float progress01)
  {
      // Skalakan intensitas atau trigger efek pembersihan
      if (progress01 >= 1.0f)
      {
          villageProblemVfx?.Stop();
          villageRestoredVfx?.Play();
      }
  }
  ```

---

## 📋 6. Checklist Eksekusi untuk Laptop B

- [ ] **Langkah 1:** Jalankan `npx skills add kanaworksai-hue/effekseer-vfx-skill` (atau clone manual ke `.agents/skills/`).
- [ ] **Langkah 2:** Impor paket `EffekseerForUnity` ke Unity 6 URP via Unity Package Manager git URL.
- [ ] **Langkah 3:** Tambahkan `EffekseerRendererFeature` pada aset URP Forward Renderer di project Unity.
- [ ] **Langkah 4:** Instruksikan agen AI di Laptop B untuk mengompilasi ketiga efek tantangan desa utama (`VFX_WindGust_Flap`, `VFX_Dragon_WaterBreath_Stream`, `VFX_Radiant_DewRain`).
- [ ] **Langkah 5:** Pasang prefab partikel hasil ekspor ke `Session4_VillageBuilder.cs` / `VillageZoneController.cs` dan verifikasi sinkronisasinya dengan `VillageChallengeUI` serta `GlobalVitalityBar`.

---

[[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[laptop B Patch 1 planning]] · [[mekanisme_tantangan_desa]]
