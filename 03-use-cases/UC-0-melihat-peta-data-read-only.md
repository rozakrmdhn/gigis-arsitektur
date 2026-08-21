# UC-0: Melihat Peta & Data Read-Only

## Identitas

| Atribut | Nilai |
|:---|:---|
| **ID** | UC-0 |
| **Nama** | Melihat Peta & Data Read-Only |
| **Aktor Utama** | Publik |
| **Aktor Lainnya** | Operator Desa, Operator Kecamatan, Operator Bappeda, Operator OPD |
| **Prioritas** | Tinggi |

## Deskripsi

Aktor mengakses sistem untuk melihat peta interaktif, data infrastruktur, dan batas wilayah administrasi secara _read-only_. Cakupan data yang ditampilkan bergantung pada peran dan status autentikasi aktor.

## Prasyarat (Precondition)

- Sistem dan layanan peta (WebGIS) tersedia dan aktif.
- Data infrastruktur dan batas wilayah sudah tersedia di basis data.

## Alur Utama (Main Flow)

1. Aktor membuka halaman peta interaktif (WebGIS).
2. Sistem menampilkan _basemap_ dan layer batas wilayah administrasi (desa & kecamatan).
3. Sistem menampilkan objek infrastruktur yang telah disetujui (_approved_) pada peta.
4. Aktor dapat melakukan:
   - Zoom in/out pada peta.
   - Klik objek infrastruktur untuk melihat detail atribut (_popup_).
   - Mencari lokasi atau objek tertentu.
5. Sistem menampilkan informasi atribut objek yang diklik.

## Alur Alternatif

### A1: Aktor adalah pengguna terautentikasi

1. Jika aktor sudah login, sistem menampilkan data tambahan sesuai hak aksesnya:
   - **Operator Desa**: Data infrastruktur dan laporan monitoring khusus desanya.
   - **Operator Kecamatan**: Data infrastruktur di seluruh kecamatannya.
   - **Operator Bappeda**: Seluruh data di tingkat kabupaten.
   - **Operator OPD**: Seluruh data secara _read-only_.

### A2: Tidak ada data infrastruktur

1. Jika belum ada data infrastruktur yang disetujui, sistem menampilkan peta kosong dengan hanya layer batas wilayah.

## Kondisi Akhir (Postcondition)

- Aktor berhasil melihat peta dan informasi infrastruktur tanpa mengubah data apapun.

## Aturan Bisnis Terkait

- Publik hanya dapat melihat data infrastruktur yang berstatus _approved_.
- Operator OPD hanya memiliki akses _read-only_ lintas wilayah.
- Operator Desa hanya dapat melihat data di wilayah desanya (`id_desa`).
