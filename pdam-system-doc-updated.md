# Dokumentasi Sistem Informasi Pembayaran PDAM dengan Integrasi Bank

## 1. Pengguna Sistem

Pengguna sistem terdiri dari:

1. **Admin Super** - Memiliki akses penuh ke seluruh sistem
2. **Admin PDAM** - Mengelola data master dan melihat laporan
3. **Petugas Pencatat Meter** - Menginput data penggunaan air pelanggan
4. **Manajer** - Melihat laporan dan statistik
5. **Integrasi Bank** - Akses sistem melalui API untuk proses pembayaran
6. **Admin IT** - Mengelola konfigurasi API dan integrasi dengan bank
7. **Publik (Pelanggan)** - Akses terbatas untuk cek tagihan dan pembayaran tanpa login

## 2. Tampilan UI Utama & API Endpoints

### 2.1 Backend Admin System

#### 2.1.1 Halaman Login
- Form login dengan input username dan password
- Validasi otentikasi pengguna
- Pengalihan berdasarkan peran pengguna

#### 2.1.2 Dashboard
- Ringkasan data penting (jumlah tagihan, pembayaran hari ini, tunggakan)
- Grafik performa pembayaran
- Notifikasi penting
- Menu navigasi utama

#### 2.1.3 Manajemen Data Pelanggan
- Tabel data pelanggan (ID, nama, alamat, nomor meter, kategori, dll)
- Form pencarian dan filter
- Tombol aksi (lihat detail, edit, nonaktifkan)
- Form tambah/edit pelanggan

#### 2.1.4 Pencatatan Meter
- Form input ID pelanggan atau scan barcode
- Tampilan data pelanggan ringkas
- Input angka meter terbaru
- Upload foto meter (opsional)
- Kalkulasi otomatis pemakaian dan tagihan

#### 2.1.5 Verifikasi Pencatatan Meter
- Daftar pencatatan meter yang perlu diverifikasi
- Tampilan perubahan pemakaian dan deteksi anomali
- Fitur verifikasi batch untuk pencatatan yang normal
- Detail pencatatan dengan foto meter

#### 2.1.6 Penetapan Tagihan
- Tabel tagihan yang perlu ditetapkan
- Filter berdasarkan wilayah, kategori, dll
- Detail kalkulasi biaya tagihan
- Fitur penetapan batch tagihan

#### 2.1.7 Pengelolaan Denda
- Dashboard statistik denda dan pemutihan
- Konfigurasi parameter denda berdasarkan periode
- Manajemen program pemutihan denda
- Rekap denda per periode dan wilayah

#### 2.1.8 Monitoring Pembayaran
- Dashboard pembayaran harian dan bulanan
- Tabel transaksi pembayaran dengan filter dan pencarian
- Rekonsiliasi data pembayaran dengan bank
- Detail transaksi pembayaran

### 2.2 Frontend Public System

#### 2.2.1 Cek Tagihan & Pembayaran
- Form inquiry tagihan dengan input ID pelanggan
- Tampilan detail tagihan
- Pilihan metode pembayaran (Virtual Account, QRIS, Teller)
- Proses pembayaran dengan instruksi sesuai metode yang dipilih

### 2.3 API Endpoints untuk Integrasi Bank

- `/api/v1/payment/verify` - Verifikasi tagihan pelanggan
- `/api/v1/payment/process` - Proses pembayaran 
- `/api/v1/payment/status` - Cek status pembayaran
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
Sistem Kalkulasi Pemakaian -> Admin Verifikasi Pencatatan ->
Sistem Kalkulasi Tagihan -> Penetapan Tagihan -> 
Terbit Tagihan -> Notifikasi Internal
```

### 3.3 Proses Pembayaran via Bank
```
Pelanggan -> Cek Tagihan di Website PDAM -> Pilih Metode Pembayaran -> 
Sistem Hasilkan VA/QRIS -> Pelanggan Bayar via Bank -> 
Bank Hit API PDAM -> API PDAM Validasi Data -> 
Update Status Pembayaran -> Kirim Response ke Bank -> 
Bank Cetak/Kirim Bukti Pembayaran
```

### 3.4 Manajemen Denda dan Pemutihan
```
Sistem -> Identifikasi Tunggakan -> Admin Set Kebijakan Denda -> 
Penerbitan Denda Otomatis -> Admin Buat Program Pemutihan (jika ada) ->
Sistem Terapkan Keringanan Denda -> Pelanggan Bayar Tagihan & Denda
```

### 3.5 Rekonsiliasi Pembayaran
```
Admin -> Jalankan Rekonsiliasi (Manual/Otomatis) -> 
Sistem Bandingkan Data PDAM vs Bank -> Identifikasi Transaksi Tidak Cocok ->
Admin Proses Penyesuaian -> Sistem Update Rekonsiliasi -> 
Generate Laporan Rekonsiliasi
```

### 3.6 Pelaporan dan Analisis
```
Manajer/Admin -> Pilih Jenis Laporan -> Tentukan Parameter -> 
Sistem Generate Laporan -> Tampilkan/Cetak Laporan
```

## 4. Struktur Database (Konseptual)

### 4.1 Tabel Utama
- **pelanggan** - Data pelanggan PDAM
- **meter_reading** - Pencatatan angka meter
- **verification** - Data verifikasi pencatatan meter
- **tagihan** - Data tagihan bulanan
- **denda** - Data denda per tagihan
- **kebijakan_denda** - Parameter denda per periode
- **program_pemutihan** - Program keringanan denda
- **pembayaran** - Riwayat pembayaran
- **va_transaction** - Data virtual account yang dibuat
- **qris_transaction** - Data kode QRIS yang dibuat
- **tarif** - Kategori dan struktur tarif
- **pengguna** - Data pengguna sistem
- **log_aktivitas** - Catatan aktivitas pengguna
- **wilayah** - Data wilayah layanan
- **bank_integration** - Konfigurasi integrasi dengan bank
- **payment_transaction** - Log transaksi API pembayaran
- **rekonsiliasi** - Data hasil rekonsiliasi
- **rekonsiliasi_detail** - Detail transaksi rekonsiliasi

## 5. Fitur-fitur Utama

### 5.1 Backend Admin System
1. **Manajemen Data Pelanggan**
   - CRUD operasi untuk data pelanggan
   - Import/export data pelanggan
   - Riwayat perubahan data pelanggan

2. **Pencatatan dan Verifikasi Meter**
   - Input pencatatan meter dengan validasi
   - Verifikasi pencatatan dengan deteksi anomali
   - Upload dan review foto meter

3. **Penetapan Tagihan**
   - Kalkulasi tagihan berdasarkan pemakaian dan tarif
   - Penetapan tagihan secara batch
   - Daftar tagihan dengan filter dan pencarian

4. **Pengelolaan Denda**
   - Konfigurasi kebijakan denda per periode
   - Manajemen program pemutihan denda
   - Laporan denda dan pemutihan

5. **Monitoring Pembayaran**
   - Daftar transaksi pembayaran
   - Rekonsiliasi dengan data bank
   - Laporan pembayaran per periode/channel

### 5.2 Frontend Public System
1. **Cek Tagihan**
   - Inquiry tagihan berdasarkan ID pelanggan
   - Tampilan rincian tagihan
   - Riwayat tagihan dan status pembayaran

2. **Pembayaran Online**
   - Pembayaran via Virtual Account
   - Pembayaran via QRIS
   - Petunjuk pembayaran melalui teller bank

### 5.3 Integrasi dengan Bank
1. **API Integration**
   - Verifikasi tagihan pelanggan
   - Proses pembayaran real-time
   - Notifikasi status pembayaran

2. **Virtual Account & QRIS**
   - Pembuatan dan manajemen VA
   - Pembuatan dan manajemen QRIS
   - Validasi pembayaran dari berbagai channel

## 6. Teknologi yang Digunakan

- **Frontend**: JavaScript ES6+, HTML5, CSS3
- **Backend**: Node.js dengan Express.js
- **Database**: PostgreSQL
- **API**: RESTful API dengan Express.js
- **Integrasi Pembayaran**: Webhook, API Bank, VA, QRIS
- **Autentikasi**: JWT (JSON Web Token)
- **Keamanan API**: Rate limiting, API Keys, Signature Verification
- **Pelaporan**: PDF.js untuk pembuatan laporan
- **Versioning**: Git

## 7. Keamanan Sistem

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

## 8. Deployment dan Infrastruktur

### 8.1 Requirement Server
- **Web Server**: Nginx sebagai reverse proxy
- **Application Server**: Node.js server (min. 4 GB RAM)
- **Database Server**: PostgreSQL (min. 8 GB RAM)
- **Operating System**: Linux (Ubuntu Server)
- **SSL Certificate**: Wajib untuk keamanan komunikasi

### 8.2 Arsitektur Deployment
- Lingkungan development, staging, dan production terpisah
- Load balancer untuk high availability
- Database replication untuk backup dan keandalan
- Monitoring server dengan Prometheus dan Grafana
- Backup otomatis dengan retention policy

## 9. Integrasi dan Interoperabilitas

### 9.1 Integrasi dengan Bank Sultra
- API pembayaran terintegrasi
- Verifikasi tagihan real-time
- Notifikasi pembayaran otomatis
- Rekonsiliasi harian

### 9.2 Potensi Integrasi Lainnya
- Sistem SMS Gateway untuk notifikasi pelanggan
- Mobile app PDAM untuk cek tagihan dan pembayaran
- Integrasi dengan sistem lain di lingkungan pemerintah daerah

## 10. Pemeliharaan dan Dukungan

### 10.1 Pemeliharaan Rutin
- Update security patches
- Backup dan restore testing
- Performance tuning
- Monitoring dan analisis log

### 10.2 Dukungan Pengguna
- Dokumentasi user manual
- Training untuk staf PDAM
- Help desk untuk penanganan masalah
- Kanal support via email dan telepon