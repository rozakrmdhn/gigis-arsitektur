# Activity Diagram: Digitasi Infrastruktur & Submit ke Bappeda

Diagram aktivitas ini menggambarkan alur kerja Operator Kecamatan dalam melakukan digitasi data infrastruktur spasial berdasarkan Draft Monitoring dari Bappeda, lalu mengajukannya ke Bappeda untuk diverifikasi.

**Use Case Terkait**: [UC-6 Digitasi Infrastruktur Spasial](../03-use-cases/UC-6-digitasi-infrastruktur-spasial.md), [UC-7 Submit Infrastruktur ke Bappeda](../03-use-cases/UC-7-submit-infrastruktur-ke-bappeda.md)

```mermaid
flowchart TD
    Start((Mulai)) --> A[Buka Modul Digitasi Infrastruktur]

    subgraph Validasi ["Validasi Prasyarat"]
        A --> B{Draft Monitoring\nTersedia untuk Kecamatan?}
        B -->|Tidak| C["Tampilkan Pesan:\n'Draft Monitoring belum tersedia.\nHubungi Bappeda.'"]
        C --> Stop((Berhenti))
    end

    subgraph Kecamatan ["Operator Kecamatan"]
        B -->|Ya| D[Pilih Draft Monitoring Sebagai Acuan]
        D --> E[Tampilkan Peta Interaktif WebGIS]
        E --> F[Pilih Tipe Infrastruktur]
        F --> G{Tentukan Jenis Geometri}
        G -->|Garis| H1["Gambar LineString\npada Peta"]
        G -->|Area| H2["Gambar Polygon\npada Peta"]
        G -->|Titik| H3["Gambar Point\npada Peta"]
        H1 --> I["Isi Form Atribut Dinamis:\nNama, Kondisi, Panjang/Luas,\nAtribut Spesifik"]
        H2 --> I
        H3 --> I
        I --> J[Simpan Data]
        J --> K{Validasi Geometri & Atribut}
        K -->|Tidak Valid| L[Tampilkan Pesan Error]
        L --> I
        K -->|Valid| M["Status: DRAFT"]
        M --> N{Submit ke Bappeda?}
        N -->|Belum| O[Kembali ke Daftar]
        O --> N
        N -->|Ya| P[Pilih Data & Submit]
    end

    subgraph Submit ["Proses Submit"]
        P --> Q[Konfirmasi Pengajuan]
        Q --> R["Status: VERIFIKASI BAPPEDA"]
        R --> S[Data Terkunci dari Edit]
        S --> T[Catat Log Pengajuan]
    end

    T --> End1((Selesai))
```

## Penjelasan Alur

1. **Validasi Prasyarat**: Sistem memeriksa keberadaan Draft Monitoring untuk kecamatan. Tanpa draft, digitasi tidak dapat dilakukan.
2. **Pemilihan Acuan**: Kecamatan memilih Draft Monitoring sebagai acuan digitasi.
3. **Digitasi Geometri**: Operator menggambar geometri sesuai tipe infrastruktur (LineString, Polygon, atau Point) pada peta interaktif.
4. **Atribut Dinamis**: Operator mengisi form atribut yang tampil secara dinamis berdasarkan tipe infrastruktur (disimpan sebagai JSONB).
5. **Validasi**: Sistem memvalidasi kesesuaian tipe geometri dan kelengkapan atribut.
6. **Submit**: Data yang sudah didigitasi diajukan ke Bappeda. Status berubah menjadi `verifikasi_bappeda` dan data terkunci.
