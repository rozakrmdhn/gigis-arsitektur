# UC-2: Geotagging Infrastruktur

## Identitas

| Atribut | Nilai |
|:---|:---|
| **ID** | UC-2 |
| **Nama** | Geotagging Infrastruktur |
| **Aktor Utama** | Operator Desa |
| **Prioritas** | Tinggi |

## Deskripsi

Operator Desa melakukan pendataan awal lokasi infrastruktur dengan mengisi form geotagging dan menandai titik koordinat lokasi. Proses ini hanya dapat dilakukan jika Plotting Anggaran (UC-1) sudah aktif untuk wilayah desanya.

## Prasyarat (Precondition)

- Operator Desa sudah login dan terautentikasi.
- Plotting Anggaran untuk wilayah desa yang bersangkutan sudah **aktif** (UC-1 selesai).
- Data master wilayah desa sudah tersedia.

## Alur Utama (Main Flow)

1. Operator Desa membuka modul Geotagging.
2. Sistem memvalidasi keberadaan Plotting Anggaran aktif untuk desa tersebut.
3. Sistem menampilkan daftar geotagging yang sudah dibuat sebelumnya (jika ada).
4. Operator Desa memilih **Buat Geotagging Baru**.
5. Sistem menampilkan form geotagging dan peta interaktif.
6. Operator Desa mengisi informasi form:
   - Nama/identitas infrastruktur.
   - Jenis infrastruktur.
   - Kondisi awal.
   - Keterangan tambahan.
   - Dokumentasi foto (opsional).
7. Operator Desa menandai titik lokasi infrastruktur pada peta (_geotagging_).
8. Sistem menangkap koordinat (latitude, longitude) dari titik yang ditandai.
9. Operator Desa menyimpan form geotagging.
10. Sistem menyimpan data dengan status **`draft`** dan mengaitkannya ke `id_desa` milik operator.

## Alur Alternatif

### A1: Plotting Anggaran belum ada

1. Pada langkah 2, jika sistem tidak menemukan Plotting Anggaran aktif untuk desa tersebut:
2. Sistem menampilkan pesan: _"Plotting Anggaran belum tersedia untuk desa Anda. Hubungi Bappeda."_
3. Operator Desa tidak dapat membuat geotagging baru.

### A2: Koordinat di luar batas desa

1. Pada langkah 7, jika titik yang ditandai berada di luar poligon batas administrasi desa:
2. Sistem menampilkan peringatan dan mencegah penyimpanan.

### A3: Edit geotagging berstatus draft

1. Operator Desa dapat mengedit form geotagging yang masih berstatus `draft`.
2. Data yang sudah di-submit (UC-3) tidak dapat diedit hingga ditolak kembali.

## Kondisi Akhir (Postcondition)

- Data geotagging tersimpan dengan status `draft`.
- Data terkait dengan `id_desa` dan `plotting_id` yang aktif.
- Data siap untuk di-submit ke Kecamatan (UC-3).

## Aturan Bisnis Terkait

- **BR-PL-03**: Geotagging hanya diizinkan jika Plotting Anggaran aktif sudah ada.
- **BR-WL-01**: Titik geotagging harus berada di dalam batas poligon administrasi desa.
- **BR-WL-02**: Operator Desa hanya dapat mengelola data di wilayah desanya.
- **FR-GT-00**: Validasi Plotting Anggaran sebelum izinkan geotagging.
