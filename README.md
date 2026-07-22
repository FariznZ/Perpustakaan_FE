# 📚 Perpustakaan Digital — Frontend

Aplikasi web perpustakaan digital: katalog buku, peminjaman, baca PDF langsung di browser, membership (free/premium), dan panel admin untuk kelola buku.

Dibangun dengan **Vue 3 + Vite**, styling **Tailwind CSS 4**, state management **Pinia**.

## Yang Harus Di-install Dulu (Prasyarat)

Sebelum menjalankan projek ini, pastikan sudah ter-install di komputer:

### 1. Node.js (wajib)

npm sudah otomatis ikut ter-install bersama Node.js, jadi tidak perlu install npm terpisah.

- Download dari: https://nodejs.org (pilih versi **LTS**)
- Minimal versi Node.js **20.19+** atau **22.12+** (syarat Vite 8)
- Projek ini dikembangkan dengan Node.js v24

Cek apakah sudah ter-install, buka terminal / CMD lalu ketik:

```bash
node --version   # harus muncul, contoh: v24.18.0
npm --version    # harus muncul, contoh: 11.16.0
```

Kalau muncul "command not found" / "tidak dikenali", berarti belum ter-install → install Node.js dulu.

### 2. Git (opsional)

Hanya perlu kalau mau clone projek dari repository. Download: https://git-scm.com

### 3. Backend API (wajib supaya bisa login & lihat data)

Frontend ini mengambil data dari server backend (REST API) di port **3000**. Tanpa backend berjalan, halaman login akan menampilkan **"Server API Offline"**.

Alamat API di-set di 3 file berikut (ubah sesuai alamat server backend kamu):

- `src/stores/authStore.js` → baris `const API_URL = 'http://192.168.1.7:3000'`
- `src/stores/bookStore.js` → sama
- `src/stores/loanStore.js` → sama

> Kalau backend jalan di komputer yang sama, ganti jadi `http://localhost:3000`.

## Cara Install & Menjalankan

Semua perintah dijalankan dari folder projek ini.

### Langkah 1 — Masuk ke folder projek

```bash
cd "C:\Users\FX\Documents\perpustakaan"
```

### Langkah 2 — Install semua dependency

```bash
npm install
```

Perintah ini akan otomatis mengunduh semua library yang dibutuhkan (Vue, Vite, Tailwind, dll.) ke folder `node_modules`. Cukup dijalankan **sekali di awal** (atau lagi setiap kali `package.json` berubah).

### Langkah 3 — Jalankan mode development

```bash
npm run dev
```

Buka browser ke alamat yang muncul di terminal (biasanya **http://localhost:5173**). Setiap perubahan kode akan langsung ter-refresh otomatis (hot reload).

### Build untuk production (opsional)

```bash
npm run build     # hasil build masuk ke folder dist/
npm run preview   # tes hasil build secara lokal
```

## Teknologi & Library yang Dipakai

Semua library di bawah **tidak perlu di-install manual satu-satu** — sudah tercantum di `package.json` dan otomatis ter-install lewat `npm install`.

| Library | Kegunaan |
|---|---|
| Vue 3 | Framework utama frontend |
| Vite 8 | Build tool & dev server |
| Vue Router 5 | Navigasi antar halaman |
| Pinia 3 | State management (auth, buku, peminjaman) |
| Tailwind CSS 4 | Styling |
| Axios | HTTP request ke API |
| pdfjs-dist | Menampilkan/baca file PDF buku di browser |
| SweetAlert2 | Popup notifikasi & konfirmasi |

## Struktur Folder Singkat

```
src/
├── components/   # Komponen UI (layout, sidebar, kartu buku, ikon)
├── views/        # Halaman (login, register, dashboard, katalog,
│                 #   detail buku, baca PDF, riwayat pinjam,
│                 #   upgrade membership, admin kelola buku)
├── router/       # Definisi rute halaman
├── stores/       # Pinia store: authStore, bookStore, loanStore
├── data/         # Data mock / konstanta
└── assets/       # Gambar & aset statis
```

## Halaman yang Tersedia

- `/login`, `/register`, `/forgot-username`, `/forgot-password` — autentikasi
- `/dashboard` — beranda setelah login
- `/catalog` — katalog buku, `/book/:id` — detail buku
- `/read/:id` — baca buku PDF
- `/history` — riwayat peminjaman
- `/upgrade` — upgrade membership premium
- `/admin/books` — kelola buku (khusus admin)

## Masalah Umum (Troubleshooting)

| Masalah | Solusi |
|---|---|
| `'npm' is not recognized...` | Node.js belum ter-install → install dari nodejs.org, lalu tutup & buka lagi terminal |
| Login muncul "Server API Offline" | Backend belum jalan, atau `API_URL` di `src/stores/*.js` tidak sesuai alamat backend |
| `npm install` gagal / error jaringan | Cek koneksi internet, lalu coba lagi. Bisa juga hapus folder `node_modules` dan `package-lock.json`, lalu `npm install` ulang |
| Error saat `npm run dev` soal versi Node | Update Node.js ke versi 20.19+ / 22.12+ (LTS terbaru) |
| Port 5173 sudah terpakai | Vite otomatis pindah ke port berikutnya (5174, dst.) — lihat alamat di terminal |
