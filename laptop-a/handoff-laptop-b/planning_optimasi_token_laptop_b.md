---
title: Planning Implementasi & Konfigurasi Optimasi Token di Laptop B
type: planning
tags:
  - planning
  - token-optimization
  - laptop-a
  - laptop-b
  - tooling
  - rtk
  - ponytail
  - antigravity
date: 2026-11-18
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[laptop B Patch 1 planning]]"
  - "[[kontrak_integrasi_ui_dan_progress]]"
---

# ⚡ Planning Implementasi & Konfigurasi Optimasi Token di Laptop B

> **Tujuan Dokumen:** Memberikan cetak biru implementasi, spesifikasi konfigurasi, dan panduan langkah demi langkah bagi **Laptop B** untuk memasang dan mengonfigurasi tumpukan optimasi token (*The Optimal Lean Stack*). Hal ini bertujuan menekan konsumsi token input/output secara drastis tanpa mengurangi ketelitian koding Unity 6 C#, akurasi Python CV, maupun kepatuhan terhadap kontrak arsitektur proyek.

---

## 📌 1. Latar Belakang & Analisis Titik Kebocoran Token (Laptop B)

Berdasarkan audit teknis mendalam terhadap workflow harian Laptop B, terdapat 4 sumber kebocoran token utama:

1. **Log Output Terminal (`run_command`):**
   - Menjalankan test suite Python CV (`pytest`, `test_additional_gestures.py`, `test_e2e_flight_physics.py`, `test_tahap1_face_tracking.py`) dan operasi Git (`git diff`, `git status`) menghasilkan ratusan baris log teks mentah. Setiap baris masuk ke dalam riwayat konteks percakapan sebagai **input token** berulang di setiap turn berikutnya.
2. **Indexing & Scanning File Sampah / Binary:**
   - Direktori build dan metadata Unity (`Library/`, `Temp/`, `obj/`, `Build/`, `*.meta`) serta cache Python (`__pycache__/`, `.pytest_cache/`, `.venv/`) berisi ribuan berkas non-kode. Jika terindeks atau terpindai oleh pencarian global agen, puluhan ribu token terbuang seketika.
3. **Over-Engineering & Boilerplate Kode Berlebihan:**
   - LLM secara alami cenderung menulis class pembungkus, abstraksi bertingkat, atau kode penanganan yang tidak diminta. Hal ini menghabiskan kuota **output token** yang mahal dan memperpanjang berkas kode sumber.
4. **Overhead Skill yang Tidak Relevan:**
   - Adanya puluhan skill yang tidak sesuai dengan fokus kerja Laptop B (misalnya 28 skill Blender yang hanya digunakan di Laptop A) membebani metadata deskripsi pada system prompt awal (~800–1.000 token setiap turn percakapan).

---

## 🛠️ 2. Arsitektur Solusi: "The Optimal Lean Stack"

Solusi ini menggabungkan 4 instrumen dengan overhead paling minim namun memberikan dampak efisiensi terbesar:

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    THE OPTIMAL LEAN STACK (LAPTOP B)                    │
├───────────────────┬───────────────────┬────────────────┬────────────────┤
│    INSTRUMEN 1    │    INSTRUMEN 2    │  INSTRUMEN 3   │  INSTRUMEN 4   │
│.antigravityignore │  RTK (rtk.exe)    │ Ponytail-Lite  │ Clean Skills & │
│  (Workspace Root) │  (CLI Compressor) │  (AGENTS.md)   │ Session Hygiene│
├───────────────────┼───────────────────┼────────────────┼────────────────┤
│ Memblokir scan    │ Memampatkan log   │ Mencegah kode  │ Memangkas meta │
│ Library, Temp,    │ git/pytest/test   │ over-engineer  │ skill blender &│
│ cache, binary     │ hingga 60–80%     │ & boilerplate  │ compact sesi   │
├───────────────────┼───────────────────┼────────────────┼────────────────┤
│ Overhead: 0 token │ Overhead: ~35 tok │ Overhead:~80tok│ Overhead: 0 tok│
│ Hemat: Masif      │ Hemat: Signifikan │ Hemat: Output  │ Hemat: Konstan │
└───────────────────┴───────────────────┴────────────────┴────────────────┘
```

---

## 📋 3. Rincian Konfigurasi & Berkas Siap Pasang

### A. Berkas `.antigravityignore` (Root Workspace)
Tempatkan file ini langsung di root workspace Laptop B: `e:\Semester 5\Game\AR Dragon\.antigravityignore`.

```gitignore
# =============================================================================
# Antigravity Context & Indexing Ignore Rules — Laptop B (Unity + Python CV)
# =============================================================================

# --- Unity Engine Build, Temp & Metadata ---
/[Ll]ibrary/
/[Tt]emp/
/[Oo]bj/
/[Bb]uild/
/[Bb]uilds/
/[Ll]ogs/
/[Uu]ser[Ss]ettings/
/[Mm]emoryCaptures/
*.meta
*.pidb
*.unitypackage
*.apk
*.aab

# --- Visual Studio / IDE Caches ---
.vs/
.idea/
.vscode/
*.csproj
*.sln
*.suo
*.user
*.userprefs

# --- Python CV Caches & Environments ---
**/__pycache__/
**/*.pyc
**/*.pyo
**/*.pyd
.pytest_cache/
.coverage
htmlcov/
.mypy_cache/
.tox/
.venv/
venv/
ENV/
env/

# --- Heavy 3D Models & Raw Assets (Dikelola di Laptop A) ---
*.blend
*.blend1
*.fbx
*.obj
*.mtl
*.exr
*.psd
*.tif
*.tiff

# --- Compressed & Office Archives ---
*.zip
*.tar
*.gz
*.7z
*.rar
*.xlsx
*.xls
~$*
*.docx
*.pdf

# --- Media & Test Record Dumps ---
recordings/
captures/
debug_frames/
*.mp4
*.avi
*.mov

# --- Obsidian Brain Laptop A Isolation (Read-Only) ---
brain/laptop-a/memories/
brain/.obsidian/
```

---

### B. Konfigurasi Aturan Efisiensi di `AGENTS.md` (Laptop B)
Tambahkan blok aturan berikut ke dalam `AGENTS.md` di Laptop B:

```markdown
## ⚡ Aturan Efisiensi Token & Eksekusi Minimalis (Laptop B)

1. **Kompresi Terminal dengan RTK:**
   - Gunakan selalu awalan `rtk` untuk perintah eksekusi terminal berikut guna memangkas output teks berlebihan:
     - Git: `rtk git status`, `rtk git diff`, `rtk git log -n 5`
     - Test Python: `rtk pytest`, `rtk python -m pytest <path>`
     - Package: `rtk pip list`, `rtk pip install ...`
   - *Pengecualian:* Jangan bungkus perintah native PowerShell yang menggunakan operator pipe (`|`), `Select-Object`, atau cmdlet khusus Windows.

2. **Prinsip Coding Minimalis (Ponytail-Lite / YAGNI):**
   - **No Over-Engineering:** Tulis implementasi paling sederhana, padat, dan langsung menjawab kebutuhan tugas saat ini. Jangan menambahkan wrapper, layer abstraksi baru, atau helper method spekulatif yang tidak diminta.
   - **Standard & Built-in First:** Manfaatkan API bawaan Unity engine (`Mathf`, `Vector3`, `SmoothDamp`, `Physics`) dan standard library Python (`math`, `time`, `collections`) daripada membuat fungsi matematika/utilitas kustom.
   - **Toleransi Kontrak Arsitektur (Wajib):** Prinsip minimalis **TIDAK BOLEH** melanggar atau menghapus kontrak sinkronisasi di `kontrak_integrasi_ui_dan_progress.md`, struktur *Hybrid UI* (Live/Placeholder/Dormant), maupun atribut serialisasi Unity (`[SerializeField]`, namespace `DragonWingAR.*`).
   - **Targeted Diff / Edit:** Gunakan penggantian blok kode yang spesifik dan terkonsentrasi; hindari memformat ulang atau menulis ulang berkas secara utuh hanya untuk perubahan beberapa baris kode.

3. **Session Hygiene:**
   - Jalankan slash command `/compact` setiap kali berpindah antar-fokus tugas besar (misalnya selesai verifikasi kamera dinamis dan hendak masuk ke Sesi 4 Village Builder) agar riwayat percakapan tidak membengkak.
```

---

## 🚀 4. Prosedur Instalasi & Verifikasi Bertahap

### Langkah 1: Pasang Binary RTK di Laptop B
1. Buka PowerShell di Laptop B dan jalankan installer resmi:
   ```powershell
   winget install rtk-ai.rtk
   ```
   *Catatan alternatif:* Jika `winget` tidak tersedia, unduh `rtk-x86_64-pc-windows-msvc.zip` dari [github.com/rtk-ai/rtk/releases](https://github.com/rtk-ai/rtk/releases), ekstrak berkas `rtk.exe`, lalu letakkan di direktori yang terdaftar pada system `PATH` (misal `C:\Windows\System32` atau folder bin lokal).
2. Verifikasi instalasi:
   ```powershell
   rtk --version
   ```
   *(Harus menampilkan versi RTK yang aktif).*

### Langkah 2: Pasang File `.antigravityignore`
1. Buat file `.antigravityignore` di root workspace `e:\Semester 5\Game\AR Dragon\.antigravityignore` sesuai isi spesifikasi di atas.
2. Pastikan file tidak tersembunyi dan dapat dibaca oleh Antigravity.

### Langkah 3: Update `AGENTS.md`
1. Buka berkas `AGENTS.md` di Laptop B.
2. Sisipkan blok **`## ⚡ Aturan Efisiensi Token & Eksekusi Minimalis (Laptop B)`** pada bagian panduan kerja.

### Langkah 4: Audit & Isolasi Skill di Laptop B
1. Periksa direktori `.agents/skills/` di Laptop B.
2. Pastikan skill yang aktif di Laptop B hanya yang relevan dengan tugas eksekusinya:
   - Tidak perlu menyalin 28 skill Blender dari Laptop A ke Laptop B.
   - Simpan paket skill `effekseer-vfx-skill` saat pengerjaan VFX Sesi 4 dimulai.
   - Biarkan skill native Antigravity bekerja secara otomatis on-demand.

### Langkah 5: Uji Coba Verifikasi (Sanity Check)
Jalankan uji coba di terminal Laptop B:
```powershell
rtk git status
rtk pytest ARDragonSkyGuardian_CV/tests/test_tahap1_face_tracking.py
```
Periksa output: terminal harus menampilkan ringkasan bersih yang padat tanpa log chatter berlebihan.

---

## 💬 5. Prompt Eksekusi untuk Laptop B (Siap Copy-Paste)

Gunakan prompt di bawah ini untuk menginstruksikan agent di **Laptop B** agar melakukan instalasi dan konfigurasi di atas secara otomatis:

```markdown
Halo Agent Laptop B! Mohon eksekusi rencana optimasi token untuk lingkungan kerja Laptop B berdasarkan dokumen panduan arsitektur `brain/laptop-a/planning_optimasi_token_laptop_b.md`.

Ikuti langkah-langkah kerja berikut secara tertib:

1. Buat file `.antigravityignore` di root workspace dengan isi yang mengecualikan:
   - Artefak build dan cache Unity (`Library/`, `Temp/`, `obj/`, `Build/`, `*.meta`).
   - Cache Python (`**/__pycache__/`, `**/*.pyc`, `.pytest_cache/`, `.venv/`).
   - File biner 3D mentah dan arsip berat (`*.blend`, `*.fbx`, `*.obj`, `*.zip`, `*.xlsx`, `*.png`, `*.jpg`).
   (Salin template lengkap dari `brain/laptop-a/planning_optimasi_token_laptop_b.md` Bagian 3A).

2. Periksa apakah `rtk` (Rust Token Killer) sudah terpasang di sistem dengan menjalankan:
   `rtk --version`
   Jika belum terpasang, pasang menggunakan `winget install rtk-ai.rtk`. Jika winget tidak tersedia, beritahukan agar binary `rtk.exe` ditaruh di PATH.

3. Perbarui berkas `AGENTS.md` di bagian pedoman Laptop B dengan menambahkan section:
   `## ⚡ Aturan Efisiensi Token & Eksekusi Minimalis (Laptop B)`
   yang memuat:
   - Kewajiban awalan `rtk` untuk perintah git status/diff dan pytest.
   - Prinsip coding minimalis Ponytail (YAGNI, standard lib first, small targeted diffs).
   - Penegasan bahwa aturan minimalis tidak boleh melanggar kontrak UI/arsitektur di `kontrak_integrasi_ui_dan_progress.md`.

4. Lakukan verifikasi singkat:
   - Jalankan `rtk git status` untuk memastikan kompresi terminal aktif.
   - Pastikan tidak ada skrip C# (`.cs`) atau skrip Python (`.py`) yang terblokir oleh `.antigravityignore`.

5. Catat hasil eksekusi ini ke dalam `brain/laptop-b/Devlog.md` dan centang tugas di `brain/laptop-b/Tasks.md`.
```
