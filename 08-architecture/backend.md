# Backend Architecture

Backend GIGIS / MELAROSA dirancang menggunakan arsitektur monolitik modular (_Modular Monolith_), berfokus pada kecepatan (_fast routing_) dan keamanan yang ketat.

## Tech Stack Utama

*   **Runtime**: Node.js
*   **Framework**: Hapi.js (`@hapi/hapi`)
*   **ORM**: Sequelize
*   **Validasi**: Joi (`joi`)
*   **Otorisasi**: CASL (`@casl/ability`)
*   **Authentication**: JWT (`@hapi/jwt`, `hapi-auth-jwt2`)

## Struktur Modul

Aplikasi Backend dibagi berdasarkan area fitur fungsional (Domain/Modul), bukan hanya berdasarkan teknis MVC.

```text
src/
├── config/         # Konfigurasi Database, Env, Abilities
├── middleware/     # Middleware otorisasi (checkAbility)
├── models/         # Definisi model Sequelize dan asosiasi
├── modules/        # Area Domain (Routing, Handler, Validation, Service)
│   ├── auth/
│   ├── infrastruktur-aset/
│   ├── monitoring-laporan/
│   ├── usulan-desa/
│   └── ...
├── plugins/        # Plugin Hapi (Swagger, Auth dll)
├── routes/         # Central Router Registry (api.js)
├── services/       # Layanan berbagi lintas-modul
└── utils/          # Fungsi bantuan (logger, formatters)
```

## Pola Desain Backend

1. **Route-Handler-Service Pattern**:
    *   **Route**: Menerima request HTTP, mendefinisikan validasi payload (dengan Joi), dan memeriksa _pre-requisites_ (seperti `checkAbility`).
    *   **Handler**: Meneruskan input ke layanan bisnis dan mengatur format response HTTP (menggunakan Boom untuk error).
    *   **Service**: Berisi murni logika bisnis dan interaksi dengan Database Model (Sequelize).

2. **Attribute-Based Access Control (ABAC)**
    Backend menggunakan CASL untuk mendefinisikan apa yang boleh dilakukan (action) oleh user terhadap sumber daya (subject). Aturan tersebut di-evaluasi pada level `middleware/authorize.js` di setiap rute sebelum _request_ diproses lebih lanjut.
