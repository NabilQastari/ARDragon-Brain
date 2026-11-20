---
title: Kontrak Integrasi UI Unity & Sinkronisasi Progress (Handoff Laptop B)
tags:
  - ui-ux
  - unity-ui
  - handoff
  - sync-protocol
  - laptop-a
status: ready-for-laptop-b
owner: Laptop A
updated: 2026-11-15
related:
  - "[[00 - Overview]]"
  - "[[rencana_desain_ui]]"
  - "[[panduan_aset_ui_unity]]"
  - "[[konteks]]"
  - "[[prd]]"
  - "[[laptop-b/Tasks]]"
  - "[[laptop-b/Devlog]]"
---

# 🔗 KONTRAK INTEGRASI UI UNITY & SINKRONISASI PROGRESS (LAPTOP A → LAPTOP B)
## FANAMIR: THE SKY GUARDIAN *(Internal Project: AR Dragon Sky Guardian)*

> **Tujuan Dokumen:** Menjadi kontrak permanen antara desain UI (`brain/laptop-a/`) dan implementasi Unity 6 di **Laptop B** (`AR Dragon Sky Guardian/`, namespace internal `DragonWingAR.*` tetap dipertahankan; judul publik game & karakter naga resmi **`Fanamir: The Sky Guardian`** / **`Fanamir`**).
> **Konteks Fase Saat Ini:** Laptop B sedang berada di fase **pengujian & pematangan mekanisme kontrol terbang (`Test_Dragon_Flight.unity`)** dan sudah mengimplementasikan UI v1.6.1 dasar. Fitur **pulau 3D final**, **tantangan 3 desa (`VillageZoneController`)**, **timer/level progression (`GameManager`)**, dan **sistem BGM/SFX** belum tersambung penuh.
> **Prinsip Utama:** Seluruh scene & komponen UI **tetap diimplementasikan dan ditata lengkap sekarang** memakai strategi ***Live + Placeholder + Dormant Hidden*** tanpa mengganggu pengujian mekanisme saat ini, serta **WAJIB disinkronkan secara otomatis setiap kali ada pembaruan/penambahan fitur gameplay di `brain/laptop-b/`**.

---

## 1. MATRIKS STATUS ELEMEN UI SAAT INI (FASE TESTING MEKANISME)

Saat mengimplementasikan aset dari `UI_Unity_Export/` (**140 komponen PNG** + **11 referensi layar `1920×1080`** per revisi `v1.8`), Agent/Developer Laptop B membagi seluruh elemen UI ke dalam **3 kategori eksekusi**:

### 🟢 Kategori A — LIVE WIRED (Langsung Berfungsi Penuh Sekarang)
Fitur backend-nya **sudah tersedia dan terverifikasi** di Laptop B, sehingga UI wajib tersambung ke kode nyata:

| Layar / Komponen | Skrip & Port Backend di Laptop B | Perilaku Nyata Saat Ini |
| :--- | :--- | :--- |
| **Boot Flow: Splash & Start Screen (`SCREEN 09 & 10`, Baru `v1.8`)** | `BootFlowController.cs` *(baru)* + `LobbyNavigationManager.cs` | Saat `MainMenu_Lobby.unity` dimulai pertama kali: **`Splash Screen` (`SCREEN 09`)** tampil ~`2.5s` (skippable setelah `0.5s`) $\rightarrow$ **`Start Screen` (`SCREEN 10`)** menunggu *Press Any Button / Click* (kecuali `Escape` / tombol `ESC · QUIT` untuk keluar) $\rightarrow$ masuk **`Lobby` (`SCREEN 00 - Missions`)**. Saat kembali dari `Test_Dragon_Flight.unity` (*Pause / Finish Modal*), `SkipBootScreensOnce` membawa pemain langsung ke Tab `Missions`. |
| **Shared Navbar (`SCREEN 00/01/05`)** | `LobbyNavigationManager.cs` *(baru)* + `PythonProcessManager.cs` | Pindah tab `CALIBRATION ↔ MISSIONS ↔ SETTINGS`, badge status kamera dinamis pojok kanan atas (`STANDBY`, `LATAR BELAKANG`, `JENDELA PREVIEW`), tombol `ESC · QUIT` / `ESC · BACK`. |
| **CTA Stage 01 (`PLAY`) & Calibration (`READY TO FLY`)** | Scene Loader / Bootstrapper | Membuka/masuk ke scene terbang uji aktif **`Test_Dragon_Flight.unity`** sambil menjaga status kamera/sensitivitas tetap utuh. |
| **Modul Kontrol Kamera 5 State (`SCREEN 01`, `02`, `05`, `06`)** | `PythonProcessManager.cs` | Dropdown kamera DirectShow (`RefreshCameraListAsync()` / `--list-json`), popup list **100% solid opaque (`alpha = 1.0f`, `#FBF6EB`)**, tombol `Scan`, `Latar Belakang` (`--headless`), `Jendela Preview`, dan `Matikan`; refleksi 5 state (`STANDBY Belum Pilih`, `STANDBY Terpilih`, `MEMUAT YOLO`, `LIVE`, `ERROR`). Default tetap `autoLaunchPython = false`. |
| **Viewport Mirror Kamera + Skeleton + Trigger Kalibrasi (`SCREEN 01`)** | `UDPPoseReceiver.cs` (UDP `5052`, `5053`, `5054`) | `RawImage` feed PIP kamera (`5053`), panel sidebar status `calib_state_id` (`0–5`) & `calib_progress` (`0–100%`), tombol `RECALIBRATE (C)` (`TriggerCalibration()` / `CancelCalibration()`) dan `RESET DEFAULT` (`ResetCalibrationProfile()`) ke UDP `5054`. |
| **Live 3D Dragon Preview (`SCREEN 01` Kanan)** | Prefab `Dragon_Model.fbx` + `DragonWingAnimator.cs` + `RenderTexture` | Kamera terisolasi menyorot model naga 3D rigged di atas pedestal, memutar/mengepak sayap secara real-time mengikuti `UDPPoseReceiver` / state pose, plus strip telemetri `STATE`, `SPD`, `PITCH`. |
| **4 Kartu Pose Dinamis (`RECENT POSES · MAX 4`) + Modal Guide `SCREEN 07` (`SCREEN 01` & `07`)** | `CalibrationPanelUI.cs`, `PoseGuideOverlayUI.cs` *(baru)*, `UDPPoseReceiver.cs` | Pada `SCREEN 01`, hanya tampil **4 mini card (`1×4`)**. Default saat game baru dijalankan / reset menampilkan **4 Pose Utama** (`#08 Glide`, `#06 FlapToClimb`, `#04 Takeoff`, `#13 TuckToDive`). Setiap kali pose baru `01..14` terdeteksi stabil di kamera, pose itu masuk/promosi ke **Slot 1 (`SLOT 1 · ACTIVE`)** dan menggeser riwayat sebelumnya ke Slot 2–4 (antrean MRU max 4 tanpa duplikasi). Tombol **`OPEN POSE GUIDE [15 POSES]`** membuka modal **`SCREEN 07` (`Pose Guide · All 15 Poses`)** berisi katalog lengkap 15 pose beserta penjelasannya (plus live highlight & tutup via tombol `CLOSE POSE GUIDE`, `ESC · CLOSE`, atau tombol `Escape`). |
| **In-Game Flight HUD — Telemetri, PIP & Pause (`SCREEN 02`)** | `InGameFlightHUD.cs` + `DragonFlightController.cs` + `UDPPoseReceiver.cs` | Pembacaan live Altitude (`m`), Airspeed (`m/s`), mode pose aktif (`POSE T/Y/A` / `ActiveFlightStateName`), header kontrol kamera + monitor PIP 16:9 (`5053`), serta tombol `ESC · PAUSE` (`Time.timeScale = 0f` + menu Pause ringkas / kembali ke Lobby). |
| **Slider `Turn Sensitivity` & `Text Auto-Next` (`SCREEN 05`)** | `DefaultFlightInputMapper` / `DragonLocomotionConfig` + `PlayerPrefs` | `Turn Sensitivity` (`1.0×–3.0×`) & **`Text Auto-Next` (`2.0s–10.0s`, default `5.0s`)** disimpan saat `APPLY & CLOSE` (`PlayerPrefs`) dan dibaca `QuestNarrativeUI` untuk interval auto-advance. |

---

### 🟡 Kategori B — PLACEHOLDER FUNGSIONAL (Tampil Sesuai Desain, Isi Data Sementara)
Elemen terlihat di layar saat navigasi atau terbang agar bentuk UI utuh dan siap dites tampilannya, tetapi datanya bersifat dummy/konfigurasi sementara hingga sistem aslinya dibangun:

| Layar / Komponen | Kondisi Placeholder Sekarang | Kontrak Method/Slot Masa Depan (`// TODO [SYNC-GAMEPLAY]`) |
| :--- | :--- | :--- |
| **Kartu `STAGE 02 (WILDFIRE)` & `STAGE 03 (DROUGHT)` (`SCREEN 00`)** | Tampilkan seluruh visual (pita stage, biome thumbnail, bintang contoh, gembok `UI_Scrim_LockedCard_Overlay`, tag speech-bubble `90s`/`150s`). Tombol `SELECT` Stage 02 sementara memuat `Test_Dragon_Flight.unity` (atau menampilkan banner info mode uji), sementara Stage 03 terkunci/placeholder (`ShowLockHint()`). | Hubungkan ke `StageProgressData` / `GameManager` saat scene level & sistem bintang ditambahkan. |
| **Slider `Master`, `BGM`, `SFX Volume` (`SCREEN 05`)** | Slider, nilai teks `%`, dan tombol `APPLY & CLOSE` berfungsi penuh serta disimpan di `PlayerPrefs` (`Master` dapat langsung mengatur `AudioListener.volume`), sedangkan `BGM` & `SFX` disiapkan slot `AudioMixer` / event publik tanpa wajib punya file audio dulu. | Sambungkan langsung ke `AudioManager` / `AudioMixer` pada Sesi Audio. |
| **Kapsul Tengah Atas HUD (`Timer & VILLAGES 0/3`, `SCREEN 02`)** | Tampil sebagai kapsul rapi bertulisan placeholder (mis. `--:-- · TEST FLIGHT (0/3)`). Tidak memicu timeout/kalah selama fase testing mekanisme. | Method publik: `SetMissionTimer(float remainingSeconds)` & `SetVillageCount(int cleared, int total)`. |
| **Global Vitality Bar Vertikal Kiri (`SCREEN 02`)** | Tampil lengkap (`Track + Fill + Ticks + Badge 0%`) dengan `fillAmount = 0f` statis sementara. | Method publik: `SetGlobalVitality(float normalized01)`. |
| **Quest Objective Panel Kiri Atas (`SCREEN 02`, `v1.7`)** | Tampil di atas Global Vitality Bar dengan state spawn default `TRAVEL` (*"Fly to the marked village (Smog Valley)"* / *"Free Flight Test Mode"* bila belum ada desa aktif). | Method publik: `SetQuestObjective(string statusBadge, bool isActiveTask, string mainTitle, string hintText)` & `ResetQuestObjectiveToTravel(string villageName)`. |
| **Kartu Pose ke-15 (`UI_Pose_15_VillageActions`)** | Selalu tampil utuh di katalog **`SCREEN 07` (`Pose Guide · All 15 Poses`)** beserta penjelasannya; tidak masuk ke 4 pose utama default di `SCREEN 01` kecuali dipicu oleh tantangan desa nanti (`SetVillageActionHighlight(bool)`). | Saat `VillageZoneController` / aksi desa diuji atau dipicu, aktifkan highlight kartu `#15` di `SCREEN 07` & promosikan ke Slot 1 antrean dinamis `SCREEN 01`. |

---

### ⚪ Kategori C — DORMANT / HIDDEN PREFAB (`SetActive(false)` Secara Default)
Komponen ini **tidak akan muncul otomatis selama fase testing mekanisme** karena trigger gameplay-nya (`VillageZoneController`, `GameManager`) memang belum dibuat. Namun **WAJIB tetap dirakit lengkap menjadi Prefab & dipasang nonaktif di Canvas** agar arsitektur UI komplit sejak sekarang:

| Layar / Komponen | Status Default Saat Play Mode | Method Publik Siap Pakai & Opsi Test QA |
| :--- | :--- | :--- |
| **Waypoint Compass Tengah (`UI_HUD_Waypoint_Chevron`, `SCREEN 02`)** | `SetActive(false)` ketika target desa `== null`. | `SetWaypointTarget(Transform target, string villageLabel)` / `ClearWaypoint()`. |
| **`SCREEN 08` — `QuestNarrativeUI` (`Panel_QuestNarrative`, Baru `v1.7`)** | **Tertutup (`SetActive(false)` / `alpha = 0`)** saat terbang bebas tanpa trigger masuk area desa. | `ShowNarrativeSequence(QuestLineData[] lines, string villageId, string actionInstruction, System.Action onComplete)`, `HideNarrative()`. **Berjalan otomatis (*hands-free*, tanpa tombol Continue)** dengan hitung mundur `Text Auto-Next` (default `5.0s` per baris dari Settings/`PlayerPrefs`); selesai semua baris -> otomatis tutup & ubah `QuestObjective` ke `ACTIVE TASK`. Tambah `[ContextMenu("Debug/Preview Quest Narrative")]`. |
| **`SCREEN 03` — `VillageChallengeUI` (`Panel_VillageChallenge`)** | **Tertutup (`SetActive(false)` / CanvasGroup `alpha = 0, interactable = false`)** saat gameplay normal tanpa zona desa. | `ShowChallenge(string villageTitle, string gestureInstruction, int biomeType, float progress01)`, `UpdateProgress(float progress01)`, `HideChallenge()`. |
| **`SCREEN 04` — `FinishModalUI` (`Panel_FinishModal`: Victory & Defeat)** | **Tertutup (`SetActive(false)`)** — tidak akan muncul karena belum ada tantangan/timer kalah/menang. | `ShowVictory(int stars, float elapsedSec, int cleared, int total, int rewardBonusStars)`, `ShowDefeat(float elapsedSec, int cleared, int total, string remainingNote)`, `HideModal()`, lengkap dengan tombol `LEVEL BERIKUTNYA`, `COBA LAGI`, dan `KEMBALI KE LOBBY`. |

> [!TIP]
> **Debug Test Hook Khusus Editor (`#if UNITY_EDITOR`):** Tambahkan `[ContextMenu("Debug/Preview Victory")]`, `[ContextMenu("Debug/Preview Defeat")]`, dan `[ContextMenu("Debug/Preview Village Challenge")]` (atau tombol test kecil di Custom Inspector / Editor menu — **jangan** pakai hotkey yang bentrok dengan kontrol terbang keyboard) agar Laptop B dapat menguji visual `SCREEN 03` & `SCREEN 04` kapan saja di Play Mode tanpa perlu menunggu Sesi Tantangan Desa selesai.

---

## 2. PROTOKOL WAJIB SINKRONISASI PROGRESS ↔ UI (`Feature-to-UI Sync Rule`)

> [!IMPORTANT]
> **ATURAN KERAS UNTUK AI AGENT & DEVELOPER LAPTOP B DI SESI MENDATANG:**
> Setiap kali ada pembaruan kode gameplay, penambahan scene/pulau, penambahan mekanik/pose, atau penambahan sistem tantangan di **Laptop B**, **AI Agent WAJIB mengecek kontrak tabel di bawah dan langsung memutakhirkan/menyambungkan UI placeholder/dormant terkait — BUKAN membiarkannya tetap dummy.**

| Ketika Progress Baru di Laptop B Menambahkan / Mengubah... | Perubahan UI yang WAJIB Ikut Dilakukan |
| :--- | :--- |
| **1. Scene Gameplay & Pulau 3D Baru** *(mis. `Pulau_Archipelago.fbx` masuk scene baru selain `Test_Dragon_Flight.unity`)* | Arahkan tombol **`PLAY`** / **`READY TO FLY`** (atau `StageConfig`) ke scene gameplay baru sambil mempertahankan opsi testing `Test_Dragon_Flight.unity`. Cek ulang `Main Camera` / `Layer` supaya UI Canvas tetap bersih. |
| **2. Tantangan Desa (`VillageZoneController.cs` & VFX 3 Desa)** | 1. Saat **Fanamir** masuk trigger area desa & memainkan *Idle Fly*: panggil `QuestNarrativeUI.ShowNarrativeSequence(...)` (`SCREEN 08`) yang berjalan **auto-play *hands-free*** mengikuti interval slider `Text Auto-Next` (Settings); setelah selesai, box otomatis menutup, meng-update `Quest Objective Panel` (`SCREEN 02`) ke **`ACTIVE TASK`**, lalu melanjutkan ke `VillageChallengeUI.ShowChallenge(...)`.<br>2. Hubungkan arah Pad Desa bertanda (*marked village*) ke `UpdateWaypoint(...)` agar `UI_HUD_Waypoint_Chevron` aktif selama objective berstatus `TRAVEL`.<br>3. Sambungkan progres total desa (`0/3 -> 3/3`) ke `SetVillageCount(...)` & `SetGlobalVitality(...)` di HUD `SCREEN 02`.<br>4. Aktifkan highlight kartu **`15_VillageActions`** bila gestur aksi desa dipraktikkan.<br>5. **Jika ada desa/gestur yang ditambah, dihapus, atau berubah aturan/teksnya,** perbarui teks TMP/ikon pada `SCREEN 03`, `SCREEN 08`, deskripsi di `SCREEN 01`, dan pill `LIMIT ...` di `SCREEN 00`. |
| **3. Sistem Misi, Timer, Bintang & Progression (`GameManager.cs`)** | 1. Hubungkan hitungan mundur asli (`90s` / `150s` / `210s`) ke `SetMissionTimer(...)` di HUD `SCREEN 02`.<br>2. Hubungkan event menang / waktu habis ke `FinishModalUI.ShowVictory(...)` & `FinishModalUI.ShowDefeat(...)` (`SCREEN 04`), pastikan `Time.timeScale` & input/kamera ditangani bersih saat modal tampil.<br>3. Hubungkan status unlock & bintang (`PlayerPrefs` / save profile) ke **`Panel_Missions` (`SCREEN 00`)** untuk men-toggle `UI_Scrim_LockedCard_Overlay`, status kunci `STAGE 02/03`, serta bintang emas/kosong. |
| **4. Mekanik Terbang / Daftar Pose Baru atau Diubah** | Perbarui katalog pose di **`CalibrationPanelUI` (4 pose utama default / antrean riwayat dinamis `SCREEN 01`)** & **`PoseGuideOverlayUI` (grid 15 kartu lengkap `SCREEN 07`)**, warna pill status pose di dock telemetri `SCREEN 02`, dan binding state highlight pada UI controller agar tetap 1:1 dengan `DragonFlightController` & `motion_recognizer.py`. |
| **5. Sistem Audio (`AudioManager` / `BGM` / `SFX`)** | Hubungkan slider **`Master`**, **`BGM`**, dan **`SFX Volume`** pada `Panel_Settings` (`SCREEN 05`) ke `AudioMixer` / sumber audio resmi, lalu hapus komentar `TODO [SYNC-AUDIO]`. |
| **6. Kewajiban Log ke Obsidian `brain/laptop-b/`** | Setiap sinkronisasi UI ↔ Gameplay **WAJIB** dicatat di `brain/laptop-b/Devlog.md` (menyebut komponen mana yang berpindah status dari *Placeholder/Dormant* menjadi *Live*) dan di-centang di `brain/laptop-b/Tasks.md`, lalu `git -C brain commit && push`. |

---

## 3. SALINAN PROMPT IMPLEMENTASI UI UNTUK AI AGENT LAPTOP B

> Gunakan prompt lengkap di bawah ini pada AI Agent di **Laptop B** bersamaan dengan lampiran file **ZIP hasil ekspor [`UI_Unity_Export/`](file:///e:/Semester%205/Game/AR%20Dragon/UI_Unity_Export/)** (`Components/` 134 PNG + `Designs/` 7 PNG).

```markdown
Tugas: Implementasikan seluruh UI & Scene UI game "AR Dragon: Sky Guardian" di Unity 6 URP (Laptop B) menggunakan paket ZIP aset UI yang saya lampirkan bersama prompt ini, sambil memastikan sistem testing mekanisme terbang saat ini (`Test_Dragon_Flight.unity`) tetap berjalan normal tanpa regresi.

### 1. BACA KONTEKS & ATURAN WAJIB TERLEBIH DAHULU
1. Jalankan `git -C brain pull`, lalu baca `AGENTS.md` (workspace ini adalah Laptop B: dilarang mengubah/menghapus file di `brain/laptop-a/`, tulis log hanya di `brain/laptop-b/`).
2. Baca tuntas dokumentasi desain & implementasi UI dari Laptop A (read-only):
   - `brain/laptop-a/rencana_desain_ui.md`
   - `brain/laptop-a/panduan_aset_ui_unity.md` (WAJIB perhatikan seluruh nilai dimensi aktual, aturan Linear Color Space `alpha = 1.0f`, urutan Sprite Editor Inspector `L, T, R, B` vs C# `Sprite.border` `(L, B, R, T)`, aturan Horizontal 3-Slice `T=0, B=0`, batasan tinggi tombol bertakik `=`, viewport berlubang `alpha=0`, dan margin `clip: false`)
   - `brain/laptop-a/kontrak_integrasi_ui_dan_progress.md`
3. Pahami kode eksisting Laptop B di `AR Dragon Sky Guardian/` & `brain/laptop-b/` (`Tasks.md`, `Devlog.md`, `konteks.md`), khususnya:
   - `Test_Dragon_Flight.unity`
   - `PythonProcessManager.cs`, `UDPPoseReceiver.cs`, `DragonFlightController.cs`, `DefaultFlightInputMapper.cs`, `DragonWingAnimator.cs`, `InGameFlightHUD.cs`, `DragonLocomotionConfig.asset`.

### 2. IMPOR & KONFIGURASI OTOMATIS ASET ZIP (`UI_Unity_Export`)
1. Ekstrak ZIP aset UI terlampir:
   - Masukkan seluruh isi folder `Components/` (`134` PNG dalam `20` subfolder) ke `AR Dragon Sky Guardian/Assets/UI/Components/`.
   - Simpan folder `Designs/` (`7` PNG layar `1920×1080`) di luar folder build runtime (mis. di folder dokumentasi/referensi di luar `Assets/`) hanya sebagai cetak biru visual saat menata UI.
2. Buat Editor Tool / AssetPostprocessor C# (`Assets/Editor/UISpriteSetupUtility.cs`) untuk mengonfigurasi **seluruh 134 PNG** secara presisi tanpa salah ketik:
   - `TextureType = Sprite`, `SpriteImportMode.Single`, `spritePixelsPerUnit = 100`, `meshType = SpriteMeshType.FullRect`, `filterMode = FilterMode.Bilinear`, `textureCompression = TextureImporterCompression.Uncompressed`, `sRGBTexture = true`, `alphaIsTransparency = true`.
   - Set **`spriteBorder = new Vector4(L, B, R, T)`** *(AWAS: parameter `Vector4` C# urutannya Left, Bottom, Right, Top — JANGAN terbalik dengan urutan tabel `L, T, R, B` di `panduan_aset_ui_unity.md`!)* untuk semua aset `9-Slice` dan `Horizontal 3-Slice` sesuai tabel persis di `brain/laptop-a/panduan_aset_ui_unity.md`.
3. Perhatikan bahwa seluruh **134 PNG bersifat textless (tanpa teks)**. Gunakan `TextMeshProUGUI` dengan font bergaya rounded geometric sans-serif (`Fredoka` / `Nunito` / font rounded yang disiapkan di `Assets/Fonts/`), warna tinta gelap `#2D2926` / `#332C25` di perkamen, dan `#FFFFFF` di tombol gelap/hijau/coral & viewport `#1A2125`. Pastikan **semua panel perkamen & Popup Dropdown kamera (`UI_Dropdown_Popup_9Slice`) menggunakan `Image.color = Color.white` (`alpha = 1.0f`, zero transparency)** di Linear Color Space!

### 3. ARSITEKTUR UI HYBRID (LIVE + PLACEHOLDER + DORMANT) — TANPA MERUSAK TEST MEKANISME
Karena progress project saat ini masih di tahap **testing mekanisme terbang** (belum ada Pulau 3D final, belum ada `VillageZoneController`/tantangan desa, belum ada `GameManager`/timer/bintang, dan belum ada sistem BGM/SFX lengkap):

1. **Perlindungan Wajib Testing Mekanisme (`Test_Dragon_Flight.unity`):**
   - Jangan merusak wiring `/Dragon_Player`, `DragonFlightController`, `DefaultFlightInputMapper`, `DragonWingAnimator`, maupun `DragonLocomotionConfig.asset`.
   - Pastikan scene `Test_Dragon_Flight.unity` **tetap bisa di-Play langsung dari Editor** tanpa error jika sedang menguji gerakan naga, maupun dijalankan normal lewat Main Menu Lobby.
   - Pastikan `PythonProcessManager` & `UDPPoseReceiver` tetap memegang aturan aman (`autoLaunchPython = false` saat startup, zero duplicate process, bersih berhenti saat stop Play Mode / pindah scene).

2. **Yang HARUS Dibuat LIVE WIRED Sekarang:**
   - **Scene Lobby baru (`Assets/_Scenes/MainMenu_Lobby.unity`, Build Index 0; `Test_Dragon_Flight.unity` Build Index 1)** + **`LobbyNavigationManager.cs`**:
     - Shared Top Bar + 3 Tab (`CALIBRATION`, `MISSIONS`, `SETTINGS`) + Badge Status Kamera Dinamis pojok kanan atas tersinkron ke `PythonProcessManager.cs` + `ESC · QUIT` / `ESC · BACK`.
     - **Tab `MISSIONS` (`SCREEN 00`):** Tombol **`PLAY`** pada Stage 01 langsung meluncurkan `Test_Dragon_Flight.unity` sambil membawa status kamera/konfigurasi aktif.
     - **Tab `CALIBRATION` (`SCREEN 01`):** Hubungkan penuh modul kamera 5 state ke `PythonProcessManager.cs`, mirror webcam PIP (`UDP 5053`) + sidebar status kalibrasi (`calib_state_id 0–5` & `calib_progress`) + tombol trigger kalibrasi (`RECALIBRATE (C)` / `RESET DEFAULT` ke `UDP 5054`) ke `UDPPoseReceiver.cs`. Buat **Live 3D Dragon Preview** memakai kamera terisolasi + `RenderTexture` + `Dragon_Model.fbx` (`DragonWingAnimator.cs`) di balik `UI_Viewport_Calib_Frame_Hollow_9Slice`. Tampilkan ke-15 kartu pose (`UI_Pose_01..15`) dan hubungkan **14 kartu pose lokomosi (`01–14`)** agar otomatis highlight (`UI_Plaque_PoseCard_Active_9Slice` + `UI_Pill_Active_Gold`) saat pose aktif terdeteksi. Tombol **`READY TO FLY`** masuk ke `Test_Dragon_Flight.unity`.
     - **Tab `SETTINGS` (`SCREEN 05` + `SCREEN 06`):** Hubungkan modul `Camera Input & AI YOLO` lengkap dengan ke-5 visual state + **Popup Dropdown 100% Opaque `#FBF6EB`** ke `PythonProcessManager.cs`. Hubungkan slider **`Turn Sensitivity`** (`1.0×–3.0×`) secara nyata + persisten (`PlayerPrefs`) ke kontrol kemudi.
   - **Upgrade HUD In-Game (`SCREEN 02`) di `Test_Dragon_Flight.unity`:**
     - Bangun uGUI Canvas `1920×1080` (`Match = 0.5`, latar transparan di atas kamera 3D) menggantikan HUD IMGUI lama untuk tampilan utama pemain (opsional sediakan toggle debug kecil `F3`/Inspector flag jika masih diperlukan teks debug mentah `LastTransitionReason` saat testing).
     - Hubungkan **Dock Telemetri Kanan Bawah** (Altitude, Airspeed, Pose State Badge), **Header Kontrol Kamera + Monitor PIP 16:9 (`UDP 5053` + `UI_Viewport_PIP_Bezel_Hollow_9Slice`)**, dan **Tombol `ESC · PAUSE`** (`Time.timeScale = 0f` + Pause Menu sederhana: Resume / Settings / Back to Lobby).

3. **Yang Dijadikan PLACEHOLDER FUNGSIONAL (Siap Ganti Data Nanti):**
   - **Stage 02 (`WILDFIRE`) & Stage 03 (`DROUGHT` / Locked)** pada `SCREEN 00`: render penuh sesuai desain (pita tag, thumbnail biome, slot bintang, gembok `UI_Scrim_LockedCard_Overlay`, callout `90s`/`150s`) dengan data placeholder bertanda `// TODO [SYNC-GAMEPLAY]`. Tombol `SELECT` Stage 02 boleh dialihkan ke `Test_Dragon_Flight.unity` atau notifikasi mode uji; Stage 03 tampil terkunci.
   - **Slider `Master`, `BGM`, `SFX Volume`** pada `SCREEN 05`: UI slider & angka `%` berfungsi penuh dan disimpan ke `PlayerPrefs` (`Master` mengatur `AudioListener.volume`); siapkan event/hook placeholder untuk `BGM` & `SFX` AudioMixer.
   - **HUD Atas Tengah (`Timer & VILLAGES 0/3`) & Vitality Bar Vertikal Kiri** pada `SCREEN 02`: tampil sesuai desain dengan nilai placeholder aman (`--:-- · TEST FLIGHT 0/3`, Vitality `0%`) + sediakan method publik (`SetMissionTimer`, `SetVillageCount`, `SetGlobalVitality`) untuk diikat di sesi berikutnya.
   - **Kartu Pose ke-15 (`UI_Pose_15_VillageActions`)** pada `SCREEN 01`: tampil utuh secara visual, status highlight dinamis diberi placeholder hook untuk tantangan desa nanti.

4. **Yang Tetap DITAMBAHKAN Sebagai DORMANT / HIDDEN PREFAB (`SetActive(false)`):**
   - **Waypoint Compass (`UI_HUD_Waypoint_Chevron`)** di `SCREEN 02`: tersembunyi default saat `target == null`, aktif jika `SetWaypointTarget(...)` dipanggil.
   - **`SCREEN 03` (`VillageChallengeUI`)** & **`SCREEN 04` (`FinishModalUI` — Victory & Defeat)**: **WAJIB dibuat lengkap menjadi Prefab & dipasang di UI Canvas dalam kondisi nonaktif (`SetActive(false)`)** sehingga tidak mengganggu/muncul otomatis selama belum ada tantangan desa, tetapi memiliki:
     - Controller ber-API bersih (`ShowChallenge`, `UpdateProgress`, `HideChallenge`; serta `ShowVictory`, `ShowDefeat`, `HideModal` lengkap dengan tombol `LEVEL BERIKUTNYA`, `COBA LAGI`, `KEMBALI KE LOBBY`).
     - `[ContextMenu]` Debug Test di Inspector (khusus Editor) agar modal/overlay ini bisa di-preview visualnya langsung di Play Mode untuk verifikasi layout 9-slice, bintang, banner Victory/Defeat, dan TMP.

### 4. VERIFIKASI & KEWAJIBAN UPDATE BRAIN (`Feature-to-UI Sync`)
1. Pastikan **0 Compiler Error & 0 Console Warning/Error** pada Unity (`Assets/Refresh`), verifikasi seluruh `9-Slice` rapi tanpa distorsi sudut/notch, uji **Popup Dropdown Kamera 100% Opaque** di Linear Color Space (zero tembus teks), dan uji Play Mode baik dari `MainMenu_Lobby` maupun langsung di `Test_Dragon_Flight.unity` (verifikasi kamera 5 state, kalibrasi `5054`, UDP `5052/5053`, serta preview Editor `SCREEN 03/04`).
2. Buat/perbarui catatan di **`brain/laptop-b/`** (`Tasks.md`, `Devlog.md`, dan catatan arsitektur/memori UI di `brain/laptop-b/`) yang secara eksplisit mendokumentasikan:
   - Mana elemen UI yang sudah **Live**, mana yang masih **Placeholder**, dan mana yang **Dormant Prefab** beserta nama script & method publiknya.
   - **Aturan Sinkronisasi Progress Wajib di sesi-sesi mendatang:** setiap kali ada pembaruan di Laptop B (mis. penambahan **Pulau 3D**, **Tantangan 3 Desa / `VillageZoneController`**, **`GameManager` Timer/Stars/Unlock**, **AudioManager**, atau **perubahan gestur**), agent/developer wajib sekaligus membuka/menyesuaikan slot placeholder UI terkait agar UI dan mekanisme game selalu 100% sinkron.
3. Akhiri dengan `git -C brain add` (hanya folder `brain/laptop-b/`), `commit`, dan `push`.
```

---

## 4. PROMPT PENYESUAIAN UI KALIBRASI & POSE GUIDE OVERLAY (LAPTOP B — DELTA PATCH v1.6.1)

> Gunakan prompt ringkas di bawah ini jika UI awal (`MainMenu_Lobby.unity`, `134` sprite komponen di `Assets/UI/Components/`, `CalibrationPanelUI.cs`, `UISceneAndPrefabBuilder.cs`) **sudah terimplementasi** di Laptop B dan hanya perlu disesuaikan ke revisi **`SCREEN 01` (4 kartu riwayat dinamis) + `SCREEN 07` (Modal Pose Guide 15 Pose)**.
> **File yang dikirim bersama prompt ini cukup `2` PNG referensi baru dari `UI_Unity_Export/Designs/` saja:**
> 1. `01_Calibration_MotionTutorial.png` *(timpa file lama di `docs/UI_Designs_Reference/`)*
> 2. `07_PoseGuide_OverlayModal.png` *(tambah file baru ke `docs/UI_Designs_Reference/`)*
> *(Tidak perlu mengirim ulang / mengubah `134` PNG di `Assets/UI/Components/` maupun `UISpriteSetupUtility.cs` karena seluruh komponennya sudah tersedia di project Laptop B).*

```markdown
Tugas: Terapkan penyesuaian desain UI Kalibrasi (`SCREEN 01`) dan tambahkan modal Pose Guide Overlay (`SCREEN 07`) pada project Unity 6 URP "AR Dragon: Sky Guardian" di Laptop B (berbasis implementasi UI Hybrid yang sudah berjalan di `MainMenu_Lobby.unity`), tanpa mengganggu `Test_Dragon_Flight.unity` maupun pengujian mekanisme terbang.

### 1. BACA DOKUMENTASI & REFERENSI TERBARU
1. Jalankan `git -C brain pull` terlebih dahulu (patuhi aturan `AGENTS.md`: dilarang mengedit `brain/laptop-a/`, catat pembaruan hanya di `brain/laptop-b/`).
2. Baca pembaruan terbaru dari Laptop A (read-only):
   - `brain/laptop-a/rencana_desain_ui.md` (bagian `2.2` revisi `SCREEN 01` & `2.2.1` `SCREEN 07`)
   - `brain/laptop-a/panduan_aset_ui_unity.md` (item `3. SCREEN 01`, `8. SCREEN 07`, & Checklist C# Bab `5`)
   - `brain/laptop-a/kontrak_integrasi_ui_dan_progress.md`
3. Pasang 2 file referensi PNG (`1920×1080`) yang saya lampirkan ke `docs/UI_Designs_Reference/`:
   - Timpa `docs/UI_Designs_Reference/01_Calibration_MotionTutorial.png`
   - Tambahkan `docs/UI_Designs_Reference/07_PoseGuide_OverlayModal.png`
4. **Catatan Aset:** Tidak ada penambahan maupun perubahan pada `Assets/UI/Components/` (`134` PNG) maupun `UISpriteSetupUtility.cs`. Gunakan sprite yang sudah di-import:
   - `UI_Panel_Parchment_Full_9Slice` *(ingat temuan sebelumnya: ornamen kipas sudut sudah menyatu di artwork PNG ini, jadi jangan ditumpuki `UI_Corner_Fan_Medallion_24`)*
   - `UI_Panel_Parchment_Calib_9Slice`, `UI_Plaque_PoseCard_Default_9Slice`, `UI_Plaque_PoseCard_Active_9Slice`, `UI_Pill_Active_Gold`, `UI_Pose_01..15`, `UI_Chip_Bar_Cream_9Slice`, `UI_Badge_Label_Amber_Blank`, `UI_Btn_Secondary_Cream_9Slice`, `UI_Btn_TopChrome_Cream_9Slice`, `UI_IconBadge_CloseX_20`, `UI_Crest_Baroque_Scrollwork`, `UI_Banner_Stepped_Modal_Blank`.

### 2. PENYESUAIAN `SCREEN 01` (`CalibrationPanelUI.cs` & `UISceneAndPrefabBuilder.cs`)
Ubah tata letak panel Calibration pada `MainMenu_Lobby.unity` agar sesuai dengan `01_Calibration_MotionTutorial.png`:
1. **Perbesar Top Panels (`Left Panel` Mirror & `Right Panel` Live Dragon Preview):**
   - Tingkatkan tinggi kedua panel atas menjadi proporsi **`860×540`** di kanvas referensi `1920×1080` sehingga area viewport Skeleton (`~380 px` tinggi) dan area `RenderTexture` Live 3D Dragon Preview (`~438 px` tinggi) jauh lebih lega, jelas, dan tetap fit 100% di dalam viewport `1920×1080` bersama strip tengah, 4 kartu bawah, & bar rule/CTA.
2. **Strip Header Tengah + Tombol Pembuka Pose Guide (`y ≈ 676–678` dari atas):**
   - Di sebelah kiri (`~1372×50`): `UI_Chip_Bar_Cream_9Slice` berisi pill amber `UI_Badge_Label_Amber_Blank` bertulisan **`RECENT POSES · MAX 4`** dan teks penjelas TMP: *"Default at launch: 4 Main Poses (Glide, Flap, Takeoff, Tuck Dive) · Slots update dynamically with your last 4 detected poses"*.
   - Di sebelah kanan (`~368×54`): tombol **`OPEN POSE GUIDE`** (`UI_Btn_Secondary_Cream_9Slice`) dilengkapi badge emas **`15 POSES`** (`UI_Pill_Active_Gold`) yang membuka modal `SCREEN 07`.
3. **Baris `1×4` Kartu Pose Dinamis (Hanya Maksimal 4 Kartu di Halaman Kalibrasi):**
   - Hapus/gantikan grid `3×5` lama di panel Calibration menjadi tepat **4 slot kartu besar (`~422×166 px` tiap kartu, jarak horizontal `24 px`)**.
   - **Perilaku Default Saat Launch / Reset:**
     - Saat game baru dibuka / panel reset, ke-4 slot default menampilkan **4 Pose Utama**:
       1. Slot 1: **Glide (T-Pose)** (`#08`, highlight aktif awal memakai `UI_Plaque_PoseCard_Active_9Slice` & badge `SLOT 1 · ACTIVE`)
       2. Slot 2: **Flap To Climb** (`#06`, `UI_Plaque_PoseCard_Default_9Slice`, badge `SLOT 2 · DEFAULT`)
       3. Slot 3: **Takeoff** (`#04`, `UI_Plaque_PoseCard_Default_9Slice`, badge `SLOT 3 · DEFAULT`)
       4. Slot 4: **Tuck To Dive** (`#13`, `UI_Plaque_PoseCard_Default_9Slice`, badge `SLOT 4 · DEFAULT`)
   - **Perilaku Rotasi Dinamis (*Last-Performed MRU Queue*):**
     - Saat tracker pose (`UDPPoseReceiver` / state pose live `01..14`, serta `SetVillageActionHighlight(true)` untuk `#15` nanti) mendeteksi pose valid baru `P` (abaikan `None`/tidak terdeteksi, dan tambahkan debounce/dwell singkat mis. `~0.15s` agar kedipan noise CV 1-frame tidak mengacak urutan):
       - Jika `P` sudah ada di dalam 4 slot (`Slot 2..4`), **pindahkan `P` ke Slot 1** tanpa membuat duplikat kartu, sementara slot sebelumnya bergeser ke kanan mengisi posisi `P` yang kosong.
       - Jika `P` belum ada di 4 slot, **sisipkan `P` ke Slot 1** dan geser Slot `1→2→3→4` (item lama ke-5 terkeluar otomatis; total tetap maksimal 4 kartu).
       - Slot 1 selalu disorot aktif (`UI_Plaque_PoseCard_Active_9Slice`, pill `SLOT 1 · ACTIVE` atau `SLOT 1 · RECENT`), sedangkan Slot 2–4 memakai `UI_Plaque_PoseCard_Default_9Slice` dengan label `SLOT 2..4 · RECENT` (atau `DEFAULT` bila slot tsb masih bawaan awal dan belum pernah digeser).
       - Sediakan juga method `ResetRecentPosesToDefault()` (mis. terpanggil saat klik `RESET DEFAULT` atau opsi reset sesi) yang mengembalikan urutan `[08, 06, 04, 13]`.
     - *(Catatan Sprite `UI_Pose_08_Glide_ActiveGold.png`)*: Karena PNG #08 memiliki lingkaran medali emas bawaan sedangkan pose lain netral, jadikan **swap plaque `UI_Plaque_PoseCard_Active_9Slice` + warna badge `SLOT 1 · ACTIVE`** sebagai indikator aktif utama yang konsisten di semua pose.

### 3. TAMBAHAN MODAL `SCREEN 07` — `PoseGuideOverlayUI.cs` & `PF_PoseGuideOverlay.prefab`
Bangun modal **Pose Guide Overlay (`07_PoseGuide_OverlayModal.png`)** di Lobby Canvas (`SetActive(false)` secara default, tampil di layer teratas di atas halaman Calibration saat `OPEN POSE GUIDE` diklik):
1. **Struktur Visual (`1920×1080`):**
   - Backdrop scrim solid gelap (`Image` warna `#1B1815`, `alpha ≈ 0.58f`) menutupi background lobby agar fokus tertuju ke modal & memblokir klik di belakangnya.
   - Pojok kanan atas layar: tombol **`ESC · CLOSE`** (`UI_Btn_TopChrome_Cream_9Slice` + `UI_IconBadge_CloseX_20`).
   - Kontainer utama `1824×824`: `UI_Panel_Parchment_Full_9Slice` (tanpa duplikasi ornamen sudut), mahkota `UI_Crest_Baroque_Scrollwork`, dan banner **`POSE GUIDE · ALL 15 POSES`** (`UI_Banner_Stepped_Modal_Blank`).
   - Strip legenda kategori atas: 4 pil kategori warna lembut (`01–03 · GROUND`, `04–05 · LAUNCH & STEER`, `06–12 · CORE FLIGHT`, `13–15 · DIVE · DASH · VILLAGE`) + keterangan *"Default 4 Main Poses shown at launch (04, 06, 08, 13) · Calibration displays your last 4 detected poses"*.
   - **Grid lengkap `3 baris × 5 kolom` berisi semua `15` pose (`#01` s.d. `#15`):** ikon `UI_Pose_01..15`, judul pose, subtitle mekanik (warna per kategori), deskripsi singkat yang jelas bagi pemain, nomor `#01..#15`, serta badge **`MAIN`** pada pose `#04, #06, #08, #13`. Saat kamera aktif & modal ini dibuka, kartu pose yang sedang terdeteksi otomatis disorot **`ACTIVE`** (`UI_Plaque_PoseCard_Active_9Slice` + `UI_Pill_Active_Gold`).
   - Strip aturan bawah: `KEY POSE RULES` (`UI_Chip_Bar_Cream_9Slice` + `UI_Badge_Label_Amber_Blank`) + tombol penutup **`CLOSE POSE GUIDE`** (`UI_Btn_Secondary_Cream_9Slice`).
2. **Wiring Navigasi & Tombol `Escape`:**
   - Tutup modal melalui klik **`CLOSE POSE GUIDE`**, klik **`ESC · CLOSE`**, maupun penekanan tombol keyboard `Escape` (*new Input System*).
   - Perbarui `LobbyNavigationManager.cs`: **prioritaskan menutup `PoseGuideOverlayUI` lebih dulu** ketika `Escape` ditekan saat overlay sedang terbuka, baru kemudian pressing `Escape` berikutnya keluar dari tab Calibration ke Missions!

### 4. VERIFIKASI & PEMBARUAN `brain/laptop-b/`
1. Perbarui `Assets/Editor/UISceneAndPrefabBuilder.cs` (`BuildAll()`) agar perubahan `Calibration` (`1×4` + panel `860×540`) dan prefab/panel `PF_PoseGuideOverlay.prefab` bisa di-generate ulang bersih tanpa manual wiring yang rawan hilang.
2. Pastikan **`0` Compiler Errors & `0` Console Errors/Warnings** pada script baru/terubah (`CalibrationPanelUI`, `PoseGuideOverlayUI`, `LobbyNavigationManager`, `UISceneAndPrefabBuilder`).
3. Jalankan uji Play Mode di `MainMenu_Lobby.unity` (dan verifikasi singkat `Test_Dragon_Flight.unity` tetap berjalan normal):
   - Saat awal masuk Calibration: tampil 4 pose default (`08, 06, 04, 13`) tanpa terpotong.
   - Uji simulasi / live deteksi pose: Slot 1 berpindah dinamis ke pose terakhir, tidak ada duplikat kartu, maksimal tetap 4 kartu, dan `ResetRecentPosesToDefault()` mengembalikan 4 pose utama.
   - Uji klik **`OPEN POSE GUIDE`** -> modal `SCREEN 07` menampilkan 15 pose lengkap dengan deskripsi + tag `MAIN`, tidak terpotong di `1920×1080`, dan menutup bersih via **`CLOSE POSE GUIDE`**, **`ESC · CLOSE`**, serta keyboard **`Escape`** (tanpa langsung keluar dari tab Calibration).
4. Catat seluruh perubahan ini di **`brain/laptop-b/`** (`Tasks.md`, `Devlog.md`, dan perbarui `brain/laptop-b/memories/Implementasi UI Hybrid AR Dragon.md`) lalu lakukan `git -C brain add` (hanya `brain/laptop-b/`), `commit`, dan `push`.
```

---

## 5. PROMPT PENYESUAIAN UI v1.7 — BRANDING FANAMIR, QUEST OBJECTIVE PANEL & QUEST NARRATIVE BOX (LAPTOP B)

> Gunakan prompt di bawah ini di **Laptop B** untuk menerapkan pembaruan `v1.7` di atas implementasi UI Hybrid eksisting (`MainMenu_Lobby.unity` & `Test_Dragon_Flight.unity`).
> **Aset yang dikirim bersama prompt ini:**
> - **6 PNG Komponen Baru** ke `Assets/UI/Components/` (`134 -> 140` PNG total):
>   1. `01_Panels/UI_Panel_QuestObjective_9Slice.png` (`240×135`)
>   2. `01_Panels/UI_Panel_NarrativeBox_9Slice.png` (`304×200`)
>   3. `02_Banners/UI_Banner_SpeakerTag_Blank.png` (`240×42`)
>   4. `06_Icons/UI_Icon_QuestScroll_Gold.png` (`48×48`)
>   5. `06_Icons/UI_Icon_AutoPlay_Timer.png` (`48×48`) *(ikon jam auto-next untuk Settings & Narrative)*
>   6. `06_Icons/UI_Icon_PromptNext_Arrow.png` (`48×48`) *(opsional)*
> - **7 PNG Desain Referensi (`1920×1080`)** ke `docs/UI_Designs_Reference/`: `00_MainMenu_LevelSelection.png`, `01_Calibration_MotionTutorial.png`, `02_InGame_FlightHUD.png`, `05_Settings_AudioModal.png` *(kini 5 slider)*, `06_CameraControl_5VisualStates.png`, `07_PoseGuide_OverlayModal.png`, dan file baru `08_QuestNarrative_Overlay.png` *(versi auto-play tanpa Continue)*.

```markdown
Tugas: Terapkan pembaruan UI v1.7 untuk game "Fanamir: The Sky Guardian" (sebelumnya "AR Dragon: Sky Guardian") pada project Unity 6 URP di Laptop B, menambahkan Quest Objective Panel di `SCREEN 02`, pengaturan `Text Auto-Next` di `SCREEN 05`, dan Quest Narrative Text Box Auto-Play (`SCREEN 08`), tanpa merusak mekanisme terbang `Test_Dragon_Flight.unity` maupun struktur script internal (`DragonWingAR.*`).

### 1. BACA DOKUMENTASI & PASANG ASET BARU v1.7
1. Jalankan `git -C brain pull` terlebih dahulu (patuhi aturan `AGENTS.md`: dilarang mengedit `brain/laptop-a/`, log hanya di `brain/laptop-b/`).
2. Baca pembaruan terbaru `v1.7`:
   - `brain/00 - Overview.md`
   - `brain/laptop-a/rencana_desain_ui.md` (bagian `2.3`, `3.1`, & `3.1.1`)
   - `brain/laptop-a/panduan_aset_ui_unity.md` (tabel `3.1`, `3.2`, `3.6`, & perakitan `SCREEN 02`/`05`/`08`)
   - `brain/laptop-a/kontrak_integrasi_ui_dan_progress.md`
3. Salin 6 PNG baru ke `Assets/UI/Components/` dan perbarui `Assets/Editor/UISpriteSetupUtility.cs` untuk mengonfigurasi border C# `Sprite.border = new Vector4(L, B, R, T)` berikut:
   - `UI_Panel_QuestObjective_9Slice.png` (`240×135`) -> `new Vector4(24, 29, 24, 24)` (`Sliced`)
   - `UI_Panel_NarrativeBox_9Slice.png` (`304×200`) -> `new Vector4(30, 36, 30, 30)` (`Sliced`)
   - `UI_Banner_SpeakerTag_Blank.png` (`240×42`) -> `new Vector4(8, 0, 8, 0)` (`Sliced`, Horizontal 3-Slice — kunci height `42 px` atau `new Vector4(8, 10, 8, 6)` bila diskalakan vertikal)
   - `UI_Icon_QuestScroll_Gold.png`, `UI_Icon_AutoPlay_Timer.png`, & `UI_Icon_PromptNext_Arrow.png` (`48×48`) -> `Simple`, `Preserve Aspect = true`, border `Vector4.zero`.
4. Pasang 7 file referensi PNG (`00, 01, 02, 05, 06, 07, 08`) ke `docs/UI_Designs_Reference/`.

### 2. PEMBARUAN BRANDING PUBLIK (`FANAMIR: THE SKY GUARDIAN` & `Fanamir`)
1. Perbarui seluruh teks UI yang masih bertulisan `"AR DRAGON · SKY GUARDIAN"` / `"AR Dragon: Sky Guardian"` menjadi **`"FANAMIR: THE SKY GUARDIAN"`** pada Top Bar Lobby (`SCREEN 00`, `01`, `05`), header panel kanan Calibration (`"LIVE FANAMIR PREVIEW · GLIDE (T-POSE)"`), dan deskripsi pose `#01–#03` (`Dragon` -> `Fanamir`).
2. **JANGAN** mengubah namespace C# (`DragonWingAR.*`) atau nama prefab/scene yang berisiko merusak binding eksisting (`Test_Dragon_Flight.unity`).

### 3. QUEST OBJECTIVE PANEL DI `SCREEN 02` (`InGameFlightHUD.cs`)
1. Tambahkan **Quest Objective Panel** di pojok **kiri atas** HUD (`x: 60, y: 40` dari kiri-atas kanvas `1920×1080`, ukuran bodi `424×112`, di atas lencana `33%` `GlobalVitalityBar` dengan jarak vertikal aman `~128 px`) menggunakan `UI_Panel_QuestObjective_9Slice` + `UI_Socket_Medallion_38_Dark` + `UI_Icon_QuestScroll_Gold` + pill amber `QUEST OBJECTIVE` + badge status (`TRAVEL` krem / `ACTIVE TASK` hijau `#5C8D46`).
2. Sediakan API publik di `InGameFlightHUD.cs`:
   - `SetQuestObjective(string statusBadge, bool isActiveTask, string mainTitle, string hintText)`
   - Default saat `Test_Dragon_Flight` / level dimuat: state `TRAVEL` mengarahkan pemain menuju desa bertanda (*marked village*), mis. *"Fly to the marked village (Smog Valley)"* (tetap aman sebagai UI fungsional selama `VillageZoneController` belum dipasang).

### 4. PENGATURAN `TEXT AUTO-NEXT` DI `SCREEN 05` (SETTINGS)
1. Sesuaikan panel **Settings** (`PF_SettingsPanel`) agar menampung **5 baris slider (`h = 58 px`, step `65 px`)** mengacu pada `05_Settings_AudioModal.png`: `Master Volume`, `BGM Volume`, `SFX Volume`, `Turn Sensitivity`, dan baris ke-5 **`Text Auto-Next`** (ikon `UI_Icon_AutoPlay_Timer`, slider warna amber `#E0A23B`, rentang **`2.0s–10.0s`**, default **`5.0s`**).
2. Simpan nilainya ke `PlayerPrefs` (mis. key `Setting_NarrativeAutoNextSec`, default `5.0f`) saat `APPLY & CLOSE` agar langsung terbaca oleh `QuestNarrativeUI` di scene gameplay.

### 5. QUEST NARRATIVE TEXT BOX AUTO-PLAY `SCREEN 08` (`QuestNarrativeUI.cs` — Dormant Hidden Default)
1. Buat komponen & prefab **`PF_QuestNarrativeBox.prefab`** (`SetActive(false)` secara default di HUD `Test_Dragon_Flight.unity`), di-anchor **bawah-kiri/tengah** (`x: 156..1392, y: 828..1040` dari atas pada `1920×1080`, ukuran bodi `1236×212`, `0` tabrakan dengan Vitality Bar kiri maupun PIP Kamera & Telemetri kanan bawah `x >= 1440`) sesuai `08_QuestNarrative_Overlay.png`:
   - **Tanpa tombol Continue:** Karena tangan pemain sibuk melakukan gestur pose di kamera, jangan buat tombol Continue wajib klik/Space.
   - Kiri & Tengah box: `UI_Panel_NarrativeBox_9Slice` + tag nama desa/pembicara overlap atas kiri (`UI_Banner_SpeakerTag_Blank`) + pill **`LINE 1 / 3 · AUTO-PLAY`** + emblem kiri + narasi krisis desa (2–3 baris) + strip `WHAT TO DO:`.
   - Kanan box (**Auto-Play Countdown Widget**): Badge gelap `AUTO-PLAY · HANDS-FREE`, teks hitung mundur dinamis `NEXT IN X.Xs / Y.Ys`, progress bar horizontal (`Image.Type = Filled Horizontal Left`) yang terisi/berjalan otomatis, serta sub-teks *"Auto-updates Quest Objective · Speed adjustable in Settings"*.
2. Sediakan API publik `ShowNarrativeSequence(...)` & `HideNarrative()` serta `[ContextMenu("Debug/Preview Quest Narrative")]` untuk uji Editor: text box berjalan otomatis berpindah baris setiap `N` detik sesuai setting `Text Auto-Next`, lalu menutup sendiri setelah baris terakhir selesai dan langsung memanggil `InGameFlightHUD.SetQuestObjective(...)` (beralih dari `TRAVEL` -> `ACTIVE TASK`).
```

---

## 6. PROMPT PENYESUAIAN UI v1.8 — SPLASH SCREEN (`SCREEN 09`) & START SCREEN (`SCREEN 10`) (LAPTOP B)

> Gunakan prompt di bawah ini di **Laptop B** setelah pembaruan `v1.7` terpasang, untuk menambahkan **Splash Screen (`SCREEN 09`)** dan **Start Screen (`SCREEN 10` — *Press Any Button to Start*)** sebelum masuk ke **Lobby (`MainMenu_Lobby.unity`)**.
> **Zero komponen sprite baru:** Tidak perlu menambah/mengubah **`140` PNG** di `Assets/UI/Components/` maupun `UISpriteSetupUtility.cs`. Cukup kirim **`2` PNG referensi desain baru (`1920×1080`)** ke `docs/UI_Designs_Reference/`:
> 1. `09_SplashScreen.png` (`1920×1080`)
> 2. `10_StartScreen.png` (`1920×1080`)

```markdown
Tugas: Implementasikan alur boot **Splash Screen (`SCREEN 09`) -> Start Screen (`SCREEN 10`: Press Any Button to Start) -> Lobby (`MainMenu_Lobby.unity`)** pada project Unity 6 URP "Fanamir: The Sky Guardian" di Laptop B (di atas UI v1.7 yang sudah berjalan), tanpa merusak testing mekanisme langsung di `Test_Dragon_Flight.unity`.

### 1. BACA DOKUMENTASI & PASANG REFERENSI v1.8
1. Jalankan `git -C brain pull` terlebih dahulu (patuhi aturan `AGENTS.md`: dilarang mengedit `brain/laptop-a/`, log hanya di `brain/laptop-b/`).
2. Baca pembaruan terbaru `v1.8`:
   - `brain/laptop-a/rencana_desain_ui.md` (bagian `2.0`: `SCREEN 09` & `SCREEN 10`)
   - `brain/laptop-a/panduan_aset_ui_unity.md` (tabel `Designs/` + bagian `4.2` item `10–11` + `BootFlowController` pada Bab `5`)
   - `brain/laptop-a/kontrak_integrasi_ui_dan_progress.md`
3. Pasang 2 file referensi PNG baru (`1920×1080`) ke `docs/UI_Designs_Reference/`:
   - `docs/UI_Designs_Reference/09_SplashScreen.png`
   - `docs/UI_Designs_Reference/10_StartScreen.png`
4. **Catatan Aset:** Semua sprite sudah tersedia di `Assets/UI/Components/` (`140` PNG dari `v1.7`, 0 file baru): `UI_Panel_Parchment_Full_9Slice`, `UI_Plaque_SubParchment_9Slice`, `UI_Crest_Baroque_Scrollwork`, `UI_Badge_Label_Amber_Blank`, `UI_Banner_Stepped_Modal_Blank`, `UI_Tag_Stage_Gold_Blank`, `UI_HUD_Capsule_Parchment_9Slice`, `UI_Diamond_Fleuron_18`, `UI_Btn_TopChrome_Cream_9Slice`, `UI_IconBadge_CloseX_20`, dan `UI_Bg_Lobby_AerialIsland`.

### 2. ARSITEKTUR ALUR BOOT (`BootFlowController.cs` di `MainMenu_Lobby.unity`)
Implementasikan `SplashScreenPanel` dan `StartScreenPanel` di dalam `MainMenu_Lobby.unity` (Build Index `0`, berbagi `Background_AerialIsland` dengan `Lobby`) melalui komponen baru **`BootFlowController.cs`** (yang berkoordinasi dengan `LobbyNavigationManager.cs` & dirakit otomatis di `UISceneAndPrefabBuilder.cs`):
1. **Saat Game Pertama Kali Dijalankan (`SkipBootScreensOnce == false`):**
   - `SharedTopNavbar` & `TabContentRoot` Lobby tersembunyi/tertutup sementara (`alpha = 0` / nonaktif).
   - **Fase 1 — `SplashScreenPanel` (`SCREEN 09`):** Tampil full-screen (`#F2EFE7` solid opaque, bingkai `UI_Panel_Parchment_Full_9Slice`, mahkota `UI_Crest_Baroque_Scrollwork`, sub-plakat `UI_Plaque_SubParchment_9Slice`, tag `ANCIENT DEW DRAGON`, judul **`FANAMIR`**, banner `UI_Banner_Stepped_Modal_Blank` terkunci tinggi `77 px` **`THE SKY GUARDIAN`**, footer `WEBCAM EXERGAMING · BODY-FLIGHT ADVENTURE`). Jalankan fade-in `0.45s` -> hold `1.8s` -> fade-out `0.45s` via `CanvasGroup` (dapat di-skip ke Start Screen lewat klik/tombol setelah `0.5s` agar tidak melompati Start Screen), lalu otomatis mengaktifkan `StartScreenPanel`.
   - **Fase 2 — `StartScreenPanel` (`SCREEN 10`):** Memakai `Background_AerialIsland` yang sama di belakang layar, menampilkan plakat judul di langit atas (`UI_Crest_Baroque_Scrollwork`, `UI_Panel_Parchment_Full_9Slice`, `UI_Tag_Stage_Gold_Blank` `"SKY EXERGAMING AR"`, **`FANAMIR`**, `UI_Banner_Stepped_Modal_Blank` `"THE SKY GUARDIAN"`), tombol pojok kanan atas **`ESC · QUIT`**, dan kapsul **`PRESS ANY BUTTON TO START`** (`UI_HUD_Capsule_Parchment_9Slice` + sepasang `UI_Diamond_Fleuron_18`) dengan animasi *alpha/scale pulse* halus di bagian bawah.
   - **Input `Press Any Button to Start` (*New Input System*):** Gunakan `Keyboard.current.anyKey.wasPressedThisFrame` (kecuali tombol `Escape` yang langsung memanggil `QuitApplication()`), `Gamepad` button press bila ada, serta `Mouse.current.leftButton/rightButton.wasPressedThisFrame` (pastikan klik pada tombol **`ESC · QUIT`** tidak masuk ke Lobby melainkan keluar!). Saat terpicu, beri jeda transisi singkat (`~0.25s` cooldown/fade) menuju **Lobby (`SCREEN 00` — Tab `Missions`)** agar klik tidak tembus ke kartu level.
2. **Saat Kembali dari Gameplay (`Test_Dragon_Flight.unity`):**
   - Tambahkan flag statis `BootFlowController.SkipBootScreensOnce` yang diset `true` oleh `PauseModalUI` (`Back to Lobby`) & `FinishModalUI` (`ESC · LOBBY`) sebelum `SceneManager.LoadScene("MainMenu_Lobby")`, sehingga pemain yang kembali dari terbang **langsung masuk ke Tab `Missions`** tanpa mengulang Splash/Start Screen.
   - Pastikan `PythonProcessManager.autoLaunchPython` tetap `false` selama Splash & Start Screen.

### 3. VERIFIKASI & PEMBARUAN `brain/laptop-b/`
1. Pastikan **0 Compiler Errors & 0 Console Warnings** setelah kompilasi & `UISceneAndPrefabBuilder.BuildAll()`.
2. Uji Play Mode `MainMenu_Lobby.unity` (`capture_game_view` `1920×1080`):
   - Startup awal: `SplashScreenPanel` tampil rapi sesuai `09_SplashScreen.png`, lalu otomatis beralih ke `StartScreenPanel` (`10_StartScreen.png`).
   - Di `StartScreenPanel`: menekan tombol/klik di area layar langsung masuk mulus ke Lobby Tab `Missions`; menekan tombol `ESC · QUIT` / `Escape` memicu `QuitApplication()`.
   - Simulasi kembali dari `Test_Dragon_Flight.unity` (`SkipBootScreensOnce = true`): langsung membuka Lobby Tab `Missions` tanpa mengulang Splash.
   - Uji non-regresi `Test_Dragon_Flight.unity`: tetap langsung bisa di-Play di Editor tanpa error (`0` errors, `0` warnings, `0` proses Python CV tertinggal).
3. Catat hasil implementasi ke **`brain/laptop-b/`** (`Tasks.md`, `Devlog.md`, dan `memories/Implementasi UI Hybrid AR Dragon.md`), lalu `git -C brain add` (hanya `brain/laptop-b/`), `commit`, dan `push`.
```

---
**Navigasi Obsidian:** [[00 - Overview]] · [[panduan_aset_ui_unity]] · [[rencana_desain_ui]] · [[konteks]] · [[prd]] · [[Tasks]] · [[Devlog]] · [[laptop-b/Tasks]] · [[laptop-b/Devlog]]
