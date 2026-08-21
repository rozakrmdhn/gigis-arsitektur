# Functional Requirements

Kebutuhan fungsional mendefinisikan fitur-fitur dan kemampuan yang harus dimiliki oleh sistem GIGIS / MELAROSA untuk mendukung proses bisnisnya.

## 1. Manajemen Spasial Terpadu (Unified Spatial Management)

Sistem harus mampu mengelola berbagai tipe infrastruktur secara dinamis melalui arsitektur terpadu.

*   **FR-SP-01**: Sistem harus menyediakan fasilitas digitasi peta interaktif (WebGIS) untuk menggambar infrastruktur bertipe garis (_LineString_), titik (_Point_), dan area (_Polygon_).
*   **FR-SP-02**: Sistem harus dapat memisahkan penyimpanan koordinat (geometri) dengan data referensi master (jalan, jembatan, dll).
*   **FR-SP-03**: Sistem harus memungkinkan penambahan atribut infrastruktur yang bersifat dinamis (disimpan sebagai JSONB) bergantung pada tipe infrastruktur yang sedang diinput.
*   **FR-SP-04**: Sistem harus menampilkan *basemap* spasial dan lapisan (*layer*) batas wilayah administrasi desa dan kecamatan.

## 2. Alur Pengajuan dan Verifikasi (Submission & Approval Workflow)

Infrastruktur dan laporan yang dimasukkan dari tingkat bawah harus melalui mekanisme verifikasi berjenjang.

*   **FR-WF-01**: Operator Desa harus dapat melakukan pengajuan (_submit_) data segmen infrastruktur atau laporan monitoring ke tingkat Kecamatan.
*   **FR-WF-02**: Operator Kecamatan harus dapat memverifikasi, mengembalikan (_reject_ dengan catatan), atau meneruskan pengajuan ke tingkat Bappeda.
*   **FR-WF-03**: Operator Bappeda harus dapat menyetujui (_approve_) atau menolak pengajuan.
*   **FR-WF-04**: Sistem harus merekam _log history_ dari setiap perubahan status verifikasi beserta nama verifikator dan catatannya.
*   **FR-WF-05**: Sistem harus memungkinkan pengajuan masal (_batch submit_) untuk mempercepat proses persetujuan.

## 3. Monitoring dan Pelaporan

Sistem harus dapat memantau kondisi dan realisasi infrastruktur seiring waktu.

*   **FR-MN-01**: Sistem harus memungkinkan pembuatan formulir/laporan monitoring untuk segmen infrastruktur tertentu.
*   **FR-MN-02**: Sistem harus mencatat riwayat perubahan kondisi fisik infrastruktur ke dalam _monitoring log_.
*   **FR-MN-03**: Sistem harus dapat melampirkan dokumentasi pendukung (foto url) dan keterangan perbaikan/kerusakan pada saat melakukan monitoring.
*   **FR-MN-04**: Sistem harus menyediakan rekapitulasi atau _summary_ statistik kondisi infrastruktur per desa dan kecamatan.

## 4. Plotting Anggaran & Perencanaan

*   **FR-PL-01**: Operator Bappeda harus dapat membuat dokumen "Plotting Anggaran" berdasarkan tahun anggaran.
*   **FR-PL-02**: Sistem harus memungkinkan pengaitan (_linking_) antara Plotting Anggaran dengan usulan/segmen infrastruktur spasial tertentu.
*   **FR-PL-03**: Sistem harus menampilkan indikator visual pada peta untuk infrastruktur yang sudah mendapatkan plotting anggaran di tahun berjalan.

## 5. Manajemen Autentikasi dan Otorisasi (RBAC/ABAC)

*   **FR-AU-01**: Sistem harus mengautentikasi pengguna menggunakan JWT (_JSON Web Token_).
*   **FR-AU-02**: Sistem harus membatasi akses baca dan tulis (_Create, Update, Delete_) pengguna hanya untuk data yang berada di wilayahnya (`id_desa` atau `id_kecamatan`), kecuali Super Admin dan Bappeda.
*   **FR-AU-03**: Sistem harus menyediakan mekanisme manajemen sesi yang memungkinkan _revoke_ (pencabutan) akses secara *real-time* atau *logout all devices*.
