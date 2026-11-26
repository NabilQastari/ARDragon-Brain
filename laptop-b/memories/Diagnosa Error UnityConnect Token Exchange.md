---
title: "Diagnosa Error UnityConnect Token Exchange"
type: session-memory
tags:
  - session-memory
  - laptop-b
  - unity-editor
  - troubleshooting
date: 2026-11-26
owner: Laptop B
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
---

# Diagnosa Error UnityConnect Token Exchange

## 1. Tujuan & Konteks Sesi
- Pengguna melaporkan error di Console Unity Editor dari tangkapan layar:
  `UnityConnectWebRequestException: Token Exchange failed due a failure with the web request.`
  (`UnityEditor.Connect.TokenExchange.VerifyTokenExchangeResponse`)
- Memastikan apakah error tersebut berasal dari kode proyek **AR Dragon Sky Guardian** atau dari internal Unity Editor, serta memberikan langkah penyelesaiannya.

---

## 2. Keputusan Desain / Arsitektur / Teknis
- **Hasil Diagnosa:**
  - Melalui `editor_status` dan `console_get` Unity MCP (`Unity 6000.3.7f1`), kompilasi proyek bersih (`compilationFailed: false`) dan tidak ada error pada script game (`Session 4` verification log di console tetap lulus).
  - Stack trace berasal murni dari internal Unity Editor: `UnityEditor.Connect.UnityConnect.RequestNewServiceToken()` $\rightarrow$ `ServiceToken.GetServiceTokenAsync()` $\rightarrow$ `TokenExchange.VerifyTokenExchangeResponse()`.
  - Pemeriksaan `AR Dragon Sky Guardian/ProjectSettings/UnityConnectSettings.asset` memastikan seluruh layanan Unity Cloud/Services nonaktif (`m_Enabled: 0`).
- **Akar Masalah:** Kegagalan request jaringan latar belakang Unity Editor saat menukar/memperbarui token sesi akun Unity (karena koneksi terputus/timeout, sesi login **Unity Hub** kedaluwarsa, atau akses ke `*.unity.com` / `*.unity3d.com` terhambat VPN/proxy/firewall).

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
- Tidak ada file kode C#, Python, maupun aset Unity yang diubah pada sesi ini (hanya pembacaan status MCP & `UnityConnectSettings.asset`).
- Langkah perbaikan yang diberikan ke pengguna:
  1. Klik **Clear** di Console Unity jika hanya muncul sesekali (tidak memengaruhi Play Mode, UDP, maupun build).
  2. Cek koneksi internet dan nonaktifkan VPN/proxy sementara bila ada.
  3. **Sign out** lalu **Sign in** ulang di **Unity Hub**, kemudian restart **Unity Editor**.
  4. *(Opsional)* **Sign out** akun di kanan atas Unity Editor apabila tidak menggunakan layanan Unity Cloud/Asset Store di Editor.

---

## 4. Detail Penting yang Harus Diingat
- Jika `UnityConnectWebRequestException: Token Exchange failed...` muncul di Console, periksa dulu `compilationFailed` dan asal stack trace-nya (`UnityEditor.Connect.*`); selama berasal dari `UnityEditor.Connect`, bug tersebut bukan regresi kode `DragonWingAR.*`.
- `ProjectSettings/UnityConnectSettings.asset` proyek memakai `m_Enabled: 0` untuk seluruh layanan Unity Connect/Analytics/Ads/CrashReporting.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- Lanjutkan antrean tugas aktif di `[[Tasks]]` (uji Play Mode langsung, texturing/ekspor `rumah.blend`, serta finalisasi build standalone).

---
**Navigasi Obsidian:** [[00 - Overview]] · [[Tasks]] · [[Devlog]]
