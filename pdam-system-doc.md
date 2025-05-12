# Dokumen Sistem Informasi Pembayaran PDAM dengan Integrasi Bank

## 1. Pengguna Sistem

Dengan batasan bahwa pelanggan tidak termasuk sebagai pengguna aplikasi, maka pengguna sistem terdiri dari:

1. **Admin Super** - Memiliki akses penuh ke seluruh sistem
2. **Admin PDAM** - Mengelola data master dan melihat laporan
3. **Petugas Pencatat Meter** - Menginput data penggunaan air pelanggan
4. **Manajer** - Melihat laporan dan statistik
5. **Integrasi Bank** - Akses sistem melalui API untuk proses pembayaran
6. **Admin IT** - Mengelola konfigurasi API dan integrasi dengan bank

## 2. Tampilan UI Utama & API Endpoints

### 2.1 Halaman Login
- Form login dengan input username dan password
- Validasi otentikasi pengguna
- Pengalihan berdasarkan peran pengguna

### 2.2 Dashboard
- Ringkasan data penting (jumlah tagihan, pembayaran hari ini, tunggakan)
- Grafik performa pembayaran
- Notifikasi penting
- Menu navigasi utama

### 2.3 Manajemen Data Pelanggan
- Tabel data pelanggan (ID, nama, alamat, nomor meter, kategori, dll)
- Form pencarian dan filter
- Tombol aksi (lihat detail, edit, nonaktifkan)
- Form tambah/edit pelanggan

### 2.4 Pencatatan Meter
- Form input ID pelanggan atau scan barcode
- Tampilan data pelanggan ringkas
- Input angka meter terbaru
- Upload foto meter (opsional)
- Kalkulasi otomatis pemakaian dan tagihan

### 2.5 Integrasi Pembayaran Bank
- Dashboard status integrasi dengan sistem bank
- Log transaksi pembayaran dari bank
- Konfigurasi endpoint API
- Monitoring status pembayaran
- Manajemen webhook notifikasi

### 2.6 Manajemen Tagihan
- Tabel daftar tagihan (status: lunas, belum bayar, terlambat)
- Filter berdasarkan periode, wilayah, status
- Tombol aksi (lihat detail, proses pembayaran)
- Cetak rekap tagihan

### 2.7 Laporan dan Statistik
- Filter laporan (periode, wilayah, kategori pelanggan)
- Grafik pendapatan
- Statistik pembayaran tepat waktu vs terlambat
- Analisis penggunaan air per kategori pelanggan
- Cetak laporan dalam berbagai format

### 2.8 Pengaturan Sistem
- Manajemen pengguna dan hak akses
- Konfigurasi parameter sistem (tarif, denda, dll)
- Konfigurasi backup database
- Log aktivitas pengguna

### 2.9 Manajemen API & Integrasi Bank
- Dashboard monitoring API
- Konfigurasi endpoint dan kredensial bank
- Manajemen API keys dan signature keys
- Log transaksi API
- Pengaturan webhook dan notifikasi
- Pengujian API

### 2.10 API Endpoints (Untuk Integrasi Bank)
- `/api/v1/payment/verify` - Verifikasi tagihan pelanggan
- `/api/v1/payment/process` - Proses pembayaran 
- `/api/v1/payment/status` - Cek status pembayaran
- `/api/v1/payment/cancel` - Pembatalan transaksi
- `/api/v1/payment/receipt` - Generate bukti pembayaran
- `/api/webhook/payment-notification` - Endpoint notifikasi dari bank

## 3. Alur Sistem/Proses Bisnis

### 3.1 Pendaftaran Pelanggan Baru
```
Admin PDAM -> Input Data Pelanggan -> Penentuan Kategori Tarif -> 
Penerbitan ID Pelanggan -> Aktivasi Layanan
```

### 3.2 Pencatatan Meter dan Penerbitan Tagihan
```
Petugas Pencatat -> Input ID Pelanggan -> Input Angka Meter -> 
Sistem Kalkulasi Pemakaian -> Sistem Kalkulasi Tagihan -> 
Penerbitan Tagihan -> Notifikasi Internal
```

### 3.3 Proses Pembayaran via Bank
```
Pelanggan -> Aplikasi Bank (Teller/QRIS/ATM) -> Bank Hit API PDAM -> 
API PDAM Validasi Data -> Update Status Pembayaran -> 
Kirim Response ke Bank -> Bank Cetak/Kirim Bukti Pembayaran
```

### 3.4 Manajemen Tunggakan
```
Sistem -> Identifikasi Tunggakan -> Admin PDAM Review -> 
Penerbitan Denda -> Notifikasi Internal
```

### 3.5 Pelaporan dan Analisis
```
Manajer/Admin -> Pilih Jenis Laporan -> Tentukan Parameter -> 
Sistem Generate Laporan -> Tampilkan/Cetak Laporan
```

## 4. Struktur Database (Konseptual)

### 4.1 Tabel Utama
- **pelanggan** - Data pelanggan PDAM
- **meter_reading** - Pencatatan angka meter
- **tagihan** - Data tagihan bulanan
- **pembayaran** - Riwayat pembayaran
- **tarif** - Kategori dan struktur tarif
- **pengguna** - Data pengguna sistem
- **log_aktivitas** - Catatan aktivitas pengguna
- **wilayah** - Data wilayah layanan
- **bank_integration** - Konfigurasi integrasi dengan bank
- **payment_transaction** - Log transaksi API pembayaran
- **api_keys** - Manajemen API keys untuk Bank
- **webhook_events** - Log notifikasi webhook

## 5. Teknologi yang Digunakan

- **Frontend**: JavaScript ES6+, HTML5, CSS3, Framework UI (seperti Bootstrap)
- **Backend**: Node.js dengan Express.js
- **Database**: PostgreSQL
- **API**: RESTful API dengan Express.js, API Gateway
- **Integrasi Pembayaran**: Webhook, API Bank, Enkripsi Standar Perbankan
- **Autentikasi**: JWT (JSON Web Token), OAuth 2.0 untuk integrasi bank
- **Keamanan API**: Rate limiting, API Keys, Signature Verification
- **Pelaporan**: PDF.js atau library serupa untuk pembuatan laporan
- **Versioning**: Git

## 6. Keamanan Sistem

- Autentikasi multi-level berbasis peran
- Enkripsi password dan data sensitif
- Validasi input untuk mencegah SQL Injection
- Rate limiting untuk mencegah brute force attack
- Pencatatan log aktivitas pengguna dan transaksi
- Backup database reguler
- Enkripsi komunikasi API dengan TLS/SSL
- Implementasi signature digital untuk verifikasi transaksi
- Whitelist IP untuk akses API bank
- Audit trail untuk semua transaksi keuangan
