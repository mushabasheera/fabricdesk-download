# FabricDesk

Aplikasi desktop Windows untuk operasional **Cisco ACI & Nexus Dashboard**: cari endpoint, cek contract,
ping/traceroute, cek kesehatan leaf & port, fault, TCAM, dan API Runner. Dibuat oleh Mushab Hamid.

## Download

**[Download versi terbaru](https://github.com/mushabasheera/fabricdesk-download/releases/latest)**. Tidak perlu
akun GitHub. Di halaman itu, bagian **Assets**, download dua file:

- `FabricDesk.exe`: aplikasinya
- `FabricDesk-publisher.cer`: sertifikat penerbit (cukup dipasang sekali per laptop)

## Pasang sertifikat (sekali saja per laptop)

Exe sudah ditandatangani digital. Supaya Windows mengenali penerbitnya:

1. Double-click `FabricDesk-publisher.cer` > **Install Certificate** > pilih **Local Machine**.
2. Pilih **Place all certificates in the following store** > **Trusted Root Certification Authorities** > Finish.
3. Ulangi langkah 1-2, kali ini pilih store **Trusted Publishers**.

Kalau tetap muncul peringatan SmartScreen ("Windows protected your PC"), klik **More info** > **Run anyway**.
Peringatan ini muncul karena aplikasinya belum banyak diunduh, bukan karena aplikasinya berbahaya.

## Mulai pakai

1. Double-click `FabricDesk.exe`. Butuh Microsoft Edge WebView2 Runtime (sudah ada di Windows 10/11).
2. **Settings > Add customer** > isi IP APIC per site + username/password > **Test Connection**.
3. Pilih menu di kiri (Endpoint Locator, Contract Check, Leaf Health, dll).

Semua data tersimpan di laptop masing-masing (`%APPDATA%\FabricDesk`). Password disimpan terenkripsi
(Windows DPAPI) dan tidak dikirim ke mana pun selain ke APIC / Nexus Dashboard yang Anda isi sendiri.
Aplikasi hanya membaca dari fabric. Perubahan konfigurasi hanya lewat API Runner, dengan konfirmasi.

Untuk berbagi daftar customer antar engineer: **Settings > Export / Import** (file `.fdbak`, dikunci passphrase).

## Update

Download `FabricDesk.exe` versi terbaru dari halaman Releases, lalu timpa file lama. Data & customer tetap aman.
