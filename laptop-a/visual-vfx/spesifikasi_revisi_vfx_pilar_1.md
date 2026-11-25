---
title: "Spesifikasi Revisi VFX (Pilar 1): Perombakan 5 VFX Eksisting & 3 VFX Baru (Three.js Studio + Unity 6)"
type: specification
tags:
  - specification
  - vfx
  - effekseer
  - pilar-1
  - threejs
  - unity6
  - laptop-a
owner: Laptop A
updated: 2026-11-25
status: active
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[visual-vfx/planning_vfx_fov_skybox_dan_animasi]]"
  - "[[gameplay-mechanics/planning_post_animasi_vfx_ui_dan_kontrol]]"
---

# ✨ Spesifikasi Revisi VFX (Pilar 1): Perombakan 5 VFX & Penambahan 3 VFX Baru
## Fanamir: The Sky Guardian *(Internal Project: AR Dragon)*

> **Dokumen Resmi Desain & Spesifikasi VFX (Laptop A):**
> Merespons evaluasi visual langsung pengguna pada Three.js VFX Studio (`dragon_anim_studio/` `v22`) atas paket `Export_VFX_LaptopB`. Seluruh perubahan dirancang terlebih dahulu di `dragon_anim_studio/vfx/recipes/` + `vfx_player.js` (dengan *Ribbon Trail Renderer* baru) agar pengguna dapat meninjau hasilnya secara $360^\circ$ sebelum diteruskan sebagai Master Prompt eksekusi Unity Effekseer ke **Laptop B**.

---

## 📋 Matriks Ringkas Perubahan 10 VFX (`5` Reworked + `3` Baru + `2` Dipertahankan)

| No | ID / Nama Efek | Status | Ringkasan Perubahan & Target Socket |
| :-: | :--- | :---: | :--- |
| 1 | **`VFX_Zephyria_WindGust`** | 🔄 **Reworked** | Mengganti tebasan sabit kaku (*slash arc*) menjadi arus angin nyata: 4 pita angin tipis berkibar (`T_WispTrail01`), 28 garis *wind shear* cepat, pusaran eddy angin kecil, debu/daun bercahaya, & kabut sepoi. |
| 2 | **`VFX_Lake_WaterSiphon`** | 🔄 **Reworked** | Menghapus silinder beam tebal di danau; diubah menjadi aura energi uap air (*evaporating energy veil* Fresnel tipis + benang uap naik + butiran cahaya menguap + riak permukaan). |
| 3 | **`VFX_Lake_MouthIntake`** | 🆕 **Baru** | Efek hisapan energi air meluncur masuk langsung ke dalam rongga mulut naga yang terbuka (`Head` local `(0, 0.34, -0.04)`) selama proses pengisian di Danau Zephyria (`#15B-1`). |
| 4 | **`VFX_Solaria_CelestialDewRain`** | 🔄 **Reworked** | Mengurangi kepekatan pilar emas agar tidak menutup badan naga; menambah variasi 3 cincin halo bertingkat, bintang berkelip, cipratan rintik di tanah, & memperpanjang durasi hujan embun di Unity menjadi **`12–15 detik`** pasca pemulihan 100%. |
| 5 | **`VFX_Solaria_ChestGemGlow`** | 🆕 **Baru** | Efek sinar permata dada bercahaya (`T_Flare01` + `T_Star01` + partikel kilau) yang tertanam tepat pada tulang `Spine_02` lokal `(0, 0.14, -0.17)` saat ritual Solaria. |
| 6 | **`VFX_Pyrewood_Wildfire`** | 🆕 **Baru** | Efek kebakaran hutan aktif di Biome 2 (*Pyrewood*): lidah api oranye-emas beranimasi *flipbook 4×4*, asap gelap membubung, & bara api terbang. Di Unity ditempatkan di gugus pohon terbakar dan mengecil (`1.0 → 0.0`) mengikuti progres pemadaman. |
| 7 | **`VFX_Flight_SonicDashShockwave`** | 🔄 **Reworked** | Mengubah cincin kejut tunggal redup menjadi terowongan garis-garis angin berdensitas tinggi (`34` garis dalam + `26` garis halus luar + selubung Fresnel ringan) yang menyapu tubuh naga. |
| 8 | **`VFX_Flight_WingtipTrails`** | 🔄 **Reworked** | Mengganti titik-titik asap samar menjadi **pita angin halus meruncing (*True Ribbon Trail*)** berpadu garis *slipstream* tipis; di Unity memakai offset `wingtipBoneLength = 1.48f` (`1.1866 * 1.25`) atau Unity `TrailRenderer`. |
| 9 | **`VFX_Pyrewood_WaterBreath`** | 🔧 **Socket Fix** | Di Unity `DragonVfxController.cs`, rotasi wajib diselaraskan ke arah moncong tulang `Head` (bukan `visualModel`) agar semburan ikut menyapu saat kepala menoleh/menunduk. |
| 10 | **`VFX_Pyrewood_ExtinguishSteam`** | ✅ **Siap** | Dipertahankan; telah teruji menyemburkan cincin kejut uap & kepulan asap putih *flipbook* saat api padam. |

---

## 🛠️ Detail Teknis Implementasi Target di Unity 6 (`Laptop B`)

1. **`VFX_Pyrewood_Wildfire` (Hutan Kebakaran Biome 2):**
   - Pasang beberapa instance emitter di area gugus pohon terbakar (`SubMarker_HutanTerbakar` & rim pohon gosong Biome 2).
   - Skalakan emisi / `transform.localScale` secara proporsional dengan sisa krisis `1.0f - pyrewoodProgress` sehingga kobaran api meredup dan padam bertahap saat disembur `WaterBreath`, lalu berhenti total ketika `100%` pulih.
2. **`VFX_Solaria_CelestialDewRain` (Durasi Hujan Diperpanjang):**
   - Selain aktif *looping* saat ritual `#15C` berlangsung, ubah momen pemulihan `100%` Desa Solaria di `VillageZoneController.cs` dari *one-shot* singkat menjadi hujan perayaan berkepanjangan selama **`12–15 detik`** (`solariaPostRestoreRainTimer = 14f`) agar suasana plato gersang yang dibasahi embun terasa jauh lebih magis.
3. **`VFX_Lake_MouthIntake` + `VFX_Solaria_ChestGemGlow` (Bone-Socketed Emitters):**
   - `VFX_Lake_MouthIntake` diikat ke `mouthBoneOverride` (`Head`, menghadap arah hisapan masuk ke mulut), aktif bersamaan dengan `VFX_Lake_WaterSiphon` saat Auto-Hover di Danau Zephyria.
   - `VFX_Solaria_ChestGemGlow` diikat ke tulang `Spine_02` (offset lokal bagian depan dada/permata), aktif saat pose `#15C Solaria` & selebrasi pemulihan.
4. **Koreksi Socket & Pita Sayap (`DragonVfxController.cs`):**
   - Perbaiki `wingtipBoneLength` dari `1.95f` $\to$ **`1.48f`** (`1.1866 * 1.25`) dan pertimbangkan komponen pendukung `TrailRenderer` tipis pada ujung `Wing_Finger_Outer.L/R` untuk pita angin mulus tanpa putus di kecepatan `>= 18 m/s`.
   - Perbaiki rotasi `waterBreathEmitter` mengikuti arah moncong aktual `Head` (bukan `visualModel.rotation`).

---

*Navigasi:*
[[00 - Overview]] | [[Tasks]] | [[Devlog]] | [[visual-vfx/planning_vfx_fov_skybox_dan_animasi]] | [[gameplay-mechanics/planning_post_animasi_vfx_ui_dan_kontrol]]
