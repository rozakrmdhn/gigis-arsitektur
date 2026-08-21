# UC-9: Manajemen Pengguna & Sistem

## Identitas

| Atribut | Nilai |
|:---|:---|
| **ID** | UC-9 |
| **Nama** | Manajemen Pengguna & Sistem |
| **Aktor Utama** | Super Admin |
| **Prioritas** | Tinggi |

## Deskripsi

Super Admin mengelola pengguna, peran (_role_), hak akses (_permission_), dan konfigurasi sistem secara keseluruhan. Termasuk di dalamnya pembuatan akun, penetapan wilayah kerja, dan pencabutan sesi.

## Prasyarat (Precondition)

- Super Admin sudah login dan terautentikasi.
- Super Admin memiliki hak akses penuh ke seluruh fitur dan konfigurasi sistem.

## Alur Utama (Main Flow)

1. Super Admin membuka modul Manajemen Pengguna.
2. Sistem menampilkan daftar pengguna yang terdaftar.
3. Super Admin memilih aksi yang diinginkan:

### Tambah Pengguna Baru

4. Super Admin memilih **Tambah Pengguna**.
5. Sistem menampilkan form pendaftaran.
6. Super Admin mengisi:
   - Nama lengkap.
   - Username / email.
   - Password awal.
   - Role (Operator Bappeda, Kecamatan, Desa, OPD).
   - Wilayah kerja (`id_desa` atau `id_kecamatan`).
7. Sistem menyimpan akun dan menetapkan permission sesuai role.

### Edit Pengguna

8. Super Admin memilih pengguna dan mengubah informasi (role, wilayah, status aktif).
9. Jika role atau wilayah diubah, sistem dapat mencabut (_revoke_) sesi JWT pengguna tersebut secara otomatis.

### Nonaktifkan / Hapus Pengguna

10. Super Admin menonaktifkan atau menghapus akun pengguna.
11. Sistem mencabut seluruh sesi aktif pengguna tersebut.

## Alur Alternatif

### A1: Manajemen role & permission

1. Super Admin membuka modul Role & Permission.
2. Sistem menampilkan daftar role dan permission yang terkonfigurasi.
3. Super Admin dapat menambah, mengedit, atau menghapus konfigurasi permission pada suatu role.

### A2: Manajemen data master

1. Super Admin mengelola data master wilayah (kecamatan, desa), tipe infrastruktur, kategori, dan referensi lainnya.

## Kondisi Akhir (Postcondition)

- Pengguna baru terdaftar dan dapat login sesuai role-nya.
- Perubahan role/wilayah berlaku efektif setelah pengguna login ulang.
- Sesi yang dicabut memaksa pengguna melakukan login ulang.

## Aturan Bisnis Terkait

- **TR-AU-02**: Perubahan role/wilayah dapat mencabut sesi JWT secara sepihak.
- **TR-AU-03**: Otorisasi dilakukan secara _stateless_ menggunakan CASL berdasarkan JWT payload.
- **FR-AU-02**: Akses data dibatasi berdasarkan wilayah (`id_desa` / `id_kecamatan`).
