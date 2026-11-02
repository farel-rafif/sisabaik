# Laporan Hasil Uji - Chapter 9: Membangun Antarmuka dengan Vue

Laporan ini mendokumentasikan hasil pengujian implementasi antarmuka frontend SisaBaik menggunakan Vue 3, Vite, dan Tailwind CSS sesuai dengan instruksi pada Chapter 9.

## 1. Informasi Pengujian
* Penguji: Mahasiswa / Pengembang SisaBaik
* Tanggal Pengujian: 2 November 2026
* Commit Terkait: `feat: build SisaBaik Vue components`
* Versi Lingkungan:
  * Node.js: v22.18.0
  * npm: 10.9.2

## 2. Hasil Aktual Pengujian Komponen dan Fitur
Pengujian dilakukan secara lokal pada server pengembangan Vite (`http://localhost:5173`) dan melalui proses build produksi. Berikut adalah rincian hasil pengujian:

* **Katalog dan Interaksi Pencarian/Filter**: Komponen daftar penawaran berhasil merespons input pencarian dan pilihan kategori secara real-time. Data awal dari fixture lokal termuat dengan baik di memori browser.
* **Kondisi Kosong (Empty State)**: Komponen state kosong tampil dengan benar beserta ilustrasi SVG saat pencarian tidak menemukan hasil atau ketika daftar penawaran kosong.
* **Formulir Valid (Valid Form)**: Formulir penawaran surplus makanan dapat diisi dengan benar, melakukan validasi reaktif, dan berhasil mengirimkan data baru ke state penawaran lokal melalui mekanisme `emits`.
* **Formulir Invalid (Invalid Form)**: Validasi formulir berhasil mencegah pengiriman data jika terdapat field wajib yang kosong atau format tidak sesuai, serta menampilkan pesan galat yang jelas.
* **Aliran Props dan Emits**: Komunikasi antar komponen berjalan lancar. Komponen induk meneruskan data penawaran ke komponen anak menggunakan `props`, sedangkan aksi penambahan data maupun pemfilteran dikirim kembali ke komponen induk menggunakan `emits`.
* **Perhitungan Ringkasan Pembeli dan Penyedia**: Komponen ringkasan secara dinamis menghitung total item aktif, jumlah surplus makanan yang terselamatkan, serta total estimasi nilai secara reaktif berdasarkan data fixture lokal di memori.

## 3. Dokumentasi Visual (Referensi Tangkapan Layar)
Berikut adalah daftar tangkapan layar yang telah diverifikasi pada direktori kerja:
1. Tampilan Katalog Penawaran Surplus Makanan
2. Tampilan Kondisi Kosong (Empty State)
3. Validasi Formulir Invalid (Pesan galat input)
4. Pengiriman Formulir Valid (Sukses menambah data penawaran)
5. Tampilan Responsif Perangkat Seluler (Mobile View)
6. Output Terminal pada saat Proses Build Produksi (`npm run build`)

## 4. Catatan Arsitektur dan Keamanan
* Browser tidak memanggil API backend eksternal pada modul ini; seluruh manipulasi state dilakukan secara lokal di memori browser menggunakan fixture data.
* Backend dari Modul 8 tetap mempertahankan fungsi pendaftaran mandiri, verifikasi penyedia, autentikasi, serta otorisasi. Integrasi penuh antara frontend Vue 3 dan backend akan dilaksanakan pada pertemuan atau modul berikutnya.
* Tidak ada variabel lingkungan rahasia (`.env` backend), kata sandi, *hash*, JWT, atau data akun nyata yang disertakan di dalam dokumentasi maupun kode sumber frontend.