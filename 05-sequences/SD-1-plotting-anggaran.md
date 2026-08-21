# Sequence Diagram: Pembuatan Plotting Anggaran

Diagram urutan ini menggambarkan interaksi teknis saat Operator Bappeda membuat Plotting Anggaran baru.

**Terkait**: [UC-1](../03-use-cases/UC-1-pembuatan-plotting-anggaran.md) | [AD-1](../04-activities/AD-1-plotting-anggaran.md)

```mermaid
sequenceDiagram
    autonumber
    actor OB as Operator Bappeda
    participant FE as Frontend (React)
    participant API as Backend API (Hapi.js)
    participant DB as Database (PostgreSQL)

    OB->>FE: Buka Modul Plotting Anggaran
    FE->>API: GET /v1/plotting-anggaran
    API->>API: verifyJWT() & checkAbility('read', 'PlottingAnggaran')
    API->>DB: SELECT * FROM plotting_anggaran WHERE tahun = ...
    DB-->>API: JSON Array
    API-->>FE: HTTP 200 OK (Daftar Plotting)
    FE-->>OB: Tampilkan Daftar Plotting Anggaran

    OB->>FE: Klik "Buat Plotting Anggaran Baru"
    FE-->>OB: Tampilkan Form Input

    OB->>FE: Isi & Submit Form
    FE->>API: POST /v1/plotting-anggaran
    API->>API: verifyJWT() & checkAbility('create', 'PlottingAnggaran')
    API->>API: Validasi Joi (tahun, sumber_dana, wilayah, nilai)

    alt Validasi Gagal
        API-->>FE: HTTP 400 Bad Request
        FE-->>OB: Tampilkan Pesan Error
    else Validasi Berhasil
        API->>DB: INSERT INTO plotting_anggaran (...)
        DB-->>API: 1 row inserted (status: aktif)
        API-->>FE: HTTP 201 Created
        FE-->>OB: Notifikasi "Plotting Anggaran Berhasil Dibuat"
    end
```

## Penjelasan Teknis

1. **Authorization**: Hanya role `operator_bappeda` yang memiliki ability `create` pada subject `PlottingAnggaran`.
2. **Validasi**: Skema Joi memvalidasi field wajib (tahun anggaran, sumber dana, wilayah cakupan, nilai).
3. **Status**: Plotting Anggaran tersimpan dengan status `aktif`, yang kemudian menjadi prasyarat geotagging Desa dan draft monitoring.
