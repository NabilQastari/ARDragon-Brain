---
title: Prompt Eksekusi Sesi 5 Dasar — Game Loop, Timer Countdown, & Sistem Penilaian 3 Bintang (Laptop B)
tags:
  - prompt
  - laptop-b
  - sesi-5
  - game-loop
  - scoring
  - star-system
  - unity
owner: Laptop A
updated: 2026-11-21
status: ready-for-execution
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[arsitektur_sesi_5_gameloop_dan_scoring]]"
  - "[[mekanisme_tantangan_desa]]"
  - "[[kontrak_integrasi_ui_dan_progress]]"
---

# 🚀 Prompt Eksekusi Sesi 5 Dasar: Game Loop, Timer, & Skor 3 Bintang untuk Laptop B

> **Petunjuk untuk Pengguna:** Salin (*copy*) seluruh isi kotak **"MASTER PROMPT EKSEKUSI SESI 5 DASAR UNTUK LAPTOP B"** di bagian akhir dokumen ini, lalu kirimkan langsung ke AI Agent di **Laptop B** setelah menjalankan `git -C brain pull`.

---

## 🧭 Ikhtisar Arsitektur Sesi 5 Dasar (Laptop B)

Sesi 4B (Paket Visual VFX, Dynamic FOV 85°, & Skybox Dinamis) telah selesai 100%. Di patch sebelumnya, Laptop B telah meletakkan fondasi awal pemisahan stage dan `PF_FinishModal`. 

Tugas di **Sesi 5 Dasar** ini adalah menyempurnakan dan membakukan **aturan penilaian bintang resmi berbasis Progres Global**, siklus *Game Lifecycle State Machine* (termasuk countdown awal 3 detik), sistem pembukaan gembok level (*Level Unlocking*), dan penyimpanan persisten di `PlayerPrefs`.

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│                       SISTEM GAME LOOP & SKOR SESI 5                        │
├──────────────────────────┬──────────────────────────┬───────────────────────┤
│ 1. FORMULA BINTANG       │ 2. TIMER COUNTDOWN       │ 3. PROGRESI GEMBOK    │
│ • Universal Global Prog: │ • Stage 1: 120s          │ • Syarat Buka Level:  │
│   33.4% = 1 Bintang ⭐   │ • Stage 2: 240s          │   Minimal 1 Bintang ⭐│
│   66.7% = 2 Bintang ⭐⭐ │ • Stage 3: 360s          │ • Stage 01 -> 02 -> 03│
│   100%  = 3 Bintang ⭐⭐⭐│ • Freeze saat narasi lore│ • Stage 03 -> FreePlay│
│ • Stage 2: 1 desa = 50%  │ • Waktu habis: hitung    │ • Persistensi di      │
│   (Tetap 1 Bintang!)     │   bintang progres akhir  │   PlayerPrefs         │
└──────────────────────────┴──────────────────────────┴───────────────────────┘
```

---

## 📋 Rincian 4 Tahapan Eksekusi di Laptop B

### Tahap 1: Pembakuan Formula Bintang Berbasis Progres Global (`GameManager.cs` / `DragonGameSettings.cs`)
* Rumus kalkulasi bintang:
  ```csharp
  public static int CalculateStars(float globalVitalityNormalized)
  {
      if (globalVitalityNormalized >= 1.000f) return 3; // 100%
      if (globalVitalityNormalized >= 0.667f) return 2; // >= 66.7%
      if (globalVitalityNormalized >= 0.334f) return 1; // >= 33.4%
      return 0; // Gagal / < 33.4%
  }
  ```
* **Aturan Stage 2 (2 Desa, 50% per Desa):**
  * Selesai 1 Desa $\rightarrow$ Global Vitality $50\%$. Karena $50\% < 66.7\%$, pemain mendapatkan **1 Bintang** ⭐.
  * Masuk desa kedua hingga progres mencapai minimal $66.7\%$ $\rightarrow$ naik menjadi **2 Bintang** ⭐⭐.
  * Kedua desa pulih $100\%$ $\rightarrow$ **3 Bintang** ⭐⭐⭐.

---

### Tahap 2: Countdown Awal 3 Detik (*CountdownStart State*) & Penanganan Waktu Habis
1. **Countdown Start (3.. 2.. 1.. TERBANG!):**
   * Saat scene `MainIsland_Gameplay.unity` dimuat, tampilkan overlay countdown teks singkat (3.0 detik). Naga melayang tenang di Center Peak spawn point `(0, 2.5, 27.5)`.
   * Setelah countdown selesai, input kemudi dan timer misi aktif penuh.
2. **Aturan Waktu Habis (`TimeUp`):**
   * Jika timer countdown mencapai `00:00`:
     * Naga melambat halus ke mode hover.
     * Evaluasi bintang dihitung dari **persentase Progres Global terakhir yang dicapai pemain**.
     * Jika pemain berhasil meraih $\ge 33.4\%$ ($\ge 1$ Bintang), statusnya tetap **BERHASIL LULUS** dengan perolehan bintang tersebut, dan gembok level berikutnya tetap terbuka!
     * Jika pemain gagal mencapai $33.4\%$ ($0$ Bintang), tampilkan status **WAKTU HABIS / COBA LAGI**.

---

### Tahap 3: Progresi Pembukaan Level & Persistensi `PlayerPrefs`
1. **Kunci Pembukaan Level:**
   * `Fanamir_Stage01_Unlocked = 1` (selalu terbuka).
   * `Fanamir_Stage02_Unlocked = (Stage01_BestStars >= 1) ? 1 : 0`.
   * `Fanamir_Stage03_Unlocked = (Stage02_BestStars >= 1) ? 1 : 0`.
   * `Fanamir_FreePlay_Unlocked = (Stage03_BestStars >= 1) ? 1 : 0`.
2. **Sinkronisasi Kartu Lobby (`MissionsPanelUI.cs`):**
   * Nonaktifkan testing mode `UnlockAllStagesForTesting` pada mode permainan normal (namun tetap sediakan toggle inspeksi jika diperlukan).
   * Kartu level menampilkan jumlah bintang emas yang sudah diraih (`UI_Icon_Star_Gold`) dan waktu tercepat.
   * Tombol stage terkunci menampilkan scrim gembok dengan tombol charcoal `LOCKED`; stage terbuka menampilkan tombol hijau `PLAY`.

---

### Tahap 4: Penyempurnaan `FinishModalUI.cs` (`SCREEN 04`)
* Tampilkan animasi sekuensial bintang 1..3 sesuai bintang yang diperoleh.
* Tampilkan ringkasan: Waktu bermain (`MM:SS`), Desa Pulih (`X / N Desa`), dan Persentase Vitalitas.
* Tombol **`LEVEL BERIKUTNYA`**:
  * Aktif jika bintang diraih $\ge 1$ dan stage aktif $< 3$.
  * Di Stage 3 (Tamat), ganti atau sembunyikan menjadi tombol **`FREE PLAY MODE`** atau arahkan kembali ke Lobby.
* Tombol **`COBA LAGI`**: Me-restart stage saat ini.
* Tombol **`KEMBALI KE LOBBY`**: Memuat `MainMenu_Lobby.unity`.

---

## 📋 MASTER PROMPT EKSEKUSI SESI 5 DASAR UNTUK LAPTOP B (SIAP SALIN)

```markdown
Halo Laptop B! Sesi 4B (VFX Effekseer, Dynamic FOV 85°, & Skybox Dinamis) telah sukses 100%.
Sekarang jalankan eksekusi Sesi 5 Dasar: Pembakuan Game Loop, Timer Countdown, Sistem Penilaian 3 Bintang, & Progresi Level mengacu pada dokumen brain:
- `brain/laptop-a/arsitektur_sesi_5_gameloop_dan_scoring.md`
- `brain/laptop-a/mekanisme_tantangan_desa.md`
- `brain/laptop-a/panduan_screenshot_mandiri_dan_laporan.md`

Berikut instruksi teknis yang harus dikerjakan:

### 1. Pembakuan Formula Bintang Berbasis Progres Global (`Global Vitality`):
Perbarui logika penilaian bintang di `DragonGameSettings.cs` / `VillageZoneController.cs`:
- Bintang dihitung MURNI dari Progres Global Vitalitas (bukan sisa waktu):
  - Global Vitality >= 1.000f (100%) -> 3 Bintang ⭐⭐⭐
  - Global Vitality >= 0.667f (66.7%) -> 2 Bintang ⭐⭐
  - Global Vitality >= 0.334f (33.4%) -> 1 Bintang ⭐
  - Global Vitality < 0.334f -> 0 Bintang (Gagal)
- Perhatikan Stage 2 (2 Desa, 50% per Desa): 1 desa pulih (50%) = tetap 1 Bintang karena belum mencapai 66.7%. Baru naik 2 bintang bila desa kedua terisi minimal 16.7% (total 66.7%).

### 2. Siklus Hidup Game (State Machine) & Countdown Awal 3 Detik:
- Tambahkan fase awal singkat `CountdownStart` (3.. 2.. 1.. TERBANG!) saat `MainIsland_Gameplay.unity` dimulai sebelum timer dan kontrol penerbangan penuh diaktifkan.
- Pastikan Timer Misi (Stage 1: 120s, Stage 2: 240s, Stage 3: 360s) otomatis PAUSE saat dialog narasi `PF_QuestNarrativeBox` aktif, dan RESUME saat dialog selesai.
- Saat waktu habis (TimeUp 00:00):
  - Hentikan kontrol secara aman (naga melambat ke auto-hover).
  - Evaluasi bintang dari progres global terakhir. Jika pemain meraih >= 1 Bintang (>= 33.4%), pemain tetap dinyatakan LULUS dengan rating bintang tersebut dan berhak membuka stage berikutnya! Tampilkan FinishModal dengan opsi lanjut.
  - Jika < 33.4% (0 Bintang), tampilkan status WAKTU HABIS dengan tombol Coba Lagi.

### 3. Progresi Pembukaan Level & Persistensi `PlayerPrefs`:
- Syarat membuka gembok level berikutnya adalah MINIMAL 1 BINTANG (>= 33.4%).
- Implementasikan pembukaan sekuensial di `DragonGameSettings.cs`:
  - Stage 1 selesai (>= 1 ⭐) -> Buka Stage 2
  - Stage 2 selesai (>= 1 ⭐) -> Buka Stage 3
  - Stage 3 selesai (>= 1 ⭐) -> Buka Mode Free Play (Tamat!)
- Simpan bintang tertinggi (`Fanamir_Stage0X_BestStars`) dan status buka level di `PlayerPrefs`.
- Hubungkan ke `MissionsPanelUI.cs` di Lobby agar kartu level menampilkan medali bintang yang telah diperoleh serta memperbarui tombol LOCKED / PLAY.

### 4. Penyempurnaan `FinishModalUI.cs` (`SCREEN 04`):
- Pastikan animasi bintang terisi menyala berurutan (1..3) sesuai bintang yang didapat.
- Tampilkan statistik: Waktu tempuh (MM:SS), Desa Pulih (X / N), dan Persentase Vitalitas.
- Tombol `LEVEL BERIKUTNYA` aktif jika bintang >= 1 dan bukan Stage 3. Di Stage 3, berikan tombol untuk membuka atau masuk ke Free Play Mode / Lobby.
- Tombol `COBA LAGI` me-restart stage, dan tombol `KEMBALI KE LOBBY` kembali ke Lobby.

### 5. Pengujian Play Mode & Screenshot:
- Buat dan jalankan skrip verifikasi otomatis Play Mode (`Session5GameLoopVerificationRunner.cs`) untuk menguji:
  1. Transisi countdown awal 3s -> Playing.
  2. Jeda timer saat narasi aktif.
  3. Perhitungan bintang Stage 1, Stage 2 (1 desa = 50% = 1 bintang), dan Stage 3.
  4. Pembukaan level berikutnya saat minimal 1 bintang diraih.
  5. Kondisi waktu habis saat progres >= 33.4% tetap lulus dan membuka level berikutnya.
- Ambil tangkapan layar mandiri Finish Modal (`SS_Sesi5_FinishModal_Victory.png` dan `SS_Sesi5_Lobby_StageProgress.png`) ke `docs/figures/raw_screenshots/`.
- Perbarui `brain/laptop-b/Devlog.md` dan `Tasks.md`, lalu commit dan push ke origin main.
```
