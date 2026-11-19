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

## 2. Skybox Fantasi & Penyatuan Atmosfer (Atmospheric Blending)

### A. Konsep Visual: *Stylized Ghibli / Genshin Fantasy Sky*
Saat ini langit di `MainIsland_Gameplay.unity` masih menggunakan background bawaan URP yang cenderung datar. Kita membutuhkan Skybox bertema fantasi petualangan:
1. **Gradasi Langit:** Biru azur cerah di zenit puncak langit, melandai ke cyan hangat dan putih-emas lembut di garis cakrawala (*horizon*).
2. **Awan Kumulus Bertingkat:** Awan kapas stylized semi-kartun yang bergerak perlahan (*subtle panning/rotation*) di ketinggian $400\text{–}600\text{ m}$.
3. **Matahari & Rim Lighting:** Matahari hangat di arah sudut $45^\circ$ yang menghasilkan *soft rim-light* di punggung naga dan atap rumah desa.
4. **Penyatuan Warna Kabut (Horizon Fog Blending):**
   - Menyelaraskan warna `RenderSettings.fogColor` URP persis dengan warna horizon Skybox.
   - Hal ini menghilangkan garis potong tepi laut pada jarak batas kliping kamera ($4200\text{ m}$), sehingga lautan tampak menyatu tak terbatas dengan langit kabut di kejauhan.

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

## 4. Matriks Sinergi VFX Effekseer + Animasi + Kamera

| Fase / Aksi | Animasi Fanamir | Efek Visual Partikel (Effekseer) | Respon Kamera / Atmosfer |
| :--- | :--- | :--- | :--- |
| **Menukik Cepat** (`#13`) | Sayap terlipat rapat, kaki menempel perut, ekor lurus | Kerucut angin distorsi (*Dive Wind Cone*) + jejak partikel uap | FOV melebar hingga `80°`, kamera menjauh, desau angin kencang |
| **Semburan Air** (`#15B`) | Kepala mengarah ke hutan, rahang bawah terbuka lebar | Kolom jet air deras berkabut (*WaterBreath Stream*) dari soket mulut | Kamera sedikit bergetar (*subtle impulse rumble*), api hutan berubah uap air |
| **Ritual Embun** (`#15C`) | Pose sayap melayang stabil, kepala mendongak ke langit | Cincin pilar cahaya langit (*Beacon*) + partikel hujan kristal emas-biru | Pencahayaan langit sedikit meredup memberi kontras pada rintik embun berkilau |
| **Pembersihan Kabut** (`#15A`) | Kepakan sayap kuat dan berulang mendorong angin ke bawah | Hembusan gelombang angin sabit hijau toska (*Wind Blades*) | Partikel kabut kelabu di lembah terurai (*fade out*) menjadi udara cerah |

---
*Navigasi Vault:* [[00 - Overview]] | [[Tasks]] | [[Devlog]] | [[mekanisme_tantangan_desa]] | [[rencana_skill_vfx_effekseer_laptop_b]]
