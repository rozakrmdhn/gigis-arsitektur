# GIS Architecture

Sistem Informasi Geografis (GIS) pada GIGIS / MELAROSA adalah fondasi utama yang memungkinkan penyimpanan, manipulasi, dan visualisasi data infrastruktur berbasis ruang.

## Komponen GIS Utama

Arsitektur GIS sistem ini bergantung pada tiga pilar utama: PostGIS (Database), Turf.js (Klien Pemrosesan), dan Leaflet (Visualisasi Klien).

### 1. Database Spasial (PostGIS)
*   **Fungsi**: Mesin penyimpan dan pemrosesan kueri geospasial pada _backend_.
*   **Sistem Koordinat (SRID)**: Semua geometri disimpan menggunakan referensi sistem **SRID 4326 (WGS 84)**. SRID ini merupakan standar global yang digunakan oleh GPS dan sebagian besar pustaka WebGIS.
*   **Tipe Data**: Memanfaatkan tipe `GEOMETRY` di PostgreSQL yang memuat struktur `Point`, `LineString`, `Polygon`, maupun koleksinya.
*   **Indeks**: Ekstensi PostGIS menggunakan indeks spasial (GiST) untuk mempercepat pencarian data berdasar *bounding box* (misalnya: mencari semua infrastruktur di dalam radius tertentu atau yang saling berpotongan).

### 2. Klien Visualisasi (React Leaflet / OpenLayers)
*   **Fungsi**: Menampilkan peta dasar (*Basemap*) dan me-render (menggambar) objek GeoJSON yang diterima dari backend ke atas layar pengguna.
*   **Basemap Server**: Sistem mengkonsumsi *map tiles* (peta raster/vektor dasar) dari layanan pihak ketiga (seperti OpenStreetMap, Google Maps, atau layanan peta lokal instansi) melalui URL berbasis XYZ (contoh: `https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png`).
*   **Fitur**: Mendukung proses interaktif seperti *zooming*, *panning*, *clicking* (untuk melihat popup atribut data infrastruktur).

### 3. Pemrosesan Spasial Frontend (Turf.js)
*   **Fungsi**: Melakukan operasi *geo-processing* sederhana pada browser pengguna tanpa perlu melakukan koneksi (API call) bolak-balik ke server.
*   **Penggunaan Praktis**: Saat operator desa mendigitasi (menggambar) jalan baru, Turf.js digunakan untuk menghitung **panjang garis jalan** (dalam kilometer) secara *real-time*. Begitu pula ketika menggambar batas, Turf.js dapat menghitung luasan poligon sebelum form pengajuan di-*submit* ke backend.

## Alur Transformasi Geometri

Penyimpanan dan transmisi data spasial mengikuti alur translasi berikut:

1. **Client (Leaflet/Turf)**: Data geometri ditangkap sebagai objek JavaScript yang berformat **GeoJSON**.
2. **Transmisi (Network)**: Data dikirim melalui REST API HTTP POST/PUT menggunakan tipe MIME `application/json`.
3. **Backend (Hapi.js/Sequelize)**: Data GeoJSON dari _request payload_ diterjemahkan oleh Sequelize menjadi perintah SQL khusus PostGIS (misal menggunakan _function_ `ST_GeomFromGeoJSON()`).
4. **Database (PostGIS)**: Data secara fisik di-_serialize_ ke format biner **WKB (Well-Known Binary)** dalam kolom `geom` untuk efisiensi penyimpanan dan optimasi _index_.
5. Saat data diminta kembali oleh klien (GET _request_), Backend menggunakan ekstensi (seperti `ST_AsGeoJSON()`) untuk melakukan konversi mundur dari WKB kembali ke bentuk **GeoJSON** sebelum ditransmisikan.
