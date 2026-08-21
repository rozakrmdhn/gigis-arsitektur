# Sequence Diagram: Digitasi Infrastruktur & Submit ke Bappeda

Diagram urutan ini menggambarkan interaksi teknis saat Operator Kecamatan melakukan digitasi infrastruktur spasial berdasarkan Draft Monitoring dan mengajukannya ke Bappeda.

**Terkait**: [UC-6](../03-use-cases/UC-6-digitasi-infrastruktur-spasial.md), [UC-7](../03-use-cases/UC-7-submit-infrastruktur-ke-bappeda.md) | [AD-5](../04-activities/AD-5-digitasi-dan-submit.md)

```mermaid
sequenceDiagram
    autonumber
    actor OK as Operator Kecamatan
    participant FE as Frontend (React)
    participant MAP as WebGIS (Leaflet/Turf.js)
    participant API as Backend API (Hapi.js)
    participant DB as Database (PostGIS)

    %% Validasi Draft Monitoring
    OK->>FE: Buka Modul Digitasi Infrastruktur
    FE->>API: GET /v1/draft-monitoring?id_kecamatan={id}
    API->>API: verifyJWT() & checkAbility('read', 'DraftMonitoring')
    API->>DB: SELECT * FROM draft_monitoring WHERE id_kecamatan = ...
    DB-->>API: JSON Array
    API-->>FE: HTTP 200 OK

    alt Draft Monitoring Tidak Tersedia
        FE-->>OK: Pesan "Draft Monitoring belum tersedia. Hubungi Bappeda."
    else Draft Monitoring Tersedia
        FE-->>OK: Tampilkan Daftar Draft Monitoring
        OK->>FE: Pilih Draft Monitoring Sebagai Acuan

        %% Digitasi pada Peta
        FE->>MAP: Render Peta Interaktif + Layer Batas Wilayah
        MAP-->>OK: Peta Siap untuk Digitasi

        OK->>MAP: Gambar Geometri (LineString / Polygon / Point)
        MAP->>FE: Capture GeoJSON Coordinates
        FE-->>OK: Tampilkan Form Atribut Dinamis

        OK->>FE: Isi Atribut & Simpan
        FE->>API: POST /v1/infrastruktur/{tipe}/segmen
        Note right of FE: Body: { geojson, atribut (JSONB),<br/>draft_monitoring_id, plotting_id }
        API->>API: verifyJWT() & checkAbility('create', 'InfrastrukturSegmen')
        API->>API: Validasi Joi & Validasi Tipe Geometri

        alt Tipe Geometri Tidak Sesuai
            API-->>FE: HTTP 400 "Tipe geometri tidak sesuai dengan infrastruktur"
            FE-->>OK: Tampilkan Pesan Error
        else Tipe Geometri Valid
            API->>DB: INSERT INTO infrastruktur_segmen (geom, atribut, status, ...) VALUES (ST_GeomFromGeoJSON(...), ...)
            DB-->>API: 1 row inserted (status: draft)
            API-->>FE: HTTP 201 Created
            FE-->>OK: Notifikasi "Infrastruktur Berhasil Disimpan (Draft)"
        end

        %% Submit ke Bappeda
        OK->>FE: Pilih Data & Klik "Submit ke Bappeda"
        FE->>API: PUT /v1/infrastruktur/{tipe}/segmen/{id}/submit
        API->>API: verifyJWT() & checkAbility('update', 'InfrastrukturSegmen')
        API->>DB: UPDATE infrastruktur_segmen SET status = 'verifikasi_bappeda', submitted_at = NOW()
        DB-->>API: 1 row updated
        API-->>FE: HTTP 200 OK
        FE-->>OK: Notifikasi "Berhasil Diajukan ke Bappeda"
    end
```

## Penjelasan Teknis

1. **Validasi Draft Monitoring**: Digitasi hanya bisa dilakukan jika Draft Monitoring tersedia untuk kecamatan terkait (BR-MN-02).
2. **GeoJSON → WKB**: Frontend mengirim geometri dalam format GeoJSON. Backend mengkonversinya ke format PostGIS menggunakan `ST_GeomFromGeoJSON` (TR-SP-03).
3. **Validasi Geometri**: API memastikan tipe geometri (LineString/Polygon/Point) sesuai dengan definisi tipe infrastruktur (TR-SP-01).
4. **Atribut Dinamis**: Atribut spesifik per tipe infrastruktur disimpan sebagai JSONB (FR-SP-03).
5. **State Transition**: `draft` → `verifikasi_bappeda`. Data terkunci dari edit oleh Kecamatan setelah submit (BR-VR-02).
