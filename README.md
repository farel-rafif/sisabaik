# SisaBaik - Frontend Vue 3

SisaBaik adalah aplikasi web untuk pengelolaan dan penyelamatan surplus makanan guna mengurangi limbah pangan. Proyek ini dibangun menggunakan Vue 3 dengan Composition API, Vite, dan Tailwind CSS.

Pada Chapter 9 ini, fokus utama adalah membangun antarmuka interaktif sisi klien secara modular, mencakup manajemen props dan emits, pengelolaan fixture data lokal di memori, validasi formulir reaktif, serta pemisahan komponen antarmuka yang meliputi katalog, form penawaran, ringkasan, dan state kosong.

## Persyaratan Sistem dan Lingkungan

* Node.js: Versi 22.18.0 atau 24.12.0 (atau yang lebih baru sesuai spesifikasi modul)
* npm: Versi terbaru yang disertakan bersama Node.js

## Panduan Menjalankan Proyek

Semua perintah di bawah ini dijalankan di dalam direktori `frontend`:

1. Masuk ke folder frontend:
   ```bash
   cd frontend
   ```

2. Instalasi Dependensi:
   ```bash
   npm install
   ```
   (Atau gunakan `npm ci` untuk instalasi bersih berbasis `package-lock.json`)

3. Menjalankan Server Pengembangan:
   ```bash
   npm run dev
   ```
   Akses URL lokal yang tertera di terminal, biasanya pada alamat `http://localhost:5173`.

4. Membuat Berkas Produksi:
   ```bash
   npm run build
   ```

5. Pratinjau Hasil Build:
   ```bash
   npm run preview
   ```

## Struktur Aset dan Catatan Arsitektur

* Aset Ilustrasi SVG (`src/assets/`): Berisi logo aplikasi dan berbagai ikon ilustrasi pendukung status kosong serta kategori makanan.
* Fixture Data dan Memori Lokal: Data awal penawaran makanan serta ringkasan pembeli dan penyedia dikelola secara lokal di dalam memori browser (state lokal Vue) untuk keperluan interaksi antarmuka pada modul ini.
* Catatan Penting: Browser tidak memanggil API backend pada modul ini. Backend Modul 8 tetap memiliki fungsi pendaftaran mandiri, verifikasi penyedia, autentikasi, serta otorisasi; integrasi penuh dengan backend akan dilakukan pada pertemuan berikutnya.