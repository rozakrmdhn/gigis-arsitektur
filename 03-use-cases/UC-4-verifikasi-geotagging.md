# UC-4: Verifikasi Geotagging

## Identitas

| Atribut | Nilai |
|:---|:---|
| **ID** | UC-4 |
| **Nama** | Verifikasi Geotagging |
| **Aktor Utama** | Operator Kecamatan |
| **Prioritas** | Tinggi |

## Deskripsi

Operator Kecamatan menerima dan memverifikasi form geotagging yang diajukan oleh Operator Desa. Kecamatan dapat menyetujui (meneruskan ke proses selanjutnya) atau menolak dengan catatan agar Desa memperbaiki datanya.

## Prasyarat (Precondition)

- Operator Kecamatan sudah login dan terautentikasi.
- Terdapat form geotagging berstatus `verifikasi_kecamatan` di wilayah kecamatannya (UC-3 selesai).

## Alur Utama (Main Flow)

1. Operator Kecamatan membuka modul verifikasi geotagging.
2. Sistem menampilkan daftar form geotagging berstatus `verifikasi_kecamatan` di kecamatannya.
3. Operator Kecamatan memilih salah satu form untuk diperiksa.
4. Sistem menampilkan detail form geotagging beserta lokasi pada peta.
5. Operator Kecamatan memeriksa kelengkapan dan keakuratan data.
6. Operator Kecamatan memilih aksi **Setujui / Verifikasi**.
7. Sistem mengubah status menjadi **`terverifikasi_kecamatan`**.
8. Sistem mencatat log verifikasi (tanggal, waktu, nama verifikator).

## Alur Alternatif

### A1: Tolak geotagging (reject)

1. Pada langkah 6, Operator Kecamatan memilih aksi **Tolak**.
2. Sistem mewajibkan pengisian **Catatan Verifikasi** (alasan penolakan).
3. Sistem mengubah status kembali menjadi `draft`.
4. Sistem membuka kunci form sehingga Operator Desa dapat mengedit dan memperbaikinya.
5. Sistem mencatat log penolakan beserta catatannya.

### A2: Verifikasi batch

1. Operator Kecamatan memilih beberapa form geotagging sekaligus.
2. Sistem memproses verifikasi secara bersamaan untuk seluruh item yang dipilih.

## Kondisi Akhir (Postcondition)

- **Jika disetujui**: Status berubah menjadi `terverifikasi_kecamatan`. Data menjadi referensi valid di tingkat kecamatan.
- **Jika ditolak**: Status kembali ke `draft`. Operator Desa dapat mengedit ulang. Catatan penolakan tercatat di log.

## Aturan Bisnis Terkait

- **BR-VR-01**: Data belum final sebelum disetujui Bappeda.
- **BR-VR-02**: Data terkunci selama dalam proses verifikasi.
- **BR-VR-03**: Penolakan wajib disertai catatan alasan.
- **BR-WL-02**: Kecamatan hanya memverifikasi data di wilayah kecamatannya.
