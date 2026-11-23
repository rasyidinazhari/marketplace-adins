# WarungAdins - Frontend Web Application

Aplikasi antarmuka web (Single Page Application) untuk platform e-commerce WarungAdins. Dibangun menggunakan Angular 17 dengan Standalone Components dan Tailwind CSS.

Dokumentasi lengkap mengenai arsitektur sistem, alur modul pembeli dan penjual, serta panduan instalasi dapat dilihat pada file dokumentasi utama di root proyek: [README.md](../README.md).

## Menjalankan Frontend Secara Mandiri

1. Pasang paket dependensi:
   ```bash
   npm install
   ```
2. Pastikan konfigurasi URL API backend di `src/environments/environment.ts` sesuai:
   ```typescript
   export const environment = {
     production: false,
     apiUrl: 'https://localhost:7153/api',
   };
   ```
3. Jalankan server pengembangan:
   ```bash
   npm start
   ```
4. Buka peramban di `http://localhost:4200`.

## Perintah Tambahan

- Membangun aplikasi produksi:
  ```bash
  npm run build
  ```
- Menjalankan pengujian unit:
  ```bash
  npm test
  ```
