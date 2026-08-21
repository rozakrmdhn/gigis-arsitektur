# Sequence Diagram: Approve/Reject Infrastruktur oleh Bappeda

Diagram urutan ini menggambarkan interaksi teknis saat Operator Bappeda melakukan persetujuan atau penolakan data infrastruktur spasial yang diajukan oleh Operator Kecamatan.

**Terkait**: [UC-8](../03-use-cases/UC-8-approve-reject-infrastruktur.md) | [AD-6](../04-activities/AD-6-approve-reject-infrastruktur.md)

```mermaid
sequenceDiagram
    autonumber
    actor OB as Operator Bappeda
    participant FE as Frontend (React)
    participant API as Backend API (Hapi.js)
    participant DB as Database (PostGIS)

    %% Lihat Daftar
    OB->>FE: Buka Modul Verifikasi Infrastruktur
    FE->>API: GET /v1/infrastruktur/{tipe}/segmen?status=verifikasi_bappeda
    API->>API: verifyJWT() & checkAbility('read', 'InfrastrukturSegmen')
    API->>DB: SELECT *, ST_AsGeoJSON(geom) FROM infrastruktur_segmen WHERE status = 'verifikasi_bappeda'
    DB-->>API: JSON Array + GeoJSON
    API-->>FE: HTTP 200 OK
    FE-->>OB: Tampilkan Daftar & Geometri di Peta

    %% Detail
    OB->>FE: Pilih Data untuk Diperiksa
    FE->>API: GET /v1/infrastruktur/{tipe}/segmen/{id}
    API->>DB: SELECT segmen.*, dm.*, pa.* FROM infrastruktur_segmen JOIN draft_monitoring JOIN plotting_anggaran
    DB-->>API: Detail lengkap + relasi
    API-->>FE: HTTP 200 OK
    FE-->>OB: Tampilkan Detail (Geometri, Atribut, Draft Monitoring, Plotting Anggaran, Log)

    %% Keputusan
    alt Approve (Setujui)
        OB->>FE: Klik "Approve"
        FE->>API: PUT /v1/infrastruktur/{tipe}/segmen/{id}/approve
        API->>API: verifyJWT() & checkAbility('update', 'InfrastrukturSegmen')
        API->>DB: BEGIN TRANSACTION
        API->>DB: UPDATE infrastruktur_segmen SET status = 'approved', verifikator = '...', approved_at = NOW()
        DB-->>API: 1 row updated

        opt Jika ada laporan monitoring terkait
            API->>DB: UPDATE infrastruktur_tipe SET kondisi = (latest monitoring kondisi)
        end

        API->>DB: INSERT INTO verification_log (entity_id, action='approved', actor, timestamp)
        DB-->>API: Log recorded
        API->>DB: COMMIT
        DB-->>API: Transaction Committed
        API-->>FE: HTTP 200 OK
        FE-->>OB: Notifikasi "Data Disetujui & Dipublikasikan"

    else Reject (Tolak)
        OB->>FE: Klik "Reject" & Isi Catatan Alasan
        FE->>API: PUT /v1/infrastruktur/{tipe}/segmen/{id}/reject
        Note right of FE: Body: { catatan: "Alasan penolakan..." }
        API->>API: verifyJWT() & checkAbility('update', 'InfrastrukturSegmen')
        API->>DB: BEGIN TRANSACTION
        API->>DB: UPDATE infrastruktur_segmen SET status = 'draft', catatan_verifikasi = '...'
        DB-->>API: 1 row updated
        API->>DB: INSERT INTO verification_log (entity_id, action='rejected', actor, notes, timestamp)
        DB-->>API: Log recorded
        API->>DB: COMMIT
        DB-->>API: Transaction Committed
        API-->>FE: HTTP 200 OK
        FE-->>OB: Notifikasi "Data Ditolak"
    end
```

## Penjelasan Teknis

1. **Database Transaction**: Operasi approve/reject menggunakan transaksi database untuk memastikan konsistensi antara update status dan pencatatan log (atomicity).
2. **Approve**: Status berubah `verifikasi_bappeda` → `approved`. Data dipublikasikan dan terkunci secara permanen. Data dapat dilihat oleh Publik.
3. **Reject**: Status kembali ke `draft`. Data dibuka kunci agar Kecamatan dapat memperbaiki. Catatan penolakan wajib diisi (BR-VR-03).
4. **Trigger Update Master**: Saat approve, jika ada laporan monitoring terkait, sistem dapat memperbarui atribut kondisi pada master infrastruktur (BR-MN-04).
5. **Audit Trail**: Setiap keputusan dicatat di `verification_log` yang bersifat immutable (BR-MN-03, FR-WF-04).
