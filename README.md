# Catatan Teknologi dan Arsitektur Proyek

Berikut adalah rincian teknologi dan framework yang digunakan dalam proyek ini, serta penjelasan mengenai bagaimana Frontend dan Backend saling terhubung.

## 1. Teknologi / Framework yang Digunakan

### A. Backend (`\backend`)
Folder ini berisi logika bisnis utama dan API server.
*   **Framework Utama:** **Laravel 12.0** (PHP Framework).
*   **Bahasa Pemrograman:** **PHP 8.2**.
*   **Database:** MySQL (Berdasarkan konfigurasi `.env`).
*   **Authentication:** `laravel/sanctum` (Untuk autentikasi berbasis Token/API).
*   **Package Penting Lainnya:**
    *   `laravolt/indonesia`: Kemungkinan digunakan untuk data wilayah (Provinsi, Kota, Kecamatan, dll).

### B. Frontend User (`\user`)
Folder ini berisi aplikasi antarmuka untuk pengguna umum (customer).
*   **Framework:** **React 19.1.0**.
*   **Build Tool:** **Vite 6.3.5** (Untuk build yang cepat dan performa tinggi).
*   **Bahasa Pemrograman:** JavaScript (JSX).
*   **Styling:** **Tailwind CSS 4.1.10**.
*   **Routing:** `react-router-dom` (v7.6.2).
*   **HTTP Client:** `axios` (Untuk melakukan request ke Backend).
*   **UI/Komponen Lain:** `framer-motion` (Animasi), `react-leaflet` (Peta), `lucide-react` (Ikon).

### C. Frontend Admin (`\user\admin`)
Folder ini berisi dashboard admin untuk pengelolaan sistem.
*   **Framework:** **Next.js 16.0.7** (React Framework untuk Production).
*   **Bahasa Pemrograman:** **TypeScript** (TSX).
*   **Styling:** **Tailwind CSS 4.0.0**.
*   **UI Library:** **Shadcn UI** (Dibangun di atas Radix UI).
*   **State Management/Form:** `react-hook-form`, `zod` (Validasi schema).
*   **HTTP Client:** `axios`.
*   **Visualisasi Data:** `recharts` (Untuk grafik/chart di dashboard).

---

## 2. Metode Penghubung Backend dan Frontend

Meskipun folder Frontend (`user` dan `user/admin`) dan Backend (`backend`) terpisah, mereka terhubung melalui protokol **HTTP RESTful API**.

### Cara Kerjanya:

1.  **API sebagai Jembatan:**
    *   Backend Laravel tidak merender HTML (View). Sebaliknya, ia bertindak sebagai penyedia data (API Provider).
    *   Backend mengekspos endpoint URL (contoh: `http://localhost:8000/api/user/login` atau `http://localhost:8000/api/cars`).
    *   Data dikirim dan diterima dalam format **JSON**.

2.  **HTTP Client (Axios):**
    *   Baik di aplikasi React (`user`) maupun Next.js (`user/admin`), library **Axios** digunakan untuk "memanggil" endpoint tersebut.
    *   Frontend mengirim *Request* (GET, POST, PUT, DELETE) ke URL Backend.
    *   Backend memproses request, lalu mengirim *Response* balik ke Frontend.

3.  **Konfigurasi Base URL:**
    *   Agar Frontend tahu ke mana harus mengirim request, alamat server Backend disimpan dalam *Environment Variable*.
    *   Di **React (`user`)**: Menggunakan `import.meta.env.VITE_BASE_URL`.
    *   Di **Next.js (`user/admin`)**: Menggunakan `process.env.NEXT_PUBLIC_API_URL`.

4.  **Autentikasi (Bearer Token):**
    *   Saat user login, Backend memberikan sebuah **Token** (via Laravel Sanctum).
    *   Token ini disimpan oleh Frontend (biasanya di `localStorage` atau `cookie`).
    *   Setiap kali Frontend meminta data pribadi (protected routes), Token ini dilampirkan dalam *Header* request (`Authorization: Bearer <token>`).
