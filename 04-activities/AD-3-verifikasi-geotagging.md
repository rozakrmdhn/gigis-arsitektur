# Activity Diagram: Verifikasi Geotagging oleh Kecamatan

Diagram aktivitas ini menggambarkan alur kerja Operator Kecamatan dalam memverifikasi form geotagging yang diajukan oleh Operator Desa.

**Use Case Terkait**: [UC-4 Verifikasi Geotagging](../03-use-cases/UC-4-verifikasi-geotagging.md)

```mermaid
flowchart TD
    Start((Mulai)) --> A[Buka Modul Verifikasi Geotagging]

    subgraph Kecamatan ["Operator Kecamatan"]
        A --> B["Tampilkan Daftar Geotagging\nStatus: VERIFIKASI KECAMATAN"]
        B --> C[Pilih Form untuk Diperiksa]
        C --> D["Tampilkan Detail:\nForm, Lokasi di Peta, Foto"]
        D --> E[Periksa Kelengkapan & Keakuratan]
        E --> F{Apakah Data Sesuai?}
        F -->|Ya| G["Pilih: Setujui / Verifikasi"]
        F -->|Tidak| H["Pilih: Tolak"]
    end

    subgraph Approve ["Proses Verifikasi"]
        G --> I["Status: TERVERIFIKASI KECAMATAN"]
        I --> J[Catat Log Verifikasi]
    end

    subgraph Reject ["Proses Penolakan"]
        H --> K["Wajib Isi Catatan\nVerifikasi / Alasan"]
        K --> L["Status Kembali: DRAFT"]
        L --> M[Data Dibuka Kunci untuk Desa]
        M --> N[Catat Log Penolakan]
    end

    J --> End1((Selesai))
    N --> End2["Operator Desa\nDapat Memperbaiki"]
    End2 --> End3((Selesai))
```

## Penjelasan Alur

1. **Daftar Verifikasi**: Operator Kecamatan melihat daftar form geotagging berstatus `verifikasi_kecamatan` di wilayah kecamatannya.
2. **Pemeriksaan**: Operator memeriksa detail form, lokasi di peta, dan kelengkapan data.
3. **Keputusan**:
   - **Setujui**: Status berubah menjadi `terverifikasi_kecamatan`. Data menjadi referensi valid.
   - **Tolak**: Wajib mengisi catatan alasan. Status kembali ke `draft` dan data dibuka kunci agar Operator Desa dapat memperbaiki.
4. **Log**: Setiap keputusan tercatat di log verifikasi beserta nama verifikator dan waktu.
