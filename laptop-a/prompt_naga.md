---
title: "Prompt Remodel Blender MCP — Ancient Dew Water Dragon"
tags:
  - blender
  - prompt
  - dragon
  - 3d-model
  - laptop-a
status: reference
owner: Laptop A
updated: 2026-11-13
related:
  - "[[00 - Overview]]"
  - "[[konteks]]"
  - "[[prd]]"
---

# 🐉 PROMPT REMODEL BLENDER MCP: NAGA "ANCIENT DEW WATER DRAGON"

> **Mode kerja: REMODEL model yang sudah ada, bukan membuat dari awal.** Proyek: AR Dragon Sky Guardian (Unity).
> **Referensi bentuk dan gaya:** dua gambar (lembar model: FRONT, SIDE, BACK, TOP, 3/4; lembar pose: GLIDE, TURN, FLAP UP/DOWN, HOVER, SPRAY WATER, DEW RAIN).
> **Kondisi awal:** `dragon_gen.py` (generator prosedural, Blender 5.0.1, rig 35 tulang, 7 klip, ekspor FBX terverifikasi) dan model hasilnya di scene. Semua angka kondisi awal ada di skrip, jadi **jangan disalin ulang ke prompt**.
> **Cakupan:** perbaikan bentuk, proporsi, dan detail mesh agar mirip referensi, **tanpa merusak rig, animasi, dan kompatibilitas Unity**.

## 🧭 CARA PAKAI

1. Kirim **Bagian A + Langkah 0** bersama kedua gambar referensi. Tunggu laporan, lalu jawab **"Mulai"**.
2. Kirim **Task 1 sampai Task 5** satu per satu, dengan urutan **kaki → badan → sayap → kepala → ekor**. Tiap task berhenti di akhir sampai kamu menjawab **"Lanjut"**.
3. Urutan ini sengaja dari fondasi ke tepi: kaki dan badan lebih dulu karena sayap menempel di bahu, kepala menempel di leher, dan ekor menyambung dari pinggul.

---

# BAGIAN A: KONTEKS DAN ATURAN (berlaku untuk semua task)

## 🎯 Peran dan misi
Kamu adalah Character/Technical Artist Blender senior. Misimu: **memperbaiki** naga yang sudah ada sampai mirip kedua gambar referensi (ramah, gemuk, imut, heroik), tetap lolos `validate()`, dan tetap bisa dipakai Unity lewat `DragonAnimationBridge`. Kerjamu iteratif per wilayah: ubah, ukur, bandingkan dengan referensi, perbaiki, lapor.

Konteks gameplay yang memengaruhi desain:
- Naga terbang otomatis ke depan, kamera pengejar berada **di belakang dan di atas** (lokal sekitar `(0, 4, -12)`). **Punggung, sayap atas, dan siluet dari belakang paling sering terlihat**, jadi detail terbaik ditaruh di sisi atas.
- Roll belok (`-turnInput * 35` pada sumbu Z) dilakukan skrip Unity. **Klip tidak boleh membakar roll badan.**
- Animator: `Bool IsHovering`, `Int ActionType`, `Float TurnBlend`.

## 📌 Sumber kebenaran
1. **Gambar = acuan bentuk dan gaya. Angka di prompt ini = acuan ukuran.** Jika gambar tidak konsisten antar-tampilan (misal TOP tampak lebih pendek dari SIDE), ikuti angka.
2. **`dragon_gen.py` asli tidak boleh diubah.** Semua perubahan dikerjakan di salinannya, `dragon_gen_v2.py`.

## 🔒 Aturan keras
1. **Jangan memanggil `stage_scene()` dan `run_all()`.** Keduanya menghapus semua objek berawalan `Dragon`. Jangan pakai `bpy.ops.wm.read_factory_settings` (bisa memutus MCP).
2. **Ubah bentuk lewat konstanta dan fungsi di file `dragon_gen_v2.py`, lalu `exec` ulang seluruh file.** Jangan menimpa konstanta di memori setelah `exec`, karena `RIG` dihitung saat file dimuat (posisi tulang kaki, sayap, dan lain-lain akan basi dan tidak cocok dengan mesh).
3. Satu panggilan `execute_blender_code` = satu langkah (±20 detik). Pisahkan: (a) `stage_mesh(); stage_rig()`, (b) `stage_anim()`, (c) `validate()`.
4. **Tanpa aset eksternal**, tanpa tekstur gambar, tanpa Automatic Weights. Seed tetap **7**.
5. **Tidak menambah material baru** (tetap 13, urutan dan hex tetap). Detail baru memakai material yang ada: lubang hidung dan garis mulut = `M_Pupil`, tulang daun sirip ekor = `M_BackDark`, kilau mata = `M_Pearl`.
6. **Satu task hanya boleh mengubah wilayahnya sendiri** (lihat tabel kepemilikan). Dijaga oleh *region fingerprint* di Langkah 0.
7. **Jangan menimpa file lama.** Sebelum tiap task: salin `dragon_gen_v2.py` menjadi `dragon_gen_v2_pre_T{n}.py`. Setelah lulus: simpan `Dragon_R{n}_{wilayah}.blend` (contoh `Dragon_R1_Kaki.blend`).
8. Jika error: perbaiki sekecil mungkin, jalankan ulang langkah itu, laporkan perbaikannya. Maksimal **3 putaran perbaikan** per task; jika masih ada selisih, laporkan dan tanya.

## 🗺️ Kepemilikan wilayah
| Task | Wilayah | Isi | Tulang yang boleh digeser |
| :-: | :--- | :--- | :--- |
| 1 | **Kaki** | tabung kaki, telapak, cakar (4 kaki) | `Leg_*`, `Foot_*` |
| 2 | **Badan** | leher, torso, pinggul (cincin `y ≥ -1.70`), duri `y ≥ -1.70`, kristal dada, lumut bahu, pewarnaan perut | `Body`, `Neck_01..03`, `Crystal` (`Root` tetap) |
| 3 | **Sayap** | lengan, 3 jari, membran, tetes embun (kiri dan kanan) | `Wing_*` |
| 4 | **Kepala** | kepala, rahang, mata, tanduk, sirip telinga, hidung, garis mulut | `Head`, `Jaw` |
| 5 | **Ekor** | cincin `y ≤ -2.20`, duri ekor, sirip ekor | `Tail_01..04`, `TailFin` |

Catatan sambungan: cincin `y = -1.70` (pangkal ekor) **milik Badan** dan dibekukan setelah Task 2. Cincin ekor pertama (`y = -2.20`) harus menyambung mulus dengannya. Cincin leher paling atas (`y = 1.70`) juga dibekukan setelah Task 2 agar Task 4 punya titik sambung yang pasti. Tabung badan dan ekor adalah **satu tabung**, jadi jumlah sisi (`n`) ditetapkan di Task 2 dan berlaku untuk keduanya.

## 🔁 Invarian (tidak boleh berubah di task mana pun)
- Nama dan hierarki **35 tulang**, 13 material (urutan `MAT_ORDER`), 7 nama klip, arah naga **+Y**, sisi `.R = +X`, titik asal di tengah torso.
- Hasil `validate()` harus tetap lolos: rentang sayap 11.5-12.5 m, panjang hidung-ekor 7.3-7.9 m (**target ≤ 7.85**), rasio 1.5-1.7, tinggi 2.2-2.8 m, simetri < 1 cm, ≤ 8.000 segitiga, semua vertex berbobot (jumlah = 1, maks 4 pengaruh).
- T-pose terbang lurus tetap menjadi pose dasar.
- Gaya **ramah dan heroik**.

## 🔺 Anggaran segitiga (batas atas per wilayah; ukur baseline di Langkah 0)
| Kaki | Badan | Sayap | Kepala | Ekor | Total anggaran | Cadangan |
| :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| ≤ 1.000 | ≤ 1.200 | ≤ 1.500 | ≤ 1.800 | ≤ 700 | 6.200 | 1.800 (batas total 8.000) |

Butuh lebih dari anggaran? Minta izin dulu, lalu ambil dari cadangan.

## ♻️ Siklus standar tiap task
1. **Fingerprint awal:** jalankan `region_fingerprint()` dan `tri_report()`, simpan hasilnya.
2. **Rencana singkat:** tabel perubahan (parameter, lama, baru, alasan). Langsung kerjakan, kecuali ada konflik dengan invarian (jika ada, tanya).
3. **Backup** skrip, lalu edit wilayah task di `dragon_gen_v2.py`.
4. **Regenerasi:** `exec` ulang file → `stage_mesh(); stage_rig()` → `stage_anim()` → `validate()`.
5. **Guard:** `region_fingerprint()` lagi. **Fingerprint wilayah lain harus identik** dengan sebelum task. Jika berubah, cari penyebabnya dan perbaiki sebelum lanjut.
6. **Uji deformasi dan animasi** khusus wilayah (di tiap task). Screenshot dari `DCam_Front`, `DCam_Side`, `DCam_Top`, `DCam_Back`, `DCam_Hero`, dibandingkan dengan screenshot sebelum task.
7. **Bandingkan dengan referensi** lewat checklist task.
8. Simpan `.blend`, kirim laporan, **berhenti**.

## 🛡️ Format laporan (akhir tiap task)
1. Hasil `validate()` (salin baris PASS/FAIL) dan hasil guard fingerprint.
2. Tabel perubahan parameter (nama, lama, baru, alasan) dan jumlah segitiga wilayah (sebelum dan sesudah, total).
3. Checklist task terisi (cocok / selisih + usulan).
4. Screenshot sebelum dan sesudah.
5. Masalah tersisa dan usulan.
6. Pertanyaan konfirmasi.

---

# 📥 LANGKAH 0: AUDIT DAN PERSIAPAN (jangan mengubah bentuk apa pun)

**0a. Baca dan analisis.** Buka kedua gambar referensi, `dragon_gen.py`, serta [[prd]] (DragonAnimationBridge) dan [[konteks]] jika ada. Panggil `get_scene_info` dan laporkan versi Blender serta objek di scene. Jika ada objek bawaan (`Cube`, `Light`, `Camera`), **tanyakan dulu** sebelum menghapus.

**0b. Tentukan mode kerja.** Cek apakah mesh di scene masih identik dengan keluaran skrip:
```python
exec(open(r"<PATH>/dragon_gen.py", encoding="utf-8").read(), globals())
build_parts()                      # hanya di memori, tidak menyentuh scene
cur = np.array([v.co[:] for v in bpy.data.objects["Dragon_Mesh"].data.vertices])
ref = np.array(PARTS["v"])
print(len(cur), len(ref), cur.shape == ref.shape and np.allclose(cur, ref, atol=1e-5))
np.savez("/tmp/orig_parts.npz", v=ref)   # untuk uji refactor di 0c
```
- **Mode A (identik):** semua task memakai alur skrip di atas (regenerasi lewat `stage_mesh()`).
- **Mode B (berbeda, ada edit manual di Blender):** **berhenti dan tanya saya.** Pilihan: (1) skrip menjadi sumber kebenaran dan edit manual dibuang, atau (2) edit di tempat lewat `bmesh` dengan topeng grup vertex dan material, **tanpa** `stage_mesh()`, vertex group dipertahankan. Jangan memilih sendiri.

**0c. Refactor tanpa mengubah hasil** (salin `dragon_gen.py` ke `dragon_gen_v2.py`, lalu):
1. Pecah `build_parts()` menjadi `build_legs()`, `build_body()`, `build_wings()`, `build_head()`, `build_tail()`. **Urutan `commit()` harus sama dengan aslinya** (badan+ekor, kepala, jaw, mata/tanduk/telinga, duri, kristal+lumut, kaki, sayap, sirip ekor) agar hasilnya persis sama.
2. Pecah `BODY_RINGS` menjadi `TORSO_RINGS` (`y ≥ -1.70`) dan `TAIL_RINGS` (`y ≤ -2.20`), dengan `BODY_RINGS = TORSO_RINGS + TAIL_RINGS`. Pecah juga daftar `y` duri menjadi duri badan dan duri ekor.
3. Tambahkan argumen `region` pada `commit()` dan simpan `PARTS["region"]` per face (`"kaki"`, `"badan"`, `"sayap"`, `"kepala"`, `"ekor"`).
4. Pisahkan `stage_cameras()` (kamera dan lampu saja, **tanpa menghapus objek Dragon**) dari `stage_scene()`. Jika kamera `DCam_*` belum ada di scene, buat dengan `stage_cameras()`.
5. Tambahkan helper berikut:
```python
import hashlib
def region_fingerprint():
    build_parts(); V = np.array(PARTS["v"]); out = {}
    for rg in sorted(set(PARTS["region"])):
        idx = sorted({i for f, r in zip(PARTS["f"], PARTS["region"]) if r == rg for i in f})
        out[rg] = hashlib.md5(np.round(V[idx], 4).tobytes()).hexdigest()[:8]
    return out
def tri_report():
    t = {}
    for f, r in zip(PARTS["f"], PARTS["region"]): t[r] = t.get(r, 0) + len(f) - 2
    t["TOTAL"] = sum(t.values()); return t
def insert_rings(rings, n_between=1):
    """Sisipkan cincin interpolasi halus (Catmull-Rom pada y, z, rx, rz) di antara tiap pasangan cincin."""
    ...   # tulis sendiri; cincin asli tetap berada di posisinya
```
6. **Uji identitas:** `build_parts()` di `dragon_gen_v2.py` harus menghasilkan `PARTS["v"]` yang `np.allclose` dengan `/tmp/orig_parts.npz`, dengan jumlah face yang sama. Jika tidak identik, perbaiki refactor. **Jangan lanjut sebelum identik.**

**0d. Baseline.** Laporkan `tri_report()` (segitiga per wilayah) dan `region_fingerprint()` awal. Jalankan `stage_rig(); stage_anim(); validate()` pada skrip v2 (harus sama dengan sebelum refactor). Ambil screenshot 5 kamera sebagai `before_*`.

**0e. Analisis dan berhenti.** Tulis ringkasan maksimal 10 poin: selisih terbesar model saat ini terhadap referensi per wilayah (kaki, badan, sayap, kepala, ekor), plus daftar ketidakkonsistenan gambar. Lalu **berhenti** sampai saya menjawab "Mulai".

> Jika kamu tidak bisa membaca file lokal, minta saya menempelkan isi `dragon_gen.py`.

---

# 🦵 TASK 1: REMODEL KAKI

**Tujuan:** kaki lebih **pendek, gemuk, dan imut**, dilipat di bawah badan, dengan telapak dan 3 cakar yang jelas. Hanya wilayah **kaki** (`LEG_F`, `LEG_H`, `build_legs()`).

**Perubahan yang diminta** (angka = titik awal, boleh disetel ±):
| # | Perubahan | Titik awal |
| :-: | :--- | :--- |
| 1 | Pendekkan rantai tulang kaki terhadap titik pangkalnya (`P0`): `P'i = P0 + k·(Pi − P0)` | `k ≈ 0.87` (pendek 10-15%). Telapak naik dari z ≈ -1.0 menjadi ± -0.9 |
| 2 | Gemukkan radius tiap segmen | ×1.15-1.20 (depan ≈ 0.19, 0.145, 0.11, 0.13; belakang ≈ 0.25, 0.19, 0.12, 0.14) |
| 3 | Tabung lebih halus: sisi 6 → 8, tambah 1 titik tengah di lutut agar tekukan membulat | ±8 sisi, 5 titik jalur |
| 4 | Tambah **telapak** membulat (bola gepeng, `M_Body`, bobot kaku ke `Foot_*`) sebelum cakar | r ≈ 0.13, skala z ≈ 0.6 |
| 5 | Cakar 3 buah tetap, sedikit lebih tebal dan pendek (`M_Pearl`) | radius 0.05, panjang ±0.22 |
| 6 | Pangkal kaki harus **tersembunyi di dalam torso** (tidak ada celah dan tidak menembus kulit luar) | Periksa dari `DCam_Side` dan `DCam_Hero` |

**Dibekukan:** semua wilayah selain kaki. Sumbu simetri X.
**Anggaran:** ≤ 1.000 segitiga. **Awasi:** tinggi total tetap 2.2-2.8 m setelah telapak naik. Perbarui angka di dokumentasi ("kaki menyentuh Z ≈ ...") dengan nilai hasil ukur.

**Uji deformasi dan animasi:**
- Putar manual: `Leg_F_Upper.R` X ±20°, `Leg_H_Upper.L` X ±20°, `Foot_H.R` X ±15°. Cari paha robek, kaki menembus perut atau ekor, bobot tulang kaki "menarik" torso.
- Putar klip `Dragon_Hover` di frame 1, 7, 13, 19: kaki menjuntai tanpa menembus badan.
- Simetri kiri-kanan tetap < 1 cm.

| # | Checklist referensi | Hasil |
| :-: | :--- | :--- |
| 8 | Empat kaki dilipat, 3 cakar per kaki, proporsi gemuk dan imut? | |
| K1 | Kaki depan lebih kecil dari kaki belakang (seperti referensi)? | |
| K2 | Tidak ada celah di pangkal kaki dari 5 kamera? | |

**Kriteria lulus:** `validate()` semua PASS, segitiga kaki ≤ 1.000, fingerprint wilayah lain identik, uji deformasi bersih, checklist minimal 2 dari 3 cocok.

**🛑 Pertanyaan akhir:** "Kaki sudah sesuai referensi? Lanjut ke Task 2 (badan)?"

---

# 🫁 TASK 2: REMODEL BADAN (leher, torso, pinggul)

**Tujuan:** torso **lebih gemuk**, transisi leher-dada **mulus**, duri punggung lebih besar dan bulat, kristal dan lumut mengikuti bentuk baru. Wilayah **badan** (`TORSO_RINGS`, duri badan, kristal, lumut, `shade_mats`).

**Perubahan yang diminta:**
| # | Perubahan | Titik awal |
| :-: | :--- | :--- |
| 1 | Gemukkan torso (cincin `y = 0.50` sampai `-1.25`) | rx dan rz ×1.10-1.15. Bahu (`y = 0.15`) tetap titik akar sayap, **titik `S` sayap harus tetap di dalam volume badan** |
| 2 | Tambah cincin di leher-dada dan leher-bahu dengan `insert_rings` (kurva halus, bukan siku) | +4 sampai +6 cincin pada `y 1.70 → 0.15` |
| 3 | Penampang tabung 8 → **10 sisi** (berlaku juga untuk ekor, karena satu tabung) | `n = 10` |
| 4 | Pita perut krem tetap bergantian, tetapi **lebar pita ≈ semula** meski cincin bertambah (pakai `(sg // k) % 2`) | ±0.4-0.5 m per pita |
| 5 | Duri badan: lebih besar, lebih bulat (kerucut tumpul 6 sisi atau bentuk tetes), tinggi 0.26 → 0.12 | 7-8 duri dari `y = 1.40` sampai `-1.70`. **Total duri badan + ekor = 14-16** (ekor: Task 5) |
| 6 | Kristal dada: facet lebih banyak (mis. 12-14 face), tetap bersinar, tetap tulang `Crystal`, posisi tidak menembus kulit dada setelah dada gemuk | Tinggi ±0.17-0.2 |
| 7 | Lumut bahu: 2 bercak mengikuti permukaan bahu baru | Dekat `(±0.42, 0.12, 0.56)`, sesuaikan |
| 8 | Jika garis tengah cincin bergeser > 0.05 m dari garis tulang, geser `Body` / `Neck_01..03` di `RIG` agar mengikuti | Sebutkan di laporan |

**Dibekukan:** cincin `y = 1.70` dan `y = -1.70` (setelah task ini), semua wilayah lain. **Anggaran:** ≤ 1.200 segitiga.
**Awasi:** `S` dan `R` sayap tetap menempel ke badan (Task 3 bergantung pada ini).

**Uji deformasi dan animasi:**
- `Neck_01` dan `Neck_02` X ±25°, `Neck_03` X ±20°, `Body` X ±10°.
- Cari: leher mengerut, penampang gepeng, **celah antara leher dan kepala** saat leher ditekuk (kepala adalah objek kaku), kristal menembus kulit.
- Klip `Dragon_Glide` dan `Dragon_TurnL/R` pada frame awal, tengah, akhir.

| # | Checklist referensi | Hasil |
| :-: | :--- | :--- |
| 4 | Warna: punggung gelap, sisi teal, perut krem dengan pita sisik? | |
| 5 | Kristal dada berpendar, duri putih di punggung? | |
| 6 | Lumut hijau kecil di bahu? | |
| B1 | Leher ke dada mulus dan gemuk-ramah, bukan bersudut? | |
| B2 | Tampilan atas dan belakang kaya detail (kamera game melihat dari sini)? | |

**Kriteria lulus:** `validate()` semua PASS, segitiga badan ≤ 1.200, fingerprint wilayah lain (kaki, sayap, kepala, ekor) identik, checklist minimal 4 dari 5 cocok.

**🛑 Pertanyaan akhir:** "Badan sudah sesuai? Lanjut ke Task 3 (sayap)?"

---

# 🪽 TASK 3: REMODEL SAYAP

**Tujuan:** sayap kelelawar **3 jari** yang jelas terbaca dari atas, lengan lebih tebal, tepi belakang bergerigi lebih tegas, motif tetes embun lebih rapat. Wilayah **sayap** (`WING`, `build_wings()`). Kiri dan kanan **cermin sempurna**.

**Perubahan yang diminta:**
| # | Perubahan | Titik awal |
| :-: | :--- | :--- |
| 1 | **Titik dimensi dikunci:** `T1.x = 6.0` (rentang 12 m), tinggi sayap, dan rasio sayap:panjang tidak berubah | Rentang tetap 11.5-12.5 m |
| 2 | Tebalkan lengan (S → E → Wr) dan jari | Lengan 0.13/0.10/0.07 → ≈ 0.16/0.12/0.085. Jari 0.055 → 0.016 menjadi ≈ 0.065 → 0.02 |
| 3 | Lengan melengkung sedikit: tambah titik antara S-E dan E-Wr, sisi tabung 5 → 6 | Lengkung ≤ 0.1 m, tetap T-pose datar |
| 4 | 3 jari tampak jelas dari atas: jarak antar-ujung jari cukup, ketebalan konsisten | Cek dari `DCam_Top` |
| 5 | Tepi belakang membran lebih bergerigi (cekung lebih dalam) | `pull` 0.14-0.20 → ≈ 0.22-0.28 |
| 6 | Tetes embun: **5-6 per panel** (4 panel luar per sayap), ukuran bervariasi (panjang 0.10-0.18), arah memanjang ke pergelangan, di atas membran (z +0.03) | Sebar teratur, tidak saling menimpa |
| 7 | Pangkal membran dan `S` / `R` menyatu dengan badan baru (tidak ada celah dari atas dan belakang) | Periksa dari `DCam_Top` dan `DCam_Back` |

**Dibekukan:** semua wilayah selain sayap. **Anggaran:** ≤ 1.500 segitiga (dua sayap).

**Uji deformasi dan animasi:**
- `Wing_Arm.R` dan `Wing_Arm.L` Y ±40°, `Wing_Fore.R` Y ±30°, `Wing_F2.R` Y ±20°.
- Cari: membran robek, tarikan di pangkal bahu, tetes embun terbang lepas dari membran.
- Klip: `Dragon_Hover` (frame naik dan turun), `Dragon_DewRain` (sayap V tinggi), `Dragon_TurnL/R`. Bandingkan dengan pose WING FLAP UP, WING FLAP DOWN, dan DEW RAIN di lembar pose.

| # | Checklist referensi | Hasil |
| :-: | :--- | :--- |
| 1 | Siluet sayap: lengan naik ke siku lalu turun ke ujung, 3 jari menyebar ke tepi bergerigi? | |
| 6 | Tetes embun di membran, mirip referensi? | |
| S1 | Dari belakang dan atas, sayap menjadi siluet dominan dan bersih? | |
| S2 | Sayap kiri dan kanan simetris (selisih X < 1 cm)? | |

**Kriteria lulus:** `validate()` semua PASS (rentang sayap, rasio, simetri), segitiga sayap ≤ 1.500, fingerprint wilayah lain identik, tidak ada membran robek, checklist 4 dari 4 cocok.

**🛑 Pertanyaan akhir:** "Sayap sudah sesuai? Lanjut ke Task 4 (kepala)?"

---

# 🐲 TASK 4: REMODEL KEPALA

**Tujuan:** kepala **lebih besar, bulat, ramah** (mata emas besar, moncong membulat, bukan runcing dan galak). Wilayah **kepala** (`HEAD_RINGS`, `JAW_RINGS`, mata, tanduk, telinga, hidung, garis mulut, `build_head()`).

**Perubahan yang diminta:**
| # | Perubahan | Titik awal |
| :-: | :--- | :--- |
| 1 | Perbesar kepala dan buat membulat | Radius cincin ×1.20-1.30 (lebar maks 0.80 → 0.96-1.00 m). **Hidung `y ≤ 2.90`** agar panjang total ≤ 7.85 m |
| 2 | Tambah cincin: 5 → 7 cincin; penampang kepala 8 → **12 sisi**, rahang 3 → 4 cincin dan 10 sisi | |
| 3 | Moncong lebih pendek dan bulat: ujung tidak meruncing ke titik, tutup dengan bola kecil | Cincin ujung r ≈ 0.16 / 0.13 |
| 4 | Mata lebih besar, pupil lebih besar, tambah kilau (bola kecil `M_Pearl`) | Mata r 0.105 → ≈ 0.14-0.15, pupil ≈ 0.07-0.08, kilau r ≈ 0.025 |
| 5 | Tanduk tetap melengkung ke belakang, pangkal lebih tebal, **pangkalnya masuk ke permukaan kepala baru** | Pangkal r 0.075 → ≈ 0.09 |
| 6 | Sirip telinga (2 kerucut per sisi) dipindah mengikuti lebar kepala baru | |
| 7 | **Lubang hidung** (2 bentuk kecil `M_Pupil`) dan **garis mulut** (strip tipis `M_Pupil` sepanjang sambungan kepala-rahang, bobot kaku ke `Head`) | r hidung ≈ 0.025, tebal garis ≈ 0.012 |
| 8 | Dagu dan rahang krem (`M_Belly`), atas kepala `M_Body`, warna rapi di 12 sisi | |
| 9 | Perbarui `Head` dan `Jaw` di `RIG` agar engsel rahang berada di bawah-belakang mata pada kepala baru. `Jaw` harus tetap anak `Head` | Engsel baru di dekat `(0, 1.90, 0.80)`, sesuaikan |

**Dibekukan:** cincin leher `y = 1.70` (titik sambung), semua wilayah lain. **Anggaran:** ≤ 1.800 segitiga.
**Awasi:** panjang total hidung-ekor ≤ 7.9 m, tinggi tanduk-kaki 2.2-2.8 m, rasio sayap:panjang ≥ 1.5.

**Uji deformasi dan animasi:**
- `Head` X ±20°, `Neck_03` X ±20°, `Jaw` X -30°.
- Cari: tanduk atau mata mencuat dari kulit, rahang menembus muka atas, **celah leher-kepala**, garis mulut terbang.
- Klip `Dragon_SprayWater` (frame 1, 7, 13: kepala menunduk, **mulut terbuka**) dan `Dragon_DewRain` (kepala menengadah). Bandingkan dengan pose SPRAY WATER dan DEW RAIN.

| # | Checklist referensi | Hasil |
| :-: | :--- | :--- |
| 2 | Kepala besar dan ramah (mata emas besar, moncong membulat), BUKAN runcing dan galak? | |
| 3 | Dua tanduk putih melengkung ke belakang dan dua sirip telinga? | |
| H1 | Lubang hidung dan garis mulut terlihat dari `DCam_Front` dan `DCam_Hero`? | |
| H2 | Mulut terbuka jelas saat `SprayWater`, tanpa tembus mesh? | |

**Kriteria lulus:** `validate()` semua PASS, segitiga kepala ≤ 1.800, fingerprint wilayah lain identik, uji bersih, checklist 4 dari 4 cocok.

**🛑 Pertanyaan akhir:** "Kepala sudah sesuai? Lanjut ke Task 5 (ekor)?"

---

# 🦎 TASK 5: REMODEL EKOR

**Tujuan:** ekor menjalar panjang, meruncing mulus, duri ekor serasi dengan duri badan, **sirip ekor berbentuk daun** dengan lengkung halus dan tulang daun tengah. Wilayah **ekor** (`TAIL_RINGS`, duri ekor, sirip ekor, `build_tail()`).

**Perubahan yang diminta:**
| # | Perubahan | Titik awal |
| :-: | :--- | :--- |
| 1 | Pangkal ekor lebih gemuk, penampang menyambung mulus dari cincin `y = -1.70` (milik Badan) | rx dan rz cincin `-2.20` ≈ ×1.15, lalu meruncing halus |
| 2 | Tambah cincin dengan `insert_rings` agar runcingnya mulus | +3 sampai +5 cincin pada `y -2.20 → -4.10` |
| 3 | **Panjang total tidak bertambah:** ujung sirip tetap ±`y = -4.92` | Panjang hidung-ekor tetap ≤ 7.85 m |
| 4 | Duri ekor: 6-8 buah, mengecil bertahap, bentuk sama dengan duri badan (Task 2) | **Total duri badan + ekor = 14-16** |
| 5 | Sirip ekor berbentuk daun: kontur 8 → ±12 titik, lebar total tetap ±0.80 m | |
| 6 | Lengkung halus pada sirip (ujung naik ±0.05-0.08 m, tetap simetris kiri-kanan) | Tetap ekor lurus pada T-pose |
| 7 | **Tulang daun tengah:** strip tipis `M_BackDark`, sedikit di atas permukaan sirip. Opsional 2-3 pasang urat samping lebih tipis | Tebal ±0.055 |
| 8 | Sirip menempel ke `Tail_04` tanpa celah | Pangkal sirip masuk ke ujung ekor |

**Dibekukan:** cincin `y = -1.70` dan semua wilayah lain. **Anggaran:** ≤ 700 segitiga.

**Uji deformasi dan animasi:**
- `Tail_01`, `Tail_02`, `Tail_03`, `Tail_04` masing-masing Z ±20°, `TailFin` Z ±25°.
- Cari: ekor patah menyudut, penampang gepeng, sirip lepas atau tembus ekor.
- Klip `Dragon_Glide` (ekor menjalar ±6°) dan `Dragon_TurnL/R` (ekor melengkung berlawanan).

| # | Checklist referensi | Hasil |
| :-: | :--- | :--- |
| 7 | Sirip ekor berbentuk daun, ekor menjalar panjang meruncing? | |
| 5 | Duri putih berlanjut dari punggung sampai ekor dengan ritme sama? | |
| E1 | Tulang daun tengah terlihat dan menyambung ke ekor? | |

**Kriteria lulus:** `validate()` semua PASS, segitiga ekor ≤ 700, fingerprint wilayah lain identik, panjang total ≤ 7.9 m, checklist 3 dari 3 cocok.

**🛑 Pertanyaan akhir:** "Ekor sudah sesuai? Lanjut ke validasi akhir?"

---

# ✅ PENUTUP: VALIDASI AKHIR (setelah Task 5 dijawab "Lanjut")

1. `validate()` penuh dan `tri_report()` total (≤ 8.000).
2. Checklist global dari lembar model: **#9** (atas dan belakang paling kaya detail) dan **#10** (rasio 1.5-1.7 dan semua tampilan konsisten). Screenshot 5 kamera, dibandingkan dengan `before_*`.
3. Putar ke-7 klip, pastikan loop mulus (frame akhir = frame awal) dan tidak ada tembus mesh. Bandingkan dengan lembar pose.
4. Ekspor FBX dengan `export_fbx(...)`, impor ulang di scene bersih, laporkan objek, **7 klip**, dan **35 tulang**. Catatan impor Unity tidak berubah (Generic, Root = `Root`, Loop Time semua klip, `M_Crystal` emisi, `M_WingMembrane` Two-Sided).
5. Serahkan: `dragon_gen_v2.py` final, `Dragon_Main.fbx`, `.blend` per task, dan rekap semua perubahan parameter.

---

## ❌ JANGAN DILAKUKAN
- Jangan membuat naga dari awal, memanggil `stage_scene()` atau `run_all()`, atau mengubah `dragon_gen.py` asli.
- Jangan menyentuh wilayah di luar task yang sedang berjalan (guard fingerprint harus identik).
- Jangan mengganti nama tulang, mengubah jumlah tulang, atau menambah material.
- Jangan menyeramkan, agresif, atau berdarah. Jangan menambah penunggang, pelana, zirah, api, atau latar.
- Jangan melebihi 8.000 segitiga atau membuat rasio sayap:panjang keluar dari 1.5-1.7.
- Jangan membakar roll badan ke dalam klip. Jangan pakai tekstur gambar, aset luar, atau Automatic Weights.
- Jangan menimpa `.blend` atau skrip sebelumnya dan jangan lanjut task tanpa jawaban "Lanjut".


---
**Navigasi Obsidian:** [[00 - Overview]] · [[konteks]] · [[prd]] · [[Tasks]] · [[Devlog]]
