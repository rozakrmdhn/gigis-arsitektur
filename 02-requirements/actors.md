# Actors & Roles

Sistem GIGIS / MELAROSA menggunakan model _Role-Based Access Control_ (RBAC) yang dikombinasikan dengan pembatasan wilayah (ABAC - _Attribute-Based Access Control_) untuk mengatur hak akses setiap pengguna.

## Daftar Aktor (Roles)

Berikut adalah peran (*roles*) utama yang terdefinisi di dalam sistem:

### 1. Super Admin (`super_admin`)

* **Deskripsi**: Administrator sistem yang memiliki hak akses penuh ke seluruh fitur dan konfigurasi sistem tanpa batasan wilayah.
* **Tanggung Jawab**: Manajemen pengguna, manajemen role & permissions, manajemen referensi master, dan pemantauan sistem secara keseluruhan.
* **Lingkup Akses**: Seluruh Kabupaten (Semua Kecamatan & Desa).

### 2. Operator Bappeda (`operator_bappeda`)

* **Deskripsi**: Pengguna dari tingkat Kabupaten (Badan Perencanaan Pembangunan Daerah) yang bertugas sebagai verifikator akhir dan perencana.
* **Tanggung Jawab**:
  * Menerima dan memverifikasi usulan/segmen infrastruktur yang diajukan oleh Kecamatan.
  * Melakukan plotting anggaran.
  * Menganalisis hasil pelaporan monitoring seluruh wilayah.
* **Lingkup Akses**: Seluruh Kabupaten (Semua Kecamatan & Desa).

### 3. Operator Kecamatan (`operator_kecamatan`)

* **Deskripsi**: Pengguna yang bertanggung jawab mengelola dan memverifikasi data pada tingkat Kecamatan.
* **Tanggung Jawab**:
  * Melihat dan mengelola data infrastruktur di dalam kecamatannya.
  * Memverifikasi data/laporan geotagging yang diajukan oleh Desa untuk diverifikasi Kecamatan.
  * Melakukan input data infrastruktur atau monitoring mewakili kecamatan.
* **Lingkup Akses**: Terbatas pada ID Kecamatan (`id_kecamatan`) yang melekat pada profil penggunanya.

### 4. Operator Desa (`operator_desa`)

* **Deskripsi**: Pengguna pada tingkat paling dasar (Pemerintah Desa) yang bertugas melakukan _data entry_ dan inventarisasi infrastruktur desa.
* **Tanggung Jawab**:
  * Memasukkan informasi form data/laporan geotagging.
  * Melakukan laporan geotagging lokasi infrastruktur.
  * Mengajukan submit form data/laporan geotagging ke tingkat Kecamatan untuk diverifikasi.
* **Lingkup Akses**: Terbatas pada ID Desa (`id_desa`) yang melekat pada profil penggunanya.

### 5. Operator OPD (`operator_opd`)

* **Deskripsi**: Pengguna dari Organisasi Perangkat Daerah teknis (misalnya: Dinas PUPR) yang memantau data spasial.
* **Tanggung Jawab**:
  * Melihat dan memantau (read-only) data infrastruktur yang ada di dalam sistem.
  * Mengakses laporan monitoring dan statistik infrastruktur sebagai bahan evaluasi dinas.
* **Lingkup Akses**: Read-only lintas wilayah kecamatan/desa sesuai dengan kepentingan OPD terkait.

## Matriks Hak Akses (Permissions)

Sistem menggunakan pustaka `@casl/ability` untuk menegakkan aturan otorisasi di _backend_. Aturan hak akses disimpan secara dinamis di dalam tabel `permissions`.

| Entitas / Modul            | Operator Desa                                      | Operator Kecamatan                       | Operator Bappeda             | Operator OPD |
| :------------------------- | :------------------------------------------------- | :--------------------------------------- | :--------------------------- | :----------- |
| **Form Geotagging**        | Create, Read, Submit (Khusus Desa Ybs)             | Read, Verify, Submit to Bappeda          | Read, Approve, Reject        | Read-Only    |
| **Infrastruktur Spasial**  | No Access                                          | Read, Update (Khusus Kecamatan Ybs)      | Read, Update, Approve (Semua) | Read-Only    |
| **Laporan Monitoring**     | No Access                                          | Read, Verify, Submit to Bappeda          | Read, Approve, Reject        | Read-Only    |
| **Batas Wilayah**          | Read-Only                                          | Read-Only                                | Read-Only                    | Read-Only    |
| **Plotting Anggaran**      | No Access                                          | Read-Only                                | Create, Read, Update, Delete | No Access    |
| **Manajemen Pengguna**     | No Access                                          | No Access                                | No Access (Only Super Admin) | No Access    |
