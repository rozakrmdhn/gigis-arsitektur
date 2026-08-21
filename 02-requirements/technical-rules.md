# Technical Rules

Aturan teknis memuat batasan dan kebijakan yang bersumber dari kebutuhan implementasi sistem, arsitektur, dan keamanan aplikasi GIGIS / MELAROSA.

## 1. Aturan Data Spasial

*   **TR-SP-01 (Konsistensi Tipe Geometri)**: Segmen spasial harus berjenis geometri yang sesuai dengan tipe infrastrukturnya. Jika tipe infrastruktur didefinisikan sebagai garis, maka koordinat yang dimasukkan (melalui endpoint `/segmen`) harus berupa `LineString`. Demikian halnya dengan `/area` yang harus berupa `Polygon`. Sistem akan menolak input geometri yang tidak sesuai tipe.
*   **TR-SP-02 (Sistem Koordinat)**: Seluruh data geometri wajib disimpan menggunakan sistem koordinat **WGS 84 (SRID 4326)**. Konversi dari sistem koordinat lain harus dilakukan sebelum data dikirim ke API.
*   **TR-SP-03 (Format Transmisi Geometri)**: Data geometri ditransmisikan antara Frontend dan Backend menggunakan format **GeoJSON**. Backend bertanggung jawab mengkonversi GeoJSON ke format biner WKB (Well-Known Binary) untuk penyimpanan di PostGIS.

## 2. Aturan Autentikasi & Sesi

*   **TR-AU-01 (Token Expiry)**: Access Token (JWT) memiliki batas waktu kedaluwarsa (_expiry_) yang singkat demi keamanan. Aplikasi klien wajib menggunakan mekanisme _Refresh Token_ di _background_ (`/v1/refresh`) untuk memperpanjang sesi tanpa perlu _login_ ulang.
*   **TR-AU-02 (Pencabutan Sesi)**: Jika admin mengubah _role_ atau wilayah (`id_desa`) dari suatu akun pengguna, sistem dapat secara sepihak mencabut (_revoke_) _session_ JWT pengguna tersebut sehingga klien akan dipaksa melakukan _login_ ulang untuk mendapatkan izin yang baru.
*   **TR-AU-03 (Stateless Authorization)**: Otorisasi dilakukan secara _stateless_ menggunakan CASL pada setiap _request_. Kemampuan (_abilities_) pengguna di-evaluasi secara dinamis berdasarkan `role` dan atribut wilayah (`id_desa`, `id_kecamatan`) dari JWT payload.

## 3. Aturan API

*   **TR-API-01 (Versioning)**: Seluruh endpoint API menggunakan prefix versi `/v1` atau `/api/v1` untuk memastikan kompatibilitas klien terhadap perubahan API di masa mendatang.
*   **TR-API-02 (Validasi Input)**: Setiap _request_ ke API divalidasi menggunakan skema Joi di tingkat _route_ sebelum diproses oleh _handler_. Request yang tidak memenuhi skema akan ditolak dengan HTTP Status `400 Bad Request`.
*   **TR-API-03 (Format Response Error)**: Seluruh _error response_ menggunakan format standar `{ status: 'error', message: '...' }` dengan HTTP Status Code yang sesuai (400, 401, 403, 404, 500).
