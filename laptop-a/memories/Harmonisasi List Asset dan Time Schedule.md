---
title: "Harmonisasi List Asset dan Time Schedule"
type: session-memory
tags:
  - session-memory
  - laptop-a
  - list-asset
  - time-schedule
  - blender-prompt
date: 2026-11-13
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[konteks]]"
  - "[[prd]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
---

# Harmonisasi List Asset dan Time Schedule

## 1. Tujuan & Konteks Sesi
- Mengulas dokumen teknis utama (`konteks.md` dan `prd.md`) terkait kebutuhan awal pembuatan prototipe/desain GUI game *AR Dragon Sky Guardian*.
- Memeriksa dan menganalisis isi file lembar kerja `TS & List Asset.xlsx` yang memuat daftar aset 3D, environment, serta jadwal pengerjaan (*Time Schedule* / TS) tim (Nabil, Fauzih, Asmir).
- Menyusun prompt teknis lengkap untuk Hermes Agent agar dapat memodelkan pulau melayang (*Floating Island Base Terrain*) menggunakan Blender MCP (`bpy`).
- Memperbaiki serta menyelaraskan sheet `List Asset` di dalam Excel agar sesuai dengan jadwal di sheet `TS` dan prinsip efisiensi produksi anti-bug (`konteks.md`), kemudian menyederhanakannya menjadi **Versi 2** yang fokus pada aset utama.

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Prinsip Aset 3D Anti-Bug (Single Model Transition):**
   - Tidak membuat aset ganda untuk kondisi rusak vs pulih (misalnya rumah tercemar vs rumah pulih). Cukup 1 model dasar di mana perubahan status dikontrol melalui skrip C# di Unity (`emission.rateOverTime` pada asap polusi, `transform.localScale` pada api, dan `Color.Lerp` pada material pohon/tanah).
2. **Prop Desa 3 (Tantangan Kemarau):**
   - Menetapkan status Prop Desa 3 yang sebelumnya tertulis *"Menunggu jenis tantangan"* menjadi **Tanah Retak / Sumber Air Kering (Tantangan Desa 3 - Kemarau / Drought)** sesuai gestur pemanggilan hujan embun (tangan membentuk huruf V/Y di atas kepala).
3. **Penyelarasan List Asset Versi 2 (Ramping & Fokus Aset Utama):**
   - Sheet utama `List Asset` disederhanakan menjadi 5 entri aset inti yang selaras 1-to-1 dengan kategori *Assets* pada Time Schedule (Model Naga, Model Rumah, Model Pulau, Model Pohon, dan VFX Particles).
   - Daftar detail prop, vegetasi sekunder, dan dekorasi opsional diarsipkan pada sheet pendukung `List Asset (Detail)` agar tetap bisa dijadikan rujukan tahap pemolesan (*polish*) berikutnya tanpa membingungkan prioritas tim.

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- **`TS & List Asset_backup.xlsx`:** Dibuat sebagai file cadangan sebelum melakukan modifikasi struktural workbook Excel.
- **`TS & List Asset.xlsx`:**
  - Sheet `List Asset` dirombak ke **Versi 2** berisi 5 aset prioritas (Karakter Naga, Rumah Desa, Pulau Melayang, Pohon Modular, dan VFX Particles) lengkap dengan PIC, target pekan, status, progres (%), dan keterangan teknis.
  - Sheet `List Asset (Detail)` dibuat untuk menampung 16 entri aset lengkap (termasuk jalan, batu tebing, semak, cerobong polusi, reruntuhan terbakar, tanah retak, serta dekorasi air terjun/awan/burung).
  - Sheet `TS` diperbarui: mengunci jadwal baris **VFX Particles** (kegiatan B.5) pada **Pekan 10 – 11** dengan formatting fill aktif yang konsisten.
- **Penyusunan Prompt Blender MCP untuk Hermes Agent:** Menghasilkan prompt terstruktur untuk pembuatan model pulau melayang mencakup skala 1:1 meter, origin di (0,0,0), ketinggian tanah Y = 0 s/d 2.5m, slot material terpisah, dan penguncian 3 koordinat desa datar.

## 4. Detail Penting yang Harus Diingat
- **Koordinat Standar 3 Desa di Unity:**
  - `Village_1_Pollution`: $(-35, 2, -10)$
  - `Village_2_Wildfire`: $(40, 2, 20)$
  - `Village_3_Drought`: $(0, 2, 65)$
- **Parameter Terbang & Batas Pulau:**
  - Karakter naga terbang auto-forward pada ketinggian konstan $Y = 15\text{ m}$. Permukaan tanah pulau dibatasi pada $Y = 0.0\text{ m}$ s/d $2.5\text{ m}$ agar naga bebas hambatan.
  - Titik spawn naga: $(0, 15, -80)$ menghadap ke arah $+Z$.
  - Bounding box pulau: Lebar $X \approx 110\text{--}130\text{ m}$, Panjang $Z \approx 140\text{--}160\text{ m}$ (dari $Z = -50$ sampai $Z = +90$), kedalaman tebing kerucut terbalik hingga $Y = -25\text{ m}$ s/d $-35\text{ m}$.
- **Slot Material Pulau yang Dipersiapkan:**
  - `Mat_GrassTop` (`#5EA648`)
  - `Mat_CliffRock` (`#5C6B73` / `#6E5D4F`)
  - `Mat_DirtPath` (`#9E7B56`)
  - `Mat_LakeWater` (`#38A3A5`)
  - `Mat_VillagePlaza_Drought` (`#8B5A2B`) untuk kebutuhan `Color.Lerp` ke hijau subur (`#2E8B57`).

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] Berikan prompt pemodelan pulau kepada Hermes Agent melalui Blender MCP untuk menghasilkan file 3D `Island_Main_Base.fbx`.
- [ ] Lanjutkan pembahasan implementasi prototipe/desain GUI game (Lobby & Level Select, In-Game HUD, Gesture Feedback, Result Modal) di Unity berdasarkan arahan user selanjutnya.
- [ ] Perbarui progres pengerjaan pada `TS & List Asset.xlsx` seiring selesainya aset dari PIC masing-masing.

---
**Navigasi Obsidian:** [[00 - Overview]] · [[konteks]] · [[prd]] · [[rencana_desain_ui]] · [[Tasks]] · [[Devlog]]
