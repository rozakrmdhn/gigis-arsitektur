# Architecture Decision Records (ADR)

Dokumen ini mencatat keputusan-keputusan arsitektur (_Architecture Decisions_) krusial yang dibuat selama pengembangan sistem GIGIS / MELAROSA beserta konteks dan alasannya.

## ADR-01: Pendekatan Unified Spatial Architecture (Satu Tabel Spasial untuk Berbagai Infrastruktur)

**Status:** Diterima (Accepted)

**Konteks:**
Pada iterasi awal, setiap jenis infrastruktur spasial (misal: Jalan Kabupaten, Jembatan, Jalan Lingkungan) dibuatkan tabel spasial mandiri yang menyimpan geometri (titik/garis/poligon) secara terpisah. Hal ini menyebabkan kesulitan yang besar ketika sistem harus merender *basemap* terpadu dari seluruh jenis infrastruktur sekaligus, serta membuat sistem tidak *scalable* ketika menambah kategori infrastruktur baru (harus membuat _route_, tabel, model, dan API baru berulang kali).

**Keputusan:**
Sistem beralih menggunakan _Unified Architecture_. Tabel geometri spasial disatukan menjadi `infrastruktur_segmen` (Garis/Titik) dan `infrastruktur_area` (Poligon). Atribut statis master tetap berada di tabel master-nya (misal: `jalan_porosdesa`), sedangkan data geometrinya diletakkan di tabel spasial terpadu.

**Konsekuensi:**
*   **Positif:**
    *   API menjadi sangat modular (menggunakan parameter `/v1/infrastruktur/{tipe}`).
    *   Sangat mudah menambahkan kategori infrastruktur baru ke depannya tanpa merubah struktur tabel database secara masif.
    *   Proses rendering GeoJSON oleh frontend lebih cepat karena dikumpulkan dari satu/dua sumber tabel spasial saja.
*   **Negatif:**
    *   Kompleksitas kueri data meningkat ketika membutuhkan _join_ dari atribut master spesifik dengan data koordinatnya.
    *   Penggunaan kolom tipe `JSONB` diperlukan untuk menampung atribut tak terstruktur yang berbeda dari tiap tipe.

---

## ADR-02: Penyimpanan Atribut Dinamis menggunakan JSONB

**Status:** Diterima (Accepted)

**Konteks:**
Menyambung dengan ADR-01, karena seluruh titik/garis/poligon spasial disimpan pada satu tabel (`infrastruktur_segmen` / `infrastruktur_area`), terdapat dilema dalam cara menyimpan kolom/atribut pendukung (seperti warna cat untuk jembatan, tipe perkerasan untuk jalan, dsb). Jika dibuat kolom _fixed_ di tabel tersebut, akan banyak kolom bernilai `NULL` (karena tidak relevan).

**Keputusan:**
Menyediakan kolom dengan tipe data `JSONB` (dinamai `atribut`) di dalam tabel spasial utama untuk menyimpan spesifikasi atau metadata yang spesifik (*domain-specific*) dari masing-masing tipe infrastruktur.

**Konsekuensi:**
*   **Positif:** Struktur database jauh lebih fleksibel dan terhindar dari *Sparse Tables* (tabel kopong).
*   **Negatif:** Kehilangan struktur relasional kaku (tipe statik) untuk field-field di dalam kolom JSON. Akan cukup rumit apabila di masa depan dituntut membuat sistem _reporting/filtering_ yang rumit berdasar kriteria atribut internal di dalam JSONB tersebut (meskipun PostgreSQL mendukung indeks JSONB).

---

## ADR-03: Implementasi RBAC dan ABAC menggunakan @casl/ability

**Status:** Diterima (Accepted)

**Konteks:**
Sistem GIGIS membutuhkan tidak hanya _Role-Based Access Control_ (contoh: "Apakah dia Operator Desa?"), tetapi juga membutuhkan kontrol tingkat instansi _Attribute-Based_ (contoh: "Operator Desa hanya boleh mengedit infrastruktur yang `id_desa`-nya cocok dengan profilnya").

**Keputusan:**
Mengadopsi _library_ isomorfik `@casl/ability`. Definisi _rule_ kemampuan (`abilities`) di-kueri secara dinamis dari tabel `permissions` dan disesuaikan nilainya dengan atribut _user_ saat ini pada proses injeksi *middleware*.

**Konsekuensi:**
*   **Positif:** Logika _authorization_ sangat rapi dan dapat digunakan ulang lintas *route* maupun di UI Frontend.
*   **Negatif:** Mengharuskan pengikatan data `id_desa` dan `id_kecamatan` pada setiap payload yang di-_submit_, serta membutuhkan pembacaan ulang *abilities* pada tiap panggilan API yang bisa menimbulkan pinalti kinerja kecil jika tidak *di-cache* dengan benar.
