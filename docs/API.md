```markdown
# Dokumentasi Kontrak REST API - SisaBaik Chapter 7

Dokumentasi ini merangkum endpoint, struktur *query*, *field* wajib, aturan bisnis, serta kode status respons yang diimplementasikan secara nyata pada REST API SisaBaik.

## Base URL
`http://127.0.0.1:3000/api`

---

## Daftar Endpoint Terimplementasi

### 1. Health Check
* **GET `/health`**
  * **Deskripsi:** Memeriksa status proses server dan koneksi basis data PostgreSQL.
  * **Query Parameter:** Tidak ada.
  * **Contoh Response Sukses (`200 OK`):**
    ```json
    {
      "success": true,
      "data": {
        "status": "ok",
        "database": "connected",
        "waktu": "2026-10-12T00:00:00.000Z"
      }
    }
    ```

---

### 2. Penawaran (Offers)
* **GET `/penawaran`**
  * **Deskripsi:** Mengambil daftar koleksi penawaran dengan dukungan pencarian dan filter.
  * **Query yang Didukung:**
    * `q` (string): Kata kunci pencarian judul/deskripsi penawaran.
    * `kategori` (string): Filter berdasarkan kategori penawaran.
    * `status` (string): Filter berdasarkan status penawaran.
    * `tersedia` (boolean/string): Filter untuk penawaran yang stoknya masih ada.
    * `page` (integer): Nomor halaman paginasi.
    * `limit` (integer): Batas jumlah data per halaman.

* **GET `/penawaran/:id`**
  * **Deskripsi:** Mengambil detail satu data penawaran spesifik berdasarkan parameter ID.
  * **Parameter URL:** `:id` (ID unik penawaran).

* **POST `/penawaran`**
  * **Deskripsi:** Menambahkan data penawaran baru (memerlukan header `Content-Type: application/json`).
  * **Field Wajib (Body Request):**
    * `penyediaId` (string/integer): ID penyedia/merchant pemilik penawaran.
    * `judul` (string): Nama atau judul penawaran produk.
    * `harga` (number): Harga satuan produk.
    * `stok` (integer): Jumlah ketersediaan awal paket.

* **PUT `/penawaran/:id`**
  * **Deskripsi:** Memperbarui atribut data penawaran yang diizinkan (kecuali ID dan relasi penyedia).
  * **Field Wajib/Editable (Body Request):**
    * `judul` (string)
    * `harga` (number)
    * `stok` (integer)
    * `status` (string)

* **DELETE `/penawaran/:id`**
  * **Deskripsi:** Menghapus penawaran. Sistem akan menolak penghapusan jika penawaran tersebut sudah memiliki riwayat transaksi/pesanan.
  * **Response Sukses:** `204 No Content` (tanpa body).
  * **Response Gagal:** `409 Conflict` (jika penawaran sudah berriwayat).

---

### 3. Pesanan (Orders) & Aturan Bisnis
* **GET `/pesanan`**
  * **Deskripsi:** Mengambil daftar ringkasan pesanan secara terpaginasi.
  * **Query yang Didukung:** `page`, `limit`.

* **GET `/pesanan/:id`**
  * **Deskripsi:** Mengambil detail lengkap struktur pesanan (termasuk data header pesanan dan rincian item di dalamnya).
  * **Parameter URL:** `:id` (ID unik pesanan).

* **POST `/pesanan`**
  * **Deskripsi:** Membuat pesanan baru secara atomik menggunakan transaksi database, dilengkapi validasi stok dan aturan satu penyedia.
  * **Field Wajib (Body Request):**
    * `emailPembeli` (string): Email akun pembeli terdaftar yang aktif di database.
    * `items` (array of objects): Daftar barang yang dibeli, di mana setiap objek item wajib berisi:
      * `penawaranId` (string/integer)
      * `kuantitas` (integer)
  * **Aturan Bisnis Penting (Single Provider Rule / Validasi Lintas Penyedia):**
    * Sistem menerapkan validasi ketat agar seluruh item yang dimasukkan ke dalam satu kali transaksi pesanan **wajib berasal dari penyedia (*merchant*) yang sama**. Jika pembeli mencampur item dari penyedia berbeda, transaksi akan ditolak dengan error `MIXED_PROVIDER` (`409 Conflict`).
  * **Contoh Request Body:**
    ```json
    {
      "emailPembeli": "siti.pembeli@example.test",
      "items": [
        {
          "penawaranId": "7",
          "kuantitas": 1
        }
      ]
    }
    ```

---

## Standar Format Response & Kode Status Error

* **Format Sukses:**
  ```json
  {
    "success": true,
    "data": { ... }
  }

```

* **Format Gagal:**
```json
{
  "success": false,
  "error": {
    "code": "KODE_ERROR",
    "message": "Pesan deskripsi kesalahan..."
  }
}

```


* **Daftar Kode Status HTTP yang Digunakan:**
    * `200 OK`: Berhasil membaca atau memperbarui data.
    * `201 Created`: Berhasil membuat sumber data baru (disertai data hasil pembuatan).
    * `204 No Content`: Berhasil menghapus data tanpa mengembalikan isi *body*.
    * `400 Bad Request`: Validasi input gagal, atribut wajib kosong, atau format JSON rusak.
    * `404 Not Found`: Endpoint atau ID data yang dicari tidak ditemukan di database.
    * `409 Conflict`: Terjadi konflik aturan bisnis (seperti stok habis saat transaksi, penghapusan penawaran berriwayat, atau pelanggaran aturan satu penyedia / *mixed provider*).
    * `413 Payload Too Large`: Ukuran *payload* atau body request melampaui batas maksimum server.
    * `415 Unsupported Media Type`: Header `Content-Type` tidak sesuai (bukan `application/json` pada method mutasi).
    * `500 Internal Server Error`: Terjadi kendala tak terduga pada server atau kegagalan koneksi basis data.



```

```