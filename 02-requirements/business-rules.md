# Business Rules

Aturan bisnis (Business Rules) memuat kebijakan-kebijakan operasional yang ditegakkan secara _hard-coded_ di dalam logika sistem (_backend_ maupun _frontend_) GIGIS / MELAROSA.

## 1. Aturan Verifikasi Data

*   **BR-VR-01 (Verifikasi Berjenjang)**: Data yang dibuat oleh Operator Desa otomatis memiliki status `draft` atau `verifikasi_kecamatan`. Data tersebut tidak dianggap final atau terpublikasi sebelum disetujui oleh Bappeda.
*   **BR-VR-02 (Kunci Edit)**: Jika sebuah segmen infrastruktur telah di-_submit_ ke Kecamatan atau Bappeda, Operator Desa tidak diizinkan lagi mengubah atribut maupun geometrinya hingga data tersebut ditolak (dikembalikan) atau di-klaim kembali.
*   **BR-VR-03 (Alur Mundur/Revert)**: Data yang ditolak oleh Bappeda atau Kecamatan harus menyertakan "Catatan Verifikasi" yang merinci alasan penolakan agar Operator tingkat bawah dapat memperbaikinya.

## 2. Aturan Data Spasial

*   **BR-SP-01 (Konsistensi Geometri)**: Segmen spasial harus berjenis geometri yang sesuai. Jika tipe infrastruktur didefinisikan sebagai garis, maka koordinat yang dimasukkan (melalui endpoint `/segmen`) harus berupa `LineString`. Demikian halnya dengan `/area` yang harus berupa `Polygon`.
*   **BR-SP-02 (Pembatasan Wilayah Digitasi)**: Geometri (titik/garis/poligon) yang digambar oleh Operator Desa seyogyanya berada di dalam batas poligon administrasi desanya. Sistem secara logis mengaitkan infrastruktur tersebut ke `id_desa` milik pembuatnya.

## 3. Aturan Monitoring & Realisasi

*   **BR-MN-01 (Keterikatan Laporan)**: Laporan monitoring bersifat tidak dapat dihapus (`soft delete` atau restricted) jika sudah terkunci atau diverifikasi untuk menjaga integritas sejarah (_audit trail_).
*   **BR-MN-02 (Pembaruan Kondisi Otomatis)**: Ketika laporan monitoring disetujui oleh Bappeda, nilai "kondisi" terbaru pada laporan tersebut dapat memicu pembaruan atribut "kondisi" pada master infrastruktur terkait.

## 4. Aturan Anggaran & Plotting

*   **BR-PL-01 (Satu Tahun, Satu Plot)**: Suatu usulan atau segmen infrastruktur sebaiknya hanya dikaitkan dengan satu Plotting Anggaran dari jenis sumber dana yang sama dalam satu tahun berjalan, kecuali diizinkan spesifik oleh regulasi multi-tahun.
*   **BR-PL-02 (Otoritas Plotting)**: Pengaitan anggaran (menetapkan `plotting_id` pada segmen) murni merupakan wewenang Operator Bappeda atau OPD teknis terkait, bukan Operator Desa atau Kecamatan.

## 5. Aturan Autentikasi

*   **BR-AU-01 (Token Expiry)**: Access Token (JWT) memiliki batas waktu kedaluwarsa (_expiry_) yang singkat demi keamanan. Aplikasi klien wajib menggunakan mekanisme _Refresh Token_ di _background_ (/v1/refresh) untuk memperpanjang sesi tanpa perlu _login_ ulang.
*   **BR-AU-02 (Pencabutan Sesi)**: Jika admin mengubah _role_ atau wilayah _(id_desa)_ dari suatu akun pengguna, sistem dapat secara sepihak mencabut (_revoke_) _session_ JWT pengguna tersebut sehingga klien akan dipaksa melakukan _login_ ulang untuk mendapatkan izin yang baru.
