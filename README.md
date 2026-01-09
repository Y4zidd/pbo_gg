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

Table master

- users
- cars
- locations
- bookings
- booking_options
- payments
- rental_partners
- car_images
- coupons
- user_verification_codes

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

---

## 3. Konsep & Arsitektur (Jawaban untuk Dosen)

Bagian ini penting jika ditanya mengenai struktur kode dan pola desain yang digunakan.

### A. Backend: MVC (Model-View-Controller)
Meskipun digunakan sebagai API (tanpa View HTML), Laravel tetap menerapkan pola MVC:
*   **Model:** Representasi tabel database (file di `app/Models/`). Contoh: `Car.php`, `Booking.php`. Menggunakan **Eloquent ORM** untuk interaksi database yang mudah.
*   **Controller:** Mengatur logika alur data (file di `app/Http/Controllers/`). Menerima input dari request, memprosesnya (lewat Model), dan mengembalikan response JSON.
*   **Route:** Mendefinisikan endpoint URL (file `routes/api.php`).

### B. Frontend: Component-Based Architecture
*   **React & Next.js** memecah UI menjadi bagian-bagian kecil yang disebut **Component** (contoh: `Navbar`, `CarCard`, `Button`).
*   **Keuntungan:** Reusable (bisa dipakai ulang), mudah di-maintenance, dan kode lebih rapi.
*   **State Management:** Menggunakan React Hooks (`useState`, `useEffect`) untuk mengelola data lokal di komponen.

---

## 4. Keamanan (Security)

Jika ditanya "Bagaimana keamanan sistem ini?", jawab poin-poin berikut:

1.  **Authentication (Sanctum):** Menggunakan Token-based auth. Setiap request ke endpoint sensitif harus membawa token yang valid. Token ini mencegah akses tanpa izin.
2.  **Middleware:**
    *   Backend memiliki `AdminMiddleware` (`app/Http/Middleware/AdminMiddleware.php`) yang mengecek apakah user memiliki role `admin` atau `staff` sebelum mengizinkan akses ke halaman admin.
    *   Jika role tidak sesuai, server langsung menolak dengan status `403 Forbidden`.
3.  **Validasi Input:**
    *   Backend menggunakan Laravel Validation (misal `$request->validate()`) untuk memastikan data yang masuk sesuai format (email harus email, password minimal sekian karakter).
    *   Frontend juga melakukan validasi form (menggunakan `zod` di Admin) sebelum data dikirim ke server.
4.  **Password Hashing:** Password pengguna tidak disimpan mentah, melainkan di-hash (dienkripsi satu arah) menggunakan standar keamanan Laravel (Bcrypt/Argon2).

---

## 5. Struktur Database (Gambaran Umum)

Berdasarkan file migrasi, berikut entitas utama dalam sistem:

*   **Users:** Menyimpan data pengguna, role (admin/user/staff), password, dll.
*   **Cars:** Data mobil yang disewakan (nama, plat nomor, harga, status).
*   **Bookings:** Transaksi penyewaan (siapa yang sewa, mobil apa, tanggal mulai-selesai).
*   **Locations:** Data lokasi pengambilan/pengembalian mobil.
*   **Payments:** Status pembayaran untuk booking.
*   **Coupons:** Diskon yang bisa digunakan user.
*   **RentalPartners:** Mitra penyedia mobil (jika sistem mendukung multi-vendor).

---

## 6. Pertanyaan & Jawaban Cepat (Antisipasi Sidang/Demo)

**Q: Kenapa Frontend dan Backend dipisah?**
A: Supaya ke depannya pengembangan lebih enak; Backend fokus jadi penyedia data (API) sehingga nanti bisa dipakai tidak hanya oleh web, tapi layanan lain tanpa perlu ubah-ubah kode yang ada.

**Q: Apa itu ORM? Kenapa pakai Eloquent?**
A: ORM (Object-Relational Mapping) memungkinkan kita berinteraksi dengan database menggunakan kode PHP (objek) daripada menulis query SQL manual. Eloquent memudahkan relasi antar tabel (misal: mengambil data User beserta Booking-nya dengan mudah).

**Q: Bagaimana cara handle role Admin dan User biasa?**
A: Di database tabel `users` ada kolom `role`. Saat login, sistem mengecek role ini. Di backend ada `Middleware` yang memblokir akses ke route admin jika role user bukan admin.

**Q: Kenapa pakai Next.js untuk Admin tapi React Vite untuk User?**
A: (Ini asumsi strategi) Next.js memiliki fitur Server-Side Rendering (SSR) dan routing yang kuat yang bagus untuk dashboard yang kompleks dan butuh performa SEO/Loading awal yang baik. React Vite sangat cepat untuk pengembangan Single Page Application (SPA) yang interaktif seperti sisi user.
