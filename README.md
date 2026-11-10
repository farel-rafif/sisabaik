# Integrasi Chapter 10 — Full Stack SisaBaik

## 1. Ringkasan

Chapter 10 mengintegrasikan frontend Vue 3 dengan backend Express dan database PostgreSQL pada aplikasi SisaBaik.

Integrasi ini mencakup:

- Pengelolaan sesi pengguna dan autentikasi.
- Registrasi dan login pembeli maupun penyedia.
- Pengelolaan profil dan verifikasi penyedia.
- Katalog penawaran makanan.
- Pembuatan, pengubahan, dan pengelolaan penawaran.
- Pemesanan dan pengelolaan transaksi.
- Pembatalan, penolakan, pengambilan, dan pembayaran pesanan.
- Dashboard pembeli dan penyedia.
- Validasi data dan penanganan kesalahan API.

Frontend menggunakan Vue 3 dan Vite, sedangkan backend menggunakan Express dengan PostgreSQL sebagai database.

## 2. Teknologi yang Digunakan

| Teknologi | Fungsi |
|---|---|
| Node.js | Runtime JavaScript |
| npm | Pengelola dependensi |
| Vue 3 | Framework frontend |
| Vite | Development server dan build frontend |
| Vue Router | Pengelolaan navigasi halaman |
| Express | Backend API |
| PostgreSQL | Database aplikasi |
| pgAdmin | Pengelolaan dan pemeriksaan database |

Versi Node.js dan npm yang digunakan saat pengerjaan:

- Node.js: 24.19.0
- npm: 11.9.0

## 3. Struktur Proyek

Struktur utama proyek:

```text
sisabaik-api/
├── database/
├── frontend/
│   ├── src/
│   │   ├── assets/
│   │   ├── components/
│   │   ├── composables/
│   │   ├── lib/
│   │   ├── router/
│   │   ├── views/
│   │   ├── App.vue
│   │   ├── main.js
│   │   └── style.css
│   ├── package.json
│   ├── package-lock.json
│   └── vite.config.js
├── scripts/
├── src/
├── public/
├── .env
├── package.json
├── package-lock.json
└── README.md
```

Frontend dan backend memiliki dependensi masing-masing. Instalasi dependensi frontend dilakukan di dalam folder `frontend`, sedangkan instalasi dependensi backend dilakukan dari folder utama proyek.

## 4. Konfigurasi Lingkungan

Backend menggunakan file `.env` untuk menyimpan konfigurasi lingkungan lokal.

Variabel yang digunakan:

```env
PORT=3000
PGHOST=localhost
PGPORT=5432
PGDATABASE=sisabaik_dev
PGUSER=postgres
PGPASSWORD=
JWT_SECRET=

NODE_ENV=development
HOST=127.0.0.1
TRUST_PROXY_HOPS=0
PGSSL=disable
```

**Catatan:**

- `PGPASSWORD` diisi dengan kata sandi PostgreSQL lokal.
- `JWT_SECRET` diisi dengan secret untuk autentikasi.
- Nilai rahasia tidak boleh dimasukkan ke Git, README, atau dibagikan melalui tangkapan layar.
- File `.env` tetap disimpan secara lokal dan tidak dimasukkan ke commit.

## 5. Instalasi Dependensi

### Backend

Jalankan dari folder utama proyek:

```powershell
npm ci
```

### Frontend

Jalankan dari folder utama proyek:

```powershell
npm --prefix frontend ci
```

Perintah `npm ci` digunakan untuk memasang dependensi berdasarkan lockfile agar versi dependensi tetap konsisten.

## 6. Menjalankan Aplikasi dalam Mode Development

### Menjalankan backend

Dari folder utama proyek:

```powershell
npm run dev
```

Backend berjalan pada:

```text
http://127.0.0.1:3000/
```

### Menjalankan frontend

Buka terminal baru:

```powershell
cd frontend
npm run dev
```

Frontend berjalan pada:

```text
http://127.0.0.1:5173/
```

### Konfigurasi proxy

Frontend menggunakan konfigurasi proxy Vite untuk meneruskan permintaan `/api` ke backend:

```text
Frontend: http://127.0.0.1:5173/
Backend:  http://127.0.0.1:3000/
API:      /api
```

Penggunaan host `127.0.0.1` secara konsisten membantu menghindari perbedaan host ketika menguji cookie, sesi, dan koneksi aplikasi.

## 7. Pengelolaan Sesi dan API

### Pengelolaan sesi

File:

```text
frontend/src/lib/sesi.js
```

File ini mengelola:

- Data pengguna yang sedang login.
- Access token.
- Pesan status sesi.
- Status logout.
- Versi sesi untuk mencegah respons dari sesi lama mengubah sesi baru.

Sesi disimpan dalam memori aplikasi. Karena itu, pemuatan ulang halaman dapat menghilangkan sesi login dan pengguna perlu login kembali.

### API helper

File:

```text
frontend/src/lib/api.js
```

API helper bertanggung jawab untuk:

- Mengirim permintaan HTTP ke backend.
- Menambahkan header autentikasi jika diperlukan.
- Mengirim data JSON.
- Memeriksa format respons.
- Menangani HTTP error.
- Menangani kegagalan jaringan.
- Menghapus sesi ketika menerima respons autentikasi yang tidak berlaku.

Kesalahan API dikelola menggunakan `ApiError` dan `pesanError()`.

### Composable pembacaan data

File:

```text
frontend/src/composables/useBaca.js
```

Composable ini mengelola proses pembacaan data, termasuk:

- Status loading.
- Data hasil permintaan.
- Pesan kesalahan.
- Pembatalan permintaan sebelumnya.
- Pencegahan respons lama menggantikan data terbaru.

## 8. Navigasi dan Halaman

Navigasi aplikasi menggunakan Vue Router dengan hash history.

File utama:

```text
frontend/src/router/index.js
```

Halaman yang tersedia:

| Halaman | Fungsi |
|---|---|
| Katalog | Menampilkan penawaran yang tersedia |
| Login | Autentikasi pengguna |
| Daftar | Registrasi akun baru |
| Profil Penyedia | Pengelolaan profil usaha |
| Penawaran Saya | Pengelolaan penawaran milik penyedia |
| Pesanan | Daftar pesanan pengguna |
| Detail Pesanan | Informasi dan tindakan pada pesanan |
| Dashboard | Ringkasan aktivitas pengguna |
| Akses Ditolak | Informasi ketika peran tidak sesuai |
| Halaman Tidak Ditemukan | Penanganan alamat yang tidak tersedia |

Router menerapkan pemeriksaan autentikasi, peran pengguna, dan status verifikasi penyedia.

## 9. Registrasi dan Autentikasi

Registrasi tersedia untuk dua jenis pengguna:

- Pembeli.
- Penyedia.

Validasi registrasi dilakukan pada frontend dan tetap harus diperiksa oleh backend.

Ketentuan validasi yang diterapkan:

- Nama lengkap: 2–100 karakter.
- Email: format email yang valid.
- Kata sandi: minimal 15 karakter dan maksimal 72 byte.
- Konfirmasi kata sandi harus sama.
- Penyedia harus mengisi informasi usaha, alamat, dan nomor telepon.

Pemberian peran dan status verifikasi tetap menjadi tanggung jawab server.

Akun penyedia yang baru didaftarkan harus melalui proses peninjauan sebelum dapat mengelola penawaran.

## 10. Pengelolaan Penawaran

Penyedia yang telah terverifikasi dapat mengelola penawaran melalui halaman Penawaran Saya.

Fitur yang tersedia:

- Membuat penawaran.
- Mengubah penawaran.
- Mengatur harga normal.
- Mengatur harga penawaran.
- Mengatur stok.
- Mengatur satuan.
- Mengatur masa berlaku.
- Mengunggah foto penawaran.

Pengubahan penawaran menggunakan informasi versi untuk mencegah perubahan data yang sudah kedaluwarsa.

Jika terjadi konflik versi, server dapat mengembalikan HTTP `409`. Pengguna perlu memuat data terbaru dan memeriksa kembali perubahan sebelum menyimpan ulang.

Stok dikelola oleh backend. Frontend tidak boleh mengurangi stok secara manual setelah transaksi.

## 11. Katalog dan Pemesanan

Katalog menampilkan penawaran yang tersedia untuk pembeli.

Fitur katalog meliputi:

- Pencarian penawaran.
- Filter kategori.
- Paginasi.
- Pengambilan data melalui API.

Pembeli dapat membuat pesanan dengan mengirimkan identitas penawaran dan kuantitas.

Contoh struktur data pemesanan:

```json
{
  "items": [
    {
      "penawaranId": 1,
      "kuantitas": 2
    }
  ]
}
```

Harga dan total transaksi dihitung serta divalidasi oleh backend. Frontend tidak mengirimkan harga buatan sendiri.

Backend bertanggung jawab terhadap:

- Pemeriksaan ketersediaan stok.
- Validasi kuantitas.
- Penyimpanan detail transaksi.
- Snapshot harga saat pesanan dibuat.
- Pengurangan stok.
- Pengembalian stok ketika pembatalan atau penolakan yang memenuhi ketentuan.

Konflik stok dapat menghasilkan respons `409`.

## 12. Alur Pesanan

Alur transaksi mencakup beberapa tahap:

1. Pembeli membuat pesanan.
2. Penyedia menerima atau menolak pesanan.
3. Penyedia menandai pesanan siap diambil.
4. Pengambilan dan pembayaran dicatat sesuai jenis transaksi.
5. Pesanan diselesaikan.

Pembeli dapat membatalkan pesanan sebelum pesanan dikonfirmasi sesuai aturan sistem.

Penyedia dapat menolak pesanan sesuai kondisi dan aturan transaksi.

Pengembalian stok ditangani oleh backend agar tidak terjadi pengembalian stok berulang.

### Pesanan berbayar

Pesanan berbayar harus memiliki konfirmasi penyerahan makanan dan penerimaan pembayaran sebelum diselesaikan.

### Pesanan gratis

Pesanan dengan harga penawaran Rp0 tidak menghasilkan pendapatan atau pengeluaran uang. Namun, unit produk yang didistribusikan tetap dapat dihitung dalam metrik dashboard.

## 13. Dashboard

Dashboard menyediakan informasi berdasarkan peran pengguna.

### Dashboard pembeli

Menampilkan ringkasan seperti:

- Pengeluaran transaksi yang telah selesai dan tercatat pembayarannya.
- Penghematan berdasarkan harga normal dan harga penawaran.
- Jumlah unit produk yang diterima.

### Dashboard penyedia

Menampilkan ringkasan seperti:

- Pendapatan transaksi berbayar yang telah selesai.
- Nilai normal produk yang didistribusikan.
- Diskon atau nilai penghematan.
- Jumlah unit produk yang didistribusikan.

Pesanan yang masih menunggu konfirmasi atau siap diambil tidak dihitung sebagai transaksi selesai.

Pesanan gratis tidak menambah pendapatan atau pengeluaran, tetapi dapat memengaruhi jumlah unit dan nilai normal produk yang didistribusikan sesuai aturan perhitungan dashboard.

Dashboard mengambil data dari backend, bukan menghitung ulang transaksi berdasarkan data lokal frontend.

## 14. Database dan Migrasi

Database menggunakan PostgreSQL.

Migrasi integrasi dilakukan menggunakan:

```powershell
npm run db:migrate
```

Migrasi tersebut menyiapkan kebutuhan autentikasi dan alur transaksi dengan mempertahankan data serta kata sandi lama.

Tabel utama yang diperiksa:

- `detail_pesanan`
- `pengambilan`
- `pengguna`
- `penawaran`
- `penyedia`
- `pesanan`

Kolom tambahan yang diperiksa meliputi:

- `penawaran.versi`
- `penawaran.foto`
- `pesanan.status_pembayaran`
- `detail_pesanan.nama_penawaran`

Pemeriksaan database dilakukan menggunakan pgAdmin dan query SELECT.

Perubahan status pesanan dilakukan melalui aplikasi, bukan dengan mengubah data transaksi secara langsung melalui SQL.

## 15. Pengujian Integrasi

Pengujian dilakukan pada lingkungan lokal menggunakan backend, frontend, dan database PostgreSQL.

### Hasil pemeriksaan yang telah dilakukan

| Pengujian | Hasil |
|---|---|
| Backend berjalan | Berhasil |
| Koneksi database | Berhasil |
| Endpoint `/api/health` | Berhasil |
| Respons API tanpa autentikasi | Menghasilkan `AUTH_REQUIRED` |
| Proxy Vite ke backend | Berhasil |
| Frontend dapat dibuka | Berhasil |
| Akses halaman pesanan sebagai guest | Dialihkan ke login |
| Registrasi dan login pembeli | Berhasil |
| Registrasi dan login penyedia | Berhasil |
| Verifikasi akun penyedia | Berhasil |
| Pembuatan tiga penawaran | Berhasil |
| Pengubahan harga penawaran | Berhasil |
| Pencarian katalog | Berhasil |
| Filter kategori dan paginasi | Berhasil |
| Pembuatan pesanan berbayar | Berhasil |
| Pembatalan pesanan pembeli | Berhasil |
| Penerimaan pesanan penyedia | Berhasil |
| Penandaan pesanan siap diambil | Berhasil |
| Validasi pengambilan dan pembayaran | Berhasil |
| Penyelesaian pesanan berbayar | Berhasil |
| Pemeriksaan dashboard pembeli | Sesuai hasil yang diharapkan |
| Pemeriksaan dashboard penyedia | Sesuai hasil yang diharapkan |
| Penolakan pesanan gratis | Berhasil |
| Penyelesaian pesanan gratis | Berhasil |
| Pemeriksaan dashboard untuk pesanan gratis | Sesuai hasil yang diharapkan |
| Build frontend | Berhasil |
| Preview frontend | Berhasil |
| Build aplikasi melalui root project | Berhasil |
| Menjalankan aplikasi melalui `start:local` | Berhasil |

### Contoh perhitungan transaksi

Pengujian menggunakan penawaran Paket Roti Integrasi:

- Harga normal: Rp20.000.
- Harga penawaran setelah perubahan: Rp7.000.
- Kuantitas: 2 paket.

Hasil perhitungan:

| Komponen | Nilai |
|---|---:|
| Total harga normal | Rp40.000 |
| Total transaksi | Rp14.000 |
| Penghematan | Rp26.000 |
| Stok awal | 5 |
| Stok setelah pemesanan | 3 |

Perhitungan tersebut menggunakan harga dan kuantitas yang tersimpan pada transaksi.

## 16. Build dan Preview

### Build frontend

Dari folder frontend:

```powershell
npm run build
```

Perintah ini menghasilkan folder:

```text
frontend/dist/
```

### Preview frontend

Masih dari folder frontend:

```powershell
npm run preview
```

Preview tersedia melalui:

```text
http://127.0.0.1:4173/
```

### Build dari root project

Dari folder utama:

```powershell
npm run build
```

Perintah tersebut menjalankan proses build frontend.

### Menjalankan aplikasi lokal melalui Express

Dari folder utama:

```powershell
npm run start:local
```

Aplikasi dapat diakses melalui:

```text
http://127.0.0.1:3000/
```

Pada mode ini, Express menyajikan frontend hasil build sekaligus menyediakan backend API.

Jangan menjalankan backend development dan `start:local` secara bersamaan jika keduanya menggunakan port yang sama.

## 17. Keterbatasan Aplikasi

Beberapa keterbatasan implementasi yang perlu diketahui:

1. Sesi frontend disimpan dalam memori sehingga login perlu dilakukan kembali setelah halaman dimuat ulang.
2. Belum tersedia integrasi payment gateway.
3. Pembayaran dicatat melalui konfirmasi pada aplikasi.
4. Belum tersedia fitur verifikasi email.
5. Belum tersedia fitur reset kata sandi.
6. Belum tersedia pembaruan data secara real-time.
7. Dashboard mengikuti data transaksi yang telah disimpan dan aturan perhitungan backend.
8. Pengujian pada lingkungan lokal tidak menggantikan pengujian keamanan dan beban pada lingkungan produksi.

## 18. Keamanan dan Pengelolaan Data

Beberapa prinsip yang diterapkan:

- Token autentikasi tidak ditampilkan pada dokumentasi.
- Kata sandi tidak disimpan dalam README.
- Validasi transaksi dilakukan oleh backend.
- Peran pengguna diperiksa sebelum mengakses halaman tertentu.
- Penyedia harus terverifikasi untuk mengelola penawaran.
- Data pesanan dibatasi berdasarkan hak akses pengguna.
- Konflik versi dan stok ditangani oleh server.
- File `.env`, `node_modules`, dan hasil build tidak dimasukkan ke commit.

## 19. Kesimpulan

Integrasi Chapter 10 menghubungkan frontend Vue 3, backend Express, dan database PostgreSQL menjadi aplikasi SisaBaik yang memiliki alur autentikasi, pengelolaan penawaran, pemesanan, pengambilan, pembayaran, serta dashboard berdasarkan peran pengguna.

Build frontend dan build aplikasi melalui root project berhasil dijalankan. Aplikasi juga berhasil diuji melalui development server, preview, dan server lokal Express.

Dengan demikian, integrasi full stack Chapter 10 telah mencapai tahap implementasi dan pengujian alur utama sesuai modul.