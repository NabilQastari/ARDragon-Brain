---
title: "Implementasi Mekanik Terbang Sky Peck, Auto-Launch Python Process Manager, dan Update PRD-Konteks v6.0"
type: session-memory
tags:
  - session-memory
  - laptop-a
  - laptop-b
  - sky-peck
  - python-auto-launch
  - unity-flight
  - udp-telemetry
date: 2026-11-13
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[laptop-b/Tasks]]"
  - "[[laptop-b/Devlog]]"
---

# Implementasi Mekanik Terbang Sky Peck, Auto-Launch Python Process Manager, dan Update PRD-Konteks v6.0

## 1. Tujuan & Konteks Sesi
- Meniru mekanik penerbangan tubuh game indie **Sky Peck** (*skypeck.fun*) agar kontrol naga terasa natural, responsif, dan bebas dari disorientasi kamera (*pitch nodding* saat mengepakkan tangan).
- Mengotomatiskan peluncuran modul Python Computer Vision (`active_cv_sender.py`) dari dalam Unity sehingga pengguna tidak perlu lagi membuka terminal terpisah, sekaligus memastikan proses Python berhenti bersih saat Unity keluar dari Play Mode agar webcam tidak terkunci.
- Memutakhirkan dokumen `konteks.md` dan `prd.md` ke **v6.0** agar selaras dengan implementasi terbaru (mekanik Sky Peck, `PythonProcessManager`, arsitektur input modular `IFlightInputProvider`, dan protokol UDP 39-float).

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Model Fisika Penerbangan Sky Peck (`DragonFlightController.cs`):**
   - **Flap to Climb (Decoupled):** Kepakan tangan memberikan daya angkat vertikal halus (`flapLiftPower`) dan dorongan maju (`flapThrustAccel`) tanpa memicu Pose Y/A, sehingga hidung naga tetap mendongak tenang ($+6^\circ \sim +15^\circ$) tanpa goyangan pitch atas-bawah.
   - **Lean to Bank & Turn:** Menggabungkan **50% Arm Tilt** (`_smoothedLW.y - _smoothedRW.y`) dan **50% Torso Lean** (`(midShoulder.x - midHip.x) / shoulderDist`) sehingga pemain dapat berbelok dengan memiringkan tangan maupun mencondongkan tubuh bagian atas.
   - **Tuck to Dive:** Merapatkan kedua tangan di dada atas dengan siku mengarah ke bawah (sesuai foto referensi pengguna) membuat naga menukik tajam hingga kecepatan $28\text{ m/s}$ (`maxDiveSpeed`).
   - **Swoop Climb Out of Dive:** Saat sayap dibuka kembali ke posisi Glide dengan kecepatan berlebih (`_currentAirspeed > cruiseSpeed + 2.5f`), naga otomatis mengonversi energi kinetik menjadi luncuran melambung naik (`_isSwoopClimbing`).
   - **Glide:** Rentangan tangan mendatar menghasilkan laju jelajah stabil $14\text{ m/s}$ (`cruiseSpeed`) dengan *sink rate* lembut $-1.0\text{ m/s}$ (`glideSinkRate`) dan pitch $-4.0^\circ$.
2. **Auto-Launch & Clean Termination Python (`PythonProcessManager.cs`):**
   - Ditempatkan langsung pada GameObject `/Dragon_Player` di scene `Test_Dragon_Flight.unity` (dan otomatis di-attach oleh `UDPPoseReceiver.Awake()` jika belum ada).
   - Menggunakan `System.Diagnostics.Process` untuk menjalankan `ARDragonSkyGuardian_CV/venv/Scripts/python.exe` dengan `active_cv_sender.py --ip 127.0.0.1 --port 5052 --video-port 5053` secara *background* (`hideConsoleWindow = true`).
   - Menggunakan `EditorApplication.playModeStateChanged` (`ExitingPlayMode`), `OnDisable()`, `OnDestroy()`, dan `OnApplicationQuit()` dengan `_pythonProcess.Kill()` + fallback `taskkill /PID {pid} /F /T` agar proses anak dan handle webcam langsung dilepaskan (*zero webcam lock*).
   - Catatan kompatibilitas Unity Mono/.NET: `Process.Kill(bool entireProcessTree)` tidak tersedia di profil .NET Unity sehingga digunakan `Process.Kill()` tanpa argumen ditambah `taskkill /F /T`.
3. **Arsitektur Input Modular & Telemetri 39-Float (v6.0):**
   - Memisahkan sensor CV dari controller fisik melalui `IFlightInputProvider.cs` (`FlightCommand` struct) dan `DefaultFlightInputMapper.cs` (dukungan *Propeller Dash* hingga $26\text{ m/s}$, *Hover*, *Gentle Descent*, *Fly Up*, kurva kemudi non-linear `1.35f`, dan `flapComfortMultiplier = 1.15f`).
   - Protokol UDP port `5052` diperluas hingga 39 nilai float (backward-compatible dengan 13/21 float) untuk membawa biometrik ternormalisasi, state kalibrasi, dan gestur lanjutan, sementara port `5053` mengirim stream JPEG PIP ke `InGameFlightHUD.cs`.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **Skrip C# Unity:**
  - `AR Dragon Sky Guardian/Assets/_Scripts/CV_Receiver/PythonProcessManager.cs` *(Baru)* — Pengelola lifecycle proses Python otomatis.
  - `AR Dragon Sky Guardian/Assets/_Scripts/CV_Receiver/UDPPoseReceiver.cs` — Integrasi `autoLaunchPython`, perbaikan duplikasi deklarasi variabel lokal `shoulderDist` & `midShoulder` (CS0128), penggabungan 50% arm tilt + 50% torso lean, serta dukungan properti biometrik & gestur 39-float.
  - `AR Dragon Sky Guardian/Assets/_Scripts/Controllers/DragonFlightController.cs` — Implementasi `HandleSkyPeckFlightPhysics`, *Propeller Dash* (`maxDashSpeed = 26f`), dan integrasi `IFlightInputProvider`.
  - `AR Dragon Sky Guardian/Assets/_Scripts/Controllers/IFlightInputProvider.cs` & `DefaultFlightInputMapper.cs` *(Baru)* — Abstraksi perintah penerbangan dan pemeta input modular.
  - `AR Dragon Sky Guardian/Assets/_Scripts/UI/InGameFlightHUD.cs` — Pembaruan HUD ke mode `SKY PECK MOTION FLIGHT` serta penambahan status `Python CV : AKTIF (PID: xxxxx) [AUTO-SYNC]`.
  - `AR Dragon Sky Guardian/Assets/_Scenes/Test_Dragon_Flight.unity` — Pemasangan komponen `PythonProcessManager` pada `Dragon_Player` dan penyimpanan scene.
- **Skrip Python (`ARDragonSkyGuardian_CV/`):**
  - `active_cv_sender.py` — Kalkulasi `total_lean` (arm tilt + torso lean), overlay label Sky Peck, dan integrasi tracker/gesture/kalibrasi v6.0.
  - `udp_sender.py` — Dukungan pengiriman CSV hingga 39 float di port `5052` dan JPEG PIP di port `5053`.
  - `test_send_dummy_udp.py` — Siklus uji sintetis 4 fase Sky Peck (Glide → Flap to Climb → Tuck Dive → Swoop Climb & Lean Turn); perbaikan `IndentationError` pada argumen `send_pose`.
- **Dokumentasi Root Project:**
  - `konteks.md` & `prd.md` — Diperbarui ke **v6.0** (Sky Peck Dynamics, `IFlightInputProvider`, `PythonProcessManager`, protokol UDP 39-float).
- **Hasil Pengujian Teramati:**
  - Kompilasi Unity (`script_recompile_status` & `console_get`): `compilationFailed: false`, `consoleErrors: 0`.
  - Uji Play Mode dengan `test_send_dummy_udp.py`: HUD menampilkan `SKY PECK MOTION FLIGHT`, `SWOOP CLIMB` (kecepatan teramati $25.1\text{ m/s}$, lean kanan $16\%$), `FLAP TO CLIMB` ($+4.8\text{ m/s}$ tanpa pitch nodding), dan kenaikan ketinggian dari $11.3\text{ m}$ ke $40.4\text{ m}$.
  - Uji Auto-Launch Python Live di Play Mode: `PythonProcessManager` otomatis menjalankan `active_cv_sender.py` (teramati PID `16092` dan `38724`), feed webcam live + skeleton YOLOv8n-pose tampil di kotak PIP kanan atas Unity, dan verifikasi `Get-Process -Name python` setelah `editor_playmode_stop` memastikan PID Python langsung berhenti bersih.
- **Sinkronisasi Git Repo Project (`origin/master`):**
  - Commit `6f03436` (Sky Peck flight mechanics), `2b4833b` (Auto-launch PythonProcessManager), dan `62c7e90` (Update PRD & konteks v6.0 + modul input/test).

---

## 4. Detail Penting yang Harus Diingat
- **Aturan Anti-Collision Brain:** Workspace ini adalah **Laptop B** (`AGENTS.md`), sehingga penulisan catatan brain wajib dilakukan di `brain/laptop-b/` dan dilarang mengedit `brain/laptop-a/`.
- **Path Python Environment:** `ARDragonSkyGuardian_CV/venv/Scripts/python.exe` dan `ARDragonSkyGuardian_CV/active_cv_sender.py` (di-resolve relatif terhadap `Application.dataPath` `../..` maupun `..`).
- **Port UDP:** `5052` untuk pose/telemetry CSV (hingga 39 float), `5053` untuk video webcam PIP JPEG (`320x180`, quality `48`).
- **Parameter Fisika Sky Peck Utama:** `cruiseSpeed = 14f` m/s, `maxDiveSpeed = 28f` m/s, `maxDashSpeed = 26f` m/s, `glideSinkRate = 1.0f` m/s, `minGroundClearance = 3.5f` m (raycast `500f`, perataan hidung `< 8.0f` m), `maxAltitude = 160f` m.
- **Unity 6 Compatibility:** Gunakan `Process.Kill()` + `taskkill /PID {pid} /F /T` (jangan pakai `Kill(bool)`), dan gunakan `FindFirstObjectByType<T>()` sebagai pengganti `FindObjectOfType<T>()`.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] **Sesi 5 (Gameplay 3 Desa & VFX):** Implementasi `VillageZoneController.cs` pada 3 pad desa (`Pad_Desa1_Polusi` `(-200, 0.13, 80)`, `Pad_Desa2_HutanKebakaran` `(10, 0.23, -133)`, `Pad_Desa3_Kekeringan` `(260, 0.82, 13)`), Auto-Hover lock, progress meter 5 detik, dan partikel VFX (asap, api, hujan embun).
- [ ] **Sesi GUI & Lobby:** Integrasi Canvas Menu Lobby (`Panel_Missions`, `Panel_Calibration`, `Panel_Settings`) dan Victory/Defeat Modal sesuai `[[laptop-a/rencana_desain_ui]]`.
- [ ] **Sesi 6 (Audio, Scoring & Build PC):** Implementasi `GameManager.cs` (Level 1–3, timer, bintang 1–3), sound effects, dan build standalone `.exe` Windows.

---

**Navigasi Obsidian:** [[00 - Overview]] · [[laptop-b/konteks|konteks]] · [[laptop-b/prd|prd]] · [[laptop-b/GDD_Pulau|GDD_Pulau]] · [[laptop-b/Tasks|Tasks]] · [[laptop-b/Devlog|Devlog]]
