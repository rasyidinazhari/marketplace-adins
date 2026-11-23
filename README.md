# WarungAdins - Platform E-Commerce Multi-Store

WarungAdins adalah platform marketplace e-commerce multi-store yang menghubungkan pembeli (Buyer) dan penjual (Seller). Proyek ini dibangun dengan arsitektur decoupled yang memisahkan antara frontend berbasis Single Page Application (SPA) dan backend berbasis RESTful API.

---

## Daftar Isi

- Tentang Proyek
- Tech Stack
- Arsitektur Sistem
- Struktur Direktori
- Fitur Utama
  - Modul Publik
  - Modul Pembeli (Buyer)
  - Modul Penjual (Seller)
  - Autentikasi dan Keamanan
- Skema Basis Data
- Prasyarat Sistem
- Panduan Instalasi dan Menjalankan Aplikasi
  - Konfigurasi dan Menjalankan Backend
  - Konfigurasi dan Menjalankan Frontend
- Dokumentasi Endpoint API
- Panduan Migrasi Database dan Troubleshooting
- Lisensi

---

## Tentang Proyek

Platform WarungAdins dirancang untuk mendukung operasional e-commerce skala penuh dengan model multi-toko. Penjual dapat memiliki dan mengelola satu atau lebih toko, menerbitkan produk dengan kategori fleksibel, memproses pesanan, serta mengatur voucher promosi toko. Pembeli dapat menjelajahi katalog produk, menambahkan barang ke keranjang belanja, memilih voucher toko saat checkout, melacak status pesanan, dan memberikan ulasan serta rating produk setelah transaksi selesai.

---

## Tech Stack

### Frontend
- Framework: Angular 17 (Standalone Components)
- State & Reaktivitas: RxJS dan Angular Signals
- Styling: Tailwind CSS, DaisyUI, Bootstrap utilities
- Token Management: jwt-decode
- HTTP Client: Angular HttpClient dengan HttpInterceptor

### Backend
- Framework: ASP.NET Core Web API (.NET 6.0)
- ORM: Entity Framework Core 6 (Code-First)
- Database Provider: Npgsql (PostgreSQL)
- Autentikasi: JWT Bearer Token (JSON Web Token)
- Pola Desain: Controller - Service - Repository (Generic Repository Pattern)
- Dokumentasi API: Swagger / OpenAPI (Swashbuckle)
- Error Handling: Global Exception Middleware terpusat

### Basis Data
- Engine: PostgreSQL (v14 atau lebih baru)

---

## Arsitektur Sistem

Sistem menggunakan pola arsitektur REST API dengan pemisahan tanggung jawab yang jelas:

1. Frontend Layer: Aplikasi Angular berjalan di browser klien (port default 4200), berinteraksi dengan API melalui protokol HTTPS. Menggunakan route guards untuk proteksi rute berdasarkan peran (Buyer/Seller/Public).
2. API Gateway / Controller Layer: Menerima request HTTP, melakukan validasi payload DTO, mengekstrak identitas pengguna dari klaim token JWT, dan memanggil Service Layer.
3. Service Layer: Menangani seluruh logika bisnis aplikasi, kalkulasi diskon voucher, validasi stok, pembaruan status transaksi, dan pemetaan data.
4. Repository Layer: Abstraksi akses data menggunakan generic repository (`IRepository<T>`) dan Entity Framework Core `DbContext`.
5. Database Layer: Basis data relasional PostgreSQL dengan relasi referensial dan constraint integritas data.

---

## Struktur Direktori

```text
miniproject1/
|-- backend/
|   |-- marketplace-AdIns-BE.sln
|   `-- marketplace-AdIns-BE/
|       |-- Controllers/          # API Controllers per domain
|       |-- Data/                 # MarketplaceDbContext dan konfigurasi model
|       |-- DTOs/                 # Data Transfer Objects untuk request/response
|       |-- Middlewares/          # Global Exception Middleware
|       |-- Migrations/           # EF Core Code-First Migrations
|       |-- Models/               # Entitas model database
|       |-- Repositories/         # IRepository dan implementasi Generic Repository
|       |-- Services/             # Business Logic Layer (Interfaces & Implementations)
|       |-- appsettings.json      # Konfigurasi koneksi database dan JWT
|       `-- Program.cs            # Inisialisasi dependency injection dan middleware pipeline
|
`-- frontend/
    |-- angular.json              # Konfigurasi workspace Angular
    |-- package.json              # Dependensi npm dan script
    |-- tailwind.config.js        # Konfigurasi Tailwind CSS
    `-- src/
        |-- environments/         # Konfigurasi variabel lingkungan (API URL)
        `-- app/
            |-- components/       # Reusable components (Hero, Showcase, Modal)
            |-- guards/           # Functional route guards (auth, loggedOut, storefront)
            |-- interceptors/     # HTTP interceptor untuk menyematkan JWT Bearer token
            |-- layout/           # Navbar dan Footer publik
            |-- models/           # TypeScript interfaces dan DTOs frontend
            |-- pages/            # Halaman aplikasi
            |   |-- auth/         # Login dan Registrasi umum
            |   |-- buyer/        # Cart, Checkout, Order List, Order Detail, Login Buyer
            |   |-- public/       # Landing page, Katalog Produk, Detail Produk
            |   `-- seller/       # Dashboard Layout, Product Management, Order List, Voucher
            `-- services/         # Layanan komunikasi HTTP per domain
```

---

## Fitur Utama

### Modul Publik
- Halaman Utama (Landing Page): Menampilkan statistik dinamis langsung dari database (total produk aktif, toko aktif, dan pembeli terdaftar) serta showcase produk unggulan.
- Katalog Produk: Penjelajahan produk dengan fitur pencarian teks dan filter berdasarkan kategori produk.
- Detail Produk: Menampilkan informasi lengkap produk, galeri gambar, deskripsi, harga, status stok, profil toko penjual, rata-rata rating bintang, jumlah produk terjual, dan daftar ulasan pembeli.

### Modul Pembeli (Buyer)
- Autentikasi Pembeli: Registrasi akun baru dan login khusus peran Pembeli.
- Keranjang Belanja:
  - Pengelompokan item keranjang berdasarkan toko.
  - Pemilihan item keranjang belanja secara granular (checkbox per item atau pilih semua).
  - Penambahan, pengurangan kuantitas, dan penghapusan item dari keranjang.
  - Perhitungan subtotal harga secara langsung.
- Checkout dan Pembayaran:
  - Pemrosesan checkout satu toko per transaksi (single-store checkout).
  - Pemilihan dan penambahan alamat pengiriman.
  - Pemilihan voucher toko yang valid dengan perhitungan otomatis potongan harga (nominal tetap atau persentase).
  - Ringkasan total pembayaran (subtotal barang dikurangi diskon voucher).
- Manajemen Pesanan:
  - Melihat riwayat pesanan dengan filter status (Menunggu Pembayaran, Diproses, Dikirim, Selesai).
  - Halaman detail pesanan dengan rincian item, alamat tujuan, dan rincian harga.
- Ulasan Produk: Memberikan rating bintang (skala 1-5) dan komentar teks pada produk dari pesanan yang telah berstatus selesai.

### Modul Penjual (Seller)
- Portal Khusus Penjual: Halaman login tersendiri dengan tata letak dashboard yang terisolasi dari area publik.
- Dukungan Multi-Toko: Pengalihan toko (Store Switcher) bagi penjual yang mengelola lebih dari satu gerai toko.
- Manajemen Produk:
  - Daftar produk toko dengan informasi stok dan harga.
  - Form tambah dan edit produk dengan format input mata uang Rupiah dan dukungan pemilihan banyak kategori sekaligus (multi-category).
  - Hapus produk yang sudah tidak dijual.
- Manajemen Pesanan Masuk:
  - Melihat daftar pesanan masuk dari pembeli.
  - Meninjau detail pesanan masuk dan produk yang dibeli.
  - Memperbarui status pesanan secara bertahap: PENDING_PAYMENT -> ON_PROCESS -> ON_DELIVERY -> DELIVERED.
- Manajemen Voucher Toko:
  - Pembuatan voucher promosi toko (persentase atau nominal, batas minimum pembelian, batas maksimal diskon, kuota penggunaan, dan masa berlaku).
  - Mengaktifkan atau menonaktifkan status voucher.

### Autentikasi dan Keamanan
- Autentikasi berbasis JSON Web Token (JWT) dengan algoritma enkripsi simetris.
- Klaim token menyimpan UserId, Email, dan Role ("Buyer" atau "Seller").
- HTTP Request Interceptor di frontend yang otomatis menyertakan header `Authorization: Bearer <token>`.
- Functional Route Guards Angular:
  - `authGuard`: Memastikan pengguna telah login dan memiliki role yang sesuai sebelum mengakses halaman tertentu.
  - `storefrontGuard`: Membatasi akun dengan role Seller agar tidak mengakses alur belanja pembeli.
  - `loggedOutGuard`: Mengalihkan pengguna yang sudah login agar tidak membuka kembali halaman login/register.
- Password hashing pada level aplikasi dan validasi kepemilikan data (data ownership validation) di setiap endpoint backend.

---

## Skema Basis Data

Sistem basis data PostgreSQL mencakup entitas utama sebagai berikut:

- `Users`: Menyimpan identitas akun (Id, Email, Password, Name, PhoneNumber, Created_at, Updated_at).
- `Roles`: Definisi peran sistem (Buyer, Seller).
- `UserRoles`: Relasi many-to-many antara pengguna dan peran.
- `Stores`: Data toko penjual, berelasi dengan User pemilik toko.
- `Categories`: Kategori produk (misal: Elektronik, Pakaian, Makanan).
- `Products`: Data barang jualan (Name, Description, Price, Stock, ImageUrl, StoreId).
- `ProductCategories`: Relasi many-to-many antara produk dan kategori.
- `Carts`: Item keranjang belanja pengguna yang persisten di basis data.
- `Addresses`: Alamat pengiriman pembeli dengan penanda alamat default.
- `Vouchers`: Voucher diskon milik toko tertentu dengan kuota dan periode validitas.
- `Orders`: Catatan transaksi pembelian yang mencakup UserId, StoreId, AddressId, VoucherId, TotalPrice, DiscountAmount, FinalPrice, dan Status.
- `OrderItems`: Rincian produk, kuantitas, dan harga satuan dalam pesanan.
- `Reviews`: Rating (1-5) dan komentar ulasan yang terhubung ke produk, pengguna, dan pesanan.

---

## Prasyarat Sistem

Sebelum menjalankan proyek, pastikan perangkat telah terinstal perangkat lunak berikut:

1. .NET SDK versi 6.0 atau lebih baru
2. Node.js versi 18.x atau lebih baru dan npm
3. PostgreSQL Server versi 14 atau lebih baru
4. Git
5. Visual Studio Code, Visual Studio 2022, atau IDE pilihan lainnya
6. dotnet-ef CLI tool (opsional untuk menjalankan migrasi langsung melalui terminal)
   Instalasi dotnet-ef global jika belum ada:
   ```bash
   dotnet tool install --global dotnet-ef --version 6.*
   ```

---

## Panduan Instalasi dan Menjalankan Aplikasi

### 1. Kloning Repositori
```bash
git clone <URL_REPOSITORY>
cd miniproject1
```

---

### 2. Konfigurasi dan Menjalankan Backend

#### A. Konfigurasi Database dan JWT
Buka file konfigurasi di `backend/marketplace-AdIns-BE/appsettings.json`. Sesuaikan string koneksi PostgreSQL dan kunci rahasia JWT sesuai lingkungan Anda:

```json
{
  "ConnectionStrings": {
    "MarketplaceAdIns": "Host=localhost;Port=5432;Database=MarketplaceAdIns;Username=postgres;Password=postgres"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*",
  "Jwt": {
    "Key": "IniAdalahKunciRahasiaYangSangatPanjangDanAman123",
    "Issuer": "MarketplaceAdInsAPI",
    "Audience": "MarketplaceAdInsClient",
    "ExpireMinutes": 60
  }
}
```

#### B. Menjalankan Migrasi Basis Data
Pastikan PostgreSQL service sedang berjalan dan database `MarketplaceAdIns` telah tersedia. Masuk ke direktori backend dan terapkan migrasi:

```bash
cd backend/marketplace-AdIns-BE
dotnet ef database update
```

Jika terjadi perubahan model dan perlu membuat migrasi baru:
```bash
dotnet ef migrations add <NamaMigrasiBaru>
dotnet ef database update
```

#### C. Menjalankan Aplikasi Backend
Jalankan backend API:
```bash
dotnet run
```

Setelah server aktif, API akan berjalan pada:
- HTTPS: `https://localhost:7153`
- Dokumentasi Swagger UI: `https://localhost:7153/swagger`

---

### 3. Konfigurasi dan Menjalankan Frontend

#### A. Masuk ke Direktori Frontend dan Pasang Dependensi
Buka terminal baru di direktori root proyek, lalu masuk ke folder frontend:

```bash
cd frontend
npm install
```

#### B. Konfigurasi Lingkungan (Environment)
Pastikan berkas `src/environments/environment.ts` mengarah ke URL API backend yang sedang aktif:

```typescript
export const environment = {
  production: false,
  apiUrl: 'https://localhost:7153/api',
};
```

#### C. Menjalankan Server Pengembangan
Jalankan server pengembangan Angular:

```bash
npm start
```
atau
```bash
ng serve
```

Akses aplikasi web melalui browser di:
`http://localhost:4200`

---

## Dokumentasi Endpoint API

Berikut adalah ringkasan endpoint utama yang disediakan oleh backend:

### Autentikasi (`/api/auth`)
| Method | Endpoint | Akses | Deskripsi |
|---|---|---|---|
| POST | `/api/auth/register` | Publik | Registrasi pengguna baru sebagai Buyer atau Seller |
| POST | `/api/auth/login` | Publik | Autentikasi pengguna dan penerbitan token JWT |

### Statistik Landing (`/api/landing`)
| Method | Endpoint | Akses | Deskripsi |
|---|---|---|---|
| GET | `/api/landing/stats` | Publik | Statistik jumlah produk aktif, toko aktif, dan pembeli |

### Produk Publik & Katalog (`/api/products`)
| Method | Endpoint | Akses | Deskripsi |
|---|---|---|---|
| GET | `/api/products` | Publik / Buyer | Mengambil katalog produk dengan filter pencarian dan kategori |
| GET | `/api/products/{id}` | Publik | Mengambil detail produk lengkap beserta rating dan ulasan |
| POST | `/api/products` | Seller | Menambahkan produk baru ke toko |
| PUT | `/api/products/{productId}` | Seller | Mengubah data produk milik toko |
| DELETE | `/api/products/{productId}` | Seller | Menghapus produk dari toko |

### Produk Khusus Penjual (`/api/seller/products`)
| Method | Endpoint | Akses | Deskripsi |
|---|---|---|---|
| GET | `/api/seller/products` | Seller | Mengambil daftar produk milik toko penjual yang login |
| GET | `/api/seller/products/{id}` | Seller | Mengambil detail produk penjual berdasarkan ID |

### Toko Penjual (`/api/seller/stores` & `/api/stores`)
| Method | Endpoint | Akses | Deskripsi |
|---|---|---|---|
| GET | `/api/seller/stores` | Seller | Mengambil daftar toko yang dimiliki oleh penjual yang login |
| POST | `/api/stores` | Terotentikasi | Mendaftarkan toko baru |

### Kategori (`/api/categories`)
| Method | Endpoint | Akses | Deskripsi |
|---|---|---|---|
| GET | `/api/categories` | Publik | Mengambil seluruh daftar kategori produk |

### Keranjang Belanja (`/api/carts`)
| Method | Endpoint | Akses | Deskripsi |
|---|---|---|---|
| GET | `/api/carts` | Terotentikasi | Mengambil seluruh item keranjang pengguna yang login |
| POST | `/api/carts/items` | Terotentikasi | Menambahkan produk ke keranjang belanja |
| PUT | `/api/carts/items/{cartId}` | Terotentikasi | Memperbarui jumlah item dalam keranjang |
| DELETE | `/api/carts/items/{cartId}` | Terotentikasi | Menghapus item dari keranjang |

### Alamat Pengiriman (`/api/address`)
| Method | Endpoint | Akses | Deskripsi |
|---|---|---|---|
| GET | `/api/address` | Terotentikasi | Mengambil daftar alamat pengiriman pengguna |
| GET | `/api/address/{addressId}` | Terotentikasi | Mengambil detail satu alamat |
| POST | `/api/address` | Terotentikasi | Menambahkan alamat baru |
| PUT | `/api/address/{addressId}` | Terotentikasi | Mengubah data alamat yang sudah ada |
| DELETE | `/api/address/{addressId}` | Terotentikasi | Menghapus alamat pengiriman |

### Voucher Toko (`/api/stores/{storeId}/vouchers`)
| Method | Endpoint | Akses | Deskripsi |
|---|---|---|---|
| GET | `/api/stores/{storeId}/vouchers` | Buyer, Seller | Mengambil daftar voucher toko yang tersedia |
| POST | `/api/stores/{storeId}/vouchers` | Seller | Membuat voucher diskon baru untuk toko |
| PATCH | `/api/stores/{storeId}/vouchers/{voucherId}` | Seller | Mengaktifkan atau menonaktifkan voucher |
| DELETE | `/api/stores/{storeId}/vouchers/{voucherId}` | Seller | Menghapus voucher toko |

### Transaksi Pesanan (`/api/orders`)
| Method | Endpoint | Akses | Deskripsi |
|---|---|---|---|
| POST | `/api/orders` | Buyer | Melakukan checkout pesanan baru |
| GET | `/api/orders` | Buyer, Seller | Mengambil riwayat pesanan (disaring berdasarkan peran pengguna) |
| GET | `/api/orders/{orderId}` | Buyer, Seller | Mengambil detail lengkap pesanan |
| PATCH | `/api/orders/{orderId}` | Buyer, Seller | Memperbarui status pesanan (PENDING_PAYMENT, ON_PROCESS, ON_DELIVERY, DELIVERED) |

### Ulasan Produk (`/api/products/{productId}/reviews`)
| Method | Endpoint | Akses | Deskripsi |
|---|---|---|---|
| GET | `/api/products/{productId}/reviews` | Publik | Mengambil seluruh ulasan untuk produk tertentu |
| POST | `/api/products/{productId}/reviews` | Terotentikasi | Mengirimkan ulasan dan rating produk dari pesanan selesai |

---

## Panduan Migrasi Database dan Troubleshooting

### 1. Masalah Kolom Baru Tidak Ditemukan (Contoh: `42703: column r.OrderId does not exist`)
Penyebab: Properti baru telah ditambahkan pada kelas model C# (misalnya penambahan `OrderId` pada entitas `Review`), namun berkas migrasi database belum dibuat atau belum diterapkan ke PostgreSQL di perangkat yang bersangkutan.

Solusi:
Jalankan perintah berikut di direktori `backend/marketplace-AdIns-BE`:
```bash
dotnet ef migrations add AddOrderIdToReview
dotnet ef database update
```
Perintah ini akan mendeteksi perbedaan antara model C# dan snapshot database, lalu membuatkan skrip SQL ALTER TABLE secara otomatis.

### 2. Masalah Sertifikat SSL Lokal (HTTPS Untrusted)
Jika browser menolak koneksi ke API `https://localhost:7153` dengan peringatan sertifikat SSL tidak tepercaya:
Jalankan perintah perbaikan sertifikat pengembang .NET:
```bash
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

### 3. Masalah CORS (Cross-Origin Resource Sharing)
Backend telah dikonfigurasi untuk menerima permintaan dari `http://localhost:4200` melalui policy `AllowAngular`. Jika frontend dijalankan pada port selain 4200, sesuaikan konfigurasi CORS pada `Program.cs`:
```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("AllowAngular", policy =>
    {
        policy.WithOrigins("http://localhost:4200", "http://localhost:<PORT_LAIN>")
              .AllowAnyHeader()
              .AllowAnyMethod();
    });
});
```

---

## Alur Kerja Transaksi E-Commerce

Berikut tahapan transaksi tipikal pada platform WarungAdins:

1. Pembeli membuka katalog produk, memilih barang, menentukan kuantitas, dan menekan tombol Tambah ke Keranjang atau Beli Sekarang.
2. Pembeli masuk ke halaman Keranjang Belanja, memilih produk yang ingin dicheckout dari toko yang sama.
3. Pembeli dialihkan ke halaman Checkout, memilih alamat pengiriman, dan memilih voucher toko yang memenuhi syarat.
4. Pembeli mengonfirmasi pesanan (status pesanan awal: `PENDING_PAYMENT`).
5. Penjual membuka portal dashboard penjual, memeriksa daftar pesanan masuk, dan mengubah status pesanan menjadi `ON_PROCESS` lalu `ON_DELIVERY` saat paket diserahkan ke jasa kirim.
6. Saat barang telah sampai ke pembeli, status pesanan diperbarui menjadi `DELIVERED`.
7. Pembeli membuka halaman Pesanan Saya, melihat tombol ulasan aktif pada pesanan yang sudah selesai, lalu memberikan rating bintang dan ulasan untuk produk terkait.

---

## Kontribusi dan Pemeliharaan

- Selalu buat branch fitur baru sebelum melakukan perubahan besar.
- Pastikan kode frontend lulus kompilasi:
  ```bash
  npm run build
  ```
- Pastikan kode backend berhasil dibangun tanpa galat:
  ```bash
  dotnet build
  ```
- Buat migrasi Entity Framework Core baru setiap kali ada perubahan definisi properti atau relasi entitas pada folder `Models`.
