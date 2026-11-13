---
title: "Konfigurasi dan Integrasi MCP Server FigCraft ke Antigravity"
type: session-memory
tags:
  - session-memory
  - laptop-a
  - mcp
  - figcraft
  - tooling
date: 2026-11-13
owner: Laptop A
related:
  - "[[00 - Overview]]"
  - "[[Tasks]]"
  - "[[Devlog]]"
---

# Konfigurasi dan Integrasi MCP Server FigCraft ke Antigravity

## 1. Tujuan & Konteks Sesi
- **Target:** Menghubungkan aplikasi desain antarmuka **FigCraft** (yang sudah diinstal pengguna di `E:\ArtCraft\Apps\FigCraft\FigCraft`) sebagai MCP (*Model Context Protocol*) server di Antigravity.
- **Latar Belakang:** Workspace Laptop A membutuhkan integrasi alat bantu desain UI/aset yang konsisten dengan konfigurasi MCP yang sudah ada (`Roblox_Studio`, `blender`, dan `photocraft`).

---

## 2. Keputusan Desain / Arsitektur / Teknis
1. **Transport Stdio Headless via `figcraft-cli.exe`:**
   - Menggunakan executable CLI bawaan `E:\ArtCraft\Apps\FigCraft\FigCraft\figcraft-cli.exe` dengan argumen `["mcp"]`, mengikuti pola konfigurasi `photocraft` (`E:\ArtCraft\Apps\PhotoCraft\photocraft-cli.exe` + `["mcp"]`).
   - Mode default berjalan secara *headless* (in-process engine session tanpa jendela UI). Opsi `--attach <PORT>`, `--automation-read-root`, `--automation-write-root`, `--no-system-fonts`, dan `--timeout` tidak dipasang secara statis di konfigurasi dasar agar fleksibel seperti `photocraft`.
2. **Lokasi Konfigurasi Global:**
   - Menambahkan entri `"figcraft"` langsung ke `C:\Users\nmast\.gemini\config\mcp_config.json` (yang ditautkan melalui *SymbolicLink* ke `C:\Users\nmast\.gemini\antigravity\mcp_config.json`) sehingga berlaku global untuk semua sesi Antigravity.
3. **Pre-populasi Skema Lazy Tools:**
   - Mengekspor langsung `instructions.md` dan skema JSON dari 19 tool hasil `initialize` + `tools/list` server MCP `figcraft` ke `C:\Users\nmast\.gemini\antigravity\mcp\figcraft\` agar siap dipakai oleh mekanisme lazy MCP tool Antigravity.

---

## 3. Ringkasan Eksekusi & Perubahan File/Aset
1. **Inspeksi Instalasi & Kemampuan CLI:**
   - Memeriksa `E:\ArtCraft\Apps\FigCraft\FigCraft` (terdapat `figcraft.exe` dan `figcraft-cli.exe`).
   - Menjalankan `figcraft-cli.exe --help` dan `figcraft-cli.exe mcp --help` untuk memastikan dukungan subcommand `mcp` pada stdio.
   - Menguji jabat tangan JSON-RPC MCP (`initialize` dan `tools/list`) menggunakan skrip Python; terkonfirmasi `serverInfo` bernama `"figcraft"` (`title: "FigCraft"`, `version: "0.18.0"`) dengan **19 tools**.
2. **Perubahan File Konfigurasi:**
   - **`C:\Users\nmast\.gemini\config\mcp_config.json`** — Menambahkan blok server `"figcraft"`:
     ```json
     "figcraft": {
         "command": "E:\\ArtCraft\\Apps\\FigCraft\\FigCraft\\figcraft-cli.exe",
         "args": [
             "mcp"
         ]
     }
     ```
   - Validasi JSON menggunakan `python -c "import json; ..."` berhasil tanpa error.
3. **Pembuatan Direktori Skema MCP:**
   - **`C:\Users\nmast\.gemini\antigravity\mcp\figcraft\`** — Berisi `instructions.md` dan 19 file skema tool:
     - `batch.json`, `create_node.json`, `export.json`, `get_node.json`, `inspect_document.json`, `inspect_ui.json`, `key.json`, `list_commands.json`, `open_file.json`, `pointer.json`, `redo.json`, `run_command.json`, `save_file.json`, `screenshot.json`, `select.json`, `set_properties.json`, `type_text.json`, `undo.json`, `wait_for_changes.json`.
4. **Hasil Pengujian di Sesi Berjalan:**
   - Pemanggilan `call_mcp_tool` (`ServerName: "figcraft"`, `ToolName: "inspect_document"`) pada sesi yang sudah berjalan mengembalikan error `tool inspect_document is not enabled for server figcraft` karena Antigravity memuat daftar server MCP saat inisialisasi sesi. Diperlukan reload/restart IDE atau refresh melalui menu **Additional Options (`...`) > MCP Servers** untuk mengaktifkannya.

---

## 4. Detail Penting yang Harus Diingat
- **Path Instalasi FigCraft:** `E:\ArtCraft\Apps\FigCraft\FigCraft\figcraft-cli.exe` (di dalam subdirektori `FigCraft\FigCraft`, berbeda satu level lebih dalam dibanding `PhotoCraft` yang berada di `E:\ArtCraft\Apps\PhotoCraft\photocraft-cli.exe`).
- **Path Konfigurasi MCP Antigravity:** `C:\Users\nmast\.gemini\antigravity\mcp_config.json` adalah *SymbolicLink* menuju `C:\Users\nmast\.gemini\config\mcp_config.json`.
- **Aturan Koordinat & Engine FigCraft (dari `instructions.md`):**
  - 1 unit = 1 px pada zoom 100%, sumbu `x` ke kanan, sumbu `y` ke bawah.
  - `create_node` dan `set_properties` memakai koordinat `x`/`y` relatif terhadap parent.
  - Warna menggunakan format `"#RRGGBB"` atau `"#RRGGBBAA"`.
  - Mendukung format `.figcraft`, `.penpot`, `.json`, `.fig`, `.sketch`, `.svg`, serta gambar `.png`/`.jpg`/`.webp`/`.gif`/`.bmp`.
- **Server MCP Aktif Lainnya di `mcp_config.json`:** `Roblox_Studio`, `blender` (`127.0.0.1:9876`), dan `photocraft`.

---

## 5. Tugas Tersisa & Langkah Selanjutnya
- [ ] Verifikasi aktivasi MCP server `figcraft` di UI Antigravity (**Additional Options (`...`) > MCP Servers**) atau pada sesi baru setelah restart/reload agar `call_mcp_tool` dapat mengeksekusi tools FigCraft secara langsung.
- [ ] (Opsional) Uji pembuatan/inspeksi mockup UI proyek (`[[rencana_desain_ui]]`) menggunakan `figcraft` bila dibutuhkan untuk aset UI 2D di Laptop A.

**Navigasi Obsidian:** [[00 - Overview]] · [[konteks]] · [[prd]] · [[rencana_desain_ui]] · [[Tasks]] · [[Devlog]]
