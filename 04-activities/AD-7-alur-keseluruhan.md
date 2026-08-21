# Activity Diagram: Alur Keseluruhan (End-to-End)

Diagram aktivitas ini menggambarkan alur kerja keseluruhan sistem GIGIS / MELAROSA dari awal (Plotting Anggaran) hingga akhir (Publikasi Data), menunjukkan bagaimana setiap aktor berinteraksi secara berurutan.

**Use Case Terkait**: UC-1 s/d UC-8

```mermaid
flowchart TD
    Start((Mulai)) --> PA

    subgraph Phase1 ["Fase 1: Perencanaan — Operator Bappeda"]
        PA["Buat Plotting Anggaran\n(UC-1)"] --> PA_OK["Plotting Anggaran: AKTIF"]
    end

    PA_OK --> GT
    PA_OK --> DM

    subgraph Phase2 ["Fase 2: Pendataan Lapangan — Operator Desa"]
        GT["Geotagging Infrastruktur\n(UC-2)"] --> GT_SAVE["Form Geotagging: DRAFT"]
        GT_SAVE --> GT_SUB["Submit ke Kecamatan\n(UC-3)"]
        GT_SUB --> GT_STATUS["Form: VERIFIKASI KECAMATAN"]
    end

    subgraph Phase3a ["Fase 3a: Verifikasi Geotagging — Operator Kecamatan"]
        GT_STATUS --> VG["Verifikasi Geotagging\n(UC-4)"]
        VG --> VG_DEC{Keputusan?}
        VG_DEC -->|Reject| VG_REJ["Kembali ke DRAFT\n+ Catatan"]
        VG_DEC -->|Approve| VG_OK["Geotagging: TERVERIFIKASI"]
    end

    VG_REJ -.->|Perbaiki| GT

    subgraph Phase3b ["Fase 3b: Inisiasi Monitoring — Operator Bappeda"]
        DM["Buat Draft Monitoring\n(UC-5)"] --> DM_OK["Draft Monitoring: TERSEDIA\nuntuk Kecamatan"]
    end

    subgraph Phase4 ["Fase 4: Digitasi Spasial — Operator Kecamatan"]
        DM_OK --> DIG["Digitasi Infrastruktur\n(UC-6)"]
        DIG --> DIG_SAVE["Infrastruktur: DRAFT"]
        DIG_SAVE --> DIG_SUB["Submit ke Bappeda\n(UC-7)"]
        DIG_SUB --> DIG_STATUS["Infrastruktur: VERIFIKASI BAPPEDA"]
    end

    subgraph Phase5 ["Fase 5: Persetujuan Akhir — Operator Bappeda"]
        DIG_STATUS --> APR["Approve/Reject\n(UC-8)"]
        APR --> APR_DEC{Keputusan?}
        APR_DEC -->|Reject| APR_REJ["Kembali ke DRAFT\n+ Catatan"]
        APR_DEC -->|Approve| APR_OK["Infrastruktur: APPROVED"]
    end

    APR_REJ -.->|Perbaiki| DIG

    subgraph Phase6 ["Fase 6: Publikasi"]
        APR_OK --> PUB["Publikasi ke Database\nSpasial Utama"]
        PUB --> LOCK["Data Terkunci Permanen"]
        LOCK --> VIEW["Dapat Dilihat oleh\nPublik & Semua Aktor"]
    end

    VIEW --> End1((Selesai))
```

## Ringkasan Fase

| Fase | Aktor | Aktivitas | Status Data |
|:---|:---|:---|:---|
| **1. Perencanaan** | Bappeda | Buat Plotting Anggaran | Aktif |
| **2. Pendataan** | Desa | Geotagging + Submit | Draft → Verifikasi Kecamatan |
| **3a. Verifikasi** | Kecamatan | Verifikasi Geotagging | Terverifikasi / Reject |
| **3b. Inisiasi** | Bappeda | Buat Draft Monitoring | Tersedia |
| **4. Digitasi** | Kecamatan | Digitasi Spasial + Submit | Draft → Verifikasi Bappeda |
| **5. Approval** | Bappeda | Approve / Reject | Approved / Reject |
| **6. Publikasi** | Sistem | Publikasi & Kunci Data | Final / Read-Only |

## Alur Penolakan (Reject Path)

- **Reject oleh Kecamatan (Fase 3a)**: Form geotagging dikembalikan ke Operator Desa dengan catatan. Desa memperbaiki dan submit ulang.
- **Reject oleh Bappeda (Fase 5)**: Data infrastruktur dikembalikan ke Operator Kecamatan dengan catatan. Kecamatan memperbaiki dan submit ulang.
