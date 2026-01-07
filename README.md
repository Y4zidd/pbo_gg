Berikut script penjelasan OOP saja, dengan lokasi file yang jelas. Kamu bisa baca ini sambil rekam.

---

## 1. Enkapsulasi (Encapsulation)

“Pertama, saya terapkan enkapsulasi di layer model dan DAO.

Di paket `rentalmobil.model`, misalnya di file `rentalmobil/model/User.java`, semua atribut seperti `idUser`, `username`, `password`, `namaLengkap`, `level`, dan `photoPath` bertipe `private`. Akses ke field-field ini tidak langsung, tapi melalui method getter dan setter, seperti `getIdUser()`, `setPassword()`, `getNamaLengkap()`, dan seterusnya.

Pola yang sama juga ada di `rentalmobil/model/Konsumen.java` untuk field `noKTP`, `namaKonsumen`, `tempatTinggal`, dan `nomorTelepon`, dan juga di `rentalmobil/model/Mobil.java`, `Merek.java`, serta `Transaksi.java`. Dengan cara ini data di dalam objek terlindungi dan hanya bisa diubah lewat method yang sudah saya sediakan.

Di paket `rentalmobil.dao` seperti `rentalmobil/dao/KonsumenDao.java` dan `rentalmobil/dao/MobilDao.java`, saya juga menerapkan enkapsulasi terhadap akses database. Detail koneksi dan SQL ada di dalam method seperti `find(...)`, `getAll()`, dan `upsert(...)`, sehingga bagian lain aplikasi tidak berinteraksi langsung dengan `Connection` atau `PreparedStatement`, tapi hanya memanggil method DAO-nya.”

---

## 2. Inheritance dan `super()` (Pewarisan)

“Untuk konsep inheritance atau pewarisan, saya gunakan di hierarki user.

Class dasar atau superclass‑nya adalah `User` yang ada di file `rentalmobil/model/User.java`. Di class ini saya simpan atribut umum semua pengguna, seperti `idUser`, `username`, `password`, `namaLengkap`, dan `level`.

Kemudian saya buat dua subclass: `Admin` dan `Petugas` di paket yang sama.  
`Admin` ada di `rentalmobil/model/Admin.java` dan dideklarasikan sebagai:

```java
public class Admin extends User { ... }
```

`Petugas` ada di `rentalmobil/model/Petugas.java` dengan deklarasi:

```java
public class Petugas extends User { ... }
```

Di kedua subclass ini, constructor memanggil constructor milik `User` menggunakan keyword `super`. Contohnya di `Admin`:

```java
public Admin(int idUser, String username, String password, String namaLengkap) {
    super(idUser, username, password, namaLengkap, "ADMIN");
}
```

dan di `Petugas`:

```java
public Petugas(int idUser, String username, String password, String namaLengkap) {
    super(idUser, username, password, namaLengkap, "PETUGAS");
}
```

Jadi di sini jelas terlihat pemakaian inheritance (`extends User`) dan pemanggilan `super(...)` di constructor untuk menginisialisasi bagian yang diwarisi dari `User`.”

---

## 3. Overloading (Polimorfisme Statis)

“Untuk overloading, saya gunakan terutama di constructor.

Contoh pertama ada di `rentalmobil/model/User.java`. Di class `User` saya punya beberapa constructor dengan parameter berbeda: constructor kosong, constructor dengan parameter `(idUser, username, password, namaLengkap, level)`, dan satu constructor lagi yang menambahkan `photoPath`. Nama constructornya sama, tapi daftar parameternya berbeda, ini yang disebut constructor overloading.

Contoh kedua ada di `rentalmobil/model/Konsumen.java`. Konsumen punya constructor kosong `Konsumen()`, dan constructor lengkap `Konsumen(String noKTP, String namaKonsumen, String tempatTinggal, String nomorTelepon)`. Dengan adanya beberapa bentuk constructor, saya bisa membuat objek dengan data minimal maupun dengan data lengkap sesuai kebutuhan.”

*(kalau kamu ingin, sebut juga constructor tambahan yang kamu lihat di file Konsumen sekarang)*

---

## 4. Overriding & Polymorphism Dinamis

“Untuk overriding, saya implementasikan di beberapa class model.

Di `rentalmobil/model/Merek.java` dan `rentalmobil/model/Mobil.java`, saya override method `toString()` yang diwarisi dari `Object`. Di `Merek`, `toString()` saya ubah supaya mengembalikan `namaMerek`, dan di `Mobil`, `toString()` saya ubah supaya menampilkan kombinasi merek, nama mobil, dan plat nomor. Ini membuat objek bisa langsung ditampilkan dengan teks yang lebih informatif di combobox atau tabel.

Di hierarki user, saya juga override method `getRoleDescription()`.  
Method dasarnya ada di `User` (`rentalmobil/model/User.java`) yang mengembalikan `"User"`.  
Kemudian di `Admin` (`rentalmobil/model/Admin.java`) saya override menjadi `"Admin"`, dan di `Petugas` (`rentalmobil/model/Petugas.java`) saya override menjadi `"Petugas"`. Ini contoh overriding di dalam inheritance.

Polymorphism dinamis muncul di service login. Di file `rentalmobil/service/AuthService.java`, method `login(String username, String password)` selalu mengembalikan tipe `User`, tetapi di dalamnya, kalau level di database adalah `ADMIN` saya buat objek `Admin`, dan kalau levelnya `PETUGAS` saya buat objek `Petugas`. Jadi dari luar, variabelnya bertipe `User`, tapi objek konkretnya bisa Admin atau Petugas, dan method yang di‑override seperti `getRoleDescription()` akan mengikuti tipe objek sebenarnya. Itu yang disebut polymorphism.”

---

## 5. Abstraction & Layering (Model – DAO – Service)

“Untuk abstraksi, saya memisahkan beberapa layer.

Layer model di paket `rentalmobil.model` hanya berisi representasi data dan sedikit logic lokal. Layer DAO di paket `rentalmobil.dao` seperti `KonsumenDao`, `MobilDao`, `UserDao`, dan `TransaksiDao` menyembunyikan detail akses database di dalam method‑method publik, sehingga bagian lain hanya tahu fungsi seperti `getAll()`, `find()`, `insert()`, atau `getMonthlyDetails(...)`.

Di atasnya ada layer service, misalnya `rentalmobil/service/RentalService.java`, yang menyatukan beberapa operasi DAO menjadi satu proses bisnis, seperti `sewaMobil(...)` dan `prosesPengembalian(...)`. Cara ini menunjukkan penerapan konsep abstraksi dan pemisahan tanggung jawab antar class.”

---

## 6. Association / Composition (HAS‑A)

“Terakhir, untuk hubungan antar objek yang bukan pewarisan, saya gunakan association atau composition.

Misalnya, di `rentalmobil/model/Transaksi.java`, class `Transaksi` punya field `idMobil`, `noKTP`, dan `idUserPetugas`. Artinya satu transaksi berhubungan dengan satu mobil, satu konsumen, dan satu petugas, tapi `Transaksi` bukan turunan dari `Mobil` atau `Konsumen`. Jadi di sini relasinya adalah **has‑a**, bukan `extends`.

Begitu juga di `rentalmobil/service/RentalService.java`, service ini memiliki field `MobilDao`, `KonsumenDao`, dan `TransaksiDao`. Service tersebut menggunakan objek‑objek DAO itu untuk menjalankan proses bisnis. Ini contoh komposisi di level service.”

---

Kalau kamu baca script ini pelan‑pelan, kamu sudah menjawab:

- OOP apa saja yang dipakai (enkapsulasi, inheritance + super, overloading, overriding, polymorphism, abstraksi, dan association).
- Letak kodenya ada di file mana saja di dalam proyek.
