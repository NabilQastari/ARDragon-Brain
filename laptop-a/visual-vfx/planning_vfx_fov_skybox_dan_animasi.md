---
title: Perencanaan VFX Effekseer, Dynamic FOV, Skybox Fantasi, & Blueprint Animasi Naga
tags:
  - vfx
  - camera-fov
  - skybox
  - animation
  - laptop-a
  - planning
owner: Laptop A
updated: 2026-11-19
status: active
---

# 🌌 Perencanaan VFX Effekseer, Dynamic FOV, Skybox Fantasi, & Blueprint Animasi Naga

Dokumen ini memetakan rencana pendalaman visual, atmosfer penerbangan, dan ekspresi gerak naga Fanamir pasca implementasi dasar Sesi 4. 

---

## 1. Dynamic Camera FOV & Speed Sense (Sensasi Kecepatan Terbang)

### A. Masalah & Solusi Desain
Saat naga terbang dalam skala kepulauan $2400 \times 2400\text{ m}$, kecepatan tinggi ($20\text{–}30\text{ m/s}$) bisa terasa "lambat" jika kamera hanya diam di sudut pandang statis. Solusinya adalah **Dynamic Field of View (FOV) & Camera Pull-Back**.

### B. Matriks Parameter Dinamis Kamera
| Manuver / Kecepatan | Rentang Kecepatan | Target FOV Kamera | Jarak Kamera (*Offset Z*) | Efek Tambahan |
| :--- | :--- | :--- | :--- | :--- |
| **Grounded / Idle** | $0\text{ m/s}$ | **`60°`** (Standar) | Normal (`-6.0 m`) | Kamera stabil |
| **Cruising Glide** (`#08`) | $14\text{–}20\text{ m/s}$ | **`64°–68°`** | Mundur halus (`-6.3 m`) | Ribbon tipis di ujung sayap (*Wingtip Trail*) |
| **Tuck to Dive** (`#13`) | $26\text{–}28\text{ m/s}$ | **`76°–80°`** | Mundur jauh (`-7.2 m`) | Kerucut distorsi angin (*Dive Wind Cone*) + getaran kamera mikro (*subtle camera rumble*) |
| **Propeller Dash** (`#14`) | $30\text{ m/s}$ | **`82°–85°`** (*Surge*) | Mundur impulsif (`-7.5 m`) | Cincin sonik uap (*Sonic Shockwave Ring*) + transisi balik mulus |
| **Auto-Hover** (Desa) | $0\text{ m/s}$ | **`60°`** | Normal (`-6.0 m`) | Fixed Flight Cam (bebas disorientasi) |

* **Formula Penghalusan (*Damping*):** Perubahan FOV wajib menggunakan `Mathf.SmoothDamp` dengan `smoothTime ~ 0.25–0.35s` agar transisi terasa organik dan tidak menimbulkan motion sickness pada pemain.

---

## 2. Skybox Fantasi Dinamis & Penyatuan Atmosfer 3 Biome (Atmospheric Blending)

### A. Konsep Visual: *Dynamic Biome-Adaptive Fantasy Atmosphere*
Sistem atmosfer game Fanamir dirancang secara **dinamis** melalui controller `BiomeAtmosphereController.cs`. Langit dan kabut beradaptasi secara organik mengikuti lokasi dan status pemulihan naga:

1. **Area Laut Lepas / Kepulauan Netral (Default):**
   - **Skybox:** Gradasi biru azur cerah di zenit puncak langit, melandai ke cyan hangat dan putih-emas lembut di garis cakrawala (*horizon*), dilengkapi awan kumulus stylized semi-kartun di ketinggian $400\text{–}600\text{ m}$.
   - **Penyatuan Warna Kabut (*Horizon Fog Blending*):** Menyelaraskan warna `RenderSettings.fogColor` URP persis dengan warna horizon Skybox sehingga batas kliping laut ($>2000\text{ m}$) menyatu mulus tanpa patahan visual.

2. **Desa Zephyria (Lembah Polusi / *Smog Valley*):**
   - **Saat Krisis (Unvisited / In-Progress):** Langit muram kelabu kekuningan/kehijauan beracun (*toxic haze*), matahari redup temaram, kabut URP tebal kelabu memendekkan jarak pandang ($\sim 150\text{ m}$).
   - **Saat Pulih (100% Restored):** Kabut tersapu dalam $3\text{ detik}$, bertransformasi menjadi **biru toska jernih berkilau angin segar**.

3. **Desa Pyrewood (Hutan Kebakaran / *Wildfire Woods*):**
   - **Saat Krisis (Unvisited / In-Progress):** Langit oranye pekat membara tertutup kepulan asap hitam pekat (*smoke plume*), kabut bara merah-oranye hangat, partikel abu beterbangan.
   - **Saat Pulih (100% Restored):** Bara padam menjadi uap putih sejuk, warna langit berangsur netral menjadi **biru cerah berpadu dengan hijaunya dedaunan pohon yang selamat**.

4. **Desa Solaria (Dataran Tinggi Kekeringan / *Drought Plateau*):**
   - **Saat Krisis (Unvisited / In-Progress):** Terik matahari menyengat ekstrem (*blinding harsh sun*), warna langit pudar kekuningan pucat berdebu tanpa awan, kabut tipis panas bergelombang (*heat shimmer & dust haze*).
   - **Saat Pulih (100% Restored):** Begitu Ritual Embun Suci dipanggil, pilar cahaya menembus awan, langit berubah menjadi **biru keemasan yang menyejukkan (*radiant golden-blue sky*)** diiringi rintik-rintik embun berkilau.

5. **Mekanisme Transisi Halus (Unity URP):**
   - **Transisi Masuk (Radius $\approx 250\text{ m}$):** Menginterpolasi (*lerp*) material langit & parameter kabut secara lembut ($2.5\text{ detik}$).
   - **Transisi Selebrasi (Kemenangan 100%):** Berjalan bersamaan dengan animasi selebrasi auto-hover $2.5\text{ detik}$, menghasilkan efek "langit terbuka / pembersihan dramatis".

---

## 3. Blueprint Animasi Naga Fanamir (Sesi Khusus Terpisah)

> **Catatan Alokasi:** Diskusi desain dilakukan terpadu, namun **eksekusi teknis animasi akan dialokasikan ke sesi tersendiri** agar fokus tidak tumpang-tindih dengan sistem partikel VFX.

### A. Komponen yang Perlu Ditingkatkan dari Prosedural ke Skeletal Rig
Saat ini `DragonWingAnimator.cs` baru mengendalikan **12 tulang sayap**. Untuk membuat Fanamir terasa hidup layaknya makhluk purba agung, kita perlu menganimasikan bagian tubuh lainnya:

1. **Ekor & Tulang Belakang (*Spine & Tail Undulation*):**
   - **Tail Sway:** Ekor meliuk anggun secara dinamis mengikuti arah belok horizontal (*yaw banking*).
   - **Spine Arch:** Punggung melengkung aerodinamis saat menukik tajam (*Tuck Dive*), dan melengkung ke atas saat *Swoop Climb* atau *Fly Up*.
2. **Kepala & Rahang (*Head & Jaw Bone*):**
   - **Jaw Open (Membuka Mulut):** Rotasi tulang rahang bawah (`Bone_Jaw`) terhubung langsung ke telemetri `MOUTH_OPEN` CV saat semburan air purba (*Water Breath*) di Desa Pyrewood.
   - **Head Tilt:** Menunduk dan mendongak sinkron dengan `DragonHeadLookController.cs`.
3. **Kaki & Cakar (*Legs & Claws Aerodynamics*):**
   - **Flight Tuck:** Kaki depan dan belakang melipat rapat ke bawah perut saat meluncur cepat (*streamlined body*).
   - **Hover / Touchdown:** Kaki menjuntai turun secara rileks saat auto-hover atau bersiap mendarat.
4. **Penghalusan Transisi Antar-Pose (*Blend Tree / Cross-Fading*):**
   - Transisi sayap antar-regime (misal dari *Flap* ke *Glide*, dari *Glide* ke *Tuck Dive*) diberi kurva interpolasi sferis (*Slerp*) agar tidak ada gerakan patah (*snapping*).

---

## 4. Katalog Efek Visual Effekseer & Matriks Sinergi

### A. Daftar 7 Efek Visual Utama (Effekseer Unity 6 URP)
| No | Kategori | Nama Efek Visual (`.efkefc`) | Deskripsi Visual di Layar | Pemicu Gameplay |
| :---: | :--- | :--- | :--- | :--- |
| 1 | **Desa Zephyria** | `VFX_Zephyria_WindGust` | Gelombang bilah angin sabit toska transparan yang berhembus kuat ke arah lembah desa. | Setiap kepakan sayap (`#06 Flap`) di dalam zona tantangan Zephyria. |
| 2 | **Danau Zephyria** | `VFX_Lake_WaterSiphon` | Spiral partikel air jernih berkilau yang tersedot naik ke dada naga saat mengisi daya. | Saat naga auto-hover di atas Danau Zephyria (`Sub-Marker`). |
| 3 | **Desa Pyrewood** | `VFX_Pyrewood_WaterBreath` | Semburan jet air deras berkabut dari soket mulut naga yang menghujam titik kebakaran hutan. | Buka mulut / tangkup dada (`#15B Water Breath`) di zona Pyrewood. |
| 4 | **Desa Pyrewood** | `VFX_Pyrewood_ExtinguishSteam` | Letupan uap air putih tebal disertai desisan saat semburan air memadamkan kobaran api pohon. | Kontak semburan air dengan pohon/tanah yang terbakar. |
| 5 | **Desa Solaria** | `VFX_Solaria_CelestialDewRain` | Pilar cahaya suci keemasan dari langit + taburan partikel embun berkilau membasahi tanah gersang. | Pose kedua tangan terangkat ke langit (`#15C Dew Rain`) di Solaria. |
| 6 | **Terbang Naga** | `VFX_Flight_WingtipTrails` | Pita uap tipis bercahaya di ujung kedua sayap naga saat meluncur kencang atau belok tajam. | Kecepatan $\ge 18\text{ m/s}$ atau belok tajam (*banking*). |
| 7 | **Terbang Naga** | `VFX_Flight_SonicDashShockwave` | Cincin gelombang kejut sonik (*shockwave cone ring*) yang meledak di belakang naga. | Manuver melesat kencang *Propeller Dash* (`#14`). |

### B. Matriks Sinergi VFX + Animasi + Kamera
| Fase / Aksi | Animasi Fanamir | Efek Visual Partikel (Effekseer) | Respon Kamera & Atmosfer |
| :--- | :--- | :--- | :--- |
| **Menukik Cepat** (`#13`) | Sayap terlipat rapat, kaki menempel perut, ekor lurus | Kerucut angin distorsi (*Dive Wind Cone*) + jejak partikel uap | FOV melebar hingga `80°`, kamera mundur (`-7.2 m`), desau angin kencang |
| **Melesat Kencang** (`#14`) | Lengan atas melebar, putaran siku propeller | Cincin gelombang kejut uap sonik di ekor | FOV impulsif melonjak ke **`85°`**, kamera mundur (`-7.5 m`) |
| **Semburan Air** (`#15B`) | Kepala mengarah ke hutan, rahang bawah terbuka lebar | Kolom jet air deras berkabut dari soket mulut | Kamera bergetar mikro (*impulse rumble*), api hutan berubah uap air |
| **Ritual Embun** (`#15C`) | Pose sayap melayang stabil, kepala mendongak ke langit | Cincin pilar cahaya langit + partikel hujan kristal emas-biru | Pencahayaan langit sedikit meredup memberi kontras pada rintik embun |
| **Pembersihan Kabut** (`#15A`) | Kepakan sayap kuat dan berulang mendorong angin ke bawah | Hembusan gelombang angin sabit toska | Kabut kelabu di lembah terurai (*fade out*) menjadi udara cerah |

---
*Navigasi Vault:* [[00 - Overview]] | [[Tasks]] | [[Devlog]] | [[mekanisme_tantangan_desa]] | [[rencana_skill_vfx_effekseer_laptop_b]]
