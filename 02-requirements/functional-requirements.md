# Functional Requirements

Kebutuhan fungsional mendefinisikan fitur-fitur dan kemampuan yang harus dimiliki oleh sistem GIGIS / MELAROSA untuk mendukung proses bisnisnya.

## 1. Geotagging Infrastruktur

Sistem harus menyediakan fasilitas bagi Operator Desa untuk melakukan pendataan lokasi infrastruktur melalui mekanisme geotagging, **dengan syarat Operator Bappeda telah menerbitkan Plotting Anggaran yang aktif untuk wilayah desa tersebut**.

*   **FR-GT-00**: Sistem harus memvalidasi keberadaan Plotting Anggaran yang aktif sebelum mengizinkan Operator Desa membuat form geotagging. Jika belum ada Plotting Anggaran, sistem menolak pembuatan geotagging baru.
*   **FR-GT-01**: Sistem harus menyediakan form geotagging yang memungkinkan Operator Desa memasukkan informasi dan titik koordinat lokasi infrastruktur.
*   **FR-GT-02**: Sistem harus memungkinkan Operator Desa mengajukan (_submit_) form geotagging ke tingkat Kecamatan untuk diverifikasi.
*   **FR-GT-03**: Sistem harus membatasi data geotagging yang dapat dibuat dan dilihat oleh Operator Desa hanya pada wilayah desanya (`id_desa`).
*   **FR-GT-04**: Sistem harus memungkinkan Operator Desa melihat (read-only) data infrastruktur spasial yang berada di dalam wilayah desanya.
*   **FR-GT-05**: Sistem harus memungkinkan Operator Desa mengakses laporan monitoring dan statistik kondisi infrastruktur khusus desanya secara read-only.

## 2. Manajemen Spasial Terpadu (Unified Spatial Management)

Sistem harus mampu mengelola berbagai tipe infrastruktur secara dinamis melalui arsitektur terpadu. Pengelolaan data spasial dilakukan oleh Operator Kecamatan dan Bappeda.

*   **FR-SP-01**: Sistem harus menyediakan fasilitas digitasi peta interaktif (WebGIS) untuk menggambar infrastruktur bertipe garis (_LineString_), titik (_Point_), dan area (_Polygon_).
*   **FR-SP-02**: Sistem harus dapat memisahkan penyimpanan koordinat (geometri) dengan data referensi master (jalan, jembatan, dll).
*   **FR-SP-03**: Sistem harus memungkinkan penambahan atribut infrastruktur yang bersifat dinamis (disimpan sebagai JSONB) bergantung pada tipe infrastruktur yang sedang diinput.
*   **FR-SP-04**: Sistem harus menampilkan *basemap* spasial dan lapisan (*layer*) batas wilayah administrasi desa dan kecamatan.

## 3. Alur Pengajuan dan Verifikasi (Submission & Approval Workflow)

Form geotagging dan data infrastruktur yang dimasukkan dari tingkat bawah harus melalui mekanisme verifikasi berjenjang.

*   **FR-WF-01**: Operator Desa harus dapat melakukan pengajuan (_submit_) form geotagging ke tingkat Kecamatan untuk diverifikasi.
*   **FR-WF-02a**: Operator Kecamatan harus dapat memverifikasi form geotagging dari Desa, lalu menyetujui atau mengembalikan (_reject_ dengan catatan).
*   **FR-WF-02b**: Operator Kecamatan harus dapat mengajukan (_submit_) data infrastruktur hasil digitasinya sendiri ke tingkat Bappeda untuk diverifikasi.
*   **FR-WF-03**: Operator Bappeda harus dapat menyetujui (_approve_) atau menolak pengajuan.
*   **FR-WF-04**: Sistem harus merekam _log history_ dari setiap perubahan status verifikasi beserta nama verifikator dan catatannya.
*   **FR-WF-05**: Sistem harus memungkinkan pengajuan masal (_batch submit_) untuk mempercepat proses persetujuan.

## 4. Monitoring dan Pelaporan

Sistem harus dapat memantau kondisi dan realisasi infrastruktur seiring waktu. Monitoring diinisiasi oleh Bappeda dan dilanjutkan oleh Kecamatan melalui proses digitasi.

*   **FR-MN-00**: Sistem harus memungkinkan Operator Bappeda membuat draft monitoring berdasarkan Plotting Anggaran yang telah disetujui, sebagai acuan resmi bagi Operator Kecamatan untuk melakukan digitasi infrastruktur.
*   **FR-MN-01**: Sistem harus memungkinkan Operator Kecamatan melihat dan melakukan digitasi infrastruktur spasial berdasarkan draft monitoring yang diterbitkan oleh Bappeda.
*   **FR-MN-02**: Sistem harus mencatat riwayat perubahan kondisi fisik infrastruktur ke dalam _monitoring log_.
*   **FR-MN-03**: Sistem harus dapat melampirkan dokumentasi pendukung (foto url) dan keterangan perbaikan/kerusakan pada saat melakukan monitoring.
*   **FR-MN-04**: Sistem harus menyediakan rekapitulasi atau _summary_ statistik kondisi infrastruktur per desa dan kecamatan yang dapat diakses secara read-only oleh Operator OPD.

## 5. Plotting Anggaran & Perencanaan

*   **FR-PL-01**: Operator Bappeda harus dapat membuat dokumen "Plotting Anggaran" berdasarkan tahun anggaran.
*   **FR-PL-02**: Sistem harus memungkinkan pengaitan (_linking_) antara Plotting Anggaran dengan usulan/segmen infrastruktur spasial tertentu.
*   **FR-PL-03**: Sistem harus menampilkan indikator visual pada peta untuk infrastruktur yang sudah mendapatkan plotting anggaran di tahun berjalan.

## 6. Manajemen Autentikasi dan Otorisasi (RBAC/ABAC)

*   **FR-AU-01**: Sistem harus mengautentikasi pengguna menggunakan JWT (_JSON Web Token_).
*   **FR-AU-02**: Sistem harus membatasi akses baca dan tulis (_Create, Update, Delete_) pengguna hanya untuk data yang berada di wilayahnya (`id_desa` atau `id_kecamatan`), kecuali Super Admin dan Bappeda.
*   **FR-AU-03**: Sistem harus menyediakan mekanisme manajemen sesi yang memungkinkan _revoke_ (pencabutan) akses secara *real-time* atau *logout all devices*.
