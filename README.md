[README (2).md](https://github.com/user-attachments/files/33955886/README.2.md)
# MICHIA — Panduan Menjalankan (Backend + Frontend)

MICHIA = *Michi* (道, "jalan") + AI. Aplikasi personal development journal untuk mahasiswa:
**Live. Journal. Understand. Grow.** Prinsip utamanya: **AI Suggests. User Decides.**

Panduan ini membawamu dari nol sampai web bisa dipakai di laptop sendiri.

---

## 1. Struktur folder

Buat satu folder induk, misalnya `michia-app`, lalu susun seperti ini:

```
michia-app/
├── db/
│   └── schema.sql        (dari michia-backend.zip)
├── backend/              (dari michia-backend.zip, FastAPI)
└── frontend/             (dibuat dengan create-next-app, isinya dari michia-frontend.zip)
```

## 2. Yang perlu dipasang dulu

| Alat | Versi | Cek di terminal |
|---|---|---|
| Node.js | LTS (20 atau lebih baru) | `node -v` |
| Python | 3.10 atau lebih baru | `python --version` |
| PostgreSQL | 14 atau lebih baru | `psql --version` |
| VS Code | bebas | |

Catatan PostgreSQL di Windows: saat instalasi kamu diminta membuat **password untuk user `postgres`**. Catat, nanti dipakai di file `.env`.
Kalau perintah `psql` tidak dikenali, buka **SQL Shell (psql)** dari Start Menu, atau tambahkan folder `bin` PostgreSQL ke PATH.

---

## 3. Siapkan database

Buka terminal di folder `michia-app`, lalu:

```bash
psql -U postgres -c "CREATE DATABASE michia;"
psql -U postgres -d michia -f db/schema.sql
```

Kalau berhasil, akan muncul banyak baris `CREATE TYPE` dan `CREATE TABLE` tanpa error.

---

## 4. Jalankan backend (FastAPI)

```bash
cd backend
python -m venv .venv
```

Aktifkan virtual environment:

```bash
# Windows (CMD)
.venv\Scripts\activate
# Windows (PowerShell). Kalau diblokir, jalankan dulu: Set-ExecutionPolicy -Scope Process Bypass
.venv\Scripts\Activate.ps1
# Mac / Linux
source .venv/bin/activate
```

Pasang paket dan siapkan file `.env`:

```bash
pip install -r requirements.txt
copy .env.example .env        # Mac/Linux: cp .env.example .env
```

Buka `backend/.env` di VS Code, lalu isi:

- `DATABASE_URL` → ganti `postgres:postgres` menjadi `postgres:PASSWORDMU`
- `SECRET_KEY` → isi string acak panjang. Buat dengan:
  `python -c "import secrets; print(secrets.token_urlsafe(48))"`

Jalankan server:

```bash
uvicorn app.main:app --reload
```

Cek di browser:
- http://localhost:8000/health → harus tampil `{"status":"ok"}`
- http://localhost:8000/docs → dokumentasi API interaktif

**Biarkan terminal ini tetap terbuka** selama kamu memakai aplikasi.

---

## 5. Jalankan frontend (Next.js)

Buka terminal **baru** di folder `michia-app`:

```bash
npx create-next-app@latest frontend --ts --app --src-dir --no-tailwind --import-alias "@/*"
```

Tekan Enter untuk pertanyaan yang muncul (pakai pilihan default). Lalu:

1. Ekstrak `michia-frontend.zip`.
2. Salin isi folder `michia/src` dari zip ke `frontend/src`, pilih **Replace / timpa** kalau ditanya.
3. Salin `.env.local.example` ke `frontend/.env.local` (sejajar dengan `package.json`).

```bash
cd frontend
npm run dev
```

Buka http://localhost:3000. Kamu akan langsung diarahkan ke halaman login.

---

## 6. Alur pemakaian dari awal (pengguna baru)

**Langkah 1 — Daftar akun.** Di halaman login tekan **"Belum punya akun?"**, isi email, password (minimal 8 karakter), dan nama panggilan (opsional), lalu **Daftar**. Setelah itu kamu otomatis masuk.

**Langkah 2 — Onboarding (6 layar, hanya sekali).**
1. *Welcome*: pengenalan MICHIA.
2. *Kenalan*: isi nama panggilan.
3. *Apa yang ingin kamu kembangkan?*: pilih satu atau lebih area (Programming, Embedded System, Akademik, dst.).
4. *Buat goal*: nama goal terisi otomatis dari pilihanmu dan boleh diedit. Isi **Why** (kenapa penting) dan tentukan **prioritas** High / Medium / Low.
5. *Habit (opsional)*: misalnya "Coding 1 jam", 4 kali per minggu. Boleh dikosongkan.
6. *Preferensi AI*: **OFF**, **ASK ME**, atau **AUTOMATIC INSIGHT**. Bisa diubah kapan saja di Profile.

**Langkah 3 — Home (dashboard).** Menampilkan sapaan, tombol **+ Write Journal**, progress goal prioritas tinggi, habit hari ini, dan Today's Insight.

**Langkah 4 — Lengkapi goal.** Buka menu **Goals**, tekan **Edit** pada goal, lalu isi **milestone** (satu per baris). Progress goal dihitung dari milestone yang kamu centang, jadi goal tanpa milestone akan tetap 0%.

**Langkah 5 — Tulis jurnal pertama.** Buka **Journal**, tulis bebas (tidak harus tiap hari, tidak ada template). Boleh pilih chip **Life Context** (Mood, Tidur & kesehatan, Keuangan, Sosial, Karier, Kuliah) kalau ada yang memengaruhi harimu. Tekan **Simpan**.

**Langkah 6 — Tanggapi saran AI** (jika mode AI aktif). Muncul kartu "Aku menemukan sesuatu di jurnalmu" berisi aktivitas, hubungan ke goal, dan persen keyakinan. Tekan **Why?** untuk melihat alasannya, lalu pilih:
- **Confirm**: aktivitas langsung tersimpan sebagai terkonfirmasi.
- **Edit**: dikirim ke halaman Activities dengan status "Perlu konfirmasi" untuk kamu koreksi.
- **Ignore**: tidak disimpan.

Pada mode ASK AI, kartu ini baru muncul setelah kamu menekan **Analyze with AI**.

**Langkah 7 — Activities.** Lihat semua aktivitas (manual dan hasil AI), ubah goal yang terhubung lewat dropdown, **Confirm / Ignore** saran yang tertunda, atau catat aktivitas manual.

**Langkah 8 — Habits.** Centang habit tiap kali dikerjakan. Di sebelah nama habit terlihat berapa kali sudah dikerjakan minggu ini dibanding targetnya.

**Langkah 9 — Insights & Weekly Reflection.** Setelah ada jurnal dalam 7 hari terakhir, halaman **Insights** menampilkan statistik minggu ini dan insight dengan format Observation → Pattern → Impact → Suggestion → Reflection. Di bawahnya ada kolom **"Menurutmu minggu ini bagaimana?"** untuk refleksi tertulis.

**Langkah 10 — Profile.** Ganti nama, pilih **tema warna** (Rose, Ocean, Lime Sun, Sky Sand, Burgundy), ubah mode AI, ulangi onboarding, **Keluar**, atau **Hapus akun** (menghapus semua datamu dari database).

**Urutan ringkas untuk pemakaian harian:** tulis jurnal → tanggapi saran AI → centang habit → sesekali cek Goals dan Insights → tutup minggu dengan Weekly Reflection.

---

## 7. Menjalankan tes backend

```bash
cd backend
pytest
```

Tes memakai SQLite sementara, jadi tidak menyentuh database PostgreSQL-mu.

## 8. Build versi produksi (lokal)

```bash
# frontend
cd frontend
npm run build
npm start
```

Untuk deploy sungguhan: pakai `SECRET_KEY` baru yang kuat, ubah `CORS_ORIGINS` di backend ke alamat frontend-mu, ubah `NEXT_PUBLIC_API_URL` ke alamat backend, dan layani keduanya lewat HTTPS.

---

## 9. Masalah yang sering muncul

| Gejala | Penyebab dan solusi |
|---|---|
| Login menampilkan "Tidak bisa terhubung ke server" | Backend belum jalan atau port salah. Pastikan `uvicorn` aktif dan `http://localhost:8000/health` bisa dibuka. |
| Backend error `Field required ... secret_key` | `.env` belum dibuat atau `SECRET_KEY` kosong. |
| `password authentication failed for user "postgres"` | Password di `DATABASE_URL` salah. |
| `relation "users" does not exist` | `schema.sql` belum dijalankan ke database `michia`. |
| `type "priority_level" already exists` | `schema.sql` sudah pernah dijalankan. Hapus database (`DROP DATABASE michia;`), buat ulang, jalankan lagi. |
| `psql` tidak dikenali | Pakai SQL Shell (psql) atau tambahkan folder `bin` PostgreSQL ke PATH. |
| Browser menolak request (error CORS) | Pastikan frontend berjalan di `http://localhost:3000`, sesuai `CORS_ORIGINS` di `.env` backend. Restart backend setelah mengubah `.env`. |
| Port 3000 sudah dipakai | Next.js akan menawarkan port lain. Kalau memakai port selain 3000, tambahkan alamatnya ke `CORS_ORIGINS`. |
| Setelah update kode, tampilan lama masih muncul | Hentikan `npm run dev` (Ctrl+C), jalankan lagi, lalu refresh dengan Ctrl+Shift+R. |

---

## 10. Batasan versi saat ini (jujur)

- **Activities dan Weekly Reflection** masih disimpan di browser (localStorage) per akun, belum di database. Datanya tidak ikut berpindah antar perangkat.
- **"AI" masih simulasi berbasis kata kunci**, belum memakai LLM sungguhan. Penghubungan aktivitas ke goal hanya otomatis kalau nama goal sama persis dengan nama bawaan onboarding (misalnya "Menguasai Embedded System" atau "Lebih mahir programming"); kalau goal diganti namanya, hubungkan manual lewat halaman Activities.
- Belum ada: reset password, verifikasi email, refresh token (token berlaku 24 jam, setelah itu login lagi), dan reminder/notifikasi.
ilihanmu).
