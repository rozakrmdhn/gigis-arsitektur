# Business Rules

Aturan bisnis memuat kebijakan-kebijakan operasional yang bersumber dari proses bisnis dan regulasi organisasi, yang ditegakkan oleh sistem GIGIS / MELAROSA.

## 1. Aturan Verifikasi Data

*   **BR-VR-01 (Verifikasi Berjenjang)**: Form geotagging yang dibuat oleh Operator Desa otomatis memiliki status `draft` atau `verifikasi_kecamatan`. Data tersebut tidak dianggap final atau terpublikasi sebelum disetujui oleh Bappeda.
*   **BR-VR-02 (Kunci Edit)**: Jika sebuah form geotagging telah di-_submit_ ke Kecamatan atau Bappeda, Operator Desa tidak diizinkan lagi mengubah isinya hingga data tersebut ditolak (dikembalikan) atau di-klaim kembali.
*   **BR-VR-03 (Alur Mundur/Revert)**: Data yang ditolak oleh Bappeda atau Kecamatan harus menyertakan "Catatan Verifikasi" yang merinci alasan penolakan agar Operator Desa dapat memperbaikinya.

## 2. Aturan Wilayah

*   **BR-WL-01 (Pembatasan Wilayah Geotagging)**: Titik koordinat geotagging yang ditandai oleh Operator Desa harus berada di dalam batas poligon administrasi desanya. Sistem secara logis mengaitkan data geotagging tersebut ke `id_desa` milik pembuatnya.
*   **BR-WL-02 (Pembatasan Akses Data)**: Operator Desa hanya dapat melihat dan mengelola data yang berada di dalam wilayah desanya (`id_desa`). Operator Kecamatan hanya dapat mengelola data di wilayah kecamatannya (`id_kecamatan`).

## 3. Aturan Monitoring & Realisasi

*   **BR-MN-01 (Inisiasi Draft Monitoring)**: Draft monitoring hanya dapat dibuat oleh Operator Bappeda berdasarkan Plotting Anggaran yang telah disetujui. Draft monitoring ini menjadi acuan resmi bagi Operator Kecamatan untuk melakukan digitasi data infrastruktur.
*   **BR-MN-02 (Kewajiban Digitasi Berdasarkan Draft)**: Operator Kecamatan wajib mengacu pada draft monitoring yang diterbitkan oleh Bappeda dalam melakukan digitasi infrastruktur spasial. Digitasi di luar lingkup draft monitoring harus mendapat persetujuan khusus.
*   **BR-MN-03 (Keterikatan Laporan)**: Laporan monitoring bersifat tidak dapat dihapus (`soft delete` atau restricted) jika sudah terkunci atau diverifikasi untuk menjaga integritas sejarah (_audit trail_).
*   **BR-MN-04 (Pembaruan Kondisi Otomatis)**: Ketika laporan monitoring disetujui oleh Bappeda, nilai "kondisi" terbaru pada laporan tersebut dapat memicu pembaruan atribut "kondisi" pada master infrastruktur terkait.

## 4. Aturan Anggaran & Plotting

*   **BR-PL-01 (Satu Tahun, Satu Plot)**: Suatu usulan atau segmen infrastruktur sebaiknya hanya dikaitkan dengan satu Plotting Anggaran dari jenis sumber dana yang sama dalam satu tahun berjalan, kecuali diizinkan spesifik oleh regulasi multi-tahun.
*   **BR-PL-02 (Otoritas Plotting)**: Pengaitan anggaran (menetapkan `plotting_id` pada segmen) murni merupakan wewenang Operator Bappeda, bukan Operator Desa, Kecamatan, maupun OPD.
*   **BR-PL-03 (Prasyarat Geotagging)**: Operator Desa hanya dapat melakukan geotagging infrastruktur apabila Operator Bappeda telah menerbitkan Plotting Anggaran yang aktif untuk wilayah desa yang bersangkutan. Geotagging tanpa Plotting Anggaran yang valid tidak diizinkan oleh sistem.
