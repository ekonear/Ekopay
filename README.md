# EKOPAY — PWA Bank Permainan (dengan Login)

Progressive Web App: bankir membuat akun untuk tiap pemain, pemain login ke rekening masing-masing, dan bisa bayar lewat scan QR dengan nominal yang dikunci oleh bankir.

## Langkah 1 — Buat Realtime Database (kalau belum)

1. Buka https://console.firebase.google.com, buat project (gratis, tanpa kartu kredit).
2. Sidebar kiri → **Databases & Storage** → **Realtime Database** → **Create Database**.
3. Pilih lokasi, klik **Next** → pilih **Start in test mode** → **Enable**.
4. Salin **Database URL** yang muncul di bagian atas halaman.

## Langkah 2 — Aktifkan Login (Authentication)

1. Sidebar kiri → cari **Authentication** (biasanya di bawah "Build" atau langsung ada menunya) → **Get Started**.
2. Tab **Sign-in method** → pilih **Email/Password** → aktifkan toggle **Enable** → **Save**.
   (Aplikasi memakai username biasa, bukan email asli — di belakang layar cuma diubah jadi format `username@ekopay.local` supaya cocok dengan sistem login Firebase.)

## Langkah 3 — Ambil Web API Key

1. Klik ⚙️ (Project Settings) di sidebar kiri atas → **Project settings**.
2. Tab **General**, scroll ke bawah ke bagian **Your apps** / atau langsung terlihat **Web API Key** di halaman itu.
3. Salin nilainya.

## Langkah 4 — Pasang Security Rules

1. Sidebar kiri → **Realtime Database** → tab **Rules**.
2. Hapus semua isi kotak rules, ganti dengan isi file **`database.rules.json`** yang ada di folder ini.
3. Klik **Publish**.

Aturan ini memastikan: hanya akun yang login yang bisa baca/tulis data, hanya bankir yang bisa membuat akun pemain baru & QR pembayaran, dan tiap pemain hanya bisa menulis ke data akunnya sendiri.

## Langkah 5 — Hubungkan ke aplikasi

Buka `config.js`, isi dua baris ini dengan nilai dari Langkah 1 & 3:
```js
const FIREBASE_URL = "https://xxx-default-rtdb.asia-southeast1.firebasedatabase.app";
const FIREBASE_API_KEY = "AIzaSy...";
```

## Langkah 6 — Deploy

Sama seperti sebelumnya — upload semua file di folder ini (index.html, manifest.json, service-worker.js, config.js, database.rules.json, icons/) ke GitHub Pages atau Netlify. `database.rules.json` boleh ikut ter-upload, tidak masalah (cuma referensi, tidak dipakai runtime).

## Cara Main

1. **Buka aplikasi pertama kali** → karena belum ada bankir, akan muncul layar **Setup Awal** → isi nama, username, password untuk akun bankirmu sendiri → otomatis masuk ke dashboard Bankir.
2. **Bankir**: tab Aksi → *Tambah Akun Pemain* → isi nama, username, password untuk tiap pemain → beri tahu mereka username & password itu.
3. **Bankir**: tab Aksi → *Tambah Dana ke Bank* untuk mengisi kas, lalu tab Pemain → *Isi* untuk top-up saldo tiap pemain.
4. **Bankir**: tab QR Bayar → *Buat QR Pembayaran Baru* → isi nominal & label (misal "Denda melanggar aturan") → QR muncul, tunjukkan ke pemain yang harus bayar.
5. **Pemain**: buka aplikasi → login dengan username & password dari bankir → tab Transfer → scan QR (bisa QR pemain lain, QR bank, atau QR pembayaran bankir) → nominal otomatis terkunci untuk QR pembayaran → konfirmasi.
6. Tombol **Reset Permainan** (di tab Aksi bankir) menghapus semua akun termasuk akun bankir — perlu setup ulang dari Langkah pertama.

## Soal keamanan

- Pemain **tidak punya fitur apa pun di dalam aplikasi** untuk mengubah saldo sendiri — saldo hanya berubah lewat top-up bankir, transfer sah, atau bayar QR.
- Hanya akun yang dibuat bankir yang bisa login; tidak ada pendaftaran akun sendiri di aplikasi.
- Security Rules mewajibkan login untuk semua akses data, dan membatasi siapa boleh menulis ke mana.
- Catatan realistis: siapa pun yang cukup teknis (misalnya membuka DevTools browser dan mengambil token login mereka sendiri) secara teori masih bisa mencoba mengirim permintaan langsung ke database menggunakan akun mereka sendiri. Untuk menutup celah itu 100%, dibutuhkan server tepercaya (Cloud Function) yang memvalidasi setiap transaksi — di luar cakupan setup gratis ini. Untuk permainan santai, proteksi saat ini sudah cukup memadai.
