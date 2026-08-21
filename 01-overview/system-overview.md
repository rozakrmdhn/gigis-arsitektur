# System Overview

## 1. Deskripsi Sistem

GIGIS / MELAROSA merupakan aplikasi WebGIS yang digunakan untuk mengelola, memvisualisasikan, memantau, dan menyajikan informasi infrastruktur berbasis spasial.

Sistem mengintegrasikan data atribut dan data geografis sehingga informasi infrastruktur dapat dikelola dalam satu platform dan divisualisasikan melalui peta interaktif.

GIGIS / MELAROSA dirancang untuk mendukung proses pengelolaan data infrastruktur, inventarisasi spasial, digitasi geometry, monitoring kondisi dan realisasi infrastruktur, serta penyajian informasi sebagai bahan pemantauan dan pengambilan keputusan.

---

## 2. Tujuan Sistem

Sistem dikembangkan untuk:

1. Menyediakan platform terpusat untuk pengelolaan data infrastruktur.
2. Mengintegrasikan data atribut dengan informasi geografis.
3. Menyediakan visualisasi infrastruktur melalui peta interaktif.
4. Mendukung proses digitasi dan pengelolaan geometry.
5. Mendukung monitoring kondisi dan realisasi infrastruktur.
6. Menyediakan informasi spasial yang dapat digunakan untuk pemantauan dan analisis.
7. Menyediakan data dan informasi yang konsisten bagi pengguna sesuai dengan hak aksesnya.

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

### 3.2 Manajemen Infrastruktur

Digunakan untuk mengelola data utama infrastruktur, meliputi:

- Identitas infrastruktur
- Informasi lokasi
- Atribut infrastruktur
- Jenis dan kategori
- Status
- Informasi pendukung lainnya

### 3.3 Manajemen Data Spasial

Digunakan untuk mengelola informasi geografis dari objek infrastruktur, meliputi:

- Point
- LineString
- Polygon
- Geometry editing
- Penyimpanan koordinat
- Visualisasi pada peta

Jenis geometry ditentukan berdasarkan karakteristik infrastruktur yang dikelola.

### 3.4 Monitoring Infrastruktur

Digunakan untuk mencatat dan memantau perkembangan, kondisi, serta realisasi infrastruktur berdasarkan periode atau parameter monitoring yang ditetapkan.

### 3.5 Visualisasi WebGIS

Sistem menyediakan peta interaktif untuk:

- Menampilkan layer geografis.
- Menampilkan objek infrastruktur.
- Menampilkan informasi atribut.
- Melakukan pencarian lokasi.
- Melakukan identifikasi objek pada peta.
- Melakukan digitasi dan editing geometry.
- Menampilkan informasi spasial secara interaktif.

### 3.6 Pelaporan dan Informasi

Sistem menyediakan informasi dan rekapitulasi yang dapat digunakan untuk pemantauan dan penyajian data infrastruktur.

---

## 4. Aktor Sistem

Sistem dapat digunakan oleh beberapa kelompok pengguna sesuai dengan kewenangan dan hak akses masing-masing.

Aktor utama sistem meliputi:

| Aktor | Deskripsi |
|---|---|
| Administrator | Mengelola konfigurasi, pengguna, role, permission, dan data sistem sesuai kewenangan. |
| Operator | Mengelola dan memperbarui data infrastruktur serta data spasial sesuai kewenangan. |
| Pengguna | Mengakses informasi dan visualisasi data sesuai dengan hak akses yang diberikan. |

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