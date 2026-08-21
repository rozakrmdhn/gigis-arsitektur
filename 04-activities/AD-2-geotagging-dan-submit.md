# Activity Diagram: Geotagging & Submit ke Kecamatan

Diagram aktivitas ini menggambarkan alur kerja Operator Desa dalam melakukan geotagging infrastruktur dan mengajukan form ke tingkat Kecamatan untuk diverifikasi.

**Use Case Terkait**: [UC-2 Geotagging Infrastruktur](../03-use-cases/UC-2-geotagging-infrastruktur.md), [UC-3 Submit Geotagging ke Kecamatan](../03-use-cases/UC-3-submit-geotagging-ke-kecamatan.md)

```mermaid
flowchart TD
    Start((Mulai)) --> A[Buka Modul Geotagging]

    subgraph Validasi ["Validasi Prasyarat"]
        A --> B{Plotting Anggaran\nAktif untuk Desa?}
        B -->|Tidak| C["Tampilkan Pesan:\n'Plotting Anggaran belum tersedia.\nHubungi Bappeda.'"]
        C --> Stop((Berhenti))
    end

    subgraph Desa ["Operator Desa"]
        B -->|Ya| D[Pilih 'Buat Geotagging Baru']
        D --> E[Tampilkan Form & Peta Interaktif]
        E --> F["Isi Form:\nNama, Jenis, Kondisi,\nKeterangan, Foto"]
        F --> G["Tandai Titik Lokasi\npada Peta"]
        G --> H{Koordinat di\nDalam Batas Desa?}
        H -->|Tidak| I[Tampilkan Peringatan]
        I --> G
        H -->|Ya| J[Simpan Form Geotagging]
        J --> K["Status: DRAFT"]
        K --> L{Submit ke\nKecamatan?}
        L -->|Belum| M[Kembali ke Daftar Geotagging]
        M --> L
        L -->|Ya| N[Pilih Form & Submit]
    end

    subgraph Submit ["Proses Submit"]
        N --> O[Konfirmasi Pengajuan]
        O --> P["Status: VERIFIKASI KECAMATAN"]
        P --> Q[Data Terkunci dari Edit]
        Q --> R[Catat Log Pengajuan]
    end

    R --> End1((Selesai))
```

## Penjelasan Alur

1. **Validasi Prasyarat**: Sistem memeriksa apakah Plotting Anggaran sudah aktif untuk desa. Jika belum, proses dihentikan.
2. **Pengisian Form**: Operator Desa mengisi informasi infrastruktur dan menandai titik lokasi pada peta.
3. **Validasi Koordinat**: Sistem memastikan titik geotagging berada di dalam batas poligon administrasi desa.
4. **Simpan Draft**: Data tersimpan dengan status `draft`. Operator dapat mengedit sebelum di-submit.
5. **Submit**: Operator mengajukan form ke Kecamatan. Status berubah menjadi `verifikasi_kecamatan` dan data terkunci.
