# UC-3: Submit Geotagging ke Kecamatan

## Identitas

| Atribut | Nilai |
|:---|:---|
| **ID** | UC-3 |
| **Nama** | Submit Geotagging ke Kecamatan |
| **Aktor Utama** | Operator Desa |
| **Prioritas** | Tinggi |

## Deskripsi

Operator Desa mengajukan (_submit_) form geotagging yang sudah diisi ke tingkat Kecamatan untuk diverifikasi. Setelah di-submit, data terkunci dan tidak dapat diedit oleh Operator Desa.

## Prasyarat (Precondition)

- Operator Desa sudah login dan terautentikasi.
- Form geotagging berstatus `draft` dan sudah diisi lengkap (UC-2 selesai).

## Alur Utama (Main Flow)

1. Operator Desa membuka daftar geotagging miliknya.
2. Operator Desa memilih satu atau lebih form geotagging berstatus `draft`.
3. Operator Desa memilih aksi **Submit ke Kecamatan**.
4. Sistem menampilkan konfirmasi pengajuan.
5. Operator Desa mengonfirmasi.
6. Sistem mengubah status form geotagging menjadi **`verifikasi_kecamatan`**.
7. Sistem mengunci form dari perubahan oleh Operator Desa.
8. Sistem mencatat log pengajuan (tanggal, waktu, nama pengaju).

## Alur Alternatif

### A1: Batch submit (pengajuan masal)

1. Operator Desa memilih beberapa form geotagging sekaligus.
2. Sistem memproses seluruh pengajuan secara bersamaan (_batch_).
3. Sistem menampilkan ringkasan hasil submit (berhasil/gagal per item).

### A2: Data belum lengkap

1. Jika form geotagging belum memenuhi validasi kelengkapan data, sistem menolak pengajuan dan menampilkan pesan kesalahan.

## Kondisi Akhir (Postcondition)

- Status geotagging berubah dari `draft` menjadi `verifikasi_kecamatan`.
- Data terkunci dari perubahan oleh Operator Desa.
- Data siap untuk diverifikasi oleh Operator Kecamatan (UC-4).
- Log pengajuan tercatat di sistem.

## Aturan Bisnis Terkait

- **BR-VR-01**: Data yang di-submit berstatus `verifikasi_kecamatan`, belum final.
- **BR-VR-02**: Data yang sudah di-submit tidak dapat diedit hingga ditolak kembali.
- **FR-WF-05**: Sistem mendukung pengajuan masal (_batch submit_).
