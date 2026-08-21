# UC-1: Pembuatan Plotting Anggaran

## Identitas

| Atribut | Nilai |
|:---|:---|
| **ID** | UC-1 |
| **Nama** | Pembuatan Plotting Anggaran |
| **Aktor Utama** | Operator Bappeda |
| **Prioritas** | Tinggi |

## Deskripsi

Operator Bappeda membuat data Plotting Anggaran berdasarkan tahun anggaran. Plotting Anggaran ini menjadi **prasyarat utama** bagi Operator Desa untuk melakukan geotagging (UC-2) dan bagi Bappeda sendiri untuk membuat Draft Monitoring (UC-5).

## Prasyarat (Precondition)

- Operator Bappeda sudah login dan terautentikasi.
- Data master wilayah (kecamatan & desa) sudah tersedia di sistem.

## Alur Utama (Main Flow)

1. Operator Bappeda membuka modul Plotting Anggaran.
2. Operator Bappeda memilih **Buat Plotting Anggaran Baru**.
3. Sistem menampilkan form Plotting Anggaran.
4. Operator Bappeda mengisi informasi:
   - Tahun anggaran.
   - Sumber dana.
   - Wilayah cakupan (kecamatan/desa target).
   - Nilai anggaran.
   - Keterangan.
5. Operator Bappeda menyimpan data.
6. Sistem menyimpan Plotting Anggaran dengan status **aktif**.
7. Sistem menampilkan konfirmasi berhasil.

## Alur Alternatif

### A1: Edit Plotting Anggaran

1. Operator Bappeda dapat mengedit Plotting Anggaran yang sudah ada selama belum ada Draft Monitoring atau Geotagging yang terkait.

### A2: Data tidak lengkap

1. Jika data wajib belum diisi, sistem menampilkan pesan validasi dan mencegah penyimpanan.

## Kondisi Akhir (Postcondition)

- Plotting Anggaran tersimpan di basis data dengan status aktif.
- Operator Desa di wilayah terkait kini dapat melakukan geotagging (UC-2).
- Operator Bappeda kini dapat membuat Draft Monitoring (UC-5).

## Aturan Bisnis Terkait

- **BR-PL-01**: Satu segmen infrastruktur hanya dikaitkan dengan satu Plotting Anggaran per sumber dana per tahun.
- **BR-PL-02**: Pembuatan Plotting Anggaran adalah wewenang eksklusif Operator Bappeda.
- **BR-PL-03**: Plotting Anggaran aktif menjadi prasyarat geotagging oleh Desa.
