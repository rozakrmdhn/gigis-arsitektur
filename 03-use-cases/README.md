# Use Cases

Bagian ini mendefinisikan kasus penggunaan (_Use Cases_) utama di dalam sistem GIGIS / MELAROSA, yang mencakup interaksi antara berbagai aktor (Publik, Desa, Kecamatan, Bappeda, OPD, Super Admin) dengan sistem sesuai dengan aturan bisnis yang telah ditetapkan.

## Diagram Use Case

```mermaid
flowchart LR
    %% Actors
    PB["Publik"]
    OD["Operator Desa"]
    OK["Operator Kecamatan"]
    OB["Operator Bappeda"]
    OP["Operator OPD"]
    SA["Super Admin"]

    subgraph "GIGIS / MELAROSA"
        UC0(["Melihat Peta & Data Read-Only"])

        UC1(["Pembuatan Plotting Anggaran"])
        
        UC2(["Geotagging Infrastruktur"])
        UC3(["Submit Geotagging ke Kecamatan"])
        
        UC4(["Verifikasi Geotagging"])
        
        UC5(["Pembuatan Draft Monitoring"])
        
        UC6(["Digitasi Infrastruktur Spasial"])
        UC7(["Submit Infrastruktur ke Bappeda"])
        
        UC8(["Approve/Reject Infrastruktur"])
        
        UC9(["Manajemen Pengguna & Sistem"])
    end

    PB --> UC0
    OP --> UC0
    OD --> UC0
    OK --> UC0
    OB --> UC0

    OB --> UC1
    
    OD --> UC2
    OD --> UC3
    
    OK --> UC4
    
    OB --> UC5
    
    OK --> UC6
    OK --> UC7
    
    OB --> UC8
    
    SA --> UC9

    %% Dependencies
    UC1 -.->|Syarat| UC2
    UC2 -.->|Alur| UC3
    UC3 -.->|Alur| UC4
    UC1 -.->|Syarat| UC5
    UC5 -.->|Acuan| UC6
    UC6 -.->|Alur| UC7
    UC7 -.->|Alur| UC8
```

## Deskripsi Use Case

1. **UC0: Melihat Peta & Data Read-Only**: Aktor Publik, Operator OPD, Desa, Kecamatan, dan Bappeda dapat melihat peta interaktif, batas wilayah, serta laporan/rekapitulasi sesuai batasan aksesnya masing-masing. Publik hanya dapat melihat data yang sudah disetujui.
2. **UC1: Pembuatan Plotting Anggaran**: Operator Bappeda membuat data plotting anggaran yang akan menjadi dasar (syarat) pelaksanaan pendataan infrastruktur di tahun berjalan.
3. **UC2: Geotagging Infrastruktur**: Operator Desa melakukan input form geotagging beserta titik lokasi awal. Proses ini **hanya bisa dilakukan jika Plotting Anggaran (UC1) sudah dibuat** oleh Bappeda untuk desa tersebut.
4. **UC3 & UC4: Submit & Verifikasi Geotagging**: Operator Desa mengajukan (submit) form geotagging ke tingkat Kecamatan. Operator Kecamatan bertugas memverifikasinya.
5. **UC5: Pembuatan Draft Monitoring**: Operator Bappeda membuat Draft Monitoring berdasarkan Plotting Anggaran (UC1) sebagai inisiasi proses pemantauan riwayat kondisi fisik infrastruktur.
6. **UC6: Digitasi Infrastruktur Spasial**: Operator Kecamatan melakukan penggambaran geometri (garis/poligon) dan pengisian atribut dinamis secara interaktif melalui WebGIS. **Proses ini wajib mengacu pada Draft Monitoring (UC5)** dari Bappeda.
7. **UC7 & UC8: Submit Infrastruktur & Approval Bappeda**: Operator Kecamatan mengajukan hasil digitasi spasial ke tingkat Bappeda. Bappeda melakukan persetujuan (_approve_) atau penolakan (_reject_ dengan catatan). Data yang disetujui menjadi data final yang terpublikasi.
8. **UC9: Manajemen Pengguna & Sistem**: Super Admin mengelola otorisasi, menambah akun, mengatur _role_, dan memetakan hak akses wilayah (_id_desa_, _id_kecamatan_).
