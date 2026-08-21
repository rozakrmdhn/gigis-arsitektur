# Activity Diagram: Pengajuan Data Spasial

Diagram aktivitas ini menggambarkan alur kerja pengajuan (submission) data infrastruktur spasial dari tingkat Desa hingga mendapat persetujuan dari Bappeda.

```mermaid
flowchart TD
    subgraph Desa [Operator Desa]
        A[Mulai Digitasi WebGIS] --> B[Gambar Geometri & Isi Atribut]
        B --> C{Simpan Data}
        C -->|Valid| D[Status: DRAFT]
        C -->|Tidak Valid| B
        
        D --> E[Review Data Internal]
        E --> F[Submit ke Kecamatan]
    end

    subgraph Kecamatan [Operator Kecamatan]
        F --> G[Data Masuk: VERIFIKASI KECAMATAN]
        G --> H[Evaluasi Geometri & Atribut]
        H --> I{Apakah Sesuai?}
        I -->|Tidak| J[Reject dengan Catatan]
        I -->|Ya| K[Submit ke Bappeda]
    end
    
    J -.-> |Data dikembalikan ke Desa| D

    subgraph Bappeda [Operator Bappeda]
        K --> L[Data Masuk: VERIFIKASI BAPPEDA]
        L --> M[Evaluasi Akhir & Pleno]
        M --> N{Apakah Disetujui?}
        N -->|Tidak| O[Reject dengan Catatan]
        N -->|Ya| P[Approve Data]
    end
    
    O -.-> |Data dikembalikan ke Kecamatan/Desa| D
    
    subgraph Sistem [Database Terpusat]
        P --> Q[Status: TERVERIFIKASI]
        Q --> R[Data Terkunci / Read-Only bagi Desa]
        R --> S[Publikasi di Peta Utama]
    end
```

## Penjelasan Alur

1. **Pembuatan Draft**: Operator Desa menggambar segmen jalan/jembatan. Selama statusnya `DRAFT`, operator desa dapat bebas mengubah geometri dan atribut.
2. **Pengajuan Pertama**: Saat data dirasa sudah valid, Operator Desa menekan tombol _Submit_. Status berubah menjadi `verifikasi_kecamatan`. Saat ini, fitur _editing_ untuk akun desa dikunci.
3. **Verifikasi Tingkat 1 (Kecamatan)**: Operator Kecamatan melihat daftar usulan yang masuk. Jika ada kesalahan, data dapat di-_reject_ dan kembali menjadi `DRAFT` (dengan melampirkan catatan revisi). Jika benar, diteruskan menjadi `verifikasi_bappeda`.
4. **Verifikasi Akhir (Bappeda)**: Bappeda memegang otoritas penuh. Setelah di-_approve_, status data berubah menjadi `terverifikasi` dan menjadi bagian dari master data spasial kabupaten.
