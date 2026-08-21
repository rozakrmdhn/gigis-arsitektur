# Activity Diagram: Approve/Reject Infrastruktur oleh Bappeda

Diagram aktivitas ini menggambarkan alur kerja Operator Bappeda dalam melakukan persetujuan atau penolakan data infrastruktur spasial yang diajukan oleh Operator Kecamatan.

**Use Case Terkait**: [UC-8 Approve/Reject Infrastruktur](../03-use-cases/UC-8-approve-reject-infrastruktur.md)

```mermaid
flowchart TD
    Start((Mulai)) --> A[Buka Modul Verifikasi Infrastruktur]

    subgraph Bappeda ["Operator Bappeda"]
        A --> B["Tampilkan Daftar Data\nStatus: VERIFIKASI BAPPEDA"]
        B --> C[Pilih Data untuk Diperiksa]
        C --> D["Tampilkan Detail:\nGeometri di Peta, Atribut,\nDraft Monitoring, Plotting Anggaran,\nLog Riwayat"]
        D --> E[Evaluasi Akhir]
        E --> F{Apakah Disetujui?}
        F -->|Ya| G["Pilih: Approve"]
        F -->|Tidak| H["Pilih: Reject"]
    end

    subgraph Approve ["Proses Persetujuan"]
        G --> I["Status: APPROVED"]
        I --> J[Publikasi ke Database Spasial Utama]
        J --> K[Data Terkunci Permanen]
        K --> L["Data Dapat Dilihat\noleh Publik & Semua Aktor"]
        L --> M[Catat Log Persetujuan]
    end

    subgraph Reject ["Proses Penolakan"]
        H --> N["Wajib Isi Catatan\nVerifikasi / Alasan"]
        N --> O["Status Kembali: DRAFT"]
        O --> P[Data Dibuka Kunci untuk Kecamatan]
        P --> Q[Catat Log Penolakan]
    end

    M --> End1((Selesai))
    Q --> End2["Operator Kecamatan\nDapat Memperbaiki & Submit Ulang"]
    End2 --> End3((Selesai))
```

## Penjelasan Alur

1. **Daftar Verifikasi**: Bappeda melihat daftar data infrastruktur berstatus `verifikasi_bappeda`.
2. **Evaluasi**: Bappeda memeriksa geometri pada peta, atribut, keterkaitan dengan Draft Monitoring dan Plotting Anggaran, serta log riwayat.
3. **Keputusan**:
   - **Approve**: Status menjadi `approved`. Data dipublikasikan ke database spasial utama, terkunci permanen, dan dapat dilihat oleh semua aktor termasuk publik.
   - **Reject**: Wajib mengisi catatan alasan. Status kembali ke `draft` dan data dibuka kunci agar Kecamatan dapat memperbaiki.
4. **Pembaruan Kondisi**: Jika data yang di-approve memiliki laporan monitoring terkait, sistem dapat memicu pembaruan atribut "kondisi" pada master infrastruktur.
