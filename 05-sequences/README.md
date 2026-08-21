# Sequence Diagrams

Bagian ini mendokumentasikan diagram urutan (_Sequence Diagrams_) yang menggambarkan interaksi teknis antar komponen sistem GIGIS / MELAROSA. Setiap diagram mengacu pada Use Case dan Activity Diagram yang telah didefinisikan sebelumnya.

## Daftar Sequence Diagram

| No | File | Interaksi | UC / AD Terkait |
|:---|:---|:---|:---|
| 1 | [SD-1](SD-1-plotting-anggaran.md) | Pembuatan Plotting Anggaran | UC-1 / AD-1 |
| 2 | [SD-2](SD-2-geotagging-infrastruktur.md) | Geotagging & Submit oleh Desa | UC-2, UC-3 / AD-2 |
| 3 | [SD-3](SD-3-verifikasi-geotagging.md) | Verifikasi Geotagging oleh Kecamatan | UC-4 / AD-3 |
| 4 | [SD-4](SD-4-draft-monitoring.md) | Pembuatan Draft Monitoring | UC-5 / AD-4 |
| 5 | [SD-5](SD-5-digitasi-dan-submit.md) | Digitasi Infrastruktur & Submit ke Bappeda | UC-6, UC-7 / AD-5 |
| 6 | [SD-6](SD-6-approve-reject-infrastruktur.md) | Approve/Reject Infrastruktur oleh Bappeda | UC-8 / AD-6 |

## Komponen Sistem

Semua sequence diagram menggunakan komponen berikut:

| Komponen | Teknologi | Peran |
|:---|:---|:---|
| **Frontend** | React + React Router v7 + Vite | UI, peta interaktif (Leaflet/Turf.js) |
| **Backend API** | Hapi.js (Modular Monolith) | Business logic, otorisasi (CASL), routing |
| **Database** | PostgreSQL + PostGIS | Penyimpanan data atribut dan geometri spasial |
