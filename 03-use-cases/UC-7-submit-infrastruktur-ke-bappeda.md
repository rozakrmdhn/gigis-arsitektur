# UC-7: Submit Infrastruktur ke Bappeda

## Identitas

| Atribut | Nilai |
|:---|:---|
| **ID** | UC-7 |
| **Nama** | Submit Infrastruktur ke Bappeda |
| **Aktor Utama** | Operator Kecamatan |
| **Prioritas** | Tinggi |

## Deskripsi

Operator Kecamatan mengajukan data infrastruktur spasial yang telah didigitasi (UC-6) ke tingkat Bappeda untuk diverifikasi dan disetujui. Setelah di-submit, data terkunci dari perubahan oleh Kecamatan.

## Prasyarat (Precondition)

- Operator Kecamatan sudah login dan terautentikasi.
- Data infrastruktur spasial berstatus `draft` dan sudah diisi lengkap (UC-6 selesai).

## Alur Utama (Main Flow)

1. Operator Kecamatan membuka daftar infrastruktur spasial milik kecamatannya.
2. Operator Kecamatan memilih satu atau lebih data infrastruktur berstatus `draft`.
3. Operator Kecamatan memilih aksi **Submit ke Bappeda**.
4. Sistem menampilkan konfirmasi pengajuan.
5. Operator Kecamatan mengonfirmasi.
6. Sistem mengubah status data menjadi **`verifikasi_bappeda`**.
7. Sistem mengunci data dari perubahan oleh Kecamatan.
8. Sistem mencatat log pengajuan (tanggal, waktu, nama pengaju).

## Alur Alternatif

### A1: Batch submit

1. Operator Kecamatan memilih beberapa data infrastruktur sekaligus.
2. Sistem memproses seluruh pengajuan secara bersamaan.
3. Sistem menampilkan ringkasan hasil submit.

### A2: Data belum lengkap

1. Jika data infrastruktur belum memenuhi validasi (geometri kosong, atribut wajib belum diisi), sistem menolak pengajuan dan menampilkan pesan kesalahan.

## Kondisi Akhir (Postcondition)

- Status infrastruktur berubah dari `draft` menjadi `verifikasi_bappeda`.
- Data terkunci dari perubahan oleh Kecamatan.
- Data siap untuk di-approve/reject oleh Bappeda (UC-8).
- Log pengajuan tercatat di sistem.

## Aturan Bisnis Terkait

- **BR-VR-01**: Data belum final sebelum disetujui Bappeda.
- **BR-VR-02**: Data terkunci selama dalam proses verifikasi.
- **FR-WF-05**: Sistem mendukung pengajuan masal (_batch submit_).
