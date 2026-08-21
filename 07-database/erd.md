# Entity Relationship Diagram (ERD)

Berikut adalah diagram relasi entitas inti dari sistem GIGIS / MELAROSA, khususnya yang berpusat pada manajemen infrastruktur spasial dan proses monitoring.

```mermaid
erDiagram
    USERS {
        uuid id PK
        string email
        string role
        integer id_kecamatan
        bigint id_desa
    }

    BATAS_KECAMATAN {
        integer id PK
        string nama_kecamatan
        geometry geom
    }

    BATAS_DESA {
        bigint id PK
        string nama_desa
        integer id_kecamatan FK
        geometry geom
    }

    INFRASTRUKTUR_TIPE {
        uuid id PK
        string kode
        string nama
    }

    JALAN_POROSDESA {
        uuid id PK
        integer kode_ruas
        string nama_ruas
        bigint id_desa FK
        integer id_kecamatan FK
        geometry geom
    }

    INFRASTRUKTUR_SEGMEN {
        uuid id PK
        string tipe_kode FK
        uuid parent_id FK "Logical FK to specific master"
        uuid plotting_id FK
        geometry geom "LineString/Point"
        jsonb atribut
        string status_verifikasi
    }

    INFRASTRUKTUR_AREA {
        uuid id PK
        string tipe_kode FK
        uuid parent_id FK "Logical FK to specific master"
        uuid plotting_id FK
        geometry geom "Polygon"
        jsonb atribut
        string status_verifikasi
    }

    MONITORING_LAPORAN {
        uuid id PK
        bigint id_desa FK
        integer id_kecamatan FK
        uuid user_id FK
        uuid plotting_id FK
        string status
    }

    MONITORING_LAPORAN_SEGMEN {
        uuid laporan_id FK
        uuid segmen_id FK
    }

    PLOTTING_ANGGARAN {
        uuid id PK
        bigint id_desa FK
        integer id_kecamatan FK
        string tahun_anggaran
    }

    MONITORING_LOG {
        uuid id PK
        uuid segmen_id FK
        uuid user_id FK
        string kondisi
        string keterangan
    }

    %% Relationships
    BATAS_KECAMATAN ||--o{ BATAS_DESA : "memiliki"
    BATAS_KECAMATAN ||--o{ USERS : "dikelola_oleh"
    BATAS_DESA ||--o{ USERS : "dikelola_oleh"
    
    BATAS_DESA ||--o{ JALAN_POROSDESA : "terdapat"
    
    JALAN_POROSDESA ||--o{ INFRASTRUKTUR_SEGMEN : "has_many (via parent_id)"
    JALAN_POROSDESA ||--o{ INFRASTRUKTUR_AREA : "has_many (via parent_id)"
    
    INFRASTRUKTUR_TIPE ||--o{ INFRASTRUKTUR_SEGMEN : "mengklasifikasikan"
    
    USERS ||--o{ MONITORING_LAPORAN : "membuat"
    USERS ||--o{ MONITORING_LOG : "mencatat"
    
    MONITORING_LAPORAN ||--o{ MONITORING_LAPORAN_SEGMEN : "terdiri_dari"
    INFRASTRUKTUR_SEGMEN ||--o{ MONITORING_LAPORAN_SEGMEN : "dilaporkan_dalam"
    
    INFRASTRUKTUR_SEGMEN ||--o{ MONITORING_LOG : "memiliki_history"
    
    PLOTTING_ANGGARAN ||--o{ INFRASTRUKTUR_SEGMEN : "direncanakan_pada"
    PLOTTING_ANGGARAN ||--o{ INFRASTRUKTUR_AREA : "direncanakan_pada"
    PLOTTING_ANGGARAN ||--o{ MONITORING_LAPORAN : "di_monitoring"

```

> [!NOTE]
> ERD di atas menampilkan relasi konseptual utama. Tabel referensi lain seperti `jembatan_porosdesa`, `jalan_lingkungan` memiliki relasi yang serupa dengan `jalan_porosdesa` terhadap tabel `infrastruktur_segmen` / `infrastruktur_area`.
