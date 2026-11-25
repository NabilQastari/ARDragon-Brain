---
title: "Master Prompt Eksekusi Model Naga Baru & 20 Pose Animasi Three.js Studio (Laptop B)"
tags:
  - handoff
  - prompt
  - animation
  - dragon-model
  - fbx
  - laptop-b
  - unity
owner: Laptop A
status: ready-to-copy
updated: 2026-11-25
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[visual-vfx/studio_animasi_threejs_fanamir]]"
  - "[[visual-vfx/arsitektur_animasi_naga_fanamir]]"
---

# 🐉 Master Prompt Eksekusi Model Naga Baru & 20 Pose Animasi (`v15` — Laptop B)

> **Untuk Pengguna / AI Agent di Laptop B:**
> Pembaruan model 3D naga (**bebas *weight bleed / stretching*** & ***dorsal stripe* kepala mulus simetris**) beserta cuplikan C# Unity untuk **seluruh 20 Pose & Manuver Resmi (`v15`, termasuk pose baru `#15B-1 Water Siphon`)** telah dikemas ke dalam satu arsip:
> - Paket ZIP: [`assets/Export_DragonModel_Anim20_LaptopB.zip`](file:///e:/Semester%205/Game/AR%20Dragon/assets/Export_DragonModel_Anim20_LaptopB.zip)
> - Folder staging: [`assets/Export_DragonModel_Anim20_LaptopB/`](file:///e:/Semester%205/Game/AR%20Dragon/assets/Export_DragonModel_Anim20_LaptopB/)
>
> Salin seluruh isi blok **"MASTER PROMPT LAPTOP B"** di bawah ini ke sesi chat **Laptop B** setelah memindahkan/mengekstrak file ZIP tersebut ke Laptop B.

---

```markdown
Halo Laptop B! Ini adalah sesi eksekusi untuk mengintegrasikan **Model 3D Naga Fanamir Terbaru (bebas skin-weight stretching & garis dorsal kepala sudah diperbaiki)** sekaligus menyinkronkan seluruh **20 Pose & Manuver Animasi Prosedural Terverifikasi (`v15`)** dari Three.js Animation Studio ke proyek **Unity 6 (`AR Dragon Sky Guardian`)**.

Sesuai aturan kolaborasi `AGENTS.md`:
1. Jalankan `git -C brain pull` terlebih dahulu untuk menarik log & spesifikasi terbaru dari Laptop A.
2. Baca `brain/00 - Overview.md`, `brain/laptop-b/Tasks.md`, `brain/laptop-b/Devlog.md`, dan dokumen handoff `brain/laptop-a/handoff-laptop-b/prompt_eksekusi_model_dan_animasi_20_pose_laptop_b.md`.
3. Ingat aturan anti-collision: Laptop B hanya menulis/mengedit catatan di `brain/laptop-b/` (`brain/laptop-a/` read-only).

---

## 📦 PAKET SUMBER (`Export_DragonModel_Anim20_LaptopB.zip`)
Ekstrak `Export_DragonModel_Anim20_LaptopB.zip` (berisi):
1. `Assets/Assets 3D/Dragon_Model.fbx` — Model rigged 47 tulang (`ARM_Dragon`, 13 `SkinnedMeshRenderer`, orientasi `-Z Forward / Y Up` -> Unity `+Z Forward`, skala `1.0`, `add_leaf_bones=False`, `use_armature_deform_only=False` agar `Root` tetap utuh):
   - Bebas *Automatic Weights Bleed* pada `SM_Dragon_Torso` & `SM_Dragon_Spikes_Details` (mengatasi poligon ketiak/punggung tertarik saat kepakan & *tuck dive*).
   - Perbaikan 3 poligon asimetris pada `SM_Dragon_Head` (`0` pasangan asimetris dari kepala hingga ekor).
2. `UNITY_20_POSES_REFERENCE.cs.txt` — Cuplikan rotasi C# Unity yang sudah diekspor & dikonversi untuk seluruh **20 Pose Resmi + Rest Pose** (`#01..#15C` + `#15B-1 Water Siphon`).
3. `Reference_AnimStudio/` (`studio.js`, `index.html`) — Kode sumber penuh Three.js Studio `v15` (berisi fungsi `computeTrottingLegAngles`, `evaluatePropellerDashStroke`, `applyUniversalHeadNeck`, `applyFlightWing`, dan `updateDragonProceduralAnimation`).
4. `copy_dragon_to_unity.bat` — Utilitas salin cepat `Dragon_Model.fbx`.

---

## 🛠️ LANGKAH EKSEKUSI TEKNIS

### LANGKAH 1: Timpa Model Naga (`Assets/Assets 3D/Dragon_Model.fbx`) & Jaga Material URP
1. Backup `AR Dragon Sky Guardian/Assets/Assets 3D/Dragon_Model.fbx` lama (bila perlu), lalu salin `Assets/Assets 3D/Dragon_Model.fbx` baru menimpa file lama (atau jalankan `copy_dragon_to_unity.bat` dari root workspace).
2. **Jangan menghapus `.meta` file** maupun **material URP eksisting** di Unity agar prefab `/Dragon_Player/Visual_Dragon/Dragon_Model` dan `LiveDragonPreviewController` tetap terhubung utuh (`localPosition = (0, -0.7937, 0.6985)`, `localScale = (1.25, 1.25, 1.25)`), serta pastikan 12 material naga (terutama **`M_Dragon_Chest_Crystal`** yang memiliki emisi biru-cyan) dan socket VFX (`DragonVfxController` / `Bone_Jaw` / `Wing_Finger_Outer.L/R`) tetap terpasang benar setelah reimport!
3. Verifikasi `ModelImporter` pada `Dragon_Model.fbx`: pastikan seluruh **47 tulang** `ARM_Dragon` dan **13 `SkinnedMeshRenderer`** (`SM_Dragon_Head`, `Torso`, `Neck`, `Tail`, `FrontLegs`, `HindLegs`, `WingBones`, `WingMembranes`, `Spikes_Details`, `Eyes`, `Jaw`, `Horns`, `Claws`) terimpor bersih tanpa warning/error dan `updateWhenOffscreen = true` pada renderer di prefab.

---

### LANGKAH 2: Helper Konversi Matematika Studio (`FromStudioEulerXYZ`) — WAJIB!
Three.js Studio menggunakan koordinat **Right-Handed (`+X` Kiri Naga)** dengan urutan Euler **`XYZ`**, sedangkan Unity menggunakan **Left-Handed (`+X` Kanan Naga)** dan `Quaternion.Euler` memakai urutan **`ZXY`**.
Agar seluruh sudut **20 pose** di Three.js Studio terpetakan dengan **presisi identik `0.00e+00`** pada rig `Dragon_Model.fbx` di Unity, tambahkan helper berikut (mis. di kelas utilitas bersama atau di `DragonWingAnimator.cs` & `DragonBodyAnimator.cs`):

```csharp
public static Quaternion FromStudioEulerXYZ(float xDeg, float yDeg, float zDeg)
{
    // Mengonversi rotasi lokal instrinsik XYZ Three.js Studio (+X Kiri, RH)
    // ke rotasi lokal Left-Handed Unity FBX (+X Kanan, LH) secara eksak.
    return Quaternion.AngleAxis(xDeg, Vector3.right)
         * Quaternion.AngleAxis(-yDeg, Vector3.up)
         * Quaternion.AngleAxis(-zDeg, Vector3.forward);
}
```
Seluruh nilai sudut pada `UNITY_20_POSES_REFERENCE.cs.txt` sudah memakai `bone.localRotation = _initBone * FromStudioEulerXYZ(x, y, z)`.

---

### LANGKAH 3: Sinkronisasi Penuh 20 Pose ke Controller Rig Naga (Urutan Eksekusi 50 -> 55 -> 60)
Rujuk langsung detail sudut di `UNITY_20_POSES_REFERENCE.cs.txt` dan `Reference_AnimStudio/studio.js`, sambil **mempertahankan pembagian kendali & arsitektur kontroler yang sudah stabil**:

1. **Pengecualian Posisi/Attitude Terbang (`Visual_Dragon` vs `Root`):**
   - **JANGAN** menimpa ketinggian & belok navigasi `DragonFlightController` dengan `visualRoot.localPosition = (0, 0.52f, 0)` dari cuplikan pratinjau studio!
   - Di gameplay (`MainIsland_Gameplay.unity`, `Test_Dragon_Flight.unity`), gunakan base offset `/Dragon_Player/Visual_Dragon/Dragon_Model` (`y = -0.7937f`), dan hanya tambahkan **osilasi vertikal / surge aditif** (`hoverWave * 0.07f`, `downThrust * 0.07f`, `siphonHeave * 0.035f`, `dash thrust Y +0.08f / Z +0.09f`, `walk strideBob +0.018f`).
   - Pastikan rotasi attitude `Root` (`+28°` Hover, `+20°` Zephyria, `+25°` Solaria, `-10°` Water Siphon, `-45°` TuckDive, dll.) **selaras & tidak ganda** (*double-pitch*) dengan rotasi `Visual_Dragon` dari `DragonFlightController.ApplyAttitudeRotation()`!
   - Pada **`LiveDragonPreviewController.cs`** (layar Kalibrasi UI `RenderTexture`), seluruh 47 tulang termasuk `Root` bisa menerapkan rotasi preset studio secara penuh.

2. **`DragonWingAnimator.cs` (`[DefaultExecutionOrder(50)]` — 12 Tulang Sayap):**
   Sinkronkan seluruh regime sayap agar identik dengan `studio.js` `v15`:
   - `#01 Idle`, `#02 Walk`, `#03 LookUp`: sayap terlipat tinggi di samping tubuh (`22°, ∓25°, ±44°`) dengan napas lembut / `walkBounce` bebas tumpang tindih ekor.
   - `#04 Takeoff`, `#06 Flap Climb`, `#06B Steep Climb`: `applyFlightWing` ber-lag (`shFlap`, `faLag -0.50/-0.55`, `tipLag -0.95/-1.00`) + dukungan modulasi CV per-sisi jika sedang mode terbang bebas (`Dynamic Free Tracking`).
   - `#05 Lean & Turn` & `#08 Cruise Glide`: *flat forward-swept airfoil* `180°` (`Shoulder (4°, ±34°, ∓20°)`, `Forearm (-10°, 0, ∓8°)`); pada `#05 Lean` sayap tetap simetris lurus sementara `Root/Visual_Dragon` yang miring (*banking*).
   - `#07 Fly Up`: *Y-Dihedral* `+43.8°` (`32°, 36°, -18°`).
   - `#09A Hover`: flutter lebar `1.4 Hz` (`6° + shFlap 22°`, `shRy 28°`, `shRz -12°`).
   - `#09B Slow Glide`: bentang kompak tertekuk setengah ke belakang (`10°, ±22°, ±14°` / forearm `-6°, ∓10°, ±24°`).
   - `#10 Low-A Descent`: *anhedral* `-34°` (`-28°` bahu, `-14°` lengan bawah, `shRy 30°`, `shRz -14°`).
   - `#11 Y-Dive`: Y bersiku lebar (`4°, ±30°, ±14°`).
   - `#12 W-Pose Elevator`: *airfoil* datar horizontal `97%` berbentuk huruf **`W`** (`Shoulder (-32°, ±66°, ∓18°)`, `Forearm (62°, ±30°, ∓8°)`, 4 jari membentang miring `-40°/-34°/-26°/-16°`).
   - `#13 Tuck to Dive`: lipatan **`Z-fold` *zero-twist*** rapat menempel badan (`Shoulder (-8.7°, ∓5.6°, ±65.3°)`, `Forearm (126.1°, ∓40.4°, ∓21.2°)`, 4 jari lurus ke belakang `-109.3°/-92.1°/-67.5°/-26.0°`).
   - `#14 Propeller Dash`: **Orbit 3D gaya renang *butterfly* (`1.4 Hz`, kurva `evaluatePropellerDashStroke`)** (`thW = th - 0.22*sin(th)`, bahu berayun vertikal `cos(thW)` + sapuan depan-belakang `sin(thW)` + *feathering* pitch, diikuti *lag* lengan bawah & cambuk jari).
   - **Village Actions (`#15A`–`#15C`):**
     - `#15A Zephyria`: sapuan badai raksasa ke depan-bawah (`shFlap 3° + 45°*sin`, `pitchY 22°->44°`, `sweepZ +8°->-34°`).
     - `#15B Pyrewood Water Breath`: kepakan hover stabil `1.2 Hz` (`20°`).
     - **`#15B-1 Pyrewood Water Siphon` *(BARU)*:** kepakan aktif `1.3 Hz` (`8° + shFlap 24°`, `shRy 28°`, `shRz -14°`) selama fase menyedot air di danau.
     - `#15C Solaria Dew Rain`: **Double I-Pose `"11"`** tegak lurus ke langit (`Shoulder (72°, ±22°, ∓34°)`, `Forearm (12°, 0, ∓10°)` + *shimmer* halus).

3. **`DragonBodyAnimator.cs` (`[DefaultExecutionOrder(55)]` — Badan, Kaki, Ekor, Rahang):**
   - **Walk (`#02`):** Implementasikan *Quadruped Diagonal Trotting Gait* (`computeTrottingLegAngles` `1.25 Hz`, proyeksi sagital paha belakang `0.64, ∓0.26, ∓0.72`, `pelvis` *sway/yaw*, `spine` *counter-yaw*, `strideBob`).
   - **Kaki Terbang & Hover (`Front` & `Hind`):**
     - *Hover / Pyrewood Breath / Takeoff / W-Pose:* Kaki menjuntai santai (*Hover Drop*: Depan `16°/14°/-52°`, Belakang `-9.4°, ±1.9°, ±7.7° / -4° / -18° / -55°` + *sway* halus).
     - *Streamlined Flight (`Glide/Climb/Steep/FlyUp/SlowGlide/YDive/Lean/Dive/Dash`):* Kaki depan terlipat rapi ke dada (`60°/72°/-102°`), kaki belakang lurus aerodinamis sejajar ekor (`-33.3°, ±0.3°, ±32.5° / -14° / -48° / -68°`).
     - *Low-A Descent (`#10`) & **Water Siphon (`#15B-1`)**:* Kaki bersiap mendarat / melipat sedang (`Depan 34°/42°/-58°`, `Belakang -16.8°, ±2.5°, ±14.8° / +10° / -24° / -46°`).
     - **`#15A Zephyria`:** Kaki depan **sedikit terlipat** (`36°/44°/-58°` + *sway*), kaki belakang *Hover* rileks.
     - **`#15C Solaria`:** Kaki depan **terlipat masuk/ke atas mengapit & menyentuh Permata Dada** (`UpperArm (-26°, 0, ±8°)`, `Forearm (84°, 0, ∓20°)`, `Paw (-22°, 0, 0)` + `gemPulse`), kaki belakang *Hover* rileks.
   - **Rahang (`Jaw`) & Ekor (`Tail_01..Tail_05_Fin`):**
     - Pertahankan `MOUTH_OPEN` CV (`0°` s.d. `-30°`) & *Victory Roar* (`-25°`), tambahkan animasi **`sipJaw`** ritmis ringan (`-14° .. -21°`) saat mode **`#15B-1 Water Siphon`** aktif.
     - Pertahankan *cruising undulation* + *yaw tail sway* + kurva ekor ke atas (`+X`) saat Hover / Zephyria / Pyrewood / Solaria / Water Siphon.

4. **`DragonHeadLookController.cs` (`[DefaultExecutionOrder(60)]` — Leher & Kepala 3 Sumbu):**
   - Gunakan `applyUniversalHeadNeck` distribusi `25% / 35% / 40%` beserta proyeksi sumbu Yaw/Roll per tulang (`0.85/0.53`, `0.89/0.45`, `0.05/1.00`) menggunakan `FromStudioEulerXYZ(...)`.
   - Tambahkan penyesuaian khusus mode terkunci:
     - `#13 Tuck Dive`: inline peluru `-45°` (`-44° / -16° / +52°`).
     - `#14 Propeller Dash`: leher lebih menjulur maju (`-26° / -14° / +40°`).
     - `#15A Zephyria`: kepala sedikit menunduk `-14°` dunia.
     - `#15B Pyrewood Water Breath`: leher terjulur lurus ke depan `+26 cm` (`-32° / -16° / +26°`).
     - **`#15B-1 Pyrewood Water Siphon` *(BARU)*:** leher & kepala menunduk jauh ke bawah seperti meminum air (`Neck_01 -56°`, `Neck_02 -24°`, `Head +38°` + `drinkBob`).
     - `#15C Solaria Dew Rain`: mendongak khidmat ke langit `+34°` (`-16° / -8° / +10°` + `skyShimmer`).

5. **Integrasi `#15B-1 Water Siphon` di `VillageZoneController.cs` (Danau Pyrewood):**
   - Pada Sub-Marker 1 Desa Pyrewood (**Danau Zephyria / Lake Water Siphon**), aktifkan state animasi **`WaterSiphon`** selama naga melakukan Auto-Hover penyedotan air hingga tangki `100%` (*Auto-Release*), lalu kembali normal saat keluar dari danau (dan gunakan **`WaterBreath` `#15B`** saat menyembur api di Sub-Marker 2 Hutan Terbakar!).

---

### LANGKAH 4: Verifikasi Penuh & Dokumentasi
1. Jalankan kompilasi Unity (`0` errors, `0` warnings baru).
2. Jalankan test suite eksisting (`DragonAnimationVerificationRunner`, `MomentumFlightVerificationRunner`, `Session4VerificationRunner`, `Session5GameLoopVerificationRunner`) — perbarui `DragonAnimationVerificationRunner` bila perlu untuk menguji juga `Walk`, `W-Pose`, `PropellerDash` 3D orbit, `Zephyria`, **`Pyrewood Water Siphon` (`#15B-1`)**, dan `Solaria` (pose `11` + cakar di permata dada).
3. Verifikasi visual di Game View & Scene View (ambil tangkapan layar ke `docs/figures/raw_screenshots/`) untuk memastikan:
   - Model `Dragon_Model.fbx` baru bebas *weight bleed / stretching* di ketiak/punggung dan garis dorsal kepala mulus.
   - Seluruh transisi 20 pose & aksi 3 desa (`Zephyria`, `Pyrewood Lake Water Siphon`, `Pyrewood Forest Water Breath`, `Solaria`) berjalan mulus tanpa konflik eksekusi antar skrip maupun `NullReferenceException`.
   - Material `M_Dragon_Chest_Crystal` tetap menyala dan 8 socket `VFX_Sockets` tetap valid (`Bone_Jaw`, `Wing_Finger_Outer.L/R`).
4. Kembalikan scene aktif ke `Assets/_Scenes/MainMenu_Lobby.unity` (Build Index `0`), catat hasil ke `brain/laptop-b/Devlog.md` dan `brain/laptop-b/Tasks.md`, lalu `commit` dan `push` sub-repo `brain/`.
```
