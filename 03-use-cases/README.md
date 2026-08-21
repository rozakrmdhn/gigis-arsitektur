# Use Cases

Bagian ini mendefinisikan kasus penggunaan (_Use Cases_) utama di dalam sistem GIGIS / MELAROSA, yang mencakup interaksi antara berbagai aktor (Desa, Kecamatan, Bappeda, OPD) dengan sistem.

## Diagram Use Case

```mermaid
flowchart LR
    %% Actors
    OD["Operator Desa"]
    OK["Operator Kecamatan"]
    OB["Operator Bappeda"]
    OP["Operator OPD"]
    SA["Super Admin"]

    subgraph "GIGIS / MELAROSA"
        UC1(["Login & Autentikasi"])
        
        UC2(["Digitasi Infrastruktur (Segmen/Area)"])
        UC3(["Input Atribut Infrastruktur"])
        UC4(["Submit Data ke Kecamatan"])
        
        UC5(["Verifikasi Data Desa"])
        UC6(["Submit Data ke Bappeda"])
        
        UC7(["Approve/Reject Data"])
        UC8(["Plotting Anggaran"])
        
        UC9(["Monitoring Kondisi Fisik"])
        UC10(["Input Realisasi Pembangunan"])
        
        UC11(["Manajemen Pengguna & Role"])
    end

    OD --> UC1
    OK --> UC1
    OB --> UC1
    OP --> UC1
    SA --> UC1

    OD --> UC2
    OD --> UC3
    OD --> UC4
    OD --> UC9
    OD --> UC10

    OK --> UC5
    OK --> UC6
    OK --> UC9
    OK --> UC10

    OB --> UC7
    OB --> UC8

    OP --> UC2
    OP --> UC3

    SA --> UC11
```

## Deskripsi Use Case

1. **Digitasi Infrastruktur (UC2 & UC3)**: Operator Desa menggambar geometri segmen (garis/titik) atau area (poligon) pada peta WebGIS dan mengisi form atribut spesifik sesuai tipe infrastruktur. Data berstatus `draft`.
2. **Pengajuan Data (UC4 & UC6)**: Operator Desa mengirim data yang telah selesai didigitasi ke tingkat Kecamatan. Operator Kecamatan memeriksanya, lalu meneruskannya ke Bappeda.
3. **Verifikasi dan Approval (UC5 & UC7)**: Bappeda melakukan persetujuan akhir. Data yang disetujui akan dipublikasikan secara resmi ke dalam basis data spasial utama dan terkunci dari perubahan tingkat bawah.
4. **Monitoring & Realisasi (UC9 & UC10)**: Secara berkala, pengguna di lapangan memperbarui kondisi fisik infrastruktur beserta dokumentasi foto.
5. **Plotting Anggaran (UC8)**: Bappeda memetakan alokasi anggaran pembangunan jalan/jembatan berdasarkan tahun anggaran yang di-link langsung ke objek spasial terkait.
