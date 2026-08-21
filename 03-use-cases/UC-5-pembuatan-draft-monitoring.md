# UC-5: Pembuatan Draft Monitoring

## Identitas

| Atribut | Nilai |
|:---|:---|
| **ID** | UC-5 |
| **Nama** | Pembuatan Draft Monitoring |
| **Aktor Utama** | Operator Bappeda |
| **Prioritas** | Tinggi |

## Deskripsi

Operator Bappeda membuat Draft Monitoring berdasarkan Plotting Anggaran (UC-1) yang telah disetujui. Draft monitoring ini menjadi **acuan resmi** bagi Operator Kecamatan untuk melakukan digitasi data infrastruktur spasial (UC-6).

## Prasyarat (Precondition)

- Operator Bappeda sudah login dan terautentikasi.
- Plotting Anggaran sudah aktif dan tersedia di sistem (UC-1 selesai).

## Alur Utama (Main Flow)

1. Operator Bappeda membuka modul Monitoring.
2. Operator Bappeda memilih **Buat Draft Monitoring Baru**.
3. Sistem menampilkan form draft monitoring.
4. Operator Bappeda mengisi informasi:
   - Plotting Anggaran yang menjadi dasar.
   - Wilayah cakupan (kecamatan target).
   - Periode monitoring.
   - Target infrastruktur yang harus didigitasi.
   - Keterangan/instruksi tambahan.
5. Operator Bappeda menyimpan draft monitoring.
6. Sistem menyimpan draft dan mengaitkannya ke `plotting_id`.
7. Sistem menampilkan konfirmasi berhasil.
8. Draft monitoring kini dapat diakses oleh Operator Kecamatan terkait.

## Alur Alternatif

### A1: Edit draft monitoring

1. Operator Bappeda dapat mengedit draft monitoring yang sudah dibuat selama belum ada digitasi (UC-6) yang mengacu padanya.

### A2: Plotting Anggaran tidak ditemukan

1. Jika tidak ada Plotting Anggaran aktif, sistem menampilkan pesan bahwa draft monitoring tidak dapat dibuat tanpa dasar anggaran.

## Kondisi Akhir (Postcondition)

- Draft monitoring tersimpan di basis data dan terkait dengan Plotting Anggaran.
- Operator Kecamatan di wilayah target kini dapat melihat draft dan melakukan digitasi infrastruktur (UC-6).

## Aturan Bisnis Terkait

- **BR-MN-01**: Draft monitoring hanya dapat dibuat oleh Bappeda berdasarkan Plotting Anggaran yang disetujui.
- **BR-MN-02**: Kecamatan wajib mengacu pada draft monitoring saat digitasi.
