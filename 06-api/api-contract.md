# API Contract

Backend GIGIS / MELAROSA menyediakan sekumpulan RESTful API yang melayani integrasi data spasial dan sistem monitoring dengan aplikasi Frontend.

## 1. Konvensi Umum

*   **Base URL & Versi**: Seluruh endpoint memiliki prefix `/v1` atau `/api/v1` (contoh: `https://api.domain.com/api/v1/infrastruktur/jalan/segmen`).
*   **Format Data**: Payload untuk request dan response secara default menggunakan `application/json`.
*   **Autentikasi**: Sebagian besar endpoint memerlukan header `Authorization: Bearer <JWT_TOKEN>`.
*   **Validasi**: API menggunakan pustaka validasi (Joi) di tingkat _route_. Jika input tidak valid, API akan merespons dengan HTTP Status `400 Bad Request`.

## 2. Autentikasi & Sesi (Auth Module)

Modul Auth menangani registrasi, login, dan manajemen token JWT.

| Method | Endpoint | Deskripsi | Auth |
| :--- | :--- | :--- | :--- |
| `POST` | `/v1/signin` | Login pengguna untuk mendapatkan Access Token dan Refresh Token | Public |
| `POST` | `/v1/refresh` | Memperbarui Access Token menggunakan Refresh Token | Public |
| `POST` | `/v1/signout` | Logout pengguna dari sesi saat ini (mencabut Access Token) | JWT |
| `POST` | `/v1/logout-all` | Mencabut seluruh sesi yang aktif dari pengguna terkait | JWT |
| `GET` | `/v1/sessions` | Mengambil daftar sesi pengguna yang aktif saat ini | JWT |

## 3. Infrastruktur & Aset Spasial Terpadu

Endpoint ini mengimplementasikan konsep *unified architecture* yang dapat melayani berbagai tipe infrastruktur secara dinamis melalui parameter path `{tipe}`.

### 3.1. Endpoint Master & Registry

| Method | Endpoint | Deskripsi | Auth |
| :--- | :--- | :--- | :--- |
| `GET` | `/v1/infrastruktur` | Mendapatkan daftar registry tipe infrastruktur yang didukung sistem (Jalan, Jembatan, dll) | Optional |
| `GET` | `/v1/infrastruktur/{tipe}` | Mengambil data master infrastruktur spesifik berdasarkan tipenya | Optional |

### 3.2. Geometri Segmen (Garis / Titik)

| Method | Endpoint | Deskripsi | Auth |
| :--- | :--- | :--- | :--- |
| `GET` | `/v1/infrastruktur/{tipe}/segmen` | Mengambil semua segmen spasial untuk tipe tertentu (umumnya merespons dengan format GeoJSON) | Optional |
| `POST` | `/v1/infrastruktur/{tipe}/segmen` | Membuat segmen baru untuk tipe tertentu | JWT (Create) |
| `GET` | `/v1/infrastruktur/{tipe}/segmen/{id}` | Mengambil detail segmen beserta JSONB atributnya berdasarkan ID | JWT (Read) |
| `PUT/PATCH` | `/v1/infrastruktur/{tipe}/segmen/{id}`| Memperbarui data, atribut, maupun geometri (koordinat) segmen | JWT (Update) |
| `DELETE`| `/v1/infrastruktur/{tipe}/segmen/{id}`| Menghapus segmen | JWT (Delete) |

### 3.3. Geometri Area (Poligon)

| Method | Endpoint | Deskripsi | Auth |
| :--- | :--- | :--- | :--- |
| `GET` | `/v1/infrastruktur/{tipe}/area` | Mengambil semua poligon area untuk tipe tertentu (dalam format GeoJSON) | Optional |
| `POST` | `/v1/infrastruktur/{tipe}/area` | Membuat poligon area baru | JWT (Create) |

## 4. Alur Persetujuan & Verifikasi (Workflow)

Endpoint khusus yang dirancang untuk mendukung alur kerja _approval_ data (contohnya antara tingkat Kecamatan dan Kabupaten/Bappeda).

| Method | Endpoint | Deskripsi | Auth |
| :--- | :--- | :--- | :--- |
| `PUT` | `/v1/infrastruktur/{tipe}/segmen/{id}/submit-bappeda` | Mengirim data segmen (yang baru di-digitasi/diubah) untuk diverifikasi oleh Bappeda | JWT |
| `PUT` | `/v1/infrastruktur/{tipe}/segmen/{id}/approve-bappeda` | Bappeda menyetujui (_approve_) data segmen yang diajukan | JWT |
| `POST`| `/v1/infrastruktur/{tipe}/segmen/batch-submit-bappeda` | Mengajukan banyak segmen sekaligus ke Bappeda | JWT |

> [!TIP]
> **Dynamic Routing & RBAC**
> Endpoint `/infrastruktur/{tipe}` akan secara dinamis memetakan ke tabel dan validasi yang sesuai dengan `{tipe}` tersebut. Akses (RBAC) divalidasi pada level _middleware_ menggunakan kombinasi dari parameter tipe dan hak ases pengguna (`checkAbility(action, subject)`).
