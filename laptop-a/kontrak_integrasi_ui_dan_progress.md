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
updated: 2026-11-14
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
## AR DRAGON: SKY GUARDIAN

> **Tujuan Dokumen:** Menjadi kontrak permanen antara desain UI (`brain/laptop-a/`) dan implementasi Unity 6 di **Laptop B** (`AR Dragon Sky Guardian/`, namespace `DragonWingAR.*`).
> **Konteks Fase Saat Ini:** Laptop B sedang berada di fase **pengujian & pematangan mekanisme kontrol terbang (`Test_Dragon_Flight.unity`)** (`PythonProcessManager.cs`, `UDPPoseReceiver.cs`, `DefaultFlightInputMapper.cs`, `DragonFlightController.cs`, `DragonWingAnimator.cs`). Fitur **pulau 3D final**, **tantangan 3 desa (`VillageZoneController`)**, **timer/level progression (`GameManager`)**, dan **sistem BGM/SFX** **belum diimplementasikan**.
> **Prinsip Utama:** Seluruh scene & komponen UI **tetap diimplementasikan dan ditata lengkap sekarang** memakai strategi ***Live + Placeholder + Dormant Hidden*** tanpa mengganggu pengujian mekanisme saat ini, serta **WAJIB disinkronkan secara otomatis setiap kali ada pembaruan/penambahan fitur gameplay di `brain/laptop-b/`**.

---

## 1. MATRIKS STATUS ELEMEN UI SAAT INI (FASE TESTING MEKANISME)

Saat mengimplementasikan aset dari `UI_Unity_Export/` (134 komponen PNG + 7 referensi layar `1920×1080`), Agent/Developer Laptop B membagi seluruh elemen UI ke dalam **3 kategori eksekusi**:

### 🟢 Kategori A — LIVE WIRED (Langsung Berfungsi Penuh Sekarang)
Fitur backend-nya **sudah tersedia dan terverifikasi** di Laptop B, sehingga UI wajib tersambung ke kode nyata:

| Layar / Komponen | Skrip & Port Backend di Laptop B | Perilaku Nyata Saat Ini |
| :--- | :--- | :--- |
| **Shared Navbar (`SCREEN 00/01/05`)** | `LobbyNavigationManager.cs` *(baru)* + `PythonProcessManager.cs` | Pindah tab `CALIBRATION ↔ MISSIONS ↔ SETTINGS`, badge status kamera dinamis pojok kanan atas (`STANDBY`, `LATAR BELAKANG`, `JENDELA PREVIEW`), tombol `ESC · QUIT` / `ESC · BACK`. |
| **CTA Stage 01 (`PLAY`) & Calibration (`READY TO FLY`)** | Scene Loader / Bootstrapper | Membuka/masuk ke scene terbang uji aktif **`Test_Dragon_Flight.unity`** sambil menjaga status kamera/sensitivitas tetap utuh. |
| **Modul Kontrol Kamera 5 State (`SCREEN 01`, `02`, `05`, `06`)** | `PythonProcessManager.cs` | Dropdown kamera DirectShow (`RefreshCameraListAsync()` / `--list-json`), popup list **100% solid opaque (`alpha = 1.0f`, `#FBF6EB`)**, tombol `Scan`, `Latar Belakang` (`--headless`), `Jendela Preview`, dan `Matikan`; refleksi 5 state (`STANDBY Belum Pilih`, `STANDBY Terpilih`, `MEMUAT YOLO`, `LIVE`, `ERROR`). Default tetap `autoLaunchPython = false`. |
| **Viewport Mirror Kamera + Skeleton + Trigger Kalibrasi (`SCREEN 01`)** | `UDPPoseReceiver.cs` (UDP `5052`, `5053`, `5054`) | `RawImage` feed PIP kamera (`5053`), panel sidebar status `calib_state_id` (`0–5`) & `calib_progress` (`0–100%`), tombol `RECALIBRATE (C)` (`TriggerCalibration()` / `CancelCalibration()`) dan `RESET DEFAULT` (`ResetCalibrationProfile()`) ke UDP `5054`. |
| **Live 3D Dragon Preview (`SCREEN 01` Kanan)** | Prefab `Dragon_Model.fbx` + `DragonWingAnimator.cs` + `RenderTexture` | Kamera terisolasi menyorot model naga 3D rigged di atas pedestal, memutar/mengepak sayap secara real-time mengikuti `UDPPoseReceiver` / state pose, plus strip telemetri `STATE`, `SPD`, `PITCH`. |
| **Grid 15 Kartu Pose — 14 Pose Lokomosi (`SCREEN 01`)** | `UDPPoseReceiver.cs` / `DragonFlightController.cs` | Seluruh 15 kartu tampil rapi; kartu `01_GroundedIdle` s.d. `14_PropellerDash` otomatis berpindah ke `UI_Plaque_PoseCard_Active_9Slice` + `UI_Pill_Active_Gold` saat pose tersebut aktif terdeteksi. |
| **In-Game Flight HUD — Telemetri, PIP & Pause (`SCREEN 02`)** | `InGameFlightHUD.cs` + `DragonFlightController.cs` + `UDPPoseReceiver.cs` | Pembacaan live Altitude (`m`), Airspeed (`m/s`), mode pose aktif (`POSE T/Y/A` / `ActiveFlightStateName`), header kontrol kamera + monitor PIP 16:9 (`5053`), serta tombol `ESC · PAUSE` (`Time.timeScale = 0f` + menu Pause ringkas / kembali ke Lobby). |
| **Slider `Turn Sensitivity` (`SCREEN 05`)** | `DefaultFlightInputMapper` / `DragonLocomotionConfig` + `PlayerPrefs` | Mengubah faktor sensitivitas belok secara persisten (`1.0×`–`3.0×`) dan menyimpannya saat `APPLY & CLOSE`. |

---

### 🟡 Kategori B — PLACEHOLDER FUNGSIONAL (Tampil Sesuai Desain, Isi Data Sementara)
Elemen terlihat di layar saat navigasi atau terbang agar bentuk UI utuh dan siap dites tampilannya, tetapi datanya bersifat dummy/konfigurasi sementara hingga sistem aslinya dibangun:

| Layar / Komponen | Kondisi Placeholder Sekarang | Kontrak Method/Slot Masa Depan (`// TODO [SYNC-GAMEPLAY]`) |
| :--- | :--- | :--- |
| **Kartu `STAGE 02 (WILDFIRE)` & `STAGE 03 (DROUGHT)` (`SCREEN 00`)** | Tampilkan seluruh visual (pita stage, biome thumbnail, bintang contoh, gembok `UI_Scrim_LockedCard_Overlay`, tag speech-bubble `90s`/`150s`). Tombol `SELECT` Stage 02 sementara memuat `Test_Dragon_Flight.unity` (atau menampilkan banner info mode uji), sementara Stage 03 terkunci/placeholder (`ShowLockHint()`). | Hubungkan ke `StageProgressData` / `GameManager` saat scene level & sistem bintang ditambahkan. |
| **Slider `Master`, `BGM`, `SFX Volume` (`SCREEN 05`)** | Slider, nilai teks `%`, dan tombol `APPLY & CLOSE` berfungsi penuh serta disimpan di `PlayerPrefs` (`Master` dapat langsung mengatur `AudioListener.volume`), sedangkan `BGM` & `SFX` disiapkan slot `AudioMixer` / event publik tanpa wajib punya file audio dulu. | Sambungkan langsung ke `AudioManager` / `AudioMixer` pada Sesi Audio. |
| **Kapsul Tengah Atas HUD (`Timer & VILLAGES 0/3`, `SCREEN 02`)** | Tampil sebagai kapsul rapi bertulisan placeholder (mis. `--:-- · TEST FLIGHT (0/3)`). Tidak memicu timeout/kalah selama fase testing mekanisme. | Method publik: `SetMissionTimer(float remainingSeconds)` & `SetVillageCount(int cleared, int total)`. |
| **Global Vitality Bar Vertikal Kiri (`SCREEN 02`)** | Tampil lengkap (`Track + Fill + Ticks + Badge 0%`) dengan `fillAmount = 0f` statis sementara. | Method publik: `SetGlobalVitality(float normalized01)`. |
| **Kartu Pose ke-15 (`UI_Pose_15_VillageActions`, `SCREEN 01`)** | Kartu panduan gestur 3 desa tampil statis sebagai edukasi awal; highlight aktif otomatis belum tersambung ke logika skor desa. | Aktifkan highlight otomatis saat gestur aksi desa terdeteksi di dalam zona tantangan / uji kalibrasi desa. |

---

### ⚪ Kategori C — DORMANT / HIDDEN PREFAB (`SetActive(false)` Secara Default)
Komponen ini **tidak akan muncul otomatis selama fase testing mekanisme** karena trigger gameplay-nya (`VillageZoneController`, `GameManager`) memang belum dibuat. Namun **WAJIB tetap dirakit lengkap menjadi Prefab & dipasang nonaktif di Canvas** agar arsitektur UI komplit sejak sekarang:

| Layar / Komponen | Status Default Saat Play Mode | Method Publik Siap Pakai & Opsi Test QA |
| :--- | :--- | :--- |
| **Waypoint Compass Tengah (`UI_HUD_Waypoint_Chevron`, `SCREEN 02`)** | `SetActive(false)` ketika target desa `== null`. | `SetWaypointTarget(Transform target, string villageLabel)` / `ClearWaypoint()`. |
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
| **2. Tantangan Desa (`VillageZoneController.cs` & VFX 3 Desa)** | 1. Hubungkan trigger masuk/keluar desa ke `VillageChallengeUI.ShowChallenge(...)`, `UpdateProgress(...)`, & `HideChallenge()`.<br>2. Hubungkan arah Pad Desa aktif ke `UpdateWaypoint(...)` agar `UI_HUD_Waypoint_Chevron` aktif menunjuk desa terdekat.<br>3. Sambungkan progres total desa (`0/3 -> 3/3`) ke `SetVillageCount(...)` & `SetGlobalVitality(...)` di HUD `SCREEN 02`.<br>4. Aktifkan highlight kartu **`15_VillageActions`** bila gestur aksi desa dipraktikkan.<br>5. **Jika ada desa/gestur yang ditambah, dihapus, atau berubah aturan/teksnya,** perbarui teks TMP/ikon pada `SCREEN 03`, deskripsi di `SCREEN 01`, dan pill `LIMIT ...` di `SCREEN 00`. |
| **3. Sistem Misi, Timer, Bintang & Progression (`GameManager.cs`)** | 1. Hubungkan hitungan mundur asli (`90s` / `150s` / `210s`) ke `SetMissionTimer(...)` di HUD `SCREEN 02`.<br>2. Hubungkan event menang / waktu habis ke `FinishModalUI.ShowVictory(...)` & `FinishModalUI.ShowDefeat(...)` (`SCREEN 04`), pastikan `Time.timeScale` & input/kamera ditangani bersih saat modal tampil.<br>3. Hubungkan status unlock & bintang (`PlayerPrefs` / save profile) ke **`Panel_Missions` (`SCREEN 00`)** untuk men-toggle `UI_Scrim_LockedCard_Overlay`, status kunci `STAGE 02/03`, serta bintang emas/kosong. |
| **4. Mekanik Terbang / Daftar Pose Baru atau Diubah** | Perbarui grid kartu pose di `Panel_Calibration` (`SCREEN 01`), warna pill status pose di dock telemetri `SCREEN 02`, dan binding state highlight pada UI controller agar tetap 1:1 dengan `DragonFlightController` & `motion_recognizer.py`. |
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
**Navigasi Obsidian:** [[00 - Overview]] · [[panduan_aset_ui_unity]] · [[rencana_desain_ui]] · [[konteks]] · [[prd]] · [[Tasks]] · [[Devlog]] · [[laptop-b/Tasks]] · [[laptop-b/Devlog]]
