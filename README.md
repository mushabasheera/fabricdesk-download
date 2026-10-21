# FabricDesk

Aplikasi desktop Windows untuk operasional **Cisco ACI & Nexus Dashboard**, multi-customer, jalan lokal di laptop.
Dibuat oleh Mushab Hamid (PT Multipolar Technology Tbk).

**[Download versi terbaru](https://github.com/mushabasheera/fabricdesk-download/releases/latest)**. Tidak perlu
akun GitHub. Cara pasang ada di bagian [Download & pasang](#download--pasang).

Versi saat ini: **v0.4.0** (21 Okt 2026).

---

## Fitur

| Menu | Fungsi |
|---|---|
| **Endpoint Locator** | Cari IP / MAC, atau tarik semua endpoint (filter tenant / VRF / EPG) -> leaf / port, VLAN, tenant, VRF, BD, EPG, ESG, last change |
| **Contract Check** | Apakah `src -> dst:port` sudah diizinkan policy ACI. Cek contract langsung, contract interface, vzAny, preferred group, VRF unenforced, contract di tenant common, scope lintas VRF, objek stretched / shadow, ext-EPG (longest prefix, flag catch-all 0.0.0.0/0), firewall boundary. Multi-site: dicek di **kedua site** (status `PARTIAL` kalau cuma satu site yang izinkan). Hasil menunjukkan siapa *consumer* dan *provider*. Input manual atau import form request (CSV / XLSX) |
| **Ping** | Multi-thread dari laptop (input IP / CIDR / range / file, hasil live, tombol Stop, log) atau dari leaf (`iping -V tenant:vrf` lewat SSH) |
| **Traceroute** | Dari laptop (`tracert`) atau dari leaf (`itraceroute -V tenant:vrf`) |
| **API Runner** | **Templates** (urutan API call, mis. "Configure Contract") -> **Library** (buat API call / import Postman, assign ke template) -> **Environments** (variable ala Postman, bisa banyak & import environment Postman). Run dari template atau langsung dari Library: pilih environment + site, delay, test data CSV / XLSX (1 baris = 1 iteration), **Preview iterations** sebelum run, on error: stop / skip iteration / continue, hasil + pesan error **per iteration**. Login & token diurus app (credential tidak perlu ditaruh di environment) |
| **Change Diff** | Snapshot config (tenant / `uni/infra`) sebelum & sesudah change, lalu bandingkan (added / removed / modified, alert kalau ada object terhapus) |
| **Fault & Health** | Fault sejak waktu tertentu (per severity / kata kunci) + health fabric, node, tenant |
| **Leaf Health** (baru) | Per leaf: health score, CPU, memory, fault leaf itu, TCAM / capacity, daftar port (status, speed, SFP, CRC, error, tetangga LLDP / CDP). **Klik port** -> detail: alasan port down, counter error, transceiver (PID / serial), optical Rx / Tx / suhu dengan ambang warn / alarm (copper / UTP: *Not available*), LLDP / CDP |
| **Resource Check** | VLAN free per pool / per IPG (AAEP -> domain -> pool), port free per leaf, TCAM / capacity per leaf (Policy CAM, endpoint L2 / L3, LPM, VLAN) + jumlah zoning rule |
| **Settings** | Wizard customer (site APIC, NDO / ND cluster, SSH ke leaf, Test Connection, firewall boundary, mapping kolom form request), Export / Import, Audit Log |

- Semua tabel hasil bisa diexport ke **CSV** dan **Excel**.
- **Read-only ke fabric.** Perubahan config hanya lewat API Runner, wajib Preview iterations lolos + konfirmasi
  ketik nama site. Sebelum WRITE ke APIC, app bisa trigger config snapshot otomatis (rollback lewat Admin > Config
  Rollbacks).
- Mendukung banyak customer; pilih customer di header. Setting tiap customer diisi sekali lewat wizard.

---

## Batasan & status

Semua fitur sudah dites otomatis pakai **APIC tiruan**. Yang sudah terbukti di fabric asli: login APIC & NDO
(cluster ND 3 node), API Runner ke NDO, pembacaan Policy CAM / TCAM & endpoint L2 / L3.

| Area | Belum ada / perlu verifikasi di fabric asli |
|---|---|
| Leaf Health | **Baru, belum dites ke leaf asli.** CPU, memory, CRC, SFP, optical, LLDP / CDP bisa kosong kalau nama object-nya beda di versi ACI tertentu |
| Settings | Test Connection NDO / ND: pembacaan versi ND / NDO belum diverifikasi |
| Endpoint Locator | ESG tag selector, history endpoint move |
| Contract Check | Routing / L3Out tidak dicek (ALLOWED belum tentu traffic jalan), service graph / PBR, intersite vzAny per versi |
| Ping / Traceroute | Mode dari leaf (SSH iping / itraceroute) belum dites ke leaf asli; ND Connectivity Analysis belum ada |
| Fault & Health | Anomaly Nexus Dashboard belum ada |
| Resource Check | Interface selector access policy; angka TCAM belum dibandingkan langsung dengan Capacity Dashboard di GUI |
| Change Diff | Diff template NDO belum ada |
| API Runner | Belum ada backup NDO otomatis sebelum WRITE (backup manual dulu); script Postman (pre-request / test) tidak dijalankan |
| Umum | Hanya Windows 10 / 11. Sertifikat self-signed, jadi SmartScreen bisa tetap memberi peringatan |

Ketemu bug atau angka yang beda dengan GUI APIC? Kabari Mushab Hamid. Teks error di app bisa diblok & di-copy.

---

## Download & pasang

**[Buka halaman download](https://github.com/mushabasheera/fabricdesk-download/releases/latest)**. Di bagian
**Assets**, download dua file:

- `FabricDesk.exe`: aplikasinya (~40 MB)
- `FabricDesk-publisher.cer`: sertifikat penerbit (cukup dipasang sekali per laptop)

### Notes

Kalau tetap muncul peringatan SmartScreen ("Windows protected your PC"), klik **More info** > **Run anyway**.
Peringatan ini muncul karena aplikasinya belum banyak diunduh, bukan karena aplikasinya berbahaya.

## Mulai pakai

1. Double-click `FabricDesk.exe`. Window muncul ±3 detik. Butuh Microsoft Edge WebView2 Runtime (sudah ada di
   Windows 10 / 11).
2. **Settings > Add customer** > isi IP APIC per site (boleh beberapa IP cluster, pisahkan koma) + username /
   password > **Test Connection**.
3. Pilih menu di kiri.

Semua data tersimpan di laptop masing-masing (`%APPDATA%\FabricDesk`). Password disimpan terenkripsi (Windows
DPAPI) dan hanya dikirim ke APIC / Nexus Dashboard yang Anda isi sendiri. Log error:
`%APPDATA%\FabricDesk\logs\fabricdesk.log`.

Berbagi daftar customer antar engineer: **Settings > Export / Import** (file `.fdbak`, dikunci passphrase).
Saran: export **tanpa** credential kalau akun APIC-nya personal.

## Update

Download `FabricDesk.exe` versi terbaru dari halaman Releases, lalu timpa file lama. Data & customer tetap aman.
