# SisaBaik API

SisaBaik adalah platform yang menghubungkan penyedia makanan dengan konsumen guna menekan tingkat limbah makanan melalui penjualan surplus produk berkualitas.

## Struktur Proyek dan Migrasi Database

Proyek ini berbasis PostgreSQL. Urutan eksekusi skrip migrasi dan data awal:

* `database/01_schema.sql` - Skema basis data relasional utama.
* `database/02_seed.sql` - Data awal untuk pengujian.
* `database/03_queries.sql` hingga `06_...` - Kumpulan query pendukung CRUD dan transaksi.
* `database/07_auth.sql` - Migrasi sistem autentikasi, manajemen token, dan pencatatan sesi.

## Fitur Pendaftaran Mandiri dan Peran Pengguna

Sistem menyediakan pendaftaran mandiri untuk dua peran utama:

1. **Konsumen**: Pengguna umum yang dapat melihat katalog publik, membuat pesanan, dan bertransaksi.
2. **Penyedia**: Pengusaha makanan yang mendaftarkan profil usaha untuk menawarkan surplus makanan.
   * **Peninjauan Penyedia**: Akun penyedia baru memerlukan proses peninjauan atau verifikasi sebelum diizinkan mempublikasikan penawaran di katalog publik.

## Keamanan, Pengujian Autentikasi, dan Logout

* **Pengujian Auth (`auth:test`)**: Jalankan perintah berikut untuk menguji integritas autentikasi dan alur token:
  ```bash
  npm run auth:test
  ```
* **Efek Logout Semua Sesi**: Saat pengguna melakukan logout dari semua sesi, sistem menghapus seluruh token aktif dan sesi terkait berdasarkan migrasi `07_auth.sql`. Token JWT lama langsung tidak valid di seluruh perangkat.

## Konfigurasi Environment

Gunakan daftar nama variabel lingkungan berikut pada file `.env` (tanpa menyertakan nilai rahasia):

* `PORT`
* `DATABASE_URL`
* `JWT_SECRET`
* `NODE_ENV`

## Cara Menjalankan Server dan Halaman

1. Instal dependensi:
   ```bash
   npm install
   ```
2. Jalankan server dalam mode pengembangan:
   ```bash
   npm run dev
   ```
3. Akses antarmuka statis melalui browser pada file `public/index.html` atau alur autentikasi di `public/auth.html`.