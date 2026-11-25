---
title: "Master Prompt: Implementasi Final Animasi Fanamir (Dynamic Wings, Dynamic Head, & Gait Trotting) di Laptop B"
type: prompt
tags:
  - prompt
  - handoff
  - animation
  - dynamic-wings
  - dynamic-head
  - laptop-b
owner: Laptop A
updated: 2026-11-25
status: ready-to-run
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[visual-vfx/spesifikasi_dynamic_wings_dan_head_tracking]]"
  - "[[visual-vfx/planning_perbaikan_animasi_dan_pose]]"
---

# 🤖 Master Prompt: Implementasi Final Animasi Fanamir di Laptop B
## Dynamic Wings (Dual-Track 1:1), Dynamic Head (3-Sumbu), & Gait Trotting

> **Instruksi Penggunaan:**
> Salin seluruh isi master prompt di bawah ini dan berikan langsung ke AI Agent di **Laptop B**.
> Prompt ini berisi panduan implementasi 5 langkah terpadu untuk menyelesaikan seluruh antrean animasi Fanamir yang tersisa di Unity C# dan Python CV.

---

```markdown
# 🐉 MASTER PROMPT LAPTOP B: IMPLEMENTASI FINAL ANIMASI NAGA FANAMIR
## Dual-Track Dynamic Wings, Pelacakan Kepala 3-Sumbu, & Siklus Trotting Kaki

Halo Agent Laptop B! Mohon eksekusi pembaruan arsitektur animasi naga Fanamir (*Fanamir: The Sky Guardian*, Unity 6 `6000.3.7f1` URP + Python CV) sesuai dengan dokumen spesifikasi resmi [[laptop-a/visual-vfx/spesifikasi_dynamic_wings_dan_head_tracking]].

Pembaruan ini mencakup 5 langkah utama:

---

### 📌 LANGKAH 1: Dual-Track Dynamic Wings & Wing Overdrive Steer (`DragonWingAnimator.cs` & `DragonFlightController.cs`)

1. **Implementasikan Sistem Dua Jalur (Dual-Track) pada `DragonWingAnimator.cs`:**
   - **Jalur A (Dynamic Free Tracking 1:1):**
     - Aktif saat pose: `Glide (#08)`, `FlyUp (#07)`, `Flap (#06)`, dan `Hover (#09A/#09B)`.
     - Hitung elevasi pergelangan tangan kiri & kanan mandiri terhadap bahu:
       - `deltaYL = leftWrist.y - leftShoulder.y`
       - `deltaYR = rightWrist.y - rightShoulder.y`
     - Petakan secara asimetris ke sudut rotasi sayap:
       - `shoulderAngleL = Mathf.Lerp(-30f, 40f, Mathf.InverseLerp(-0.30f, 0.45f, deltaYL))`
       - `shoulderAngleR = Mathf.Lerp(-30f, 40f, Mathf.InverseLerp(-0.30f, 0.45f, deltaYR))`
     - Petakan bukaan rentang tangan (`ArmOpenness` $0.0 \dots 1.0$) ke kembang-kuncup membran 4 pasang jari sayap (`Wing_Finger_Outer..Inner`).
   - **Jalur B (Preset Locked Poses):**
     - Aktif saat pose khusus: `TuckDive (#13)`, `PropellerDash (#14)`, `GentleDescent (#10)`, `FlyDownW (#12)`, `GroundedIdle (#01)`, dan `Village Actions (#15A..C)`.
     - Mengunci ke pose preset yang stabil, estetik, dan bebas jitter webcam.
   - **Transisi Antar Jalur:** Gunakan `Mathf.SmoothStep` dengan durasi `0.20s` untuk mencegah sayap meloncat kaget (*snapping*).

2. **Implementasikan Kemudi Belok Sayap (*Wing Overdrive Steer*) di `DragonFlightController.cs`:**
   - Kemudi utama tetap dipegang kemiringan bahu (*Shoulder Lean*).
   - Hitung selisih sayap: `wingDiff = deltaYL - deltaYR`.
   - Pasang *Deadzone*: Jika `Mathf.Abs(wingDiff) <= 0.15f`, kemudi sayap $= 0$ (tangan bebas berekspresi santai).
   - Jika `Mathf.Abs(wingDiff) > 0.15f`:
     - `wingSteer = Mathf.Sign(wingDiff) * (Mathf.Abs(wingDiff) - 0.15f) * 1.5f;`
     - `totalYaw = Mathf.Clamp(shoulderSteer + wingSteer, -1.0f, 1.0f);`

---

### 📌 LANGKAH 2: Pelacakan Wajah 3-Sumbu & Distribusi Leher (`mouth_tracker.py`, `active_cv_sender.py`, `DragonHeadLookController.cs`)

1. **Python CV (`mouth_tracker.py` & `active_cv_sender.py`):**
   - Tambahkan ekstraksi **Head Yaw** ($\pm 35^\circ$ dari deviasi `(Nose.x - MidShoulder.x) / ShoulderWidth`) dan **Head Roll** ($\pm 15^\circ$ dari kemiringan garis mata).
   - Kirimkan telemetri `head_yaw` dan `head_roll` melalui paket UDP port `5052` (ekspansi token float opsional pada posisi yang aman).
2. **Unity Controller (`DragonHeadLookController.cs`):**
   - Terima `HeadPitch`, `HeadYaw`, dan `HeadRoll`.
   - Terapkan **Hierarchical Neck Blend** (rotasi organik 3 segmen):
     - `Neck_01.localRotation`: $25\%$ rotasi total.
     - `Neck_02.localRotation`: $35\%$ rotasi total.
     - `Head.localRotation`: $40\%$ rotasi total.
     - Gunakan `Mathf.SmoothDamp (0.10f–0.12f)` untuk anti-jitter.
3. **Kunci Aerodinamis Kecepatan Tinggi:**
   - Saat `flightController.CurrentAirspeed >= 22f` atau status `TuckDive` / `PropellerDash` aktif, redam `HeadYaw` dan `HeadRoll` ke $0^\circ$ dalam `0.20s` agar kepala mengunci lurus ke depan.

---

### 📌 LANGKAH 3: Kamera Melirik Sinematik (`DynamicFlightCameraController.cs` / `DragonFlightController.cs`)

- Sambungkan rotasi `HeadYaw` ke offset sudut pan kamera utama penerbangan:
  - Kamera menambahkan offset halus $\sim 10\text{--}12\%$ dari sudut `HeadYaw` naga via `Mathf.SmoothDamp` (`smoothTime = 0.25s`).
  - Ketika pemain menoleh mengamati pemandangan, kamera ikut melirik lembut memberikan sensasi sinematik luas.

---

### 📌 LANGKAH 4: Siklus Jalan 4 Kaki & Kepakan Badai Propeller Dash (`DragonBodyAnimator.cs` & `DragonWingAnimator.cs`)

1. **Siklus Kaki Jalan Trotting (`DragonBodyAnimator.cs`):**
   - Salin fungsi matematika `computeTrottingLegAngles` dari `dragon_anim_studio/studio.js` ke C#.
   - Saat `isGrounded && forwardSpeed > 0.5f`:
     - Gerakkan kaki diagonal serempak (Front Left + Hind Right vs Front Right + Hind Left) dengan frekuensi $\approx 1.4\text{ Hz}$.
     - Sinkronkan dengan panggilan audio `SFX_Dragon_Footstep_01/02`.
2. **Kepakan Badai Propeller Dash ($3.8\text{ Hz}$ di `DragonWingAnimator.cs`):**
   - Ganti putaran heliks lama dengan kurva kepakan `evaluatePropellerDashStroke` dari `studio.js` (Upstroke 35% melengkung ke belakang, Downstroke 65% akselerasi kubik menghantam bawah, dan Tip Lag pada jari sayap).

---

### 📌 LANGKAH 5: Verifikasi Play Mode & Non-Regresi

1. Jalankan test suite Python CV:
   - Pastikan seluruh suite lulus 100% (terutama `test_all_17_poses_alignment.py`, `test_momentum_and_control_revision.py`).
2. Uji Play Mode Unity pada scene `MainMenu_Lobby.unity` dan `MainIsland_Gameplay.unity`:
   - Verifikasi bahwa kompilasi bersih (`0` errors, `0` warnings).
   - Verifikasi gerakan tangan asimetris pada mode Glide/Hover.
   - Verifikasi tolehan kepala pemain diikuti oleh kepala naga & kamera melirik halus.
   - Verifikasi jalan di tanah menampilkan langkah kaki 4 trotting.
   - Verifikasi audio BGM & SFX tetap berjalan tanpa desinkronisasi.
3. Ambil screenshot bukti uji jika perlu, perbarui `brain/laptop-b/Tasks.md` & `Devlog.md`, dan lakukan `git commit & push`.
```

---

*Navigasi:*
[[00 - Overview]] | [[Tasks]] | [[Devlog]] | [[visual-vfx/spesifikasi_dynamic_wings_dan_head_tracking]]
