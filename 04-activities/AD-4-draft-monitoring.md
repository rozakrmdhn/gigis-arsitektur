# Activity Diagram: Pembuatan Draft Monitoring

Diagram aktivitas ini menggambarkan alur kerja Operator Bappeda dalam membuat Draft Monitoring berdasarkan Plotting Anggaran. Draft monitoring menjadi **acuan resmi** bagi Operator Kecamatan untuk melakukan digitasi infrastruktur spasial.

**Use Case Terkait**: [UC-5 Pembuatan Draft Monitoring](../03-use-cases/UC-5-pembuatan-draft-monitoring.md)

```mermaid
flowchart TD
    Start((Mulai)) --> A[Buka Modul Monitoring]

    subgraph Validasi ["Validasi Prasyarat"]
        A --> B{Plotting Anggaran\nAktif Tersedia?}
        B -->|Tidak| C["Tampilkan Pesan:\n'Buat Plotting Anggaran terlebih dahulu.'"]
        C --> Stop((Berhenti))
    end

    subgraph Bappeda ["Operator Bappeda"]
        B -->|Ya| D[Pilih 'Buat Draft Monitoring Baru']
        D --> E[Tampilkan Form Draft Monitoring]
        E --> F["Isi Form:\nPlotting Anggaran Dasar,\nWilayah Cakupan Kecamatan,\nPeriode Monitoring,\nTarget Infrastruktur"]
        F --> G[Simpan Draft]
    end

    subgraph Sistem ["Sistem"]
        G --> H{Validasi Data}
        H -->|Tidak Valid| I[Tampilkan Pesan Error]
        I --> E
        H -->|Valid| J[Simpan ke Database]
        J --> K["Draft Monitoring Terkait\nke Plotting Anggaran"]
        K --> L["Draft Dapat Diakses oleh\nOperator Kecamatan Terkait"]
    end

    L --> End1((Selesai))
```

## Penjelasan Alur

1. **Validasi Prasyarat**: Sistem memeriksa keberadaan Plotting Anggaran aktif. Tanpa plotting, draft tidak dapat dibuat.
2. **Pengisian Form**: Bappeda memilih Plotting Anggaran sebagai dasar, menentukan wilayah kecamatan target, periode monitoring, dan target infrastruktur yang harus didigitasi.
3. **Penyimpanan**: Draft tersimpan dan dikaitkan ke `plotting_id`.
4. **Dampak**: Operator Kecamatan di wilayah target kini dapat melihat draft dan melakukan digitasi infrastruktur spasial (AD-5).
