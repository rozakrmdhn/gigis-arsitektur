# Sequence Diagram: Verifikasi Geotagging oleh Kecamatan

Diagram urutan ini menggambarkan interaksi teknis saat Operator Kecamatan memverifikasi (approve/reject) form geotagging yang diajukan oleh Operator Desa.

**Terkait**: [UC-4](../03-use-cases/UC-4-verifikasi-geotagging.md) | [AD-3](../04-activities/AD-3-verifikasi-geotagging.md)

```mermaid
sequenceDiagram
    autonumber
    actor OK as Operator Kecamatan
    participant FE as Frontend (React)
    participant API as Backend API (Hapi.js)
    participant DB as Database (PostgreSQL)

    %% Lihat Daftar
    OK->>FE: Buka Modul Verifikasi Geotagging
    FE->>API: GET /v1/geotagging?status=verifikasi_kecamatan&id_kecamatan={id}
    API->>API: verifyJWT() & checkAbility('read', 'Geotagging')
    API->>DB: SELECT * FROM geotagging WHERE status = 'verifikasi_kecamatan' AND id_kecamatan = ...
    DB-->>API: JSON Array
    API-->>FE: HTTP 200 OK
    FE-->>OK: Tampilkan Daftar Geotagging Menunggu Verifikasi

    %% Detail
    OK->>FE: Pilih Form untuk Diperiksa
    FE->>API: GET /v1/geotagging/{id}
    API->>DB: SELECT *, ST_AsGeoJSON(geom) FROM geotagging WHERE id = ...
    DB-->>API: Detail + GeoJSON
    API-->>FE: HTTP 200 OK
    FE-->>OK: Tampilkan Detail Form & Lokasi di Peta

    %% Keputusan
    alt Approve (Setujui)
        OK->>FE: Klik "Setujui"
        FE->>API: PUT /v1/geotagging/{id}/verify
        API->>API: verifyJWT() & checkAbility('update', 'Geotagging')
        API->>DB: UPDATE geotagging SET status = 'terverifikasi_kecamatan', verifikator = '...', verified_at = NOW()
        DB-->>API: 1 row updated
        API->>DB: INSERT INTO verification_log (geotagging_id, action, actor, notes, timestamp)
        DB-->>API: Log recorded
        API-->>FE: HTTP 200 OK
        FE-->>OK: Notifikasi "Geotagging Disetujui"
    else Reject (Tolak)
        OK->>FE: Klik "Tolak" & Isi Catatan Alasan
        FE->>API: PUT /v1/geotagging/{id}/reject
        Note right of FE: Body: { catatan: "Alasan penolakan..." }
        API->>API: verifyJWT() & checkAbility('update', 'Geotagging')
        API->>DB: UPDATE geotagging SET status = 'draft', catatan_verifikasi = '...'
        DB-->>API: 1 row updated
        API->>DB: INSERT INTO verification_log (geotagging_id, action, actor, notes, timestamp)
        DB-->>API: Log recorded
        API-->>FE: HTTP 200 OK
        FE-->>OK: Notifikasi "Geotagging Ditolak"
    end
```

## Penjelasan Teknis

1. **Scope Filtering**: Query difilter berdasarkan `id_kecamatan` dari JWT payload, memastikan Kecamatan hanya melihat data di wilayahnya (BR-WL-02).
2. **Approve**: Status berubah `verifikasi_kecamatan` → `terverifikasi_kecamatan`. Data tetap terkunci dari Desa.
3. **Reject**: Status kembali ke `draft`. Data dibuka kunci agar Desa dapat memperbaiki. Catatan penolakan wajib diisi (BR-VR-03).
4. **Audit Trail**: Setiap keputusan dicatat di `verification_log` (FR-WF-04).
