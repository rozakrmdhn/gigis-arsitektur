# Sequence Diagram: Pembuatan Draft Monitoring

Diagram urutan ini menggambarkan interaksi teknis saat Operator Bappeda membuat Draft Monitoring berdasarkan Plotting Anggaran. Draft ini menjadi acuan digitasi bagi Operator Kecamatan.

**Terkait**: [UC-5](../03-use-cases/UC-5-pembuatan-draft-monitoring.md) | [AD-4](../04-activities/AD-4-draft-monitoring.md)

```mermaid
sequenceDiagram
    autonumber
    actor OB as Operator Bappeda
    participant FE as Frontend (React)
    participant API as Backend API (Hapi.js)
    participant DB as Database (PostgreSQL)

    OB->>FE: Buka Modul Monitoring
    FE->>API: GET /v1/plotting-anggaran?status=aktif
    API->>API: verifyJWT() & checkAbility('read', 'PlottingAnggaran')
    API->>DB: SELECT * FROM plotting_anggaran WHERE status = 'aktif'
    DB-->>API: JSON Array
    API-->>FE: HTTP 200 OK (Daftar Plotting Aktif)
    FE-->>OB: Tampilkan Daftar Plotting Anggaran

    OB->>FE: Klik "Buat Draft Monitoring Baru"
    FE-->>OB: Tampilkan Form Draft Monitoring

    OB->>FE: Isi Form & Submit
    Note right of FE: Body: { plotting_id, id_kecamatan,<br/>periode, target_infrastruktur,<br/>keterangan }
    FE->>API: POST /v1/draft-monitoring
    API->>API: verifyJWT() & checkAbility('create', 'DraftMonitoring')
    API->>API: Validasi Joi (plotting_id, wilayah, periode)

    API->>DB: SELECT id FROM plotting_anggaran WHERE id = plotting_id AND status = 'aktif'

    alt Plotting Anggaran Tidak Valid
        DB-->>API: 0 rows
        API-->>FE: HTTP 400 "Plotting Anggaran tidak ditemukan atau tidak aktif"
        FE-->>OB: Tampilkan Pesan Error
    else Plotting Anggaran Valid
        DB-->>API: 1 row
        API->>DB: INSERT INTO draft_monitoring (plotting_id, id_kecamatan, periode, target, ...)
        DB-->>API: 1 row inserted
        API-->>FE: HTTP 201 Created
        FE-->>OB: Notifikasi "Draft Monitoring Berhasil Dibuat"
    end
```

## Penjelasan Teknis

1. **Validasi Relasi**: API memvalidasi bahwa `plotting_id` yang dirujuk benar-benar ada dan berstatus aktif sebelum menyimpan draft (BR-MN-01).
2. **Authorization**: Hanya role `operator_bappeda` yang memiliki ability `create` pada subject `DraftMonitoring`.
3. **Scope**: Draft monitoring dikaitkan ke `id_kecamatan` target, sehingga hanya Operator Kecamatan di wilayah tersebut yang dapat melihatnya.
