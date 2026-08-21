# UC-6: Digitasi Infrastruktur Spasial

## Identitas

| Atribut | Nilai |
|:---|:---|
| **ID** | UC-6 |
| **Nama** | Digitasi Infrastruktur Spasial |
| **Aktor Utama** | Operator Kecamatan |
| **Prioritas** | Tinggi |

## Deskripsi

Operator Kecamatan melakukan penggambaran geometri infrastruktur (titik, garis, poligon) secara interaktif pada peta WebGIS dan mengisi atribut dinamis sesuai tipe infrastruktur. Proses ini **wajib mengacu pada Draft Monitoring (UC-5)** yang diterbitkan oleh Bappeda.

## Prasyarat (Precondition)

- Operator Kecamatan sudah login dan terautentikasi.
- Draft Monitoring untuk wilayah kecamatannya sudah tersedia (UC-5 selesai).
- Data master tipe infrastruktur sudah tersedia.

## Alur Utama (Main Flow)

1. Operator Kecamatan membuka modul digitasi infrastruktur.
2. Sistem menampilkan daftar Draft Monitoring yang tersedia untuk kecamatannya.
3. Operator Kecamatan memilih salah satu Draft Monitoring sebagai acuan.
4. Sistem menampilkan peta interaktif WebGIS dengan _basemap_ dan layer batas wilayah.
5. Operator Kecamatan memilih tipe infrastruktur yang akan didigitasi.
6. Sistem menentukan jenis geometri yang sesuai (Point, LineString, atau Polygon).
7. Operator Kecamatan menggambar geometri pada peta:
   - **LineString** untuk segmen (jalan, saluran, dll).
   - **Polygon** untuk area (gedung, taman, dll).
   - **Point** untuk titik (jembatan, gardu, dll).
8. Sistem menampilkan form atribut dinamis (JSONB) sesuai tipe infrastruktur.
9. Operator Kecamatan mengisi atribut:
   - Nama infrastruktur.
   - Kondisi fisik.
   - Panjang/luas (otomatis dihitung dari geometri).
   - Atribut spesifik lainnya.
10. Operator Kecamatan menyimpan data.
11. Sistem menyimpan geometri (WKB/PostGIS) dan atribut dengan status **`draft`**.
12. Sistem mengaitkan data ke `id_kecamatan`, `draft_monitoring_id`, dan `plotting_id`.

## Alur Alternatif

### A1: Edit geometri yang sudah ada

1. Operator Kecamatan dapat mengedit geometri dan atribut infrastruktur yang masih berstatus `draft`.
2. Sistem memperbarui data geometri dan atribut di basis data.

### A2: Tipe geometri tidak sesuai

1. Jika operator mencoba menggambar geometri yang tidak sesuai tipe infrastruktur (misal: Polygon untuk jalan), sistem menampilkan peringatan dan mencegah penyimpanan.

### A3: Tanpa Draft Monitoring

1. Jika tidak ada Draft Monitoring yang tersedia, sistem menampilkan pesan bahwa digitasi belum dapat dilakukan dan mengarahkan untuk menghubungi Bappeda.

## Kondisi Akhir (Postcondition)

- Data infrastruktur spasial (geometri + atribut) tersimpan dengan status `draft`.
- Data terkait dengan Draft Monitoring dan Plotting Anggaran.
- Data siap untuk di-submit ke Bappeda (UC-7).

## Aturan Bisnis Terkait

- **BR-MN-02**: Digitasi wajib mengacu pada Draft Monitoring dari Bappeda.
- **BR-WL-02**: Kecamatan hanya dapat mendigitasi di wilayah kecamatannya.
- **TR-SP-01**: Tipe geometri harus sesuai dengan definisi infrastruktur.
- **TR-SP-02**: Geometri disimpan dalam SRID 4326.
- **TR-SP-03**: Transmisi menggunakan format GeoJSON.
