# TEKS PRESENTASI — SISTEM INFORMASI PERPUSTAKAAN DIGITAL

Dokumen ini berisi teks siap tempel untuk setiap slide PPT.
Proyek terdiri dari **Frontend (FE)** dan **Backend (BE)** yang terpisah.

---

## Slide 1 — Judul

**Sistem Informasi Perpustakaan Digital**
Aplikasi Manajemen & Peminjaman Buku Berbasis Web

- Frontend : Vue.js 3 (SPA)
- Backend  : Node.js + Express REST API
- (Nama / Kelas / Tanggal presentasi)

---

## Slide 2 — Latar Belakang

- Pengelolaan perpustakaan manual (buku besar/kertas) rawan hilang, lambat, dan sulit direkap.
- Siswa/anggota kesulitan mengecek ketersediaan buku dan riwayat peminjaman.
- Dibutuhkan sistem digital yang dapat diakses online (peminjaman online) maupun offline (datang langsung).
- Perpustakaan juga ingin menyediakan **buku digital (PDF)** yang bisa dibaca langsung dari aplikasi.

---

## Slide 3 — Tujuan Sistem

- Mendigitalkan katalog buku beserta stok, harga, dan cover.
- Memudahkan proses peminjaman, perpanjangan, dan pengembalian buku.
- Menyediakan pembacaan buku digital (PDF reader) di dalam aplikasi.
- Memisahkan hak akses **Admin** dan **User/Anggota** secara aman.
- Menghitung denda keterlambatan secara otomatis.

---

## Slide 4 — Arsitektur Sistem

Arsitektur **Client–Server** dengan pemisahan FE dan BE:

```
[ Browser / User ]
        │  (HTTP + JSON)
        ▼
[ Frontend Vue.js 3 SPA ]  ──►  axios  ──►  [ Backend Express REST API ]
                                                      │
                                                      ▼
                                              [ Database MySQL ]
                                              [ Penyimpanan File: /uploads ]
```

- Komunikasi FE ↔ BE lewat REST API (format JSON).
- Autentikasi menggunakan **JWT (JSON Web Token)**.
- File cover & PDF buku disimpan di server dan diakses via URL statis `/uploads`.

---

## Slide 5 — Teknologi Frontend (FE)

**Folder: `perpustakaan`**

| Komponen | Teknologi |
|---|---|
| Framework | Vue.js 3 |
| Build tool | Vite |
| Routing | Vue Router |
| State Management | Pinia |
| HTTP Client | Axios |
| Styling | Tailwind CSS 4 |
| PDF Reader | pdfjs-dist |
| Notifikasi/Alert | SweetAlert2 |

Aplikasi berbentuk **SPA (Single Page Application)**.

---

## Slide 6 — Teknologi Backend (BE)

**Folder: `Perpustakaan_BE`**

| Komponen | Teknologi |
|---|---|
| Runtime | Node.js |
| Framework | Express 5 |
| Database | MySQL (driver mysql2) |
| Query Builder / Migrasi | Knex.js |
| Autentikasi | JSON Web Token (jsonwebtoken) |
| Enkripsi Password | bcrypt |
| Upload File | Multer |
| Kirim Email | Nodemailer |
| ID Unik | UUID |
| CORS | cors |

Pola arsitektur : **Routes → Controller → Service → Model** (berlapis/layered).

---

## Slide 7 — Struktur Folder Backend

```
Perpustakaan_BE/
├── src/
│   ├── routes/        → definisi endpoint (auth, buku, siswa, kelas, peminjaman, user)
│   ├── controllers/   → menangani request & response
│   ├── services/      → logika bisnis
│   ├── models/        → akses data ke database (Knex)
│   ├── middlewares/   → auth (JWT), upload (Multer), cors
│   ├── utils/         → mailer, asyncHandler
│   └── config/        → koneksi database
├── migrations/        → struktur tabel database
├── seeds/             → data awal (admin, buku, kelas, siswa)
└── public/uploads/    → file cover & PDF buku
```

---

## Slide 8 — Struktur Folder Frontend

```
perpustakaan/
└── src/
    ├── views/         → halaman (Login, Dashboard, Katalog, Detail, dll)
    ├── components/    → AppLayout, Sidebar, BookCard, Icon
    ├── stores/        → Pinia (authStore, bookStore, loanStore)
    ├── router/        → konfigurasi routing & proteksi halaman
    └── assets/        → gambar & aset statis
```

---

## Slide 9 — Struktur Database

**Tabel utama:**

- **users** — id, username, password (bcrypt), role (admin/user), name, email, no_telp
- **buku** — id, nama_buku, harga_buku, jenis_buku, penulis, deskripsi, foto_buku, pdf_buku, stok
- **kelas** — data kelas
- **siswa** — data siswa (terhubung ke kelas)
- **peminjaman** — id, siswa_id, buku_id, tanggal_pinjam, tanggal_kembali, tanggal_dikembalikan, status, jumlah_perpanjangan, denda

Semua tabel memakai **UUID** sebagai primary key dan **soft delete** (`deleted_at`).

---

## Slide 10 — Fitur Autentikasi

- **Login** & **Register** anggota.
- **Lupa Username** & **Lupa Password** (reset via email — Nodemailer).
- Password dienkripsi dengan **bcrypt**.
- Sesi login diamankan dengan **JWT**.
- Proteksi halaman: hanya user login yang bisa masuk; halaman `/admin` hanya untuk role **admin**.

Endpoint: `POST /auth/login`, `/auth/register`, `/auth/forgot-username`, `/auth/forgot-password`, `/auth/reset-password`, `/auth/logout`

---

## Slide 11 — Fitur Manajemen Buku (Admin)

- Tambah, ubah, hapus data buku (CRUD).
- Upload **cover buku** dan **file PDF** buku.
- Kelola stok buku.
- Soft delete & hard delete (hapus permanen).

Endpoint utama:
- `GET /buku` , `GET /buku/:id` (publik)
- `POST /buku` , `PUT /buku/:id` , `DELETE /buku/:id` (khusus admin)
- `POST /buku/:id/pdf` , `POST /buku/:id/foto` (upload file)

---

## Slide 12 — Fitur Peminjaman Buku

**Peminjaman Online (oleh anggota):**
- Anggota mengajukan pinjam → status awal **"menunggu"**.
- Admin melakukan **approve** atau **reject** pengajuan.

**Peminjaman Offline (oleh admin):**
- Admin mencatat peminjaman anggota yang datang langsung.

**Proses lain:**
- **Perpanjang** masa pinjam.
- **Pengembalian** buku.
- **Denda otomatis** keterlambatan (Rp 2.000/hari).

Endpoint: `POST /peminjaman`, `POST /peminjaman/approve/:id`, `PUT /peminjaman/:id/perpanjang`, `PUT /peminjaman/:id/return`

---

## Slide 13 — Fitur Frontend (Halaman)

| Halaman | Fungsi |
|---|---|
| Login / Register | Masuk & daftar akun |
| Dashboard | Ringkasan informasi |
| Katalog (Catalog) | Daftar buku yang tersedia |
| Detail Buku | Informasi lengkap 1 buku |
| PDF Reader | Membaca buku digital dalam aplikasi |
| Riwayat Peminjaman | Daftar buku yang dipinjam anggota |
| Upgrade | Peningkatan keanggotaan/membership |
| Admin Books | Kelola data buku (khusus admin) |

---

## Slide 14 — Alur Peminjaman Online (Flow)

1. Anggota login → membuka **Katalog**.
2. Memilih buku → melihat **Detail Buku**.
3. Mengajukan **peminjaman** (status: menunggu).
4. **Admin** menyetujui (approve) → status: dipinjam.
5. Anggota membaca buku / mengambil buku.
6. Sebelum jatuh tempo → bisa **perpanjang**.
7. **Pengembalian** → jika telat, sistem menghitung **denda** otomatis.

---

## Slide 15 — Keamanan Sistem

- Password di-hash memakai **bcrypt** (tidak disimpan sebagai teks asli).
- Autentikasi & otorisasi dengan **JWT** + middleware `authenticateToken`.
- Middleware `authorizeAdmin` membatasi aksi sensitif hanya untuk admin.
- **CORS** dikonfigurasi untuk keamanan komunikasi FE–BE.
- Validasi tipe & ukuran file saat upload (Multer).

---

## Slide 16 — Kelebihan Sistem

- Arsitektur FE–BE terpisah → mudah dikembangkan & di-maintain.
- Mendukung peminjaman **online maupun offline**.
- Ada **buku digital (PDF)** yang bisa dibaca langsung.
- Perhitungan denda otomatis.
- Hak akses bertingkat (admin & anggota).
- Tampilan modern & responsif (Tailwind CSS).

---

## Slide 17 — Pengembangan Selanjutnya (Saran)

- Notifikasi otomatis jatuh tempo via email/WhatsApp.
- Laporan & statistik peminjaman (dashboard analitik).
- Pembayaran denda online.
- Fitur pencarian & filter buku lebih lengkap.
- Aplikasi versi mobile.

---

## Slide 18 — Penutup

**Kesimpulan:**
Sistem Informasi Perpustakaan Digital berhasil mengintegrasikan pengelolaan buku, anggota, dan peminjaman dalam satu platform web modern dengan pemisahan Frontend (Vue.js) dan Backend (Express REST API).

**Terima Kasih**
(Sesi Tanya Jawab)
