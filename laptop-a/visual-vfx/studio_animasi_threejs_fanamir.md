---
title: Fanamir 3D Dragon Animation & Pose Studio (Three.js WebGL)
tags:
  - threejs
  - webgl
  - visualizer
  - animation-studio
  - dragon-rig
  - visual-vfx
  - laptop-a
owner: Laptop A
updated: 2026-11-23
status: active
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
  - "[[visual-vfx/evaluasi_dan_revisi_pose_tahap_1]]"
  - "[[visual-vfx/planning_perbaikan_animasi_dan_pose]]"
  - "[[visual-vfx/prompt_eksekusi_animasi_tahap_1_laptop_b]]"
---

# 🐉 Fanamir 3D Dragon Animation & Pose Studio (Three.js WebGL)

> **Inovasi Studio 3D Interaktif:** Alih-alih melakukan *trial-and-error* rotasi tulang secara buta di Unity Editor (edit skrip $\rightarrow$ Play Mode $\rightarrow$ tangkap layar $\rightarrow$ cek manual), studio berbasis WebGL Three.js ini memuat langsung model 3D naga resmi Fanamir (`dragon_model.glb`, 47 tulang rig `ARM_Dragon` lengkap dengan seluruh 13 mesh kulit & sayap) ke dalam peramban web interaktif. Pemain dan pengembang dapat memutar kamera $360^\circ$, memilih preset pose resmi, menggeser slider sudut tulang secara *real-time*, dan mengekspor nilai rotasi langsung menjadi kode C# Unity!

---

## 🚀 1. Cara Menjalankan Studio

Aplikasi web telah disiapkan secara mandiri (*self-contained & offline-ready*) di folder:  
📁 [`dragon_anim_studio/`](file:///e:/Semester%205/Game/AR%20Dragon/dragon_anim_studio/)

### Cara Menjalankan:
1. **Otomatis / 1-Klik:**  
   Klik dua kali file batch di root workspace:  
   ⚡ [`start_dragon_studio.bat`](file:///e:/Semester%205/Game/AR%20Dragon/start_dragon_studio.bat)  
   *(Skrip ini otomatis menyalakan server lokal di port `8088` dan membuka peramban web default ke `http://localhost:8088`).*
2. **Atau Akses Langsung saat Server Aktif:**  
   Buka peramban ke alamat:  
   🌐 **[http://localhost:8088](http://localhost:8088)**

---

## 🎨 2. Fitur-Fitur Utama Studio

### A. Preset Pose Utama (Berdasarkan Lembar Rujukan Resmi)
* 🪶 **Hovering (`hover`):** Mengacu pada [`Ref_Dragon_Hover_Leg_Anatomy.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi/Ref_Dragon_Hover_Leg_Anatomy.jpg):
  * Kemiringan tubuh tegap condong naik $+30^\circ$ (*Upright Hovering Posture*).
  * Dada terangkat anggun memperlihatkan permata dada (*Chest Gem*).
  * Kaki depan menggantung santai tertekuk $\sim 85^\circ$ ke bawah-depan (*dangling loosely, slightly forward*).
  * Kaki belakang melayang bebas tertekuk $\sim 40^\circ$ di bawah tubuh (*hanging naturally below body*).
  * Ekor melengkung anggun ke atas sebagai penyeimbang bobot (*counter-balance curve*).
  * Dilengkapi osilasi apung magis vertikal (*buoyant hover bobbing*) dan kepakan sayap lebar (*broad flapping motion*).
* 🦅 **Flight / Glide (`flight`):** Mengacu pada [`Ref_Dragon_Flight_Leg_Anatomy.jpg`](file:///e:/Semester%205/Game/AR%20Dragon/Referensi/Ref_Dragon_Flight_Leg_Anatomy.jpg):
  * Postur tubuh mendatar ramping ($0^\circ$).
  * Kaki depan melipat rapi ke arah belakang di bawah rongga dada (*folded neatly under chest*).
  * Kaki belakang **lurus memanjang ke belakang sejajar pangkal ekor** (*extended backward, streamlined along tail*).
  * Bentangan sayap aerodinamis horizontal dengan osilasi lambat ekor (*cruising undulation*).
* 🐾 **Ground Walk (`walk`):**
  * Posisi menapak tanah dengan siklus langkah 4 kaki diagonal (*quadruped trotting gait*: Kaki Depan Kiri + Belakang Kanan vs Depan Kanan + Belakang Kiri).
* ⚡ **Propeller Dash (`dash`):**
  * Kepakan sayap bertenaga badai berfrekuensi tinggi ($3.8\text{ Hz}$) dengan kedalaman ayunan dorong menekan ke bawah tubuh dan lenturan elastis ujung selaput sayap (*wing flex*).
* 🔻 **Tuck Dive (`dive`):**
  * Sayap melipat kaku merapat di sepanjang tulang belakang (*rigid dive tuck*), menukik tajam aerodinamis mirip peluru.
* ⚪ **Rest Pose (`rest`):**
  * Menampilkan T-pose / bind pose baku model 3D untuk kalibrasi awal.

---

### B. Kontrol Kamera & Tampilan 3D
* **Mouse Controls:** Klik Kiri Drag untuk memutar kamera $360^\circ$, Klik Kanan Drag untuk menggeser (*pan*), Scroll Mouse untuk memperbesar/memperkecil (*zoom*).
* **Sudut Kamera Instan:**
  * 📐 **Side View (90°):** Tampilan samping persis seperti lembar rujukan anatomi.
  * 🎯 **3/4 Diagonal View:** Sudut pandang tiga perempat dinamis.
  * 👁️ **Front View:** Tampilan depan dada dan kepala.
  * 🦅 **Top View:** Tampilan atas bentangan sayap.
* **Alat Bantu Visual:**
  * 🦴 **Wireframe Bones:** Menampilkan garis kerangka tulang (*SkeletonHelper*) untuk melihat orientasi sendi secara transparan.
  * 🌐 **Grid:** Menghidupkan/mematikan garis dasar lantai.

---

### C. Live Fine-Tuning Sliders & Generator Kode C# Unity
* Setiap sudut tulang (kemiringan badan, elevasi dada, tekukan kaki depan, ekstensi kaki belakang, kepala, ekor, dan kecepatan sayap) dapat diatur secara langsung lewat slider di bilah kanan.
* **Tombol "📋 Ekspor Kode C# Unity":**
  * Membuka modal yang secara otomatis menyusun baris kode C# Unity berbasis nilai sudut rotasi yang sedang aktif di layar.
  * Kode ini dapat langsung disalin ke clipboard dan ditempelkan ke `DragonBodyAnimator.cs` di Laptop B untuk menjamin visual di game $100\%$ identik dengan apa yang telah disetujui di Three.js Studio!

---

*Navigasi:* [[00 - Overview]] | [[Tasks]] | [[Devlog]] | [[visual-vfx/evaluasi_dan_revisi_pose_tahap_1]] | [[visual-vfx/planning_perbaikan_animasi_dan_pose]]
