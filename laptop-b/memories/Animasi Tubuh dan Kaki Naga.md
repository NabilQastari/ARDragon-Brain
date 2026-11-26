---
title: Animasi Tubuh dan Kaki Naga
type: session-memory
tags:
  - session-memory
  - laptop-b
  - animation
  - procedural-animation
  - dragon-rig
  - flight-legs
  - hover-posture
date: 2026-11-23
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[laptop-a/visual-vfx/arsitektur_animasi_naga_fanamir]]"
  - "[[laptop-a/visual-vfx/prompt_eksekusi_animasi_naga_laptop_b]]"
  - "[[laptop-a/visual-vfx/planning_perbaikan_animasi_dan_pose]]"
  - "[[laptop-a/visual-vfx/prompt_eksekusi_animasi_tahap_1_laptop_b]]"
  - "[[laptop-a/handoff-laptop-b/panduan_screenshot_mandiri_dan_laporan]]"
---

# 🐉 Animasi Tubuh Prosedural Naga Fanamir & Peningkatan Tahap 1

## 1. Tujuan & Konteks Sesi
Mengimplementasikan sistem animasi prosedural tubuh naga Fanamir (`DragonBodyAnimator.cs`) di atas rig 47-tulang `ARM_Dragon` (Unity 6 `6000.3.7f1` URP) serta menyempurnakan **Tahap 1** anatomi kaki terbang dan sikap tubuh saat *hover*:
1. Menghidupkan rahang (`Jaw` / `Bone_Jaw`), punggung (`Spine_01..02`), ekor (`Tail_01..05_Fin`), serta kaki depan & belakang pada `/Dragon_Player` di `MainIsland_Gameplay.unity` dan `Test_Dragon_Flight.unity` tanpa mengganggu `DragonWingAnimator` (12 tulang sayap) maupun `DragonHeadLookController` (`Neck_01`, `Neck_02`, `Head`).
2. Menyelaraskan postur **Flight Tuck** (kecepatan $\ge 12\text{ m/s}$), **Hover Chest Elevation (`+16°`)**, **Buoyant Hover Bobbing**, dan **Hover Drop** kaki sesuai spesifikasi `[[laptop-a/visual-vfx/prompt_eksekusi_animasi_tahap_1_laptop_b]]`.
3. Memastikan urutan scene editor & build dimulai dari menu (`MainMenu_Lobby.unity` Build Index `0`).

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Orkestrasi Urutan Eksekusi Rig `ARM_Dragon`:**
   - `DragonWingAnimator` (`[DefaultExecutionOrder(50)]`) $\rightarrow$ `DragonBodyAnimator` (`[DefaultExecutionOrder(55)]`) $\rightarrow$ `DragonHeadLookController` (`[DefaultExecutionOrder(60)]`).
   - `DragonBodyAnimator.CreateRig()` mengonversi sumbu kanonik `Visual_Dragon` ke ruang lokal tiap tulang (`AxisPitchUp = inv * (-visualRoot.right)`, `AxisYaw = inv * visualRoot.up`, `AxisRoll = inv * visualRoot.forward`) agar rotasi konsisten lintas orientasi armature (`ARM_Dragon` `270° X`) dan simetris `.L` / `.R` (`sideSign = +1` kiri, `-1` kanan).
2. **Artikulasi Rahang (`Jaw`) & *Victory Roar*:**
   - `MOUTH_OPEN` (`IPoseProvider.MouthOpen`, UDP `5052`): `< 0.25` tertutup rapat (`0°`), `> 0.40` terbuka lebar (`-30°` pitch lokal), dihaluskan dengan `Mathf.SmoothDamp(..., jawSmoothTime = 0.12f)`.
   - `VillageZoneController.CompleteVillageRestoration()` memanggil `PlayVictoryRoar(victoryCelebrateDelay)` (`-25°`, `2.5s`) secara otomatis saat pemulihan desa selesai.
3. **Liukan Ekor Dinamis, *Cruising Undulation*, & *Spine Arch*:**
   - *Hierarchical wave lag* berlawanan arah kemudi (`TailCumulativeWeights = { 0.15f, 0.30f, 0.55f, 0.75f, 1.00f }`, kecepatan lag `10f -> 4f` dari pangkal ke ujung).
   - *Cruising Undulation* sinusoidal (`0.6 Hz`, amplitudo `6°`, beda fase `0.45 rad`) saat meluncur lurus dan meredup saat belok tajam, di tanah, atau hover.
   - *Spine Arch*: `-6°`/segmen saat `#13 Tuck Dive`, `+5°`/segmen saat `#07 Fly Up` / `#06B Steep Climb Flap`.
4. **Anatomi Kaki Terbang (*Flight Tuck*) & Postur Hover (Tahap 1):**
   - **Flight Tuck (`legTuckWeight -> 1.0f`, kecepatan $\ge 12\text{ m/s}$):**
     - Kaki depan (`Shoulder_Front`, `UpperArm_Front`, `Forearm_Front`, `Paw_Front`): `FrontTuckPitchDeg = { -6f, -62f, -32f, -42f }`, `FrontTuckRollInDeg = { 4f, 0f, 0f, 0f }` sehingga melipat rapat ke belakang di bawah rongga dada/perut tanpa menjuntai ke bawah (`pawY = -0.13`, `pawZ <= -0.85`).
     - Kaki belakang (`Thigh_Hind`, `Shin_Hind`, `Metatarsus_Hind`, `Foot_Hind`): `HindTuckPitchDeg = { -88f, 32f, -52f, -65f }`, `HindTuckRollInDeg = { 10f, 0f, 0f, 0f }` sehingga melurus merapat sejajar sepanjang pangkal ekor (`footZ = -0.98`).
   - **Postur Hover (`!grounded && (IsHovering || IsHoverActiveBrake || CurrentSpeed < 6f)` saat tidak dive/fly-up/steep-climb):**
     - Elevasi dada $+8^\circ$ pada `Spine_01` + $+8^\circ$ pada `Spine_02` (total $+16^\circ$) via `Mathf.SmoothDamp` (`0.25s`).
     - *Buoyant Hover Bobbing*: osilasi vertikal `0.40 Hz`, amplitudo `0.18 m` pada `visualRoot.localPosition` (di-reset bersih ke `_baseVisualLocalPos` di `OnDisable()`).
     - *Hover Drop* (`legTuckWeight -> 0.0f`, transisi `0.5s` dengan hysteresis `6..12 m/s`): `FrontDropPitchDeg = { 0f, -25f, 8f, -20f }` dan `HindDropPitchDeg = { -35f, 12f, -16f, -38f }`.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Skrip Baru & Modifikasi (`AR Dragon Sky Guardian/`):**
  - `Assets/_Scripts/Controllers/DragonBodyAnimator.cs` *(Baru, lalu diperbarui pada Tahap 1)*.
  - `Assets/_Scripts/Gameplay/VillageZoneController.cs` (pemicu `PlayVictoryRoar` saat desa selesai pulih).
  - `Assets/Editor/Session4_VillageBuilder.cs` (guard penambahan komponen `DragonBodyAnimator` pada `/Dragon_Player`).
  - `Assets/Editor/DragonAnimationVerificationRunner.cs` *(Baru, lalu diperbarui untuk assertion & screenshot Tahap 1)*.
- **Scene Terverifikasi:**
  - `/Dragon_Player` terpasang `DragonBodyAnimator` di `Assets/_Scenes/MainIsland_Gameplay.unity` dan `Assets/_Scenes/Test_Dragon_Flight.unity`; scene aktif editor dikembalikan ke `Assets/_Scenes/MainMenu_Lobby.unity` (Build Index `0`, diikuti `MainIsland_Gameplay` Index `1` dan `Test_Dragon_Flight` Index `2`).
- **Hasil Verifikasi Play Mode (`MainIsland_Gameplay.unity`):**
  - `DragonAnimationVerificationRunner.RunPlayModeVerificationAsync()`: **`ALL DRAGON BODY ANIMATION CHECKS PASSED (100%)`** (`0` console errors, `0` console warnings):
    - `Jaw(closed=0.0, partial=-14.0, open=-30.0, roar=-25.0)`
    - `Tail(right tip=20.0/root=3.0, left tip=-20.0, undulation=48.5)`
    - `Spine(dive=-6.0, flyUp=5.0, hoverChest=+16.0°, bobSpan=0.36m)`
    - `Legs(dive spd=20.8 tuck=1.00 thigh=88° footZ=-0.98 pawY=-0.13, hover spd=0.0 tuck=0.00 thigh=35° transition=0.43s)`
    - `WingRegimes=[GroundedIdle,GentleDescent,Glide,TuckDive,FlyUp,Hover]`
  - Non-regresi `MomentumFlightVerificationRunner.RunPlayModeVerificationAsync()`: **`ALL MOMENTUM & FLIGHT PHYSICS CHECKS PASSED (100%)`** (`0` console errors, `0` console warnings).
- **Tangkapan Layar Mandiri (`docs/figures/raw_screenshots/`):**
  - `SS_Animasi_Dragon_JawOpen.png`, `SS_Animasi_Dragon_TailSway.png`
  - `SS_Animasi_Tahap1_FlightLegs.png`, `SS_Animasi_Tahap1_HoverChest.png`

---

## 4. Detail Penting yang Harus Diingat
- **Penamaan Tulang Aktual `ARM_Dragon`:** Rahang pada FBX bernama `Jaw` (skrip mendukung fallback `"Bone_Jaw", "Jaw"`), ekor ujung bernama `Tail_05_Fin`, punggung memiliki 2 tulang (`Spine_01`, `Spine_02`), kaki depan 4 segmen (`Shoulder_Front`, `UpperArm_Front`, `Forearm_Front`, `Paw_Front` `.L/.R`), kaki belakang 4 segmen (`Thigh_Hind`, `Shin_Hind`, `Metatarsus_Hind`, `Foot_Hind` `.L/.R`).
- **Tutup Lipatan Kaki Depan & Bobbing:** Nilai `FrontTuckPitchDeg` (`{ -6f, -62f, -32f, -42f }`) dipilih setelah inspeksi visual screenshot karena melipat `Forearm_Front` ke depan (`+100°`) membuat siku/lengan bawah menggantung di bawah dada; melipat ke belakang dengan roll-in `4°` membuat profil perut rata aerodinamis. `visualRoot.localPosition` hanya dimodifikasi saat `visualRoot != transform` dan dikembalikan di `OnDisable()`.
- **Unity Auto-Refresh saat Unfocused:** Setelah edit file C# dari luar Editor, selalu pastikan `AssetDatabase.Refresh()` + `CompilationPipeline.RequestScriptCompilation()` (atau domain reload `codeLoadCount` bertambah) sebelum menjalankan test Play Mode.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- Melanjutkan roadmap peningkatan animasi & pose Naga Fanamir tahap berikutnya sesuai [[laptop-a/visual-vfx/planning_perbaikan_animasi_dan_pose]] (Tahap 2–4: siklus jalan quadruped di tanah, kepala dinamis 3 sumbu CV, dual-track sayap 1:1 vs preset terkunci, serta overhaul kepakan bertenaga keras `#14 Propeller Dash`) dan/atau Sesi 5 Lanjutan (Build Windows Standalone + `start_game.bat`).

---
*Navigasi Vault:* [[00 - Overview]] | [[Tasks]] | [[Devlog]] | [[laptop-a/visual-vfx/arsitektur_animasi_naga_fanamir]] | [[laptop-a/visual-vfx/planning_perbaikan_animasi_dan_pose]]
