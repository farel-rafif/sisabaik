# Dokumentasi API SisaBaik

Dokumentasi ini merangkum endpoint, spesifikasi payload, aturan keamanan, serta kebijakan akses data pada SisaBaik API.

## Endpoint Registrasi

Sistem menyediakan dua jalur pendaftaran terpisah untuk mengakomodasi perbedaan data profil:

1. **Registrasi Konsumen**
   * `POST /api/auth/register/consumer`
   * Mendaftarkan akun baru sebagai pembeli/konsumen umum.

2. **Registrasi Penyedia**
   * `POST /api/auth/register/provider`
   * Mendaftarkan akun baru sebagai penyedia makanan dengan menyertakan informasi badan usaha.

## Field Profil Usaha dan Body Bisnis

Payload untuk registrasi penyedia atau pembaruan profil usaha mencakup atribut berikut:
* `business_name` (string, wajib): Nama resmi atau nama dagang usaha.
* `business_category` (string, wajib): Kategori kuliner (contoh: Bakery, Restoran, Katering).
* `address` (string, wajib): Alamat fisik lokasi usaha.
* `operational_hours` (string/object): Jam operasional penyerahan makanan surplus.

## Status Error API

Respon kesalahan mengembalikan kode status HTTP standar:
* `400 Bad Request`: Validasi input gagal atau format payload tidak sesuai.
* `401 Unauthorized`: Token akses tidak valid, kedaluwarsa, atau tidak disertakan.
* `403 Forbidden`: Hak akses tidak mencukupi (misalnya penyedia belum lolos tahap peninjauan).
* `404 Not Found`: Sumber daya atau endpoint tidak ditemukan.
* `429 Too Many Requests`: Melewati batas laju permintaan (*rate limit*).
* `500 Internal Server Error`: Kendala tak terduga pada server atau database.

## Batas Laju (Rate Limiting) Terpisah

Sistem menerapkan pembatasan laju permintaan yang dibedakan berdasarkan jenis endpoint:
* **Endpoint Publik / Umum**: Batas laju standar untuk navigasi katalog.
* **Endpoint Autentikasi & Transaksi**: Batas laju yang lebih ketat guna mencegah serangan brute-force dan penyalahgunaan pemesanan.

## Cakupan Pesanan (Order Scopes)

Pengelolaan pesanan dibatasi berdasarkan peran dan kepemilikan:
* Konsumen hanya dapat melihat, membuat, dan membatalkan pesanan milik mereka sendiri.
* Penyedia hanya dapat mengelola pesanan yang masuk ke inventaris produk atau toko mereka.

## Kebijakan Katalog Publik

* Katalog publik (`GET /api/catalog` atau sejenisnya) hanya menampilkan daftar penawaran dari **penyedia yang status akunnya sudah disetujui (verified/reviewed)**.
* Penawaran dari penyedia yang masih dalam status peninjauan tidak akan muncul di katalog publik.