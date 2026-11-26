---
title: Spesifikasi Revisi Skybox Fantasi Stylized & Awan Dinamis 3 Biome
type: specification
tags:
  - skybox
  - shader
  - urp
  - atmosphere
  - visual-vfx
  - laptop-a
owner: Laptop A
updated: 2026-11-26
status: ready-for-execution
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[visual-vfx/planning_vfx_fov_skybox_dan_animasi]]"
  - "[[handoff-laptop-b/prompt_eksekusi_revisi_skybox_laptop_b]]"
---

# 🌤️ Spesifikasi Revisi Skybox Fantasi *Stylized* & Awan Dinamis 3 Biome

> **Latar Belakang Masalah:** Pada implementasi Sesi 4B awal, `DynamicBiomeSkybox.shader` baru berupa gradasi warna polos tanpa lapisan awan *stylized* maupun piringan/halo matahari yang kuat, dan baru di-set saat Play Mode (`InitRuntimeSkybox()`). Akibatnya, di area *NeutralOcean* maupun di *Edit Mode*, langit masih tampak mirip dengan `Default-Skybox` prosedural bawaan Unity.

---

## 🎯 Tujuan Revisi
1. **Identitas Visual Jelas (*Ghibli / Genshin Fantasy Sky*):** Mengubah langit menjadi kubah langit fantasi kaya warna dengan **gradasi 3-stop**, **awan kumulus *stylized* berlapis** (sisi terang + bayangan awan + *silver lining* matahari), **awan cirrus tinggi**, dan **piringan matahari + halo atmosfer**.
2. **Terlihat Sejak Edit Mode & Frame Pertama Gameplay:** Memasang `Mat_DynamicBiomeSkybox.mat` langsung pada `RenderSettings.skybox` (*Lighting Settings*) di `MainIsland_Gameplay.unity` (dan opsional `Test_Dragon_Flight.unity`), bukan hanya mengandalkan `Awake()` runtime.
3. **Tetap 100% Kompatibel dengan `BiomeAtmosphereController.cs` & `VFXAtmosphereVerificationRunner`:** Mempertahankan seluruh nama state (`NeutralOcean`, `ZephyriaCrisis`, `ZephyriaRestored`, `PyrewoodCrisis`, `PyrewoodRestored`, `SolariaCrisis`, `SolariaRestored`), API publik (`CurrentFogColor`, `CurrentFogEndDistance`, `CurrentSkyTint`, `NotifyVillageRestored`, `SetVillageCenters`), dan penyatuan `_HorizonColor == RenderSettings.fogColor`.

---

## 🎨 1. Arsitektur Shader `DynamicBiomeSkybox.shader` (Unity 6 URP)

Shader `DragonWingAR/DynamicBiomeSkybox` memakai `Tags { "Queue"="Background" "RenderType"="Background" "PreviewType"="Skybox" }` dengan 4 lapisan rendering yang dipadukan di fragmen *sky dome*:

### A. Lapisan 1 — Kubah Langit 3-Stop + Nadir Laut
- **`_ZenithColor`**: Warna puncak langit (`y >= 0.45`).
- **`_MidSkyColor`**: Warna langit tengah (`y ~ 0.16`) agar transisi biru azur $\to$ cyan hangat tidak pucat seperti Unity default.
- **`_HorizonColor`**: Warna tepat di cakrawala (`y = 0.0`) — **WAJIB identik 100%** dengan `RenderSettings.fogColor` agar batas laut jauh ($> 2000\text{ m}$) hilang mulus tanpa garis potong.
- **`_NadirColor`**: Warna refleksi bawah horizon (`y < 0`, `#184E66` senada `Mat_Archipelago_Ocean.mat`).

### B. Lapisan 2 — Matahari *Stylized* & *Atmospheric Sun Glow*
- Mengikuti `_MainLightPosition.xyz` (dengan fallback arah tetap terkalibrasi jika tidak ada *Main Light*).
- **Piringan Matahari (*Sun Disc*):** Lingkaran tajam bertepi lembut (`smoothstep`) dengan warna hangat `_SunColor`.
- **Corona & Horizon Scatter:** Halo radial lembut (`pow(saturate(dot(dir, sunDir)), _SunHaloPower)`) yang memancarkan kehangatan keemasan di sisi matahari, serta diperkuat saat `SolariaCrisis` (terik menyengat).

### C. Lapisan 3 — Awan Kumulus *Stylized* (Ghibli/Genshin)
- Proyeksi kubah planar *horizon-curved* (`uv = dir.xz / (max(dir.y, 0.03) + 0.18)`) sehingga awan tampak menjauh ke perspektif cakrawala (tidak meregang di zenit maupun menumpuk rata).
- Menggunakan *2-octave / 3-octave smooth value noise* + *domain warping* ringan dengan batas tepi semi-cel (`smoothstep(_CloudThreshold - _CloudSoftness, _CloudThreshold + _CloudSoftness, density)`):
  - **Sisi Terang (`_CloudLitColor`):** Putih-krem hangat di sisi atas/menghadap matahari.
  - **Sisi Bayangan (`_CloudShadowColor`):** Biru-lavender / slate lembut di bagian perut bawah awan agar awan bervolume 3D.
  - **Silver Lining:** Kilau tepi terang saat arah pandang mendekati matahari (`pow(dot(dir, sunDir), 6)` di tepi awan tipis).
  - **Fade Horizon:** Awan memudar halus mendekati `y <= 0.03` ke `_HorizonColor` agar tidak bertabrakan dengan kabut laut.

### D. Lapisan 4 — Awan *Cirrus* Tinggi & Aliran Angin
- Lapisan awan tipis berserat di ketinggian atas (`y > 0.18`), bergerak pelan mengikuti `_Time.y * _CloudSpeed`.

---

## 🌈 2. Matriks Palet & Parameter 7 State `BiomeAtmosphereController.cs`

| State | Zenith (`_ZenithColor`) | Mid Sky (`_MidSkyColor`) | Horizon & Fog (`_HorizonColor`) | Cloud Lit / Shadow | Cloud Coverage (`0..1`) | Sun Halo | Fog End (`m`) |
| :--- | :--- | :--- | :--- | :--- | :---: | :---: | :---: |
| **`NeutralOcean`** | `#1C67CC` *(Azur Cerah)* | `#57B3F2` *(Cyan Langit)* | `#C5EBFA` *(Cyan Putih Hangat)* | `#FFFDF5` / `#8DB6DC` | `0.48` | `0.45` | `3800 m` |
| **`ZephyriaCrisis`** | `#4A5C52` *(Kelabu Racun)* | `#6E7F6B` *(Smog Hijau)* | `#8B9884` *(Kabut Polusi)* | `#9EA899` / `#4A5649` | `0.78` | `0.15` | `150 m` |
| **`ZephyriaRestored`** | `#1577C9` *(Biru Toska Segar)* | `#4CD0E2` *(Toska Jernih)* | `#CDF6F5` *(Putih Angin Bersih)* | `#FFFFFF` / `#7AC2D8` | `0.42` | `0.50` | `4000 m` |
| **`PyrewoodCrisis`** | `#3B2320` *(Asap Gelap)* | `#9C4424` *(Oranye Bara)* | `#C76734` *(Kabut Bara)* | `#E88246` / `#2A1A18` | `0.82` | `0.35` | `260 m` |
| **`PyrewoodRestored`** | `#1E6FD0` *(Biru Sejuk)* | `#61BDF2` *(Cyan Segar)* | `#CDEFD9` *(Hijau-Langit Sejuk)* | `#FFFDF8` / `#85B6D2` | `0.45` | `0.48` | `4000 m` |
| **`SolariaCrisis`** | `#9E8C68` *(Terik Berdebu)* | `#C9B283` *(Kuning Pucat)* | `#E5D2A3` *(Kabut Debu Panas)* | `#F2E5C4` / `#BAA47A` | `0.16` | `0.95` | `380 m` |
| **`SolariaRestored`** | `#1D67C8` *(Biru Keemasan)* | `#5DB6EE` *(Langit Cerah)* | `#FCE8BA` *(Cakrawala Emas)* | `#FFF8E4` / `#99B3D6` | `0.40` | `0.65` | `4000 m` |

> **Tambahan `AmbientLight` Trilight Sinkron:** `BiomeAtmosphereController` juga dapat menginterpolasi `RenderSettings.ambientSkyColor`, `ambientEquatorColor`, dan `ambientGroundColor` secara lembut (`RenderSettings.ambientMode = AmbientMode.Trilight`) agar pencahayaan pulau dan tubuh Fanamir menyatu alami dengan perubahan langit.

---

## ☁️ 3. Layer Pelengkap: Awan 3D Parallax Kepulauaan (`PF_StylizedClouds`)

Agar terasa kedalaman 3D saat naga mendaki ke ketinggian `165–260 m` atau menukik:
- Buat generator/prefab ringan `PF_StylizedClouds` berisi `12–16` gugusan awan *stylized* (*smooth low-poly puff spheres* atau *soft billboard clusters*) yang ditempatkan di cincin luar kepulauan:
  - **Radius XZ:** `950 m – 1650 m` dari pusat `(0, 0, 0)` (di luar lintasan desa utama agar tidak menghalangi kamera atau quest marker).
  - **Ketinggian Y:** `300 m – 520 m`.
  - **Material:** `URP/Lit` atau `URP/Unlit` lembut berwarna putih-krem bersih dengan `shadowCastingMode = Off`, `receiveShadows = false`, tanpa collider.
  - Bergerak mengorbit sangat pelan (`0.3°/detik`) atau diam ringan untuk sensasi skala dunia.

---

## 🔧 4. Aturan *Runtime Instance* vs *Edit Mode Asset*

Untuk mencegah aset material di disk berubah-ubah (*dirty*) setiap Play Mode sekaligus membuat skybox langsung tampak indah saat scene dibuka di Unity Editor:
1. Konfigurasikan nilai default `Mat_DynamicBiomeSkybox.mat` menggunakan palet **`NeutralOcean`** (bukan warna abu/hitam kosong).
2. Pasang `Mat_DynamicBiomeSkybox.mat` langsung ke `RenderSettings.skybox` pada scene `MainIsland_Gameplay.unity` (dan opsional `Test_Dragon_Flight.unity`).
3. Di `BiomeAtmosphereController.InitRuntimeSkybox()`, buat instance material runtime (`new Material(skyboxTemplateMaterial)` atau `Instantiate(...)`) saat Play Mode dan bersihkan di `OnDestroy()`, sehingga perubahan lerp warna selama gameplay tidak mengotori file `.mat` di repository.

---
*Navigasi Vault:* [[00 - Overview]] | [[Tasks]] | [[Devlog]] | [[visual-vfx/planning_vfx_fov_skybox_dan_animasi]] | [[handoff-laptop-b/prompt_eksekusi_revisi_skybox_laptop_b]]
