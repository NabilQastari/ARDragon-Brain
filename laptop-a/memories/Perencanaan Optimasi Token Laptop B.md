---
title: Perencanaan Optimasi Token Laptop B
type: session-memory
tags:
  - session-memory
  - laptop-a
  - token-optimization
  - rtk
  - ponytail
  - antigravityignore
date: 2026-11-18
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[planning_optimasi_token_laptop_b]]"
  - "[[kontrak_integrasi_ui_dan_progress]]"
---

# 🧠 Perencanaan Optimasi Token Laptop B

> **Ringkasan Eksekutif:** Sesi ini berfokus pada audit mendalam terhadap 6 kandidat alat dan konfigurasi optimasi token (RTK, Ponytail Antigravity IDE, Token-Saver Skill, Ponytail Original, Native Antigravity Skills, dan Rules/.antigravityignore) khusus untuk lingkungan eksekusi **Laptop B** (Unity 6 C# dan Python CV). Hasil evaluasi dirumuskan menjadi arsitektur solusi **"The Optimal Lean Stack"**, dituangkan ke dalam dokumen perencanaan resmi di vault, dan dilengkapi prompt eksekusi siap pakai untuk agent di Laptop B.

---

## 1. Tujuan & Konteks Sesi

- **Konteks Masalah:** Laptop B yang menangani eksekusi kode Unity C# dan modul Python CV rentan mengalami pemborosan token (*token bloat*) akibat output log terminal pengujian (`pytest`) yang panjang, pemindaian direktori build/cache Unity dan Python (`Library/`, `Temp/`, `__pycache__/`, `*.meta`), serta potensi gaya penulisan kode berlebihan (*over-engineering*).
- **Target Utama Sesi:**
  1. Menganalisis 6 kandidat alat optimasi token berdasarkan 7 parameter teknis ketat (kompatibilitas Antigravity, jenis token yang dihemat, otomatisasi, tumpang-tindih, potensi konflik arsitektur, overhead token, dan bukti verifikasi).
  2. Menyusun tabel evaluasi komparatif dengan kategori rekomendasi: *Highly Recommended*, *Recommended*, *Optional*, dan *Not Recommended*.
  3. Merumuskan arsitektur kombinasi paling efektif (*The Optimal Lean Stack*) dengan overhead seminimal mungkin tanpa menurunkan kualitas koding, akurasi, dan kepatuhan kontrak arsitektur Unity.
  4. Menyimpan dokumen perencanaan ke dalam brain vault (`brain/laptop-a/planning_optimasi_token_laptop_b.md`) dan menyiapkan prompt eksekusi siap pakai untuk Laptop B.

---

## 2. Keputusan Desain / Arsitektur / Teknis

### A. Hasil Evaluasi & Pengelompokan 6 Kandidat
1. **Antigravity Rules & `.antigravityignore` (`Highly Recommended`):**
   - 100% native bawaan, 0 overhead token input. Memotong kebocoran token paling fatal (5.000–30.000+ token per turn) dengan memblokir scanning folder build/cache Unity (`Library/`, `Temp/`, `obj/`, `*.meta`) dan Python (`__pycache__/`, `.pytest_cache/`, `.venv/`).
2. **Native Antigravity Agent Skills (`Highly Recommended`):**
   - 100% native via mekanisme *progressive disclosure* (hanya memuat nama dan deskripsi singkat ~30 token, isi `SKILL.md` baru dibaca saat relevan).
   - *Keputusan Arsitektur:* 28 skill Blender di `.agents/skills/blender-*` diisolasi di Laptop A dan tidak disalin ke Laptop B agar tidak menyia-nyiakan ~900 token deskripsi di system prompt awal Laptop B.
3. **RTK (Rust Token Killer) — `rtk-ai/rtk` (`Recommended`):**
   - Berupa executable binary Rust (`rtk.exe` di Windows) yang memangkas output log terminal CLI (`git status`, `git diff`, `pytest`) hingga 60–80%.
   - *Keputusan Integrasi Windows:* Karena transparent hook level-OS di Windows PowerShell tidak otomatis, integrasi dilakukan melalui instruksi terfokus di `AGENTS.md` (overhead ~35 token).
4. **Ponytail Original — `DietrichGebert/ponytail` (`Recommended` — Diadopsi via Rules):**
   - Mengadopsi prinsip minimal coding (YAGNI, standard library first, small targeted diffs) ke dalam aturan ringkas di `AGENTS.md` (~80 token overhead).
   - *Pengecualian Kritis (Guardrail):* Prinsip minimalis **TIDAK BOLEH** melanggar kontrak sinkronisasi di `kontrak_integrasi_ui_dan_progress.md`, struktur *Hybrid UI* (Live/Placeholder/Dormant), maupun atribut serialisasi Unity (`[SerializeField]`).
5. **Token-Saver Skill — `estherwasatester/token-optimization-skill` (`Optional / Tidak Perlu`):**
   - Klaim fitur "Model Routing" dan "Caching" tidak dapat dikendalikan dari level skill prompt Antigravity client. Prinsip hygiene-nya cukup dipenuhi oleh slash command native `/compact`.
6. **Ponytail Antigravity IDE — `rizalrepo/ponytail-antigravity-IDE` (`Not Recommended`):**
   - Merupakan fork repositori personal yang redundan 100% terhadap upstream resmi `DietrichGebert/ponytail`.

### B. Kombinasi "The Optimal Lean Stack" untuk Laptop B
Kombinasi 4 pilar efisien:
- **Pilar 1:** File `.antigravityignore` di root workspace Laptop B.
- **Pilar 2:** Binary `rtk.exe` via winget / PATH Windows.
- **Pilar 3:** Blok aturan efisiensi Ponytail-Lite di `AGENTS.md` Laptop B.
- **Pilar 4:** Isolasi skill non-relevan & disiplin `/compact` berkala.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset

1. **Pembuatan Laporan Audit & Evaluasi Artifact:**
   - Dihasilkan laporan komprehensif di artifact: [`analisis_optimasi_token_laptop_b.md`](file:///C:/Users/nmast/.gemini/antigravity/brain/aa5b27c3-5405-4b89-951a-df3a7f607856/analisis_optimasi_token_laptop_b.md).
2. **Penerbitan Dokumen Perencanaan di Vault Brain:**
   - Dibuat berkas perencanaan baru: [`brain/laptop-a/planning_optimasi_token_laptop_b.md`](file:///e:/Semester%205/Game/AR%20Dragon/brain/laptop-a/planning_optimasi_token_laptop_b.md) yang memuat spesifikasi lengkap `.antigravityignore`, blok aturan `AGENTS.md`, prosedur instalasi RTK, dan prompt eksekusi siap salin untuk Laptop B.
3. **Pembaruan Indeks Vault & Pelacakan Tugas:**
   - [`brain/00 - Overview.md`](file:///e:/Semester%205/Game/AR%20Dragon/brain/00%20-%20Overview.md): Menambahkan tautan dokumen `planning_optimasi_token_laptop_b` pada peta dokumen Laptop A.
   - [`brain/laptop-a/Tasks.md`](file:///e:/Semester%205/Game/AR%20Dragon/brain/laptop-a/Tasks.md): Menandai selesai tugas penyusunan perencanaan optimasi token dan menambahkan item monitor eksekusi di fokus aktif.
   - [`brain/laptop-a/Devlog.md`](file:///e:/Semester%205/Game/AR%20Dragon/brain/laptop-a/Devlog.md): Mencatat kronologi keputusan audit 6 kandidat dan perumusan *The Optimal Lean Stack*.
4. **Sinkronisasi Git Sub-repo `brain/`:**
   - Commit: `docs(laptop-a): add token optimization planning and execution prompt for laptop-b` (`d796efe`).
   - Push: Berhasil terunggah ke remote repository `origin main` di `https://github.com/NabilQastari/ARDragon-Brain.git`.

---

## 4. Detail Penting yang Harus Diingat

- **Anti-Collision Rule:** Seluruh perubahan di sesi ini hanya menyentuh `brain/laptop-a/` dan `brain/00 - Overview.md`. Tidak ada file di `brain/laptop-b/` yang diubah.
- **Overhead Token Stack Baru:**
  - `.antigravityignore`: 0 token.
  - Aturan RTK di `AGENTS.md`: ~35 token.
  - Aturan Ponytail-Lite di `AGENTS.md`: ~80 token.
  - Total overhead: ~115 token per turn, berbanding potensi penghematan ribuan input token per perintah terminal dan ratusan output token per generasi kode.
- **Aturan Batasan RTK di PowerShell:** Perintah yang memakai piping native PowerShell (`|`, `Select-Object`, `Where-Object`) tidak boleh dibungkus dengan `rtk` mentah karena RTK dirancang untuk process argument standar.
- **Batasan Ponytail:** Tidak boleh menghapus `[SerializeField]`, method jembatan event UI, atau penanda sinkronisasi yang diwajibkan oleh arsitektur Laptop A.

---

## 5. Tugas Tersisa & Langkah Selanjutnya

1. **Eksekusi di Laptop B:**
   - Berikan prompt eksekusi yang telah disiapkan di [[planning_optimasi_token_laptop_b]] §5 ke AI Agent di Laptop B saat sesi kerja Laptop B berikutnya dibuka.
2. **Monitoring Pasca-Instalasi di Laptop B:**
   - Verifikasi bahwa `rtk git status` dan `rtk pytest` berjalan mulus di Windows PowerShell Laptop B.
   - Pastikan file skrip `.cs` dan `.py` tidak terblokir oleh `.antigravityignore`.
3. **Lanjut Fokus Sesi 4:**
   - Setelah optimasi token aktif di Laptop B, lanjutkan eksekusi Checklist Pra-Sesi 4 dan instalasi skill VFX Effekseer (`kanaworksai-hue/effekseer-vfx-skill`) sesuai [[rencana_skill_vfx_effekseer_laptop_b]].

---

### 🧭 Navigasi Vault
- 🏠 [[00 - Overview]]
- 📋 [[Tasks]]
- 📝 [[Devlog]]
- ⚡ [[planning_optimasi_token_laptop_b]]
- 🤝 [[kontrak_integrasi_ui_dan_progress]]
