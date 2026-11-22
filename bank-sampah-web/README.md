# Bank Sampah Digital — Tugas 3

Implementasi sistem informasi bank sampah berbasis web: Next.js 16 (App Router,
TypeScript, Tailwind CSS 4) + PostgreSQL (Neon) dengan driver `pg`.

## Fitur utama

1. **Dashboard** — KPI operasional, tren setoran 6 bulan, komposisi kategori.
2. **Manajemen nasabah** — daftar, pencarian, tambah, ubah, hapus (dilindungi FK).
3. **Master jenis sampah & harga** — kategori, harga per kg, riwayat snapshot.
4. **Setoran sampah** — banyak jenis per transaksi, harga snapshot, total otomatis.
5. **Penarikan saldo** — validasi saldo dua lapis (aplikasi + trigger database).
6. **Laporan** — filter periode, rekap kategori, nasabah teraktif, kinerja petugas, ekspor CSV.

## Menjalankan lokal

```bash
cd bank-sampah-web
npm install
cp .env.example .env.local      # lalu isi DATABASE_URL
npm run db:init                 # membuat tabel + data awal (sekali saja)
npm run dev                     # http://localhost:3000
```

Butuh Node.js 22.x (lihat `engines` di `package.json`).

## Skrip

| Perintah | Keterangan |
|---|---|
| `npm run dev` | Server pengembangan |
| `npm run build` | Build produksi |
| `npm run start` | Menjalankan hasil build |
| `npm run db:init` | Reset schema + seed data (**menghapus data lama**) |
| `npm run test:e2e` | Uji end-to-end Playwright terhadap URL di `BASE_URL` |

## Deploy ke Vercel

> **Penting:** aplikasi Next.js ada di subfolder `bank-sampah-web`, bukan di root repo.
> Saat import project di Vercel, wajib set **Root Directory = `bank-sampah-web`**.
> Tanpa langkah ini Vercel akan error `NEXT_NOT_FOUND` / "No framework detected".

1. Push repo ke GitHub (lihat `README.md` di root repo).
2. Di [vercel.com/new](https://vercel.com/new), import repo GitHub tersebut.
3. Pada bagian **Configure Project**:
   - **Framework Preset**: `Next.js` (otomatis terdeteksi)
   - **Root Directory**: klik **Edit** → pilih folder `bank-sampah-web`
   - **Build Command**: biarkan default (`npm run build`)
   - **Output Directory**: biarkan default (`.next`)
   - **Install Command**: biarkan default (`npm install`)
4. Buka **Environment Variables**, tambahkan:
   - `DATABASE_URL` = connection string PostgreSQL/Neon (host **pooler**, `sslmode=require`)
5. Klik **Deploy**.

### Setelah deploy pertama

- Jalankan inisialisasi database satu kali dari komputer lokal (bukan dari Vercel):

  ```bash
  cd bank-sampah-web
  vercel env pull .env.local --environment=production
  npm run db:init
  ```

  Perintah ini **menghapus lalu membuat ulang** seluruh tabel. Jangan jalankan
  jika database sudah berisi data penting.

- Cek koneksi database lewat endpoint health:

  ```text
  https://<domain-vercel-kamu>/api/health
  ```

  Respons sukses: `{"status":"ok","database":"connected",...}`

## Struktur database

7 tabel (`kategori_sampah`, `jenis_sampah`, `nasabah`, `petugas`, `setoran`,
`detail_setoran`, `penarikan_saldo`), 3 view rekap, dan 1 trigger pelindung saldo.
DDL lengkap: `db/schema.sql`; data awal: `db/seed.sql`.

## Troubleshooting deploy

| Gejala | Penyebab | Solusi |
|---|---|---|
| `NEXT_NOT_FOUND` / no framework detected | Root Directory belum diarahkan ke `bank-sampah-web` | Set **Root Directory** di Project Settings → General |
| Halaman 500 `DATABASE_URL belum dikonfigurasi` | Env var belum ada di Vercel | Tambah `DATABASE_URL` di Environment Variables, lalu redeploy |
| `relation "nasabah" does not exist` | Database belum diinisialisasi | Jalankan `npm run db:init` |
| Build gagal karena versi Node | Node terlalu lama | Pastikan Node 22.x (sudah dipin di `package.json`) |
