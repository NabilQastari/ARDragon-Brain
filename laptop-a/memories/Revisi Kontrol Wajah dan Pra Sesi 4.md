---
title: Revisi Kontrol Wajah, Clean CV Preview, & Checklist Pra-Sesi 4
type: session-memory
tags:
  - session-memory
  - laptop-a
  - face-tracking
  - cv-control
  - pose-guide
  - sesi-4
  - pre-challenge-checklist
date: 2026-11-18
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[planning_revisi_titik_wajah_dan_pov]]"
  - "[[planning_audit_pose_dan_clean_cv_preview]]"
  - "[[checklist_finalisasi_pra_sesi_4]]"
  - "[[laptop B Patch 1 planning]]"
  - "[[mekanisme_tantangan_desa]]"
  - "[[rencana_skill_vfx_effekseer_laptop_b]]"
---

# 🧠 Memori Sesi — Revisi Kontrol Wajah, Clean CV Preview, & Checklist Pra-Sesi 4

## 1. Tujuan & Konteks Sesi
- **Proyek:** *Fanamir: The Sky Guardian* (namespace C# internal `DragonWingAR.*`), fase transisi dari finalisasi kontrol Computer Vision menuju **Sesi 4 (Mekanisme Tantangan Desa & `VillageZoneController.cs`)**.
- **Fokus Utama Sesi (Laptop A):**
  1. Merevisi spesifikasi pelacakan titik wajah agar lebih ergonomis dan terpisah tegas dari kemudi belok horizontal.
  2. Menerbitkan dokumen panduan pose visual resmi (`Panduan_15_Pose_Fanamir.docx` & `.pdf`, 13 halaman) untuk kebutuhan audit pose.
  3. Merumuskan pembersihan visual preview OpenCV/PIP (tanpa kotak teks telemetri, mempertahankan **hanya angka FPS**) serta protokol audit pose berbasis tangkapan layar.
  4. Mengaudit seluruh prasyarat teknis yang wajib diselesaikan sebelum memulai **Sesi 4** (`checklist_finalisasi_pra_sesi_4.md` — 5 Pilar) dan menyelaraskannya dengan progres aktual Laptop B.

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Spesifikasi 3 Titik Wajah & Sumbu Kontrol Vertikal (`planning_revisi_titik_wajah_dan_pov.md`):**
   - Pelacakan wajah ditetapkan ke **3 titik inti**: **Hidung (`Nose`)**, **Dagu (`Chin`)**, dan **Mulut (`Mouth`)**.
   - **Hidung & Dagu $\rightarrow$ `HEAD_PITCH` (`[-1.0 .. +1.0]`):** Mengendalikan pandangan kepala/POV naga sekaligus sumbu vertikal terbang (*Fly Up* saat dagu diangkat; *Pitch Down* saat dagu ditundukkan; *#03 Grounded Look Up* saat di darat).
   - **Pembebasan Dada (*No Chest Elevation*):** Mengangkat dada/membusungkan dada dihapus sepenuhnya dari syarat mendongak maupun *Fly Up*.
   - **Tegasan Mutlak (`Kepala != Belok Horizontal`):** Gerakan kepala hanya mengatur pitch vertikal, **sama sekali tidak membelokkan arah terbang** ke kiri/kanan. Kemudi horizontal 100% dikendalikan oleh kemiringan badan & perbedaan lengan (*Lean & Arm Tilt*).
   - **Mulut (`Mouth`):** Mengukur rasio bukaan mulut (`MOUTH_OPEN`) untuk memicu semburan air purba (*Water Breath*) di Desa Pyrewood.
2. **Pembersihan Teks Preview CV (`planning_audit_pose_dan_clean_cv_preview.md`):**
   - Menghapus seluruh kotak hitam HUD telemetri, angka float (`HEAD_PITCH`, `MOUTH_OPEN`), dan label teks status pose pada jendela preview OpenCV maupun feed PIP Unity.
   - **Pengecualian Tunggal:** Angka **FPS** tetap dipertahankan kecil dan bersih di sudut layar agar performa real-time tetap terpantau tanpa menutupi tubuh pemain.
   - Paket UDP port `5052` (44-float CSV) tetap dikirim utuh ke Unity.
3. **5 Pilar Prasyarat Pra-Sesi 4 (`checklist_finalisasi_pra_sesi_4.md`):**
   - **Pilar 1 (Kontrol & CV):** Clean CV preview (hanya FPS) + integrasi Tahap 2 `HEAD_PITCH` ke Unity (`DragonHeadLookController` & `DragonFlightController.cs`).
   - **Pilar 2 (Audit Empiris Pose):** Uji webcam terhadap seluruh pose, screenshot pose bermasalah, dan tuning threshold di Python CV/Unity.
   - **Pilar 3 (Scene Marker & Waypoint):** Penempatan/verifikasi koordinat 3 desa & 3 sub-objektif di `MainIsland_Gameplay.unity` + hookup `WaypointCompass.cs`.
   - **Pilar 4 (Auto-Hover & Escape di Controller Naga):** Implementasi `EnterAutoHover()`, mekanisme darurat `Flap to Escape` (mengepakkan sayap untuk membatalkan auto-hover kapan saja), dan `Auto-Release` saat air danau 100%.
   - **Pilar 5 (Kesiapan VFX Effekseer):** Pemasangan skill & plugin `EffekseerForUnity` (URP Unity 6) serta penyiapan emitter kabut, api hutan, semburan air, dan hujan embun.
4. **Pembatasan Sesi untuk Menjaga Konteks:**
   - Sesi ditutup setelah kontrol & pose selesai disinkronkan di Laptop B, agar pembahasan mendalam mengenai mekanisme tantangan desa (**Sesi 4**) dimulai di sesi chat baru dengan konteks yang bersih dan terfokus.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Dokumen Perencanaan Baru di `brain/laptop-a/`:**
  - `brain/laptop-a/planning_revisi_titik_wajah_dan_pov.md` — Spesifikasi 3 titik wajah (Hidung, Dagu, Mulut) & decoupling POV/Fly Up dari dada dan belok horizontal.
  - `brain/laptop-a/planning_audit_pose_dan_clean_cv_preview.md` — Aturan clean CV preview (sisakan FPS) & protokol audit screenshot.
  - `brain/laptop-a/checklist_finalisasi_pra_sesi_4.md` — Checklist 5 Pilar Pra-Sesi 4 sebagai acuan langsung menuju `VillageZoneController.cs`.
- **Dokumen Manual Pose yang Dihasilkan di Sesi Awal (sebelum pembaruan v3.0 17-pose):**
  - `Panduan_15_Pose_Fanamir.docx` & `Panduan_15_Pose_Fanamir.pdf` (13 halaman di root workspace & `docs/`), yang kemudian pada sesi paralel/berikutnya di tanggal `2026-11-18` diperbarui oleh Laptop A menjadi `Panduan_17_Pose_Fanamir_CV.docx` & `.pdf` (11 halaman, 17 pose vector stickman + skeleton).
- **Status Eksekusi Terkini di Laptop B (Terverifikasi via `git -C brain pull` hingga commit `de3d095` per `2026-11-18`):**
  - ✅ **Pilar 1 & Pilar 2 sudah selesai dieksekusi di Laptop B** (`brain/laptop-b/Devlog.md`):
    - Clean CV preview terverifikasi pada port `5053` (`FPS: 30` di sudut kiri atas, tanpa kotak HUD/label teks).
    - `DragonHeadLookController.cs` (`Neck_01` 25%, `Neck_02` 35%, `Head` 40%, `+28°/-22°`) dan integrasi `HEAD_PITCH` (`tokens[43]`) di `DragonFlightController.cs` selesai & lulus Play Mode Unity (`MainIsland_Gameplay.unity`, `Test_Dragon_Flight.unity`, `MainMenu_Lobby.unity`) dengan `0` errors & `0` warnings.
    - Penyelarasan 17 pose CV (`#03, #06, #07, #09–#12, #14, #15A–C`), peningkatan `#14 Propeller Dash` (`dashBurstImpulse`, `30 m/s`, animasi heliks di `DragonWingAnimator.cs`, FOV surge), UI 17 kartu pose di `SCREEN 01` & `SCREEN 07`, serta **9/9 suite test Python CV lulus 100%**.

---

## 4. Detail Penting yang Harus Diingat
- **Penamaan Resmi 3 Desa & Koordinat Target (`MainIsland_Gameplay.unity`):**
  1. **Biome 1 (Smog Valley) — Desa Zephyria:** `(-562.40, 14.10, 274.30)`. Tantangan: Kepakkan sayap (`#06`/`#15A`) untuk menyapu kabut asap (`Y ~ +25m`).
  2. **Biome 2 (Wildfire Woods) — Desa Pyrewood:** `(739.60, 12.90, 7.80)`. Tantangan 2 langkah: Ambil air di Danau Zephyria (`0->100%`, Auto-Release) lalu buka mulut (`#15B`, `MOUTH_OPEN > 0.35`) menyemburkan air ke hutan terbakar.
  3. **Biome 3 (Drought Plateau) — Desa Solaria:** `(39.40, 14.70, -647.60)`. Tantangan: Terbang ke titik atas tebing (`Y ~ +40m`) dan angkat kedua tangan lurus vertikal (**Double I-Pose `11`**, `#15C`) untuk memanggil hujan embun (*Dew Summoning*).
- **Parameter Penting dari Update Terbaru Laptop B:**
  - `MOUTH_OPEN` hysteresis terbaru di Python CV & Unity: `ON = 0.35`, `OFF = 0.25`.
  - `#07 Fly Up` (Open Y-Pose / dongak dagu) terpisah tegas dari `#15C Solaria Dew Rain` (Double I-Pose `11`, `wristSpanRatio <= 1.40f`).
  - Identitas publik game adalah **Fanamir: The Sky Guardian**; namespace internal tetap `DragonWingAR.*`.

---

## 5. Tugas Tersisa & Langkah Selanjutnya (Fokus Sesi Berikutnya)
Karena **Pilar 1 & Pilar 2 telah selesai di Laptop B**, fokus pada **sesi chat berikutnya** langsung beralih ke **Diskusi Mendalam Tantangan Desa & Eksekusi Pilar 3–5 menuju Sesi 4**:
1. **Diskusi & Pendalaman Desain Tantangan Desa (Laptop A):**
   - Meninjau ulang dan memperdalam detail mekanik di `brain/laptop-a/mekanisme_tantangan_desa.md` (radius tepat tiap titik narasi & tantangan, durasi/kecepatan pengisian bar progres tiap desa, aturan `Auto-Hover` vs `Flap to Escape`, dialog Elder Rowan / Ranger Keith / Elder Mira, alur quest berjenjang, dan transisi antar-stage / *Free Play Mode*).
   - Mematangkan spesifikasi teknis `VillageZoneController.cs`, `Session4_VillageBuilder.cs`, dan integrasinya dengan UI *dormant* (`PF_QuestNarrativeBox`, `PF_VillageChallengeOverlay`, `QuestObjectivePanel`, `WaypointCompass`, `GlobalVitalityBar`) serta **Pilar 5 (Effekseer VFX)**.
2. **Menyusun Prompt Sesi 4 untuk Laptop B (Setelah Diskusi Matang):**
   - **Pilar 3:** Setup GameObject marker 3 desa + 3 sub-marker + hookup `WaypointCompass.cs`.
   - **Pilar 4:** Implementasi `EnterAutoHover`, `Flap to Escape`, & `Auto-Release` di `DragonFlightController.cs`.
   - **Pilar 5 & Sesi 4:** Setup plugin `EffekseerForUnity` + build `VillageZoneController.cs`, `Session4_VillageBuilder.cs`, VFX 3 desa, dan *Feature-to-UI Sync*.

---
*Navigasi Vault:* [[00 - Overview]] | [[Tasks]] | [[Devlog]] | [[checklist_finalisasi_pra_sesi_4]] | [[mekanisme_tantangan_desa]]
