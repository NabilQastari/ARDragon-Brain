---
title: Arsitektur Sesi 5 — Game Loop, Timer Countdown, & Sistem Penilaian 3 Bintang
tags:
  - game-loop
  - scoring
  - star-system
  - sesi-5
  - architecture
  - laptop-a
owner: Laptop A
updated: 2026-11-21
status: approved
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[mekanisme_tantangan_desa]]"
  - "[[kontrak_integrasi_ui_dan_progress]]"
  - "[[rencana_desain_ui]]"
---

# 🏆 Arsitektur Sesi 5: Game Loop, Timer Countdown, & Sistem Penilaian 3 Bintang

> **Tujuan Dokumen:** Menjadi cetak biru arsitektur resmi bagi pengontrol permainan terpadu (**`GameManager.cs`**) di Unity 6. Dokumen ini mendefinisikan siklus hidup permainan (*Game Lifecycle State Machine*), sistem evaluasi bintang berbasis progres global ($33.4\%, 66.7\%, 100\%$), aturan jeda waktu saat narasi, integrasi layar akhir (`PF_FinishModal`), dan sistem pembukaan level berjenjang (*Level Unlocking*).

---

## 🧭 1. Siklus Hidup Permainan (*Game Lifecycle State Machine*)

Pengontrol utama permainan (`GameManager.cs`) berjalan menggunakan *State Machine* berikut:

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│                       GAME LIFECYCLE STATE MACHINE                          │
└─────────────────────────────────────────────────────────────────────────────┘
                                       │
                                       ▼
                             ┌───────────────────┐
                             │    [LevelInit]    │ ◄── Inisialisasi Scene, Posisi Naga di Center Peak,
                             └───────────────────┘     Setup Silinder Desa sesuai Stage Aktif
                                       │
                                       ▼
                             ┌───────────────────┐
                             │  [CountdownStart] │ ◄── Layar Beku 3.0 detik: "3... 2... 1... TERBANG!"
                             └───────────────────┘     (Input Kemudi Diaktifkan)
                                       │
                                       ▼
        ┌─────────────────────────────────────────────────────────────┐
        │                          [Playing]                          │
        │ • Timer Countdown berjalan mundur                           │
        │ • Pause otomatis saat dialog narasi aktif                   │
        │ • Pemain bebas menjelajah & memulihkan desa                 │
        └─────────────────────────────────────────────────────────────┘
                       │                               │
           (Waktu Habis: 00:00)              (Semua Desa Pulih: 100%)
                       │                               │
                       ▼                               ▼
             ┌───────────────────┐           ┌───────────────────┐
             │      [TimeUp]     │           │     [Victory]     │
             │ Naga melambat     │           │ Auto-Hover        │
             │ halus ke hover    │           │ Selebrasi 2.5s    │
             └───────────────────┘           └───────────────────┘
                       │                               │
                       └───────────────┬───────────────┘
                                       │
                                       ▼
                             ┌───────────────────┐
                             │   [FinishModal]   │ ◄── Buka PF_FinishModal (SCREEN 04):
                             └───────────────────┘     Tampilkan 0..3 Bintang, Waktu, Tombol Next/Retry
```

---

## ⏱️ 2. Konfigurasi Timer Countdown per Stage

Waktu bermain disesuaikan dengan skala kepulauan $2400 \times 2400\text{ m}$ dan jarak tempuh naga:

| Stage | Misi / Desa yang Terlibat | Batas Waktu Maksimal | Perilaku Timer |
| :---: | :--- | :---: | :--- |
| **Stage 01** *(Tutorial)* | Desa Zephyria (1 Desa) | **120 Detik** *(2 Menit)* | Jarak tempuh dekat (~580m) + 8–10 kepakan sayap. |
| **Stage 02** *(Medium)* | Desa Zephyria + Desa Pyrewood (2 Desa) | **240 Detik** *(4 Menit)* | Termasuk penerbangan isi air di Danau Zephyria. Bebas urutan. |
| **Stage 03** *(Final)* | Seluruh 3 Desa (Zephyria, Pyrewood, Solaria) | **360 Detik** *(6 Menit)* | Misi pemulihan seluruh kepulauan. |
| **Free Play** *(Bonus)* | 3 Desa Aktif Bebas | **Tanpa Batas Waktu** *(∞)* | Eksplorasi santai tanpa tekanan waktu (terbuka pasca Stage 03). |

### ⏸️ Aturan Jeda Timer Saat Narasi (*Narrative Time-Freeze*):
* Saat pemain memasuki silinder narasi ($R = 35\text{ m}$) dan dialog tetua desa (`PF_QuestNarrativeBox`, 3 baris auto-play) aktif:
  * **Timer Countdown OTOMATIS BERHENTI (*PAUSE*)**.
  * Hal ini menjamin pemain dapat membaca cerita/lore dengan tenang tanpa terburu-buru.
* Begitu dialog selesai ditutup atau pemain keluar silinder:
  * **Timer KEMBALI BERJALAN (*RESUME*)**.

---

## ⭐ 3. Formula Sistem Bintang Berbasis Progres Global (`Global Vitality`)

Bintang diberikan berdasarkan **persentase akumulasi pemulihan kepulauan**, bukan kecepatan waktu:

$$\text{Global Vitality (\%)} = \sum_{i=1}^{N} \left( \frac{\text{Progres Desa}_i}{N} \right)$$

Di mana $N$ adalah jumlah desa aktif pada stage tersebut.

### 📐 Ambang Batas Bintang Universal:
* 🥇 **$100.0\%$ Progres Global** $\rightarrow$ ⭐⭐⭐ **3 Bintang (Gold Guardian — Pemulihan Sempurna)**
* 🥈 **$\ge 66.7\%$ Progres Global** $\rightarrow$ ⭐⭐ **2 Bintang (Silver Guardian)**
* 🥉 **$\ge 33.4\%$ Progres Global** $\rightarrow$ ⭐ **1 Bintang (Bronze Guardian — Syarat Buka Level Berikutnya)**
* ❌ **$< 33.4\%$ Progres Global** $\rightarrow$ **0 Bintang (Gagal / Belum Mencapai Ambang Minimum)**

---

### 🗺️ Penerapan Logika Bintang di Setiap Stage:

#### A. Stage 03 (3 Desa: Zephyria, Pyrewood, Solaria — $N = 3$)
Tiap desa berbobot tepat $33.33\%$:
* Pulihkan **1 Desa** $\rightarrow$ Progres $33.4\% \rightarrow$ ⭐ **1 Bintang**
* Pulihkan **2 Desa** $\rightarrow$ Progres $66.7\% \rightarrow$ ⭐⭐ **2 Bintang**
* Pulihkan **3 Desa** $\rightarrow$ Progres $100.0\% \rightarrow$ ⭐⭐⭐ **3 Bintang**

#### B. Stage 02 (2 Desa: Zephyria & Pyrewood — $N = 2$)
Tiap desa berbobot $50.0\%$:
* Pulihkan **1 Desa** $\rightarrow$ Progres $50.0\%$. Karena $50.0\% \ge 33.4\%$ namun belum mencapai $66.7\%$, pemain mendapatkan ⭐ **1 Bintang**.
* Mulai memulihkan desa kedua hingga minimal sepertiganya ($50\% + 16.7\% = 66.7\%$) $\rightarrow$ naik menjadi ⭐⭐ **2 Bintang**.
* Pulihkan **2 Desa Penuh** $\rightarrow$ Progres $100.0\% \rightarrow$ ⭐⭐⭐ **3 Bintang**.

#### C. Stage 01 (1 Desa: Zephyria — $N = 1$)
Progres desa terhubung $1:1$ ke Global Vitality:
* Kepakan sayap mencapai **$33.4\%$** $\rightarrow$ ⭐ **1 Bintang**
* Kepakan sayap mencapai **$66.7\%$** $\rightarrow$ ⭐⭐ **2 Bintang**
* Kepakan sayap mencapai **$100.0\%$** $\rightarrow$ ⭐⭐⭐ **3 Bintang**

---

## 🔓 4. Aturan Progresi & Pembukaan Level (*Level Unlocking Rule*)

1. **Syarat Membuka Gembok Level Berikutnya:**
   * **Cukup Minimal 1 Bintang ($\ge 33.4\%$ Progres Global)**.
   * Pemain yang telah berhasil menyelamatkan minimal sepertiga kepulauan berhak melanjutkan petualangan ke level selanjutnya.
   * Pemain dapat sewaktu-waktu mengulang (*Retry*) level sebelumnya dari Lobby untuk menyempurnakan perolehan menjadi 3 Bintang.
2. **Sekuens Progresi:**
   * Pemain Baru: **Stage 01** terbuka; Stage 02, 03, dan Free Play terkunci gembok.
   * Selesaikan Stage 01 ($\ge 1$ ⭐) $\rightarrow$ **Buka Stage 02**.
   * Selesaikan Stage 02 ($\ge 1$ ⭐) $\rightarrow$ **Buka Stage 03**.
   * Selesaikan Stage 03 ($\ge 1$ ⭐) $\rightarrow$ **Buka Free Play Mode** (Tamat / Game Complete!).

---

## 💾 5. Sistem Penyimpanan Data Persisten (`PlayerPrefs`)

Untuk menjaga progres pemain tetap tersimpan meski game ditutup:

| Key PlayerPrefs | Tipe Data | Nilai Default | Keterangan |
| :--- | :---: | :---: | :--- |
| `Fanamir_Stage01_Unlocked` | `int` | `1` *(True)* | Stage 01 selalu terbuka. |
| `Fanamir_Stage02_Unlocked` | `int` | `0` *(False)* | Terbuka jika Stage 01 meraih $\ge 1$ bintang. |
| `Fanamir_Stage03_Unlocked` | `int` | `0` *(False)* | Terbuka jika Stage 02 meraih $\ge 1$ bintang. |
| `Fanamir_FreePlay_Unlocked` | `int` | `0` *(False)* | Terbuka jika Stage 03 meraih $\ge 1$ bintang. |
| `Fanamir_Stage01_BestStars` | `int` | `0` | Rekor bintang tertinggi Stage 01 ($0–3$). |
| `Fanamir_Stage02_BestStars` | `int` | `0` | Rekor bintang tertinggi Stage 02 ($0–3$). |
| `Fanamir_Stage03_BestStars` | `int` | `0` | Rekor bintang tertinggi Stage 03 ($0–3$). |
| `Fanamir_Stage01_BestTime` | `float` | `0.0f` | Rekor waktu tercepat menyelesaikan Stage 01. |

---

## 🎨 6. Integrasi Layar Kemenangan `PF_FinishModal` (`SCREEN 04`)

Saat kondisi `Victory` atau `TimeUp` tercapai:
1. **Penyajian Visual:**
   * Menampilkan medali judul: *"MISI SELESAI"* (jika $\ge 1$ Bintang) atau *"WAKTU HABIS"* (jika $0$ Bintang).
   * **Animasi Bintang Terisi:** Bintang emas (`UI_Icon_Star_Gold`) terisi berurutan dari kiri ke kanan dengan jeda $0.4\text{ detik}$ diiringi efek suara lenting (*chime SFX*).
   * **Rincian Statistik:**
     * *Waktu Bermain:* `01:45` (format `MM:SS`).
     * *Desa Pulih:* `2 / 2 Desa` (persentase vitalitas global).
     * *Skor Poin:* Dihitung dari `(Vitalitas * 100) + (Sisa Detik * 25)`.
2. **Navigasi Tombol Interaktif:**
   * **`LEVEL BERIKUTNYA`** *(Tombol Utama Hijau):* Aktif hanya jika pemain meraih $\ge 1$ Bintang dan belum berada di stage terakhir.
   * **`COBA LAGI`** *(Tombol Kuning):* Me-restart stage yang sama untuk mengejar bintang lebih tinggi.
   * **`KEMBALI KE LOBBY`** *(Tombol Krem):* Kembali ke `MainMenu_Lobby.unity` (`SCREEN 00`) dengan kartu level yang sudah terbarui status bintangnya.

---

## 💻 7. Spesifikasi Teknis Kode C# (`GameManager.cs`)

```csharp
namespace DragonWingAR
{
    public enum GameState
    {
        LevelInit,
        CountdownStart,
        Playing,
        Paused,
        TimeUp,
        Victory
    }

    public class GameManager : MonoBehaviour
    {
        public static GameManager Instance { get; private set; }

        [Header("Stage Configuration")]
        public int currentStageIndex = 1; // 1, 2, 3, or 4 (Free Play)
        public float stageTimeLimitSeconds = 120f;
        public bool isFreePlayMode = false;

        [Header("Live Game State")]
        public GameState currentState = GameState.LevelInit;
        public float timeRemaining;
        public bool isTimerPaused = false;
        public float globalVitalityPercent = 0f; // 0.0f to 1.0f

        // Star Threshold Constants
        public const float STAR_1_THRESHOLD = 0.334f;
        public const float STAR_2_THRESHOLD = 0.667f;
        public const float STAR_3_THRESHOLD = 1.000f;

        public int CalculateStars()
        {
            if (globalVitalityPercent >= STAR_3_THRESHOLD) return 3;
            if (globalVitalityPercent >= STAR_2_THRESHOLD) return 2;
            if (globalVitalityPercent >= STAR_1_THRESHOLD) return 1;
            return 0;
        }

        public void PauseTimerForNarrative(bool pause)
        {
            isTimerPaused = pause;
        }

        public void UpdateGlobalVitality(float normalizedProgress)
        {
            globalVitalityPercent = Mathf.Clamp01(normalizedProgress);
            
            // Check instant 100% victory
            if (globalVitalityPercent >= 1.0f && currentState == GameState.Playing)
            {
                TriggerVictory();
            }
        }
    }
}
```

---
*Navigasi Vault:* [[00 - Overview]] | [[Tasks]] | [[Devlog]] | [[mekanisme_tantangan_desa]] | [[kontrak_integrasi_ui_dan_progress]] | [[prompt_eksekusi_vfx_skybox_dan_fov_laptop_b]]
