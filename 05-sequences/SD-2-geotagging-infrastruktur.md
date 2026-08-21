# Sequence Diagram: Geotagging & Submit oleh Desa

Diagram urutan ini menggambarkan interaksi teknis saat Operator Desa melakukan geotagging infrastruktur dan mengajukan form ke Kecamatan. Termasuk validasi prasyarat Plotting Anggaran.

**Terkait**: [UC-2](../03-use-cases/UC-2-geotagging-infrastruktur.md), [UC-3](../03-use-cases/UC-3-submit-geotagging-ke-kecamatan.md) | [AD-2](../04-activities/AD-2-geotagging-dan-submit.md)

```mermaid
sequenceDiagram
    autonumber
    actor OD as Operator Desa
    participant FE as Frontend (React)
    participant API as Backend API (Hapi.js)
    participant DB as Database (PostgreSQL)

    %% Validasi Prasyarat
    OD->>FE: Buka Modul Geotagging
    FE->>API: GET /v1/plotting-anggaran?id_desa={id_desa}&status=aktif
    API->>API: verifyJWT() & checkAbility('read', 'PlottingAnggaran')
    API->>DB: SELECT * FROM plotting_anggaran WHERE wilayah @> id_desa AND status = 'aktif'
    DB-->>API: Result

    alt Plotting Anggaran Tidak Ditemukan
        API-->>FE: HTTP 200 OK (empty array)
        FE-->>OD: Pesan "Plotting Anggaran belum tersedia. Hubungi Bappeda."
    else Plotting Anggaran Aktif
        API-->>FE: HTTP 200 OK (data plotting)
        FE-->>OD: Tampilkan Form Geotagging & Peta

        %% Input Geotagging
        OD->>FE: Isi Form & Tandai Titik Lokasi di Peta
        FE->>FE: Capture Koordinat (lat, lng)
        FE->>API: POST /v1/geotagging
        API->>API: verifyJWT() & checkAbility('create', 'Geotagging')
        API->>API: Validasi Joi (nama, jenis, koordinat, plotting_id)

        API->>DB: SELECT ST_Contains(geom, ST_Point(lng, lat)) FROM batas_desa WHERE id_desa = ...

        alt Koordinat di Luar Batas Desa
            DB-->>API: false
            API-->>FE: HTTP 400 "Koordinat di luar batas administrasi desa"
            FE-->>OD: Tampilkan Peringatan
        else Koordinat Valid
            DB-->>API: true
            API->>DB: INSERT INTO geotagging (...) VALUES (..., status='draft')
            DB-->>API: 1 row inserted
            API-->>FE: HTTP 201 Created
            FE-->>OD: Notifikasi "Geotagging Berhasil Disimpan (Draft)"
        end

        %% Submit ke Kecamatan
        OD->>FE: Pilih Geotagging & Klik "Submit ke Kecamatan"
        FE->>API: PUT /v1/geotagging/{id}/submit
        API->>API: verifyJWT() & checkAbility('update', 'Geotagging')
        API->>DB: UPDATE geotagging SET status = 'verifikasi_kecamatan', submitted_at = NOW()
        DB-->>API: 1 row updated
        API-->>FE: HTTP 200 OK
        FE-->>OD: Notifikasi "Berhasil Diajukan ke Kecamatan"
    end
```

## Penjelasan Teknis

1. **Validasi Plotting Anggaran**: Sebelum form geotagging ditampilkan, sistem memeriksa keberadaan Plotting Anggaran aktif untuk desa terkait (FR-GT-00, BR-PL-03).
2. **Validasi Spasial**: API menggunakan fungsi PostGIS `ST_Contains` untuk memastikan titik geotagging berada di dalam poligon batas desa (BR-WL-01).
3. **State Transition**: `draft` → `verifikasi_kecamatan`. Setelah submit, data terkunci dari edit oleh Desa (BR-VR-02).
4. **Ownership**: Data secara otomatis dikaitkan ke `id_desa` dari JWT payload pengguna.
