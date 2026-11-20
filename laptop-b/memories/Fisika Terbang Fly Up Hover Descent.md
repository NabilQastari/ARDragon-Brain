---
title: "Fisika Terbang Fly Up, Hover Mode, dan Gentle Descent di Unity"
type: session-memory
tags:
  - session-memory
  - laptop-b
  - flight-physics
  - unity
  - computer-vision
date: 2026-11-21
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
---

# Fisika Terbang Fly Up, Hover Mode, dan Gentle Descent di Unity

## 1. Tujuan & Konteks Sesi
Sesi ini melanjutkan tahap **Phase B (Character Flight Physics, Unity Integration & HUD Polish)** setelah Phase A (Computer Vision & Gesture Recognition) selesai dan disetujui. Fokus utama adalah menghubungkan tiga kontrol terbang tambahan—**Fly Up**, **Hover Mode**, dan **Gentle Descent**—dari pipeline UDP Python (`39` token CSV) ke arsitektur input modular Unity (`IPoseProvider` → `IFlightInputProvider` → `DragonFlightController` → `InGameFlightHUD`), sekaligus memverifikasi kompilasi Unity 6 dan pengujian *end-to-end* tanpa menyentuh *wing rigging* maupun animasi tulang sayap.

---

## 2. Keputusan Desain / Arsitektur / Teknis
- **Strict Priority Arbitration di `DragonFlightController.HandleSkyPeckFlightPhysics()`:**
  Urutan prioritas dikunci secara eksklusif agar gestur tidak saling menimpa:
  1. **Propeller Dash** (`_isPropellerDash`)
  2. **Tuck to Dive / Normal Dive** (`_isTuckDiving`)
  3. **In-Flight Hover Mode** (`_isInFlightHover`): menjaga sikap horizontal (`targetPitch = 0f`), mendeselerasi kecepatan maju ke `0 m/s`, mempertahankan ketinggian dengan *bobbing* alami saat kepakan lengan bawah aktif (`HoverPower > 0.05f`), atau turun perlahan (`-1.6 m/s`) jika kepakan berhenti.
  4. **Gentle Descent / Tucked T-Pose** (`_isGentleDescent`): meluncur maju pada kecepatan jelajah ringan (`16 m/s`) dengan laju turun lambat (`-1.8 m/s`) dan pitch ringan (`-8°`), jauh lebih lembut dibanding normal dive (`-40°`, hingga `28 m/s`).
  5. **Fly Up** (`_isFlyUp`): mendongak ke atas (`16°..24°` sesuai `FlapPower`), memberi daya angkat vertikal tinggi (`flyUpLiftPower = 11.0f`) dan akselerasi maju (`flyUpThrustAccel = 14.0f`).
  6. **Normal Flap to Climb** (`_isFlapping`)
  7. **Glide & Swoop Climb** (default jelajah / konversi kecepatan sisa pasca-dive).
- **Pemisahan Auto-Hover Desa vs In-Flight Hover:**
  `_isHovering` (`SetHoverState()` untuk area desa) dipertahankan terpisah dari `_isInFlightHover` agar perilaku desa tetap kompatibel dan pemain tetap memegang kendali belok saat hover di udara.
- **Keyboard Fallback Uji Cepat (`UDPPoseReceiver.cs`):**
  Menambahkan tombol `H` (Hover), `G` (Gentle Descent), dan `U` (Fly Up) pada cabang *New Input System* (`#if ENABLE_INPUT_SYSTEM`) maupun *Legacy Input Manager*.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- [`AR Dragon Sky Guardian/Assets/_Scripts/CV_Receiver/UDPPoseReceiver.cs`](file:///d:/Fauzih/Kuliah/SEMESTER%20V/Proyek%20Multimedia%20dan%20Game/AR%20Dragon/AR%20Dragon%20Sky%20Guardian/Assets/_Scripts/CV_Receiver/UDPPoseReceiver.cs):
  - Menyalin `pyIsHover`, `pyIsGentle`, `pyIsFlyUp`, `targetChestElevation`, dan `pyForearmFlapPower` dari buffer UDP thread-safe di `Update()`.
  - Menambahkan fallback keyboard `H`, `G`, `U` pada `HandleKeyboardFallback()`.
- [`AR Dragon Sky Guardian/Assets/_Scripts/Controllers/IFlightInputProvider.cs`](file:///d:/Fauzih/Kuliah/SEMESTER%20V/Proyek%20Multimedia%20dan%20Game/AR%20Dragon/AR%20Dragon%20Sky%20Guardian/Assets/_Scripts/Controllers/IFlightInputProvider.cs):
  - Menambahkan `IsHover`, `HoverPower`, `IsGentleDescent`, `IsFlyUp`, `ChestElevation`, dan `ActiveStateId` pada struct `FlightCommand`.
- [`AR Dragon Sky Guardian/Assets/_Scripts/Controllers/DefaultFlightInputMapper.cs`](file:///d:/Fauzih/Kuliah/SEMESTER%20V/Proyek%20Multimedia%20dan%20Game/AR%20Dragon/AR%20Dragon%20Sky%20Guardian/Assets/_Scripts/Controllers/DefaultFlightInputMapper.cs):
  - Memetakan `IsHover`, `IsGentleDescent`, `IsFlyUp`, `ChestElevation`, `ForearmFlapPower`, dan `ActiveStateId` dari `IPoseProvider` ke `FlightCommand`.
- [`AR Dragon Sky Guardian/Assets/_Scripts/Controllers/DragonFlightController.cs`](file:///d:/Fauzih/Kuliah/SEMESTER%20V/Proyek%20Multimedia%20dan%20Game/AR%20Dragon/AR%20Dragon%20Sky%20Guardian/Assets/_Scripts/Controllers/DragonFlightController.cs):
  - Menambahkan parameter Inspector, properti publik (`IsInFlightHover`, `IsGentleDescent`, `IsFlyUp`), pembacaan pada `FetchFlightCommand()`, serta cabang fisika prioritas 1–7 di `HandleSkyPeckFlightPhysics()`.
- [`AR Dragon Sky Guardian/Assets/_Scripts/UI/InGameFlightHUD.cs`](file:///d:/Fauzih/Kuliah/SEMESTER%20V/Proyek%20Multimedia%20dan%20Game/AR%20Dragon/AR%20Dragon%20Sky%20Guardian/Assets/_Scripts/UI/InGameFlightHUD.cs):
  - Menambahkan label dan warna status untuk `HOVER MODE`, `GENTLE DESCENT`, dan `FLY UP` pada `DrawPiPSkeleton()` dan `DrawFlightHUD()`.
- [`ARDragonSkyGuardian_CV/test_e2e_flight_physics.py`](file:///d:/Fauzih/Kuliah/SEMESTER%20V/Proyek%20Multimedia%20dan%20Game/AR%20Dragon/ARDragonSkyGuardian_CV/test_e2e_flight_physics.py) *(baru dibuat)*:
  - Pengujian simulasi fisika Unity, roundtrip UDP 39-token (`127.0.0.1:5098`), dan arbitrase `GestureStateMachine`.

### Hasil Pengujian yang Diamati
- `.\ARDragonSkyGuardian_CV\venv\Scripts\python.exe ARDragonSkyGuardian_CV/test_e2e_flight_physics.py`: **6/6 suites PASSED (100%)** (`Fly Up`: Speed `21.00 m/s`, Vy `11.00 m/s`, Pitch `23.9°`; `Hover Hold`: Speed `0.03 m/s`, Vy `0.00 m/s`, Pitch `0.0°`; `Hover Flap Ceased`: Vy `-1.57 m/s`; `Gentle Descent`: Speed `15.94 m/s`, Vy `-1.79 m/s`, Pitch `-8.0°`).
- `test_additional_gestures.py`: **10/10 skenario PASSED** (`5.82 detik`).
- `test_phase5_validation.py`: **7/7 tests PASSED**.
- `test_phase6_final_session.py`: **7/7 tests PASSED**.
- **Kompilasi Unity (`console_get` Unity MCP):** `compilationFailed: false`, `consoleErrors: 0` (Unity `6000.3.7f1`).

---

## 4. Detail Penting yang Harus Diingat
- **Parameter Inspector `DragonFlightController.cs`:** `flyUpLiftPower = 11.0f`, `flyUpThrustAccel = 14.0f`, `flyUpPitch = 24.0f`, `hoverDecelRate = 4.0f`, `inFlightHoverBobAmp = 0.35f`, `inFlightHoverBobFreq = 2.2f`, `gentleDescentSpeed = 16.0f`, `gentleDescentSinkRate = 1.8f`, `gentleDescentPitch = -8.0f`.
- **Konflik Template Prompt Brain:** User sempat menempelkan template prompt berlabel `laptop-a`, namun mengonfirmasi bahwa workspace ini adalah **Laptop B** sehingga seluruh catatan memori wajib berada di `brain/laptop-b/` sesuai `AGENTS.md`.
- **Catatan Vault:** Sudah ada file memori lain berjudul `Rework CV dan Kontrol Terbang Baru.md` di `brain/laptop-b/memories/`; file ini mendokumentasikan khusus integrasi Phase B Unity & verifikasi E2E agar tidak menimpa catatan lama.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] Uji Play Mode langsung menggunakan webcam asli di Unity Editor untuk memvalidasi kenyamanan transisi *Fly Up*, *Hover Mode*, dan *Gentle Descent*.
- [ ] Lanjutkan antrean implementasi pada [[Tasks]].

**Navigasi Obsidian:** [[00 - Overview]] · [[konteks]] · [[prd]] · [[progress]] · [[GDD_Pulau]] · [[Tasks]] · [[Devlog]]
