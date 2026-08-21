# Scope

Dokumen ini mendefinisikan ruang lingkup fungsional dan batasan sistem GIGIS / MELAROSA.

## Ruang Lingkup Fungsional

Sistem mencakup area fungsional berikut:

### 1. Geotagging Infrastruktur

Sistem menyediakan fasilitas bagi Operator Desa untuk melakukan pendataan awal infrastruktur melalui form geotagging. Cakupan:

- Input informasi dan koordinat lokasi infrastruktur.
- Pengajuan (_submit_) form geotagging ke Kecamatan untuk diverifikasi.
- Akses read-only data infrastruktur dan laporan monitoring khusus desa yang bersangkutan.

### 2. Manajemen Data Master

Pengelolaan data referensi yang digunakan oleh seluruh komponen sistem:

- Data wilayah administrasi (Kecamatan, Desa).
- Jenis dan kategori infrastruktur.
- Status dan referensi lainnya.

### 3. Manajemen Data Spasial

Pengelolaan informasi geografis infrastruktur dilakukan oleh Operator Kecamatan berdasarkan draft monitoring dari Bappeda:

- Digitasi geometri bertipe Point, LineString, dan Polygon.
- Editing dan pembaruan koordinat geometri.
- Penyimpanan geometri dengan sistem koordinat WGS 84 (SRID 4326).
- Pengelolaan atribut dinamis per tipe infrastruktur (JSONB).

### 4. Plotting Anggaran & Draft Monitoring

Dilakukan oleh Operator Bappeda sebagai dasar proses monitoring:

- Pembuatan Plotting Anggaran berdasarkan tahun anggaran.
- Pembuatan draft monitoring berdasarkan Plotting Anggaran yang disetujui.
- Pengaitan (_linking_) Plotting Anggaran dengan objek infrastruktur spasial.

### 5. Alur Verifikasi Berjenjang

Sistem mengimplementasikan alur persetujuan data secara berjenjang:

- Operator Desa → mengajukan form geotagging ke Kecamatan.
- Operator Kecamatan → memverifikasi dan meneruskan ke Bappeda.
- Operator Bappeda → menyetujui atau menolak (disertai catatan).

### 6. Monitoring Kondisi Infrastruktur

- Pencatatan riwayat perubahan kondisi fisik infrastruktur.
- Pembuatan formulir/laporan monitoring oleh Kecamatan dan Bappeda.
- Dokumentasi pendukung (foto) dan keterangan kondisi.
- Rekapitulasi dan statistik kondisi infrastruktur per wilayah.

### 7. Visualisasi WebGIS

- Peta interaktif yang dapat diakses publik (tanpa login) untuk melihat infrastruktur.
- Basemap dan layer batas wilayah administrasi.
- Identifikasi dan popup informasi objek pada peta.
- Digitasi geometri interaktif (khusus Operator Kecamatan).

### 8. Manajemen Pengguna & Otorisasi

- Autentikasi berbasis JWT dengan mekanisme refresh token.
- Role-Based Access Control (RBAC) per peran pengguna.
- Attribute-Based Access Control (ABAC) — pembatasan akses berdasarkan wilayah (`id_desa`, `id_kecamatan`).
- Manajemen sesi dan pencabutan akses (_revoke_).

---

## Batasan Sistem

Berikut adalah hal-hal yang **berada di luar** cakupan sistem:

| No | Di Luar Cakupan |
|:---|:---|
| 1 | Operator Desa tidak dapat melakukan digitasi geometri infrastruktur secara langsung (hanya geotagging). |
| 2 | Operator OPD tidak dapat melakukan perubahan data (read-only). |
| 3 | Plotting Anggaran hanya dapat dibuat oleh Operator Bappeda. |
| 4 | Pengguna publik tidak dapat melihat data monitoring, laporan, maupun anggaran. |
| 5 | Sistem tidak menangani proses pengadaan atau pembayaran anggaran secara langsung. |
