---
title: Master Prompt Eksekusi Loading Screen, UI Event Desa Minimalis, & Animasi "Juicy UI" (Laptop B)
tags:
  - handoff
  - prompt
  - laptop-b
  - ui-ux
  - loading-screen
  - ui-animation
  - village-hud
owner: Laptop A
updated: 2026-11-26
status: ready-for-execution
---

# 🤖 Master Prompt Eksekusi: Loading Screen (`SCREEN 11`), UI Event Desa Minimalis (`SCREEN 03B/03C`), & Animasi "Juicy UI" (Laptop B)

> **Petunjuk untuk Pengguna:**  
> Salin seluruh blok prompt di bawah ini dan kirimkan ke AI Agent di **Laptop B** (pastikan folder atau ZIP `Export_UI_Animasi_Loading_Desa_LaptopB` sudah berada di `assets/` Laptop B).

```markdown
Tugas: Impor paket aset baru dari `assets/Export_UI_Animasi_Loading_Desa_LaptopB/`, bangun **Loading Screen Antar-Scene (`SCREEN 11`)** lengkap dengan animasi siluet naga mengambang naik-turun & dynamic trail, rombak **UI Event Desa (`PF_VillageChallengeOverlay`)** menjadi versi minimalis **`416 × 520 px`** dengan 3 ikon emblem custom baru, serta implementasikan seluruh **Animasi "Juicy UI" (Pilar 2)** di Unity 6.

---

### 1. PERSIAPAN & IMPOR ASET (`Export_UI_Animasi_Loading_Desa_LaptopB/`)
1. Jalankan `git -C brain pull`. Baca `brain/laptop-a/gameplay-mechanics/planning_post_animasi_vfx_ui_dan_kontrol.md` (Pilar 2: Animasi UI) dan `assets/Export_UI_Animasi_Loading_Desa_LaptopB/README_EXPORT_UI.md`.
2. Salin seluruh aset ke folder Unity (`AR Dragon Sky Guardian/`):
   - `Components/08_LoadingScreen/*.png` -> `Assets/UI/Components/08_LoadingScreen/`
   - `Components/05_Challenge_Minimalist/*.png` -> `Assets/UI/Components/05_Challenge_Minimalist/`
   - `Designs/*.png` -> `docs/UI_Designs_Reference/`
3. Perbarui `UISpriteSetupUtility.cs` untuk mendaftarkan ke-16 sprite baru beserta 9-slice border sesuai `README_EXPORT_UI.md` (`MeshType = FullRect`, `FilterMode = Bilinear`, `SpriteMode = Single`), lalu jalankan `Assets/Refresh` dan re-setup sprite.

---

### 2. LANGKAH 1 — LOADING SCREEN ANTAR-SCENE (`SCREEN 11`)
Buat **`LoadingScreenManager.cs`** (`DontDestroyOnLoad` singleton) dan prefab **`PF_LoadingScreenOverlay.prefab`** mengacu referensi `11_LoadingScreen.png`:
1. **Visual & Tata Letak (`1920×1080` Canvas Overlay SortingOrder 999):**
   - **Background Senja Twilight:** Gradasi langit senja (`#0E1A2E -> #1B4358 -> #B56B3F -> #241C18`) dipadukan gambar pulau `UI_Bg_Lobby_AerialIsland` (`opacity ~0.32`) dan vignette gelap lembut di bagian bawah.
   - **Header Atas:** `UI_Loading_Crest_Gold` + teks `"FANAMIR · THE SKY GUARDIAN"`.
   - **Hero Naga & Dynamic Trail (Area Tengah Atas):**
     - Pasang `UI_Loading_Dragon_Silhouette` di tengah atas layar.
     - **Animasi Wajib:** Naga bergerak naik-turun secara kontinu dan halus (`Mathf.Sin(Time.unscaledTime * 2.2f) * 14f` px) disertai sedikit anggukan rotasi Z (`±4°`).
     - **Dynamic Trail:** Pasang pita trail dinamis (`LineRenderer` / `TrailRenderer` UI atau scrolling wave ribbon bertinta cyan-ke-emas) yang ujung kanannya menempel langsung pada ekor siluet naga dan meliuk bergelombang mengikuti gerakan naik-turun naga secara *real-time*, ditambah beberapa `UI_Loading_Sparkle_Star` berkedip halus.
   - **Kartu Tip Perkamen (`TipCard_Container`):**
     - Menggunakan `UI_Loading_TipCard_Base` + `UI_Loading_Tag_Amber` (`"TAHUKAH KAMU?"`), ikon pose acak dari `06_Poses/` / `05_Challenge_Minimalist/`, dan teks tips berganti acak setiap loading (mis. tips `#14 Propeller Dash Boost 150 m/s`, `#04 Takeoff 3 kepakan`, `Glide-to-Walk mendarat mulus`, `Escape 3x kepak / Tuck Dive Zephyria`).
   - **Bottom Loading Cluster:**
     - `UI_Loading_Fleuron_Gold` + status dinamis (*"MENYUSURI ALIRAN ANGIN KEPULAUAN..."* saat masuk pulau / *"KEMBALI KE BALAI TETUA..."* saat ke lobby), persentase `0%–100%` (`#E5AD44`), `UI_Loading_Track_Outer`, `UI_Loading_Progress_Fill` (`Filled Horizontal`), dan `UI_Loading_Tip_Star` yang menempel tepat di ujung kemajuan bar.
2. **Integrasi Transisi Scene:**
   - Ganti pemanggilan `SceneManager.LoadScene(...)` pada perpindahan antara `MainMenu_Lobby`, `MainIsland_Gameplay`, dan `Test_Dragon_Flight` (`MissionsPanelUI`, `CalibrationPanelUI`, `PauseModalUI`, `FinishModalUI`) menjadi `LoadingScreenManager.Instance.LoadSceneAsync(sceneName)` (dengan Fade-In `0.30s` -> Async Load + Smooth Bar `0.9 -> 1.0` -> Hold `0.25s` -> Fade-Out `0.35s`, memakai `Time.unscaledDeltaTime` agar aman saat `Time.timeScale == 0`).

---

### 3. LANGKAH 2 — ROMBAK UI EVENT DESA MINIMALIS (`PF_VillageChallengeOverlay.prefab` & `VillageChallengeUI.cs`)
Mengacu desain final **`03B_SideDocked_ChallengeHUD_Improved.png`** & **`03C_SideDocked_3Biome_Showcase.png`**:
1. **Ukuran & Tata Letak Minimalis (`416 × 520 px`, *Middle-Right* `(-32, 0)`):**
   - Hapus seluruh kotak bertumpuk (*HeroCard* dalam kartu, bingkai kotak di sekitar ikon, subjudul krisis, tag `NO-DECAY`, dan kotak *footer keycap* lama) — sisakan **hanya 4 zona bersih** di atas `UI_Panel_Challenge_Minimalist_Frame`:
     1. **Judul Desa:** `DESA ZEPHYRIA` / `DESA PYREWOOD` / `DESA SOLARIA` (`Fredoka Bold 21pt`, `#231E18`) + 1 garis pemisah tipis (`#B8AE9E`).
     2. **Custom Emblem + Perintah + Status:**
        - Pasang 3 ikon emblem custom baru sesuai desa:
          - Zephyria -> `UI_Emblem_Zephyria_WingFlap`
          - Pyrewood -> `UI_Emblem_Pyrewood_WaterBreath` *(khusus sub-tahap Danau gunakan emblem air/siphon yang pas atau ikon pose)*
          - Solaria -> `UI_Emblem_Solaria_DewSummon`
        - Perintah singkat 1 baris (`KEPAKKAN SAYAP` / `SEMBURKAN AIR` / `ANGKAT TANGAN "11"`) + sub-instruksi 1 baris (`Ayunkan lengan (#06)` / `Buka mulut lebar (#15B)` / `Tahan lurus ke atas (#15C)`).
        - Badge Status Tabletop Otentik (titik & teks **rata tengah terpadu**):
          - Terdeteksi: `UI_Badge_Active_Green` (`#5C8D46`, teks `#FFFDF5` `"●  GERAKAN TERDETEKSI!"`).
          - Menunggu: `UI_Badge_Waiting_Amber` (`#DF9E38`, teks `#231E18` `"○  MENUNGGU GERAKAN..."`).
     3. **Progres Bar Minimalis:** Label kiri (`PEMULIHAN DESA` / `PEMADAMAN API`) + `%` besar di kanan (`66%`, `46%`, `47%`) + track bar (`336×26 px`).
     4. **Escape Hint Ringan:** Teks bersih tanpa kotak di paling bawah:
        - Zephyria: `"Tuck Dive (#13) untuk keluar"`
        - Pyrewood & Solaria: `"Kepak sayap 3x untuk keluar"`
   - Pastikan **ZERO text/icon clipping** di seluruh resolusi `1920×1080`!

---

### 4. LANGKAH 3 — IMPLEMENTASI ANIMASI "JUICY UI" (PILAR 2)
Tambahkan script helper **`UIAnimationUtils.cs`** (atau coroutine/tweening ringan berbasis `unscaledDeltaTime`, tanpa dependensi plugin eksternal berbayar jika DOTween belum terpasang):
1. **Side-Docked Challenge HUD (`VillageChallengeUI.cs`):**
   - **Intro:** Meluncur elastis dari kanan luar layar (`x: 460 -> -32`) dalam `0.35s` kurva `EaseOutBack`.
   - **Outro:** Meluncur ke kanan (`x: -32 -> 460`) dalam `0.20s` kurva `EaseInQuad`.
   - **Kartu Status Pose:** Saat berubah ke *Detected*, jalankan efek *punch scale* (`1.0 -> 1.08 -> 1.0` dalam `0.18s`).
2. **Dual-Layer "Trailing Ghost Bar" (`VillageChallengeUI.cs` & `InGameFlightHUD.cs`):**
   - Tambahkan lapisan `GhostFillImage` (`#FFFDF5` alpha `~0.35`) di belakang bar pengisi utama (`ActiveFillImage`).
   - Bar utama melesat cepat (`SmoothDamp 0.08s`), sedangkan *Ghost Bar* menyusul lambat (`SmoothDamp 0.35s`) sehingga pemain melihat jejak dorongan setiap kepakan.
3. **Quest Narrative Box (`QuestNarrativeUI.cs`):**
   - **Parchment Unroll:** Set pivot Y ke `0` (bawah); saat muncul animasikan `localScale.y` dari `0 -> 1.05 -> 1.0` (`0.28s`, `EaseOutBack`), diikuti fade-in teks dialog.
   - Tambahkan efek *shimmer sweep* berjalan pada countdown bar auto-play.
4. **Finish Modal Selebrasi (`FinishModalUI.cs`):**
   - **Modal Drop-in:** Panel perkamen turun dari atas layar (`y: +400 -> 0`) dalam `0.45s` `EaseOutBounce`.
   - **Sequential Star Pop-in:** Bintang 1 (`0.55s`), Bintang 2 (`0.95s` jika $\ge 66.7\%$), Bintang 3 (`1.35s` jika $100\%$) muncul dengan *scale punch* `0 -> 1.4 -> 1.0` dibarengi SFX bintang dan letupan partikel/sparkle emas di UI canvas saat bintang ke-3 terbuka!
5. **Micro-Interactions Tombol & Denied Shake (`MissionsPanelUI.cs`, `CalibrationPanelUI.cs`, `LobbyNavigationManager.cs`):**
   - Tambahkan efek *hover scale* (`1.04x`) dan *press squish* (`0.96x`) pada tombol-tombol utama Lobby & Modal.
   - Jika pemain menekan `PLAY` saat kamera belum kalibrasi, jalankan *horizontal shake* (`±10 px`, `0.3s`) pada banner peringatan kalibrasi.

---

### 5. VERIFIKASI PLAY MODE & PENUTUP
1. Jalankan Play Mode di `MainMenu_Lobby.unity` -> klik `PLAY` -> pastikan **`SCREEN 11` Loading Screen** muncul mulus dengan naga naik-turun + dynamic trail + tips card, lalu masuk ke `MainIsland_Gameplay.unity`.
2. Uji seluruh 3 desa (`Zephyria`, `Pyrewood`, `Solaria`) dan pastikan **UI Event Desa Minimalis (`416×520`)** tampil tajam dengan 3 emblem custom baru, zero clipping, animasi slide-in `EaseOutBack`, trailing ghost bar, unroll narasi, dan selebrasi bintang Finish Modal (`0` console errors/warnings).
3. Ambil tangkapan layar verifikasi mandiri ke `docs/figures/raw_screenshots/` (`SS_UI_LoadingScreen.png` dan `SS_UI_Minimalist_VillageHUD_3Biomes.png`), lalu kembalikan scene aktif ke `MainMenu_Lobby.unity`.
4. Perbarui catatan memori di `brain/laptop-b/` (`Devlog.md`, `Tasks.md`, & memory file baru), lalu lakukan git commit & push di folder `brain/`.
```
