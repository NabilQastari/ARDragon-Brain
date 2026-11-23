---
title: "Planning Perbaikan UI In-Game: Isolasi Textbox Narasi & Redesain Side-Docked Event Desa"
tags:
  - ui-ux
  - design
  - laptop-a
  - in-game-hud
  - village-challenge
  - quest-narrative
status: approved
owner: Laptop A
updated: 2026-11-23
related:
  - "[[00 - Overview]]"
  - "[[rencana_desain_ui]]"
  - "[[kontrak_integrasi_ui_dan_progress]]"
  - "[[panduan_aset_ui_unity]]"
  - "[[mekanisme_tantangan_desa]]"
---

# 🎨 Planning Perbaikan UI In-Game: Isolasi Textbox Narasi & Redesain Side-Docked Event Desa
## Fanamir: The Sky Guardian *(Internal Project: AR Dragon)*

> **Dokumen Resmi Desain & Arsitektur (Laptop A):** Merespons langsung evaluasi pengujian gameplay dari pengguna mengenai dua masalah krusial pada antarmuka in-game:
> 1. **Kebocoran Elemen UI saat Textbox Narasi Muncul:** Seluruh UI HUD umum masih tampil bertumpuk; seharusnya layar terisolasi penuh menyisakan **hanya UI Textbox Narasi dan Quest Objective Panel**, serta ukuran textbox dan font teks dialog perlu diperbesar secara drastis untuk keterbacaan game *exergaming* dari kejauhan ($\sim 1.5\text{--}2.5\text{ m}$).
> 2. **UI Event Desa Menghalangi Naga Fanamir:** Modal tantangan desa lama (`SCREEN 03`, kartu perkamen raksasa $720 \times 670\text{ px}$ di tengah layar) menutup visual badan naga 3D sehingga pemain tidak dapat melihat aksi naga (mengepak sayap, mengisi energi air di danau, menyemburkan nafas air *water breath*, atau ritual embun). Seluruh UI gameplay lain disembunyikan, dan UI tantangan desa diganti menjadi **Side-Docked Challenge HUD** di **samping layar** yang hanya berisi **Progress Bar dan Indikator Pose Target**.

---

## 🔍 1. Analisis Masalah & Akar Penyebab (Root Cause Analysis)

### A. Masalah 1: Kebocoran UI In-Game saat Textbox Dialog Muncul
* **Kondisi Eksisting:**
  Saat Fanamir memasuki radius trigger desa dan memainkan animasi *auto-hover*, dialog narasi desa (`PF_QuestNarrativeBox` / `SCREEN 08`) muncul di area bawah layar. Namun, elemen HUD penerbangan normal seperti Kapsul Timer & Counter Desa (tengah atas), Tombol Pause (kanan atas), *Global Vitality Bar* (kiri), *Telemetry Dock* (kanan bawah), *Momentum Dock* (kanan bawah), klaster PIP Kamera (kanan bawah), dan panah *Waypoint Compass* masih tetap aktif dan tampil.
* **Dampak Negatif:**
  Layar terasa sangat sesak (*visual clutter*), perhatian pemain terpecah antara membaca cerita desa dan melihat angka telemetri yang tidak bergerak, serta merusak suasana naratif sinematik.
* **Akar Masalah Teknis:**
  Method `InGameFlightHUD.SetNarrativeIsolationMode(bool)` pada implementasi sebelumnya hanya menonaktifkan sebagian widget (misal hanya dock telemetri) atau tidak mengaitkan seluruh container HUD (seperti `MissionCapsule`, `GlobalVitalityBar`, `Btn_EscPause`, dan *Waypoint Compass*) ke dalam kontrol visibilitas terpadu saat dialog aktif.
* **Masalah Skala Tipografi:**
  Ukuran awal Textbox (`1236 × 212 px`) dengan font dialog $\sim 22\text{--}24\text{ pt}$ dirancang untuk monitor desktop jarak dekat ($\sim 50\text{ cm}$). Pada realitas game *exergaming* berbasis webcam, pemain berdiri atau duduk mundur pada jarak $\sim 1.5\text{--}2.5\text{ meter}$ agar tubuh masuk ke dalam frame kamera, sehingga teks narasi terasa terlalu kecil dan sulit dibaca tanpa menyipitkan mata.

---

### B. Masalah 2: Modal Tantangan Desa Menutupi Badan Naga
* **Kondisi Eksisting:**
  Ketika narasi selesai dan tantangan desa dimulai (`ChallengeActive`), sistem lama memunculkan `PF_VillageChallengeOverlay` (`SCREEN 03`) berupa kartu modal perkamen raksasa berukuran **$720 \times 670\text{ px}$** tepat di tengah layar ($x: 600\dots 1320, y: 205\dots 875$).
* **Dampak Negatif Fatal:**
  Kamera utama game berada dalam mode *chase/follow* tepat di belakang naga Fanamir ($x: 960, y: \sim 500$). Kartu modal raksasa tersebut menutupi tubuh naga secara total!
  - Pada **Desa Zephyria**, pemain mengepakkan tangan, namun tidak bisa melihat sayap naga 3D mengepak bertenaga.
  - Pada **Desa Pyrewood (Danau)**, pemain mengisi energi air, namun tidak melihat naga melayang di atas permukaan air.
  - Pada **Desa Pyrewood (Hutan)**, pemain membuka mulut lebar untuk menyemburkan nafas air purba (*Water Breath*), namun artikulasi rahang buka mulut `Bone_Jaw` dan semprotan partikel VFX air terhalang oleh plakat perkamen raksasa!
  - Pada **Desa Solaria**, ritual embun langit (*Dew Rain*) terhalang oleh modal.
  Pemain kehilangan kepuasan visual interaktif dari hasil gerakan fisik mereka.
* **Solusi yang Diinginkan Pengguna:**
  1. Hapus konsep modal tengah layar!
  2. Saat event desa berlangsung, sembunyikan UI gameplay lain yang tidak relevan.
  3. Tampilkan hanya **Bar Progres Desa** dan **Indikator Pose/Gerakan yang harus dilakukan**.
  4. Letakkan komponen tersebut di **bagian samping layar** agar seluruh area tengah layar (tempat naga Fanamir beraksi) terbuka $100\%$ tanpa halangan apa pun.

---

## 📐 2. Spesifikasi Solusi Desain Baru (User-Centered UI/UX)

```text
====================================================================================================
1. TAMPILAN SAAT TEXTBOX NARASI AKTIF (FULL NARRATIVE ISOLATION MODE)
====================================================================================================
+--------------------------------------------------------------------------------------------------+
| [📜 QUEST OBJECTIVE]                                                                             |
| | SMOG VALLEY CRISIS                                                         (UI LAIN HIDE 100%) |
| | Dengarkan petuah Tetua Rowan...                                                                |
|                                                                                                  |
|                                                                                                  |
|                                                                                                  |
|                                    [ AREA NAGA 3D AUTO-HOVER ]                                   |
|                                       (BEBAS & SANGAT LEGA)                                      |
|                                                                                                  |
|                                                                                                  |
|     +--------------------------------------------------------------------------------------+     |
|     | [ DESA ZEPHYRIA · TETUA ROWAN ]                                [ LINE 1 / 3 · AUTO ] |     |
|     | +-------+  "Kabut jelaga pekat dari cerobong kuno merayap menuruni lembah...         |     |
|     | | ICON  |   Udara kami kian beracun dan tanaman pinus mulai mengering!               |     |
|     | | BIOME |   Wahai Penjaga Langit, kepakkan sayapmu untuk mengusir kabut ini!"        |     |
|     | +-------+  +-----------------------------------------------------------------------+ |     |
|     |            | [!] PETUNJUK: Kepakkan kedua tanganmu melewati bahu secara ritmis!    | |     |
|     |            +-----------------------------------------------------------------------+ |     |
|     |                                                        [ AUTO-PLAY · LANJUT DLM 3.8s ] |     |
|     +--------------------------------------------------------------------------------------+     |
+--------------------------------------------------------------------------------------------------+

====================================================================================================
2. TAMPILAN SAAT EVENT TANTANGAN DESA (SIDE-DOCKED CHALLENGE HUD)
====================================================================================================
+--------------------------------------------------------------------------------------------------+
| [📜 ACTIVE TASK]                             [ 01:45 · TIMER KRISIS ]                            |
| | USIR KABUT LEMBAH                                                                              |
|                                                                      +-------------------------+ |
|                                                                      | DESA ZEPHYRIA           | |
|                                                                      | PEMBERSIHAN KABUT       | |
|                                                                      +-------------------------+ |
|                                                                      | +---------------------+ | |
|                                                                      | |   [IKON POSE #06]   | | |
|                                                                      | |   KEPAKKAN SAYAP!   | | |
|                                                                      | +---------------------+ | |
|                                                                      | ● GERAKAN TERDETEKSI!   | |
|                                                                      +-------------------------+ |
|                           [ NAGA FANAMIR 3D BERAKSI ]                | PROGRESS PEMULIHAN DESA | |
|                         - Sayap Mengepak Bertenaga -                 | [=================    ] | |
|                           - Semburan Partikel VFX -                  | 7 / 10 KEPAKAN (70%)    | |
|                        (100% BEBAS OBSTRUKSI DI TENGAH)              +-------------------------+ |
|                                                                      | [TUCK DIVE] UTK KELUAR  | |
|                                                                      +-------------------------+ |
+--------------------------------------------------------------------------------------------------+
```

---

## 🏛️ 3. Cetak Biru Teknis Part 1: Textbox Narasi Ekstra Besar & Isolasi Total

### A. Dimensi & Posisi Baru Textbox (`PF_QuestNarrativeBox`)
* **Ukuran Box:** Dari $1236 \times 212\text{ px}$ diperbesar menjadi **$1440 \times 300\text{ px}$** ($+204\text{ px}$ lebar, $+88\text{ px}$ tinggi).
* **Posisi Canvas:** Terpusat horizontal pada layar $1920 \times 1080$, anchor *Bottom-Center*, `anchoredPosition = (0, 36)` ($x: 240\dots 1680, y: 36\dots 336$).
* **Sprite 9-Slice:** Tetap menggunakan `UI_Panel_NarrativeBox_9Slice.png` dengan border 9-Slice yang sudah dikonfigurasi, sehingga sudut *stepped rabbeted plaque* tetap tajam tanpa distorsi.

### B. Skala Tipografi & Elemen Interior Textbox (Optimal untuk Jarak 2 Meter)
1. **Banner Pembicara (`UI_Banner_SpeakerTag_Blank`):**
   - Ukuran: $420 \times 52\text{ px}$ (overlap tepi atas kiri $y = +26\text{ px}$).
   - Teks Pembicara: `Fredoka Bold`, ukuran font dinaikkan dari $20\text{ pt}$ menjadi **$28\text{--}30\text{ pt}$** (`#2D2926`).
2. **Badge Hitung Mundur Baris (`UI_Tag_Stage_Gold_Blank` / Pill Tag):**
   - Ukuran: $240 \times 44\text{ px}$ di sudut kanan atas box.
   - Teks: `Fredoka Bold 20\text{--}22\text{ pt}` (`"LINE 1 / 3 · AUTO-PLAY"`).
3. **Plakat Emblem Biome Kiri (`UI_Illust_Frame_Dark_9Slice`):**
   - Ukuran: Diperbesar dari $132 \times 150\text{ px}$ menjadi **$160 \times 180\text{ px}$** pada $x = 36\text{ px}$.
   - Ikon Biome: Ukuran $96 \times 96\text{ px}$ diapit teks nama biome di bawahnya ($18\text{ pt}$).
4. **Teks Dialog Narasi (2–3 Baris Utama):**
   - Area Teks: Lebar $880\text{ px}$, tinggi $120\text{ px}$ ($x = 220\dots 1100$).
   - Font: `Nunito ExtraBold` / `Nunito Bold`.
   - Ukuran Font: Dinaikkan drastis dari $22\text{--}24\text{ pt}$ menjadi **$34\text{--}36\text{ pt}$** dengan line spacing $1.2\times$. Sangat kontras dan terbaca jelas dari jarak $2.5\text{ meter}$.
5. **Strip Instruksi "PETUNJUK AKSI":**
   - Background: `UI_Row_NotesBox_Beige_9Slice` diperlebar menjadi **$880 \times 58\text{ px}$** tepat di bawah teks dialog.
   - Soket Ikon Aksi: $46 \times 46\text{ px}$ berisi ikon gestur/seruan.
   - Teks Petunjuk: Dinaikkan dari $18\text{ pt}$ menjadi **$26\text{--}28\text{ pt}$** (`#332C25`), format tebal: *"PETUNJUK: Kepakkan kedua tanganmu secara ritmis untuk mengusir kabut!"*.
6. **Panel Auto-Play Countdown Kanan:**
   - Ikon Timer: `UI_Icon_AutoPlay_Timer` ($48 \times 48\text{ px}$).
   - Teks Waktu: `Fredoka Bold 24\text{ pt}` (*"LANJUT 3.5s / 5.0s"*).
   - Bar Pengisi Auto-Advance: Dipertebal dari $6\text{ px}$ menjadi **$14\text{ px}$** (`UI_Progress_Horiz_Track_9Slice` + `UI_Progress_Horiz_Fill_Blue_9Slice` / fill hijau emerald).

### C. Protokol Isolasi UI Menyeluruh (`SetNarrativeIsolationMode(true)`)
Saat dialog narasi dibuka:
* **HANYA DUA ELEMEN YANG TAMPIL:**
  1. `QuestObjectivePanel` (kiri atas, posisi `(60, 40)`).
  2. `PF_QuestNarrativeBox` (bawah tengah).
* **SEMUA ELEMEN BERIKUT WAJIB DINONAKTIFKAN:**
  - `MissionCapsule` (Timer tengah atas & Counter desa) $\rightarrow$ `SetActive(false)`.
  - `Btn_EscPause` (Tombol pause kanan atas) $\rightarrow$ `SetActive(false)`.
  - `GlobalVitalityBar` (Bar energi kiri) $\rightarrow$ `SetActive(false)`.
  - `TelemetryDock` (Dock kecepatan/ketinggian kanan bawah) $\rightarrow$ `SetActive(false)`.
  - `MomentumDock` (Dock momentum kanan bawah) $\rightarrow$ `SetActive(false)`.
  - `PIP_CameraControlsCard` & `PIP_CameraPanel` (Klaster PIP kanan bawah) $\rightarrow$ `SetActive(false)`.
  - `WaypointCompass` (`UI_HUD_Waypoint_Chevron`) $\rightarrow$ `SetActive(false)`.
  - *World-Space Village Marker Billboard* $\rightarrow$ disembunyikan agar teks 3D tidak mengambang di depan pemandangan desa.

---

## 🛡️ 4. Cetak Biru Teknis Part 2: Side-Docked Challenge HUD (Event Desa)

### A. Konsep & Alasan Penempatan Samping
* **Penghapusan Modal Tengah (`PF_VillageChallengeOverlay` lama):**
  Tidak ada lagi kartu perkamen $720 \times 670\text{ px}$ di tengah layar!
* **Penempatan Baru: Sisi Kanan Layar (`Right-Center Docked`):**
  - Panel samping vertikal ramping: **Lebar $400\text{ px}$**, **Tinggi $620\text{ px}$**.
  - Anchor: `Middle-Right (1.0, 0.5)`, `anchoredPosition = (-30, 0)`.
  - Keunggulan: Area tengah layar ($x = 350\dots 1500$, bentang $> 1150\text{ px}$) **100% BEBAS & KOSONG**. Pemain dapat melihat seluruh artikulasi tubuh naga Fanamir, kibasan sayap, semburan air purba, dan efek kabut/api yang memudar secara dramatis!

### B. Komponen Esensial Side-Docked Challenge HUD
Panel samping dibangun menggunakan komponen resmi eksisting dari `UI_Unity_Export/Components/` tanpa memerlukan aset baru:

1. **Bingkai Kontainer Samping (`UI_Panel_Parchment_Calib_9Slice` / `UI_Plaque_SubParchment_9Slice`):**
   - Dimensi $400 \times 620\text{ px}$, warna perkamen solid `#F2EFE7` (`alpha = 1.0f`), outline tinta netral `#332C25`.
2. **Header Nama Desa & Status Krisis (Atas Panel Samping):**
   - Komponen: `UI_Header_Bar_Parchment_9Slice` atau `UI_Banner_Stepped_SubPanel_Blank` ($360 \times 64\text{ px}$).
   - Teks Utama: **`DESA ZEPHYRIA`** / **`DESA PYREWOOD`** / **`DESA SOLARIA`** (`Fredoka Bold 24\text{ pt}`).
   - Sub-teks Krisis: `"PEMBERSIHAN KABUT LEMBAH"` / `"PEMULIHAN HUTAN TERBAKAR"` / `"RITUAL EMBUN SUCI"` (`Nunito ExtraBold 16\text{ pt}`).
3. **Kartu Indikator Gestur / Pose Target (`Pose Indicator Card`):**
   - Komponen: `UI_Plaque_PoseCard_Active_9Slice` ($360 \times 220\text{ px}$).
   - **Medali Siluet Pose Besar ($84 \times 84\text{ px}$):**
     - Zephyria: `UI_Pose_06_FlapToClimb.png` (Ikon Kepakan Sayap).
     - Pyrewood Tahap 1: `UI_Pose_15_VillageActions.png` (Ikon Mengisi Air / Danau).
     - Pyrewood Tahap 2: `UI_Illust_Dragon_WaterBreath.png` (Ikon Buka Mulut / Sembur Air).
     - Solaria: `UI_Pose_15_VillageActions.png` (Ikon Angkat Kedua Tangan).
   - **Teks Komando Gerakan yang Kuat:**
     - Teks Utama: **`"KEPAKKAN SAYAP!"`** / **`"SEMBURKAN AIR!"`** / **`"ANGKAT KEDUA TANGAN!"`** (`Fredoka Bold 28\text{ pt}`, `#2D2926`).
     - Sub-instruksi: *"Ayunkan kedua tangan melewati bahu"* / *"Buka mulut lebar atau tangkupkan tangan"* (`Nunito Bold 16\text{ pt}`).
   - **Live Detection Feedback Badge (Indikator Responsif):**
     - Terletak tepat di bawah instruksi pose ($320 \times 36\text{ px}$):
       - Saat gestur pemain terdeteksi aktif oleh webcam: Badge berubah menjadi hijau emerald menyala **`● GERAKAN TERDETEKSI (AKTIF!)`** dengan efek animasi denyut lembut (*pulse*).
       - Saat pemain berhenti bergerak: Badge netral amber **`○ MENUNGGU GERAKAN...`**.
       - Memberikan kepastian visual instan kepada pemain bahwa webcam membaca tubuh mereka tanpa harus menatap jendela PIP.
4. **Progress Bar Pemulihan Desa (Large Tactile Progress Bar):**
   - Komponen: `UI_Progress_Horiz_Track_9Slice` + `UI_Progress_Horiz_Fill_Blue_9Slice` (atau fill hijau emerald `#5C8D46`).
   - Ukuran Bar: **$360 \times 48\text{ px}$** (sangat tebal dan taktil).
   - Nilai Pengisian: $0\% \to 100\%$ dihaluskan dengan `Mathf.SmoothDamp`.
   - **Teks Progres Numerik:**
     - Ditampilkan tepat di atas atau di tengah bar: **`7 / 10 KEPAKAN (70%)`** atau **`85% TERISI`** (`Fredoka Bold 22\text{ pt}`).
   - **Sifat No-Decay:** Bar tidak berkurang jika pemain jeda beristirahat.
5. **Petunjuk Escape / Keluar dari Area:**
   - Komponen: `UI_Chip_Bar_Cream_9Slice` ($360 \times 38\text{ px}$) di dasar panel.
   - Teks:
     - Zephyria: `[TUCK DIVE (#13)] / [DASH] UNTUK KELUAR` (`Fredoka SemiBold 15\text{ pt}`).
     - Desa Lain: `[KEPAK SAYAP] / [TUCK DIVE] UNTUK KELUAR`.

### C. Kebijakan Tampilan UI saat Event Tantangan Desa (`SetVillageChallengeMode(true)`)
Saat `ChallengeActive`:
* **TAMPILKAN:**
  1. `Side-Docked Challenge HUD` (kanan tengah).
  2. `QuestObjectivePanel` (kiri atas, status `ACTIVE TASK`).
  3. `MissionCapsule` (tengah atas — Timer krisis tetap berjalan karena pemain berkejaran dengan waktu untuk evaluasi bintang 1–3).
* **SEMBUNYIKAN (HIDE TOTAL):**
  1. `GlobalVitalityBar` (kiri).
  2. `TelemetryDock` (kanan bawah — kecepatan & ketinggian tidak relevan saat auto-hover).
  3. `MomentumDock` (kanan bawah).
  4. `PIP_CameraControlsCard` & `PIP_CameraPanel` (atau disembunyikan/diperkecil rapi agar pandangan naga bersih).
  5. `WaypointCompass` (sudah sampai di desa).
  6. Seluruh modal perkamen raksasa tengah layar dinonaktifkan!

---

## 💻 5. Arsitektur Kode Unity C# yang Diperbarui

### A. `InGameFlightHUD.cs`
Tambahkan dan perbarui mode visibilitas antarmuka secara terpusat:
```csharp
// Di InGameFlightHUD.cs

[Header("Challenge & Narrative References")]
[SerializeField] private GameObject missionCapsule;
[SerializeField] private GameObject btnEscPause;
[SerializeField] private GameObject globalVitalityBar;
[SerializeField] private GameObject telemetryDock;
[SerializeField] private GameObject momentumDock;
[SerializeField] private GameObject pipCameraControlsCard;
[SerializeField] private GameObject pipCameraPanel;
[SerializeField] private GameObject waypointCompass;
[SerializeField] private VillageChallengeUI sideChallengeUI;
[SerializeField] private QuestNarrativeUI questNarrativeUI;

/// <summary>
/// Mode 1: Isolasi Total saat Textbox Narasi Muncul.
/// Hanya menampilkan QuestObjectivePanel dan QuestNarrativeBox.
/// </summary>
public void SetNarrativeIsolationMode(bool active)
{
    // Sembunyikan/tampilkan seluruh HUD gameplay
    if (missionCapsule != null) missionCapsule.SetActive(!active);
    if (btnEscPause != null) btnEscPause.SetActive(!active);
    if (globalVitalityBar != null) globalVitalityBar.SetActive(!active);
    if (telemetryDock != null) telemetryDock.SetActive(!active);
    if (momentumDock != null) momentumDock.SetActive(!active);
    if (pipCameraControlsCard != null) pipCameraControlsCard.SetActive(!active);
    if (pipCameraPanel != null) pipCameraPanel.SetActive(!active);
    if (waypointCompass != null) waypointCompass.SetActive(!active);

    if (sideChallengeUI != null) sideChallengeUI.gameObject.SetActive(false);

    // QuestObjectivePanel tetap aktif (true)
    if (questObjectivePanel != null) questObjectivePanel.gameObject.SetActive(true);
}

/// <summary>
/// Mode 2: Tampilan Tantangan Desa Samping (Side-Docked Challenge HUD).
/// Menyembunyikan telemetri/vitality agar naga di tengah layar terlihat 100% bebas.
/// </summary>
public void SetVillageChallengeMode(bool active)
{
    if (globalVitalityBar != null) globalVitalityBar.SetActive(!active);
    if (telemetryDock != null) telemetryDock.SetActive(!active);
    if (momentumDock != null) momentumDock.SetActive(!active);
    if (pipCameraControlsCard != null) pipCameraControlsCard.SetActive(!active);
    if (pipCameraPanel != null) pipCameraPanel.SetActive(!active);
    if (waypointCompass != null) waypointCompass.SetActive(!active);

    // Timer krisis dan QuestObjectivePanel tetap tampil
    if (missionCapsule != null) missionCapsule.SetActive(true);
    if (btnEscPause != null) btnEscPause.SetActive(true);
    if (questObjectivePanel != null) questObjectivePanel.gameObject.SetActive(true);

    if (sideChallengeUI != null) sideChallengeUI.gameObject.SetActive(active);
}

/// <summary>
/// Mode 3: Kembalikan HUD Penerbangan Normal saat terbang bebas di atas pulau.
/// </summary>
public void RestoreNormalFlightHUD()
{
    if (missionCapsule != null) missionCapsule.SetActive(true);
    if (btnEscPause != null) btnEscPause.SetActive(true);
    if (globalVitalityBar != null) globalVitalityBar.SetActive(true);
    if (telemetryDock != null) telemetryDock.SetActive(true);
    if (momentumDock != null) momentumDock.SetActive(true);
    if (pipCameraControlsCard != null) pipCameraControlsCard.SetActive(true);
    if (pipCameraPanel != null) pipCameraPanel.SetActive(true);
    if (waypointCompass != null) waypointCompass.SetActive(true);

    if (sideChallengeUI != null) sideChallengeUI.gameObject.SetActive(false);
    if (questNarrativeUI != null) questNarrativeUI.gameObject.SetActive(false);
}
```

### B. `VillageChallengeUI.cs` (Redesain Menjadi Side-Docked HUD)
Ubah API dan hierarki komponen:
```csharp
// Di VillageChallengeUI.cs
public class VillageChallengeUI : MonoBehaviour
{
    [Header("UI Containers")]
    [SerializeField] private RectTransform sidePanelRoot; // Anchored Right-Center (-30, 0)
    [SerializeField] private TextMeshProUGUI txtVillageTitle;
    [SerializeField] private TextMeshProUGUI txtCrisisSubtitle;
    [SerializeField] private Image imgPoseIcon;
    [SerializeField] private TextMeshProUGUI txtPoseCommand;
    [SerializeField] private TextMeshProUGUI txtPoseHint;
    [SerializeField] private GameObject feedbackBadgeActive;
    [SerializeField] private GameObject feedbackBadgeWaiting;
    [SerializeField] private Image imgProgressBarFill;
    [SerializeField] private TextMeshProUGUI txtProgressNumeric;
    [SerializeField] private TextMeshProUGUI txtEscapeHint;

    private float _targetProgress;
    private float _currentProgress;

    public void ShowSideChallenge(string villageTitle, string crisisSubtitle, string command, string hint, Sprite poseSprite, string escapeText)
    {
        gameObject.SetActive(true);
        if (txtVillageTitle != null) txtVillageTitle.text = villageTitle;
        if (txtCrisisSubtitle != null) txtCrisisSubtitle.text = crisisSubtitle;
        if (txtPoseCommand != null) txtPoseCommand.text = command;
        if (txtPoseHint != null) txtPoseHint.text = hint;
        if (imgPoseIcon != null && poseSprite != null) imgPoseIcon.sprite = poseSprite;
        if (txtEscapeHint != null) txtEscapeHint.text = escapeText;

        SetGestureFeedback(false);
        UpdateProgress(0f, "0%");
    }

    public void UpdateProgress(float progress01, string customLabel = null)
    {
        _targetProgress = Mathf.Clamp01(progress01);
        if (imgProgressBarFill != null) imgProgressBarFill.fillAmount = _targetProgress;
        if (txtProgressNumeric != null)
        {
            txtProgressNumeric.text = string.IsNullOrEmpty(customLabel) 
                ? $"{Mathf.RoundToInt(_targetProgress * 100f)}% PULIH" 
                : customLabel;
        }
    }

    public void SetGestureFeedback(bool isPerforming)
    {
        if (feedbackBadgeActive != null) feedbackBadgeActive.SetActive(isPerforming);
        if (feedbackBadgeWaiting != null) feedbackBadgeWaiting.SetActive(!isPerforming);
    }

    public void HideSideChallenge()
    {
        gameObject.SetActive(false);
    }
}
```

### C. `VillageZoneController.cs` (Sinkronisasi Event & Feedback)
```csharp
// Di VillageZoneController.cs
private void EnterNarrativeState()
{
    _currentState = VillageState.NarrativeActive;
    if (inGameHUD != null) inGameHUD.SetNarrativeIsolationMode(true);
    questNarrativeUI.ShowNarrativeSequence(GetDialogLines(), _villageName, GetActionHint(), OnNarrativeCompleted);
}

private void EnterChallengeState()
{
    _currentState = VillageState.ChallengeActive;
    if (inGameHUD != null) inGameHUD.SetVillageChallengeMode(true);
    
    // Buka Side Challenge HUD dengan ikon dan teks yang sesuai biome
    villageChallengeUI.ShowSideChallenge(
        _villageTitle, 
        _crisisSubtitle, 
        _gestureCommand, 
        _gestureHint, 
        GetPoseSpriteForBiome(), 
        GetEscapeText()
    );
}

private void UpdateChallengeLoop()
{
    bool isPerforming = CheckActiveGesturePerforming();
    villageChallengeUI.SetGestureFeedback(isPerforming);
    
    // Update progress tanpa decay
    villageChallengeUI.UpdateProgress(_restorationProgress, GetProgressLabel());
    
    if (_restorationProgress >= 1.0f)
    {
        OnChallengeSuccess();
    }
}

private void OnChallengeSuccess()
{
    _currentState = VillageState.Restored;
    villageChallengeUI.HideSideChallenge();
    if (inGameHUD != null) inGameHUD.RestoreNormalFlightHUD();
    // Berikan Launch Thrust dan trigger restorasi visual pulau
}
```

---

## 📋 6. Master Prompt Eksekusi Siap Salin untuk Laptop B

Salin instruksi lengkap di bawah ini ke sesi chat **Laptop B** untuk eksekusi di Unity Editor & C#:

```markdown
Tugas: Implementasikan Perbaikan UI In-Game (Isolasi Textbox Narasi & Redesain Side-Docked Challenge HUD) di Unity 6 (Laptop B) sesuai dokumen desain resmi `[[laptop-a/ui-ux/planning_perbaikan_ui_ingame_dan_event_desa]]`.

### 1. BACA ATURAN & DOKUMEN RUJUKAN
1. Jalankan `git -C brain pull`.
2. Baca dokumen `brain/laptop-a/ui-ux/planning_perbaikan_ui_ingame_dan_event_desa.md` secara seksama.

### 2. PERBAIKAN 1: FULL NARRATIVE ISOLATION MODE & TEXTBOX LEBIH BESAR
1. **Perbesar Prefab Textbox Narasi (`PF_QuestNarrativeBox.prefab`):**
   - Atur RectTransform menjadi lebar `1440 px`, tinggi `300 px`, anchor *Bottom-Center*, `anchoredPosition = (0, 36)`.
   - Perbesar `UI_Banner_SpeakerTag_Blank` menjadi `420 × 52 px` dengan font `Fredoka Bold 28 pt`.
   - Perbesar area teks dialog: font `Nunito ExtraBold` dinaikkan menjadi `34 pt`, line spacing `1.2`.
   - Perbesar strip instruksi petunjuk aksi: `Fredoka Bold 26 pt`.
   - Perbesar countdown auto-play: teks `Fredoka Bold 24 pt`, bar pengisi `14 px`.
2. **Isolasi UI di `InGameFlightHUD.cs` (`SetNarrativeIsolationMode(bool active)`):**
   - Saat `active == true`, pastikan SEMUA elemen HUD berikut di-hide (`SetActive(false)`):
     - `MissionCapsule` (timer tengah atas)
     - `Btn_EscPause` (tombol pause)
     - `GlobalVitalityBar` (bar energi kiri)
     - `TelemetryDock` & `MomentumDock` (dock kanan bawah)
     - `PIP_CameraControlsCard` & `PIP_CameraPanel` (klaster PIP)
     - `WaypointCompass`
   - Pastikan HANYA `QuestObjectivePanel` dan `PF_QuestNarrativeBox` yang aktif di layar!

### 3. PERBAIKAN 2: REDESAIN SIDE-DOCKED CHALLENGE HUD (SCREEN 03)
1. **Redesain Hierarki `PF_VillageChallengeOverlay.prefab`:**
   - Ubah dari modal tengah $720 \times 670\text{ px}$ menjadi panel samping kanan:
     - Anchor: *Middle-Right (1.0, 0.5)*, `anchoredPosition = (-30, 0)`, lebar `400 px`, tinggi `620 px`.
   - Komposisi panel:
     - Header nama desa & krisis ($360 \times 64\text{ px}$).
     - Kartu Indikator Pose Target ($360 \times 220\text{ px}$): ikon pose besar ($84 \times 84\text{ px}$), teks komando tegas ($28\text{ pt}$), dan badge live feedback (`● GERAKAN TERDETEKSI` vs `○ MENUNGGU GERAKAN`).
     - Progress Bar Pemulihan Desa ($360 \times 48\text{ px}$) tebal dengan teks persentase/hitungan ($22\text{ pt}$).
     - Hint escape di bagian dasar ($360 \times 38\text{ px}$).
2. **Hubungkan ke `InGameFlightHUD.cs` & `VillageZoneController.cs`:**
   - Buat method `SetVillageChallengeMode(bool active)` di `InGameFlightHUD.cs` yang menyembunyikan telemetri/vitality/PIP dan mengaktifkan Side Challenge HUD.
   - Hubungkan deteksi gestur real-time dari `VillageZoneController` ke `villageChallengeUI.SetGestureFeedback(isPerforming)`.
   - Pastikan area tengah layar naga 100% bebas dari halangan modal perkamen!

### 4. VERIFIKASI PLAY MODE
1. Jalankan scene `MainIsland_Gameplay.unity` di Play Mode.
2. Masuk ke pad desa Zephyria: Verifikasi bahwa HANYA Textbox Narasi besar dan Quest Objective yang tampil.
3. Setelah narasi selesai dan masuk tantangan kabut: Verifikasi bahwa panel samping kanan muncul, naga di tengah terlihat jelas sedang mengepak, badge feedback menyala saat mengepak, bar progres bertambah tanpa decay, dan seluruh animasi naga bebas terlihat.
4. Lakukan commit & push log ke `brain/laptop-b/`.
```

---

## 🧭 7. Ringkasan & Langkah Selanjutnya

1. **Sinkronisasi Desain:** Dokumen perencanaan ini mengunci solusi permanen untuk kenyamanan visual in-game dan memberikan kebebasan pandang $100\%$ pada aksi naga Fanamir.
2. **Kesiapan Eksekusi:** Master prompt pada Bagian 6 siap diberikan kepada Laptop B untuk diterapkan pada prefab UI dan skrip C# tanpa memerlukan aset gambar baru.
3. **Pembaruan Vault:** [[Tasks]] dan [[Devlog]] diperbarui untuk mencatat penyelesaian perancangan ini.

---

[[00 - Overview]] · [[Tasks]] · [[Devlog]] · [[rencana_desain_ui]] · [[kontrak_integrasi_ui_dan_progress]] · [[mekanisme_tantangan_desa]]
