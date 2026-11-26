---
title: Master Prompt Eksekusi Kontrol, Momentum v5.0, Ground Lockout, & Escape Desa (Laptop B)
tags:
  - handoff
  - prompt
  - laptop-b
  - flight-controller
  - momentum
  - steering
  - ground-mode
  - village-escape
owner: Laptop A
updated: 2026-11-26
status: ready-for-execution
---

# 🤖 Master Prompt Eksekusi: Kontrol Halus, Sistem Momentum v5.0, Ground Lockout, & Escape Desa (Laptop B)

> **Petunjuk untuk Pengguna:**  
> Salin seluruh teks di bawah ini dan berikan langsung ke AI Agent di **Laptop B** untuk dieksekusi di Unity 6 & Python CV.

```markdown
Tugas: Implementasikan perbaikan kontrol kemudi halus (deadzone ±0.08 & slider Settings), Toggle ON/OFF Steep Bouncy di Settings, mekanisme Ground Mode (Takeoff wajib minimal 3 kepakan, perbaikan Ground Walk T-pose, & Air Pose Lockout saat grounded), standarisasi Leave/Escape desa (3x flap Pyrewood/Solaria vs 100% Tuck Dive Zephyria), serta pembaharuan profil kecepatan & sistem Momentum v5.0 (speed 16–150 m/s, landing glide-to-walk seamless, glancing bounce penalti 10%) mengacu spesifikasi resmi Laptop A di `brain/laptop-a/gameplay-mechanics/spesifikasi_kecepatan_momentum_v5_dan_kontrol.md`.

---

### 1. PERSIAPAN AWAL
1. Jalankan `git -C brain pull` untuk memastikan catatan terbaru telah tersinkronisasi.
2. Pastikan scene `MainMenu_Lobby.unity` dan `MainIsland_Gameplay.unity` dalam kondisi bersih.

---

### 2. LANGKAH 1 — KEMUDI BELOK HALUS & SLIDER SENSITIVITAS DI SETTINGS
1. **Deadzone & Kurva Kemudi (`DragonFlightController.cs` & `DefaultFlightInputMapper.cs`):**
   - Tetapkan `steeringDeadzone = 0.08f` (clamp input belok `r` ke `0.0f` jika `|r| <= 0.08f`).
   - Terapkan kurva non-linier kuadratik di luar deadzone:
     `float rEff = Mathf.Sign(r) * Mathf.Pow((Mathf.Abs(r) - 0.08f) / (1.0f - 0.08f), 1.35f);`
   - Naikkan ambang batas *Wing Overdrive Steer* dari `0.15f` ke **`0.22f`**.
2. **Settings Persistence & Slider (`DragonGameSettings.cs`, `SettingsPanelUI.cs`, `UISceneAndPrefabBuilder.cs`):**
   - Tambahkan property persisten `SteeringSensitivity` (key: `Fanamir_SteeringSensitivity`, float, rentang `0.5f–2.0f`, default `1.0f`).
   - Tambahkan baris slider `Row_SteeringSensitivity` di `PF_SettingsPanel.prefab` (`BuildSettingsPanelPrefab()`) dengan label `"Steering Sensitivity"` dan nilai display `0.5x – 2.0x`.
   - Hubungkan slider ke `DragonFlightController.cs`: kalikan `rEff` dengan `DragonGameSettings.SteeringSensitivity`.

---

### 3. LANGKAH 2 — TOGGLE STEEP BOUNCY COLLISION DI SETTINGS
1. **Settings Persistence (`DragonGameSettings.cs`):**
   - Tambahkan properti `SteepBounceEnabled` (key: `Fanamir_SteepBounceEnabled`, int `1 = ON, 0 = OFF`, default `1`).
2. **UI Settings Panel (`SettingsPanelUI.cs`, `UISceneAndPrefabBuilder.cs`):**
   - Tambahkan toggle switch / button `Row_SteepBounceToggle` (`"Steep Bounce Collision: [ ON | OFF ]"`).
3. **Logika Tabrakan (`DragonFlightController.cs`):**
   - Di `TryHandleGlancingCollisionSweep()` dan `OnCollisionEnter()`:
     - Jika `hitNormal.y <= bounceMaxNormalY` (tebing curam / dinding):
       - Jika `DragonGameSettings.SteepBounceEnabled == true`: jalankan `ApplyGlancingBounce()` dengan penalti momentum **hanya `-10%`** (bukan -50%).
       - Jika `DragonGameSettings.SteepBounceEnabled == false`: naga tidak memantul tolak elastis, melainkan meluncur geser lurus (*smooth sliding contact*) menyusuri tebing tanpa gaya tolak mendadak atau guncangan kamera.

---

### 4. LANGKAH 3 — GROUND MODE LOCKOUT, TAKEOFF 3 KEPAKAN, & GROUND WALK
1. **Takeoff Minimal 3 Kepakan Sayap (`DragonFlightController.cs`):**
   - Saat `_isGrounded == true`, lepas landas (`_isTakingOff = true`) **HANYA** aktif jika pemain melakukan **minimal 3 kali kepakan sayap bertenaga** (`takeoffFlapCounter >= 3`) dalam jendela waktu `2.5 detik` (`FlapPower >= 0.28f`).
   - Begitu mencapai kepakan ke-3: naga terangkat naik, reset counter ke 0, dan berikan **`+10%` Momentum instan**!
2. **Konsistensi Ground Walk (`DragonFlightController.cs` & `DefaultFlightInputMapper.cs`):**
   - Saat `_isGrounded == true`, mendeteksi T-Pose (`#08 Glide`) atau lengan menjauh dari pinggul (`UpperArmOpenness >= 0.35f` / `is_arms_away == true`) **100% konsisten menggerakkan naga berjalan maju di darat (`Ground Walk`, speed `3.5 m/s`)**.
   - Memiringkan badan saat T-pose di darat membelokkan arah jalan naga (*Walk & Turn*).
   - Merapatkan kedua tangan di samping pinggul (`UpperArmOpenness < 0.25f`) menghentikan naga ke `Grounded Idle` (`0 m/s`).
3. **Air Pose Lockout (`DragonFlightController.cs`):**
   - Saat `_isGrounded == true`, **BLOKIR TOTAL** eksekusi pose udara: `#07 Fly Up`, `#13 Tuck Dive`, `#14 Propeller Dash`, `#09A Hover`, `#10 Gentle Descent`, `#11 Fly Down`, `#12 W-Pose`.
   - Pose-pose tersebut tidak boleh memicu dorongan kecepatan udara di darat. Satu-satunya jalan kembali ke udara adalah via 3x Kepakan Takeoff.
4. **Pendaratan Kasual vs Glide-to-Walk:**
   - Kasual landing (jatuh/stall/menukik ke tanah datar): deselerasi momentum $X\% \to 0\%$ dan speed $X \to 0\text{ m/s}$ dalam `3.0 detik`.
   - **Glide-to-Ground Seamless Walk:** Jika menyentuh tanah datar saat sedang posisi Glide datar (`#08`), naga **LANGSUNG** bertransisi ke `Ground Walk` (`3.5 m/s`) tanpa henti mendadak.

---

### 5. LANGKAH 4 — ATURAN LEAVE / ESCAPE TEXTBOX & EVENT DESA
1. **`VillageZoneController.cs` & `DragonFlightController.cs`:**
   - **Desa Pyrewood & Desa Solaria:** Untuk keluar (*leave/escape*) dari Textbox Narasi atau Auto-Hover Tantangan, pemain **wajib melakukan 3 kali kepakan sayap berturut-turut** (`escapeFlapCounter >= 3` dalam jendela `2.5 detik`). Satu kepakan saja tidak membatalkan auto-hover.
   - **Desa Zephyria:** Kepakan sayap **TIDAK BISA** membatalkan auto-hover (karena kepakan adalah aksi misi membersihkan kabut). Satu-satunya cara escape di Zephyria adalah pose **Tuck Dive (`#13`)** yang ditahan selama $\ge 0.6\text{ detik}$.
   - Saat escape berhasil, pemicu keluar memberikan dorongan dinamis mengikuti sisa bar momentum dan pose aktif pemain saat melesat keluar.

---

### 6. LANGKAH 5 — UPDATE PROFIL KECEPATAN & SISTEM MOMENTUM v5.0
Perbarui parameter pada `DragonFlightController.cs` & `DragonLocomotionConfig`:
1. **Kecepatan Glide (#08):**
   - Momentum $0\%$: `16 m/s`.
   - Momentum $< 30\%$: `25 – 40 m/s` (lerp proporsional).
   - Momentum $\ge 30\%$ s.d. $< 75\%$: `40 m/s`.
   - Momentum $\ge 75\%$: dinamika Propeller Dash Normal (`40 – 100 m/s`).
   - Drag alami meluncur: `-3.0% / detik`.
2. **Propeller Dash (#14):**
   - Normal: `40 – 100 m/s` (akselerasi dari 40 ke 100 dalam `3.0 detik`), charge rate: `+10% / detik` (`+1% / 0.1s`).
   - **BOOST (saat momentum 100% dan tetap dash):** `80 – 150 m/s`!
3. **Tuck to Dive (#13):**
   - Kecepatan: `30 – 100 m/s` (akselerasi ke 100 m/s dalam `2.0 detik`), charge rate: `+13% / detik`.
4. **Flap to Climb (#06):**
   - Charge: `+5% / detik`, soft cap: `50%`, kecepatan: `25 – 40 m/s` dalam `3.0 detik`.
5. **Steep Climb (#06B):**
   - Charge: `+4% / detik`, soft cap: `30%`, kecepatan: `20 – 35 m/s` dalam `4.0 detik` (Vy `+8..12 m/s`).
6. **Y-Dive (#11):**
   - Charge: `+8% / detik`, soft cap: `60%`, kecepatan: `35 – 60 m/s`.
7. **Fly Up / Glide Up Y-Pose (#07):**
   - Konsumsi: `-10% / detik`.
   - Kecepatan: $\ge 80\% \to 40\text{ m/s}$; $\ge 40\% \to 25\text{ m/s}$; $< 40\% \to 20\text{ m/s}$; saat $0\% \to 12\text{ m/s}$ (gentle stall).
8. **Rem & Pengereman:**
   - `#09A Hover Active`: rem `-40% / detik` hingga `0 m/s`.
   - `#09B Slow Glide`: konsumsi `-10% / detik`, kunci jelajah `10 – 14 m/s`.
   - `#10 Gentle Descent`: konsumsi `-15% / detik`, speed `14 – 18 m/s`, Vy `-3.0 m/s`.
   - `#12 Fly Down W-Pose`: konsumsi `-35% / detik`, speed `5 m/s`, Vy `-6.0 m/s`.

---

### 7. LANGKAH 6 — BONUS: PEMBERSIHAN RENDER GRAPH EFFEKSEER
- Pada `Assets/Resources/EffekseerSettings.asset`, pastikan **Enable URP Raster Pass** bernilai `1` (`enableURPRasterPass: 1`) untuk mengeliminasi potensi warning/exception `ArgumentNullException` pada event GUI repaints Unity 6.

---

### 8. LANGKAH 7 — VERIFIKASI PLAY MODE & PENGEMBALIAN SCENE
1. Uji Play Mode di `MainMenu_Lobby.unity`:
   - Verifikasi slider `Steering Sensitivity` dan toggle `Steep Bounce Collision` di Settings Panel.
2. Uji Play Mode di `MainIsland_Gameplay.unity`:
   - Verifikasi terbang lurus stabil dengan deadzone $\pm 0.08$.
   - Verifikasi take off membutuhkan minimal 3 kepakan.
   - Verifikasi mendarat kasual vs glide-to-walk langsung berjalan maju ($3.5\text{ m/s}$).
   - Verifikasi seluruh pose udara terkunci saat grounded.
   - Verifikasi escape desa (3 kepakan di Pyrewood/Solaria vs Tuck Dive di Zephyria).
   - Verifikasi akselerasi Propeller Dash hingga $100\text{ m/s}$ dan boost hingga $150\text{ m/s}$.
3. Kembalikan scene aktif ke `MainMenu_Lobby.unity` (Build Index 0).
4. Buat catatan sesi di `brain/laptop-b/memories/` dan perbarui `Devlog.md` serta `Tasks.md`, lalu lakukan git commit & push di folder `brain/`.
```
