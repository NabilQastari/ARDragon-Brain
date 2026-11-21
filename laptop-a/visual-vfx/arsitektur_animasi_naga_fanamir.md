---
title: Arsitektur Animasi Tubuh Naga Fanamir (DragonBodyAnimator)
tags:
  - animation
  - procedural-animation
  - dragon-rig
  - visual-vfx
  - laptop-a
owner: Laptop A
updated: 2026-11-22
status: approved
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[planning_vfx_fov_skybox_dan_animasi]]"
  - "[[prompt_eksekusi_animasi_naga_laptop_b]]"
  - "[[cv-control/planning_revisi_titik_wajah_dan_pov]]"
---

# 🐉 Arsitektur Animasi Tubuh Naga Fanamir (`DragonBodyAnimator`)

> **Tujuan Dokumen:** Menjadi spesifikasi teknis resmi untuk merancang dan mengimplementasikan sistem animasi prosedural tubuh naga Fanamir (**`DragonBodyAnimator.cs`**) di Unity 6. Sistem ini menghidupkan 35 tulang tubuh yang sebelumnya diam kaku pada rig 47 tulang `ARM_Dragon`, melengkapi sistem sayap `DragonWingAnimator.cs` dan kepala `DragonHeadLookController.cs`.

---

## 🧭 1. Pembagian Peran Pengontrol Rig Naga Fanamir

Armature naga (`ARM_Dragon`, 47 tulang total) dikendalikan secara modular dan harmonis oleh 3 komponen:

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│                 TRIO PENGONTROL RIG NAGA FANAMIR (UNITY 6)                  │
├──────────────────────────┬──────────────────────────┬───────────────────────┤
│ 1. SAYAP (12 TULANG)     │ 2. LEHER & KEPALA (3 T)  │ 3. TUBUH & EKOR (32 T)│
│ • DragonWingAnimator.cs  │ • HeadLookController.cs  │ • DragonBodyAnimator  │
│ • 12 Tulang Sayap        │ • Neck_01, Neck_02, Head │ • Bone_Jaw (Rahang)   │
│ • Flap, Glide, TuckDive, │ • HEAD_PITCH CV          │ • Spine_01..03 (Punggung)│
│   Dash, Hover, Descent   │ • Menunduk & Mendongak   │ • Tail_01..05 (Ekor)  │
│                          │                          │ • 4 Kaki & Cakar      │
└──────────────────────────┴──────────────────────────┴───────────────────────┘
```

---

## 🐲 2. Tiga Pilar Gerakan Tubuh Fanamir

### Pilar A: Artikulasi Rahang Buka Mulut (`Bone_Jaw`)
1. **Momen & Pemicu:**
   * **Semburan Air Purba (*Water Breath* `#15B`):** Saat naga memadamkan kobaran api di Hutan Pyrewood.
   * **Raungan Kemenangan (*Victory Roar*):** Saat tantangan desa selesai $100\%$ dan naga auto-hover selebrasi $2.5\text{ detik}$.
2. **Formula Pembacaan CV:**
   * Sinyal `MOUTH_OPEN` (rentang $0.0\dots 1.0$) dibaca dari telemetri UDP `5052`.
   * **Sudut Bukaan:** `0°` (tertutup rapat saat `MOUTH_OPEN < 0.25`) hingga `-30°` ke bawah (terbuka lebar saat `MOUTH_OPEN > 0.40`).
   * **Penghalusan:** Menggunakan `Mathf.SmoothDamp` ($0.12\text{ detik}$) untuk mencegah getaran (*jitter*) dari webcam.
   * **Sinkronisasi Partikel:** Emitter `VFX_Pyrewood_WaterBreath` memancar tepat dari soket di antara gigi rahang bawah dan atas.

---

### Pilar B: Liukan Ekor Dinamis & Lengkungan Punggung (*Spine & Tail Sway*)
Sebagai naga air purba (*Ancient Dew Water Dragon*), Fanamir memiliki tubuh panjang yang berenang di angkasa secara luwes:

1. **Liukan Ekor saat Belok (*Yaw Turn Secondary Motion*):**
   * Saat naga berbelok (*steering / roll banking*), ekor meliuk tertinggal lembut (*wave lag*) berlawanan dengan arah sentakan kemudi.
   * **Distribusi Fleksi Berjenjang (Hierarchical Wave Lag):**
     * `Tail_01`: meliuk $\approx 15\%$ dari total sudut belok.
     * `Tail_02`: meliuk $\approx 30\%$.
     * `Tail_03`: meliuk $\approx 55\%$.
     * `Tail_04`: meliuk $\approx 75\%$.
     * `Tail_05` (Ujung Sirip Ekor): meliuk penuh $\approx 100\%$ menciptakan cambukan anggun.
2. **Gelombang Santai saat Meluncur (*Cruising Undulation*):**
   * Saat naga terbang lurus mendatar (*Glide* `#08`), ekor berosilasi secara periodik dengan fungsi sinus lambat:
     $$\text{Angle}(t, i) = A \cdot \sin(2\pi \cdot f \cdot t - i \cdot \phi)$$
     * Frekuensi: $f \approx 0.6\text{ Hz}$ (santai dan megah).
     * Amplitudo: $A \approx 6.0^\circ$.
     * Beda Fase: $\phi \approx 0.45\text{ rad}$ antar-tulang ekor.
3. **Lengkungan Punggung Aerodinamis (*Spine Pitch Arch*):**
   * **Menukik Tajam (*Tuck Dive* `#13`):** `Spine_01..03` melengkung cembung ke bawah ($\sim -6^\circ$ per segmen) membentuk siluet aerodinamis mirip peluru.
   * **Menanjak Tajam (*Fly Up* `#07` / *Steep Climb* `#06B`):** Tulang belakang melenting anggun ke atas ($\sim +5^\circ$ per segmen).

---

### Pilar C: Lipatan Kaki & Cakar Aerodinamis (*Legs & Claws Aerodynamics*)
1. **Mode Meluncur Cepat (*Flight Tucking* — Kecepatan $\ge 12\text{ m/s}$):**
   * **Kaki Depan:** Melipat rapi ke arah belakang menempel di bawah tulang dada.
   * **Kaki Belakang:** Melipat lurus ke belakang sejajar pangkal ekor.
   * *Hasil Visual:* Naga terlihat seperti predator udara aerodinamis berkecepatan tinggi tanpa hambatan udara.
2. **Mode Melayang / Mendarat (*Hover & Descent Drops* — Kecepatan $< 6\text{ m/s}$ / Auto-Hover):**
   * Keempat kaki berangsur-angsur menjuntai turun rileks (*smooth lerp* $0.5\text{ detik}$).
   * Jari-jari cakar sedikit membuka santai, siap menyentuh daratan atau menyeimbangkan gravitasi saat berada di atas desa.
3. **Mode di Tanah (*Grounded Idle / Walk*):**
   * Keempat kaki menapak tanah menopang tubuh Fanamir dengan stabil.

---

## 💻 3. Spesifikasi Kerangka Kode C# (`DragonBodyAnimator.cs`)

```csharp
namespace DragonWingAR.Controllers
{
    [DefaultExecutionOrder(55)] // Berjalan tepat setelah DragonFlightController & DragonWingAnimator
    public class DragonBodyAnimator : MonoBehaviour
    {
        [Header("Bone References")]
        [SerializeField] private Transform boneJaw;
        [SerializeField] private Transform[] spineBones; // Spine_01, Spine_02, Spine_03
        [SerializeField] private Transform[] tailBones;  // Tail_01 .. Tail_05
        [SerializeField] private Transform[] frontLegBones; // Left & Right Front Legs
        [SerializeField] private Transform[] backLegBones;  // Left & Right Back Legs

        [Header("Tuning Parameters")]
        public float maxJawOpenAngle = 30f;
        public float tailSwayMultiplier = 25f;
        public float tailUndulationSpeed = 0.6f;
        public float tailUndulationAmplitude = 6f;
        public float legTuckSpeed = 3.5f;

        private Quaternion[] _initialTailRotations;
        private Quaternion[] _initialLegRotations;
        private float _currentJawAngle;
        private float _jawVelocity;
        private float _legTuckWeight; // 0.0f (Hover Drop) .. 1.0f (Full Flight Tuck)

        // Dipanggil setiap LateUpdate untuk menganimasikan transformasi lokal tulang
        private void LateUpdate()
        {
            UpdateJawAnimation();
            UpdateSpineAndTailAnimation();
            UpdateLegsAnimation();
        }
    }
}
```

---
*Navigasi Vault:* [[00 - Overview]] | [[Tasks]] | [[Devlog]] | [[visual-vfx/planning_vfx_fov_skybox_dan_animasi]] | [[visual-vfx/prompt_eksekusi_animasi_naga_laptop_b]]
