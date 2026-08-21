# Sequence Diagram: Verifikasi Laporan Monitoring

Diagram urutan (Sequence Diagram) di bawah menggambarkan interaksi teknis antar komponen ketika laporan monitoring diajukan oleh tingkat Desa dan diverifikasi hingga tingkat Bappeda.

```mermaid
sequenceDiagram
    autonumber
    actor OD as Operator Desa
    participant FE as Frontend (React)
    participant API as Backend API (Hapi.js)
    participant DB as Database (PostgreSQL)
    actor OB as Operator Bappeda

    %% Submission by Desa
    OD->>FE: Klik "Submit Laporan ke Kecamatan"
    FE->>API: PUT /v1/infrastruktur/{tipe}/segmen/{id}/submit-bappeda
    API->>API: verifyJWT() & checkAbility('update')
    
    API->>DB: UPDATE status_verifikasi = 'verifikasi_kecamatan'
    DB-->>API: 1 row updated
    
    API-->>FE: HTTP 200 OK (Status Updated)
    FE-->>OD: Notifikasi "Berhasil Diajukan"
    
    %% Bappeda Approval
    note over OB, API: Proses serupa juga terjadi di tingkat Kecamatan sebelum ke Bappeda
    OB->>FE: Buka Daftar Pengajuan (Menunggu Approval)
    FE->>API: GET /v1/infrastruktur/{tipe}/segmen?status=verifikasi_bappeda
    API->>DB: SELECT * FROM infrastruktur_segmen WHERE status = ...
    DB-->>API: JSON Array Data
    API-->>FE: HTTP 200 OK
    FE-->>OB: Tampilkan Tabel Data
    
    OB->>FE: Klik "Setujui" pada Laporan X
    FE->>API: PUT /v1/infrastruktur/{tipe}/segmen/{id}/approve-bappeda
    API->>API: verifyJWT() & checkAbility('update', 'Jalan')
    
    API->>DB: UPDATE status_verifikasi = 'terverifikasi', verifikator = 'Nama OB'
    
    alt Jika tipe laporan memengaruhi master infrastruktur
        API->>DB: UPDATE infrastruktur_tipe (Misal: Update Kondisi Fisik Master)
    end
    
    DB-->>API: Transaction Committed
    API-->>FE: HTTP 200 OK (Approved)
    FE-->>OB: Notifikasi "Data Disetujui"
```

## Penjelasan Teknis

1. **Authorization Middleware**: Sebelum _controller logic_ dieksekusi, API secara ketat memeriksa apakah token JWT valid (`verifyJWT`) dan apakah aktor yang melakukan _request_ memiliki hak akses (ability) yang cukup dengan mengevaluasi aturan CASL.
2. **State Transition**: Perpindahan _state_ (dari `draft` -> `verifikasi_kecamatan` -> `verifikasi_bappeda` -> `terverifikasi`) dijamin konsistensinya melalui _database transaction_.
3. **Trigger Update Master**: Saat status laporan diubah menjadi disetujui (terverifikasi), backend mungkin secara asinkron atau dalam satu transaksi akan menyinkronkan data atribut kondisi terbaru pada tabel master agar mencerminkan kondisi lapangan yang paling mutakhir.
