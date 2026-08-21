# Database Schema

Aplikasi GIGIS / MELAROSA menggunakan **PostgreSQL** dengan ekstensi **PostGIS** untuk penyimpanan dan pengelolaan data spasial. Backend aplikasi menggunakan ORM **Sequelize** untuk memetakan model ke dalam database.

## Struktur Tabel Utama

Secara garis besar, skema database dibagi menjadi beberapa kelompok fungsional:

### 1. Data Spasial & Infrastruktur (Unified Architecture)

Sistem menggunakan arsitektur terpadu (*unified architecture*) untuk menangani berbagai tipe infrastruktur menggunakan tabel master spasial:

*   **`infrastruktur_segmen`**
    *   Fungsi: Menyimpan data geometri bertipe garis (*LineString*) atau titik (*Point*).
    *   Kolom Penting: `id` (UUID), `tipe_kode` (jalan, jembatan, dll), `parent_id` (FK ke tabel master spesifik), `geom` (GEOMETRY SRID 4326), `atribut` (JSONB untuk properti dinamis), `status_verifikasi`.
*   **`infrastruktur_area`**
    *   Fungsi: Menyimpan data geometri bertipe poligon (*Polygon*).
    *   Kolom Penting: Sama seperti segmen, namun dengan bentuk poligon.
*   **`infrastruktur_tipe`**
    *   Fungsi: Tabel referensi/kategori tipe infrastruktur (contoh: Jalan Desa, Jembatan, Irigasi).

### 2. Master Data Infrastruktur Terpisah (Legacy / Specific Master)

Terdapat tabel master terpisah yang memuat atribut spesifik sebelum diintegrasikan ke tabel spasial terpadu, antara lain:

*   **`jalan_porosdesa`**: Master jalan poros desa (menyimpan kode ruas, nama ruas, panjang, lebar, dsb).
*   **`jalan_segmenkab`**: Master jalan segmen kabupaten.
*   **`jembatan_porosdesa`**: Master data jembatan.
*   **`jalan_lingkungan`**: Master data jalan lingkungan.

> [!NOTE]
> Hubungan antara tabel master spesifik dengan `infrastruktur_segmen` / `infrastruktur_area` direlasikan melalui kolom `parent_id`.

### 3. Monitoring & Pelaporan

Digunakan untuk mencatat riwayat pemeliharaan, kondisi, dan plotting anggaran:

*   **`monitoring_laporan`**: Header laporan monitoring/realisasi yang dibuat oleh user (Kecamatan/Desa/Bappeda).
*   **`monitoring_laporan_segmen`**: Tabel pivot (Many-to-Many) yang menghubungkan `monitoring_laporan` dengan `infrastruktur_segmen`.
*   **`monitoring_segmen` / `monitoring_log`**: Catatan riwayat monitoring secara mendetail untuk sebuah segmen infrastruktur.
*   **`plotting_anggaran`**: Menyimpan data alokasi atau plotting anggaran pembangunan untuk wilayah tertentu.

### 4. Batas Wilayah & Geografis

*   **`bataswilayah_kecamatan`**: Tabel wilayah administratif tingkat kecamatan (mengandung geometri poligon batas kecamatan).
*   **`bataswilayah_desa`**: Tabel wilayah administratif tingkat desa/kelurahan (mengandung geometri poligon batas desa).

### 5. Manajemen Pengguna & Autentikasi (RBAC)

*   **`users`**: Menyimpan data pengguna (email, password, role, id_kecamatan, id_desa, dsb).
*   **`roles` & `permissions`**: Manajemen hak akses berbasis peran (Role-Based Access Control).
*   **`menus` & `role_menu`**: Konfigurasi menu dinamis berdasarkan peran pengguna.

## Tipe Data Khusus

*   **Geometri Spasial**: Semua data spasial disimpan dalam kolom `geom` menggunakan tipe `GEOMETRY` dari PostGIS dengan sistem koordinat **SRID 4326 (WGS 84)**.
*   **Atribut Dinamis**: Untuk mengakomodasi atribut yang berbeda-beda tiap jenis infrastruktur tanpa harus menambah kolom database baru, tabel `infrastruktur_segmen` dan `infrastruktur_area` memanfaatkan tipe data **JSONB** pada kolom `atribut`.
*   **Primary Key**: Sebagian besar tabel menggunakan `UUID (v4)` sebagai Primary Key, meskipun beberapa tabel master referensi masih menggunakan `INTEGER` atau `BIGINT`.
