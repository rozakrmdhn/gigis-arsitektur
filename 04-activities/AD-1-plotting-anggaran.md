# Activity Diagram: Pembuatan Plotting Anggaran

Diagram aktivitas ini menggambarkan alur kerja pembuatan Plotting Anggaran oleh Operator Bappeda. Plotting Anggaran menjadi **prasyarat** bagi Operator Desa untuk melakukan geotagging (AD-2) dan bagi Bappeda untuk membuat Draft Monitoring (AD-4).

**Use Case Terkait**: [UC-1 Pembuatan Plotting Anggaran](../03-use-cases/UC-1-pembuatan-plotting-anggaran.md)

```mermaid
flowchart TD
    Start((Mulai)) --> A[Buka Modul Plotting Anggaran]

    subgraph Bappeda ["Operator Bappeda"]
        A --> B[Pilih 'Buat Plotting Anggaran Baru']
        B --> C[Isi Form Plotting Anggaran]
        C --> D["Input: Tahun Anggaran, Sumber Dana,\nWilayah Cakupan, Nilai Anggaran"]
        D --> E[Simpan Data]
        E --> F{Validasi Data}
        F -->|Tidak Valid| G[Tampilkan Pesan Error]
        G --> C
        F -->|Valid| H[Simpan ke Database]
    end

    subgraph Sistem ["Sistem"]
        H --> I["Status: AKTIF"]
        I --> J["Plotting Anggaran Tersedia\nuntuk Wilayah Terkait"]
        J --> K["Operator Desa Dapat\nMelakukan Geotagging"]
        J --> L["Bappeda Dapat Membuat\nDraft Monitoring"]
    end

    K --> End1((Selesai))
    L --> End1
```

## Penjelasan Alur

1. **Inisiasi**: Operator Bappeda membuka modul Plotting Anggaran dan memilih buat baru.
2. **Pengisian Form**: Operator mengisi tahun anggaran, sumber dana, wilayah cakupan (kecamatan/desa target), dan nilai anggaran.
3. **Validasi**: Sistem memvalidasi kelengkapan data. Jika tidak valid, dikembalikan ke form.
4. **Penyimpanan**: Data tersimpan dengan status **aktif**.
5. **Dampak**: Plotting Anggaran aktif membuka akses bagi Operator Desa untuk geotagging dan bagi Bappeda untuk membuat Draft Monitoring.
