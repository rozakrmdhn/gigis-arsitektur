# UC-8: Approve/Reject Infrastruktur

## Identitas

| Atribut | Nilai |
|:---|:---|
| **ID** | UC-8 |
| **Nama** | Approve/Reject Infrastruktur |
| **Aktor Utama** | Operator Bappeda |
| **Prioritas** | Tinggi |

## Deskripsi

Operator Bappeda melakukan persetujuan (_approve_) atau penolakan (_reject_) terhadap data infrastruktur spasial yang diajukan oleh Operator Kecamatan (UC-7). Data yang disetujui menjadi data final yang terpublikasi secara resmi.

## Prasyarat (Precondition)

- Operator Bappeda sudah login dan terautentikasi.
- Terdapat data infrastruktur berstatus `verifikasi_bappeda` (UC-7 selesai).

## Alur Utama (Main Flow)

1. Operator Bappeda membuka modul verifikasi infrastruktur.
2. Sistem menampilkan daftar data infrastruktur berstatus `verifikasi_bappeda`.
3. Operator Bappeda memilih salah satu data untuk diperiksa.
4. Sistem menampilkan detail infrastruktur:
   - Geometri pada peta interaktif.
   - Atribut dan kondisi.
   - Data geotagging terkait (jika ada).
   - Informasi Draft Monitoring dan Plotting Anggaran.
   - Log riwayat perubahan status.
5. Operator Bappeda memeriksa kelengkapan dan keakuratan data.
6. Operator Bappeda memilih aksi **Approve**.
7. Sistem mengubah status menjadi **`approved`**.
8. Sistem mempublikasikan data ke basis data spasial utama.
9. Sistem mencatat log persetujuan (tanggal, waktu, nama verifikator).

## Alur Alternatif

### A1: Reject (tolak) infrastruktur

1. Pada langkah 6, Operator Bappeda memilih aksi **Reject**.
2. Sistem mewajibkan pengisian **Catatan Verifikasi** (alasan penolakan).
3. Sistem mengubah status kembali menjadi `draft`.
4. Sistem membuka kunci data sehingga Operator Kecamatan dapat mengedit dan memperbaikinya.
5. Sistem mencatat log penolakan beserta catatannya.

### A2: Approve/Reject batch

1. Operator Bappeda memilih beberapa data infrastruktur sekaligus.
2. Untuk approve batch, sistem memproses seluruh persetujuan secara bersamaan.
3. Untuk reject batch, sistem mewajibkan catatan penolakan yang berlaku untuk seluruh item.

### A3: Pembaruan kondisi otomatis

1. Jika data yang di-approve memiliki laporan monitoring terkait, sistem dapat memicu pembaruan atribut "kondisi" pada master infrastruktur secara otomatis.

## Kondisi Akhir (Postcondition)

- **Jika approved**: Status menjadi `approved`. Data terpublikasi dan terkunci secara permanen. Data dapat dilihat oleh Publik dan semua aktor.
- **Jika rejected**: Status kembali ke `draft`. Kecamatan dapat mengedit dan mengajukan ulang. Catatan penolakan tersedia untuk referensi perbaikan.

## Aturan Bisnis Terkait

- **BR-VR-01**: Data baru dianggap final setelah disetujui Bappeda.
- **BR-VR-03**: Penolakan wajib disertai catatan alasan.
- **BR-MN-03**: Data yang sudah diverifikasi tidak dapat dihapus (audit trail).
- **BR-MN-04**: Persetujuan dapat memicu pembaruan kondisi pada master infrastruktur.
