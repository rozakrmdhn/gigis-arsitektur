# Actors & Roles

Sistem GIGIS / MELAROSA menggunakan model _Role-Based Access Control_ (RBAC) yang dikombinasikan dengan pembatasan wilayah (ABAC - _Attribute-Based Access Control_) untuk mengatur hak akses setiap pengguna.

## Daftar Aktor (Roles)

Berikut adalah peran (*roles*) utama yang terdefinisi di dalam sistem:

### 0. Publik (`public` / tanpa autentikasi)

* **Deskripsi**: Pengguna umum yang mengakses sistem tanpa melakukan _login_. Sistem mengenali pengguna ini sebagai pengguna anonim.
* **Tanggung Jawab**:
  * Melihat (read-only) peta infrastruktur spasial secara umum.
  * Mengakses data batas wilayah administrasi (desa & kecamatan).
  * Melihat _basemap_ dan layer peta yang dipublikasikan.
* **Lingkup Akses**: Hanya data yang dipublikasikan secara umum (tanpa data sensitif atau data per-wilayah yang terbatas).

### 1. Super Admin (`super_admin`)

* **Deskripsi**: Administrator sistem yang memiliki hak akses penuh ke seluruh fitur dan konfigurasi sistem tanpa batasan wilayah.
* **Tanggung Jawab**: Manajemen pengguna, manajemen role & permissions, manajemen referensi master, dan pemantauan sistem secara keseluruhan.
* **Lingkup Akses**: Seluruh Kabupaten (Semua Kecamatan & Desa).

### 2. Operator Bappeda (`operator_bappeda`)

* **Deskripsi**: Pengguna dari tingkat Kabupaten (Badan Perencanaan Pembangunan Daerah) yang bertugas sebagai perencana, verifikator akhir, dan inisiator proses monitoring.
* **Tanggung Jawab**:
  * Melakukan plotting anggaran sebagai dasar perencanaan infrastruktur.
  * Membuat draft monitoring berdasarkan plotting anggaran sebagai acuan digitasi bagi Operator Kecamatan.
  * Menerima dan memverifikasi data infrastruktur yang diajukan oleh Kecamatan.
  * Menyetujui (_approve_) atau menolak pengajuan data dari Kecamatan.
  * Menganalisis hasil pelaporan monitoring seluruh wilayah.
* **Lingkup Akses**: Seluruh Kabupaten (Semua Kecamatan & Desa).

### 3. Operator Kecamatan (`operator_kecamatan`)

* **Deskripsi**: Pengguna yang bertanggung jawab mengelola data spasial dan memverifikasi data pada tingkat Kecamatan.
* **Tanggung Jawab**:
  * Melakukan digitasi data infrastruktur spasial (segmen/area) di dalam kecamatannya berdasarkan draft monitoring yang dibuat oleh Bappeda.
  * Memverifikasi data/laporan geotagging yang diajukan oleh Operator Desa.
  * Mengajukan (_submit_) data infrastruktur yang telah didigitasi ke Bappeda untuk diverifikasi.
* **Lingkup Akses**: Terbatas pada ID Kecamatan (`id_kecamatan`) yang melekat pada profil penggunanya.

### 4. Operator Desa (`operator_desa`)

* **Deskripsi**: Pengguna pada tingkat paling dasar (Pemerintah Desa) yang bertugas melakukan _data entry_ dan inventarisasi infrastruktur desa.
* **Tanggung Jawab**:
  * Memasukkan informasi form data/laporan geotagging.
  * Melakukan laporan geotagging lokasi infrastruktur.
  * Mengajukan submit form data/laporan geotagging ke tingkat Kecamatan untuk diverifikasi.
  * Melihat dan memantau (read-only) data infrastruktur khusus desanya.
  * Mengakses laporan monitoring dan statistik infrastruktur khusus desanya.
* **Lingkup Akses**: Terbatas pada ID Desa (`id_desa`) yang melekat pada profil penggunanya.

### 5. Operator OPD (`operator_opd`)

* **Deskripsi**: Pengguna dari Organisasi Perangkat Daerah teknis (misalnya: Dinas PUPR) yang memantau data spasial.
* **Tanggung Jawab**:
  * Melihat dan memantau (read-only) data infrastruktur yang ada di dalam sistem.
  * Mengakses laporan monitoring dan statistik infrastruktur sebagai bahan evaluasi dinas.
* **Lingkup Akses**: Read-only lintas wilayah kecamatan/desa sesuai dengan kepentingan OPD terkait.

## Matriks Hak Akses (Permissions)

Sistem menggunakan pustaka `@casl/ability` untuk menegakkan aturan otorisasi di _backend_. Aturan hak akses disimpan secara dinamis di dalam tabel `permissions`.

| Entitas / Modul            | Publik       | Operator Desa                                      | Operator Kecamatan                       | Operator Bappeda             | Operator OPD |
| :------------------------- | :----------- | :------------------------------------------------- | :--------------------------------------- | :--------------------------- | :----------- |
| **Form Geotagging**        | No Access    | Create, Read, Submit (Khusus Desa Ybs)             | Read, Verify, Submit to Bappeda          | Read, Approve, Reject        | Read-Only    |
| **Infrastruktur Spasial**  | Read-Only    | Read-Only (Khusus Desa Ybs)                        | Read, Update (Khusus Kecamatan Ybs)      | Read, Update, Approve (Semua) | Read-Only    |
| **Laporan Monitoring**     | No Access    | Read-Only (Khusus Desa Ybs)                        | Read, Verify, Submit to Bappeda          | Read, Approve, Reject        | Read-Only    |
| **Batas Wilayah**          | Read-Only    | Read-Only                                          | Read-Only                                | Read-Only                    | Read-Only    |
| **Plotting Anggaran**      | No Access    | No Access                                          | Read-Only                                | Create, Read, Update, Delete | No Access    |
| **Manajemen Pengguna**     | No Access    | No Access                                          | No Access                                | No Access (Only Super Admin) | No Access    |
