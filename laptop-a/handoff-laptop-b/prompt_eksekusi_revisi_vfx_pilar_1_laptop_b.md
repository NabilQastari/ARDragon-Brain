---
title: "Master Prompt Eksekusi Pilar 1: Revisi 5 VFX, 3 VFX Baru, Socket Neck_01/Head/Wingtip, & Efek Desa di Laptop B"
type: prompt
tags:
  - prompt
  - handoff
  - vfx
  - effekseer
  - pilar-1
  - unity6
  - laptop-b
owner: Laptop A
updated: 2026-11-26
status: ready-to-run
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[visual-vfx/spesifikasi_revisi_vfx_pilar_1]]"
---

# ✨ Master Prompt Eksekusi Pilar 1: Revisi & Penambahan VFX di Unity 6
## Fanamir: The Sky Guardian *(Laptop B Execution)*

Berikut adalah master prompt lengkap siap salin untuk AI Agent di **Laptop B** (dikerjakan bersama arsip `Export_VFX_Revisi_LaptopB.zip`):

```markdown
# ✨ MASTER PROMPT LAPTOP B: REVISI VFX PILAR 1 (5 REWORKED + 3 BARU + SOCKET & ZONA DESA)
## Fanamir: The Sky Guardian (Unity 6 `6000.3.7f1` URP + EffekseerForUnity `1.80.8`)

Halo Agent Laptop B! Seluruh perbaikan artistik VFX Pilar 1 telah diuji visual $360^\circ$ bersama pengguna di Three.js Studio (`dragon_anim_studio/` `v24`) dan dikemas ke dalam paket **`Export_VFX_Revisi_LaptopB.zip`** (berisi 8 skrip `.py` kompatibel `vfxkit`, 8 `briefs/*.json`, 10 JSON referensi visual `Threejs_Recipes_Approved/`, dan 14 tekstur PNG referensi).

Mohon ekstrak `Export_VFX_Revisi_LaptopB.zip` dan eksekusi 5 langkah berikut di Unity 6 + `EffekseerCore`:

---

### 📌 LANGKAH 1: Impor Resep Python VFX (`vfx/src/` & `vfx/briefs/`), Lint, & Build Ulang `.efkefc` / `.efk` / `.asset`

1. Ekstrak `Export_VFX_Revisi_LaptopB.zip`:
   - Salin seluruh file di `Effekseer_Python_Sources/src/*.py` ke folder `vfx/src/` (menimpa 5 file lama + menambah 3 file baru).
   - Salin seluruh file di `Effekseer_Python_Sources/briefs/*.json` ke folder `vfx/briefs/`.
   - *(Opsional referensi visual: folder `Threejs_Recipes_Approved/` berisi resep JSON versi Three.js WebGL `v24` yang menjadi acuan tampilan akhir yang telah disetujui pengguna).*
   - Jangan menimpa `vfx/textures/processed/*.png` karena seluruh **14 tekstur `T_*.png` yang dipakai sudah sama persis** (0 tekstur baru dibutuhkan).
   - **Dipertahankan tanpa ubah resep:** `VFX_Pyrewood_WaterBreath.py` & `VFX_Pyrewood_ExtinguishSteam.py`.

2. Validasi & kompilasi ulang dengan CLI `vfx`:
   - Jalankan `vfx lint` dan `vfx brief-validate <ID>` untuk seluruh **10** efek (`5 reworked + 3 baru + 2 eksisting`). Pastikan **0 error**.
   - Jalankan `vfx build` untuk menghasilkan **10 berkas `.efkefc`** (`Assets/VFX/Effekseer/`) & **10 berkas `.efk`**.
   - Jalankan `AssetDatabase.Refresh()` / utilitas pembuat `EffekseerEffectAsset` Unity agar ketiga aset baru juga memiliki `.asset` di `Assets/VFX/Effekseer/`:
     - `Assets/VFX/Effekseer/VFX_Lake_MouthIntake.asset`
     - `Assets/VFX/Effekseer/VFX_Solaria_ChestGemGlow.asset`
     - `Assets/VFX/Effekseer/VFX_Pyrewood_Wildfire.asset`
   - Pastikan `EffekseerSettings.asset` tetap memakai `RendererType = 0 (Unity)`, `MaintainGammaColorInLinearSpace = 1`, dan `enableDepth = 0` (aman dari warning RenderGraph URP).

---

### 📌 LANGKAH 2: Perbaikan Socket & Pita Sayap pada `DragonVfxController.cs`

Perbarui `Assets/_Scripts/Controllers/DragonVfxController.cs`:

1. **Koreksi Panjang Tulang Wingtip & Tambah `TrailRenderer` Pita Halus:**
   - Ubah `wingtipBoneLength` dari `1.95f` menjadi **`1.48f`** (`1.1866 * 1.25` sesuai panjang riil tulang `Wing_Finger_Outer.L/R` di `Dragon_Model.fbx`).
   - Karena user menilai titik/partikel wingtip kurang menyerupai garis pita angin, pasang **sepasang `UnityEngine.TrailRenderer`** pada socket `VFX_WingTip_L` & `VFX_WingTip_R` (atau via field `[SerializeField] private TrailRenderer wingtipLeftTrail, wingtipRightTrail;`) agar menyala paralel bersama `wingtipLeftEmitter`/`wingtipRightEmitter` saat `shouldTrail == true`:
     - `trail.emitting = active;`
     - `trail.time = 0.42f;`, `trail.minVertexDistance = 0.08f;`, `trail.startWidth = 0.16f;`, `trail.endWidth = 0.01f;`
     - Kurva warna alpha gradasi cyan-putih halus `(220, 250, 255, 200)` ke `(110, 225, 255, 0)` memakai shader `Universal Render Pipeline/Particles/Unlit` (Additive) atau `Sprites/Default` bebas bayangan (`shadowCastingMode = Off`).

2. **Koreksi Arah Semburan Air (`waterBreathEmitter`) Ikut Tulang Kepala (`Head`):**
   - Pada `SyncSocketTransforms()` (baris 106–111), sebelumnya `waterBreathEmitter.transform.rotation` mengunci ke `flightController.visualModel.rotation`.
   - Ubah agar mengikuti **arah moncong aktual tulang `Head` (`mouthBoneOverride`)**:
     - Pastikan `waterBreathEmitter` diposisikan di moncong `mouthBoneOverride.TransformPoint(new Vector3(0f, 0.34f, -0.04f))` (atau `mouthBoneOverride.position` di titik moncong) dan diarahkan ke `mouthBoneOverride.TransformDirection(mouthForwardLocalAxis)` dengan `mouthForwardLocalAxis = Vector3.up` (cek visual di Play Mode bahwa semburan keluar lurus dari moncong dan ikut menyapu saat kepala menoleh/menunduk!).

3. **Tambahkan 2 Socket VFX Baru di `DragonVfxController.cs`:**
   - `[SerializeField] private EffekseerEmitter mouthIntakeEmitter;` *(untuk `VFX_Lake_MouthIntake.asset`)*
     - Jangkar: **anak langsung tulang `Head` (`mouthBoneOverride`)** di `localPosition = new Vector3(0f, 0.34f, -0.04f)`, menghadap searah moncong kepala.
     - Method baru: `public void SetWaterIntakeActive(bool active)` (dan panggil otomatis di dalam `SetWaterSiphonActive(bool active)` atau dari `VillageZoneController`, serta matikan di `OnDisable()` & `StopAllActionEffects()`).
   - `[SerializeField] private EffekseerEmitter chestGemEmitter;` *(untuk `VFX_Solaria_ChestGemGlow.asset` — **WARNA BIRU KRISTAL**)*
     - **PENTING (Tulang & Offset Terverifikasi Geometry GLB):** Mesh permata dada `M_Dragon_Chest_Crystal` terbobot dominan pada tulang **`Neck_01`** (`56.6%` weight), BUKAN `Spine_02`!
     - Jangkar: Pasang `VFX_Solaria_ChestGemGlow` **sebagai anak langsung tulang `Neck_01`** dengan `localPosition = new Vector3(0f, 0.1214f, -0.1552f)`, `localRotation = Quaternion.identity`, `localScale = Vector3.one * 0.85f` (jangan pasang di `Spine_02` karena akan melorot ke bawah dada).
     - Method baru: `public void SetChestGemGlowActive(bool active)` (nyalakan saat pose `#15C Solaria` `handsHighUp` aktif, dan tetap menyala halus selama hujan selebrasi Solaria berlangsung; matikan di `OnDisable()` & `StopAllActionEffects()`).

4. **Ubah `sonicDashEmitter` (`VFX_Flight_SonicDashShockwave`) Menjadi Terowongan Angin Looping:**
   - Resep baru `VFX_Flight_SonicDashShockwave` bertipe `loop = true` (terowongan banyak garis angin berdensitas tinggi).
   - Ubah pemicu di `LateUpdate()` `DragonVfxController.cs` dari repeat cooldown one-shot menjadi `SetLoopingEmitter(sonicDashEmitter, isDashing);` dan geser `localPosition` `VFX_SonicDash` di `Visual_Dragon/VFX_Sockets` ke `(0f, 0.25f, 0f)` agar arus garis angin menyelimuti sayap dan tubuh naga dengan pas.

---

### 📌 LANGKAH 3: Pembaruan Efek Desa pada `VillageZoneController.cs`

Perbarui `Assets/_Scripts/Gameplay/VillageZoneController.cs`:

1. **Hutan Kebakaran Aktif di Desa Pyrewood (`VFX_Pyrewood_Wildfire`):**
   - Tambahkan `[SerializeField] private EffekseerEmitter[] pyrewoodWildfireEmitters;` pada header *VFX Effekseer Zona Desa*.
   - Helper baru `UpdatePyrewoodWildfireVisuals()`:
     - Jika Pyrewood diaktifkan di stage (`_villageEnabled[(int)VillageId.Pyrewood]`) dan `PyrewoodState != VillageState.Restored`: pastikan seluruh emitter dalam `pyrewoodWildfireEmitters` **aktif menyala (`isLooping = true`, `Play()`)** sejak stage dimulai!
     - Skalakan ukuran kobaran api tiap titik secara halus mengikuti sisa krisis:
       ```csharp
       float fireScale = Mathf.Lerp(1.0f, 0.0f, Mathf.Clamp01(_pyrewoodFireProgress));
       ```
       (terapkan `emitter.transform.localScale = baseScale * fireScale`, atau simpan skala dasar masing-masing, lalu panggil `StopRoot()` & set `localScale = Vector3.zero` ketika `_pyrewoodFireProgress >= 1f` atau `PyrewoodState == VillageState.Restored`).
     - Saat `ConfigureForStage(...)` dipanggil (restart/retry stage), reset ulang skala wildfire ke ukuran penuh & nyalakan lagi bila Pyrewood aktif!

2. **Perpanjang Durasi Hujan Embun Solaria Pasca-Restorasi Menjadi `12–15 Detik`:**
   - Saat ini pada `CompleteVillageRestoration(VillageId.Solaria)` (baris 751–755), kode hanya memanggil `solariaDewRainEmitter.isLooping = false; solariaDewRainEmitter.Play();` sehingga hujan langsung selesai dalam ~1.75 detik!
   - Tambahkan field `[SerializeField] private float solariaPostRestoreRainDuration = 14.0f;` dan timer privat `_solariaPostRestoreRainTimer`.
   - Saat `CompleteVillageRestoration(VillageId.Solaria)`:
     - Set `_solariaPostRestoreRainTimer = solariaPostRestoreRainDuration;` (`14.0f`).
     - Biarkan `solariaDewRainEmitter.isLooping = true;` dan `Play()`, serta nyalakan `dragonVfx?.SetChestGemGlowActive(true)` + `AudioManager.Instance?.SetSolariaDewChimesLoop(true)`.
   - Di `Update()` (PASTIKAN berjalan bahkan saat `_stageFinished` atau `_pendingVictory` jika masih di scene gameplay, ATAU stop bersih saat `ConfigureForStage`/`OnDisable`!):
     - Hitung mundur `_solariaPostRestoreRainTimer -= dt;` -> bila mencapai `0f`, panggil `solariaDewRainEmitter.isLooping = false; solariaDewRainEmitter.StopRoot();`, `dragonVfx?.SetChestGemGlowActive(false)`, dan `AudioManager.Instance?.SetSolariaDewChimesLoop(false)`.
   - Pada `UpdateSolariaChallenge(dt)`: aktifkan bersamaan `SetSolariaDewRainActive(handsHighUp)` dan `dragonVfx?.SetChestGemGlowActive(handsHighUp)`.
   - Pada `StopAllChallengeVfx()`: HANYA hentikan `solariaDewRainEmitter` & `chestGemEmitter` jika `_solariaPostRestoreRainTimer <= 0f` (jangan sampai `StopAllChallengeVfx()` atau `ExitAutoHover()` mematikan hujan selebrasi 14 detik yang baru saja dimulai!).

---

### 📌 LANGKAH 4: Pembaruan `Session4_VillageBuilder.cs` & Scene `MainIsland_Gameplay.unity`

Perbarui `Assets/_Scripts/EditorTools/Session4_VillageBuilder.cs`:
1. Muat 3 `EffekseerEffectAsset` baru (`VFX_Lake_MouthIntake.asset`, `VFX_Solaria_ChestGemGlow.asset`, `VFX_Pyrewood_Wildfire.asset`).
2. Pasang `VFX_Lake_MouthIntake` pada `headBone` (`Head`) di `localPosition = new Vector3(0f, 0.34f, -0.04f)` dan hubungkan ke `DragonVfxController.mouthIntakeEmitter`.
3. Pasang `VFX_Solaria_ChestGemGlow` pada tulang **`Neck_01`** (`FindDeepChild(dragonPlayer.transform, "Neck_01")`) di `localPosition = new Vector3(0f, 0.1214f, -0.1552f)`, `localScale = Vector3.one * 0.85f` dan hubungkan ke `DragonVfxController.chestGemEmitter`.
4. Pasang `TrailRenderer` pada `VFX_WingTip_L` & `VFX_WingTip_R` serta hubungkan ke `DragonVfxController`, pastikan `wingtipBoneLength = 1.48f`.
5. Geser `VFX_SonicDash` ke `localPosition = new Vector3(0f, 0.25f, 0f)` dan naikkan sedikit skala bila perlu (`1.0f–1.15f`) agar membungkus sayap naga.
6. Di area hutan terbakar Biome 2 (`Village_Pyrewood`), tempatkan **4–6 emitter `VFX_Pyrewood_Wildfire`** pada koordinat gugus pohon gosong (`SubMarker_HutanTerbakar` + offset sekitar `(-14..+14, 0, -14..+14)` ter-raycast ke permukaan terrain), dengan skala `2.2f–3.0f`, `playOnStart = true`, `isLooping = true`, dan hubungkan array-nya ke `VillageZoneController.pyrewoodWildfireEmitters`.
7. Jalankan menu editor `DragonWingAR/Session 4/Build Village Zones and Markers` lalu simpan `MainIsland_Gameplay.unity`.

---

### 📌 LANGKAH 5: Verifikasi Otomatis, Screenshot Uji Visual, & Sync Brain Laptop B

1. Pastikan kompilasi Unity `0` compile errors dan Play Mode `0` console errors/warnings (tidak ada duplikat `VFX_Sockets` atau missing asset).
2. Verifikasi skenario Play Mode untuk seluruh 10 VFX:
   - `Wingtip Trails` (`TrailRenderer` + `1.48f` tepat di ujung sayap tanpa gumpalan asap).
   - `Sonic Dash` (terowongan garis angin padat saat Propeller Dash).
   - `Zephyria Wind Gust` (pita angin & garis udara menyapu tanpa slash sabit).
   - `Pyrewood Wildfire` (api hutan membakar area Biome 2 & mengecil `1.0 -> 0.0` hingga padam total saat disembur air).
   - `Lake Water Siphon + Mouth Intake` (aura uap air halus di danau + aliran air masuk ke mulut `Head`).
   - `Pyrewood Water Breath` (semburan mengikuti arah toleh/tunduk kepala `Head`).
   - `Solaria Celestial Dew Rain + Chest Gem Glow` (aura lebih tipis & bervariasi, sinar **biru** tepat di kristal `Neck_01`, serta hujan selebrasi tetap turun `~14 detik` pasca 100% pulih!).
3. Ambil tangkapan layar verifikasi ke `docs/figures/raw_screenshots/`, perbarui `brain/laptop-b/Tasks.md` dan `Devlog.md`, lalu `git commit & push` folder `brain/`.
```
