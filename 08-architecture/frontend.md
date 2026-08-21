# Frontend Architecture

Frontend GIGIS / MELAROSA adalah aplikasi modern berbasis React yang dirancang untuk performa tinggi, khususnya dalam me-render peta interaktif.

## Tech Stack Utama

*   **Framework**: React Router v7 (SSR/SPA) + Vite.
*   **Styling**: Tailwind CSS v4 untuk styling yang cepat dan _utility-first_.
*   **Komponen UI**: Menggunakan Radix UI (sebagai basis komponen *headless* seperti dialog, dropdown, tabs) dipadukan dengan desain kustom ala Shadcn UI.
*   **Mapping/GIS**: `react-leaflet` dan `ol` (OpenLayers) untuk visualisasi geospasial di _browser_.
*   **Spatial Utils**: `@turf/turf` digunakan di _client-side_ untuk kalkulasi area, jarak, atau memproses geometri sebelum dikirim ke backend.
*   **State & Form**: `react-hook-form` dipadukan dengan `zod` untuk validasi form dan state.

## Struktur dan Pola Desain (Design Patterns)

1. **Route-Based Code Splitting**
    Menggunakan kemampuan React Router v7, kode dipecah berdasarkan *routes* halaman. Data *fetching* (mengambil data dari API) dilakukan secara paralel dengan merender halaman (menggunakan fitur *loader* pada rute).

2. **Komponen Modular (Atomic Design)**
    Komponen-komponen _User Interface_ dipecah menjadi unit-unit terkecil (seperti Button, Input, MapComponent) di dalam folder komponen terpusat, memastikan penggunaan kembali (*reusability*) yang tinggi.

3. **Client-Side Spatial Processing**
    Beban komputasi pemetaan (*drawing*, mengkalkulasi luasan digitasi sementara) dilakukan secara _client-side_ menggunakan komponen kanvas dari Leaflet dan pustaka perhitungan geometri Turf.js. Hal ini mengurangi beban API.
