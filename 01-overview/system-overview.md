# System Overview

## 1. Deskripsi Sistem

GIGIS / MELAROSA merupakan aplikasi WebGIS yang digunakan untuk mengelola, memvisualisasikan, memantau, dan menyajikan informasi infrastruktur berbasis spasial.

Sistem mengintegrasikan data atribut dan data geografis sehingga informasi infrastruktur dapat dikelola dalam satu platform dan divisualisasikan melalui peta interaktif.

GIGIS / MELAROSA dirancang untuk mendukung proses pendataan infrastruktur melalui geotagging, digitasi geometri oleh operator teknis, monitoring kondisi dan realisasi infrastruktur berbasis alur verifikasi berjenjang, serta penyajian informasi spasial sebagai bahan pemantauan dan pengambilan keputusan.

---

## 2. Tujuan Sistem

Sistem dikembangkan untuk:

1. Menyediakan platform terpusat untuk pengelolaan data infrastruktur.
2. Mengintegrasikan data atribut dengan informasi geografis.
3. Menyediakan visualisasi infrastruktur melalui peta interaktif.
4. Mendukung proses geotagging lokasi infrastruktur oleh Operator Desa.
5. Mendukung proses digitasi dan pengelolaan geometri spasial oleh Operator Kecamatan.
6. Mendukung monitoring kondisi dan realisasi infrastruktur melalui alur verifikasi berjenjang (Desa → Kecamatan → Bappeda).
7. Menyediakan informasi spasial yang dapat digunakan untuk pemantauan dan analisis.
8. Menyediakan data dan informasi yang konsisten bagi pengguna sesuai dengan hak aksesnya.

---

## 3. Ruang Lingkup Sistem

Secara umum, sistem mencakup beberapa area fungsional berikut:

### 3.1 Manajemen Data Master

Digunakan untuk mengelola data referensi yang digunakan oleh sistem, seperti:

- Kecamatan
- Desa
- Jenis infrastruktur
- Kategori infrastruktur
- Status
- Referensi lainnya

### 3.2 Geotagging Infrastruktur

Digunakan oleh Operator Desa untuk melakukan pendataan awal lokasi infrastruktur melalui form geotagging. Operator Desa **hanya dapat melakukan geotagging apabila Operator Bappeda telah menerbitkan Plotting Anggaran** yang aktif untuk wilayah desa yang bersangkutan. Data geotagging yang diajukan akan diverifikasi secara berjenjang oleh Kecamatan dan Bappeda.

### 3.3 Manajemen Data Spasial

Digunakan oleh Operator Kecamatan untuk mengelola informasi geografis dari objek infrastruktur berdasarkan draft monitoring yang diterbitkan oleh Bappeda, meliputi:

- Point
- LineString
- Polygon
- Geometry editing
- Penyimpanan koordinat
- Visualisasi pada peta

Jenis geometry ditentukan berdasarkan karakteristik infrastruktur yang dikelola.

### 3.4 Monitoring Infrastruktur

Digunakan untuk mencatat dan memantau perkembangan, kondisi, serta realisasi infrastruktur. Proses monitoring diinisiasi oleh Bappeda melalui draft monitoring berdasarkan Plotting Anggaran, kemudian ditindaklanjuti oleh Kecamatan melalui digitasi data infrastruktur.

### 3.5 Visualisasi WebGIS

Sistem menyediakan peta interaktif untuk:

- Menampilkan layer geografis.
- Menampilkan objek infrastruktur.
- Menampilkan informasi atribut.
- Melakukan pencarian lokasi.
- Melakukan identifikasi objek pada peta.
- Melakukan digitasi dan editing geometry (oleh Operator Kecamatan).
- Menampilkan informasi spasial secara interaktif (dapat diakses publik).

### 3.6 Pelaporan dan Informasi

Sistem menyediakan informasi dan rekapitulasi yang dapat digunakan untuk pemantauan dan penyajian data infrastruktur.

---

## 4. Aktor Sistem

Sistem dapat digunakan oleh beberapa kelompok pengguna sesuai dengan kewenangan dan hak akses masing-masing.

Aktor utama sistem meliputi:

| Aktor | Role | Deskripsi |
|:---|:---|:---|
| Publik | `public` | Melihat peta infrastruktur dan batas wilayah secara umum tanpa login. |
| Super Admin | `super_admin` | Mengelola konfigurasi sistem, pengguna, role, dan permission secara penuh. |
| Operator Bappeda | `operator_bappeda` | Membuat plotting anggaran, draft monitoring, dan memverifikasi data akhir. |
| Operator Kecamatan | `operator_kecamatan` | Melakukan digitasi infrastruktur spasial berdasarkan draft monitoring Bappeda. |
| Operator Desa | `operator_desa` | Melakukan geotagging infrastruktur dan mengajukannya ke Kecamatan. |
| Operator OPD | `operator_opd` | Memantau (read-only) data infrastruktur dan laporan monitoring. |

Detail aktor, role, dan permission dijelaskan pada:

`02-requirements/actors.md`


---

## 5. Konsep Sistem

GIGIS / MELAROSA menggunakan pendekatan berbasis data spasial.

Setiap data infrastruktur dapat memiliki informasi atribut dan geometry yang saling berhubungan.

Secara konseptual:

```text
                    INFRASTRUKTUR
                         │
              ┌──────────┴──────────┐
              │                     │
         Data Atribut          Data Spasial
              │                     │
              │              Point / Line / Polygon
              │                     │
              └──────────┬──────────┘
                         │
                         ▼
                  DATABASE SPASIAL
                         │
                         ▼
                    BACKEND API
                         │
                         ▼
                     FRONTEND
                         │
              ┌──────────┴──────────┐
              │                     │
           Data Table             WebGIS
                                    │
                                    ▼
                               Peta Interaktif