---
title: "Master Prompt Eksekusi Terpadu: Animasi Final, Mekanik Gameplay, & Dual Input Mode di Laptop B"
type: prompt
tags:
  - prompt
  - handoff
  - animation
  - gameplay
  - dual-input-mode
  - laptop-b
owner: Laptop A
updated: 2026-11-25
status: ready-to-run
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[visual-vfx/spesifikasi_dynamic_wings_dan_head_tracking]]"
  - "[[gameplay-mechanics/spesifikasi_gameplay_boundary_dan_dual_input_mode]]"
---

# 🤖 Master Prompt Eksekusi Terpadu: Animasi Final, Mekanik Gameplay, & Dual Input Mode
## Fanamir: The Sky Guardian (Laptop B Execution)

Berikut adalah master prompt lengkap siap salin untuk AI Agent di **Laptop B**:

```markdown
# 🐉 MASTER PROMPT LAPTOP B: IMPLEMENTASI FINAL ANIMASI, GAMEPLAY, & DUAL INPUT MODE
## Fanamir: The Sky Guardian (Unity 6 `6000.3.7f1` URP + Python CV)

Halo Agent Laptop B! Mohon eksekusi pembaruan komprehensif untuk animasi naga, aturan gameplay kepulauan, dan sistem kontrol dual mode pada proyek *Fanamir: The Sky Guardian* sesuai dokumen arsitektur resmi [[laptop-a/visual-vfx/spesifikasi_dynamic_wings_dan_head_tracking]] dan [[laptop-a/gameplay-mechanics/spesifikasi_gameplay_boundary_dan_dual_input_mode]].

Eksekusi dibagi menjadi 6 langkah modular berikut:

---

### 📌 LANGKAH 1: Dual-Track Dynamic Wings & Wing Overdrive Steer (`DragonWingAnimator.cs` & `DragonFlightController.cs`)

1. **Dual-Track System pada `DragonWingAnimator.cs` (12 Tulang Sayap):**
   - **Jalur A (Dynamic Free Tracking 1:1):**
     - Aktif saat pose: `Glide (#08)`, `FlyUp (#07)`, `Flap (#06)`, dan `Hover (#09A/#09B)`.
     - Hitung elevasi pergelangan tangan kiri & kanan secara mandiri terhadap bahu:
       - `deltaYL = leftWrist.y - leftShoulder.y;`
       - `deltaYR = rightWrist.y - rightShoulder.y;`
     - Petakan secara asimetris ke sudut elevasi sayap:
       - `shoulderAngleL = Mathf.Lerp(-30f, 40f, Mathf.InverseLerp(-0.30f, 0.45f, deltaYL));`
       - `shoulderAngleR = Mathf.Lerp(-30f, 40f, Mathf.InverseLerp(-0.30f, 0.45f, deltaYR));`
     - Petakan bukaan rentang tangan (`ArmOpenness` $0.0 \dots 1.0$) ke kembang-kuncup membran 4 pasang jari sayap (`Wing_Finger_Outer..Inner`).
   - **Jalur B (Preset Locked Poses):**
     - Aktif saat pose: `TuckDive (#13)`, `PropellerDash (#14)`, `GentleDescent (#10)`, `FlyDownW (#12)`, `GroundedIdle (#01)`, dan `Village Actions (#15A..C)`.
     - Mengunci ke pose preset yang stabil, estetik, dan bebas getaran noise webcam.
   - **Transisi Antar Jalur:** Gunakan `Mathf.SmoothStep` dengan durasi `0.20s` untuk mencegah sayap meloncat kaget (*zero snapping*).

2. **Kemudi Belok Sayap (*Wing Overdrive Steer*) di `DragonFlightController.cs`:**
   - Kemudi utama tetap dipegang kemiringan bahu (*Shoulder Lean*).
   - Hitung selisih sayap: `wingDiff = deltaYL - deltaYR;`
   - Pasang *Deadzone*: Jika `Mathf.Abs(wingDiff) <= 0.15f`, kemudi sayap $= 0$ (tangan bebas berekspresi santai tanpa membuat naga melenceng).
   - Jika `Mathf.Abs(wingDiff) > 0.15f` (sayap dibuat miring tajam $> 18^\circ$):
     - `wingSteer = Mathf.Sign(wingDiff) * (Mathf.Abs(wingDiff) - 0.15f) * 1.5f;`
     - `totalYaw = Mathf.Clamp(shoulderSteer + wingSteer, -1.0f, 1.0f);`

---

### 📌 LANGKAH 2: Pelacakan Kepala 3-Sumbu, Kamera Melirik, & Kunci Aerodinamis (`mouth_tracker.py`, `active_cv_sender.py`, `DragonHeadLookController.cs`, `DynamicFlightCameraController.cs`)

1. **Python CV (`mouth_tracker.py` & `active_cv_sender.py`):**
   - Ekstraksi **Head Yaw** ($\pm 35^\circ$ dari deviasi `(Nose.x - MidShoulder.x) / ShoulderWidth`) dan **Head Roll** ($\pm 15^\circ$ dari kemiringan sudut mata).
   - Kirimkan telemetri `head_yaw` dan `head_roll` melalui paket UDP port `5052` (token float opsional).
2. **Unity Controller (`DragonHeadLookController.cs`):**
   - Terima `HeadPitch`, `HeadYaw`, dan `HeadRoll`.
   - Terapkan **Hierarchical Neck Blend** (rotasi organik 3 segmen):
     - `Neck_01.localRotation`: $25\%$ rotasi total.
     - `Neck_02.localRotation`: $35\%$ rotasi total.
     - `Head.localRotation`: $40\%$ rotasi total.
     - Gunakan `Mathf.SmoothDamp (0.10f–0.12f)` untuk meredam jitter webcam.
3. **Kunci Aerodinamis Kecepatan Tinggi:**
   - Saat status `TuckDive` atau `PropellerDash` aktif (atau kecepatan $\ge 22\text{ m/s}$), redam `HeadYaw` dan `HeadRoll` ke $0^\circ$ dalam `0.20s` agar kepala mengunci lurus ke depan mengikuti angin.
4. **Kamera Melirik Sinematik (`DynamicFlightCameraController.cs` / `DragonFlightController.cs`):**
   - Kamera utama menambahkan offset pan halus $\sim 10\text{--}12\%$ dari sudut `HeadYaw` naga via `Mathf.SmoothDamp (0.25s)`.

---

### 📌 LANGKAH 3: Siklus Kaki Trotting & Kepakan Badai Propeller Dash (`DragonBodyAnimator.cs` & `DragonWingAnimator.cs`)

1. **Siklus Kaki Jalan Trotting (`DragonBodyAnimator.cs`):**
   - Salin fungsi matematika `computeTrottingLegAngles` dari `dragon_anim_studio/studio.js` ke C#.
   - Saat `isGrounded && forwardSpeed > 0.5f`:
     - Gerakkan kaki diagonal serempak (Front Left + Hind Right vs Front Right + Hind Left) dengan frekuensi $\approx 1.4\text{ Hz}$.
     - Sinkronkan dengan panggilan audio `SFX_Dragon_Footstep_01/02`.
2. **Kepakan Badai Propeller Dash ($3.8\text{ Hz}$ di `DragonWingAnimator.cs`):**
   - Ganti putaran heliks lama dengan kurva kepakan `evaluatePropellerDashStroke` dari `studio.js` (Upstroke 35% melengkung ke belakang, Downstroke 65% akselerasi kubik menghantam bawah, dan Tip Lag pada jari sayap).

---

### 📌 LANGKAH 4: Batas Dunia Laut ($>1100\text{ m}$) & Pantulan Tebing (`DragonFlightController.cs`)

1. **Ocean Wind Boundary Push-Back:**
   - Cek posisi naga: jika jarak horizontal dari pusat kepulauan $\sqrt{x^2 + z^2} > 1100\text{ m}$:
     - Putar haluan naga membelok halus $180^\circ$ kembali ke daratan pulau dalam $2.5\text{s}$ via `Mathf.SmoothDampAngle`.
     - Tampilkan teks peringatan di layar: `"Angin badai laut terlalu kuat! Kembali ke kepulauan."`
2. **Terrain Crash Glancing Bounce:**
   - Pasang `OnCollisionEnter` pada collider naga terhadap layer `Terrain` dan `Rocks`.
   - Vektor pantulan: $\vec{V}_{\text{refl}} = \text{Reflect}(\vec{V}_{\text{in}}, \vec{N}_{\text{surface}})$ dengan elevasi $+15^\circ$.
   - Potong `CurrentMomentum` sebesar **$50\%$** dan picu sentakan kamera mikro ($0.12\text{s}$). Naga langsung melayang aman (*safe glide recovery*) tanpa reload scene.

---

### 📌 LANGKAH 5: Alur 2-Tahap Desa Pyrewood & Layar Time Up Positif (`VillageZoneController.cs`, `GameManager.cs`, `FinishModalUI.cs`)

1. **Alur Misi Pyrewood:**
   - Gerbang danau Zephyria (`SM_Biome1_Water_01`) **TERKUNCI** sampai pemain terbang ke balai Desa Pyrewood untuk menerima misi krisis kebakaran hutan.
   - Setelah diterima, silinder danau terbuka $\to$ auto-hover mengisi air purba ($0\% \to 100\%$ dalam $4\text{ detik}$) $\to$ HUD memunculkan status `"Cadangan Air Purba: SIAP!"`.
   - Naga terbang ke Hutan Pyrewood membara $\to$ semprot api dengan pose Water Breath `#15B` ($0\% \to 100\%$).
   - `QuestObjectivePanel` (`SCREEN 02`) menampilkan 2 sub-tugas berjenjang:
     - `[1] Cadangan Energi Air Purba: [0% → 100%]` *(Di Danau Zephyria)*
     - `[2] Pemadaman Hutan Membara: [0% → 100%]` *(Di Hutan Pyrewood)*
2. **Layar Evaluasi Positif Time Up (`FinishModalUI.cs` & `GameManager.cs`):**
   - Jika waktu habis dan bintang $= 0$, judul modal berubah menjadi: **"WAKTU HABIS! USAHA YANG BAGUS!"**
   - Tampilkan statistik kebugaran fisik pemain dan tombol `[ COBA LAGI (RETRY) ]` serta `[ KEMBALI KE LOBBY (ESC) ]`.

---

### 📌 LANGKAH 6: Dual Input Mode di Settings (`DragonGameSettings.cs`, `SettingsPanelUI.cs`, `InGameFlightHUD.cs`, `MissionsPanelUI.cs`)

1. **Opsi di Layar Settings (`SCREEN 05`):**
   - Tambahkan baris: `Control Input Mode: [ CV (Webcam) | Keyboard ]`.
   - Simpan ke `PlayerPrefs` (`Fanamir_InputControlMode`, `0 = CV`, `1 = Keyboard`).
2. **Logika Mode CV:**
   - Blokade kalibrasi aktif ketat (jika `IsCameraCalibrated == false`, tombol stage `"BELUM KALIBRASI"`, dilarang masuk terbang).
   - Feed PIP webcam dan skeleton tampil normal.
3. **Logika Mode Keyboard:**
   - Blokade kalibrasi **DIBYPASS TOTAL** (pemain bisa langsung klik `PLAY` tanpa webcam).
   - Klaster PIP kamera kanan bawah dan skeleton di-hide bersih dari layar.
   - Proses Python CV di-idle hemat daya tanpa memicu error socket UDP.
   - Badge navbar Lobby berubah menjadi `KEYBOARD MODE`.

---

### 📌 LANGKAH 7: Verifikasi & Pengujian Play Mode

1. **Python Test Suites:** Jalankan `python test_all_17_poses_alignment.py` dan `python test_momentum_and_control_revision.py` (pastikan 100% PASS).
2. **Unity Play Mode (`MainIsland_Gameplay.unity` & `MainMenu_Lobby.unity`):**
   - Pastikan kompilasi bersih (`0` errors, `0` warnings).
   - Uji tolehan kepala (naga & kamera melirik).
   - Uji sayap 1:1 asimetris dan dorongan belok tajam (*Wing Overdrive Steer*).
   - Uji batas laut ($>1100\text{ m}$) dan tabrakan tebing (pantul halus).
   - Uji alur 2 tahap Pyrewood (balai desa $\to$ danau $\to$ hutan).
   - Uji saklar `Mode Keyboard` di Settings (PIP hilang, stage langsung `PLAY`).
3. Perbarui `brain/laptop-b/Tasks.md` & `Devlog.md`, lalu jalankan `git commit & push`.
```
