```markdown
# SisaBaik API (Chapter 7)

Aplikasi back-end REST API untuk platform SisaBaik yang mengintegrasikan Express.js dan basis data relasional PostgreSQL. Proyek ini dikembangkan sebagai bagian dari mata kuliah Pengembangan Web (Program Studi Bisnis Digital, Universitas Negeri Padang)[cite: 1, 2].

## Prasyarat
- Node.js versi 22 atau lebih baru[cite: 2]
- PostgreSQL lokal (dengan database `sisabaik_dev` dan skema dari Chapter 6)[cite: 2, 4]
- npm[cite: 2]

## Cara Menjalankan Proyek
1. Buat dan sesuaikan berkas `.env` di root proyek berdasarkan `.env.example`[cite: 10, 13]:
   ```env
   PORT=3000
   PGHOST=localhost
   PGPORT=5432
   PGDATABASE=sisabaik_dev
   PGUSER=postgres
   PGPASSWORD=GANTI DENGAN PASSWORD LOKAL

```
2. Install dependencies:

```bash
npm install


```
3. Jalankan server dalam mode pengembangan:

```bash
npm run dev

```


4. Buka aplikasi melalui browser di `http://127.0.0.1:3000` untuk menggunakan halaman penguji.



## Pengujian API

* **Uji Mutasi dan CRUD (Otomatis):**
```bash
npm run api:test

```


* **Uji Konkurensi / Request Bersamaan:**
```bash
node scripts/uji-konkurensi.js

```

## Catatan Implementasi & Batasan

* **Identitas & Kontrak Baru:** Pembuatan pesanan sekarang menggunakan `emailPembeli` (mengacu pada tabel `pengguna` fiktif yang aktif) dan ID penawaran/pesanan dikirim dalam response sebagai string digit.


* **Validasi & Transaksi:** Seluruh mutasi pesanan menggunakan transaksi database atomik (`REPEATABLE READ`), penguncian baris (`FOR UPDATE`), serta memvalidasi aturan satu penyedia per pesanan (`MIXED_PROVIDER`).


* **Autentikasi:** Aplikasi ini masih berjalan secara lokal dengan akun latihan dan belum menerapkan sistem login/otorisasi penuh.

```
```